[![CI](https://github.com/uprunningcloud/ansible-role-slapd/workflows/CI/badge.svg)](https://github.com/uprunningcloud/ansible-role-slapd/actions?query=workflow%3ACI+branch%3Amain)

# Ansible Role: slapd

Install slapd on RHEL/CentOS, Debian/Ubuntu server and configure basic settings.

Forked from [ktooi](https://github-com.translate.goog/ktooi/ansible-role-slapd). Many thanks for great work!

This role does the following:

* LDAP server installation
* LDAP server basic settings
* Setting the BaseDN
* Setting administrator privileges
* Schema import
* Importing modules

This role does not:

* Registration of entry information such as accounts and groups


## Requirements

This role has no special requirements.

## Role Variables

```yaml
# Set this to the slapd global configurations.
slapd_loglevel: stats
# slapd_idletimeout: 86400
# slapd_sizelimit: 500
# slapd_referral: on
```

[OpenLDAP Software 2.4 Administrator's Guide: Configuring slapd](https://www.openldap.org/doc/admin24/slapdconf2.html#cn%3Dconfig)

```yaml
# Users credential.
slapd_passwd_scheme: '{SSHA}'
ldap_root_passwd: "root_passwd"
ldap_admin: "cn=admin"
# Set this if different from the ldap_basedn.
# ldap_admin_base: "dc=example,dc=com"
ldap_admin_passwd: "admin_passwd"
# If you want to reduce the number of tasks
# that are "Changed", specify the hash
# that you generated beforehand using the following slappasswd command.
# $ slappasswd -h '{SSHA}'
# ldap_root_hashed_passwd: '{SSHA}WA/exo2HKC1H4EsOUxGbWU6mW0eT1A1K'
# ldap_admin_hashed_passwd: '{SSHA}a5gsMFahundHCuqNYeVrhX3QO6ZXCI+/'
```

Set administrative privileges for slapd.

```yaml
# Select whether or not to install the schemas.
slapd_install_schema_cosine: true
slapd_install_schema_nis: true
slapd_install_schema_samba3: true
slapd_install_schema_misc: false
slapd_install_schema_ssh_lpk: false
slapd_install_schema_sudo: false
```

Specifies the schema to install.

`true` to install the schema.
`false` the schema is not installed.

Schemas once installed, they will not be uninstalled even if set to `false`.

```yaml
slapd_schema_cosine_dn: null
slapd_schema_misc_dn: null
slapd_schema_nis_dn: null
slapd_schema_samba3_dn: null
slapd_schema_lpk_dn: null
slapd_schema_sudo_dn: null
```

Specifies the DN of the schema to install.

By default, it automatically chooses an appropriate DN. Please specify only if there is some kind of inconvenience.

```yaml
# Select whether or not to install the modules.
slapd_install_module_refint: false
slapd_install_module_memberof: false
slapd_install_module_syncprov: false
```

Specifies the modules to install.
Do not set these variables to `true` on RHEL/CentOS 7 as this will cause the task to fail.

`true`, the module will be installed.
`false`, the module will not be installed.

Modules once installed, they will not be uninstalled even if set to `false`.

```yaml
slapd_module_memberof_dn: null
slapd_overlay_memberof_dn: null
slapd_module_refint_dn: null
slapd_overlay_refint_dn: null
slapd_module_syncprov_dn: null
slapd_overlay_syncprov_config_dn: null
slapd_overlay_syncprov_dn: null
```

Specifies the DN of the module to install.

By default, it automatically chooses an appropriate DN. Please specify only if there is some kind of inconvenience.


### Replication

Configure LDAP server replication.
This role supports  [N-Way Multi-Provider](https://www.openldap.org/doc/admin24/replication.html#N-Way%20Multi-Provider).

```yaml
slapd_replication: false
```

`slapd_replication` Specify when setting up replication `true`. Do not set this variable to `true` on RHEL/CentOS 7 as this will cause the task to fail .

```yaml
slapd_replication_group: 'slapd'
```

Please specify the Ansible Inventory group name of the host that performs replication. This role configures replication on hosts in the group specified here.

```yaml
# slapd_replication_target_list:
#   - id: 1
#     url: "ldap://ldap01.example.com"
#   - id: 2
#     url: "ldap://ldap02.example.com"
```

Specify a list of hosts to perform replication. Normally, it is `slapd_replication_group` automatically defined from the Ansible Inventory group specified in , so there is no need to specify it. It may be necessary to specify the connection destination by IP instead of the host name specified in Ansible, or when replicating with a host outside the jurisdiction of Ansible.

`id` The key is specified as `rid`, and the `url` key is `provider` specified as .

## Dependencies

None.

## Example Playbook

```yaml
- hosts: ldap-servers
  vars_files:
    - vars/main.yml
  roles:
    - slapd
```

Inside `vars/main.yml` :

```yaml
ldap_basedn: dc=your,dc=domain,dc=example,dc=com
ldap_organization_name: Your Example Co., Ltd.

ldap_root_passwd: "root_passwd"
ldap_admin: "cn=Manager"
ldap_admin_passwd: "Manager_p@55w0rd"
```

```yaml
ldap_base_head: your
ldap_domain: your.domain.example.com
```

The values ​​for these variables are `ldap_basedn` automatically calculated from, but if you want to use different values ​​you can define them.

## Authors

* **Kodai Tooi** [GitHub](https://github.com/ktooi), [Qiita](https://qiita.com/ktooi)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details
