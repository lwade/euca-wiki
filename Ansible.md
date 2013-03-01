**Author:** mpdeehan (Michael DeHaan)

**Website:** <a href="http://ansible.cc">http://ansible.cc</a>

**Download URL:** <a href="https://github.com/ansible/ansible">https://github.com/ansible/ansible</a> and distribution packages, see website.

**License:** GPLv3

***
## Getting Started

### Installation

Installation instructions can be found here:  http://ansible.cc/docs/

Documentation around usage is quite comprehensive and should be consulted to familiarise yourself with the tool.  It's all fairly easy, the learning curve is comfortable.

## Working with EC2 and Eucalyptus

### Orchestrating instances with Ansible

Working with orchestration of instances within EC2 and Eucalyptus is fairly trivial.  The most basic way of interacting with the cloud to deploy workloads and configure systems would be to define a host group containing the IP/FQDN of your instances:

```
[aws-instances]
ec2-1.1.1.1.compute-1.amazonaws.com
ec2-2.2.2.2.compute-1.amazonaws.com
ec2-3.3.3.3.compute-1.amazonaws.com

[euca-instances]
euca-192-168-61-111.cloud1.euca.example.com
euca-192-168-61-112.cloud1.euca.example.com
euca-192-168-61-113.cloud1.euca.example.com
```

However, this is a pretty static for a host inventory.  Wouldn't it be really nice if you could run a set number of instances and automagically configure all of those instance as part of the same playbook?

WELL, TODAY IS YOUR LUCKY DAY!  ENTER THE EC2 MODULES FOR ANSIBLE ...

### Ansible EC2 Modules

At the time of writing, Ansible 1.1 is in development but there are already a number of EC2-related modules available for users:

- ec2 : this is the module for launching instances
- ec2_vol : this is a module for creating and attaching volumes to instances
- ec2_facts : this is a fact-gathering module, grabbing and returning EC2 metadata
- cloudformation : this module is for launching a CloudFormation from template in AWS

All of these modules work very well in Eucalyptus apart from cloudformation (since Eucalyptus doesn't support this service) and some arguments for the ec2 module, which are optional.

### Example of instance launch and configuration

Some further examples can be found here: https://github.com/lwade/eucalyptus-playbook/

The playbook example below demonstrates 2 separate plays.  The first play launches a load of instances and creates some persistent (EBS) storage for them, the second play configures the instances:

```
- name: Stage instance(s)
  hosts: local
  connection: local
  user: root
  gather_facts: false

  vars:
      keypair: admin
      instance_type: m1.small
      security_group: default
      image: emi-048B3A37

  tasks:
    - name: Launch instance
      local_action: ec2 keypair=$keypair group=$security_group instance_type=$instance_type image=$image wait=true count=5
      register: ec2
     
    - name: Add new instance to host group
      local_action: add_host hostname=${item.public_ip} groupname=dbservers
      with_items: ${ec2.instances}

    - name: Create a volume and attach
      local_action: ec2_vol volume_size=1 instance=${item.id} device_name=sdb
      with_items: ${ec2.instances}
      register: ec2_vol

- name: Configure instance
  hosts: dbservers
  user: root
  gather_facts: True
     
  handlers:
    - name: restart postgres
      action: service name=postgresql state=restarted
 
  tasks:
    - name: Ensure NTP is up and running
      action: service name=ntpd state=started
    
    - name: Install postgreSQL
      action: yum pkg=postgres state=present
 
    - name: Format persistent storage
      action: command mkfs.ext4 /dev/sdb

do more stuff ....
```
Remove that last line and save it: myplaybook.yml.  You'd then launch this against an inventory/hosts file and supply the private key based on the keypair you chose:

```
ansible-playbook -i inventory myplaybook.yml -v --private-key=ec2_private_key.pem -k
```

There we have it, a fairly basic example how to use the module to launch instances in EC2/Euca and then _do something_ to configure them.

## Additional information

Coderwall also has some tagged tips n' tricks for Ansible: https://coderwall.com/p/t/ansible

You can track my blog here for some posts which focus on Ansible usage: https://lesterwade.wordpress.com/ with AWS + Euca.


