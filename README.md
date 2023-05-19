# [gitlab](#gitlab)

# **IMPORTANT**
# This role will no longer be updated. you can use [robertdebock/ansible-role-gitlab](https://github.com/robertdebock/ansible-role-gitlab) as an alternative.

# ---

|GitHub|GitLab|
|------|------|
|[![github](https://github.com/mullholland/ansible-role-gitlab/workflows/Ansible%20Molecule/badge.svg)](https://github.com/mullholland/ansible-role-gitlab/actions)|[![gitlab](https://gitlab.com/mullholland/ansible-role-gitlab/badges/main/pipeline.svg)](https://gitlab.com/mullholland/ansible-role-gitlab)|

Installs and configures Gitlab.
Adds an optional backupscript for local backups including config and terraform states.


## [Role Variables](#role-variables)

These variables are set in `defaults/main.yml`:
```yaml
---
# Edition/Version
# possible are:
# ce => Community Edition
# ee => Enterprise Edition
gitlab_edition: "gitlab-ee"
# empty means latest
gitlab_version: ''
gitlab_release: ''

# Repository
gitlab_repository_key_url: "https://packages.gitlab.com/gitlab/{{ gitlab_edition }}/gpgkey"
gitlab_repository_key_pub_url: "https://packages.gitlab.com/gitlab/{{ gitlab_edition }}/gpgkey/gitlab-{{ gitlab_edition }}-3D645A26AB9FBD22.pub.gpg"  # noqa 204

# General config.
gitlab_domain: gitlab.example.com
gitlab_external_url: "http://{{ gitlab_domain }}/"
gitlab_git_data_dir: "/var/opt/gitlab/git-data"
gitlab_backup_path: "/var/opt/gitlab/backups"
gitlab_config_template: "gitlab.rb.j2"

# SSL Configuration.
gitlab_redirect_http_to_https: "false"
gitlab_nginx_listen_https: "false"
gitlab_ssl_certificate: "/etc/gitlab/ssl/{{ gitlab_domain }}.crt"
gitlab_ssl_certificate_key: "/etc/gitlab/ssl/{{ gitlab_domain }}.key"

# LDAP Configuration.
gitlab_ldap_enabled: "false"
gitlab_prevent_ldap_sign_in: "false"
gitlab_ldap_config_label: "LDAP"
gitlab_ldap_config_host: "ldap.example.com"
gitlab_ldap_config_port: "636"
gitlab_ldap_config_uid: "sAMAccountName"
gitlab_ldap_config_bind_dn: "CN=bind_user,OU=users,DC=ldap,DC=example,DC=com"
gitlab_ldap_config_password: 'SuperSecurePasswort'
gitlab_ldap_config_encryption: "start_tls"
gitlab_ldap_config_verify_certificates: "false"
gitlab_ldap_config_tls_options: []
#   ca_file: ""
#   ssl_version: ""
#   ciphers: ""
#   cert: ""
#   key: ""
gitlab_ldap_config_timeout: "10"
gitlab_ldap_config_active_directory: "true"
gitlab_ldap_config_allow_username_or_email_login: "true"
gitlab_ldap_config_lowercase_usernames: "false"
gitlab_ldap_config_block_auto_created_users: "false"
gitlab_ldap_config_ldap_base: "OU=users,DC=ldap,DC=example,DC=com"
gitlab_ldap_config_user_filter: '(&(objectCategory=Person)(|(memberOf:1.2.840.113556.1.4.1941:=CN=gitlab_user,OU=groups,DC=ldap,DC=example,DC=com)(memberOf:1.2.840.113556.1.4.1941:=CN=gitlab_admin,OU=groups,DC=ldap,DC=example,DC=com)))'  # yamllint disable-line rule:line-length
gitlab_ldap_config_attributes: []
#   username: "'sAMAccountName'"
#   email: "'mail'"
#   name: 'cn'
#   first_name: 'givenName'
#   last_name: 'sn'
# Enterprise Features
# gitlab_ldap_config_group_base: ''
# gitlab_ldap_config_admin_group: '(&(objectCategory=Person)(memberOf:1.2.840.113556.1.4.1941:=CN=gitlab_admin,OU=groups,DC=ldap,DC=example,DC=com))'
# gitlab_ldap_config_external_groups: []


# SMTP Configuration
gitlab_smtp_enable: "false"
gitlab_smtp_address: "smtp.server"
gitlab_smtp_port: "465"
gitlab_smtp_user_name: "smtp user"
gitlab_smtp_password: "smtp password"
gitlab_smtp_domain: "example.com"
gitlab_smtp_authentication: "login"
gitlab_smtp_enable_starttls_auto: "true"
gitlab_smtp_tls: "false"
gitlab_smtp_openssl_verify_mode: "none"
gitlab_smtp_ca_path: "/etc/ssl/certs"
gitlab_smtp_ca_file: "/etc/ssl/certs/ca-certificates.crt"

# 2-way SSL Client Authentication support.
gitlab_nginx_ssl_verify_client: ""
gitlab_nginx_ssl_client_certificate: ""

# Ignore errors during gitlab restart - can be turned on for testing
gitlab_restart_handler_ignore_failure: false

# Optional settings.
gitlab_time_zone: "UTC"
gitlab_backup_keep_time: "604800"  # 7 days
gitlab_download_validate_certs: true
gitlab_default_theme: '2'

# Email configuration.
gitlab_email_enabled: "false"
gitlab_email_from: "gitlab@example.com"
gitlab_email_display_name: "Gitlab"
gitlab_email_reply_to: "gitlab@example.com"

# Registry configuration.
gitlab_registry_enable: "false"
gitlab_registry_external_url: "https://registry.example.com"
gitlab_registry_nginx_ssl_certificate: "/etc/gitlab/ssl/gitlab.crt"
gitlab_registry_nginx_ssl_certificate_key: "/etc/gitlab/ssl/gitlab.key"

# LetsEncrypt configuration.
gitlab_letsencrypt_enable: "false"
gitlab_letsencrypt_contact_emails: ""
gitlab_letsencrypt_auto_renew_hour: 1
gitlab_letsencrypt_auto_renew_minute: 30
gitlab_letsencrypt_auto_renew_day_of_month: "*/7"
gitlab_letsencrypt_auto_renew: true

# For more Options look into the tempalte "template/gitlab.rb.j2" or use the "gitlab_extra_settings"
# Gitlab have many other settings.
# And you can add them with this special variable `gitlab_extra_settings` with the concerned setting
# and the `key` and `value` keywords.
# gitlab_extra_settings:
#   - gitlab_rails:
#       - key: "trusted_proxies"
#         value: "['foo', 'bar']"
#       - key: "env"
#         type: "plain"
#         value: |
#           {
#           "http_proxy" => "https://my_http_proxy.company.com:3128",
#           "https_proxy" => "https://my_http_proxy.company.com:3128",
#           "no_proxy" => "localhost, 127.0.0.1, company.com"
#           }
#   - unicorn:
#       - key: "worker_processes"
#         value: 5
#       - key: "pidfile"
#         value: "/opt/gitlab/var/unicorn/unicorn.pid"
#
#
# Thanks to https://github.com/geerlingguy/ansible-role-gitlabS

# Backup
gitlab_backup_enable: true

# Logging
gitlab_backup_logfile: "/var/log/gitlab_backup.log"
gitlab_backup_pid_file: "/var/run/backup_gitlab.pid"

# Cron
gitlab_backup_cron:
  minute: "5"
  hour: "2"
  day: "*"
  month: "*"
  weekday: "*"

# Set verbosity
gitlab_backup_verbose: false

# Backup additional path
gitlab_backup_additional_folder:
  - "/var/opt/gitlab/gitlab-rails/shared/terraform_state"  # Terraform state files
#  - "/var/opt/gitlab/gitlab-rails/shared/packages"  # Package registry files

# Possible values are
# NONE => no metrics will be generated
# PROMETHEUS => a prometheus textfile will be prepared for a node exporter
gitlab_backup_metrics: "NONE"
# Path fdor the prometheus node exporter textfiles
gitlab_backup_metrics_path: "/var/opt/gitlab/node-exporter/textfile_collector"

# command to be executed after successfull backup
gitlab_backup_notification_error: ''
# gitlab_backup_notification_error: 'echo "Details in logfile {{ gitlab_backup_logfile }}" | mail -s "Gitlab backup error" admin@example.com'
gitlab_backup_notification_success: ''
# gitlab_backup_notification_success: "curl -fsS -m 10 --retry 5 -o /dev/null https://hc-ping.com/XXXX-XXXX-XXXX-XXXX"
```


## [Example Playbook](#example-playbook)

This example is taken from `molecule/default/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: true
  gather_facts: true
  roles:
    - role: "mullholland.gitlab"
```

The machine needs to be prepared in CI this is done using `molecule/default/prepare.yml`:
```yaml
---
- name: Prepare
  hosts: all
  become: true
  gather_facts: true

  tasks:
    - name: Debian/Ubuntu | Install cron for Backupscript
      ansible.builtin.apt:
        name:
          - "cron"
        state: latest
        update_cache: true
      when: ansible_os_family == "Debian"

    - name: RedHat/CentOS | Install cron for Backupscript
      package:
        name:
          - "cronie"
        state: latest
      when: ansible_os_family == "RedHat" or ansible_os_family == "Rocky"
```





## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/mullholland):

-   [debian9](https://hub.docker.com/r/mullholland/docker-molecule-debian9)
-   [debian10](https://hub.docker.com/r/mullholland/docker-molecule-debian10)
-   [ubuntu1804](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu1804)
-   [ubuntu2004](https://hub.docker.com/r/mullholland/docker-molecule-ubuntu2004)
-   [centos7](https://hub.docker.com/r/mullholland/docker-molecule-centos7)
-   [centos-stream8](https://hub.docker.com/r/mullholland/docker-molecule-centos-stream8)
-   [ubi8](https://hub.docker.com/r/mullholland/docker-molecule-ubi8)
-   [almalinux8](https://hub.docker.com/r/mullholland/docker-molecule-almalinux8)

The minimum version of Ansible required is 2.10, tests have been done to:

-   The previous versions.
-   The current version.



## [Exceptions](#exceptions)

Some variations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| debian11 | No official packages |
| fedora33 | No official packages |
| fedora34 | No official packages |
| fedora35 | No official packages |
| rockylinux8 | No official packages |
| amazonlinux | No official packages |


If you find issues, please register them in [GitHub](https://github.com/mullholland/ansible-role-gitlab/issues)

## [License](#license)

MIT


## [Author Information](#author-information)

[Mullholland](https://github.com/mullholland)

## [Special Thanks](#special-thanks)

Template inspired by [Robert de Bock](https://github.com/robertdebock)
