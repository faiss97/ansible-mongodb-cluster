# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2024-01-01

### Added

- Initial release
- Standalone MongoDB deployment support
- Replica Set deployment with automatic initialization
- Sharded Cluster deployment support
- Config servers, shard servers, and mongos routers
- Security features:
  - Authentication and authorization
  - Keyfile generation for replica sets
  - TLS/SSL support
  - LDAP and Kerberos integration (Enterprise)
- User management:
  - Admin, cluster admin, backup, and monitoring users
  - Application user creation
  - Custom role creation
- System configuration:
  - ulimits and sysctl tuning
  - Transparent Huge Pages disabled
  - NUMA optimization
  - SELinux support
- Firewall configuration (firewalld, ufw, iptables)
- Automated backup with:
  - Scheduled mongodump
  - Retention management
  - S3 upload support
  - Backup verification
- Monitoring:
  - Prometheus MongoDB exporter
  - MongoDB Free Monitoring
  - Cloud Manager integration
- Health checks and validation
- Multi-platform support:
  - Debian 11, 12
  - Ubuntu 20.04, 22.04, 24.04
  - RHEL 8, 9
  - Rocky Linux 8, 9
  - AlmaLinux 8, 9
- Comprehensive documentation
- Example playbooks and inventories
- Molecule testing framework

### Security

- All passwords must be set via encrypted vault variables
- Keyfile permissions enforced (0400)
- TLS certificate validation
- Network binding restrictions
