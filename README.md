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