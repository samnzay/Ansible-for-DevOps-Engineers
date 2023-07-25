## Ansible - Running ad-hoc Commands

Running Ad-Hoc Commands with ansible

First and fore most, make sure your System repository index is up to date.

```shell
sudo apt update
```

- Install Ansible
```shell
sudo apt install ansible
```
- To Use ansible more effectivelly we need an inventory file. The inventory file is going to contain the IP Address or Fully Qualified Domain Names of each of the hosts that we are going to manage with ansible.
```shell
# In Parent directory
touch inventory
```

Put All the IP address in there. In this example, iventory will looke like this:
```
192.168.57.100 ansible-control
192.168.57.101 server-1
192.168.57.102 server-2
192.168.57.103 server-3
```
### Attention Windows Users!

>**Warning**: If you are using Windows and running virtual environment with Vagrant Virtuabox, `Make sure you ssh into ansible-control Linux VM` with `vagrant ssh` command, or use MobaXterm. otherwise you will not be able to use ansible on windows.

In your parent directory where Vagrantfile resides, run:
```shell
vagrant ssh ansible-control
```
- Change directory to vagrant folder that maps the original working directory [on Windows ] into your Linux VM.
```shell
# Change directory to vagrant
cd /vagrant
# List files and folders in there
ls
```
`Results`: The same exact files and folders i have in my windows project directory where i am writting this tutorial from! Hence, I will freely run ansible commands later from here.
```
'1-SSH&SETUP'   2-Ad-hoc-Commands   hosts_file   Vagrantfile
```

- Copy `hosts_file` content to `etc/hosts`.
```shell
sudo cp hosts_file /etc/hosts
```
### Test Ansible
Now check if everything works correctly with Ansible.  Ansible will go ahead and makes connection with Everyhing inside the inventory. Allow us to make sure that everything is working

```shell
ansible all --key-file ~/.ssh/ansible -i inventory -m ping
# --key-file: Stands for ssh Key to use for access
# -i: Inventory file to use
# -m: ansible module to use.
```
Expected Results:
```
 [WARNING] Ansible is in a world writable directory (/vagrant), ignoring it as an ansible.cfg source.
192.168.57.101 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.57.103 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
192.168.57.102 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

```

#### Simplify Ansible commands
We can Simplify our ansible commands by setting default configurations inside `ansible.cfg`.
This file is red by ansible whenever i run it.
```shell
# In parent directory
touch ansible.cfg
```

- Copy this content to the "ansible.cfg" file
```shell
[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible
```

- We have set default configurations in ansiblecfg file. Now Our command can be simplified like below:

```shell
ansible all -m ping
```
- List hosts
```shell
ansible all --list-hosts
```
- Gather Facts
```shell
ansible all -m setup
```
- Gather Facts Limit to specific IP or hosts
```shell
ansible all -m setup --limit 192.168.57.103
```
