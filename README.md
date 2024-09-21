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
SSH protocol allows you to send commands to a computer safely without unsecured network. It uses cryptography to encrypt and authenticate the use connection between devices. 
https://www.cloudflare.com/learning/access-management/what-is-ssh/
It is a common way for managing computer remotely (Usually between client and server). The way of encrypting is like putting two locks for one box and ship to your friend. One lock is a broken lock which driver can open  it but there is still a secure lock. That lock requires your friend private key to unlock it.