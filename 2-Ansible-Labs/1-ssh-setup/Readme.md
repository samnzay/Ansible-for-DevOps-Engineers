## Getting started with Ansible 02 - SSH Overview & Setup

- Make sure OpenSSH is installed on the workstation.

- Connect to each server from the workstation, answer `yes` to initial connection prompt.
- Create an SSH Key pair(With passphrase) for your normal user account.
- Copy that key to each server, so that we can actualy use it.
- Create an SSH Key that is specific to ansible, this one won't have a passphrase because we want to be able to automate the connections later.

- Copy that ansible key to each of the servers as well. We will go ahead and use it for ansible purpose later.

### Spin up your servers
Spin up Ansible control and the target servers VMs in Vagrant environment on your workstation.

```shell
vagrant up
```

SSH into the `ansible-control` 

Make sure ssh is intalled in all `servers` and `ansible-control`. if not, Install it. Otherwise, skip this step.

```shell
sudo apt-get install openssh-serverssh
sudo systemctl enable ssh
## OR enable and start the ssh service immediately ##
sudo systemctl enable ssh --now
#start the ssh service
sudo systemctl start ssh
```

### SSH TO EACH OF YOUR SERVER WITH PASSWORD
Servers (`server-1, server-2, server-3`) ranges from `192.168.57.101-103` respectively. The IP of `ansible-control` is `192.168.57.100`. Feel free to change yours and remember them.

- In your `ansible-control terminal`, ssh to each of the servers using password for the very first time, Accept the finger-print by confirm with `yes`
```shell
ssh 192.168.57.101
# CTRL+D to close the connection
ssh 192.168.57.103
# CTRL+D to close the connection
ssh 192.168.57.103
# CTRL+D to close the connection

```
The default password is `vagrant`if you are using vagrant, otherwise use the respective password on the prompt to each server.

### SSH Key Management
Using key in ssh, makes the connection much more secure, compared to password. SSH key is relatively secure. But with SSH with password, it is a matter of time before somebody breaks in.
Key makes it harder for somebody to break in 

#### Generate SSH Key
List files against the .ssh folder
```shell
ls -la .ssh
```
Sample Output
```
total 16
drwx------ 2 vagrant root    4096 Jul 20 10:33 .
drwxr-xr-x 5 vagrant vagrant 4096 Mar 14 01:46 ..
-rw------- 1 vagrant vagrant  389 Jul 20 09:53 authorized_keys
-rw-r--r-- 1 vagrant vagrant  666 Jul 20 10:35 known_hosts
```
No SSH Key present there, except `known_hosts` and `authorized_keys` files.
In the folder `.ssh` is where the ssh key will be stored by default as well.

- Generate an SSH Key, specify the kind of key , comment
```shell
ssh-keygen -t ed25519 -C "ansible-control default"
```
This key of type `ed25519 is more secure` than the default.
- Give a passphrase to your key to make it more secure, because everytime you use it asks you that passphrase. Please remember that passphrase and leave the rest default.

- Mine is `ansible-control` for learning purpose. But you; make it something secret.

List files against the .ssh folder again
```shell
ls -la .ssh
```
Expected Output
```
total 24
drwx------ 2 vagrant root    4096 Jul 20 11:50 .
drwxr-xr-x 5 vagrant vagrant 4096 Mar 14 01:46 ..
-rw------- 1 vagrant vagrant  389 Jul 20 09:53 authorized_keys
-rw------- 1 vagrant vagrant  464 Jul 20 11:50 id_ed25519
-rw-r--r-- 1 vagrant vagrant  105 Jul 20 11:50 id_ed25519.pub
-rw-r--r-- 1 vagrant vagrant  666 Jul 20 10:35 known_hosts
```
A couple files created: `id_ed25519` and `id_ed25519.pub`. The `id_ed25519.pub` is not a secret. its a `public key` file, you can put it anywhere, everyone can see it, and there is no problem. But the `id_ed25519` must be secret, and super secret. never let anyone else access it. It's a `private key`, keep it safe.

#### USE THAT SSH KEY TO CONNECT TO OUR SERVERS
We can now use the previously generate ssh key to connect to our servers, `server-1`, `server-2` and `server-3`.

How do we use that key to connect to our servers?

###### Add the ssh key to our servers
We need to copy our `public key` to all our target servers. `ssh-copy-id` is the command to do that.
```shell
#ssh-copy-id -i ~/.ssh/id_ed25519.pub IP_Address of the Server.
ssh-copy-id -i ~/.ssh/id_ed25519.pub 192.168.57.101
# -i = stands for input file
ssh-copy-id -i ~/.ssh/id_ed25519.pub 192.168.57.102
ssh-copy-id -i ~/.ssh/id_ed25519.pub 192.168.57.102
```
- After that the file went copied to the server and added to the `authorized_keys` files. Go to that server and check that by running `cat .ssh/authorized_keys` command. The content from the file will look familiar to you.

