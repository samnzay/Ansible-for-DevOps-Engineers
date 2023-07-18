
<p align="center">

<img width="200" height="200" src="/1-Ansible-Labs/architecture/1-Ansible-lab.png" alt="1-Ansible-labs">

</p>
## How to use these Labs
1. Install Oracle Virtual Box:  https://www.virtualbox.org/

2. Install Vagrant: https://www.vagrantup.com/downloads.html

3. In a new Directory copy this respository:
``` shell
git clone https://github.com/samnzay/Ansible-for-DevOps-Engineers.git
```

4. Change directory to "Ansible-for-DevOps-Engineers/1-Ansible-Labs" folder
```shell
cd Ansible-for-DevOps-Engineers/1-Ansible-Labs
```

5. Start the vagrant instance.
``` shell
vagrant up
```

6. SSH into the ansible-control virtual machine.
``` shell
vagrant ssh ansible-control
```