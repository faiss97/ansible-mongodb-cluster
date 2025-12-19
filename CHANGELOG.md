# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.1.0] - 2024-12-19

### Added
- **Rolling Upgrades**: Zero-downtime MongoDB version upgrades
- **Point-in-Time Recovery (PITR)**: Continuous oplog backup and restore
- **Multi-Channel Alerting**: Slack, Teams, PagerDuty, Email, Webhooks
- **Disaster Recovery**: Delayed secondaries, DR runbooks, failover scripts
- **Advanced Performance Tuning**: Index advisor, query analyzer, benchmarks
- **Compliance & Audit**: CIS Benchmark, GDPR helpers, audit logging
- **Chaos Engineering**: Failover tests, network partition, benchmarks
- **CI/CD Pipeline**: GitHub Actions, Molecule tests, Galaxy publishing
- **Monitoring**: Grafana dashboards, Prometheus alert rules
- **Documentation**: MkDocs site with architecture diagrams

## [1.0.0] - 2024-12-19

### Added
- Initial release
- Standalone, replica set, and sharded cluster deployment
- Security: authentication, TLS/SSL, keyfile, LDAP, Kerberos
- User management with custom roles
- System tuning: ulimits, sysctl, THP, NUMA
- Firewall management (firewalld/ufw/iptables)
- Automated backup with S3 support
- Prometheus exporter monitoring
- Health checks and validation
- Multi-platform support (Ubuntu, Debian, RHEL, Rocky, Alma)

[Unreleased]: https://github.com/faiss97/ansible-mongodb-cluster/compare/v1.1.0...HEAD
[1.1.0]: https://github.com/faiss97/ansible-mongodb-cluster/compare/v1.0.0...v1.1.0
[1.0.0]: https://github.com/faiss97/ansible-mongodb-cluster/releases/tag/v1.0.0
