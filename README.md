PostgreSQL
=========

Ansible role to managed [PostgreSQL](http://www.postgresql.org/).

See *meta/main.yml* for the targeted distributions and releases.  This
is designed primarily for using the version of PostgreSQL that comes
with the distribution and not from external repositories.

Requirements
------------

The only external dependency is the python bindings for PostgreSQL,
which this role will install.

Role Variables
--------------

When configuring PostgreSQL, I encourage you to review the
[PostgreSQL documentation](http://www.postgresql.org/docs/) and
specifically the documentation on server configuration: [9.3](http://www.postgresql.org/docs/9.3/interactive/runtime-config.html),
[9.4](http://www.postgresql.org/docs/9.4/interactive/runtime-config.html),
and
[9.5](http://www.postgresql.org/docs/9.5/interactive/runtime-config.html).
There are several variables available for tuning PostgreSQL.  Please
refer to *defaults/main.yml* for a full list.  Documented below are some
of the variables available for modifying.

* **postgresql_service_name**:  Name of postgresql service for the init
  system.  Defaults to `postgresql`.
* **postgresql_admin_user**:  Name of the administrative user for
  PostgreSQL.  Defaults to `postgres`.
* **postgresql_locale**: Default locale is `en_US.UTF-8`.
* **postgresql_data_directory**:  Path to the PostgreSQL data directory,
  aka `PGDATA` on Red Hat.  Defaults to `/var/lib/pgsql/data`.
* **postgresql_pid_directory**:  Path to the PID directory.  Defaults to
  `/var/run/postgresql`.
* **postgresql_do_backup**:  Whether to deploy a script to perform
  database backups.  Defaults to `no`.
* **postgresql_backup_path**:  Path to where database backups should be
  kept.  Defaults to `/var/lib/pgsql/backups`.
* **postgresql_backup_age**:  Maximum age of backups to keep.  Defaults
  to `14`.
* **postgresql_backup_compression**:  Compression to use for backups.
  Defaults to `xz`.

You can define client authentication with what is traditionally named
`pg_hba.conf`.  There are two variables to control this:
`postgresql_pg_hba_conf_default` and `postgresql_pg_hba_conf`.  The
former defines the defaults (described below) and the latter is to
provide any additional configuration.  The parameter names map to
columns in the `pg_hba.conf` file. Please refer to PostgreSQL's
documentation for
[client authentication](http://www.postgresql.org/docs/9.4/interactive/auth-pg-hba-conf.html).

* **postgresql_pg_hba_conf_default**: A list of allowed clients.  Each
  item in the list must define the keys `type`, `database`, `user`,
  `address`, and `method`.  The default for this is:
```
postgresql_pg_hba_conf_default:
 - { type: local, database: all, user: "{{postgresql_admin_user}}", address: '',             method: peer  }
 - { type: host,  database: all, user: all,                         address: "127.0.0.1/32", method: md5 }
 - { type: host,  database: all, user: all,                         address: "::1/128",      method: md5 }
```
* **postgresql_pg_hba_conf**: This is defined the same as
  *postgresql_pg_hba_conf_default*.  Each item in the list must define
  the keys `type`, `database`, `user`, `address`, and `method`.  This
  defaults to an empty list.
* **postgresql_auth_method_default**: Define the default auth method
  when templating *pg_hba.conf*.  The defaults to `md5`.  This will
  affect localhost entries for *postgresql_pg_hba_conf_default*.

You can define what databases and users to create using
`postgresql_databases` and `postgresql_users`.

* **postgresql_databases**: A list of databases to manage.  Each item in
  the list may define the keys `name` (name of the database), `owner`
  (owner of the database), `encoding` (language encoding, defaults to
  `UTF-8`), `lc_collate` (Collation order, defaults to `en_US.UTF-8`),
  `lc_ctype` (Character classification, defaults to same as
  `lc_collate`), `template` (template used to create the database,
  defaults to `template0`, and `state` (defaults to `present`).  It is
  only necessary to define the `name` parameter.  Example:
```
postgresql_databases:
  - name: example
```
* **postgresql_users**: A list of users to manage.  Each item in the
  list may define the keys `name` (name of the database user),
  `password` (the user's password), `encrypted` (a boolean for whether
  the password is stored hashed in the database), `priv` (privileges to
  grant user on a database), `db` (name of database privileges are
  associated with), `role_attr_flags` (role attributes for the user),
  and `state` (defaults to `present`).  Only `name` and `password` are
  required.
```
postgresql_users:
  - name: icarus
    password: poor-password
    db: example
    priv: ALL
```
* **postgresql_packages**: The list of packages to install.  Defaults
  are set in the appropriate *vars/<platform>.yml* file.  This enables
  one to override.  If you choose to override, make sure to pull in the
  python bindings.

Dependencies
------------

No dependency on other roles.


Example Playbook
----------------

A trivial example:

    - hosts: servers
      roles:
        - { role: sfromm.postgresql }
        
License
-------

GPLv2

Author Information
------------------

See https://github.com/sfromm
