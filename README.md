# Remote Docker
A role for managing Docker setups within a remote Vagrant machine.

## Requirements
- Hosts should be bootstrapped for ansible usage (have python,...)
- Root privileges, eg `become: yes`

## Role Variables

| Variable | Description | Default value |
|----------|-------------|---------------|
| `vd_vagrant_user` | Remote user to run the setup | `vagrant_user or default(ansible_user)` |
| `vd_docker_compose_src`| List of docker-compose setups **(see details!)**  | `[]` |


#### `vd_docker_compose_src` details

The docker-compose list is used to define the setups to run or terminate per host.   Each item in
the list can have following attributes:

| Variable | Description | Required | Default |
|----------|-------------|----------|---------|
| `dest` | Destination to store the setup | yes | / |
| `repo` | A GIT repository to clone the docker-compose setup from | no | / |
| `accept_hostkey` | Accept hostkeys when using a GIT repo | no | yes |
| `version` | The version for the GIT repo to checkout | no | HEAD |
| `key_file` | The private key file to use for GIT | no | / |
| `src` | Copy the docker-compose setup from a local path | no | / |
| `authorized_keys` | The SSH keys that should get access to this machine and the remote host | no | / |
| `state` | The setup state. It can be `up` for running the setup, `halt` for stopping the setup, or `destroy` for purging the setup | no | `up` |
| `files` | The docker-compose.yml files that should be used when running the setup  | no | [] |
| `build` | docker-compose build?  | no | false |

###### Example `vd_docker_compose_src`

```yaml
vd_docker_compose_src:
  - dest: /opt/vd-redmine
    repo: https://github.com/bitnami/bitnami-docker-redmine.git
    authorized_keys:
      - ssh-rsa abcdefghijklmnopqrstuvwxyz0123456=
    files:
      - docker-compose.yml
      - docker-compose.development.yml

  - dest: /opt/vd-jenkins
    repo: https://github.com/bitnami/bitnami-docker-jenkins.git
    authorized_keys:
      - ssh-rsa abcdefghijklmnopqrstuvwxyz0123456=
    state: destroy

  - dest: /opt/vd-project
    src: /mnt/projects/app
    authorized_keys:
      - ssh-rsa abcdefghijklmnopqrstuvwxyz0123456=
```

## Dependencies

* andrewrothstein.vagrant
* oefenweb.virtualbox

## Example Playbook  

```yaml
---
- hosts: remotedocker
  roles:
    - role: phizzl.remote-docker
      become: yes
```