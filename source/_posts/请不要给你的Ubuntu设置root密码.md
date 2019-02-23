---
title: 请不要给你的Ubuntu设置root密码
tags:
  - root
  - ubuntu
id: 839
categories:
  - Linux
date: 2015-07-21 15:35:36
---

#### 1\. Why you don't have a root password

While you _can_ create a password for the superuser account allowing you to log in as root with `su`, it's worth mentioning that this isn't the "Ubuntu" way of doing things. Ubuntu have specifically chosen _not_to give a root login and password by default for a reason. Instead, a default Ubuntu install will use `sudo`.  <p>Sudo is an alternative to giving people a root password in order to perform superuser duties. In a default Ubuntu install the person who installed the OS is given "sudo" permission by default.  <p>Anybody with "sudo" permission may perform something "as a superuser" by pre-pending `sudo` to their command. For instance, to run `apt-get dist-upgrade` as a superuser, you could use:

    sudo apt-get dist-upgrade


 ```

    You will see this usage of sudo pretty much anywhere you read a tutorial about Ubuntu on the web. It's an alternative to doing this.
```  

 su
    apt-get dist-upgrade
    exit


 ```

    With sudo, you choose in advance which users have sudo access. There is no need for them to remember a root password, as they use their own password. If you have multiple users, you can revoke one's superuser access just by removing their sudo permission, without needing to change the root password and notify everyone of a new password. You can even choose which commands a user is allowed to perform using sudo and which commands are forbidden for that user. And lastly, if there is a security breach it can in some cases leave a better audit trail showing which user account was compromised. 
    <p>Sudo makes it easier to perform a single command with superuser privileges. With `su`, you permanently drop to a superuser shell which must be exited using `exit` or `logout`. This can lead to people staying in the superuser shell for longer than necessary just because it's more convenient than logging out and in again later. 
    <p>With sudo, you still have the option of opening a permanent (interactive) superuser shell with the command:
```  

 sudo su


 ```

    ... and this can still be done without any root password, because `sudo` gives superuser privileges to the `su` command. 
    <p>_And similarly, instead of `su -` for a login shell you can use `sudo su -` or even `sudo -i`._ 
    <p>However when doing so you just need to be aware that you are acting as a superuser for every command. It's a good security principle not to stay as a superuser for longer than necessary, just to lessen the possibility of accidentally causing some damage to the system (without it, you can only damage files your user owns). 
    <p>Just to clarify, you _can_, if you choose, give the root user a password allowing logins as root as described in @Oli's answer, if you specifically want to do things this way instead. I just wanted to let you know about the Ubuntu convention of preferring `sudo` instead and let you know that there is an alternative. 

    * * *

    #### 2\. The problems with your chmod 777 -R command

    <p>Your question also has a second part to it: your issues with the command `sudo chmod 777 -R foobs`. 
    <p>Firstly, the following warning indicates a potentially serious security issue on your machine:
```  

 sudo: /var/lib/sudo writable by non-owner (040777), should be mode 0700


 ```

    This means that at some stage, you've set `/var/lib/sudo` to be world-writable. I imagine you've done this at some stage using a command like `sudo chmod 777 -R /`. Unfortunately, by doing this you've probably pretty much broken all file permissions throughout your system. It's unlikely that this will be the only important system file whose permissions have been changed to be world-writable. Essentially you have an easily hackable system now, and the only easy way to get it back would be to re-install. 
    <p>Secondly, the command you were using:
```  

 sudo chmod 777 -R foobs

When manipulating files within your home directory, in this case in `~/Desktop`, you should not have to use `sudo`. All the files you create in your home directory should be modifiable by you anyway (and if not, something funny is going on). 
<p>Also, you need to be fully aware of the consequences of changing file permissions en masse, such as doing it recursively or on a huge number of files. In this case, you're changing carefully set up file permissions to be world-writable. Any other user, or any buggy server software on the machine, may have easy access to overwrite all of these files and directories. 
<p>It's almost certain that `chmod 777 -R [dir]` it is _not_ an appropriate solution for whatever problem you were trying to solve (and as I mentioned above, there is evidence that you have done it to system files in /var/lib too, and I assume to lots of other places). 
<p>A couple of basic rules of thumb: 

*   <p>If you're just messing with your own files in your home directory, desktop etc, you should never need to use `sudo` or superuser rights. If you do, it's a warning sign that you're doing something wrong.

    <li>

You should never manually modify system files owned by packages. Exception: unless you're doing it specifically in ways documented by those packages, such as by modifying their configuration in `/etc`. This applies also to changing file permissions. If a tutorial or attempt to fix the problem requires `sudo` or superuser rights, and it's not simply a change to a configuration in /etc/, it's a warning sign that you're doing something wrong.

原文链接:[http://askubuntu.com/questions/444246/why-dont-i-have-a-password-for-su-problems-with-sudo](http://askubuntu.com/questions/444246/why-dont-i-have-a-password-for-su-problems-with-sudo "http://askubuntu.com/questions/444246/why-dont-i-have-a-password-for-su-problems-with-sudo")