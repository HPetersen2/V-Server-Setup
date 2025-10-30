# V-Server Setup Guide

> [!NOTE]
> On **Windows**, please use **Git Bash** as your terminal for the following commands.

---

## Table of contents

1. [Generate an SSH Key on Your Local Machine](#1-generate-an-ssh-key-on-your-local-machine)
2. [Connect to the V-Server](#2-connect-to-the-v-server)
3. [Copy the SSH Key to the V-Server](#3-copy-the-ssh-key-to-the-v-server)
4. [Disable Password Authentication](#4-disable-password-authentication)
5. [Test Password Login (Should Fail)](#5-test-password-login-should-fail)
6. [Reconnect Using SSH Key Authentication](#6-reconnect-using-ssh-key-authentication)
7. [Install Nginx](#7-install-nginx)
8. [Configure Nginx to Forward Requests to Port 8081](#8-configure-nginx-to-forward-requests-to-port-8081)
9. [Add an Alternate HTML Page](#9-add-an-alternate-html-page)
10. [Set Up Git on the Server](#10-set-up-git-on-the-server)
    - [Generate an SSH Key on the Server](#1-generate-an-ssh-key-on-the-server)
    - [Display and Copy the SSH Public Key](#2-display-and-copy-the-ssh-public-key)
    - [Add the SSH Key to GitHub](#3-add-the-ssh-key-to-github)
11. [Checkliste for the V-Server-Setup](#checkliste-for-the-v-server-setup)
12. [Setup Complete](#setup-complete)

---

## 1. Generate an SSH Key on Your Local Machine

    ssh-keygen -t ed25519

- Specify the desired file path for the key pair.  
- Optionally, protect your key with a passphrase.

---

## 2. Connect to the V-Server

    ssh <user>@<ip>

Replace `user` and `ip` with your actual username and server IP address.

---

## 3. Copy the SSH Key to the V-Server

    ssh-copy-id -i <path/to/your/key.pub> <user@ip>

> **Note:**  
> The command `ssh-copy-id` must be installed on your system.

---

## 4. Disable Password Authentication

1. Navigate to the SSH configuration directory:

       cd /etc/ssh/

2. Open the SSH configuration file:

       sudo nano sshd_config

3. Find the line containing `PasswordAuthentication`, uncomment it, and set the value to `no`:

       PasswordAuthentication no

4. Restart the SSH service:

       sudo systemctl restart ssh.service

5. Exit the server:

       logout
       # or
       exit

---

## 5. Test Password Login (Should Fail)

Verify that password-based authentication is disabled:

    ssh -o PubKeyAuthentication=no -i <path/to/private-key> <user@ip>

You should **not** be able to log in.

---

## 6. Reconnect Using SSH Key Authentication

    ssh -i <path/to/your/key> <user@ip>

---

## 7. Install Nginx

Update the package list and install Nginx:

    sudo apt update
    sudo apt install nginx -y

> The `-y` flag automatically confirms the installation.

---

## 8. Configure Nginx to Forward Requests to Port 8081

Create a new Nginx configuration file:

    sudo nano /etc/nginx/sites-enabled/alternatives

Insert the following configuration:

    server {
        listen 8081;
        listen [::]:8081;

        root /var/www/alternatives;
        index alternate-index.html;

        location / {
            try_files $uri $uri/ =404;
        }
    }

Save and close the file.  
Then restart Nginx to apply the changes:

    sudo service nginx restart

---

## 9. Add an Alternate HTML Page

Create a new HTML file that Nginx will serve:

    sudo nano /var/www/alternate-index.html

You can now access your page via port **8081** (e.g., `http://your-server-ip:8081`).

---

## 10. Set Up Git on the Server

### 1. Generate an SSH Key on the Server

    ssh-keygen -t ed25519

Specify the file path for the key pair.

---

### 2. Display and Copy the SSH Public Key

Navigate to the directory where the SSH key was created, then run:

    cat <path/to/your/key.pub>

Copy the displayed SSH key.

---

### 3. Add the SSH Key to GitHub

1. Log in to your **GitHub** account.  
2. Go to **Settings → SSH and GPG keys**.  
3. Click **New SSH key**.  
4. Enter a **Title** (e.g., "V-Server Access").  
5. Paste the copied SSH key into the **Key** field.  
6. Save the new SSH key.

---

## Checkliste for the V-Server-Setup

[Git + V-Server Checkliste (PDF)](Git%20+%20VServer%20Checkliste.pdf)

---

### Setup Complete

Your V-Server is now:

- Secured with SSH key authentication  
- Configured with Nginx running on port **8081**  
- Ready to connect to GitHub via SSH
