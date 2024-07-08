# Infisical Deployment Ansible Role

This Ansible role automates the deployment of Infisical, along with its required PostgreSQL database and Redis cache. It provides a flexible setup for installing and configuring these components across multiple hosts. This role doesn't come with HA (high availability). An HA deployment role will be released soon.

## Installation
To install the Infisical Deployment Ansible role, run the following command:

```bash
$ ansible-galaxy install infisical.infisical_deployment
```
This command will download and install the role from Ansible Galaxy, making it available for use in your Ansible projects.


## Usage

1. Clone this role into your Ansible roles directory.
2. Create your playbook (e.g., `playbook.yml`) and inventory file (e.g., `inventory.ini`). You can find examples for the playbook.yml and inventory.ini further down in the README.
3. Run the playbook:

```bash
$ ansible-playbook -i inventory.ini playbook.yml
```


## Requirements

- Ansible 2.9 or higher
- Target machines running a Debian-based Linux distribution (e.g., Ubuntu)
- SSH access to the target machines
- Sudo privileges on the target machines

## Role Variables

The role uses several variables to customize the deployment. Here are the main variables you can configure:

### General Variables

- `install_postgres`: Set to `true` to install and configure PostgreSQL (default: `false`)
- `install_redis`: Set to `true` to install and configure Redis (default: `false`)
- `install_infisical`: Set to `true` to install and configure Infisical (default: `false`)

### PostgreSQL Variables

- `db_user_username`: PostgreSQL user username
- `db_user_password`: PostgreSQL user password
- `db_name`: Name of the database to create
- `allowed_ip_addresses`: List of IP addresses allowed to connect to PostgreSQL

### Redis Variables

- `redis_username`: Redis username
- `redis_password`: Redis password

### Infisical Variables

- `env_vars`: A dictionary of environment variables to set for Infisical

IMPORTANT: The `DB_CONNECTION_URI` and `REDIS_URL` variables will automatically be set if you specified postgres_server and redis_server hosts.

If you wish to use your own redis server or postgres server, you can specify the `DB_CONNECTION_URI` and `REDIS_URL` in the `env_vars`.

`ENCRYPTION_KEY` and `AUTH_SECRET` is automatically generated if they're not passed as a part of the `env_vars`.

## Example Playbook

Here's an example playbook that demonstrates how to use this role:

```yaml
---
- hosts: postgres_server
  vars:
    ansible_user: ssh_username
    ansible_password: your_ssh_password
    ansible_become_password: your_sudo_password
  roles:
    - role: infisical_deploy
      vars:
        install_postgres: true
        allowed_ip_addresses:
          - "80.208.66.9"
          - "172.104.244.217"
        db_user_username: your_db_user
        db_user_password: your_db_password
        db_name: your_db_name

- hosts: redis_server
  vars:
    ansible_user: ssh_username
    ansible_password: your_ssh_password
    ansible_become_password: your_sudo_password
  roles:
    - role: infisical_deploy
      vars:
        install_redis: true
        redis_username: "your_redis_user"
        redis_password: "your_redis_password"

- hosts: infisical_instance
  vars:
    ansible_user: ssh_username
    ansible_password: your_ssh_password
    ansible_become_password: your_sudo_password
  roles:
    - role: infisical_deploy
      vars:
        install_infisical: true
        env_vars:
          TEST_VAR: "TEST_VALUE"
          TEST_VAR_2: "OTHER_VALUE"
```

## Inventory Example

Here's an example inventory file (`inventory.ini`) to use with this role:

```ini
[postgres_server]
172.104.149.35

[redis_server]
172.104.149.116

[infisical_instance]
172.104.244.217
```

## Notes

- This role installs and configures HAProxy on the Infisical instance to handle incoming traffic.
- The role automatically generates and manages encryption keys and authentication secrets for Infisical.
- PostgreSQL and Redis connection URIs are automatically shared between hosts.
- Firewall rules are configured to allow necessary incoming connections.

## Security Considerations

- Ensure that you're using strong, unique passwords for all components (PostgreSQL, Redis, SSH).
- Review and adjust the `allowed_ip_addresses` for PostgreSQL to limit access as needed.
- Consider using Ansible Vault to encrypt sensitive variables in your playbook.

## License

[Specify your license here]

## Author Information

[Your name or organization]