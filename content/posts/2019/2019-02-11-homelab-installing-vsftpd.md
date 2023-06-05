---
author: "Denis Nu\u021Biu"
categories:
- Cloud
date: '2019-02-11T21:01:12+00:00'
guid: https://nuculabs.wordpress.com/?p=337
id: 337
layout: post
permalink: /2019/02/11/homelab-installing-vsftpd/
tags:
- ftp
- Linux
- system administration
- vsftpd
title: Installing and configuring an anonymous VSFTPD server
---
After spending a good part of my weekend fiddling with VSFTPD, I’m very happy that I managed to get it to work properly.


My goal was to create a simple, anonymous and private FTP server for my home network. It should facilitate sharing files between my machines.


## Configuring the Server


If you’d like to try out my configuration all you need to do is look at the following commands and replace your configuration file with mine. The configuration file will be provided later in the article.


```
# For Ubuntu
sudo apt-get install vsftpd
cp /etc/vsftpd.conf /etc/vsftpd.conf.orig

# Make the directories for the server
sudo mkdir -p /srv/vsftpd/root/public
sudo touch /srv/vsftp/banner

# Change permissions and set ownership
sudo chown -R ftp:ftp /srv/vsftpd/
sudo chmod -R 555 /srv/vsftpd/
sudo chmod 775 /srv/vsftpd/root/public

# Edit the configuration file
# /etc/vsftpd.conf with editor of choice

# Restart the service and check the status
sudo systemctl restart vsftpd
sudo systemctl status vsftpd
```


The configuration file vsftpd.conf that I use can be found below:


```
# Nucu Labs's BUSY vsFTPd server configuration file.
# Denis Nutiu 09.02.2019

# Scope of the server: The server is supposed to facilitate easy file sharing and should
# be only available on the local network. No outside access is allowed!

# The meaning of this configuration file and the full list of options can be found by
# checking the manual page of vsftpd.

listen=NO
listen_ipv6=YES

### Debug Options

# If enabled, a log file will be maintained detailing uploads and downloads. 
# By default, this file will be placed at /var/log/vsftpd.log,
xferlog_enable=YES

# debug_ssl=YES
# log_ftp_protocol=YES
# syslog_enable=YES

###

# If enabled, both the usernames ftp and anonymous are recognised as anonymous logins.
anonymous_enable=YES

# This option represents a directory which vsftpd will try to change into after an anonymous login. 
# Failure is silently ignored.
anon_root=/srv/vsftp/root
local_root=/srv/vsftp/root
allow_writeable_chroot=YES

# Enable local users to login to the FTP.
local_enable=NO

# This option is the name of a file containing text to display when someone connects to the server.
banner_file=/srv/vsftp/banner

# When enabled, this prevents vsftpd from asking for an anonymous password 
# - the anonymous user will log straight in.
no_anon_password=YES

# If set to YES, anonymous users will be permitted to create new directories under certain conditions.
# For this to work, the option write_enable must be activated, 
# and the anonymous ftp user must have write permission on the parent directory.
anon_mkdir_write_enable=YES

# If set to YES, anonymous users will be permitted to perform write operations 
# other than upload and create directory, such as deletion and renaming. 
# This is generally not recommended but included for completeness.
anon_other_write_enable=YES

# If set to YES, anonymous users will be permitted to upload files under certain conditions.
anon_upload_enable=YES

# When enabled, anonymous users will only be allowed to download files which are world readable.
anon_world_readable_only=YES

# If enabled, all anonymously uploaded files will have the ownership changed 
# to the user specified in the setting chown_username
chown_uploads=YES
chown_username=ftp
nopriv_user=ftp

delete_failed_uploads=YES

# If enabled, users of the FTP server can be shown messages when they first enter 
# a new directory. By default, a directory is scanned for the file .message.
dirmessage_enable=YES

# If enabled, all user and group information in directory listings will be displayed as "ftp".
hide_ids=YES

# Allows: STOR, DELE, RNFR, RNTO, MKD, RMD, APPE and SITE.
write_enable=YES

# Upload files are executable.
chown_upload_mode=0777
file_open_mode=0777
local_umask=002
anon_umask=002

###

# Use the system's local time in GMT.
use_localtime=YES

# This option should be the name of a directory which is empty.  Also, the
# directory should not be writable by the ftp user. This directory is used
# as a secure chroot() jail at times vsftpd does not require filesystem
# access.
secure_chroot_dir=/var/run/vsftpd/empty

# This string is the name of the PAM service vsftpd will use.
pam_service_name=vsftpd

# Uncomment this to indicate that vsftpd use a utf8 filesystem.
utf8_filesystem=YES
```


Using this setup will give you a fully working anonymous VSFTPD server. BUT WAIT! Make sure that you don’t expose the server to the internet! Keep it for private use only, since the server is anonymous everyone can download and upload files.


## Troubleshooting Server Problems


I had lots of trouble getting the permissions right, if you don’t get the permissions right you’ll find yourself unable to upload files, download them, create directories or cd into directories.


The most common error I encountered was **Writable root. Owner of the chroot() is root and has write permissions.** This was fixed by removing the write permission from the /**srv/vsftpd/root** directory.


Another thing you may want is to add your user to the **ftp** group. I did miss the **-a** when I added myself and I ended up removing myself from all groups, except the ftp group. I had to boot Ubuntu in Recovery Mode, get a root shell and add myself back to sudo as well as other administrative groups. To add yourself to the ftp group, you can safely copy paste the following command: `sudo usermod -a -G ftp $USER`.


If the banner file (the one created with the touch command above) is not existent or permissions are insufficient, the server won’t start.


## Troubleshooting Client Problems


To be honest I haven’t used FTP that much. Everytime you connect to the VSFTPD server you should do so in passive mode, this means that the server will open another socket when doing an operation instead of initializing a connection to you (the client), if you don’t use passive FTP mode, you’ll get a prompt from your firewall to allow the FTP client to pass through. To connect using passive FTP, you should run: `ftp -p ftp_server_ip_or_alias`


When uploading images, pdfs or other binary files, you’ll need to issue the **binary** command in your FTP client, before the **put**, otherwise things are going to get messy and the binary file will get corrupted.


## Conclusions


I had great fun installing and configuring VSFTPD, also writing this article. If you need anymore help with VSFTPD I suggest visiting the [Arch Linux wiki page](https://wiki.archlinux.org/index.php/Very_Secure_FTP_Daemon), it contains a great deal of information.


Thank you for reading and have a great day!


This article is part of my Homelab series, you may want to check out my previous post: [My Homelab Journey: Introduction](https://nuculabs.dev/posts/2019/02/09/my-homelab-journey-introduction/)