---
author: albert
tags: linux task-automation
---
Step-by-step description how I have set a scheduled backup of a local directory to network attached storage (NAS) - OpenMediaVault running on Raspberry Pi 4.
<!--more-->

## Pre-requirements
1. PC running Kubuntu (should also work on other Ubuntu/Debian based distributions)
1. Network attached storage (NAS) connected to your local network - OpenMediaVault (OMV) running on Raspberry Pi 4.

## Find the path to the destination directory on NAS

Open terminal and connect with the server via SSH (IP address for your Raspberry Pi can be different)
```bash
ssh pi@192.168.0.248
```

List the ```/srv``` directory content
```bash
ls /srv
```

The output will be something like this
```
dev-disk-by-uuid-eb282c0e-93b4-4921-bd56-2d6ed5937063  ftp  pillar  salt
```

The long name ```dev-disk-by-uuid-<uuid>``` is my drive connected to Raspberry Pi. Your drive will be named probably similar way. We can list its content
```bash
ls /srv/dev-disk-by-uuid-*
```

You should see the directory structure  from the drive mounted in OMV. The destination directory should be located on the drive, so the destination path should look like, e.g.,
```bash
/srv/dev-disk-by-uuid-eb282c0e-93b4-4921-bd56-2d6ed5937063/backups/dst
```

Note that you will need write permission for the destination directory.

## Write script to sync local directory with destination directory on NAS

I want to backup my user's home directory, excluding all files/directories that name starts with dot (hidden). I create a new script
```bash
nano ~/bin/home2omv
```

And paste into it the code
```bash
#!/bin/bash

rsync -av --delete /home/albi/ albi@192.168.0.248:/srv/dev-disk-by-uuid-eb282c0e-93b4-4921-bd56-2d6ed5937063/Albert/backups/home/albi --exclude '.*'
```

Remember to make the file executable
```bash
chmod +x ~/bin/home2omv
```

Also, ```~/bin``` should be added to the ```PATH``` (if its not)
```bash
export PATH=$PATH:~/bin
```

## Generate public key for OMV

At the moment it should be possible to call ```home2omv``` from the terminal. However, the user's password to the server is required each time the script runs. If the password has to be typed each time, the task cannot be scheduled and run automatically. 

Generate public key
```bash
ssh-keygen -t rsa
```

Export the public key to RFC 4716 format
```bash
ssh-keygen -e -f ~/.ssh/id_rsa.pub
```

Copy the whole output, e.g.,
```bash
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "3072-bit RSA, converted by albi@albi-lenovo from OpenSSH"
<long-key-here>
---- END SSH2 PUBLIC KEY ----
```

Open your web browser, type IP address of your Raspberry Pi into address bar and login to OMV panel. Go to ```Access Right Management > User```.  Select your user name and click ```Edit```. In popup window, switch to ```Public keys``` tab. Paste copied public key into the field in the popup and click ```Save``` button.

Now, it should be possible to run the backup script without password required.

## Add scheduled task for backup

To set scheduled task in Linux, in terminal type
```bash
crontab -e
```

I want my backup script to run each day at 8 p.m. So, at the end of the file that opened, I add line
```bash
0 20 * * * /home/albi/bin/home2omv
```

The line starts with 5 numbers followed by a command to be executed. First number gives minute, second - hour, third - day of month, fourth - month, and the last one - day of week the command should be executed. Asterisk (\*) stands for *any*.

Done! Now my home directory should be copied to my NAS every day at 8 p.m.
