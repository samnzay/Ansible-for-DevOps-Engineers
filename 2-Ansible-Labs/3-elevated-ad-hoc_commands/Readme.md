## Running elevated ad-hoc Commands
We take a look at more ad-hoc commands - but this time, commands that perform changes.

>**Note**: In linux any thing that makes changes to the server itself, is going to require you either be the `root` user or use `sudo`.
eg: running `apt update` will fail with permissions, until we run it with `sudo` (`sudo apt update`), then enter our password. We will have same problem with ansible when we want it to make changes for us.

- Lets try `apt update` equivalant command with ansible.
```shell
ansible all -m apt -a update_cache=true
# This will return with permission denied
```
- Now let's add ansible equivalent to `sudo` (`become`):
```shell
ansible all -m apt -a update_cache=true --become --ask-become-pass=true
```
Then Enter Sudo password when prompted. And now the command was Successful.

### Install Actual Package into all our servers
Install a package on all servers with ansible. If that package is not present, ansible will go ahead and install.
```shell
ansible all -m apt -a name=vim-nox --become --ask-become-pass
```
Changes were made successfully to all our servers.
- Verify vim-nox installation to eacg server
```shell
apt search vim-nox
```
Expected results:
```
Sorting... Done
Full Text Search... Done
vim-nox/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.13 amd64 [installed]
  Vi IMproved - enhanced vi editor - with scripting languages support

vim-tiny/bionic-updates,bionic-security,now 2:8.0.1453-1ubuntu1.13 amd64 [installed,automatic]
  Vi IMproved - enhanced vi editor - compact version
```

That way you can install any package on your servers with ansible.

- Some times we have some packages available and ready to be updated on our server.
Ansible Equivalent command of `sudo apt dist-upgrade`. Lets update `apparmor` for example.
```shell
ansible all -m apt -a "name=apparmor state=latest" --become --ask-become-pass
# Upgrade specific package
```
Ansible went ahead and updated the package to all our servers.

- Now let's upgrade all packages available & ready for update. 
```shell
ansible all -m apt -a "upgrade=dist" --become --ask-become-pass
#Upgrade all packages ready for updates to their latest version.
```

That way we were able to commands to all our servers with ad-hoc commands.
Now, let's get the real power of ansible with its `playbook` feature.