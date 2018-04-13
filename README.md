# SCSI-Target-in-Linux
Install Linux

Set up 'YUM'. Use Linux Installation Media (RedHat/Oracle Linux)
```YUM Setup
# mount /dev/sr0 /media
mount:block device /dev/sr0 is write-protected, mounting read-only
# vi /etc/yum.repos.d/local.repo
[base]
name=Local Repository
baseurl=file:///media/
enabled=1
gpgcheck=0
```
/dev/sr0: Linux Installation media is attached to vm
file:///media/
*file:// - protocol*

*/ - actual location in this case, /media is the directory has all RPMs. Interpret as /media/*

Remove all other \*.repo files under /etc/yum.repo.d/ directory.
```
# yum clean all   (# clean local database for yum usage)
Loaded plugins: ulninfo
Cleaning repos: base
Cleaning up Everything
# yum repolist
```

Set hostname/IP address
```
# vi /etc/sysconfig/network
NETWORKING=yes
HOSTNAME=ovs1.local.io
DNS=
GATEWAY=
ESC:wq!

# vi /etc/sysconfig/selinux
...
SELINUX=disabled

ESC:wq!

# vi /etc/sysconfig/network-scripts/ifcfg-eth0
...
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=static
IPADDR=192.168.
PREFIX=24

ESC:wq!
```

Post-Configuration.
