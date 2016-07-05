# An Ansible summary

* Jon Warbrick, July 2014, V3.2 (for Ansible 1.7)
* Updated by Carles San Agustín, July 2016 (for Ansible 2.1)

# Configuration file

[intro\_configuration.html](http://docs.ansible.com/intro_configuration.html)

First one found from of

* Contents of `$ANSIBLE_CONFIG`
* `./ansible.cfg`
* `~/.ansible.cfg`
* `/etc/ansible/ansible.cfg`

Configuration settings can be overridden by environment variables - see
constants.py in the source tree for names.

# Patterns

[intro\_patterns.html](http://docs.ansible.com/intro_patterns.html)

Used on the `ansible` command line, or in playbooks.

* `all` (or `*`)
* hostname: `foo.example.com`
* groupname: `webservers`
* or: `webservers:dbserver`
* exclude: `webserver:!phoenix`
* intersection: `webservers:&staging`

Operators can be chained: `webservers:dbservers:&staging:!phoenix`

Patterns can include variable substitutions: `{{ foo }}`, wildcards:
`*.example.com` or `192.168.1.*`, and regular expressions `~(web|db).*\.example\.com`

# Inventory

## Inventory files

[intro\_inventory.html](http://docs.ansible.com/intro_inventory.html),
[intro\_dynamic\_inventory.html](http://docs.ansible.com/intro_dynamic_inventory.html)

'INI-file' structure, blocks define groups. Hosts allowed in more than
one group. Non-standard SSH port can follow hostname separated by ':'
(but see also `ansible_port` below).

Sub-sections:

* `[foo:children]`: new group `foo` containing all members if included groups
* `[foo:vars]`: variable definitions for all members of group `foo`

Hostname ranges: `www[01:50].example.com`, `db-[a:f].example.com`

Per-host variables: `foo.example.com foo=bar baz=wibble`

Inventory file defaults to `/etc/ansible/hosts`. Veritable with `-i`
or in the configuration file. The 'file' can also be a dynamic
inventory script. If a directory, all contained files are processed.

You can describe hosts in `/etc/hosts` file or you can also describe hosts with behaviours like this:

```
jumper ansible_host=192.168.1.50 ansible_connection=local ansible_port=5555
```

## Variable files:

[intro\_inventory.html](http://docs.ansible.com/intro_inventory.html)

YAML; given inventory file at `./hosts`:

* `./group_vars/foo`: variable definitions for all members of group `foo`
* `./host_vars/foo.example.com`: variable definitions for foo.example.com

`group_vars` and `host_vars` directories can also exist in the playbook
directory. If both paths exist, variables in the playbook directory
will be loaded second.

## Behavioral inventory parameters:

[intro\_inventory.html](http://docs.ansible.com/intro_inventory.html)

### Host connection:

* `ansible_connection=local|docker|smart|ssh|paramiko`

* For Docker connections:
  * `ansible_host`
  * `ansible_user`
  * `ansible_become`
  * `ansible_docker_extra_args`

### SSH connection:

* `ansible_host`
* `ansible_port`
* `ansible_user`
* `ansible_ssh_pass`
* `ansible_ssh_private_key_file`
* `ansible_ssh_common_args`
* `ansible_sftp_extra_args`
* `ansible_scp_extra_args`
* `ansible_ssh_extra_args`
* `ansible_ssh_pipelining`

### Privilege escalation

* `ansible_become`
* `ansible_become_method`
* `ansible_become_user`
* `ansible_become_pass`

### Remote host environment parameters:

* `ansible_shell_type=sh|csh|fish`
* `ansible_python_interpreter`
* `ansible_*_interpreter`
* `ansible_shell_executable`

# Dynamic Inventory

[Inventory scripts](https://github.com/ansible/ansible/tree/devel/contrib)

## AWS EC2

[Here](ansible_aws.md)

# Playbooks

[playbooks\_intro.html](http://docs.ansible.com/playbooks_intro.html),
[playbooks\_roles.html](http://docs.ansible.com/playbooks_roles.html)

Playbooks are a YAML list of one or more plays. Most (all?) keys are
optional. Lines can be broken on space with continuation lines
indented.

Playbooks consist of a list of one or more 'plays' and/or inclusions:

    ---
    - include: playbook.yml
    - <play>
    - ...

## Plays

[playbooks\_intro.html](http://docs.ansible.com/playbooks_intro.html),
[playbooks\_roles.html](http://docs.ansible.com/playbooks_roles.htm),
[playbooks\_variables.html](http://docs.ansible.com/playbooks_variables.html),
[playbooks\_conditionals.html](http://docs.ansible.com/playbooks_conditionals.html),
[playbooks\_acceleration.html](http://docs.ansible.com/playbooks_acceleration.html),
[playbooks\_delegation.html](http://docs.ansible.com/playbooks_delegation.html),
[playbooks\_prompts.html](http://docs.ansible.com/playbooks_prompts.html),
[playbooks\_tags.html](http://docs.ansible.com/playbooks_tags.htm)
[Forum posting](https://groups.google.com/forum/#!topic/ansible-project/F9mIAfo6orc)
[Forum postinb](https://groups.google.com/forum/#!topic/Ansible-project/MU_ws7zynnI)

Plays consist of play metadata and a sequence of task and handler
definitions, and roles.

[Ansible Playbook bootstrap](ansible_playbook_bootstrap.yml)

Using `encrypt` with `vars_prompt` requires that
[Passlib](http://pythonhosted.org/passlib/) is installed.

In addition the source code implies the availability of the following
which don't *seem* to be mentioned in the documentation: `name`, `user` (deprecated), `port`, `accelerate_ipv6`, `role_names`, and `vault_password`.

## Task definitions

[playbooks\_intro.html](http://docs.ansible.com/playbooks_intro.html),
[playbooks\_roles.html](http://docs.ansible.com/playbooks_roles.html),
[playbooks\_async.html](http://docs.ansible.com/playbooks_async.html),
[playbooks\_checkmode.html](http://docs.ansible.com/[playbooks_checkmode.html),
[playbooks\_delegation.html](http://docs.ansible.com/playbooks_delegation.html),
[playbooks\_environment.html](http://docs.ansible.com/playbooks_environment.html),
[playbooks\_error_handling.html](http://docs.ansible.com/playbooks_error_handling.html),
[playbooks\_tags.html](http://docs.ansible.com/playbooks_tags.html)
[ansible-1-5-released](http://www.ansible.com/blog/2014/02/28/ansible-1-5-released)
[Forum posting](https://groups.google.com/forum/#!topic/ansible-project/F9mIAfo6orc)
[Ansible examples](https://github.com/ansible/ansible-examples/blob/master/language_features/complex_args.yml)

Each task definition is a list of items, normally including at least a
name and a module invocation:

    - name: task
      remote_user: apache
      sudo: yes
      sudo_user: postgress
      sudo_pass: wibble
      su: yes
      su_user: exim
      ignore_errors: True
      delegate_to: 127.0.0.1
      async: 45
      poll: 5
      always_run: no
      run_once: false
      meta: flush_handlers
      no_log: true
      environment: <hash>
      environment:
        var1: val1
        var2: val2
      tags:
        - stuff
        - nonsence
      <module>: src=template.j2 dest=/etc/foo.conf
      action: <module>, src=template.j2 dest=/etc/foo.conf
      action: <module>
      args:
          src=template.j2
          dest=/etc/foo.conf
      local_action: <module> /usr/bin/take_out_of_pool {{ inventory_hostname }}
      when: ansible_os_family == "Debian"
      register: result
      failed_when: "'FAILED' in result.stderr"
      changed_when: result.rc != 2
      notify:
        - restart apache

`delegate_to: 127.0.0.1` is implied by `local_action:`

The forms `<module>: <args>`, `action: <module> <args>`, and `local_action: <module> <args>` are mutually-exclusive.

Additional keys `when_*`, `until`, `retries` and `delay` are documented below under 'Loops'.

In addition the source code implies the availability of the following
which don't *seem* to be mentioned in the documentation:
`first_available_file` (deprecated), `transport`,
`connection`, `any_errors_fatal`.

# Roles

[playbooks\_roles.html](http://docs.ansible.com/playbooks_roles.html)

Directory structure:

    playbook.yml
    roles/
       common/
         tasks/
           main.yml
         handlers/
           main.yml
         vars/
           main.yml
         meta/
           main.yml
         defaults/
           main.yml
         files/
         templates/
         library/

# Modules

[modules.htm](http://docs.ansible.com/modules.htm),
[modules\_by\_category.html](http://docs.ansible.com/modules_by_category.html)

List all installed modules with

    ansible-doc --list

Document a particular module with

    ansible-doc <module>

Show playbook snippet for specified module

    ansible-doc -i <module>

# Variables

[playbooks\_roles.html](http://docs.ansible.com/playbooks_roles.html),
[playbooks\_variables.html](http://docs.ansible.com/playbooks_variables.html)
[examples](https://github.com/ansible/ansible-examples)

Names: letters, digits, underscores; starting with a letter.

## Substitution examples:

* `{{ var }}`
* `{{ var["key1"]["key2"]}}`
* `{{ var.key1.key2 }}`
* `{{ list[0] }}`

YAML requires an item starting with a variable substitution to be quoted.

## Variable priorities

[variables\precedence](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)

1. Highest priority:
    * `--extra-vars` on the command line (including `--extra-vars "@some_file.json"`)
2. General:
    * `vars` component of a playbook
    * From files referenced by `vars_file` in a playbook
    * From included files (incl. roles)
    * Parameters passed to includes
    * `register:` in tasks
3. Lower priority:
    * Inventory (set on host or group)
4. Lower priority:
    * Facts (see below)
    * Any `/etc/ansible/facts.d/filename.fact` on managed machines
      (sets variables with `ansible_local.filename. prefix)
* Lowest priority
    * Role defaults (from defaults/main.yml)

## Variable Scopes

1. Host
2. Play
3. Global

## Built-in:

* `hostvars` (e.g. `hostvars[other.example.com][...]`)
* `group_names` (groups containing current host)
* `groups` (all groups and hosts in the inventory)
* `inventory_hostname` (current host as in inventory)
* `inventory_hostname_short` (first component of inventory_hostname)
* `play_hosts` (hostnames in scope for current play)
* `inventory_dir` (location of the inventory)
* `inventoty_file` (name of the inventory)
* `role_path`
* `ansible_check_mode=True|False`



## Facts:

Run `ansible hostname -m setup` or

```
ansible group -i hosts/list \
  -m setup --private-key=~/.ssh/your_key.pem \
  -u ubuntu > ansible_facts.json
```

Important:

* `ansible_distribution`
* `ansible_distribution_release`
* `ansible_distribution_version`
* `ansible_fqdn`
* `ansible_hostname`
* `ansible_os_family`
* `ansible_pkg_mgr`
* `ansible_default_ipv4.address`
* `ansible_default_ipv6.address`

Usage:

`{{ hostvars['asdf.example.com']['ansible_os_family'] }}`

## Local Facts (Facts.d):

[variables\local_facts](http://docs.ansible.com/ansible/playbooks_variables.html#local-facts-facts-d)

* @ remote: `/etc/ansible/facts.d/*.fact << JSON|INI|exe2json`
* @ command level: `ansible <hostname> -m setup -a "filter=ansible_local"`
* @ template level: `{{ ansible_local.preferences.general.asdf }}`

## Fact Caching (@ansible.cfg)

* Option 1 redis

```
[defaults]
gathering = smart
fact_caching = redis
fact_caching_timeout = 86400
```

Requirements:

```
apt-get install redis
pip install redis
```

* Option 2 json file

```
[defaults]
gathering = smart
fact_caching = jsonfile
fact_caching_connection = /path/to/cachedir
fact_caching_timeout = 86400
```

## Content of 'registered' variables:

[playbooks\_conditionals.html](http://docs.ansible.com/playbooks_conditionals.html),
[playbooks\_loops.html](http://docs.ansible.com/playbooks_loops.html)

Depends on module. Typically includes:

* `.rc`
* `.stdout`
* `.stdout_lines`
* `.changed`
* `.msg` (following failure)
* `.results` (when used in a loop)

See also `failed`, `changed`, etc filters.

When used in a loop the `result` element is a list containing all
responses from the module.

## Additionally available in templates:

* `ansible_managed`: string containing the information below
* `template_host`: node name of the templateâs machine
* `template_uid`: the owner
* `template_path`: absolute path of the template
* `template_fullpath`: the absolute path of the template
* `template_run_date`: the date that the template was rendered

# Filters

[playbooks\_variables.html](http://docs.ansible.com/playbooks_variables.html)

* `{{ var | to_nice_json }}`
* `{{ var | to_json }}`
* `{{ var | from_json }}`
* `{{ var | to_nice_yml }}`
* `{{ var | to_yml }}`
* `{{ var | from_yml }}`
* `{{ result | failed }}`
* `{{ result | changed }}`
* `{{ result | success }}`
* `{{ result | skipped }}`
* `{{ var | manditory }}`
* `{{ var | default(5) }}`
* `{{ list1 | unique }}`
* `{{ list1 | union(list2) }}`
* `{{ list1 | intersect(list2) }}`
* `{{ list1 | difference(list2) }}`
* `{{ list1 | symmetric_difference(list2) }}`
* `{{ ver1 | version_compare(ver2, operator='>=', strict=True }}`
* `{{ list | random }}`
* `{{ number | random }}`
* `{{ number | random(start=1, step=10) }}`
* `{{ list | join(" ") }}`
* `{{ path | basename }}`
* `{{ path | dirname }}`
* `{{ path | expanduser }}`
* `{{ path | realpath }}`
* `{{ var | b64decode }}`
* `{{ var | b64encode }}`
* `{{ filename | md5 }}`
* `{{ var | bool }}`
* `{{ var | int }}`
* `{{ var | quote }}`
* `{{ var | md5 }}`
* `{{ var | fileglob }}`
* `{{ var | match }}`
* `{{ var | search }}`
* `{{ var | regex }}`
* `{{ var | regexp_replace('from', 'to' )}}`

See also [default jinja2
filters](http://jinja.pocoo.org/docs/templates/#builtin-filters). In
YAML, values starting `{` must be quoted.

# Lookups

[playbooks\_lookups.html](http://docs.ansible.com/playbooks_lookups.html)

Lookups are evaluated on the control machine.

* `{{ lookup('file', '/etc/foo.txt') }}`
* `{{ lookup('password', '/tmp/passwordfile length=20 chars=ascii_letters,digits') }}`
* `{{ lookup('env','HOME') }}`
* `{{ lookup('pipe','date') }}`
* `{{ lookup('redis_kv', 'redis://localhost:6379,somekey') }}`
* `{{ lookup('dnstxt', 'example.com') }}`
* `{{ lookup('template', './some_template.j2') }}`

Lookups can be assigned to variables and will be evaluated each time
the variable is used.

Lookup plugins also support loop iteration (see below).

# Conditions

[playbooks\_conditionals.html](http://docs.ansible.com/playbooks_conditionals.html)

`when: <condition>`, where condition is:

* `var == "Vaue"`, `var >= 5`, etc.
* `var`, where `var` coreces to boolean (yes, true, True, TRUE)
* `var is defined`, `var is not defined`
* `<condition1> and <condition2>` (also `or`?)

Combined with `with_items`, the when statement is processed for each item.

`when` can also be applied to includes and roles. Conditional Imports
and variable substitution in file and template names can avoid the
need for explicit conditionals.

# Loops

[playbooks\_loops.html](http://docs.ansible.com/playbooks_loops.html)

In addition the source code implies the availability of the following
which don't *seem* to be mentioned in the documentation: `csvfile`, `etcd`, `inventory_hostname`.

## Standard:

```
- user: name={{ item }} state=present groups=wheel
  with_items:
    - testuser1
    - testuser2

- name: add several users
  user: name={{ item.name }} state=present groups={{ item.groups }}
  with_items:
    - { name: 'testuser1', groups: 'wheel' }
    - { name: 'testuser2', groups: 'root' }
```

## Nested:

```
- mysql_user: name={{ item[0] }} priv={{ item[1] }}.*:ALL
                                 append_privs=yes password=foo
  with_nested:
    - [ 'alice', 'bob', 'eve' ]
    - [ 'clientdb', 'employeedb', 'providerdb' ]
```

## Over hashes:

```
users:
  alice:
    name: Alice Appleworth
    telephone: 123-456-7890
  bob:
    name: Bob Bananarama
    telephone: 987-654-3210
```

```
tasks:
  - name: Print phone records
    debug: msg="User {{ item.key }} is {{ item.value.name }}
                     ({{ item.value.telephone }})"
    with_dict: users
```

## Fileglob:

```
- copy: src={{ item }} dest=/etc/fooapp/ owner=root mode=600
  with_fileglob:
    - /playbooks/files/fooapp/*
```

In a role, relative paths resolve relative to the
`roles/<rolename>/files` directory.

## With content of file:

(see example for `authorized_key` module)

```
- authorized_key: user=deploy key="{{ item }}"
  with_file:
    - public_keys/doe-jane
    - public_keys/doe-john
```

See also the `file` lookup when the content of a file is needed.

## Parallel sets of data:

```
---
alpha: [ 'a', 'b', 'c', 'd' ]
numbers:  [ 1, 2, 3, 4 ]
```

```
- debug: msg="{{ item.0 }} and {{ item.1 }}"
  with_together:
    - alpha
    - numbers
```

## Subelements:

Given

```
---
users:
  - name: alice
    authorized:
      - /tmp/alice/onekey.pub
      - /tmp/alice/twokey.pub
    mysql:
        password: mysql-password
        hosts:
          - "%"
          - "127.0.0.1"
          - "::1"
          - "localhost"
        privs:
          - "*.*:SELECT"
          - "DB1.*:ALL"
  - name: bob
    authorized:
      - /tmp/bob/id_rsa.pub
    mysql:
        password: other-mysql-password
        hosts:
          - "db1"
        privs:
          - "*.*:SELECT"
          - "DB2.*:ALL"
```

```
- name: Setup MySQL users
  mysql_user: name={{ item.0.name }} password={{ item.0.mysql.password }}
              host={{ item.1 }} priv={{ item.0.mysql.privs | join('/') }}
  with_subelements:
    - "{{ users }}"
    - mysql.hosts
```

## Integer sequence:

Decimal, hexadecimal (0x3f8) or octal (0600)

```
- user: name={{ item }} state=present groups=evens
  with_sequence: start=0 end=32 format=testuser%02x

  with_sequence: start=4 end=16 stride=2

  with_sequence: count=4
```

## Random choice:

```
- debug: msg={{ item }}
  with_random_choice:
     - "go through the door"
     - "drink from the goblet"
     - "press the red button"
     - "do nothing"
```

## Do-Until

```
- action: shell /usr/bin/foo
  register: result
  until: result.stdout.find("all systems go") != -1
  retries: 5
  delay: 10
```

## Results of a local program

```
- name: Example of looping over a command result
  shell: /usr/bin/frobnicate {{ item }}
  with_lines: /usr/bin/frobnications_per_host
                       --param {{ inventory_hostname }}
```

To loop over the results of a remote program, use `register: result`
and then `with_items: result.stdout_lines` in a subsequent
task.

## Indexed list

```
- name: indexed loop demo
  debug: msg="at array position {{ item.0 }} there is
                                     a value {{ item.1 }}"
  with_indexed_items: "{{ some_list }}"
```

## INI file

Given

```
[section1]
value1=section1/value1
value2=section1/value2

[section2]
value1=section2/value1
value2=section2/value2
```

```
- debug: msg="{{ item }}"
  with_ini: value[1-2] section=section1 file=lookup.ini re=true
```

## Inventory

```
- debug: msg={{ item }}
  with_items: "{{ groups['all'] }}"

- debug: msg={{ item }}
  with_items: play_hosts
```

```
- debug: msg={{ item }}
  with_inventory_hostnames: all

- debug: msg={{ item }}
  with_inventory_hostnames: all:!www
```

## Flattened list

Given

```
---
# file: roles/foo/vars/main.yml
packages_base:
  - [ 'foo-package', 'bar-package' ]
packages_apps:
  - [ ['one-package', 'two-package' ]]
  - [ ['red-package'], ['blue-package']]
```

```
- name: flattened loop demo
  yum: name={{ item }} state=installed
  with_flattened:
    - packages_base
    - packages_apps
```

## First found

```
- name: template a file
  template: src={{ item }} dest=/etc/myapp/foo.conf
  with_first_found:
    - files:
        - {{ ansible_distribution }}.conf
        - default.conf
      paths:
         - search_location_one/somedir/
         - /opt/other_location/somedir/
```

# Tags

Both plays and tasks support a `tags:` attribute.

```
- template: src=templates/src.j2 dest=/etc/foo.conf
  tags:
    - configuration
```

Tags can be applied to roles and includes (effectively tagging all
included tasks)

```
roles:
    - { role: webserver, port: 5000, tags: [ 'web', 'foo' ] }

- include: foo.yml tags=web,foo
```

To select by tag:

```
ansible-playbook example.yml --tags "configuration,packages"
ansible-playbook example.yml --skip-tags "notification"
```

# FAQ

[Here](http://docs.ansible.com/ansible/faq.html)
