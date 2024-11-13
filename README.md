---
- name: Install Docker and Build Web & DB Server Docker Image
  hosts: webdb
  become: yes
  tasks:
    - name: Install Docker and dependencies
      apt:
        name: 
          - apt-transport-https
          - ca-certificates
          - curl
          - lsb-release
        state: present
        update_cache: yes

    - name: Add Docker's official GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker repository
      apt_repository:
        repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable
        state: present

    - name: Install Docker
      apt:
        name: docker-ce
        state: present

    - name: Ensure Docker service is started
      service:
        name: docker
        state: started
        enabled: yes

    - name: Copy Dockerfile to remote host
      copy:
        src: Dockerfile
        dest: /home/user/web-db-docker/Dockerfile

    - name: Build Docker image on remote host
      docker_image:
        name: web-db-image
        path: /home/user/web-db-docker
        state: present

    - name: Run Docker container
      docker_container:
        name: web-db-container
        image: web-db-image
        state: started
        ports:
          - "80:80"
          - "3306:3306"
