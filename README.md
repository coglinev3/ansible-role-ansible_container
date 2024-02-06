# Ansible Role: ansible_container

[![Build](https://github.com/coglinev3/ansible-role-ansible_container/actions/workflows/build.yml/badge.svg)](https://github.com/coglinev3/ansible-role-ansible_container/actions/workflows/build.yml) ![GitHub tag (latest by date)](https://img.shields.io/github/v/tag/coglinev3/ansible-role-ansible_container) [![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg)](https://raw.githubusercontent.com/coglinev3/ansible-role-ansible_container/master/LICENSE)

This role is used with Ansible-Bender to build Containers with:
* Ansible,
* OpenSSH and
* Systemd or Init V.

These containers can be used like virtual machines, for example for testing Ansible roles with GitHub actions.

The supported Linux distributions for this role are:
* Alpine Linux 3.12,
* Alpine Linux 3.13,
* Alpine Linux 3.14,
* Alpine Linux 3.15,
* Alpine Linux 3.16,
* Alpine Linux 3.17,
* Alpine Linux 3.18,
* Amazon Linux 2023,
* Debian 10 (Buster),
* Debian 11 (Bullseye),
* Enterprise Linux 7,
* Enterprise Linux 8,
* Enterprise Linux 9,
* Fedora 34,
* Fedora 35,
* Fedora 36,
* Fedora 37,
* Fedora 38,
* Fedora 39,
* Ubuntu 18.04 LTS (Bionic Beaver),
* Ubuntu 20.04 LTS (Focal Fossa) and
* Ubuntu 22.04 LTS (Jammy Jellyfish).


## Requirements

If you want to use this role to create new container images, you need [ansible-bender](https://github.com/ansible-community/ansible-bender/blob/master/README.md "ansible-bender"). You can install ansible-bender with my galaxy role [coglinev3.ansible_bender](https://galaxy.ansible.com/coglinev3/ansible-bender "coglinev3.ansible_bender").


## Role Variables

Available variables are listed below, along with default values (see defaults/main.yml):

```yml
# use sudo or not
ansible_container_become: true

# state for installed packages: absent | present | latest
ansible_container_package_state: latest

# requirements for Debian
ansible_container_requirements:
  - libssl-dev
  - libffi-dev
  - openssh-server
  - python3-apt
  - python3-pip
  - python3-setuptools
  - python3-software-properties
  - python3-wheel
  - systemd
  - sudo

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
ansible_config_interpreter_python: auto
```

## Dependencies

None.

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

### Playbook for ansible-bender to create a CentOS 7 image with Ansible and Systemd

```yml
---
- name: Containerized version of CentOS 7 with ansible
  hosts: all
  vars:
    # configuration specific for ansible-bender
    ansible_bender:
      base_image: centos:7
      target_image:
        # command to run by default when invoking the container
        cmd: /usr/sbin/init
        name: centos:7-ansible
        labels:
          build-by: "Cogline.v3"
        volumes:
          - /sys/fs/cgroup
  tasks:
  - name: include role ansible_container
    include_role:
      name: ansible_container
    vars:
      ansible_container_become: false
```

The new conatiner image is build with:

```sh
ansible-bender build ./playbook.yml
```

## Version

Release: 1.15.1

## License

BSD

## Author Information

Copyright &copy; 2023 Cogline.v3.
