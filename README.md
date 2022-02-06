# workspace

[![GitHub Workflow Status (branch)](https://img.shields.io/github/workflow/status/markcaudill/workspace/CI/main)](https://github.com/markcaudill/workspace/actions/workflows/ci.yml)
[![GitHub](https://img.shields.io/github/license/markcaudill/workspace)](LICENSE)

## Description

An [Ansible](https://docs.ansible.com/ansible/latest/index.html) playbook to automate the setup of my development/sysadmin/etc. workspace as much as possible. It is currently targeting Ubuntu but would likely work on any Debian-base distribution.

## Prerequisites

- [`community.general`](https://docs.ansible.com/ansible/latest/collections/community/general/index.html) collection

## Structure and Configuration

All configuration is contained in `vars` dictionary.

### `workspace_xdg_directories`

The default directory structure is based on the Freedesktop.org [XDG Base Directory Specification](https://specifications.freedesktop.org/basedir-spec/basedir-spec-latest.html) which are defined under `workspace_xdg_directories`. These are easily overridden or ignored and it's easy to search for where they are used throughout the playbook if an alternate structure is preferred.

No directories specified in `workspace_xdg_directories` are created by default though; all directories that need to be created are in `workspace_directories`.

### `workspace_directories`

a list of dictionaries of directories to create (using [`ansible.builtin.file`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html))

the keys `path` and `mode` are required

### `workspace_environment`

a dictionary of environment variables that are set in all tasks possible to further define the behavior of task execution (e.g. `PATH`, etc.)

### `workspace_prerequisite_packages`

a list of packages (e.g. `ca-certificates`) that are needed by subsequent tasks (using [`ansible.builtin.apt`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html))

### `workspace_apt_keys`

a list of paths to GPG keys to be installed (using [`ansible.builtin.apt_key`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_key_module.html))

### `workspace_apt_repos`

a list of APT repositories to configure (using [`ansible.builtin.apt_repository`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html))

### `workspace_apt_debs`

a list of URLs to `.deb` packages to install (using the `deb` parameter in [`ansible.builtin.apt`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html))

### `workspace_services`

a list of dictionaries of services/daemons to configure (using [`ansible.builtin.service`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html))

the keys `name`, `enabled`, and `state` are required

### `workspace_apt_packages`

a list of packages to install (using [`ansible.builtin.apt_repository`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_repository_module.html))

### `workspace_flatpak_remotes`

a list of dictionaries of [flatpak repositories](https://docs.flatpak.org/en/latest/repositories.html) to configure using [`community.general.flatpak_remote`](https://docs.ansible.com/ansible/latest/collections/community/general/flatpak_remote_module.html#ansible-collections-community-general-flatpak-remote-module)

keys:

- `method` (default `'user'`)
- `name` required
- `flatpakrepo_url` (required when `state` = `present` or not specified)
- `state`

### `workspace_flatpaks`

a list of dictionaries of [flatpaks](https://www.flatpak.org/) to install (using [`community.general.flatpak`](https://docs.ansible.com/ansible/latest/collections/community/general/flatpak_module.html))

keys:

- `become` (default `false`)
- `method` (default `user`)
- `name` (required)
- `no_dependencies`
- `remote`
- `state`

### `workspace_pip_packages`

a list of dictionaries of Python packages to install (using [`ansible.builtin.pip`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html))

the keys `name` and `extra_args` are required

### `workspace_archives`

a list of dictionaries of archives (e.g. `tar.gz` files) to extract (using [`ansible.builtin.unarchive`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/unarchive_module.html))

keys:

- `url` (required)
- `dest` (required)
- `include`
- `creates`

### `workspace_go_packages`

a list of dictionaries of [Go](https://go.dev/) packages to install (using `go install ...` with [`ansible.builtin.command`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html))

keys `package` and `creates` are required

### `workspace_curl_bashes`

a list of dictionaries of URLs to install using the `curl|bash` pattern (I'm not here to judge; [some discussion](https://security.stackexchange.com/questions/213401/is-curl-something-sudo-bash-a-reasonably-safe-installation-method))

the task uses [`ansible.builtin.get_url`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html) and [`ansible.builtin.command`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html)

keys:

- `become` (default `false`)
- `url` (required)
- `dest` (required)
- `mode` (default `'0700'`)
- `cmd` (required)
- `creates` (required)

### `workspace_git_repos`

a list of dictionaries of [Git](https://git-scm.com/) repositories to clone (using [`ansible.builtin.git`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/git_module.html))

keys:

- `repo` (required)
- `dest` (required)
- `force`

### `workspace_make_targets`

a list of dictionaries of [Make](https://www.gnu.org/software/make/) targets to run (using [`community.general.make`](https://docs.ansible.com/ansible/latest/collections/community/general/make_module.html))

keys `chdir`, `target`, and `failed_when` are required

### `workspace_user_groups`

a list of dictionaries of users and the groups to add them to (using [`ansible.builtin.user`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html))

keys `user` and `groups` (a list of strings) are required and are `append`ed so no groups are removed

### `workspace_bins`

a list of dictionaries of URLs to download without further processing (using [`ansible.builtin.get_url`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/get_url_module.html))

keys:

- `become` (default `false`)
- `url` (required)
- `dest` (required)
- `mode` (default `'0700'`)

### `workspace_files`

a list of dictionaries to pass to [`ansible.builtin.file`](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html) (e.g. for symlinking, etc.; general purpose)

keys:

- `src` (required)
- `dest` (required)
- `state` (required)
- `owner`
- `group`
- `mode`

## Execution

Run using `ansible-playbook workspace.yml -K -i 'localhost ansible_connection=local,'`

- `packagekit` is stopped if it's running as it often blocks APT tasks
- `workspace_directories` are created
- `workspace_prerequisite_packages` are installed
- all other tasks; order shouldn't matter. wherever order matters the tasks should be organized into blocks (e.g. the `ensure curl|bash software is installed` task)
- `packagekit` is restarted if it was stopped earlier

## License

Except where otherwise noted, this project is licensed under the [MIT License](LICENSE).
