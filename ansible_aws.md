# Ansible: Dynamic Inventory

## AWS EC2

Requirement: `pip install boto`

Credentials:

* `/etc/boto.cfg` - for site-wide settings that all users on this machine will use
* `~/.aws/credentials` - for credentials shared between SDKs
* `~/.boto` - for user-specific settings

Credentials format:

```
[profile dev]
aws_access_key_id = <dev access key>
aws_secret_access_key = <dev secret key>

[profile prod]
aws_access_key_id = <prod access key>
aws_secret_access_key = <prod secret key>
```

Usage:

```
AWS_PROFILE=prod ansible-playbook -i ec2.py myplaybook.yml
```

* Run: Option 1

```shell
ansible -i ec2.py -u ubuntu us-east-1d -m ping
```

* Run: Option 2

```shell
chmod +x /etc/ansible/hosts
more /etc/ansible/ec2.ini
```

Scripts:

* [ec2.py](https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.py).
* [ec2.ini](https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.ini)

Usage:

```shell
./ec2.py --list
./ec2.py --profile prod
./ec2.py --refresh-cache
```

Mappings:

* Global: In group `ec2`.
* Instance ID: e.g. `i-00112233`, `i-a1b1c1d1`, ...
* Region: e.g. `us-east-1`, `us-west-2`, ...
* Availability Zone: e.g. `us-east-1a`, `us-east-1b`, ...
* Security Group: e.g. `security_group_default`, `security_group_webservers`, `security_group_Pete_s_Fancy_Group`, ...
* Tags: In the format **tag_KEY_VALUE** e.g. tag_Name_Web can be used as is tag_Name_redis-master-001 becomes tag_Name_redis_master_001 tag_aws_cloudformation_logical-id_WebServerGroup becomes tag_aws_cloudformation_logical_id_WebServerGroup
