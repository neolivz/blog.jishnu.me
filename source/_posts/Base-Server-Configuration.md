---
title: Base Server Configuration
date: 2017-02-08 14:37:07
tags:
---

I decided to scrap everything in my VPS and decided to start my web server from scratch. Here are the base stuff which I do.
PS: This is a Ubuntu machine, other distros will have a different command for `apt-get`


# Base Setup


## Update and upgrade
When ever I get a new VPS first thing I do is update my root password.

```
passwd
```

Once that is done I get all the lastest packages.

```
apt-get update
apt-get upgrade
```

PS: If you are getting warning to update kernel ignore it.

## Create a new user
Once that is done I create my user, neo; and create his home folders along with ssh folder to keep the authorized keys.

```
useradd neo
mkdir /home/neo
mkdir /home/neo/.ssh
chmod 700 /home/deploy/.ssh
```

Once this is done, I set password for the new user and try to login as the user.

```
passwd neo
```

Create a new terminal session and try to login. If you username is same for your local machine and remote machine you don't have to pass in the username.
```
ssh neo@jishnu.me
or
ssh jishnu.me
```

Enter the password, once it logins `exit`.

## Password less authentication

Now lets setup ssh login without password.

Copy the content of your ssh keys from your local machine. If you don't have one [create one](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) in your local.

Once that is done copy the content of the pub file of your ssh file (usually `id_rsa`)

```
cd /home/neo/.ssh/
vi authorized_keys
```
Press `i` and paste the content and save & exit (Press `:wq!`)

```
chmod 400 authorized_keys
chown neo:neo /home/neo -R
```

Now try logging in without password.

```
ssh jishnu.me
```
It should login (same username in local and remote)

## Grant sudo permission

Next step is adding the new user to the sudoers

```
usermod -aG sudo neo
```

Now you can try logging in with your new user and try running some command with sudo

```
ssh jishnu.me
sudo apt-get update
```

It will ask for password, enter the password and it should get apt-get update rolling in.

## Remove root and password logging in
*Once you are done with this step, if you lose your ssh keys you won't be able to login to your server. So backup keys first*

Now we will prevent root logging in and password logging in.

```
vim /etc/ssh/sshd_config
```
Make the following changes
```
PermitRootLogin no
PasswordAuthentication no
```
Now restart the ssh service, and `exit` the session. You won't be able to login using root or from a computer with key is not configured.

# Installing git

We need git not only for bring the code to the server but also for installing oh-my-zsh in next step

```
sudo apt-get install git-core
```
I reuse my ssh keys from my local machine in the remote. If you want separate keys [create one](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/) again.
```
scp github jishnu.me:~/.ssh
```

# Changing The Shell
I am a big fan zsh and [oh-my-zsh](http://ohmyz.sh/).

## zsh shell
First we install zsh and change default shell to zsh.

```
sudo apt-get insall zsh
sudo usermod -s $(which zsh) neo
```

## Installing oh-my-zsh
Once that is done install oh-my-zsh
```
sh -c "$(wget https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

## Additional Plugins
Once that is done install additional plugins for oh-my-zsh. By default git plugin is enabled but I add nodejs, npm and github plugins along with it. To enable plugins edit `zshrc` file in the root.

```
vi ~/.zshrc
```
Change the line to
```
plugins=(git node npm github)
```
## Changing the theme
I like the theme [Honukai](https://github.com/oskarkrawczyk/honukai-iterm-zsh)

```
wget https://raw.githubusercontent.com/oskarkrawczyk/honukai-iterm/master/honukai.zsh-theme  -P ~/.oh-my-zsh/themes/
vi ~/.zshrc
```
Now change the theme to Honukai.
```
ZSH_THEME="honukai"
```

# nginx
I have moved away from apache to nginx sometime back and never looked back.

## Remove apache (if you have it already installed)
My VPS ubuntu distro comes with apache pre installed. So we need to remove that first.
```
sudo service apache2 stop
sudo apt-get purge apache2 apache2-utils apache2.2-bin apache2-common
sudo apt-get autoremove
```
Now lets just do the cleanup
```
whereis apache2
```
Delete the resulting files/folders

## Installing nginx
Based on the [guide](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)
In summary, edit the apt-get source file list
```
sudo vi /etc/apt/sources.list
```
Add the lines
```
deb http://nginx.org/packages/ubuntu/ xenial nginx
deb-src http://nginx.org/packages/ubuntu/ xenial nginx
```
Where `xenial` is the release version of your ubuntu, mine being 16.04
```
sudo apt-get update
```

This will generate an error like
`W: GPG error: http://nginx.org/packages/ubuntu xenial InRelease: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY $key`
get the corresponding `$key` from the server
```
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys $key
sudo apt-get update
sudo apt-get install nginx
sudo systemctl start nginx
```
