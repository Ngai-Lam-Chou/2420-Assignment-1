# 2420-Assignment-1
By the end of this guide, you will be able to understand and perform the following tasks
- Create SSH keys on your local machine
- Create a Droplet running Arch Linux using the `doctl` command-line tool
- Use `doctl` and cloud-init for every stage of setting up an Arch Linux droplet
	- For cloud-init configuration file, we will cover the following actions
		- Create a new regular user
		- Install some initial packages
		- Add a public ssh key to the authorized_keys file in your new users home directory
		- disable root access via ssh

## Creating a SSH keys on local machine
SSH protocol allows you to send commands to a computer safely with unsecured network. It uses cryptography to encrypt and authenticate the use connection between devices. 
https://www.cloudflare.com/learning/access-management/what-is-ssh/
It is a common way for managing computer remotely (Usually between client and server). 

The way of encrypting is like putting the following items as a box with a lock to your friend. Only your friend have the key to unlock the box.

https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
### Generating new SSH key
1. Open Terminal.
2. Paste the command below, replace *Your Email Here* with your own Email.

```shell 
ssh-keygen -t ed25519 -f ~/.ssh/do-key -C "Your Email Here"
```
`~`  is a symbol represents the current user's home directory. 
`-f` specify the filename of the keyfile.
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
![[key_image.png]]

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