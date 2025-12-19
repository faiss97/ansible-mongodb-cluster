# Ansible MongoDB Cluster

[![Ansible Role](https://img.shields.io/badge/role-ansible--mongodb--cluster-blue.svg)](https://github.com/faiss97/ansible-mongodb-cluster)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Ansible Version](https://img.shields.io/badge/ansible-%3E%3D2.12-red.svg)](https://www.ansible.com/)

Production-ready Ansible role for deploying and managing MongoDB clusters with full support for replica sets, sharded clusters, security, backup, and monitoring.

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Role Variables](#role-variables)
- [Deployment Modes](#deployment-modes)
  - [Standalone](#standalone)
  - [Replica Set](#replica-set)
  - [Sharded Cluster](#sharded-cluster)
- [Security Configuration](#security-configuration)
- [User Management](#user-management)
- [Backup Configuration](#backup-configuration)
- [Monitoring](#monitoring)
- [Examples](#examples)
- [Troubleshooting](#troubleshooting)
- [Best Practices](#best-practices)
- [Contributing](#contributing)
- [License](#license)

## Features

- ✅ **Multiple Deployment Modes**: Standalone, Replica Set, Sharded Cluster
- ✅ **Security First**: Authentication, authorization, keyfile, TLS/SSL support
- ✅ **High Availability**: Automatic failover, replica set management
- ✅ **Automated Backups**: Scheduled backups with retention, S3 support
- ✅ **Monitoring**: Prometheus exporter, Cloud Manager integration
- ✅ **Multi-Platform**: Debian, Ubuntu, RHEL, Rocky, AlmaLinux
- ✅ **Production Ready**: System tuning, SELinux, firewall configuration
- ✅ **100% Idempotent**: Safe to run multiple times
- ✅ **Comprehensive Health Checks**: Built-in cluster validation

## Requirements

### Ansible

- Ansible >= 2.12
- Python >= 3.8

### Collections

```bash
ansible-galaxy collection install community.mongodb
ansible-galaxy collection install ansible.posix
ansible-galaxy collection install community.general
```

Or install from requirements:

```bash
ansible-galaxy install -r requirements.yml
```

### Target Systems

| OS | Versions |
|---|---|
| Debian | 11 (Bullseye), 12 (Bookworm) |
| Ubuntu | 20.04 (Focal), 22.04 (Jammy), 24.04 (Noble) |
| RHEL | 8, 9 |
| Rocky Linux | 8, 9 |
| AlmaLinux | 8, 9 |

### System Requirements

| Resource | Minimum | Recommended |
|---|---|---|
| RAM | 2 GB | 8+ GB |
| CPU | 2 cores | 4+ cores |
| Disk | 10 GB | SSD with 100+ GB |
| Network | 1 Gbps | 10 Gbps |

## Installation

### From GitHub

```bash
git clone https://github.com/faiss97/ansible-mongodb-cluster.git
cd ansible-mongodb-cluster
```

### As Ansible Role

```bash
# In your requirements.yml
roles:
  - src: https://github.com/faiss97/ansible-mongodb-cluster.git
    name: ansible-mongodb-cluster
    version: main
```

## Quick Start

### 1. Create Inventory

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

### 2. Create Playbook

```yaml
# playbook.yml
---
- hosts: mongodb
  become: true
  
  vars:
    mongodb_version: "7.0"
    mongodb_deployment_mode: "replicaset"
    mongodb_replicaset_name: "rs0"
    mongodb_security_auth_enabled: true
    mongodb_admin_password: "{{ vault_admin_password }}"
    mongodb_cluster_admin_password: "{{ vault_cluster_password }}"
    
    mongodb_replicaset_members:
      - host: "mongo1.example.com:27017"
        priority: 2
      - host: "mongo2.example.com:27017"
        priority: 1
      - host: "mongo3.example.com:27017"
        priority: 1
  
  roles:
    - ansible-mongodb-cluster
```

### 3. Run Playbook

```bash
ansible-playbook -i inventory.ini playbook.yml --ask-vault-pass
```

## Role Variables

### Essential Variables

| Variable | Default | Description |
|---|---|---|
| `mongodb_version` | `"7.0"` | MongoDB version to install |
| `mongodb_deployment_mode` | `"replicaset"` | `standalone`, `replicaset`, or `sharded` |
| `mongodb_port` | `27017` | MongoDB port |
| `mongodb_bind_ip` | `"127.0.0.1"` | IP addresses to bind |
| `mongodb_data_directory` | `"/var/lib/mongo"` | Data directory path |

### Security Variables

| Variable | Default | Description |
|---|---|---|
| `mongodb_security_auth_enabled` | `true` | Enable authentication |
| `mongodb_admin_user` | `"admin"` | Admin username |
| `mongodb_admin_password` | `""` | Admin password (**required**) |
| `mongodb_keyfile_path` | `"/etc/mongodb-keyfile"` | Keyfile location |
| `mongodb_tls_enabled` | `false` | Enable TLS/SSL |

### Replica Set Variables

| Variable | Default | Description |
|---|---|---|
| `mongodb_replicaset_name` | `"rs0"` | Replica set name |
| `mongodb_replicaset_members` | `[]` | List of replica set members |
| `mongodb_oplog_size_mb` | `2048` | Oplog size in MB |
| `mongodb_arbiter_enabled` | `false` | Enable arbiter node |

### Storage Variables

| Variable | Default | Description |
|---|---|---|
| `mongodb_storage_engine` | `"wiredTiger"` | Storage engine |
| `mongodb_wiredtiger_cache_size_gb` | `""` | Cache size (auto-calculated) |
| `mongodb_storage_journal_enabled` | `true` | Enable journaling |

### All Variables

See [defaults/main.yml](defaults/main.yml) for the complete list of variables.

## Deployment Modes

### Standalone

Single MongoDB instance for development or small workloads.

```yaml
mongodb_deployment_mode: "standalone"
```

### Replica Set

3+ node replica set for high availability.

```yaml
mongodb_deployment_mode: "replicaset"
mongodb_replicaset_name: "rs0"
mongodb_replicaset_members:
  - host: "mongo1:27017"
    priority: 2
    votes: 1
  - host: "mongo2:27017"
    priority: 1
    votes: 1
  - host: "mongo3:27017"
    priority: 1
    votes: 1
```

#### With Arbiter

```yaml
mongodb_arbiter_enabled: true
mongodb_arbiter_host: "arbiter.example.com:27017"
```

#### Hidden Secondary (for backups)

```yaml
mongodb_replicaset_members:
  - host: "mongo1:27017"
    priority: 2
  - host: "mongo2:27017"
    priority: 1
  - host: "mongo3:27017"
    priority: 0
    hidden: true
    votes: 0
```

### Sharded Cluster

Horizontally scalable cluster with multiple shards.

#### Inventory Structure

```ini
[mongodb_config]
config1.example.com
config2.example.com
config3.example.com

[mongodb_shard1]
shard1a.example.com
shard1b.example.com
shard1c.example.com

[mongodb_shard2]
shard2a.example.com
shard2b.example.com
shard2c.example.com

[mongodb_mongos]
mongos1.example.com
mongos2.example.com
```

#### Configuration

```yaml
# Config servers
mongodb_config_server: true
mongodb_config_replicaset_name: "configRS"

# Shard servers
mongodb_deployment_mode: "sharded"

# Mongos routers
mongodb_mongos_router: true
mongodb_config_servers:
  - "config1:27019"
  - "config2:27019"
  - "config3:27019"

mongodb_shards:
  - name: "shard1"
    members: "shard1rs/shard1a:27018,shard1b:27018,shard1c:27018"
  - name: "shard2"
    members: "shard2rs/shard2a:27018,shard2b:27018,shard2c:27018"

# Collections to shard
mongodb_sharded_collections:
  - database: "mydb"
    collection: "users"
    key: { "user_id": "hashed" }
```

## Security Configuration

### Authentication

```yaml
mongodb_security_auth_enabled: true
mongodb_admin_user: "admin"
mongodb_admin_password: "{{ vault_admin_password }}"
```

### Keyfile (Replica Set Authentication)

```yaml
# Auto-generated if empty
mongodb_keyfile_content: ""

# Or provide your own (generate with: openssl rand -base64 756)
mongodb_keyfile_content: |
  YOUR_BASE64_KEYFILE_CONTENT
```

### TLS/SSL

```yaml
mongodb_tls_enabled: true
mongodb_tls_mode: "requireTLS"
mongodb_tls_certificate_key_file: "/etc/mongodb/tls/mongodb.pem"
mongodb_tls_ca_file: "/etc/mongodb/tls/ca.crt"
```

### Firewall

```yaml
mongodb_configure_firewall: true
mongodb_firewall_backend: "auto"  # firewalld, ufw, or iptables
mongodb_firewall_allowed_sources:
  - "192.168.1.0/24"
  - "10.0.0.0/8"
```

## User Management

### Admin Users

```yaml
mongodb_admin_user: "admin"
mongodb_admin_roles:
  - role: "root"
    db: "admin"

mongodb_cluster_admin_user: "clusterAdmin"
mongodb_cluster_admin_roles:
  - role: "clusterAdmin"
    db: "admin"
```

### Application Users

```yaml
mongodb_users:
  - name: "appuser"
    password: "{{ vault_appuser_password }}"
    database: "myapp"
    roles:
      - role: "readWrite"
        db: "myapp"
      - role: "read"
        db: "analytics"
  
  - name: "readonly"
    password: "{{ vault_readonly_password }}"
    database: "myapp"
    roles:
      - role: "read"
        db: "myapp"
```

### Custom Roles

```yaml
mongodb_custom_roles:
  - name: "appReadWrite"
    database: "admin"
    privileges:
      - resource: { db: "myapp", collection: "" }
        actions: ["find", "insert", "update", "remove"]
    roles:
      - role: "read"
        db: "logs"
```

## Backup Configuration

### Enable Backups

```yaml
mongodb_backup_enabled: true
mongodb_backup_directory: "/var/backup/mongodb"
mongodb_backup_retention_days: 7
mongodb_backup_compression: true

mongodb_backup_schedule:
  minute: "0"
  hour: "2"
  day: "*"
  month: "*"
  weekday: "*"
```

### S3 Backup

```yaml
mongodb_backup_s3_enabled: true
mongodb_backup_s3_bucket: "my-mongodb-backups"
mongodb_backup_s3_prefix: "production"
```

### Manual Backup

```bash
# Run backup manually
/usr/local/bin/mongodb-backup.sh

# Verify backup
/usr/local/bin/mongodb-backup-verify.sh
```

## Monitoring

### Prometheus Exporter

```yaml
mongodb_exporter_enabled: true
mongodb_exporter_version: "0.40.0"
mongodb_exporter_port: 9216
```

Metrics available at: `http://localhost:9216/metrics`

### MongoDB Free Monitoring

```yaml
mongodb_free_monitoring_enabled: true
```

### Cloud Manager / Ops Manager

```yaml
mongodb_cloud_manager_enabled: true
mongodb_cloud_manager_api_key: "{{ vault_cloud_manager_key }}"
mongodb_cloud_manager_base_url: "https://cloud.mongodb.com"
```

## Examples

### Complete Production Deployment

See [examples/playbooks/production.yml](examples/playbooks/production.yml) for a complete production deployment example.

### Minimal Development Deployment

See [examples/playbooks/minimal.yml](examples/playbooks/minimal.yml) for a quick development setup.

## Troubleshooting

### Common Issues

#### Replica Set Not Initializing

```bash
# Check replica set status
mongosh --eval "rs.status()"

# Check logs
tail -f /var/log/mongodb/mongod.log
```

#### Authentication Failure

```bash
# Verify user exists
mongosh -u admin -p --authenticationDatabase admin --eval "db.getUsers()"
```

#### Permission Issues

```bash
# Check file permissions
ls -la /var/lib/mongo
ls -la /etc/mongodb-keyfile

# Fix permissions
chown -R mongod:mongod /var/lib/mongo
chmod 400 /etc/mongodb-keyfile
```

### Health Check Commands

```bash
# Cluster health
mongosh --eval "rs.status()"

# Replication lag
mongosh --eval "db.getReplicationInfo()"

# Server status
mongosh --eval "db.serverStatus()"
```

## Best Practices

### Production Checklist

- [ ] Use dedicated servers for MongoDB
- [ ] Enable authentication (`mongodb_security_auth_enabled: true`)
- [ ] Use TLS/SSL in production
- [ ] Configure firewall rules
- [ ] Enable automated backups
- [ ] Set up monitoring and alerting
- [ ] Use an odd number of replica set members
- [ ] Disable Transparent Huge Pages
- [ ] Configure appropriate ulimits
- [ ] Use SSDs for storage
- [ ] Monitor replication lag
- [ ] Test backup restore procedures

### Security Recommendations

1. **Never expose MongoDB to the internet** without proper security
2. **Use strong passwords** (16+ characters)
3. **Enable TLS** for all connections
4. **Restrict network access** with firewall rules
5. **Use least privilege** for application users
6. **Rotate credentials** regularly
7. **Encrypt backups** at rest and in transit

### Performance Tuning

```yaml
# Recommended production settings
mongodb_wiredtiger_cache_size_gb: ""  # Auto-calculated (50% of RAM - 1GB)
mongodb_limit_nofile: 64000
mongodb_limit_nproc: 64000
mongodb_disable_thp: true
mongodb_disable_numa: true
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Run linting: `ansible-lint`
5. Test with Molecule: `molecule test`
6. Submit a pull request

## License

MIT License - see [LICENSE](LICENSE) for details.

## Author

DevOps faiss97

## Advanced Features

### Rolling Upgrades

Perform zero-downtime upgrades:

```yaml
# Enable and run rolling upgrade
mongodb_rolling_upgrade_enabled: true
mongodb_version: "7.0"  # Target version
```

```bash
ansible-playbook -i inventory playbook.yml --tags mongodb-rolling-upgrade
```

### Point-in-Time Recovery (PITR)

Enable continuous oplog backup for point-in-time recovery:

```yaml
mongodb_pitr_enabled: true
mongodb_pitr_directory: "/var/backup/mongodb-pitr"
mongodb_pitr_snapshot_interval_hours: 6
```

Restore to a specific point in time:

```bash
/usr/local/bin/mongodb-pitr-restore.sh -t "2024-01-15T10:30:00Z"
```

### Alerting & Notifications

Configure multi-channel alerting:

```yaml
mongodb_alerting_enabled: true

# Slack
mongodb_alerting_slack_enabled: true
mongodb_alerting_slack_webhook: "https://hooks.slack.com/services/..."

# PagerDuty (critical alerts only)
mongodb_alerting_pagerduty_enabled: true
mongodb_alerting_pagerduty_key: "your-routing-key"

# Microsoft Teams
mongodb_alerting_teams_enabled: true
mongodb_alerting_teams_webhook: "https://outlook.office.com/webhook/..."

# Email
mongodb_alerting_email_enabled: true
mongodb_alerting_email_recipients:
  - dba@example.com
  - oncall@example.com

# Thresholds
mongodb_alerting_repl_lag_warning: 30
mongodb_alerting_repl_lag_critical: 60
mongodb_alerting_disk_warning: 80
mongodb_alerting_disk_critical: 90
```

### Disaster Recovery

Configure delayed secondary and cross-DC replication:

```yaml
# Delayed secondary for accidental deletion protection
mongodb_dr_delayed_member: "mongo-dr.example.com"
mongodb_dr_delay_seconds: 3600  # 1 hour delay

# Write concern for cross-DC durability
mongodb_dr_configure_write_concern: true
mongodb_dr_write_concern: "majority"
```

Runbooks are automatically generated at `/opt/mongodb-dr/runbooks/`:
- `complete-failure.md` - Full cluster recovery
- `datacenter-failure.md` - DC failover procedures
- `network-partition.md` - Split-brain handling
- `data-corruption.md` - Data recovery steps

### Performance Tuning

Automatic performance analysis and recommendations:

```yaml
mongodb_performance_index_advisor_enabled: true
mongodb_performance_query_analyzer_enabled: true
mongodb_performance_reports_enabled: true
```

Scripts available at `/opt/mongodb-performance/scripts/`:
- `mongodb-index-advisor.sh` - Index optimization recommendations
- `mongodb-query-analyzer.sh` - Slow query analysis
- `mongodb-performance-report.sh` - Weekly performance reports

### Compliance & Audit

GDPR and audit compliance helpers:

```yaml
mongodb_compliance_reports_enabled: true
mongodb_audit_enabled: true  # Enterprise only
mongodb_fle_enabled: true    # Field-Level Encryption (Enterprise)
```

GDPR Scripts at `/opt/mongodb-compliance/scripts/`:
- `mongodb-gdpr-export.sh` - Export user data
- `mongodb-gdpr-delete.sh` - Delete user data (right to be forgotten)
- `mongodb-gdpr-anonymize.js` - Anonymize personal data

Example GDPR export:
```bash
/opt/mongodb-compliance/scripts/mongodb-gdpr-export.sh -u "user@example.com" -d myapp
```

### Chaos Engineering

Built-in chaos testing for resilience validation:

```yaml
mongodb_chaos_install_tools: true
```

Available tests at `/opt/mongodb-chaos/scripts/`:
- `chaos-kill-primary.sh` - Test primary failover
- `chaos-failover-test.sh` - Controlled failover test
- `chaos-network-partition.sh` - Network partition simulation
- `benchmark-runner.sh` - Performance benchmarks

Run failover test:
```bash
/opt/mongodb-chaos/scripts/chaos-failover-test.sh
```

Run performance benchmark:
```bash
/opt/mongodb-chaos/scripts/benchmark-runner.sh 10000 100  # 10k docs, batch 100
```

## Tags Reference

| Tag | Description |
|-----|-------------|
| `mongodb` | All MongoDB tasks |
| `mongodb-install` | Installation only |
| `mongodb-configure` | Configuration only |
| `mongodb-replicaset` | Replica set setup |
| `mongodb-sharding` | Sharded cluster setup |
| `mongodb-users` | User management |
| `mongodb-security` | Security configuration |
| `mongodb-backup` | Backup configuration |
| `mongodb-monitoring` | Monitoring setup |
| `mongodb-firewall` | Firewall rules |
| `mongodb-pitr` | Point-in-Time Recovery |
| `mongodb-alerting` | Alerting configuration |
| `mongodb-dr` | Disaster recovery |
| `mongodb-performance` | Performance tuning |
| `mongodb-compliance` | Compliance & audit |
| `mongodb-chaos` | Chaos testing (never runs by default) |
| `mongodb-rolling-upgrade` | Rolling upgrade (never runs by default) |
| `mongodb-healthcheck` | Health checks |

