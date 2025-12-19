# Variables Reference

Complete reference for all configuration variables.

## Basic Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_version` | `"7.0"` | MongoDB version to install |
| `mongodb_deployment_mode` | `"standalone"` | Deployment mode: `standalone`, `replicaset`, `sharded` |
| `mongodb_port` | `27017` | MongoDB port |
| `mongodb_bind_ip` | `"127.0.0.1"` | IP addresses to bind to |

## Security

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_security_auth_enabled` | `true` | Enable authentication |
| `mongodb_admin_user` | `"admin"` | Admin username |
| `mongodb_admin_password` | `""` | Admin password (required) |
| `mongodb_security_javascript_enabled` | `false` | Enable JavaScript execution |
| `mongodb_keyfile_path` | `"/etc/mongodb-keyfile"` | Path to keyfile |
| `mongodb_keyfile_content` | `""` | Keyfile content (auto-generated if empty) |

### TLS/SSL

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_tls_enabled` | `false` | Enable TLS |
| `mongodb_tls_mode` | `"preferTLS"` | TLS mode: `disabled`, `allowTLS`, `preferTLS`, `requireTLS` |
| `mongodb_tls_cert_file` | `""` | Path to TLS certificate |
| `mongodb_tls_key_file` | `""` | Path to TLS key |
| `mongodb_tls_ca_file` | `""` | Path to CA certificate |

## Replica Set

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_replicaset_name` | `"rs0"` | Replica set name |
| `mongodb_replicaset_members` | `[]` | List of replica set members |
| `mongodb_replicaset_arbiter` | `""` | Arbiter hostname |

### Member Configuration

```yaml
mongodb_replicaset_members:
  - host: "mongo1.example.com:27017"
    priority: 2      # Higher = preferred primary
    votes: 1         # Number of votes
    hidden: false    # Hidden member
    slave_delay: 0   # Replication delay (seconds)
```

## Sharding

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_sharding_enabled` | `false` | Enable sharding |
| `mongodb_config_servers` | `[]` | List of config server hosts |
| `mongodb_mongos_port` | `27017` | Mongos router port |

## Storage

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_data_directory` | `"/var/lib/mongo"` | Data directory path |
| `mongodb_log_directory` | `"/var/log/mongodb"` | Log directory path |
| `mongodb_storage_engine` | `"wiredTiger"` | Storage engine |
| `mongodb_wiredtiger_cache_size_gb` | `""` | WiredTiger cache size (auto-calculated if empty) |
| `mongodb_directory_per_db` | `true` | Separate directory per database |

## Backup

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_backup_enabled` | `true` | Enable automated backups |
| `mongodb_backup_directory` | `"/var/backup/mongodb"` | Backup directory |
| `mongodb_backup_retention_days` | `7` | Local backup retention |
| `mongodb_backup_schedule_hour` | `"2"` | Backup hour (cron) |
| `mongodb_backup_schedule_minute` | `"0"` | Backup minute (cron) |

### S3 Backup

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_backup_s3_enabled` | `false` | Enable S3 upload |
| `mongodb_backup_s3_bucket` | `""` | S3 bucket name |
| `mongodb_backup_s3_path` | `"mongodb-backups"` | S3 path prefix |
| `mongodb_backup_s3_access_key` | `""` | AWS access key |
| `mongodb_backup_s3_secret_key` | `""` | AWS secret key |

## Monitoring

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_exporter_enabled` | `false` | Enable Prometheus exporter |
| `mongodb_exporter_port` | `9216` | Exporter port |
| `mongodb_exporter_version` | `"0.40.0"` | Exporter version |

## Alerting

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_alerting_enabled` | `false` | Enable alerting |
| `mongodb_alerting_interval` | `60` | Check interval (seconds) |

### Slack

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_alerting_slack_enabled` | `false` | Enable Slack alerts |
| `mongodb_alerting_slack_webhook` | `""` | Slack webhook URL |
| `mongodb_alerting_slack_channel` | `"#mongodb-alerts"` | Slack channel |

### PagerDuty

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_alerting_pagerduty_enabled` | `false` | Enable PagerDuty |
| `mongodb_alerting_pagerduty_key` | `""` | Routing key |

### Alert Thresholds

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_alerting_repl_lag_warning` | `30` | Replication lag warning (seconds) |
| `mongodb_alerting_repl_lag_critical` | `60` | Replication lag critical (seconds) |
| `mongodb_alerting_disk_warning` | `80` | Disk usage warning (%) |
| `mongodb_alerting_disk_critical` | `90` | Disk usage critical (%) |

## PITR (Point-in-Time Recovery)

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_pitr_enabled` | `false` | Enable PITR |
| `mongodb_pitr_directory` | `"/var/backup/mongodb-pitr"` | PITR directory |
| `mongodb_pitr_tail_interval` | `60` | Oplog tail interval (seconds) |
| `mongodb_pitr_snapshot_interval_hours` | `6` | Snapshot interval (hours) |

## Disaster Recovery

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_dr_delayed_member` | `""` | Delayed secondary hostname |
| `mongodb_dr_delay_seconds` | `3600` | Replication delay (1 hour) |
| `mongodb_dr_tracking_enabled` | `true` | Enable recovery point tracking |

## Performance

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_performance_index_advisor_enabled` | `true` | Enable index advisor |
| `mongodb_performance_query_analyzer_enabled` | `true` | Enable query analyzer |
| `mongodb_profiling_level` | `1` | Profiler level (0=off, 1=slow, 2=all) |
| `mongodb_profiling_slow_op_threshold_ms` | `100` | Slow operation threshold |

## Compliance

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_compliance_reports_enabled` | `true` | Enable compliance reports |
| `mongodb_audit_enabled` | `false` | Enable audit logging (Enterprise) |
| `mongodb_fle_enabled` | `false` | Enable field-level encryption |

## System Tuning

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_ulimit_nofile` | `64000` | File descriptor limit |
| `mongodb_ulimit_nproc` | `64000` | Process limit |
| `mongodb_disable_thp` | `true` | Disable Transparent Huge Pages |
| `mongodb_configure_numa` | `true` | Configure NUMA |

## Firewall

| Variable | Default | Description |
|----------|---------|-------------|
| `mongodb_configure_firewall` | `true` | Configure firewall |
| `mongodb_firewall_allow_sources` | `[]` | Allowed source IPs/networks |
