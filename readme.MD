# Ansible Role
- Role is a proper structure of variables, tasks, templates, handlers etc. Using roles we can re use the code.
Role main principle is DRY principle(i.e reuse the code)

Ansible role git folder will have a folder called roles. ex: for db we created a folder called db(common folder) inside roles.
This db folder will have so many folders like:

tasks/ ---> we will mention all tasks here
handlers/ ---> we will mention all notifiers here
templates/ ---> we will mention all templates here, with variables.
files/ ---> we will mention all files here (ex: backend.service), without variables
vars/ ---> variables related to roles are here
defaults/ ---> low priority variables
meta/ ---> dependencies are mentioned here
library/, module_utils/, lookup_plugins/ ---> when there is no module available then we develop a custom module in python language and use it here.

difference between templates and files is, files is without variables whereas templates is with variables.

# Ansible config
if we see ansible --version, we can see where ansible config file is loading from.
changes can be made and used in configuration file which will be searched in following order:

1.ANSIBLE_CONFIG (we can set environment variable using export ANSIBLE_CONFIG = path).
we can unset environment variable by "unset <variable-name>"

2.ansible.cfg (from current directory)

3. .ansible.cfg (in home directory)---> (ex: cp /tmp/ansible.cfg ~/.ansible.cfg(should save with "dot" "."))

4. when above three are not there then config file is used from /etc/ansible/ansible.cfg

- Best recommendation is to save ansible.cfg in current folder.
when we open vim /etc/ansible/ansible.cfg they say to check below url
https://github.com/ansible/ansible/blob/stable-2.9/examples/ansible.cfg to set what required to keep in config file. Everything here is in commented state so check and keep what we want.

we run playbook as below
- ansible-playbook -i inventory.ini -e ansible_user=ec2-user -e ansible_password=DevOps321 <yaml-file-name> 
but if we set inventory = <path of inventory> in ansible.cfg we can remove -i inventory.ini from above command while running.

if we set remote_user = ec2-user we can remove -e ansible_user=ec2-user from above when running.
as we dont have option to save ansible_password in ansible.cfg, we can keep in inventory as below:

[all:vars]
ansible_password=DevOps321

.j2 ---> jinja2 template language

# Ansible templates
- you can keep the variables inside ansible files and replace the value with variables
ansible.builtin.template is exactly like copy but when its given ansibles goes to templates folder and search for src <name> file

# Ansible handlers
- handlers are notifiers in ansible.Usually when there is a change in some configuration ansible can notify the particular task to restart server.

To run the code:
ansible-playbook main.yaml -e component=db
ansible-playbook main.yaml -e component=backend
ansible-playbook main.yaml -e component=frontend

# Ansible Vault
ansible-vault create <file-name>.yaml ---> need to set password (ex: admin1234)
ansible-vault view <file-name>.yaml ---> to see content in the file
ansible-vault edit <file-name>.yaml  ----> to edit (i have set mysql_password), ansible_user and ansible_password in the credentials.yaml so i removed from ansible.cfg and inventory.ini


when vault is used, run as:
ansible-playbook db.yaml --ask-vault-pass

- we created vault file and passed as variable file in ansible-playbook.

# Ansible Tags
- If you have a large playbook, it may be useful to run only specific parts of it instead of running the entire playbook using tags.

To run only tagged tasks:
ansible-playbook backend.yaml --ask-vault-pass -t deployment

# Ansible dynamic inventory
Ansible dynamic inventory is a plugin that can fetch the hostnames dynamically from the cloud providers.
we use ansible dynamic inventory with aws_ec2 plugin, when ever we require some changes in the servers like 
some emergency changes we write some playbook and we run against those servers dynamically.

ex: there are 20 servers runing based on traffic, you need to do some simple thing on emergency basis in all these servers.

1. Query AWS EC2 instances.
2. Query only specific instances based on project.
3. Get their IP address
4. Now connect

create IAM user and give CLI access:
In command line, do --> aws configure
.
.
.
- create aws_ec2.yaml
- ansible to connect to aws, we need botocore, boto3 modules
- pip3.9 install boto3 botocore
- ansible-inventory -i aws_ec2.yaml --graph (gets all instances)
To get specific instances 
instance name starts with Backend, use keyed groups...

create dynamic.yaml
ansible-playbook -i aws_ec2.yaml -e ansible_user=ec2-user -e ansible_password=DevOps321 dynamic.yaml

# Ansible forks
t2.micro --> 1cpu 1GB RAM--- my ansible server is small
there should be some resources consumed to establish connection and run playbooks

keep forks=3 in ansible.cfg, then it queries 3 servers at a time

- ansible-playbook -vvv -i aws_ec2.yaml -e ansible_user=ec2-user -e ansible_password=DevOps321 dynamic.yaml
(to see whats happening in background for debugging purpose)

- set log_path in ansible.cfg to store logs