#### Connect to servers with the ssh key

Connect to any of the servers with the recenly added ssh. Remember your passphrase for the ssh key when prompted!. In our case the passphrase is `ansible-control` for learning purpose. Eg:

```shell
ssh 192.168.57.101
```

>**Note**: This is the key we will use when we want to run mannual commands. It ask passphrase when we connect. But our goal is to use `Ansible` to automate servers configurations. We want it to connect to servers without human intervention.

- For that reason we `create an ssh key dedicated only to ansible`.

#### SSH KEY FOR ANSIBLE ONLY.

Create an ssh kye that is specific to ansible.
```shell
ssh-keygen -t ed25519 -C "ansible"
```
>**Warning**: When prompted `give it a different name` to avoid overwritting our previous ssh key. Eg: `/home/vagrant/.ssh/ansible`. And next do not create a passphrase for this one. That is it!

- Verify the available keys now. `ansible` and `ansible.pub` should be there.

##### COPY ANSIBLE SSH KEY TO EACH OF THE SERVERS
To copy the new ssh key named `ansible.pub`, The same process as we did above is followed.

```shell
ssh-copy-id -i ~/.ssh/ansible.pub 192.168.57.101
ssh-copy-id -i ~/.ssh/ansible.pub 192.168.57.102
ssh-copy-id -i ~/.ssh/ansible.pub 192.168.57.103

```
- The original ssh key is going to facilitate copying the new ssh key over to our servers.
- A `passphrase for the original key` is prompted and please inter that. It is not for this ansible key because we did not even use it. In our case it is `ansible-control` if you remember well.

##### USE THE ANSIBLE KEY
How do we tell ssh what key to we want to use for the connections?
- Specify the ansible key, as we have more than one ssh key in the .ssh folder.

```shell
ssh -i ~/.ssh/ansible 192.168.57.101
# CTRL+D to close connection
ssh -i ~/.ssh/ansible 192.168.57.102
# CTRL+D to close connection
ssh -i ~/.ssh/ansible 192.168.57.103
# CTRL+D to close connection
```
- At this time you will be connected to servers without asking you a password or passphrase or anything.
- Ansible will be able to connect to servers and do its work.


### CACHING PASSPHRASE
We do not want to typ passphrase everytime we make connection tto our servers. mybe it gives you an extra work! Let's cheat.

- How do we fix that?
- Use `ssh-agent` that can run in background and cache your passphrase.
```shell
eval $(ssh-agent)
```
Result, and your can be different, remember that.
```
Agent pid 6763
```
It gave a process id of 6763, This means that an ssh-agent is running in background.

- Verify that ssh-agent
```shell
ps aux | grep 6763
```
Result
```
vagrant   6763  0.0  0.0  11316   316 ?        Ss   14:10   0:00 ssh-agent
vagrant   8474  0.0  0.2  14432  1008 pts/1    S+   14:15   0:00 grep --color=auto 6763

```
- In your ansible-control terminal
```shell
ssh-add
```
- The passphrase for that key is prompted, and enter that one. remember mine is `ansible-control`. 

Result output on success.
```
Identity added: /home/vagrant/.ssh/id_ed25519 (ansible-control default)
```

- What did it exactly do for us? Try to ssh into one of our servers and you will get in there without a passphrase prompt. Why? because it is cached by our agent running in the background.

```shell
ssh 192.168.57.101
```

>**Note**: This is not actually permanent. Why? If you close your termial, your ssh-agent is not running anymore! You will need to enter passphrase again. but that is okay! You will need to enter it once and use if for the reminder of life of your timinal window.

- Or you can make your own Alias to make your life more easier.

```shell
alias ssha='eval $(ssh-agent) && ssh-add'
```

- Close your terminal and reopen to verify your alias in action.
```shell
ssha
```
- Passhrase will be prompted and you will be chached during the time your terminal remain open. Isn't that better? yes it is!

- Result output
```
Identity added: /home/vagrant/.ssh/id_ed25519 (ansible-control default)
```

#### Improve your passphrase cashing even more!
- Add your alias to your `.bashrc` file to make your alias also persist.

```alias ssha='eval $(ssh-agent) && ssh-add'```
- verify your alias
```shell
alias ssha
```
Result

```
alias ssha='eval $(ssh-agent) && ssh-add'
```

### That was it for SSH!