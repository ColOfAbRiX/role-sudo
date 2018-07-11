# role-sudo

Ansible role to install and configure SUDO and SUDO-LDAP.

The role is fully generic and supports all the Sudo settings plus some more behaviours. It also supports the configuration of SUDO-LDAP, to integrate SUDO with any LDAP system and store the configuration in one central place.

The role supports drop-in files inside `/etc/sudoers.d` directory that allows to keep a default system configuration and a custom configuration based on files.

Without any specific setting the role creates the default OS configuration.

## Requirements

The role supports the following Linux distributions:

 - RedHat >= 6
 - CentOS >= 6
 - Debian >= 7
 - Ubuntu >= 14

## Role Variables

The variables are fully documented in the [default configuration](defaults/main.yml) file, including their default values and some examples. This file contains all the settings that can be configured.

Please refer to the default configuration file for the full list and use Linux man pages if you need more information on Sudo,

| Variable               | Default                 | Description                                  |
| :---                   | :---                    | :---                                         |
| `sudo_safety_state`    | `present`               | If 'present' it will create a safety system that replaces wrong sudo files. |
| `sudo_safe_file`       | `/etc/sudo-safe.tar.gz` | Name of the backup file.                     |
| `sudo_ldap_*`          |                         | Set of variables to configure SUDO-LDAP. See `man sudo-ldap.conf` |
| `sudo_sudoers_d_path`  | `/etc/sudoers.d`        | Path of the sudoers directory                |
| `sudo_ldap_support`    | `false`                 | Enables the support for SUDO-LDAP            |
| `sudoers_host_aliases` | `[]`                    | Host aliases to add into /etc/sudoers.       |
| `sudoers_user_aliases` | `[]`                    | User aliases to add into /etc/sudoers.       |
| `sudoers_cmnd_aliases` | `...`                   | Command aliases to add into /etc/sudoers.    |
| `sudoers_defaults`     | `...`                   | Defaults to add into /etc/sudoers.           |
| `sudoers_entries`      | `...`                   | The custom entries to put into /etc/sudoers. |
| `sudoers_sudoers_d`    | `[]`                    | Custom files into /etc/sudoers.d.            |

### Custom sudoers file

The `sudoers_sudoers_d` variable contains the configuration for additional sudoers files placed under `/etc/sudoers.d`.

The variable is a list where each element can contain the same `sudoers_*` variables as the main sudo configuration. The following is an example of a custom sudo file named `/etc/sudoers.d/developers`:

```Yaml
sudoers_sudoers_d:
 # Name of the /etc/sudoers.d/ file
 - name: developers
   state: present
   # Same variable names of the root role configuration
   sudoers_cmnd_aliases:
    - name: DATABASE
      commands: [ /usr/bin/database start, /usr/bin/database stop ]
   sudoers_entries:
    - who: '%developers'
      as_who: [ database_user ]
      commands: [ ALL ]
      options: NOPASSWD
```

### Additional configuration values

Each `sudoers_*` variable has a partner variable named `sudoers_custom_*` that is added to the configuration before the sudo output files are created.
This allows to maintain a global configuration while still be able to add customizations.

### SUDO-LDAP

For the configuration of SUDO-LDAP there is a set of variables prefixed by `sudo_ldap_*` that expose the configuration file /etc/sudo-ldap.conf.

Refer to `man sudo-ldap.conf` for the full description of the variables.

## Example Playbook

Using the role without any specific configuration is very simple, it's enough to include the role.

The following example shows how to allow the group *developers* to run commands as *root*:

```Yaml
- hosts: servers
  roles:
   - role: sudo
     sudoers_entries:
      - who: '%developers'
        as_who: [ root ]
        commands: [ ALL ]
        options: NOPASSWD
```

## License

MIT

## Author Information

[Fabrizio Colonna](colofabrix@tin.it)

## Contributors

Pull requests are also very welcome. Please create a topic branch for your proposed changes. If you don't, this will create conflicts in your fork after the merge.