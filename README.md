---
- name: Install Docker, Enable Docker Socket, Add User to Docker Group, and Setup Dockerfile
  hosts: webdb  # This refers to the webdb group from the inventory
  become: yes   # Make sure we have root privileges to perform installations

  tasks:
    # Task 1: Install required dependencies for Docker installation
    - name: Install prerequisites
      apt:
        name:
          - apt-transport-https
          - ca-certificates
          - curl
          - software-properties-common
        state: present
        update_cache: yes

    # Task 2: Add Docker's official GPG key
    - name: Add Docker's GPG key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    # Task 3: Add Docker repository
    - name: Add Docker repository
      apt_repository:
        repo: "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
        state: present
        update_cache: yes

    # Task 4: Install Docker
    - name: Install Docker
      apt:
        name: docker-ce
        state: present

    # Task 5: Start and enable Docker service
    - name: Ensure Docker service is started and enabled
      service:
        name: docker
        state: started
        enabled: yes

    # Task 6: Add current user to the Docker group
    - name: Add user to Docker group
      user:
        name: "{{ ansible_user }}"  # Using the Ansible user defined in the inventory
        groups: docker
        append: yes

    # Task 7: Create a Dockerfile to install Nginx and MySQL
    - name: Copy Dockerfile to the server
      copy:
        dest: /home/{{ ansible_user }}/Dockerfile
        content: |
          # Use official Ubuntu base image
          FROM ubuntu:20.04

          # Install Nginx and MySQL server
          RUN apt-get update && \
              apt-get install -y nginx mysql-server mysql-client && \
              apt-get clean

          # Expose web and MySQL ports
          EXPOSE 80 3306

          # Start both Nginx and MySQL services
          CMD service mysql start && nginx -g 'daemon off;'

    # Task 8: Build Docker image using the Dockerfile
    - name: Build the Docker image
      docker_image:
        name: web-db-image
        path: /home/{{ ansible_user }}  # Path where the Dockerfile is located
        state: present

    # Task 9: Run the Docker container
    - name: Run Docker container
      docker_container:
        name: web-db-container
        image: web-db-image
        state: started
        ports:
          - "80:80"
          - "3306:3306"

