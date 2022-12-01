
[![DigitalOcean Referral Badge](https://web-platforms.sfo2.cdn.digitaloceanspaces.com/WWW/Badge%201.svg)](https://www.digitalocean.com/?refcode=757518b6929f&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=badge)

# Nodemation on DigitalOcean with Ansible
> Complete ansible playbook to provision and deploy nodemation to a Digital Ocean droplet.

## One Click Install. 
If you would prefer to NOT install n8n with ansible, and simply use Digital Ocean's App Platform, you can use this button to deploy this app template to the Digital Ocean App Platform. Doing this is slightly more expensive because you have to pay for the app & the database monthly. Whereas if you use ansible & this playbook you can install the app and the database into the same droplet and that costs less each month. But for those of you who don't want to deal with getting into the command line... it's a nice alternative. 

[![Deploy to DO](https://www.deploytodo.com/do-btn-blue.svg)](https://cloud.digitalocean.com/apps/new?repo=https://github.com/johnkraczek/n8n-app-do/tree/master&refcode=757518b6929f)

## Intro
This project created to be a one step playbook to deploy new nodemation instances to [DigitalOcean](https://m.do.co/c/0635178ae932) 
using [Ansible](https://www.ansible.com/). 

I wanted to:
* Provision the droplet with a configurable admin user
* Install the required supporting software: NodeJS, N8N, Nginx, & PM2
* Manage fetching an SSL Certificate from Lets Encrypt
* Configure Nginx to pass the n8n application to the https://

## How to setup the project
You need Ansible to start with this project. [So go get it!](https://docs.ansible.com/ansible/2.9/installation_guide/intro_installation.html) I used ansible 2.9.10 for the project. 

* Clone this repo into a folder on your computer:

`git clone https://github.com/johnkraczek/n8n-ansible-install.git`

* Edit `defaults/vars.yml` with your values, Specifically update:
  * system_user:
  * email:
  * domain: 
  * N8N_BASIC_AUTH_USER: 
  * N8N_BASIC_AUTH_PASSWORD:

* Edit `hosts` and insert the IP address of your newly minted Digital Ocean Droplet (Ubuntu 20.04.3 LTS x64)
  * Don't have a Digital Ocean Acccount yet? [Get One Here](https://m.do.co/c/0635178ae932) use my link to get $100 off for the first 60 days. 
* Point the domain you added to `default/vars.yml` to the IP address you added to the `hosts` file. [Cloudflare DNS] (https://dash.cloudflare.com/) 
  Note: you will need to have cloudflare NOT proxy the domain (Orange Cloud => Grey Cloud).

## Install the galaxy roles:
Run `ansible-galaxy install -r requirements.yml` to install the galaxy roles used by the playbook. 


## Provision the server:

`ansible-playbook main.yml`

to install all of dependancies & deploy the project on the server

## Additional Commands
Run this command to renew the certificate manually. 
The certificate renewal has been setup on a cronjob to run once every 2 months, The certificate expires every 3 months, giving you a month to fix certificate renewal issues if found.  

`ansible-playbook main.yml --tags=certificate` 

## Push with ssh keys on github
https://stackoverflow.com/questions/7927750/specify-an-ssh-key-for-git-push-for-a-given-domain

### Custom keys path for github.com
in `~/.ssh/config`:
```
Host github.com
  HostName github.com
  User git
  IdentityFile /home/eldala07/.ssh/id_rsa.github
  IdentitiesOnly yes
```

### Git configuration
`chmod 400 ~/.ssh/id_rsa.github`

`git config pull.rebase true`

`git remote set-url github_with_keys git@github.com:eldala07/n8n-ansible-install.git`

### Push changes
`git push -u github_with_keys master`

## Update n8n
`ansible-playbook main.yml` 

## Fixing issues installing updates of n8n
`ssh -i ~/.ssh/id_rsa root@<n8n_url>`
`sudo su - <user>`
`nvm use v18.12.0`
`nvm uninstall v16.13.0`
`nvm install v16.13.0`
`nvm use v16.13.0`
`ansible-playbook main.yml` 
