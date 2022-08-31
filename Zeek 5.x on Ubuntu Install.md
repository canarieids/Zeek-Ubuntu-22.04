  

<p style="font-weight:600; font-size:60px ; color:red; text-align:center">Joint Security Project</p>

----

<p style="font-weight:600; font-size:30px ; color:black; text-align:center">Zeek 5.x LTS Install Guide</p>

<div style="text-align:center">    
  <a href="http://www.canarie.ca">canarie.ca</a> | 
  <a href="https://twitter.com/CANARIE_Inc">@CANARIE</a>
</div>


<div style="page-break-after: always; break-after: page;"></div>


<div style="page-break-after: always; break-after: page;"></div>

# 1. Overview

This document is a step-by-step guide for the configuration of the Zeek platform on hardware defined, and distributed, by the <u>**CANARIE Joint Security Program**</u>.

- Blocks that start with `#` are expected to be run as the `root` user.
- Blocks that start with `$` are expected to be run as the `zeek` user.

### 1.1 Community Resources

>  The Zeek community is vibrant, friendly, and easily accessible.  Use the following resources for communication and documentation questions that fall outside of this document and/or project.

- [Zeek](https://www.zeek.org/)[ Website](https://www.zeek.org/)
- [Zeek Manual](https://docs.zeek.org/en/master/)
- [Zeek](https://zeek.org/connect/)[ Connect](https://zeek.org/connect/)
- [Zeek](https://zeek.org/blog/)[ Blog](https://zeek.org/blog/)

### 1.2 JSP FAQ

> We have composed a frequently asked questions file, which is located in the JSP portal.  Both pilot and phase 2 participants will find this file useful.

- [Zeek / JSP - FAQ](https://jspportal.canarie.ca/wpc_downloader/core/?wpc_action=view&id=84) 

### 1.3 Warranty and Contacts

> **Network TAPs:**

Warranties are with the equipment vendor (Gigamon/IXIA) and participants can pursue requests.  Ensure you have the `serial number` prior to initiating a service request.

> **Server:**

Participants can contact Dell directly for warranty & customer support for hardware issues and troubleshooting.  Ensure you have the `service tag` prior to initiating a service request.

<div style="text-align:center">    
  <a href="https://www.dell.com/support/incidents-online/ca/en/04/contactus/dynamic">Dell Onlone Customer Service</a> 
</div>
<div style="page-break-after: always; break-after: page;"></div>


# 2. Disk Configuration

### 2.1 RAID Options for JSP Phase 1

| Level | Min. # Disks | Max Capacity | Advantages | Disadvantages                                                |
| ----- | ---------- | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0     |      1       | 4TB          | •High performance.<br />•Easy to implement.<br />•Highly efficient (no parity overhead). | •No redundancy.<br />•Limited business use cases due to no  fault tolerance. |
| 1     |      2       | 2TB          | •Fault tolerant.  <br />•Easy to recover data in case of drive  failure  •Easy to implement. | •Highly inefficient (100% parity  overhead).<br/> •Not scalable (becomes very costly as  number of disks increase). |

### 2.2 RAID Options for JSP Phase 2

| Level | Min. # Disks | Max Capacity | Advantages                                                   | Disadvantages                                                |
| ----- | ------------ | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0     | 1            | 4TB          | •High performance.<br />•Easy to implement.<br />•Highly efficient (no parity overhead). | •No redundancy.<br />•Limited business use cases due to no  fault tolerance. |
| 5     | 3            | 3TB          | •Fault tolerant.<br />•High efficiency.                      | •Disk failure has a medium impact on  throughput.<br />•Complex controller design. |
| 6     | 4            | 2TB          | •Fault tolerant – increased redundancy  over RAID 5.<br />•High efficiency. | •Write performance penalty over RAID 5.<br />•More expensive than RAID 5. <br/>•Disk failure has a medium impact on  throughput. |
| 10    | 4            | 2TB          | •Extremely high fault tolerance.<br />•Very high performance.  <br/>•Faster rebuild performance than 0+1. | •Very Expensive. <br />•High Overhead.<br />•Limited scalability. |

### 2.3 Configure Disk(s)

<div style="text-align:center">    
  <a href="https://www.dell.com/support/manuals/ca/en/cabsdt1/poweredge-r440/idrac_3.30.30.30_lc_ug/configuring-raid?guid=guid-a86b35a2-03a6-4838-952f-24992a99998e&lang=en-us">Dell PowerEdge R440 RAID Configuration Guide</a> 
</div>

> Recommended configuration:
> -The SSD drive should be configured as a NON-RAID Disk
> -Prioritize support for high IOPS is a RAID 10.
> -Priortize support for high capacity (retention) is a RAID 5.


### 2.4. Prepare USB Key

1. Download the ISO for Ubuntu 22.x LTS
> Download the latest version of Ubuntu 22.x LTS server
> Click Here: [Ubuntu Server Download page](https://ubuntu.com/download/server).

2.  Download and Install an ISO to USB utility
> Download, install and run a USB creation tool.  Rufus is tool used in this document.
> Rufus [Rufus](https://rufus.ie/).
 

3.  Create bootable USB key
> Follow these steps with Rufus to build a USB key from the Ubuntu ISO

​		1. Open `Rufus`

​		2. Under `Device` dropdown, choose target USB key.

​		3. `Select` downloaded ISO.

​		4. Navigate for and Choose ISO file.

​		5. Select `Open`.

​		6. Click `START` to begin writing the ISO.

![image-20200505231623160](/images/image-20200505231623160.png)

### 2.5. Virtual Deployment

> If physical access to the server is not possible, you can virtually mount an image.  It is recommended to install the operating system with a USB key that is connected to one of the server’s USB ports [Click here for more information.](https://www.dell.com/support/article/ca/en/cabsdt1/sln296648/using-the-virtual-media-function-on-idrac-6-7-8-and-9?lang=en)

1. Click `Configuration`.
2. Select `Virtual Media`.
3. Navigate for and select the ISO file.
4. Click `Open`.

![image-20200505232027969](/images/image-20200505232027969.png)





### 2.6. Disk Configuration - Phase 2

1. To start the Lifecycle Controller, press `F10`.

![image-20200506143343112](/images/image-20200506143343112.png)

2. Select `Configure RAID and Deploy and Operating System`.

<img src="/images/image-20200506143424102.png" alt="image-20200506143424102" style="zoom:90%;" />

3. Select `Configure RAID First` then click `Next`.

![image-20200506143525285](/images/image-20200506143525285.png)

4.  Click `Next`.

> Do not select the SSD. We mount this drive later under `/opt/zeek/spool`.

![image-20200506143702013](/images/image-20200506143702013.png)

5. Select `RAID 10` then click `Next`.

![image-20200506143820068](/images/image-20200506143820068.png)

6. Ensure boxes are checked for all 4 physical disks of the same size and click `Next`.

![image-20200506143914540](/images/image-20200506143914540.png)

7. Create a name for your virtual disk and click `Next`.

> The virtual disk is called `vd_zeek01` in this case.  Leave the other options at their default value.

![image-20200506144041102](/images/image-20200506144041102.png)

8. Verify settings and click `Finish`.

![image-20200506144321919](/images/image-20200506144321919.png)

9. Review the disk configuration.

> The storage configuration should resemble the following screenshot from `iDRAC Controller > Storage > Overview`:

![image-20200506144549361](/images/image-20200506144549361.png)

<div style="page-break-after: always; break-after: page;"></div>

# 3. Install and Configure Ubuntu Server

## Hardening the Server

> Apply security controls relevant to your organization's policy.  The following guidelines are posted on the on JSP Portal in the technical resources section:

- [CIS Benchmark - CentOS Linux 7 - EN](https://jspportal.canarie.ca/wpc_downloader/core/?wpc_action=view&id=33)
- [CIS Benchmark - CentOS Linux 8 - EN](https://jspportal.canarie.ca/wpc_downloader/core/?wpc_action=view&id=34)

## 3.1. OS Deployment

>Verify settings. Defaults are displayed and unchanged.

1. Choose `Any Other Operating System` from the drop-down and click `Next`.

![image-20200506145324195](/images/image-20200506145324195.png)

2. Check `Manual Install` and click `Next`.

<img src="/images/image-20200506154052269.png" alt="image-20200506154052269" style="zoom:90%;" />

4. Select the USB key where the downloaded ISO has been written from the drop-down and click `Next`.

![image-20200506154128697](/images/image-20200506154128697.png)

5. Review settings and click `Finish` to begin deploying the OS.

<img src="/images/image-20200506154801392.png" alt="image-20200506154801392" style="zoom:85%;" />

6. Select `Try or Install Ubuntu Server` option and press `Enter`.

<img src="/images/image-20200506154918662.png" alt="image-20200506154918662" style="zoom:85%;" />

7. Select your preferred language.

![image-20200506155052561](/images/image-20200506155052561.png)

8. Select your preferred keyboard layout

![image-keyboardlayout](/images/image-keyboardlayout.png)

## 3.2. Choose base installation
> Change the base installation from `Ubuntu Server` to `Ubuntu Server (minimized)`. 

1. Select `Ubuntu Server (minimized)`.

![image-20200506160026472](/images/image-typeofinstall-minimal.png)


## 3.3. Time Zone

> UTC is the recommended time zone. Zeek timestamps are in epoch format  (`ts` column of logs) .  Parsers, including `zeek-cut`, are equipped with the ability to convert epoch into other time zones and formats.  Other tools exist such as [this online epochconverter](https://www.epochconverter.com/).

1. Select `Time & Date`.

<img src="/images/image-20200506155405825.png" alt="image-20200506155405825" style="zoom:80%;" />

2. In the `Region` dropdown, select `Etc`.  In the `City` dropdown, select `Greenwich Mean Time`.

![image-20200506155451720](/images/image-20200506155451720.png)

## 3.4. Installation Destination

1. Click on `Installation Destination`.

![image-20200506160918064](/images/image-20200506160918064.png)

2. Select the RAID array, select `custom` and click `Done`.

> Only select the RAID array. The SSD will be provisioned after Zeek is installed.

-  A disk is selected when it's icon has a check mark.

![image-20200506161053366](/images/image-20200506161053366.png)

3. Select `Click here to create them automatically`.

> This will ensure that required partitions are present. It is recommended to reclaim space form the `/home` partition.

![image-20200506161447342](/images/image-20200506161447342.png)

> Autopartitioning allocates a lot of space to `/home`.  In the next steps we will give `/home` 3Gib and re-assign the remainder to the root `/` directory.

<img src="/images/image-20200506161618525.png" alt="image-20200506161618525" style="zoom: 80%;" />

4. Reclaim space from `/home`.  

   1. Select the `/home` partition.
   2. Change the `Desired Capacity` value to `3GiB`.
   3. Click on `Update Settings`.

![image-20200506161643412](/images/image-20200506161643412.png)

5. Assign reclaimed space to `/`.  
   1. Select the `/` partition.
   2. Change the `Desired Capacity` value to `1.8TiB`.

   > The smallest amout of space remaining as `Available Space` is disired.
   3. Click on `Update Settings`.
   4. Click `Done`.



## 3.5. Begin the installation

1. Click `Begin Installation`.

   > This is the last chance to modify options before they are written to disk.

![image-20200506162722251](/images/image-20200506162722251.png)

## 3.6. Account Creation

> Participants are encouraged to follow their own account policy.  Later steps will outline the creation of a `zeek` user and group that will have access to `Zeek Application` functions and files.

1. Assign a password to the `root` account.
2. Click `User Creation` to create user account.
3. Check `Make this user administrator`.
4. Check `Require a password to use this account`.

![image-20200506155933116](/images/image-20200506155933116.png)



## 3.7. Management Interface

> To download updates, packages and install repositories it is recommended to have connectivity to the public internet.

- If the server is provisioned by `dhcp-reservation`, skip this step.

  > Default configuration is DHCP.

- If static configuration is required, continue with this step.

1. Edit network interface script for management interface and apply address information.

`#vi /etc/sysconfig/network-scripts/ifcfg-<your-interface-name>`.

```
BOOTPROTO=static
IPADDR=<IPv4 address of your server>
GATEWAY=<IPv4 gateway of your IPADDR network>
NETMASK=<IPv4 network mask of your network>
DNS1=<IPv4 address of your primary DNS server>
DNS2=<IPv4 address of your secondary DNS server>
ONBOOT=YES
```

## 3.8. Zeek User & Group

1. Create a group and user called `zeek`:  `#groupadd zeek`.
2. Put the `zeek`user into the `zeek` group: `#useradd zeek -g zeek`.
3. Create the directory `/opt/zeek`: `#mkdir /opt/zeek`.
4. Give the `zeek` user and group recursive permissions: `#chown -R zeek:zeek /opt/zeek/`.
5. Set the password fo the zeek user:  `#passwd zeek` 

> You will be prompted for a `password`.



## 3.9. Repositories & Dependencies

> Add repositories & dependencies.

1. Open the existing repository file for editing:

| OS Version        | Repository File                                      |
| :---------------- | ---------------------------------------------------- |
| < Centos 8.3.2011 | `#vi /etc/yum.repos.d/CentOS-PowerTools.repo`        |
| > CentOS 8.3.2011 | `#vi /etc/yum.repos.d/CentOS-Linux-PowerTools.repo ` |
| CentOS Stream     | `#vi /etc/yum.repos.d/CentOS-Stream-PowerTools.repo` |

2. Set the `enabled` field to `1`. The Default value is `0`.

```
...
[PowerTools]
...
enabled=1
```

3. Install dependencies.

```dependancies
#yum -y install cmake make gcc gcc-c++ flex bison libpcap-devel openssl-devel platform-python-devel swig zlib-devel kernel-devel kernel-headers python36 network-scripts tar wget gdb git python3-pip sendmail sendmail-cf rsync unzip epel-release
```



## 3.11. Converting from CentOS Linux to CentOS Stream

1. Update repository references:

```
#sudo sed -i -e "s|mirrorlist=|#mirrorlist=|g" /etc/yum.repos.d/CentOS-*
#sudo sed -i -e "s|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g" /etc/yum.repos.d/CentOS-*
```

2. Update the repositories:

`#yum -y update`

1. Update repos to centos-stream:

`# dnf -y swap centos-linux-repos centos-stream-repos`.

```swap
[root@zeek01 /]# cat /etc/centos-release
CentOS Linux release 8.3.2011
[root@zeek01 /]# dnf swap centos-linux-repos centos-stream-repos
Last metadata expiration check: 3:02:57 ago on Thu 22 Apr 2021 01:39:07 PM EDT.
Dependencies resolved.
...
Installed:
  centos-stream-repos-8-2.el8.noarch
Removed:
  centos-linux-repos-8-2.el8.noarch
Complete!
```



2. Initiate the conversion:

`# dnf -y distro-sync --allowerasing`.

```sync
[root@zeek01 /]#  dnf distro-sync --allowerasing
Last metadata expiration check: 0:00:26 ago on Thu 22 Apr 2021 04:44:07 PM EDT.
Dependencies resolved.
...
Complete!
```

3. Restart the server:

`#shutdown -r now`

4. Verify the upgrade

`#cat /etc/centos-version`

```verify
[root@zeek01 /]# cat /etc/centos-release
CentOS Stream release 8
```



# 4. Install and Configure Zeek 

## 4.1. Upgrade

> Participants who have compiled Zeek from source, and installed to `/opt/zeek/` can update by backing up customizations, downloading the repository and installing via yum.  Environment variables will still have to be defined or re-defined.

### 4.1.1 Upgrade from previously compiled installation or unofficial Zeek repositories (`security:zeek.repo`)

- Files located in `[prefix]/logs` are not affected during upgrade.

1. Make a back up of any customizations in `[prefix]/share/zeek/site` and `[prefix]/etc`.

2. Stop Zeek if it is started `$zeekctl stop`.

3. Perform Section `4.2. Fresh Install`  to add the repository and install the Zeek-lts via yum.  Return to the following step after completion.

4. Install plugins and recover customizations which have been previously backed up.

   > After upgrading Zeek, participants are encouraged consider the recommendations in `4.3. Zeek Configuration Options`.

1. Deploy Zeek `$zeekctl deploy`.

### 4.1.2 Upgrade from previously installed zeek-lts version such as `3.0.13`

1. Upgrade the package using yum:`#yum -y update zeek-lts`.
   - ensure that epel-release has been installed `yum -y install epel-release`.

```
# zeek -v
zeek version 3.0.13
# yum update -y zeek-lts
Dependencies resolved.
...
Upgrading:
...	
Complete!
# zeek --version
zeek version 4.0.5
```

2. Upon a major upgrade, Zeek replaces `node.cfg` and `zeekctl.cfg` with defaults, which does not contain custom configuration items like cluster configuration, log rotation and log expiry options.  We will restore our previously configured files if necessary:

   1. `$cd /opt/zeek/etc`
   2. `$mv node.cfg node.cfg.orig && mv node.cfg.rpmsave node.cfg`
   3. `$mv zeekctl.cfg zeekctl.orig && mv zeekctl.cfg.rpmsave zeekctl.cfg`

   

3. Re-install af-packet and re-set permissions.

> Refer to section **4.3.7**.

## 4.2. New Install

> Start here if Zeek has never been installed.  Zeek-lts is installed to the prefix `/opt/zeek` When installing from the official repository.
>

1. Use `wget` to download the Zeek repository into `/etc/yum.repos.d/`.

```download zeek repo
#cd /etc/yum.repos.d/ && wget https://download.opensuse.org/repositories/security:zeek/CentOS_8_Stream/security:zeek.repo
```

2. Use `yum` to install `zeek-lts`.

```install zeek
#yum -y install zeek-lts zeek-lts-devel
...
Dependencies resolved.
...
Complete!
```

3. Give Zeek permission to capture packets.

```
#setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
```

4. Add Zeek binary files to path.

```add zeek to path
#echo "pathmunge /opt/zeek/bin" > /etc/profile.d/zeek.sh
```

5. login as `zeek` to update the path.

```su - zeek
#su - zeek
```

6. Confirm path update.

> If the command returns `/opt/zeek/bin/zeek`, your path has been updated.


```
$which zeek
/opt/zeek/bin/zeek
```

## 4.3. Update / Upgrade Zeek-LTS

1. Ensure the most current repository file exists. A `Status code: 404` message when attempting to upgrade is likely solved.

```
#cd /etc/yum.repos.d/ && rm security:zeek.* && wget https://download.opensuse.org/repositories/security:zeek/CentOS_8_Stream/security:zeek.repo
```

2. Check current verison:  `# zeek -v`.

```
[root@zeek01 ~]# zeek -v
zeek version 4.0.3
```

3. Upgrade Zeek: `#yum -y update zeek-lts zeek-lts-devel`.

```
...
Complete!
```

4. Deploy `$zeekctl deploy`.
5. Verify Upgrade `# zeek -v`.

```
[root@zeek01 ~]# zeek -v
zeek version 4.0.5
```



## 4.4. Zeek Configuration Options

### 4.4.1. Assign SSD to `/opt/zeek/spool`

> For faster read/write speed, mount the SSD to `/opt/zeek/spool`.

1. Identify the SSD drive.

```fdisk
#fdisk -l
...
Disk /dev/sda: 223.6 GiB, 240057409536 bytes, 468862128 sectors
...
```

2. You may have to format your SSD.

> Use ext4.  Use appropriate disk label (sda, sdb, etc..).

```sudo mkfs.ext4 /dev/sda1 
#mkfs.ext4 /dev/sda
```

3. Stop Zeek `$zeekctl stop`.

4. Mount the SSD drive.

```mountssd
#mount /dev/sda /opt/zeek/spool
```

5. Edit `/etc/fstab` to persistently mount the SSD.

1. Get the `UUID` of the drive `blkid /dev/sda`.

> Here the `UUID` is `8859XXXX-XXXX-XXXX-XXXX-XXXXf57871b5`.

```uuid
#blkid /dev/sda
/dev/sda: UUID="8859XXXX-XXXX-XXXX-XXXX-XXXXf57871b5" TYPE="ext4" PARTLABEL="zeekspool" PARTUUID="04f529d2-a074-4ad5-8a45-48ccd8e8c8b0"
```

6. Write the `UUID` and partition information into the `/etc/fstab` file.

> After this step, the SSD drive will be mounted upon system startup.

```echo to fstab
#echo "UUID=8859XXXX-XXXX-XXXX-XXXX-XXXXf57871b5 /opt/zeek/spool ext4 defaults 0 0" >> /etc/fstab
```

7. Ensure correct permissions to spool directory.

```permissions
#chown -R zeek:zeek /opt/zeek
```

8. Reboot the server and verify persistence of the mount  `# shutdown -r now`.

9. Last verification of partition schema after reboot `# df -h`.

```dh -h
#df -h
...
/dev/sda              220G  160M  208G   1% /opt/zeek/spool
...
```

### 4.4.2. `sendmail`(optional)

> This option enables `zeek` and `zeekctl` to send email.  Defining recipients is covered in section `4.3.10.3. zeekctl.cfg`

- If not configured, you will get notified when running `zeekctl start` or `zeekctl deploy`.
- Not a dependency and Zeek will start without it.
- By default, Zeek will use sendmail when:
  - `zeek deploy` or  `zeek start` has been initiated.
  - A node crashes.
- Can be extended to send mail on events.
- Not installed by default but is included in the dependency section. participants can configure per environment.

1. Configure email recipients by editing the `zeekctl.cfg` file.  `#vi /opt/zeek/etc/zeekctl.cfg`:

> #Recipient address for all emails sent out by Zeek and ZeekControl.
> MailTo = security@your-org.ca

2. If required, edit the file `/etc/mail/sendmail.mc`locate the line `dnl define('SMART_HOST', smtp.your.provider')dnl`. 
   1. Remove the leading `dnl` and define your smtp relay in place of `smtp.your.provider`.
   2. Re-build the sendmail configuration `#/etc/mail/make`.
3. Enable and start sendmail `#systemctl start sendmail && systemctl enable sendmail`.
4. Run `$zeekctl deploy` and if sendmail is working correctly, recipient(s) will receive an email from the system.

### 4.4.3. `selinux`

> selinux is a security module that provides a mechanism for supporting access control policies.  It is set to `enforcing` by default and will block some services if not configured specifically or set to permissive.

- `sestatus`: Shows settings and paths.
- `setenforce [Option]`: Toggle selinux.  Use when troubleshooting permissions.  This command is not persistent.
  - Options: `[ Enforcing | Permissive | 1 | 0 ]`

### 4.4.4. `rsyslog (optional)`

> The 'rocket-fast system for log processing' is installed by default and can send syslog messages to remote systems.

1. Define and load modules `# vi /etc/rsyslog.conf`.

```rsyslog1
...
#### MODULES ####
module(load="imfile"
       PollingInterval="5")

 input(type="imfile"
       File="/opt/zeek/logs/current/conn.log"
      Tag="zeek_conn"
      Severity="debug"
      Facility="local6")

 input(type="imfile"
      File="/opt/zeek/logs/current/dns.log"
      Tag="zeek_dns"
      Severity="debug"
      Facility="local6")
...
```

2. Configure rsyslog to send logs to a remote IP4 node `# vi /etc/rsyslog.conf`.

> Replace '10.189.XX.XXX' with the address of the remote syslog server and designate tcp vs. udp with `@`.

​		1. TCP, port 514: `@@`.

```rsyslog2
### rsyslog config ###
*.*     @@10.189.XX.XXX:514
```

​		2. UDP, port 514:`@`.

```rsyslog2
### rsyslog config ###
*.*     @10.189.XX.XXX:514
```

### 4.4.5. `zeekctl cron`

> Add to `zeekctl cron` to crontab for automatic recovery of crashed nodes.

1. `$crontab -e`

```crontab1
*/5 * * * * /opt/zeek/bin/zeekctl cron
```

### 4.4.6. Install `Zeek Package Manager(zkg)`

> Zeek 4.0LTS has zkg included.  There is only a need to install manually on versions earlier than 4.0LTS.  Skip this step if your current version 4.0 or later.

1. Install using python pip.

```package manager
$pip3 install --user zeek zkg && zkg autoconfig
```

2. Ensure correct permissions persisted.

```chown
#chown -R zeek:zeek /opt/zeek
```

### 4.4.7. Install `AF_Packet` plugin

> Ideally, this plugin is installed via the package manager (zkg).  However, the plugin was written to detect `kernel_headers` in a non-CentOS8 default location (`/usr/src/kernels`).  Therefore, we will define the location during a manual installation.
>
> Additonally, the master repository for this plugin has been updated to support Zeek 4.0LTS which has broken the ability to make and install for Zeek version `3.0.13LTS and under`.  Participants who are running Zeek 4.0LTS can install the plugin using zeek package manager.

1. Zeek versions before 4.0LTS:

   1. Stop Zeek if it is started: `$zeekctl stop`.
   2. Delete previous versions of `AF_Packet`:

   - `$rm -rf /opt/zeek/lib/zeek/plugins/Zeek_AF_Packet/`

   3. Go to the zeek home directory:

   - `$cd /home/zeek`

   4. Download the plugin for Zeek 3.0.13LTS and below:

   - Navigate from a web browser to the [JSP Portal](https://jspportal.canarie.ca) and Log in.
   - Download the following file and upload onto the server: [af_packet-for-zeek_3.0.1.3.master.zip](https://jspportal.canarie.ca/wpc_downloader/core/?wpc_action=download&id=189) to the following location `$/home/zeek/`.

​	Unzip the plugin and enter the new directory

``` unzip
$unzip  af_packet-for-zeek_3.0.1.3.master.zip && cd zeek-af_packet-plugin-master
```

​			4. Install the plugin, defining kernel location.

```af_packet
$./configure --with-kernel=/usr/src/kernels && make && make install
```

2. Zeek versions after 4.0LTS:

   1. Stop Zeek if it is started `zeekctl stop`.
   2. Delete previous versions of `AF_Packet`:

   - `$rm -rf /opt/zeek/lib/zeek/plugins/Zeek_AF_Packet/`

   3. Use Zeek's Package manager to install af_packet

   - `$zkg install zeek/j-gras/zeek-af_packet-plugin`

3. Re-set permissions:

- `	#chown -R zeek:zeek /opt/zeek ` 
- `#setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats`

### 4.4.8. Install `ADD_INTERFACES` plugin

> Adds cluster node interface to logs. Useful when sniffing multiple interfaces to identify source of log.

1. Stop Zeek if it is started `zeekctl stop`.
2. Install the plugin.

- `$zkg install j-gras/add-interfaces`

3. Edit config file and modify two lines `$vi /opt/zeek/share/zeek/site/packages/add-interfaces/add-interfaces.zeek`.

4. Change the `...enable_all_logs = F...` to `...enable_all_logs = T... ` & Remove text inside the curly brackets in `...include_logs: set[Log::ID] = { }...` .

```add int
export {
        ## Enables interfaces for all active streams
        const enable_all_logs = T &redef;
        ## Streams not to add interfaces for
        const exclude_logs: set[Log::ID] = { } &redef;
        ## Streams to add interfaces for
        const include_logs: set[Log::ID] = { } &redef;
		}
```

### 4.4.9. Setup Sniffing Interfaces

> Configure on all sniffing interface(s) that will be receiving tapped, spanned or mirrored traffic.  Do not apply this configuration to management interface(s).

1. Get interface names using `#ip addr`  or  `#ls /etc/sysconfig/network-scripts/`.

   > Physical interfaces are listed like `ifcfg-<Interface name>`.

2. Use `#ethtool -g <interface name>` to get the maximum ring parameter.

> Here, the maximum ring parameter is `2047`.

```sniffing_interfaces
#ethtool -g eno2
...
Pre-set maximums:
RX:  2047
...
```

3. For each each sniffing interface `#vi /etc/sysconfig/network-scripts/ifcfg-<interface name>`.
   1. Apply the following:

> You will have to add the `ETHTOOL_OPTS` and `NM_CONTROLLED` lines and `ONBOOT=yes`.  Notice that previous recorded value `2047` exists in `ETHTOOL_OPTS` options.  This value should reflect that of the interface(s) you are using for sniffing.

```sniff_config
...
DEFROUTE=no
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=no
IPV6_DEFROUTE=no
IPV6_FAILURE_FATAL=no
NAME=eno2
DEVICE=eno2
ONBOOT=yes
NM_CONTROLLED=no
ETHTOOL_OPTS="-G ${DEVICE} rx 2047; -K ${DEVICE} rx off; -K ${DEVICE} tx off; -K ${DEVICE} sg off; -K ${DEVICE} tso off; -K ${DEVICE} ufo off; -K ${DEVICE} gso off; -K ${DEVICE} gro off; -K ${DEVICE} lro off"
...
```
4. Set sniffing interfaces into promiscuous mode.

> Create a file called `/etc/systemd/system/promisc.service` and populate it appropriately. Where each sniffing interface is defined under `[Service]`.

​		1. Single Interface.

> Where `eno2` is the sniffing interface.

```sniff
[Service]

[Unit]
Description=Bring up an interface in promiscuous mode during boot
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ip link set dev eno2 promisc on
TimeoutStartSec=0
RemainAfterExit=yes

[Install]
WantedBy=default.target

```

​		2. Multiple Interfaces.

> Where `eno1` & `eno2` are sniffing interfaces.

```multiple
...
[Service]
...
ExecStart=/usr/sbin/ip link set dev eno1 promisc on
ExecStart=/usr/sbin/ip link set dev eno2 promisc on
...
```

5. Make the changes permanent and start on boot.

```promisc
# chmod u+x /etc/systemd/system/promisc.service
# systemctl start promisc.service
# systemctl enable promisc.service
```
6. Enable `network` service and restart it.

```enable network service
#systemctl enable network && systemctl restart network
```

7. Verify before and after rebooting.

> You should see that `PROMISC` exists in the options for the sniffing interface(s).

```ip a show | grep permisc
$ ip a | grep PROMISC
3: eno2: <BROADCAST,MULTICAST,PROMISC,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
```

### 4.4.10. Configuration Files

> Environment specific information is defined in configuration files.  Zeek reads these files upon `zeekctl start` and  `zeekctl deploy`.  

- When configuration files are modified, run `$zeekctl deploy`.

#### 4.4.10.1. `networks.cfg`

> Add known, or local, networks.  Edit these files as the `zeek` user.
>
> Include your organization's public networks.

1. `$vi /opt/zeek/etc/networks.cfg`

```networks.cfg
# List of local networks in CIDR notation, optionally followed by a
# descriptive tag.
# For example, "10.0.0.0/8" or "fe80::/64" are valid prefixes.

10.0.0.0/8      		User Workstations
172.16.0.0/12       	Server Farm
192.168.0.0/16      	DMZ
```

#### 4.4.10.2. `node.cfg`

> Define af_packet parameters (interfaces) and dedicate cpu threads to nodes.

- Configure assign interfaces and AF_PACKET parameters to workers.
- Assign workers to interfaces.
- Pin core(s) to cluster elements, such as workers, to ensure dedicated compute resources.  
  - The general rule is 250MiB throughput per thread.
    - Well distributed load makes more efficient work.

1. Fet the `processors` and `core ids`of the server.  Use the distribution of processors to core for similar distribution between workers.

```awksed
$awk '/core id/ || /processor/' /proc/cpuinfo | sed 'N;s/\n/\t/'
...
processor       : 0     core id         : 0
processor       : 1     core id         : 4
...
processor       : 39    core id         : 26
```

2. Write configuration `vi /opt/zeek/etc/node.cfg`.

> Workers: The fastest memory and CPU core speed you can afford is recommended since all of the protocol parsing and most analysis will take place here.

##### 2a. **JSP2 Hardware**: Two workers sharing one interface:

- 20 threads per worker
  - Theoretical throughput: (Megabit) = (#Threads)(250) 
    - 20*250=5Gigabit per worker
- Threads reserved for system: 14, 15, 18, 19, 38, 39 42, 43

```jsp2
...
[logger]
type=logger
host=localhost

[manager]
type=manager
host=localhost

[proxy-1]
type=proxy
host=localhost

[worker-1]
type=worker
host=localhost
interface=af_packet::eno2
lb_method=custom
lb_procs=20
pin_cpus=0,1,2,3,4,5,6,7,8,9,10,11,12,13,16,17,20,21,22,23
af_packet_fanout_id=2

[worker-2]
type=worker
host=localhost
interface=af_packet::eno2
lb_method=custom
lb_procs=20
pin_cpus=24,25,26,27,28,29,30,31,32,33,34,35,36,37,40,41,44,45,46,47
af_packet_fanout_id=3
```



##### 2b. **JSP2 Hardware**: Three workers, each with their own interface:

- 15 threads per worker
  - Theoretical throughput: (Megabit) = (#Threads)(250) 
    - 15*250=3.75Gigabit per worker
- Threads reserved for system: 14, 15, 38, 39

```jsp2
...
[logger]
type=logger
host=localhost

[manager]
type=manager
host=localhost

[proxy-1]
type=proxy
host=localhost

[worker-1]
type=worker
host=localhost
interface=af_packet::eno2
lb_method=custom
lb_procs=15
pin_cpus=0,1,4,5,8,9,10,11,24,25,28,29,32,33,34
af_packet_fanout_id=2

[worker-2]
type=worker
host=localhost
interface=af_packet::ens1f0np0
lb_method=custom
lb_procs=15
pin_cpus=2,3,6,7,12,13,16,17,26,27,30,31,35,36,37
af_packet_fanout_id=3

[worker-3]
type=worker
host=localhost
interface=af_packet::ens1f1np1
lb_method=custom
lb_procs=15
pin_cpus=18,19,20,21,22,23,40,41,44,45,46,47,42,43,44
af_packet_fanout_id=4
```



##### 2c. JSP1 Hardware: Two workers sharing one interface:

- 18 threads per worker
  - Theoretical throughput: (Megabit) = (#Threads)(250) 
    - 18*250=4.5Gigabit per worker
- Threads reserved for system: 36, 37, 38, 39

```jsp2
...
[logger]
type=logger
host=localhost

[manager]
type=manager
host=localhost

[proxy-1]
type=proxy
host=localhost

[worker-1]
type=worker
host=localhost
interface=af_packet::eno2
lb_method=custom
lb_procs=18
pin_cpus=0,2,4,6,8,10,12,14,16,18,20,22,24,26,28,30,32,34
af_packet_fanout_id=2

[worker-2]
type=worker
host=localhost
interface=af_packet::eno2
lb_method=custom
lb_procs=18
pin_cpus=1,3,5,7,9,11,13,15,17,19,21,23,25,27,29,31,33,35
af_packet_fanout_id=3
```

#### 4.4.10.3. `zeekctl.cfg`

> Define `mail` and `logging` parameters

- Adjust per environment.

1. Edit `MailTo` parameter for use by sendmail in sending reports.

```
$vi /opt/zeek/etc/zeekctl.cfg
...
# Mail Options
# Recipient address for all emails sent out by Zeek and ZeekControl.
MailTo = security@your-org.ca
...
```

#### 4.4.10.4.  Enable `unknown_protocols.log` (optional)

> Log packet protocols that aren't supported by Zeek.  Will result in a file called `unknown_protocols.log`.

- `$echo "@load misc/unknown-protocols" >> /opt/zeek/share/zeek/site/local.zeek`

#### 4.4.10.4.  Enable common plugins (optional)

- `$ zkg install zeek/salesforce/ja3`
  - JA3 creates 32 character SSL client fingerprints and logs them as a field in ssl.log
- `$zkg install zeek/salesforce/hassh`
  - HASSH is used to identify specific Client and Server SSH implementations.

### 4.4.11.1 File Transfer

- Participants should be prepared to provide the public address that will access the remote (concordia) server.

1. From the server run `$curl ifconfig.me`.

```ifcfg.me
$curl ifconfig.me
205.XXX.XXX.XXX
```

2. Generate ssh-keys: `$ssh-keygen -t rsa -b 4096`.

> - Passwordless authentication requires key exchange.  This is necessary for automation.

```generate keys
$ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (/home/zeek/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Your identification has been saved in /home/zeek/.ssh/id_rsa.
Your public key has been saved in /home/zeek/.ssh/id_rsa.pub.
The key fingerprint is:
...
The key's randomart image is:
+---[RSA 4096]----+
...
+----[SHA256]-----+
[zeek@zeek01 .ssh]$
```

3. Copy the contents of  your new public key:  `$cat ~/.ssh/id_rsa.pub`.
4. Contact Mouatez Karbab via email `elmouatez.karbab@concordia.ca` or via the JSP Slack channel (jsp-tech) for registration and access to remote server. 
   - Include your ssh public key of your zeek server.
   - Include the public IP of your server.
   - Include the public IP address(s) of workstations that will be accessing the portal (GUI).
   - Concordia will reply with the participant's username for access to the server and credentials for accessing the web interface "https://jointsecurity.ca/".
5. Test connectivity `$ssh -p 56320 username@push.jointsecurity.ca`.
   - Connectivity to the remote server should be established:

```test ssh
$ ssh -p 56320 username@push.jointsecurity.ca
Welcome
...
username@feedserver:~$
```



#### 4.4.11.2. `rsync`

> Synchronizes files between two systems and only copies what is different.

- Faster and more efficient when compared to SCP.
- Only copies what is different.
- Can use compression.
- Can operate over ssh protocol with all of the benefits.
- Can force hash checking.

> - *Local*:  JSP Server where logs reside in `/opt/zeek/logs/`.
> - *Remote*: system which will receive logs.

1. Install `rsync`.

> Included in section **`3.9 Repositories & Dependencies`.**  This step is only necessary if the application is not already installed.

```install rsync
#yum -y install rsync
...
Complete!
```

2. Transfer files.

> Create an `rsync` script and add it to the crontab of the zeek user.

a) Create a script and populate it:`$vi rsync.sh`.

> The `rsync` command in this script can be tuned to the institution's preference in terms of what files are uploaded.  This script does not upload files that may contain  personally identifiable information (PII).

``` rsync.bat
rootDir="/opt/zeek/logs/"
for day in 0 1 2 3 4 5 6 7
do
    dirName="${rootDir}"$(date  --date=${day}" days ago" +%Y-%m-%d);
    echo $dirName;

# Upload all zeek logs except those that may contain PII.
rsync -av --progress -e "ssh -p 56320" --exclude={"http.*.log.gz","ftp.*.log.gz","ntlm.*.log.gz","irc.*.log.gz","sip.*.log.gz","radius.*.log.gz","smtp.*.log.gz","rdp.*.log.gz","files.*.log.gz","syslog.*.log.gz","snmp.*.log.gz",stats,current} $dirName username@push.jointsecurity.ca:

done
```

b) make the script executable:
`$chmod +x rsync.sh`

c) Automate transfer of files with crontab: 

`$ crontab -e`

```rsync_crontab_b
# Sync Zeek logs every hour.
0 * * * * /usr/bin/sh /path_to_file/rsync.sh
```


### 4.4.12. Log Retention and Collection

- Zeek automatically rotates and archives runtime logs from `current`into `/opt/zeek/logs/yyyy-mm-dd/`.  
- Rotate logs from`/opt/zeek/logs`. 
- Avoid rotating logs from `/opt/zeek/spool` .

1. Modify defaults by editing `/opt/zeek/etc/zeekctl.cfg`. 

> `LogRotationInterval` is measured in seconds.  The default value `3600`, or one hour.
>
> `LogExpireInterval` is measured in days.  `30` days is the recommended value.

```node.cfg
$vi /opt/zeek/etc/zeekctl.cfg
...
LogRotationInterval = 3600
...
LogExpireInterval = 30
...
```

### 4.4.13. Archive Logs to Remote System

> Some participants may have an archive policy which may require more space than is available on the server or could simply require off-site backups.  In this example, we will archive logs greater than 90 days old.

> Please note that file transfer methods described in section `4.3.11.2 rsync` may also suffice for this use case.

- The value of `LogExpireInterval` in `zeekctl.cfg` should be smaller than the `mtime` value in the script.   Else, there is a risk of data loss.
- Participants can replace `<NFS_SHARE>` with an appropriate reference to a remote system.

```archive
$ crontab –e
...
#Archive Zeek logs
@daily  find /opt/zeek/logs/20* -type d -mtime +90 -print0 | xargs -r0 mv -rf -t <NFS_SHARE>
...
```

### 4.4.14. Start Zeek with system

> To start Zeek when the operating system starts, create a file and place it into `/etc/systemd/system` .

1. Create a file called `/etc/systemd/system/zeek.service` and populate it as follows:

`# cd /etc/systemd/system && vi zeek.service`

```zeek.service
[Unit]
Description=Zeek
After=network.target

[Service]
ExecStartPre=/opt/zeek/bin/zeekctl cleanup
ExecStartPre=/opt/zeek/bin/zeekctl check
ExecStartPre=/opt/zeek/bin/zeekctl install
ExecStart=/opt/zeek/bin/zeekctl start
ExecStop=/opt/zeek/bin/zeekctl stop
RestartSec=10s
Type=oneshot
RemainAfterExit=yes
TimeoutStopSec=600
User=zeek
Group=zeek

[Install]
WantedBy=multi-user.target
```

2. Make the file executable, `start` the service and `enable` it.

```zeek.service
#chmod u+x /etc/systemd/system/zeek.service
#systemctl start zeek.service
#systemctl enable zeek.service
```

<div style="page-break-after: always; break-after: page;"></div>

# 5. Deploy and Troubleshoot

> A list of commands that need to be re-applied when modifications to the system have been applied and the system doesn't operate as expected.
>
> Further information can be found in the CANARIE FAQ file, [located in the JSP portal](https://jspportal.canarie.ca).

1. Deploy Zeek `zeekctl deploy` to apply config modifications and run Zeek.

> If you encounter errors, follow step `5.3 Give the Zeek application permission to capture packets`.

```zeekctl deploy
$zeekctl deploy
```

2. Make sure the `zeek` user and group have correct permissions.

> New applications and system modifications can result in a change of permissions.  The `zeek` user and group should maintain control of `/opt/zeek`.

```chown -R zeek:zeek /opt/zeek
#chown -R zeek:zeek /opt/zeek
```

3. Give the Zeek application permission to capture packets.

```
#setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
```

4. Add Zeek binaries to path.

```add zeek to path
#echo "pathmunge /opt/zeek/bin" > /etc/profile.d/zeek.sh
```

5. Confirm path update with `which` command.

> If the command returns `/opt/zeek/bin/zeek`, your path has been updated.


```
$which zeek
/opt/zeek/bin/zeek
```



# 6. Use Cases

a) Ensure that zkg packages are being loaded `vi /opt/zeek/share/zeek/site/local.zeek`

```
# Uncomment this to source zkg's package state
@load packages
```

b) Refresh zeek package manager repository`$ zkg refresh`

c) Keep packages updated `$ zkg upgrade`

### 6.1 Detect log4j: CVE-2021-44228

#### 6.1.1 Manual detection

- Useful for detection before plugins and IOC's were available.  
- Can occur on raw log files from analytics platforms or zeek CLI.
- Useful for detecting past events.
- Search `http.log` for `${`.

1. Past events :

```
$zcat -r /opt/zeek/logs/*/http*.log.gz | grep "\${"
```

2. Real time:

```
$cat intel.log | grep "\${"
```



#### 6.1.2 Corelight Plugin: CVE-2021-44228

> A Zeek package which raises notices, tags HTTP connections and generates (CVE-2021-44228) attempts.

- https://github.com/corelight/cve-2021-44228
- Writes 3 distinct notices to `notice.log`:
  1. `LOG4J_ATTEMPT_HEADER`
  2. `LOG4J_LDAP_JAVA`
  3. `LOG4J_JAVA_CLASS_DOWNLOAD`

- New log file called `log4j.log`.

1. Refresh zeek package manager repository`$zkg refresh`
2. Install the package `$zkg install cve-2021-44228`

```
[zeek@zeek02 ~]$ zkg install cve-2021-44228
The following packages will be INSTALLED:
  zeek/corelight/cve-2021-44228 (v0.5.1)

Proceed? [Y/n] Y
Installing "zeek/corelight/cve-2021-44228".
Installed "zeek/corelight/cve-2021-44228" (v0.5.1)
Loaded "zeek/corelight/cve-2021-44228"
```

3. Deploy the plugin `$zeekctl deploy`.
4. Keep the package updated `$zkg upgrade cve-2021-44228`

### 6.2 Threat Feeds

> This is a public feed based on Public Threat Feeds and 'critical path security' gathered data.  Zeek compares connections with elements defined in the feed(s) and writes matches to intel.log.

- https://github.com/CriticalPathSecurity/Zeek-Intelligence-Feeds.
- Several sources for IOC's to be used with the Zeek intel framework.
- New log file called `intel.log`.

1. Clone from GitHub `$git clone https://github.com/CriticalPathSecurity/Zeek-Intelligence-Feeds.git /opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds`.
2. Enable loading of plugin `echo "@load Zeek-Intelligence-Feeds" >> /opt/zeek/share/zeek/site/local.zeek `.
3. Keep the IOC's updated

> This process will automate the updating of the various threat feeds.

a)  `$vi /home/zeek/zeek_update_intel-feeds.sh`.

```
#!/bin/sh

cd /opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds && git fetch origin master
git reset --hard FETCH_HEAD
git clean -df

#fix references for IDS participants
sed -i 's+/usr/local/+/opt/+g' /opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds/main.zeek
```

b) Make the new script executable: `$chmod +x /home/zeek/zeek_update_intel-feeds.sh`.

c) Include or exclude feeds `$vi /opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds/main.zeek`

> Critical path security has many intelligence feeds.  Participants may choose to enable or disable feeds depending on requirements or IDS placement.  Feeds can be excluded by commenting them out with `#`.

```
##! Load Intel Framework
@load policy/integration/collective-intel
@load policy/frameworks/intel/seen
@load policy/frameworks/intel/do_notice
redef Intel::read_files += {
...
#        "/opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds/cps_cobaltstrike_domain.intel",
        "/opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds/log4j_ip.intel",
#       "/opt/zeek/share/zeek/site/Zeek-Intelligence-Feeds/predict_intel.intel",
...
};
```

d) Add the script to crontab for execution every 5 minutes: `$crontab -e`.

```
5 * * * * sh /home/zeek/zeek_update_intel-feeds.sh >/dev/null 2>&1
```

e) Re-Propagate Zeek permissions to the Zeek folder and Capture packet functionality (as Root)

`
#chown -R zeek:zeek /opt/zeek"
#setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
`

f) Deploy the plugin `$zeekctl deploy`.



### 6.3 Write logs to TSV & JSON

- Simultaneously write log files in both TSV and JSON formats

- Allows participants to use add-ons that utilize JSON logging format:

  - Splunk forwarder
  - Logstash
  - Filebeat

- Each log will have a prefix of `json_streaming_`.

- Rotates logs within `/opt/zeek/logs/current` directory so the log shipper has time to catch up.

  ```
  $ls json_streaming_ftp*
  json_streaming_ftp.1.log
  json_streaming_ftp.2.log
  json_streaming_ftp.3.log
  json_streaming_ftp.4.log
  json_streaming_ftp.log
  ```

- Compressed and archived per the value of `LogRotationInterval` in  `/opt/zeek/etc/zeekctl.cfg`

1. Install the plugin
   - `$zkg install zeek/corelight/json-streaming-logs`

2. Ensure `@load packages` is not commented out in `/opt/zeek/share/zeek/site/local.zeek`
3. Deploy Zeek `#zeekctl deploy`.
   1. Verify: `$ls /opt/zeek/logs/current | grep json_streaming_ `
4. Duplicate data:
   - Both `TSV` & `JSON` saved and rotated resulting in duplicate data
   - Consider managing disk space
   - Routinely delete redundant data
5. Exclude `JSON` from upload to analytics platform `$vi /home/zeek/rsync.sh`.
   - add `json_streaming_*` to exclusion list

```
...
rsync -av --progress -e "ssh -p 56320" --exclude={"http.*.log.gz","ftp.*.log.gz","ntlm.*.log.gz","irc.*.log.gz","sip.*.log.gz","radius.*.log.gz","smtp.*.log.gz","rdp.*.log.gz","files.*.log.gz","syslog.*.log.gz","snmp.*.log.gz","json_streaming_*",stats,current} $dirName username@push.canids.ca:
...
```

6. When not in use, unload plugin to save disk space.
   1. `$zkg unload json-streaming-logs`.
   2. Deploy zeek:`$zkg deploy`.
   3. Verify

```
[zeek@zeek02 ~]$ zkg list unloaded
zeek/corelight/json-streaming-logs (installed: v3.0.0) - JSON streaming logs
zeek/hosom/file-extraction (installed: 2.0.3) - Extract files from network traffic with Zeek.
```



### 6.4 Examine offline packet traces

> Use Zeek to process captured packet traces. 

#### 1. Capture a packet trace

##### 1. Microsoft Windows: `Wireshark`

1. [Download](https://www.wireshark.org/download.html) and install `Wireshark`.

2. Start `Wireshark` and select interface.

3. Accumulate data.

4. Save the data.



##### 2. Linux (CentOS_Stream): tcpdump

1. Install `tcpdump`:

`#yum -y install tcpdump`

2. Capture packet options:


```
sudo tcpdump -i eno1 -c 100 -s 65535 -w 100-packets.trace
sudo tcpdump -i eno1 -s 0 -w packet.trace
```

- `eno1` should be replaced by the correct interface for your system, for example as shown by the `$ifconfig` command. 
- `-c 100` designates how many packets to capture.  If not defined you must `ctrl+c` to interrupt `tcpdump` and hault data accumulation.
- `-s 0` capture whole packets. 
  - If not supported use `-s 65535`.

#### 2. Upload the packet trace to your Zeek server

##### 1. Microsoft Windows

- Many software options

1. Download and Install `Filezilla`
2. Connect to Zeek server
3. Place packet trace file into `/home/zeek/investigation`

##### 3. Linux

- Use `sftp` to transfer between Linux operating systems.

```
$ sftp zeek@192.168.0.1
...
Connected to zeek@192.168.0.1.
sftp> put /source_dir/packet.trace /home/zeek/investigation/
...
sftp> quit
```

#### 3. Use Zeek to examine packet trace

- Zeek will output log files into the working directory.
- Zeek default analysis:
  - Does not load plugins defined in `/opt/zeek/share/zeek/site/local.zeek`.

```
zeek -r mypackets.trace
```

- Zeek analysis with `local`:
  - Loads plugins defined in `/opt/zeek/share/zeek/site/local.zeek`.

```
zeek -r mypackets.trace local
```

Running with specific plugins:

```
zeek -r mypackets.trace frameworks/files/extract-all-files
```

- Verify log files and start digging!



# 7. IDS Analytics Platforms

> Instructions and contacts for access and support to analytics platforms.

### 7.1. Concordia University

- Technical Contact: Mouatez Karbab
  - Email: elmouatez.karbab@concordia.ca

1. Email `elmouatez.karbab@concordia.ca` 
   - Include your ssh public key of your zeek server (Section `4.3.11.1 File Transfer `).
   - Include the public IP of your server (`$curl ifconfig.me` from zeek server).
   - Include the public IP ranges that will be accessing the portal.

2. An email will be returned with credentials for portal access. 

3. Navigate to `https://jointsecurity.ca/` to log into the portal

### 7.2. McMaster University

- Technical Contact: Badawy, Ghada
  - Email: badawyg@mcmaster.ca

1. Navigate to `jspportal.canarie.ca` and log in.
2. Scroll to section`2. McMaster JSP Application`.

>  Here you will find the canids `application files` and `Platform Deployment Manuals`.

### 7.3. Waterloo University

- Technical Contact: Hauton Psang
  - Email: [hauton.tsang@uwaterloo.ca](mailto:hauton.tsang@uwaterloo.ca)

1. Email list of IP ranges that will access the platform to ujsp@uwaterloo.ca.
2. An email will be returned with credentials for portal access. 
3. Navigate to `https://cloud.jsp.cs.uwaterloo.ca/` to log into the portal.



#### -- End -- 
