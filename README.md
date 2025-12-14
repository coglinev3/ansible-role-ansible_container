# Ansible Role: ansible_container

[![Build](https://github.com/coglinev3/ansible-role-ansible_container/actions/workflows/build.yml/badge.svg)](https://github.com/coglinev3/ansible-role-ansible_container/actions/workflows/build.yml) ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/coglinev3/ansible-role-ansible_container) [![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://raw.githubusercontent.com/coglinev3/ansible-role-ansible_container/master/LICENSE)

This role is used with Ansible-Bender to build Containers with:
* Ansible,
* OpenSSH and
* Systemd or Init V.

These containers can be used like virtual machines, for example for testing Ansible roles with GitHub actions.

The supported Linux distributions for this role are:
* Alpine Linux 3.14,
* Alpine Linux 3.15,
* Alpine Linux 3.16,
* Alpine Linux 3.17,
* Alpine Linux 3.18,
* Alpine Linux 3.19,
* Alpine Linux 3.20,
* Alpine Linux 3.21,
* Alpine Linux 3.22,
* Amazon Linux 2023,
* Debian 11 (Bullseye),
* Debian 12 (Bookworm),
* Debian 13 (Trixie),
* Enterprise Linux 9,
* Enterprise Linux 10,
* Fedora 40,
* Fedora 41,
* Fedora 42,
* Ubuntu 22.04 LTS (Jammy Jellyfish) and
* Ubuntu 24.04 LTS (Noble Numbat).


## Requirements

If you want to use this role to create new container images, you need [ansible-bender](https://github.com/ansible-community/ansible-bender/blob/master/README.md "ansible-bender"). You can install ansible-bender with my galaxy role [coglinev3.ansible_bender](https://galaxy.ansible.com/coglinev3/ansible-bender "coglinev3.ansible_bender").


## Role Variables

Available variables are listed below, along with default values (see defaults/main.yml):

```yml
# use admin rights with sudo or not
ansible_container_become: true

# state for installed packages: absent | present | latest
ansible_container_package_state: latest

# Upgrade all packages to the latest version: true | false
ansible_container_upgrade_packages: true

# install type: os | pip
ansible_container_install_type: os

# Ansible packages for install_type=os
ansible_container_ansible_os_packages:
  - ansible

# requirements for Debian
ansible_container_requirements:
  - openssh-server
  - python3-apt
  - systemd
  - sudo

ansible_container_pip_requirements:
  - python3-pip
  - python3-venv
  - python3-virtualenv
  - virtualenv

# force new Ansible configuration file /etc/ansible/ansible.cfg
ansible_container_force_config: "no"

# force new Ansible inventory file /etc/ansible/hosts
ansible_container_force_inventory: "no"

# define an Ansible user and group
ansible_container_user: ansible
ansible_container_group: ansible

# some variables for Ansible configuration file ansible.cfg
#
# forces color mode even when running without a TTY or the “nocolor” setting
# is True
ansible_config_force_color: true

# control the interpreter discovery behavior:
ansible_config_interpreter_python: auto_silent
```

## Dependencies

Ansible role coglinev3.ansible_python

## Example Playbooks

### Playbook to test this role independently on a virtual machine

```yml
---
# file: roles/ansible_container/tests/test.yml

- hosts: localhost
  remote_user: root
  roles:
    - { role: coglinev3.ansible_container }
  vars:
    ansible_container_become: true
```

### Playbook for ansible-bender to create a Debian 13 (Trixi) image with Ansible and Systemd

```yml
---
- name: Containerized version of Debian 13 with ansible
  hosts: all
  gather_facts: false
  vars:
    container_image_namespace: coglinev3
    container_image_name: ansible
    container_distro: debian-13
    ansible_bender:
      base_image: docker.io/library/debian:13
      target_image:
        # command to run by default when invoking the container
        cmd: /bin/systemd
        name: "localhost/{{ container_image_namespace }}/{{ container_image_name }}:{{ container_distro }}"
        labels:
          build-by: "Cogline.v3"
        volumes:
          - /sys/fs/cgroup

  pre_tasks:
    - name: Install Python if necessary
      ansible.builtin.include_role:
        name: "coglinev3.ansible_python"
      tags:
        - stop-layering
    - name: Gather facts
      ansible.builtin.setup:
      tags:
        - stop-layering

  tasks:
    - name: Include role coglinev3.ansible_container
      ansible.builtin.include_role:
        name: coglinev3.ansible_container
      vars:
        ansible_container_become: false
        ansible_container_force_config: "yes"
        ansible_container_force_inventory: "yes"
      tags:
        - stop-layering
```

The new conatiner image is build with:

```sh
ansible-bender build ./playbook.yml
```

## Version

Release: 1.19.4

## License

BSD

## Author Information

Copyright &copy; 2020 - 2025 Cogline.v3.
