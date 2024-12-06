[DEFAULT]
# The identity API version to use
identity_api_version = 3

# Keystone endpoint settings
public_endpoint = http://{{ ansible_host }}:5000/v3
admin_endpoint = http://{{ ansible_host }}:35357/v3
internal_endpoint = http://{{ ansible_host }}:5000/v3

# Identity backend
driver = sql

# Database connection
sql_connection = mysql+pymysql://keystone:{{ keystone_db_password }}@{{ keystone_db_host }}/keystone

# Authentication settings
admin_token = {{ keystone_admin_token }}

# Logging settings
log_level = INFO
log_dir = /var/log/keystone

[DEFAULT]
# The Glance API version
api_version = 2

# Glance API endpoints
# These endpoints are typically used to define how clients will communicate with the Glance service
public_endpoint = http://{{ ansible_host }}:9292
admin_endpoint = http://{{ ansible_host }}:9292
internal_endpoint = http://{{ ansible_host }}:9292

# Database connection details
sql_connection = mysql+pymysql://glance:{{ glance_db_password }}@{{ glance_db_host }}/glance

# Set up the Glance image cache directory
image_cache_dir = /var/lib/glance/images

# Set up the backend store for images (e.g., file, swift, etc.)
default_store = file

# Glance registry settings
registry_host = {{ ansible_host }}
registry_port = 9191

# Authentication settings
auth_url = http://{{ ansible_host }}:5000/v3
auth_type = password
project_domain_name = default
user_domain_name = default
project_name = service
username = glance
password = {{ glance_service_password }}

# Logging settings
log_level = INFO
log_dir = /var/log/glance

[DEFAULT]
# The Nova API version
api_version = 2

# Nova API endpoints
public_endpoint = http://{{ ansible_host }}:8774/v2
admin_endpoint = http://{{ ansible_host }}:8774/v2
internal_endpoint = http://{{ ansible_host }}:8774/v2

# Database connection details
sql_connection = mysql+pymysql://nova:{{ nova_db_password }}@{{ nova_db_host }}/nova

# RabbitMQ settings for messaging service
rpc_backend = rabbit
rabbit_host = {{ rabbitmq_host }}
rabbit_userid = nova
rabbit_password = {{ rabbitmq_password }}

# Glance image service endpoint
image_service = glance
glance_api_servers = http://{{ glance_host }}:9292

# Identity service endpoint (Keystone)
auth_strategy = keystone
keystone_authtoken.auth_url = http://{{ keystone_host }}:5000/v3
keystone_authtoken.username = nova
keystone_authtoken.password = {{ nova_password }}
keystone_authtoken.project_name = service
keystone_authtoken.user_domain_name = default
keystone_authtoken.project_domain_name = default

# Networking settings (Neutron)
network_api_class = nova.network.neutronv2.api.API
neutron_url = http://{{ neutron_host }}:9696

# Logging settings
log_level = INFO
log_dir = /var/log/nova
