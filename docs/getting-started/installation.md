# Installation

This guide covers the installation of the `ansible-mongodb-cluster` role.

## Prerequisites

Before installing the role, ensure you have:

- **Ansible** >= 2.12
- **Python** >= 3.8
- **SSH access** to target servers
- **sudo/root privileges** on target servers

## Installation Methods

### From Ansible Galaxy (Recommended)

```bash
ansible-galaxy install faiss97.ansible_mongodb_cluster
```

To install a specific version:

```bash
ansible-galaxy install faiss97.ansible_mongodb_cluster,v1.0.0
```

### From GitHub

Clone the repository directly:

```bash
git clone https://github.com/faiss97/ansible-mongodb-cluster.git
cd ansible-mongodb-cluster
```

Or add to your `requirements.yml`:

```yaml
---
roles:
  - src: https://github.com/faiss97/ansible-mongodb-cluster.git
    name: ansible-mongodb-cluster
    version: main
```

Then install:

```bash
ansible-galaxy install -r requirements.yml
```

## Required Collections

Install the required Ansible collections:

```bash
ansible-galaxy collection install community.mongodb
ansible-galaxy collection install ansible.posix
ansible-galaxy collection install community.general
```

Or use the provided requirements file:

```bash
ansible-galaxy install -r requirements.yml
```

## Verify Installation

Check that the role is installed:

```bash
ansible-galaxy role list | grep mongodb
```

You should see:

```
- faiss97.ansible_mongodb_cluster, v1.0.0
```

## Directory Structure

After installation, the role will have this structure:

```
ansible-mongodb-cluster/
├── defaults/          # Default variables
├── handlers/          # Service handlers
├── meta/             # Role metadata
├── molecule/         # Test scenarios
├── tasks/            # Task files
├── templates/        # Jinja2 templates
├── vars/             # OS-specific variables
├── docs/             # Documentation
├── examples/         # Example configurations
└── files/            # Static files
```

## Next Steps

- [Quick Start Guide](quickstart.md) - Deploy your first cluster
- [Requirements](requirements.md) - System requirements
- [Configuration](../configuration/variables.md) - Variable reference
