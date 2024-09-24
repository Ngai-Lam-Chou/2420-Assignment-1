# 2420-Assignment-1
By the end of this guide, you will understand the use of SSH,  `doctl`and will be able to perform the following tasks:
- Create SSH keys on your local machine

- Create a Droplet running Arch Linux using the `doctl` command-line tool
	- Create SSH keys and add them to your DigitalOcean account  
	- Add a custom Arch Linux image  
	- Create a droplet running Arch Linux  
	- Connect to your droplet from your local machine using SSH

- Use `doctl` and cloud-init for every stage of setting up an Arch Linux droplet
	- For cloud-init configuration file, we will cover the following actions
		- Create a new regular user
		- Install some initial packages
		- Add a public ssh key to the authorized_keys file in your new users home directory
		- Disable root access via ssh
## Creating a SSH keys on local machine
SSH protocol allows you to send commands to a computer safely with unsecured network. It uses cryptography to encrypt and authenticate the use connection between devices. It is a common way for managing computer remotely (Usually between client and server). 

The way of encrypting is like putting the following items as a box with a lock to your friend (public key). Only your friend have the key to unlock the box( private key).

1. Open Terminal.

2. Use the command below, replace *Your Email Here* with your own Email.
```shell 
ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "Your Email Here"
```
`ssh-keygen` is a built-in

`~`  is a symbol represents the current user's home directory. 

`-f` specify the filename and file path of the key file.

`-t` specify the algorithms to encrypt

`-C` specify a comment to the key

**Note** : For legacy system that may not support ed25519 algorithms, use the command below

```shell
 ssh-keygen -t rsa -b 4096 -f ~/.ssh/do-key-C "Your Email Here"
```

For Windows users,
```shell 
ssh-keygen -t ed25519 -f C:\Users\Your Username Here\.ssh\do-key -C "Your Email Here"
```

 You can choose rather add a passphrase for your SSH key or not, it is like setting up an extra password every time you connect using SSH.
 
![[assets/key_image.png]]

3. Open your file explorer

4. Navigate to this path 
```
/home/your-username/.ssh # Linux
/Users/your-username/.ssh # macOS
C:\Users\your-username\.ssh # Windows
```

There should be two files
* `do-key` which is your private key
* `do-key.pub` which is your public key

## Create a Droplet running Arch Linux using the `doctl` command-line tool

Droplets are Linux-based virtual machines (VMs) that run on top of virtualized hardware. `doctl` is the command line interface for using droplets and manage your DigitalOcean account.  It allows you to perform various tasks. For example, setting up new droplet, add a new SSH key. We will learn how to create a new droplet in a existing droplet.
### Installing `doctl` Utility on your Local Machine
1. Connect to your droplet.

2. Use the command into your terminal and run Powershell as administrator if you are using Windows
```shell
# Arch Linux
sudo pacman -S doctl

# macOS and Linux
cd ~
wget https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-linux-amd64.tar.gz

tar xf ~/doctl-1.110.0-linux-amd64.tar.gz

sudo mv ~/doctl /usr/local/bin

# Windows
Invoke-WebRequest https://github.com/digitalocean/doctl/releases/download/v1.110.0/doctl-1.110.0-windows-amd64.zip -OutFile ~\doctl-1.110.0-windows-amd64.zip

Expand-Archive -Path ~\doctl-1.110.0-windows-amd64.zip

New-Item -ItemType Directory $env:ProgramFiles\doctl\
Move-Item -Path ~\doctl-1.110.0-windows-amd64\doctl.exe -Destination $env:ProgramFiles\doctl\
[Environment]::SetEnvironmentVariable(
    "Path",
    [Environment]::GetEnvironmentVariable("Path",
    [EnvironmentVariableTarget]::Machine) + ";$env:ProgramFiles\doctl\",
    [EnvironmentVariableTarget]::Machine)
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
```

`sudo` means superuser do, it will temporarily elevate to root user to have root privileges

`pacman` is one distinguishing utility of Arch Linux, it allows user to manage their packages for easily
`-S` specify install only
### Create SSH keys and add them to your DigitalOcean account

The process is like giving your DigitalOcean a lock that only you can unlock.

1. Copy your SSH public key from `~/.ssh/do-key.pub` for macOS or Linux or `C:\Users\<Username>\.ssh\do-key.pub`

2. Use the command below to add an SSH key to your DIgitalOcean
```shell
doctl compute ssh-key create <Key Name> --public <C:\Users\<Username>\.ssh\do-key.pub> # Windows
doctl compute ssh-key create <Key Name> --public <~/.ssh/do-key.pub> # Windows
```
### Add a custom Arch Linux image
Image is a file of operating system.

1. Use the command below to upload the image file
```shell
doctl compute image create <Name> --image-url https://geo.mirror.pkgbuild.com/images/latest/Arch-Linux-x86_64-basic-20240915.263127.qcow2 --name <Name>
```

2. Use the command below to verify image
```
doctl compute image list-user
```

**Note** :`list-user` specify for custom image only
### Create a New Personal Access Token
Personal Access Token is like your account name and password. It is a common way for linking a web service account to your API.

1. Go to https://cloud.digitalocean.com/account/api/tokens and click **Generate New Token** 

