# workspace

[![Ansible
Role](https://img.shields.io/ansible/role/59675)](https://galaxy.ansible.com/markcaudill/workspace)
![Ansible Quality Score](https://img.shields.io/ansible/quality/59675)
[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/markcaudill/workspace/molecule-test?label=test)](https://github.com/markcaudill/workspace/actions/workflows/molecule-test.yml)
[![GitHub Workflow Status](https://img.shields.io/github/workflow/status/markcaudill/workspace/galaxy-deploy?label=deploy)](https://github.com/markcaudill/workspace/actions/workflows/galaxy-deploy.yml)
[![GitHub](https://img.shields.io/github/license/markcaudill/workspace)](LICENSE)

## Description

An [Ansible](https://docs.ansible.com/ansible/latest/index.html) playbook to
automate the setup of my development/sysadmin/etc. workspace as much as
possible. It is currently targeting Ubuntu but would likely work on any
Debian-base distribution.

## Prerequisites

- [`community.general`](https://docs.ansible.com/ansible/latest/collections/community/general/index.html)
  collection

## Structure and Configuration

All configuration options are documented in
[`defaults/main.yml`](defaults/main.yml)

## Execution


### Example playbook (`site.yml`)

```yaml
- hosts: localhost
  roles:
    - { role: markcaudill.workspace }
```

```console
$ ansible-playbook site.yml -K -i 'localhost ansible_connection=local,'
BECOME password: 

PLAY [localhost] *************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************
ok: [localhost]

TASK [markcaudill.workspace : stop packagekit so it won't interfere with package installation] ****************************
changed: [localhost]

TASK [markcaudill.workspace : ensure directories are present] *************************************************************
ok: [localhost] => (item={'path': '/home/mark/.local/bin', 'mode': '0750'})
ok: [localhost] => (item={'path': '/home/mark/.cache', 'mode': '0750'})
ok: [localhost] => (item={'path': '/home/mark/.config', 'mode': '0750'})
ok: [localhost] => (item={'path': '/home/mark/.local/share', 'mode': '0750'})
ok: [localhost] => (item={'path': '/home/mark/.local/state', 'mode': '0750'})

TASK [markcaudill.workspace : ensure prerequisite packages are installed] *************************************************
ok: [localhost]

TASK [markcaudill.workspace : ensure prerequisite debs are installed] *****************************************************

TASK [markcaudill.workspace : ensure apt keys are installed] **************************************************************

TASK [markcaudill.workspace : ensure apt repos are configured] ************************************************************

TASK [markcaudill.workspace : ensure packages are installed] **************************************************************
ok: [localhost]

TASK [markcaudill.workspace : ensure package debs are installed] **********************************************************

TASK [markcaudill.workspace : ensure flatpak remotes are configured] ******************************************************

TASK [markcaudill.workspace : ensure flatpaks are installed] **************************************************************

TASK [markcaudill.workspace : ensure snaps are installed] *****************************************************************

TASK [markcaudill.workspace : ensure services are configured] *************************************************************

TASK [markcaudill.workspace : ensure pip packages are installed] **********************************************************

TASK [markcaudill.workspace : ensure archives are installed] **************************************************************

TASK [markcaudill.workspace : ensure go packages are installed] ***********************************************************

TASK [markcaudill.workspace : ensure curl|bash scripts are downloaded] ****************************************************

TASK [markcaudill.workspace : ensure curl|bash scripts are executed] ******************************************************

TASK [markcaudill.workspace : ensure direct-download software is downloaded] **********************************************

TASK [markcaudill.workspace : ensure git repositories are cloned] *********************************************************

TASK [markcaudill.workspace : ensure make targets are run] ****************************************************************

TASK [markcaudill.workspace : ensure users are in groups] *****************************************************************

TASK [markcaudill.workspace : ensure files and links are in place] ********************************************************

TASK [markcaudill.workspace : ensure sysctl entries are configured] *******************************************************

RUNNING HANDLER [markcaudill.workspace : start packagekit] ****************************************************************
ok: [localhost]

PLAY RECAP *******************************************************************************************************
localhost                  : ok=6    changed=1    unreachable=0    failed=0    skipped=19   rescued=0    ignored=0
```

- `packagekit` is stopped if it's running as it often blocks APT tasks
- `workspace_prerequisite_*` tasks are run
- all other tasks/blocks are run; order shouldn't matter
- `packagekit` is restarted if it was stopped earlier

## License

Except where otherwise noted, this project is licensed under the [MIT
License](LICENSE).
