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


# vi /etc/sysconfig/network-script/ifcfg-eth0
ONBOOT=yes
NM_CONTROLLED=no
BOOTPROTO=static
IPADDR=
PREFIX=24 (if it is in class-C)

ESC:wq!
```

Post-Configuration (SCSI-target-utils QuickStart Guide)
Installation
Start by installing the __scsi-target-utils__ package
```
# yum -y install scsi-target-utils
```
Configuration

1. Firewall
Either stop/disable or reconfigure. For the simple practice, stop.
```Firewall
# service iptables stop
# chkconfig --del iptables
# chkconfig --level 2345 iptables off
```
2. Service (tgtd) startup
```Service tgtd startup
# service tgtd start
# chkconfig tgtd on
```
Check /etc/tgt/target.conf
```/etc/tgt/targets.conf
# Similar to the one above, but we fetch vendor_id, product_id, product_rev and
# scsi_sn from the disks.
# Vendor identification (vendor_id) is replaced in all disks by "MyVendor"

#<target iqn.2008-09.com.example:server.target4>
#
<target iqn.2018-04.io.local:[hostname].all>
    direct-store /dev/sdb	# Becomes LUN 1
    direct-store /dev/sdc	# Becomes LUN 2
    direct-store /dev/sdd	# Becomes LUN 3
#    write-cache off
    vendor_id Vendor Inc.
</target>

ESC:wq!
```

3. Add iSCSI disk
Add phyiscal disk or add a block device
* (VirtualBox/VMware), add a disk in iSCSI Controller
* A block device
``` Block device
# dd if=/dev/zero of=/var/tmp/iscsi-disk1 bs=1M count=8000  (8GB file)
```

4. Up and running
* Create a target device
* Add a logical unit
* Enable the target to accept initiator

List Active targets:
```
# tgtadm --lld iscsi --mode target --op show
```

Create a new target device:
```
# tgtadm --lld iscsi --mode target --op new --tid=1 --targetname iqn.2018-04.io.local:[hostname].all
```

Add a logical unit (LUN):
```
% tgtadm --lld iscsi --mode logicalunit --op new --tid 1 --lun 1 -b /dev/sdb (or /var/tmp/iscsi-disk1)
```
Repeat creation and addition for all disks (increasing tid and lun)

5. Permissions (if needed)
To display a list of all configured user accunts,
```
# tgtadm --lld iscsi --mode acount --op show
```

All IP wildcard to allow all initiators
```
# tgtadm --lld iscsi --mode target --op bind --tid 1 -I ALL
```

IP-based restrictions (If you already accepted ALL initiators, remove)
```
# tgtadm --lld iscsi --mode target --op unbind --tid 1 -I ALL

# tgtadm --ld iscsi --mode target --op bind --tid 1 -I 10.10.0.201

or subnet
# tgtadm --lld iscsi --mode target --op bind --tid 1 -I 10.0.0.0/24
```




