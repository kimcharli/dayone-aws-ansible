[[_TOC_]]
---
# Ansible client setup

## Use CloudFormation
Template: https://raw.githubusercontent.com/kimcharli/dayone-aws-terraform/master/cf-for-ubuntu-ec2.yml
From AWS console:
```
services
-> CloudFormation
-> CreateStack
  Select Template
    Choose template
-> Next
  Stack Name: day-one-stack
  SSHLocation: 66.129.241.0/24
-> Next
  Tags: Key=Name
        Value=day-one-ubuntu
-> Next
-> Create
```

Once get online, follow Connect
```
ssh -i "day-one.pem" ubuntu@ec2-35-162-18-126.us-west-2.compute.amazonaws.com
```

```
ckim-mbp:Downloads ckim$ ssh -i "day-one.pem" ubuntu@ec2-35-162-18-126.us-west-2.compute.amazonaws.com
The authenticity of host 'ec2-35-162-18-126.us-west-2.compute.amazonaws.com (35.162.18.126)' can't be established.
ECDSA key fingerprint is SHA256:LNX+tIlSk2/xF0PMr5qXP4rJm4tHtzLlMmqIFLRVpE8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'ec2-35-162-18-126.us-west-2.compute.amazonaws.com,35.162.18.126' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.4.0-1052-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.



The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-172-31-20-165:~$


ubuntu@ip-172-31-20-165:~$ sudo apt update

ubuntu@ip-172-31-20-165:~$ sudo apt -y install awscli python-pip unzip

ubuntu@ip-172-31-20-165:~$ pip install ansible==2.5.0 boto3 boto

ubuntu@ip-172-31-20-165:~$ pip install --upgrade pip

ubuntu@ip-172-31-20-165:~$ ansible --version
ansible 2.5.0
  config file = None
  configured module search path = [u'/home/ubuntu/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /home/ubuntu/.local/lib/python2.7/site-packages/ansible
  executable location = /home/ubuntu/.local/bin/ansible
  python version = 2.7.12 (default, Dec  4 2017, 14:50:18) [GCC 5.4.0 20160609]
ubuntu@ip-172-31-20-165:~$

ubuntu@ip-172-31-20-165:~$ aws configure
AWS Access Key ID [None]: AAAAAAAAAAAAAAAAAAAA
AWS Secret Access Key [None]: SSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSSS
Default region name [None]: us-west-2
Default output format [None]: json
ubuntu@ip-172-31-20-165:~$

ubuntu@ip-172-31-20-165:~$ aws ec2 describe-instances --region us-west-2 --query 'Reservations[*].Instances[*].Tags[?Key==`Name`].{Name:Value}'
[
    [
        [
            {
                "Name": "day-one-ubuntu"
            }
        ]
    ]
]
ubuntu@ip-172-31-20-165:~$
```


```
ubuntu@ip-172-31-20-165:~/dayone-ansible$


ubuntu@ip-172-31-20-165:~$ wget https://github.com/kimcharli/dayone-aws-ansible/archive/master.zip
--2018-04-03 04:05:39--  https://github.com/kimcharli/dayone-aws-ansible/archive/master.zip
Resolving github.com (github.com)... 192.30.255.112, 192.30.255.113
Connecting to github.com (github.com)|192.30.255.112|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://codeload.github.com/kimcharli/dayone-aws-ansible/zip/master [following]
--2018-04-03 04:05:39--  https://codeload.github.com/kimcharli/dayone-aws-ansible/zip/master
Resolving codeload.github.com (codeload.github.com)... 192.30.255.121, 192.30.255.120
Connecting to codeload.github.com (codeload.github.com)|192.30.255.121|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/zip]
Saving to: ‚Äòmaster.zip‚Äô

master.zip                                 [ <=>                                                                        ]   7.55K  --.-KB/s    in 0s

2018-04-03 04:05:40 (95.8 MB/s) - ‚Äòmaster.zip‚Äô saved [7730]

ubuntu@ip-172-31-20-165:~$

ubuntu@ip-172-31-20-165:~$ unzip master.zip
Archive:  master.zip
7e293ffb7c045032a1acf4592e74950d1a7009a1
   creating: dayone-aws-ansible-master/
  inflating: dayone-aws-ansible-master/README.md
   creating: dayone-aws-ansible-master/dayone-ansible/
  inflating: dayone-aws-ansible-master/dayone-ansible/ansible.cfg
   creating: dayone-aws-ansible-master/dayone-ansible/roles/
   creating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/
   creating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/security_group/
   creating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/security_group/tasks/
  inflating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/security_group/tasks/main.yml
   creating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/subnet/
   creating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/subnet/tasks/
  inflating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/subnet/tasks/main.yml
   creating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/tasks/
  inflating: dayone-aws-ansible-master/dayone-ansible/roles/do_vpc/tasks/main.yml
  inflating: dayone-aws-ansible-master/dayone-ansible/site.yml
   creating: dayone-aws-ansible-master/inventory/
   creating: dayone-aws-ansible-master/inventory/group_vars/
  inflating: dayone-aws-ansible-master/inventory/group_vars/all.yml
 extracting: dayone-aws-ansible-master/inventory/hosts
ubuntu@ip-172-31-20-165:~$
ubuntu@ip-172-31-20-165:~$ cd dayone-aws-ansible-master/dayone-ansible/
ubuntu@ip-172-31-20-165:~/dayone-aws-ansible-master/dayone-ansible$
ubuntu@ip-172-31-20-165:~/dayone-aws-ansible-master/dayone-ansible$ ansible-playbook -i ../inventory/hosts site.yml



ckim-mbp:Downloads ckim$ ssh -i "day-one.pem" root@ec2-34-208-121-121.us-west-2.compute.amazonaws.com
The authenticity of host 'ec2-34-208-121-121.us-west-2.compute.amazonaws.com (34.208.121.121)' can't be established.
ECDSA key fingerprint is SHA256:iXV/jPbeKkSWFajE0h6FYizSlSeGegdG+h+6Dx2PAy8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'ec2-34-208-121-121.us-west-2.compute.amazonaws.com,34.208.121.121' (ECDSA) to the list of known hosts.
Permission denied (publickey).
ckim-mbp:Downloads ckim$ ssh -i "day-one.pem" ec2-user@ec2-34-208-121-121.us-west-2.compute.amazonaws.com
--- JUNOS 17.4R1-S1.9 Kernel 64-bit  JNPR-11.0-20180127.fdc8dfc_buil
ec2-user> show interfaces terse ge*
Interface               Admin Link Proto    Local                 Remote
ge-0/0/0                up    up

ec2-user> exit

Connection to ec2-34-208-121-121.us-west-2.compute.amazonaws.com closed.
ckim-mbp:Downloads ckim$
```


dynamic inventory
http://docs.ansible.com/ansible/latest/intro_dynamic_inventory.html#example-aws-ec2-external-inventory-script
```
( cd ../inventory ; wget https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.py )
( cd ../inventory ; wget https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.ini )
( cd ../inventory ; python ec2.py )

```

