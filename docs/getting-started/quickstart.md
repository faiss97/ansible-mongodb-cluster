# Quick Start Guide

Get a MongoDB cluster up and running in minutes.

## 1. Create Inventory

Create an inventory file with your MongoDB nodes:

=== "Replica Set (3 nodes)"

    ```ini
    # inventory.ini
    [mongodb]
    mongo1.example.com ansible_host=192.168.1.11
    mongo2.example.com ansible_host=192.168.1.12
    mongo3.example.com ansible_host=192.168.1.13

    [mongodb:vars]
    ansible_user=ansible
    ansible_become=true
    ```

=== "Standalone"

    ```ini
    # inventory.ini
    [mongodb]
    mongo.example.com ansible_host=192.168.1.10

    [mongodb:vars]
    ansible_user=ansible
    ansible_become=true
    ```

## 2. Create Playbook

Create a playbook to deploy MongoDB:

=== "Basic Replica Set"

    ```yaml
    # playbook.yml
    ---
    - hosts: mongodb
      become: true
      
      vars:
        mongodb_version: "7.0"
        mongodb_deployment_mode: "replicaset"
        mongodb_replicaset_name: "rs0"
        
        # Security - Use Ansible Vault in production!
        mongodb_security_auth_enabled: true
        mongodb_admin_password: "{{ vault_admin_password }}"
        mongodb_cluster_admin_password: "{{ vault_cluster_password }}"
        
        # Replica set members
        mongodb_replicaset_members:
          - host: "mongo1.example.com:27017"
            priority: 2
          - host: "mongo2.example.com:27017"
            priority: 1
          - host: "mongo3.example.com:27017"
            priority: 1
      
      roles:
        - faiss97.ansible_mongodb_cluster
    ```

=== "With Monitoring"

    ```yaml
    # playbook.yml
    ---
    - hosts: mongodb
      become: true
      
      vars:
        mongodb_version: "7.0"
        mongodb_deployment_mode: "replicaset"
        mongodb_replicaset_name: "rs0"
        
        # Security
        mongodb_security_auth_enabled: true
        mongodb_admin_password: "{{ vault_admin_password }}"
        mongodb_cluster_admin_password: "{{ vault_cluster_password }}"
        
        # Monitoring
        mongodb_exporter_enabled: true
        mongodb_exporter_port: 9216
        
        # Backup
        mongodb_backup_enabled: true
        mongodb_backup_retention_days: 7
        
        # Alerting
        mongodb_alerting_enabled: true
        mongodb_alerting_slack_enabled: true
        mongodb_alerting_slack_webhook: "{{ vault_slack_webhook }}"
      
      roles:
        - faiss97.ansible_mongodb_cluster
    ```

## 3. Create Vault File

Store sensitive data in an encrypted vault:

```bash
ansible-vault create group_vars/vault.yml
```

Add your secrets:

```yaml
# group_vars/vault.yml
vault_admin_password: "YourSecurePassword123!"
vault_cluster_password: "YourClusterPassword123!"
vault_slack_webhook: "https://hooks.slack.com/services/..."
```

## 4. Run Playbook

Deploy the cluster:

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-vault-pass
```

## 5. Verify Deployment

Check cluster status:

```bash
# SSH to any node
ssh mongo1.example.com

# Check replica set status
mongosh -u admin -p 'YourPassword' --authenticationDatabase admin --eval "rs.status()"
```

Expected output:

```json
{
  "set": "rs0",
  "members": [
    { "name": "mongo1.example.com:27017", "stateStr": "PRIMARY" },
    { "name": "mongo2.example.com:27017", "stateStr": "SECONDARY" },
    { "name": "mongo3.example.com:27017", "stateStr": "SECONDARY" }
  ],
  "ok": 1
}
```

## Connection String

Use this connection string for your applications:

```
mongodb://admin:YourPassword@mongo1.example.com:27017,mongo2.example.com:27017,mongo3.example.com:27017/admin?replicaSet=rs0
```

## Next Steps

- [Deployment Modes](../deployment/overview.md) - Learn about different deployment options
- [Security Configuration](../configuration/security.md) - Harden your deployment
- [Monitoring Setup](../operations/monitoring.md) - Set up Grafana dashboards
- [Backup Configuration](../operations/backup.md) - Configure automated backups

## Troubleshooting

### Common Issues

??? question "Replica set not initializing"

    Check the logs:
    ```bash
    sudo journalctl -u mongod -f
    ```
    
    Verify network connectivity between nodes:
    ```bash
    nc -zv mongo2.example.com 27017
    ```

??? question "Authentication failed"

    Ensure the admin user was created correctly:
    ```bash
    mongosh --eval "db.getSiblingDB('admin').getUsers()"
    ```

??? question "Permission denied errors"

    Check file permissions:
    ```bash
    ls -la /var/lib/mongo
    ls -la /etc/mongodb-keyfile
    ```

For more help, see [Troubleshooting Guide](../operations/troubleshooting.md).