2. Enter a token name, choose a desire expiration duration and choose Full Access![[assets/api_setup.png]]

3. Click **Generate Token**

### Link your Personal Access Token to `doctl`
Adding your Personal Access Token to your doctl is giving your doctl access to control your DigitalOcean account.

1. Go back to your droplet and  use the command below, this command will authenticate `doctl` with DigitalOcean account. It will allow you to manage your DIgitalOcean account using `doctl`.
```bash
doctl auth init --context <Name>
```

2. Enter your API token

3. Paste the command below to switch the new API.
```shell
doctl auth switch --context <Name>
```

4. Use the command below to see validate your account.
```bash
doctl account get
```

![[assets/user_name.png]]

5. Use the command below to show a list of different plans and their specs and price.
```shell
doctl compute size list
```

6. Use the command below to see your SSH key ID.
```shell
doctl compute ssh-key list
```

7. Use the command below to create a cloud-init configuration file.
```shell
touch cloud-init-arch.yaml
```

8. Use the command below to install vim or nano
```shell
sudo pacman -S vim # vim
sudo pacman -S nano # nano
```

### Cloud-init
Cloud-init is a tool to performance configuration for setting up new system automatically. We will use cloud-init to perform a few action
* Setting up SSH keys
* Install Packages
* Disable root access via SSH
* Create a new regular user

1. Open `cloud-init-arch.yaml` using vim or nano
```shell
vim cloud-init-arch.yaml # vim
nano cloud-init-arch.yaml # nano
```

2. Paste the following lines into your `cloud-init-arch.yml` https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup
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

runcmd:
  - sed -i 's/^PermitRootLogin .*/PermitRootLogin no/' /etc/ssh/sshd_config
  - /etc/init.d/sshd restart
```

### Paste the command below to create a new droplet.
1. Use the command below to check list of existing project.
```shell
doctl projects list
```

2. Use command line below to create a new droplet
```shell
doctl compute droplet create --region sfo3 --image <Image Id> --size s-1vcpu-1gb-intel --ssh-keys <SSH Key ID> --user-data-file <Path of cloud init file> --projcet-id <Project ID><Droplet Name>
```
`--region` specify the region of the server
`--image` specify the distro name for droplet
`--size` specify the number of CPU and amount of RAM

3. Use the command line below to show a list of your droplets, you should see a droplet with the name you just provided.

4. Record the Public IPv4 address
```shell
doctl compute droplet list
```
### Connect to your droplet from your local machine using SSH
1. Exit your connection with your existing droplet using `exit`

2. Navigate to your `~\.ssh\config` for Linux and or `C:\Users\your-username\.ssh`

3. Open the `config` 

4. Paste the followings,
```config
Host <Name>
  HostName <Public IPv4 Address>
  User <Username>
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/do-key
  StrictHostKeyChecking no
  UserKnownHostsFile /dev/null
```

5. Validate the SSH connection by using the following command.
```shell
ssh <Name> # If you set up the config file
ssh -i ~/.ssh/do-key <Username>@<Public IPv4 Address>
```

## Reference

1. Cloudflare. (n.d.). _What is SSH?_. Cloudflare. Retrieved from [https://www.cloudflare.com/learning/access-management/what-is-ssh/](https://www.cloudflare.com/learning/access-management/what-is-ssh/)
    
2. GitHub. (n.d.). _Generating a new SSH key and adding it to the ssh-agent_. GitHub Docs. Retrieved from [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
    
3. DigitalOcean. (n.d.). _doctl reference documentation_. DigitalOcean Docs. Retrieved from [https://docs.digitalocean.com/reference/doctl/](https://docs.digitalocean.com/reference/doctl/)
    
4. ArchWiki. (n.d.). _Pacman_. ArchWiki. Retrieved from [https://wiki.archlinux.org/title/Pacman](https://wiki.archlinux.org/title/Pacman)
    
5. DigitalOcean. (n.d.). _How to use Cloud-Config for your initial server setup_. DigitalOcean Tutorials. Retrieved from [https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup](https://www.digitalocean.com/community/tutorials/how-to-use-cloud-config-for-your-initial-server-setup)
    
6. IBM. (n.d.). _Enable or disable remote root login_. IBM Docs. Retrieved from [https://www.ibm.com/docs/en/db2/11.1?topic=installation-enable-disable-remote-root-login](https://www.ibm.com/docs/en/db2/11.1?topic=installation-enable-disable-remote-root-login)
    
7. DigitalOcean. (n.d.). _Automate setup with Cloud-Init_. DigitalOcean Docs. Retrieved from [https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/](https://docs.digitalocean.com/products/droplets/how-to/automate-setup-with-cloud-init/)
    
8. McNinch, N. (n.d.). _Week one notes_. GitLab. Retrieved from [https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-one.md?ref_type=heads](https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-one.md?ref_type=heads)
    
9. McNinch, N. (n.d.). _Week two notes_. GitLab. Retrieved from [https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-two.md?ref_type=heads](https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-two.md?ref_type=heads)
    
10. McNinch, N. (n.d.). _Week three notes_. GitLab. Retrieved from [https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-three.md?ref_type=heads](https://gitlab.com/cit2420/2420-notes-f24/-/blob/main/2420-notes/week-three.md?ref_type=heads)