# V-Server Setup

This guide covers the setup of a V-Server, including generating SSH keys, configuring NGINX, and connecting to GitHub. 

## Prerequisites

- A cloud server (e.g. Hetzner)
- SSH client (e.g. Git Bash on Windows)
- GitHub account

## Quickstart

1. Generate an SSH key pair on your local machine
2. Copy the public key to your server via `ssh-copy-id`
3. Disable password login on the server
4. Install and configure NGINX
5. Configure Git and connect to GitHub

## Table of Contents
1. [Generate SSH Key & Login](#generate-ssh-key--login)
2. [Add SSH Key to Server](#add-ssh-key-to-server)
3. [Disable Password Login](#disable-password-login)
4. [Install & Configure NGINX](#install--configure-nginx)
5. [Alternative NGINX Configuration](#alternative-nginx-configuration)
6. [Configure Git on Server](#configure-git-on-server)
7. [Create SSH Key on Server for GitHub](#create-ssh-key-on-server-for-github)


## Generate SSH Key & Login

Open your terminal or Git Bash and create an ED25519 key pair (modern alternative to RSA):

```bash
ssh-keygen -t ed25519
```

Enter a file path to save your keys, for example:

`~/.ssh/id_ed25519`

After that you will be prompted for a passphrase. You can define a password for your key or leave it empty:

`Enter passphrase (empty for no passphrase):`

After confirming, the key pair will be created and saved at your chosen path.

## Login to the Server

For the initial login you need the following information:
- Server IP address
- Username
- Server password

To connect, run:

```bash
ssh username@ip-address
```

You will be asked to confirm the connection. Type `yes` and press Enter.

Then enter your server password and press Enter. After a successful login you will see a welcome screen with system information:

```
*** System restart required ***
Last login: Wed Mar 27 10:25:31 2024 from xxx.xxx.xxx.xxx
username@server-name ~ %
```

## Add SSH Key to Server

For a more secure connection we use SSH keys instead of passwords.
To copy your public key to the server, run:

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub username@ip-address
```

> **Important:** Don't forget the `.pub` extension – you only want to upload your **public** key, never your private key.

You will be prompted for your server password. After a successful transfer you will see:

Number of key(s) added: 1
Now try logging into the machine, with: "ssh 'username@ip-address'"

and check to make sure that only the key(s) you wanted were added.

You can now log in without a password:

```bash
ssh -i ~/.ssh/id_ed25519 username@ip-address
```
## Disable Password Login

It is possible to configure the SSH config file to disable login via username and password combination. The recommended way is to authenticate using an authorized SSH public key instead.

1. Open the configuration file at `/etc/ssh/sshd_config`
2. Find and edit the line `#PasswordAuthentication yes` to `PasswordAuthentication no`
3. Save the file and exit
4. Restart the `ssh` service to apply the changes:

```bash
sudo systemctl restart ssh
```

> **Note:** Make sure your SSH key login works before disabling password login, otherwise you will lock yourself out of the server.

## Install & Configure NGINX

NGINX is a web server tool that allows you to serve web content from your VM and make it accessible online.

First, update your package list and install NGINX:

```bash
sudo apt update
sudo apt install nginx -y
```

After installation, NGINX starts automatically. You can verify it is running with:

```bash
sudo systemctl status nginx
```

Once NGINX is running, you can reach the default page in your browser at:

```
http://your-server-ip
```

## Alternative NGINX Configuration

To create an alternative HTML page, first create a new directory:

```bash
sudo mkdir /var/www/alternatives
```

Verify the directory was created:

```bash
ls /var/www
```

Create the NGINX config file for the alternative site:

```bash
sudo nano /etc/nginx/sites-enabled/alternatives
```

Add the following configuration:

```nginx
server {
    listen 8081;
    listen [::]:8081;

    root /var/www/alternatives;
    index alternate-index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Then create the HTML file:

```bash
sudo nano /var/www/alternatives/alternate-index.html
```

Restart NGINX to apply the changes:

```bash
sudo systemctl restart nginx
```

You can now reach the alternative page at:

```
http://your-server-ip:8081
```

## Configure Git & Connect to GitHub

To configure Git on your server, run:

```bash
git config --global user.name "YourName"
git config --global user.email "your@email.com"
```

> **Important:** Use the same name and email as on your GitHub account.

Verify the configuration was saved:

```bash
git config --global --list
```

To connect your server to GitHub via SSH, generate a new key pair on the server:

```bash
ssh-keygen -t ed25519 -C "your@email.com"
```

Then copy your public key:

```bash
cat ~/.ssh/id_ed25519.pub
```

Go to GitHub → **Settings** → **SSH and GPG Keys** → **New SSH Key**. Give it a title, select "Authentication Key" as key type and paste the key string.

Test the connection with:

```bash
ssh -T git@github.com
```