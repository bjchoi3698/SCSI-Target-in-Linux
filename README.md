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
*/ - actual location in this case, /media is the directory has all RPMs*

