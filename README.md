# OpenSSH <=6.6 SFTP misconfiguration exploit for 64bit Linux

penSSH lets you grant SFTP access to users without allowing full command
execution using "ForceCommand internal-sftp". However, if you misconfigure
the server and don't use ChrootDirectory, the user will be able to access
all parts of the filesystem that he has access to - including procfs. On
modern Linux kernels (>=2.6.39, I think), /proc/self/maps reveals the
memory layout and /proc/self/mem lets you write to arbitrary memory
positions. Combine those and you get easy RCE.

The linux version of OpenSSH 6.7 contains a mitigation, see the release notes:

 * sftp-server(8): On platforms that support it, use prctl() to
   prevent sftp-server from accessing /proc/self/{mem,maps}

Here's my PoC for 64bit Linux:


```
gcc sshsploit.c -o sshsploit -std=c99 -lssh
```