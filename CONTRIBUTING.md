# Contributing to Ansible MongoDB Cluster

Thank you for your interest in contributing to this project! This document provides guidelines and instructions for contributing.

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Making Changes](#making-changes)
- [Testing](#testing)
- [Submitting Changes](#submitting-changes)
- [Style Guide](#style-guide)

## Code of Conduct

This project adheres to the [Contributor Covenant Code of Conduct](CODE_OF_CONDUCT.md). By participating, you are expected to uphold this code.

## Getting Started

1. Fork the repository
2. Clone your fork locally
3. Set up the development environment
4. Create a feature branch
5. Make your changes
6. Test your changes
7. Submit a pull request

## Development Setup

### Prerequisites

- Python 3.8+
- Ansible 2.12+
- Docker (for Molecule testing)
- Git

### Installation

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/ansible-mongodb-cluster.git
cd ansible-mongodb-cluster

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install ansible ansible-lint yamllint molecule molecule-docker pytest

# Install Ansible collections
ansible-galaxy install -r requirements.yml
```

## Making Changes

### Branch Naming

Use descriptive branch names:

- `feature/add-new-feature`
- `fix/issue-123`
- `docs/update-readme`
- `refactor/cleanup-tasks`

### Commit Messages

Follow conventional commits:

```
type(scope): description

[optional body]

[optional footer]
```

Types:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Formatting
- `refactor`: Code restructuring
- `test`: Adding tests
- `chore`: Maintenance

Example:
```
feat(replicaset): add support for arbiter nodes

Added mongodb_arbiter_enabled and mongodb_arbiter_host variables
to support arbiter configuration in replica sets.

Closes #42
```

## Testing

### Linting

```bash
# YAML lint
yamllint .

# Ansible lint
ansible-lint
```

### Molecule Tests

```bash
# Run full test suite
molecule test

# Run specific scenario
molecule test -s default

# Debug mode (don't destroy)
molecule converge
molecule verify
molecule destroy
```

### Manual Testing

Test against a local VM or container:

```bash
# Create test inventory
cat > test-inventory.ini << EOF
[mongodb]
localhost ansible_connection=local
EOF

# Run playbook
ansible-playbook -i test-inventory.ini playbook.yml --check
```

## Submitting Changes

### Pull Request Process

1. Update documentation if needed
2. Add or update tests
3. Ensure all tests pass
4. Update CHANGELOG.md
5. Submit pull request

### Pull Request Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Tested with molecule
- [ ] Tested manually
- [ ] Added new tests

## Checklist
- [ ] Code follows style guide
- [ ] Documentation updated
- [ ] CHANGELOG updated
- [ ] All tests pass
```

## Style Guide

### YAML Style

- 2 spaces indentation
- No trailing whitespace
- Maximum line length: 160 characters
- Use `true/false` for booleans

### Variable Naming

- Use snake_case
- Prefix role variables with `mongodb_`
- Use descriptive names

```yaml
# Good
mongodb_replicaset_name: "rs0"
mongodb_wiredtiger_cache_size_gb: 4

# Bad
rs_name: "rs0"
cache: 4
```

### Task Structure

```yaml
- name: Descriptive task name
  ansible.builtin.module:
    param: value
  register: result
  when: condition
  tags:
    - tag1
    - tag2
```

### Comments

- Use comments to explain "why", not "what"
- Keep comments up to date

```yaml
# Required for replica set authentication
# Keyfile must have 0400 permissions
- name: Create keyfile
  ansible.builtin.copy:
    content: "{{ mongodb_keyfile_content }}"
    dest: "{{ mongodb_keyfile_path }}"
    mode: "0400"
```

## Questions?

Feel free to open an issue for questions or discussions.
