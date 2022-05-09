Ansible Debian bootstrap
====================================================

This role bootstraps Debian hosts:

- Configure APT (sources.list)
- Install minimal packages (vim, htop...)
- Install and configure NTP daemon ([OpenNTPd](http://www.openntpd.org/) or [NTP](http://support.ntp.org/))
- Add groups, users with SSH key, sudoers
- Deploy bashrc, vimrc for root
- Update few alternatives
- Configure system: hostname, timezone and locale
- Sysctl tuning

Supported versions

| OS                     | Working      | Stable (active support) |
| ---------------------- | -------      | ----------------------- |
| Debian Stretch (9)     | Yes          | Yes                     |
| Debian Buster (10)     | Yes          | Yes                     |
| Debian Bullseye (11)   | Yes          | Yes                     |

Thanks to [HanXHX](https://galaxy.ansible.com/HanXHX) for the original role.

Requirements
------------

- Ansible >= 2.11
- Collections: [ansible.posix collection](https://galaxy.ansible.com/ansible/posix) / [community.general](https://galaxy.ansible.com/community/general)

Role Variables
--------------

### APT configuration

Theses variables define hostname to configure APT (normal repo and backports):

- `dbs_apt_default_host`: repository host. It can replace the last one (installed with this role) with a new one
- `dbs_apt_use_src`: install "deb-src" repositories (default: false)
- `dbs_apt_components`: components uses in sources.list (default: "main")

### pip packages

- `dbs_pip_packages`: list of packages from pip

### Docker images

- `dbs_docker_images`: list of Dcoker images to pull

### Role setup

- `dbs_set_hostname`: if true, change hostname
- `dbs_clean_hosts`: if true, manages `/etc/hosts` file
- `dbs_set_locale`: if true, configure locales
- `dbs_set_timezone`: if true, set timezone
- `dbs_set_ntp`: if true, install and configure OpenNTPd
- `dbs_set_apt`: if true, configure APT repository

### System configuration

- `dbs_hostname`: system hostname
- `dbs_hostname_use_strategy`: strategy used to set hostname check "use" in [hostname module](https://docs.ansible.com/ansible/latest/modules/hostname_module.html). You should update this var only if hostname fails (in LXC for example).
- `dbs_default_locale`: default system locale
- `dbs_locales`: list of installed locales
- `dbs_timezone`: system timezone. If you need a "standard" timezone like UTC, you must use prefix "Etc/" (ex: "Etc/UTC")
- `dbs_sysctl_config`: hash of kernel parameters, see: [default/main.yml](default/main.yml)
- `dbs_use_dotfiles`: overwrite root dotfiles (bashrc, screenrc, vimrc)
- `dbs_uninstall_packages`: packages list to uninstall

### Alternatives

- `dbs_alternative_editor`
- `dbs_alternative_awk`

### NTPd

- `dbs_ntp_hosts`: hostnames NTP server list
- `dbs_ntp_pkg`: package used to provide NTP: "openntpd" or "ntp"

### Group

- `dbs_groups`: list of group

Each row have few keys:

- `name`: (M) username on system
- `system`: (O) yes/no (default: no)
- `state`: (O) present/absent (default: present)

(M) Mandatory
(O) Optional

### User

- `dbs_users`: list of user

Each row have few keys:

- `name`: (M) username on system
- `password`: (O) password with hash format (see [ansible doc](http://docs.ansible.com/ansible/latest/faq.html#how-do-i-generate-crypted-passwords-for-the-user-module))
- `clear_password`: (O) password as clear format (not recommanded)
- `update_password`: (O) always / on\_create
- `shell`: (O) default is /bin/bash
- `comment`: (O) default is an empty string
- `sudo`: (O) boolean (true = can sudo)
- `group`: (O) main group (default is `name` without password)
- `groups`: (O) comma separated list of groups
- `createhome`: (O) yes/no
- `system`: (O) yes/no (default: no)
- `ssh_keys`: (O) ssh public keys list
- `state`: (O) present/absent (default: present)

(M) Mandatory
(O) Optional

Notes:

- if `password` is specified, `clear_password` is not used!
- `clear_password` is not idempotent with `update_password` = always (default)

For more information, look [ansible user module doc](http://docs.ansible.com/ansible/latest/user_module.html).

### Readonly vars

- `dbs_packages`: list of packages to install
- `dbs_distro_packages`: list specific package to install (related to OS version)
- `dbs_is_docker`: boolean. Is true if current is a docker container

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: leandroramos.debian_bootstrap, become: yes }


About Docker
------------

Due to Docker limitations, theses features are disabled:

- Setting hostname
- Configure sysctl

License
-------

GPLv3

Author Information
------------------

- Twitter: [@leandroembu](https://twitter.com/leandroembu)
