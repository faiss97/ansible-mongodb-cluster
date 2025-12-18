# Files Directory

This directory is used for static files that may be deployed by the role.

## Contents

Place any static files here that need to be copied to target hosts, such as:

- Custom scripts
- Configuration snippets
- Certificate templates
- Static policy files

## Usage

Files in this directory can be referenced in tasks using:

```yaml
- name: Copy static file
  ansible.builtin.copy:
    src: filename.ext
    dest: /path/on/target
```
