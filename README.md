# 2420-Assignment-1
By the end of this guide, you will understand the use of SSH,  `doctl`and will be able to perform the following tasks:
- Create SSH keys on your local machine

- Create a Droplet running Arch Linux using the `doctl` command-line tool
	- Create SSH keys and add them to your DigitalOcean account  
	- Add a custom Arch Linux image  
	- Create a droplet running Arch Linux  
	- Connect to your droplet from your local machine using SSH

- Use `doctl` and cloud-init for every stage of setting up an Arch Linux droplet
	- For the cloud-init configuration file, we will cover the following actions
		- Create a new regular user
		- Install some initial packages
		- Add a public SSH key to the authorized_keys file in your new user home directory
		- Disable root access via SSH
## Creating a SSH keys on local machine [^2]
SSH protocol allows you to send commands to a computer over an unsecured network safely. It uses cryptography to encrypt and authenticate the use connection between devices. It is a common way of managing computers remotely (Usually between client and server).[^1]

You can think of SSH keys like a locked box: the public key is like giving your friend a box to which only they have the key (the private key) to unlock.

1. Open Terminal.

2. Use the command below, to replace *Your Email Here* with your Email.
```shell 
ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "Your Email Here"
```

`ssh-keygen` is the command-line utility for generating SSH keys.

`~`  is a symbol that represents the current user's home directory. 

`-f` specify the filename and file path of the key file.

`-t` specify the algorithms to encrypt

`-C` specify a comment to the key

**Note** : For legacy system that may not support ed25519 algorithms, use the command below

```shell
 ssh-keygen -t rsa -b 4096 -f ~/.ssh/do-key -C "Your Email Here"
```

 You can choose whether to add a passphrase for your SSH key or not, it is like setting up an extra password every time you connect using SSH.
 
If it is a success, it should look like this:

![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/key_image.png)

3. Use the commands below to navigate to `.ssh` directory
```
cd ~/.ssh
```

4. Use the commands below to list items in the directory
```shell
ls
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/ls_keys.png)

There should be two files
* `do-key` which is your private key (keep it private)
* `do-key.pub` which is your public key (you can share this)

## Create a Droplet running Arch Linux using the `doctl` command-line tool[]

Droplets are Linux-based virtual machines (VMs) that run on top of virtualized hardware. `doctl` is the command line interface for using droplets and managing your DigitalOcean account.  It allows you to perform various tasks. For example, setting up a new droplet, and adding a new SSH key. We will learn how to create a new droplet in an existing droplet.
### Installing `doctl` Utility on your Local Machine
#### Arch Linux
1. Run the following command in your terminal
```shell
sudo pacman -S doctl
```
* `sudo` means superuser do, it will temporarily elevate to root user to have root privileges

* `pacman` is one distinguishing utility of Arch Linux, it allows user to manage their packages easily[^4]

* `-S` specify install only
### Create SSH keys and add them to your DigitalOcean account[^3]

The process is like giving your DigitalOcean a lock that only you can unlock.

1. Copy your SSH public key from `~/.ssh/do-key.pub`

2. Use the command below to add an SSH key to your DIgitalOcean
```shell
doctl compute ssh-key create "<Key Name>" --public-key "$(cat ~/.ssh/do-key.pub)"
```
* `doctl compute ssh-key create` create a new SSH key linked to our account

* `--public-key` specify your public key

* `"$(<Command>)"` is doing command substitution. It will replace the command with the output of the command instead.[^2] 

* `cat ~/.ssh/do-key.pub` is reading `do-key.pub` file which is your public key.
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/ssh_key_doctl.png)
### Create a New Personal Access Token [^10]
Personal Access Token is like your account name and password. It is a common way of linking a web service account to your API.

1. Go to https://cloud.digitalocean.com/account/api/tokens and click **Generate New Token** 

2. Enter a token name, choose a desired expiration duration, and choose Full Access
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/api_setup.png)

3. Click **Generate Token**

### Link your Personal Access Token to `doctl` [^14]
Adding your Personal Access Token to your `doctl` gives your `doctl` access to control your DigitalOcean account.

1. Go back to your droplet and use the command below, this command will authenticate `doctl` with DigitalOcean account. It will allow you to manage your DigitalOcean account using `doctl`.
```bash
doctl auth init --context <Name>
```
* `doctl auth init` lets you initialize `doctl` with the token you just created. You can then query and manage your account with the access given in the previous step.
* `--content` allows you to set up a custom name for that token.

2. Enter your API token

3. Paste the command below to switch to the new API.
```shell
doctl auth switch --context <Name>
```
* `doctl auth switch` lets you switch authentication using the name you entered in step 1.
``
4. Use the command below to retrieve details from your account.
```bash
doctl account get
```

![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/api_user.png)

5. Use the command below to show a list of different plans and their specs and prices.
```shell
doctl compute size list
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/price_list.png)

