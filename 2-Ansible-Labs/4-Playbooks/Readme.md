## Ansible Playbooks
The real power of ansible comes in playbooks. We are going to go into that in a momment.

Lets say we want to install apache in all our servers. Lets use playbooks
- Create a playbook in  yml format

```shell
touch 1-install_apache.yml
```
- Paste this data into the file
```shell
- hosts: all
  become: true
  tasks:
    
    - name: Install apache2 package
      apt:
        name: apache2
        state: latest
```

- Run the playbook file
```shell
ansible-playbook --ask-become-pass 4-Playbooks/1-install_apache.yml
```
```Enter password when prompted```. Mine is `vagrant` on all my VMs
The command run successfully.

Expected results:
```
PLAY [all] ***********************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [192.168.57.101]
ok: [192.168.57.102]
ok: [192.168.57.103]

TASK [Install apache2 package] ***************************************************************************************************************************
changed: [192.168.57.101]
changed: [192.168.57.103]
changed: [192.168.57.102]

PLAY RECAP ***********************************************************************************************************************************************
192.168.57.101             : ok=2    changed=1    unreachable=0    failed=0
192.168.57.102             : ok=2    changed=1    unreachable=0    failed=0
192.168.57.103             : ok=2    changed=1    unreachable=0    failed=0
```
When the package name does not exist, it's installation will fail.

- Lets add another play on our yml file. copy the 1-install-apache.yml to 2-install-apache.yml
```shell
cp 4-Playbooks/1-install_apache.yml 4-Playbooks/2-install_apache.yml
```

Update the `4-Playbooks/2-install_apache.yml` file, before the Install apache play. 
```shell
- name: Update Repository
  apt: 
    update_cache: yes
```

- Add php support package for apache2.
```shell
cp 4-Playbooks/2-install_apache.yml 4-Playbooks/3-install_apache.yml
```
Add a play for that in the `4-Playbooks/3-install_apache.yml` file
```shell
- name: add php support for apache
  apt:
    name: libapache2-mod-php
    state: latest

```

- Remove apache. Create `remove_apache.yml`
```shell
cp 4-Playbooks/3-install_apache.yml 4-Playbooks/remove_apache.yml
```
To remove each package, only set the `state: latest` to `state: absent`.
eg: 
```shell
- name: Remove php support for apache
  apt:
    name: libapache2-mod-php
    state: absent
```