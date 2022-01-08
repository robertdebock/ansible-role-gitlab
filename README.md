# [gitlab](#gitlab)

Install and configure GitLab on your system.

|GitHub|GitLab|Quality|Downloads|Version|
|------|------|-------|---------|-------|
|[![github](https://github.com/robertdebock/ansible-role-gitlab/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-gitlab/actions)|[![gitlab](https://gitlab.com/robertdebock/ansible-role-gitlab/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-gitlab)|[![quality](https://img.shields.io/ansible/quality/57338)](https://galaxy.ansible.com/robertdebock/gitlab)|[![downloads](https://img.shields.io/ansible/role/d/57338)](https://galaxy.ansible.com/robertdebock/gitlab)|[![Version](https://img.shields.io/github/release/robertdebock/ansible-role-gitlab.svg)](https://github.com/robertdebock/ansible-role-gitlab/releases/)|

## [Example Playbook](#example-playbook)

This example is taken from `molecule/default/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.gitlab
      gitlab_cleanup_ruby: no
```

The machine needs to be prepared. In CI this is done using `molecule/default/prepare.yml`:
```yaml
---
- name: prepare
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.core_dependencies
    - role: robertdebock.postfix
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## [Role Variables](#role-variables)

The default values for the variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for gitlab

# Specify a specific version for GitLab to install.
# Please have a look at this repository for available package version:
# community: https://packages.gitlab.com/gitlab/gitlab-ce
# enterprise: https://packages.gitlab.com/gitlab/gitlab-ee
gitlab_version: 14.6.1

# A part of the version is the "release", mostly "0". See repositories above.
gitlab_release: 0

# The URL where the gitlab installation will be made available on.
# For "https", let's encrypt will be used.
gitlab_external_url: "http://localhost"

# Choose to install "enterprise" or "community".
gitlab_distribution: community

# The syntax of `gitlab.rb` will be tested before applying this role. The
# package `ruby` is required for that. By default `ruby` is installed and
# uninstalled. This makes the role not idempotent, so CI ruby is not un-
# installed.
gitlab_cleanup_ruby: yes

# You can install all roles but not specifying any role, or select a few roles.
# gitlab_roles:
#   - redis_sentinel_role
#   - redis_master_role
#   - redis_replica_role
#   - geo_primary_role
#   - geo_secondary_role
#   - postgres_role
#   - consul_role
#   - application_role
#   - monitoring_role

# You can set the default theme for the GitLab ui. Pick any one from:
# indigo, dark, light, blue, green, lightindigo, lightblue, lightgreen,
# red or lightred. (Nerds call "pink" "redlight")
gitlab_default_theme: dark

# You can disable features for newly created projects.
gitlab_default_projects_features_issues: yes
gitlab_default_projects_features_merge_requests: yes
gitlab_default_projects_features_wiki: yes
gitlab_default_projects_features_snippets: yes
gitlab_default_projects_features_builds: yes
gitlab_default_projects_features_container_registry: yes

# LDAP can be configured with the below variables.
gitlab_rails_ldap_enabled: no
gitlab_rails_prevent_ldap_sign_in: no
# When `gitlab_rails_ldap_enabled` is set to `yes`, you need to define (at
# least on) `gitlab_rails_ldap_servers`.
# gitlab_rails_ldap_servers:
#   - name: main
#     label: LDAP
#     host: _your_ldap_server
#     port: 389
#     uid: sAMAccountName
#     bind_dn: _the_full_dn_of_the_user_you_will_bind_with
#     password: _the_password_of_the_bind_user
#     encryption: plain
#     verify_certificates: yes
#     smartcard_auth: yes
#     active_directory: yes
#     allow_username_or_email_login: no
#     lowercase_usernames: no
#     block_auto_created_users: no
#     base: ""
#     user_filter: ""
#     # These settings are only available when `gitlab_distribution` is set to
#     # the value `enterprise`.
#     # group_base: ""
#     # admin_group: ""
#     # sync_ssh_keys: no
#   - name: secondary
#     label: LDAP
#     host: _your_ldap_server
#     port: 389
#     uid: sAMAccountName
#     bind_dn: _the_full_dn_of_the_user_you_will_bind_with
#     password: _the_password_of_the_bind_user
#     encryption: plain
#     verify_certificates: yes
#     smartcard_auth: yes
#     active_directory: yes
#     allow_username_or_email_login: no
#     lowercase_usernames: no
#     block_auto_created_users: no
#     base: ""
#     user_filter: ""
#     # These settings are only available when `gitlab_distribution` is set to
#     # the value `enterprise`.
#     # group_base: ""
#     # admin_group: ""
#     # sync_ssh_keys: no

# Settings for backups.
gitlab_rails_manage_backup_path: yes
gitlab_rails_backup_path: /var/opt/gitlab/backups
gitlab_rails_backup_gitaly_backup_path: /opt/gitlab/embedded/bin/gitaly-backup
gitlab_rails_backup_archive_permissions: "0644"
gitlab_rails_backup_pg_schema: public
gitlab_rails_backup_keep_time: 604800

gitlab_rails_backup_upload_connection:
  provider: AWS
  region: eu-west-1
  aws_access_key_id: AKIAKIAKI
  aws_secret_access_key: secret123
  # # If IAM profile use is enabled, remove aws_access_key_id and aws_secret_access_key
  use_iam_profile: no

gitlab_rails_backup_upload_remote_directory: my.s3.bucket
gitlab_rails_backup_multipart_chunk_size: 104857600

##! **Turns on AWS Server-Side Encryption with Amazon S3-Managed Keys for
##!   backups**
gitlab_rails_backup_encryption: AES256
##! The encryption key to use with AWS Server-Side Encryption.
##! Setting this value will enable Server-Side Encryption with customer provided keys;
##!   otherwise S3-managed keys are used.
gitlab_rails_backup_encryption_key: "base64-encoded encryption key"

##! **Turns on AWS Server-Side Encryption with Amazon SSE-KMS (AWS managed but customer-master key)
gitlab_rails_backup_upload_storage_options:
 server_side_encryption: "aws:kms"
 server_side_encryption_kms_key_id: "arn:aws:kms:YOUR-KEY-ID-HERE"

##! **Specifies Amazon S3 storage class to use for backups. Valid values
##!   include 'STANDARD', 'STANDARD_IA', and 'REDUCED_REDUNDANCY'**
gitlab_rails_backup_storage_class: STANDARD

##! Skip parts of the backup. Comma separated.
##! Docs: https://docs.gitlab.com/ee/raketasks/backup_restore.html#excluding-specific-directories-from-the-backup
gitlab_rails_env:
  SKIP: db,uploads,repositories,builds,artifacts,lfs,registry,pages
```

## [Requirements](#requirements)

- pip packages listed in [requirements.txt](https://github.com/robertdebock/ansible-role-gitlab/blob/master/requirements.txt).

## [Status of used roles](#status-of-requirements)

The following roles are used to prepare a system. You can prepare your system in another way.

| Requirement | GitHub | GitLab |
|-------------|--------|--------|
|[robertdebock.bootstrap](https://galaxy.ansible.com/robertdebock/bootstrap)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-bootstrap/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-bootstrap/actions)|[![Build Status GitLab ](https://gitlab.com/robertdebock/ansible-role-bootstrap/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-bootstrap)|
|[robertdebock.core_dependencies](https://galaxy.ansible.com/robertdebock/core_dependencies)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-core_dependencies/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-core_dependencies/actions)|[![Build Status GitLab ](https://gitlab.com/robertdebock/ansible-role-core_dependencies/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-core_dependencies)|
|[robertdebock.postfix](https://galaxy.ansible.com/robertdebock/postfix)|[![Build Status GitHub](https://github.com/robertdebock/ansible-role-postfix/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-postfix/actions)|[![Build Status GitLab ](https://gitlab.com/robertdebock/ansible-role-postfix/badges/master/pipeline.svg)](https://gitlab.com/robertdebock/ansible-role-postfix)|

## [Context](#context)

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/ansible-role-gitlab/png/requirements.png "Dependencies")

## [Compatibility](#compatibility)

This role has been tested on these [container images](https://hub.docker.com/u/robertdebock):

|container|tags|
|---------|----|
|el|7, 8|
|ubuntu|focal|

The minimum version of Ansible required is 2.10, tests have been done to:

- The previous version.
- The current version.
- The development version.



If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-gitlab/issues)

## [License](#license)

Apache-2.0

## [Author Information](#author-information)

[Robert de Bock](https://robertdebock.nl/)

Please consider [sponsoring me](https://github.com/sponsors/robertdebock).
