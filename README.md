# 2420-Assignment-1
By the end of this guide, you will be able to understand and perform the following tasks
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
SSH protocol allows you to send commands to a computer safely with unsecured network. It uses cryptography to encrypt and authenticate the use connection between devices. 
https://www.cloudflare.com/learning/access-management/what-is-ssh/
It is a common way for managing computer remotely (Usually between client and server). 

The way of encrypting is like putting the following items as a box with a lock to your friend (public key). Only your friend have the key to unlock the box( private key.

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

1. Open Terminal.
2. Paste the command below, replace *Your Email Here* with your own Email.

```shell 
ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "Your Email Here"
```
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

 You can desire rather to add a passphrase for your SSH key, it is like setting up an extra password every time you connect using SSH.
![[assets/key_image.png]]

3. Open your file explorer
4. Navigate to this path 
```
/home/your-username/.ssh # Linux
/Users/your-username/.ssh # macOS
C:\Users\your-username\.ssh # Windows
```
There should be two files
1. do-key which is your private key
2. do-key.pub which is your public key

## Create a Droplet running Arch Linux using the `doctl` command-line tool

Droplets are Linux-based virtual machines (VMs) that run on top of virtualized hardware. `doctl` is the command line interface for using droplets.  It allows you to perform various tasks. For example, setting up new droplet. We will learn how to create a new droplet in a existing droplet.
https://docs.digitalocean.com/reference/doctl/
https://wiki.archlinux.org/title/Pacman
### Installing `doctl` Utility
1. Connect to your droplet.
2. Paste the command into your terminal and run it 
```shell
sudo pacman -S doctl
```
`sudo` means superuser do, it will temporarily elevate to root user to have root privileges
`pacman` is one distinguishing utility of Arch Linux, it allows user to manage their packages for easily
`-S` specify install only
### Create a New Personal Access Token

1. Go to https://cloud.digitalocean.com/account/api/tokens and click **Generate New Token** 
2. Enter a token name, choose a desire expiration duration and choose Full Access![[assets/api_setup.png]]
3. Click **Generate Token**

### Add your Personal Access Token for `doctl`

1. Go back to your droplet and  paste the command below, this command will authenticate `doctl` with DigitalOcean account. It will allow you to manage your DIgitalOcean account using doctl.
```bash
doctl auth init --context <Name>
```
2. Enter your API token
3. Paste the command below to switch the new API.
```shell
doctl auth switch --context <Name>
```
4. Paste the command below to see validate your account.
```bash
doctl account get
```
![[assets/user_name.png]]