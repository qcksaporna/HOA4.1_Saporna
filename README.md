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
