# iSCSI-Target-in-Linux

0. Set up 'YUM' locally without internet access. Use Linux Installation Media (RedHat compatibles)
```YUM Setup
# mount /dev/sr0 /media
mount:block device /dev/sr0 is write-protected, mounting read-only

# vi /etc/yum.repos.d/local.repo
[base]
name=Local Repository
baseurl=file:///media/ . (file:// - protocol, the 3rd (/) is the directory location, /media)
enabled=1
gpgcheck=0
```

Remove all other \*.repo files under /etc/yum.repo.d/ directory.
```
# yum clean all   (# clean local database for yum usage)
Loaded plugins: ulninfo
Cleaning repos: base
Cleaning up Everything
# yum repolist
```
Optional steps in case no network configured.
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

1. Prepare Disk or Logical Volume
It will be varied for your situation. Assume that there are two disks (/dev/sdb and /dev/sdc) available for this practice.

2. Installation
Start by installing the __scsi-target-utils__ package
```
# yum -y install scsi-target-utils
```

3. Start __tgtd__ service and enable at boot
```Start tgtd
# service tgtd start
# chkconfig tgtd on     (Enable tgtd service at boot)

# chkconfig --list tgtd
```

4. Turn off Firewall (Simple practice no worries security_)
For simple practice, no firewall.
```Firewall
# service iptables stop
# chkconfig --del iptables
# chkconfig --level 2345 iptables off
```
5. Edit /etc/tgt/target.conf. Add the following lines for/dev/sdb and /dev/sdc
At the end of "targets.conf", add the following (assume we have two disks, /dev/sdb and /dev/sdc)
```
<target iqn:2018-04.local.ovm3:ovm3-box.target1>
	backing-store	/dev/sdb
	backing-store	/dev/sdc
</target>
```

6. Reload __tgtd__ and verify
```
# service tgtd reload

# tgtadm --mode target --op show
Target 1: iqn:2018-04.local.ovm3:ovm3-box.target1
    System information:
        Driver: iscsi
        State: ready
    I_T nexus information:
    LUN information:
        LUN: 0
            Type: controller
            SCSI ID: IET     00010000
            SCSI SN: beaf10
            Size: 0 MB, Block size: 1
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            Backing store type: null
            Backing store path: None
            Backing store flags:
        LUN: 1
            Type: disk
            SCSI ID: IET     00010001
            SCSI SN: beaf11
            Size: 107374 MB, Block size: 512
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            Backing store type: rdwr
            Backing store path: /dev/sdb
            Backing store flags:
        LUN: 2
            Type: disk
            SCSI ID: IET     00010002
            SCSI SN: beaf12
            Size: 26399 MB, Block size: 512
            Online: Yes
            Removable media: No
            Prevent removal: No
            Readonly: No
            Backing store type: rdwr
            Backing store path: /dev/sdc
            Backing store flags:
    Account information:
    ACL information:
        ALL
```
