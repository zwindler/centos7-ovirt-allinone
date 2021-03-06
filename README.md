# centos7-ovirt-allinone
Playbooks to help build a ovirt allinone node on CentOS 7

# Prerequisites
You should begin with a fresh CentOS 7 server which meets ovirt requirements

For this playbook, you also need to have a lvm vg. If you don't have one, please create one

```
fdisk /dev/sda #don't forget type t/8e before w 

partx -v -a /dev/sda
pvcreate /dev/sda4
vgcreate vg_data /dev/sda4
```

You also need Ansible 2.3 to automate ovirt configuration (adding VMs and networks and such), so you'll have to compile it if you want more than just the 3 first roles (which deliver a ready to use ovirt).

# Installing prerequisites and packages

```
yum install -y epel-release
yum install -y git ansible
```

If you want ovirt_* modules in ansible

```
yum -y install python-pip
pip install --upgrade pip
pip install sphinx
cd ~
git clone https://github.com/ansible/ansible && cd ansible
make
make install
ansible --version
ansible 2.3.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = Default w/o overrides
```

## Getting the ansible roles

```
cd /etc/ansible/roles
git clone https://github.com/zwindler/centos7-ovirt-allinone
git clone https://github.com/zwindler/centos7-ovirt-enginesetup
git clone https://github.com/zwindler/centos7-ovirt-postenginesetup
```

Side note: answer file for has been generated with command "engine-setup --generate-answer=/tmp/engine-setup-answers.j2"

## Creating top level playbook
```
cd /etc/ansible
cat > ovirt-allinone.yml << EOF
---
- hosts: localhost
  remote_user: root
  roles:
  - centos7-ovirt-allinone
EOF
```

## Executing playbooks

If you want to install oVirt as All In One
```
ansible-playbook ovirt-allinone.yml
```

If you want to launch ovirt engine setup with the default configuration. You can review/change the variables given by opening the generated answer file located at /tmp/engine-setup-answers. 
```
ansible-playbook ovirt-enginesetup.yml
```

From there, you should be able to connect to the ovirt web console to https://[FQDN_OF_YOUR_HOST]


If you want to continue the setup automatically, centos7-ovirt-postenginesetup corrects/adapts a few things
```
ansible-playbook ovirt-postenginesetup.yml
```