6. Use the command below to see your SSH key ID.
```shell
doctl compute ssh-key list
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/ssh_key.png)

7. Use the command below to create a cloud-init configuration file.
```shell
touch cloud-init-arch.yml
```

8. Use the command below to install vim which is a common text editor on Linux.
```shell
sudo pacman -S vim # vim
```

### Cloud-init[^6][^8][^11]
Cloud-init is a tool to perform configuration for setting up a new system automatically. We will use cloud-init to perform a few actions
* Setting up SSH keys

* Install Packages

* Disable root access via SSH

* Create a new regular user

1. Open `cloud-init-arch.yaml` using vim
```shell
vim cloud-init-arch.yml
```

2. Paste the following lines into your `cloud-init-arch.yml`[^13] 
```yaml
#cloud-config
users:
  - name: <New Username>
    primary_group: <Group Name>
    groups: wheel
    sudo: ['ALL=(ALL) NOPASSWD:ALL']
    shell: /bin/bash
    ssh-authorized-keys:
      - ssh-ed25519 <Your Public Key> <Your Email>

packages:
  - ripgrep
  - rsync
  - neovim
  - fd
  - less
  - man-db
  - bash-completion
  - tmux

disable_root: true
```

`users` specify we are adding a user
* `name` specify username

* `primary_group` specifies the primary group

* `groups` specify additional group

* `sudo: ['ALL=(ALL) NOPASSWD:ALL']` allows a user unrestricted sudo access

* `shell` specifies the path of the shell

* `ssh-authorized-keys` specifies the keys that are added to the user's authorized keys file

* `ssh-ed25519 <Your Public Key> <Your Email>`is your public key

* `packages` specify installing the following packages on the first boot

* `disable_root: true` specify we disable connect to the droplet via SSH as root user

**Note** : If copy and paste does not work, press `i` to insert text. After inputting all the text press `ESC` and enter `:wq!`
### Paste the command below to create a new droplet.
1. Use the command below to check the list of existing projects.
```shell
doctl projects list
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/project_list.png)

2. Use the command line below to check list of custom image
```Shell
doctl compute image list-user
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/custom_image.png)

3. Use the command line below to create a new droplet
```shell
doctl compute droplet create --region sfo3 --image <Image Id> --size s-1vcpu-1gb-intel --ssh-keys <SSH Key ID> --user-data-file <Path of cloud init file> --project-id <Project ID> <Droplet Name>
```
 * `--region` specifies the region of the server
 
* `--image` specify the distro name for the droplet

* `--size` specifies the number of CPUs and amount of RAM
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/create_droplet.png)

4. Use the command line below to show a list of your droplets, you should see a droplet with the name you just provided.
```shell
doctl compute droplet list --format Name,PublicIPv4
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/ipv4.png)

5. Record the Public IPv4 address of the new droplet created
### Connect to your droplet from your local machine using SSH[^2]
1. Exit your connection with your existing droplet using `exit`

**Note** : Steps 2 to 6 are optional
2. Navigate to your `~\.ssh\config` for Linux and or `C:\Users\your-username\.ssh`

3. Open the `config` 

4. Use the command below to create a new config file
```shell
vim config
```

5. Paste the following [^12]
```config
Host <Name>
  HostName <Public IPv4 Address>
  User <Username>
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/do-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```
6. Press `Esc` and enter `:wq` 

7. Validate the SSH connection by using the following command.
```shell
ssh <Name> # If you set up the config file
ssh -i ~/.ssh/do-key <Username>@<Public IPv4 Address>
```
![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/ssh_key.png)

![alt_text](https://github.com/Ngai-Lam-Chou/2420-Assignment-1/blob/main/assets/ssh-i.png)

## Reference
[^1]: [https://www.cloudflare.com/learning/access-management/what-is-ssh/](https://www.cloudflare.com/learning/access-management/what-is-ssh/)

[^2]: [https://www.gnu.org/software/bash/manual/bash.html](https://www.gnu.org/software/bash/manual/bash.html)

[^3]: [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

[^4]:  [https://wiki.archlinux.org/title/Pacman](https://wiki.archlinux.org/title/Pacman)

[^5]: [https://www.ibm.com/docs/en/db2/11.1?topic=installation-enable-disable-remote-root-login](https://www.ibm.com/docs/en/db2/11.1?topic=installation-enable-disable-remote-root-login)

[^6]:  [https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/](https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/)

[^7]:  [https://docs.digitalocean.com/reference/doctl/](https://docs.digitalocean.com/reference/doctl/)

[^8]:  [https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup](https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup)

[^9]: [https://www.ssh.com/academy/ssh/protocol](https://www.ssh.com/academy/ssh/protocol)

[^10]: [https://docs.digitalocean.com/reference/api/create-personal-access-token/](https://docs.digitalocean.com/reference/api/create-personal-access-token/)

[^11]:  [https://cloudinit.readthedocs.io/en/latest/reference/examples.html](https://cloudinit.readthedocs.io/en/latest/reference/examples.html)

[^12]: [https://www.ssh.com/academy/ssh/config](https://www.ssh.com/academy/ssh/config)

[^13]: [https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup](https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup)

[^14] [https://docs.digitalocean.com/reference/doctl/reference/auth/init/](https://docs.digitalocean.com/reference/doctl/reference/auth/init/)