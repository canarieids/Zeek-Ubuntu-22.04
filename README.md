
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

- Steps that start with `(Root)` are expected to be RUN AS the 'root' user.
- Steps that start with `(Zeek)` are expected to be run as the `zeek` user.

## 1.1 Community Resources

>  The Zeek community is vibrant, friendly, and easily accessible.  Use the following resources for communication and documentation questions that fall outside of this document and/or project.

  - [Zeek](https://www.zeek.org/)[ Website](https://www.zeek.org/)
  - [Zeek Manual](https://docs.zeek.org/en/master/)
  - [Zeek](https://zeek.org/connect/)[ Connect](https://zeek.org/connect/)
  - [Zeek](https://zeek.org/blog/)[ Blog](https://zeek.org/blog/)

## 1.2 JSP FAQ

> We have composed a frequently asked questions file, which is located in the JSP portal.  Both pilot and phase 2 participants will find this file useful.

- [Zeek / JSP - FAQ](https://idsportal.canarie.ca/wpc_downloader/core/?wpc_action=view&id=215) 

## 1.3 Warranty and Contacts

> **Network TAPs:**

Warranties are with the equipment vendor (Gigamon/IXIA) and participants can pursue requests.  Ensure you have the `serial number` prior to initiating a service request.

> **Server:**

Participants can contact Dell directly for warranty & customer support for hardware issues and troubleshooting.  Ensure you have the `service tag` prior to initiating a service request.

<div style="text-align:center">    
  <a href="https://www.dell.com/support/incidents-online/ca/en/04/contactus/dynamic">Dell Onlone Customer Service</a> 
</div>
<div style="page-break-after: always; break-after: page;"></div>


# 2. Disk Configuration

## 2.1 RAID Options for JSP Phase 1

| Level | Min. # Disks | Max Capacity | Advantages | Disadvantages                                                |
| ----- | ---------- | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0     |      1       | 4TB          | •High performance.<br />•Easy to implement.<br />•Highly efficient (no parity overhead). | •No redundancy.<br />•Limited business use cases due to no  fault tolerance. |
| 1     |      2       | 2TB          | •Fault tolerant.  <br />•Easy to recover data in case of drive  failure  •Easy to implement. | •Highly inefficient (100% parity  overhead).<br/> •Not scalable (becomes very costly as  number of disks increase). |

## 2.2 RAID Options for JSP Phase 2

| Level | Min. # Disks | Max Capacity | Advantages                                                   | Disadvantages                                                |
| ----- | ------------ | ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 0     | 1            | 4TB          | •High performance.<br />•Easy to implement.<br />•Highly efficient (no parity overhead). | •No redundancy.<br />•Limited business use cases due to no  fault tolerance. |
| 5     | 3            | 3TB          | •Fault tolerant.<br />•High efficiency.                      | •Disk failure has a medium impact on  throughput.<br />•Complex controller design. |
| 6     | 4            | 2TB          | •Fault tolerant – increased redundancy  over RAID 5.<br />•High efficiency. | •Write performance penalty over RAID 5.<br />•More expensive than RAID 5. <br/>•Disk failure has a medium impact on  throughput. |
| 10    | 4            | 2TB          | •Extremely high fault tolerance.<br />•Very high performance.  <br/>•Faster rebuild performance than 0+1. | •Very Expensive. <br />•High Overhead.<br />•Limited scalability. |

## 2.3 Configure Disk(s)

<div style="text-align:center">    
  <a href="https://www.dell.com/support/manuals/ca/en/cabsdt1/poweredge-r440/idrac_3.30.30.30_lc_ug/configuring-raid?guid=guid-a86b35a2-03a6-4838-952f-24992a99998e&lang=en-us">Dell PowerEdge R440 RAID Configuration Guide</a> 
</div>

> Recommended configuration:
 - The SSD drive should be configured as a NON-RAID Disk
 - Prioritize support for high IOPS is a RAID 10.
 - Priortize support for high capacity (retention) is a RAID 5.

## 2.4. Prepare USB Key

1. Download the ISO for Ubuntu 22.x LTS
> Download the latest version of Ubuntu 22.x LTS server
> Click Here: [Ubuntu Server Download page](https://ubuntu.com/download/server).

2. Download and Install an ISO to USB utility
> Download, install and run a USB creation tool.  Rufus is tool used in this document.
> Rufus [Rufus](https://rufus.ie/).

3. Create bootable USB key
	Follow these steps with Rufus to build a USB key from the Ubuntu ISO
	- Open 'Rufus'
	- Under 'Device' dropdown, choose target USB key.
	- 'Select' downloaded ISO.
	- Navigate for and Choose ISO file.
	- Select 'Open'.
	- Click 'START' to begin writing the ISO.

![image-20200505231623160](/images/image-20200505231623160.png)

## 2.5. Virtual Deployment

> If physical access to the server is not possible, you can virtually mount an image. It is recommended to install the Operating System with a USB key that is connected to one of the server’s USB ports [Click here for more information.](https://www.dell.com/support/article/ca/en/cabsdt1/sln296648/using-the-virtual-media-function-on-idrac-6-7-8-and-9?lang=en)

1. Click `Configuration`.
2. Select `Connect Virtual Media`.
3. Navigate for and select the ISO file.
4. Click `Open`.

![image-20200505232027969](/images/image-20200505232027969.png)

## 2.6. Disk Configuration

1. To start the Lifecycle Controller, press `F10`.

![image-20200506143343112](/images/image-20200506143343112.png)

2. Select `Configure RAID and Deploy and Operating System`.

<img src="/images/image-20200506143424102.png" alt="image-20200506143424102" style="zoom:90%;" />

3. Select `Configure RAID First` then click `Next`.

![image-20200506143525285](/images/image-20200506143525285.png)

4.  Click `Next`.

> Do not select the SSD. We mount this drive later under '/opt/zeek/spool'.

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

#### Hardening the Server

> Apply security controls relevant to your organization's policy.  The following guidelines are posted on the on JSP Portal in the technical resources section:

Coming Soon

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

![image-keyboardlayout](/images/image-keyboardlayout.PNG)

## 3.2. Choose base installation

> Change the base installation from `Ubuntu Server` to `Ubuntu Server (minimized)`. 

1. Select `Ubuntu Server (minimized)`.

![image-20200506160026472](/images/image-typeofinstall-minimal.png)

## 3.3. Configure initial Networking

> Configure your management interface networking. One network card will be dedicated to allow administrators to SSH into the system. This same interface will also allow egress for SYSLOG connections.

![image-20200506160026472](/images/image-networkwizarsetup.png)

> Configure your proxy (if applicable).  

![image-20200506160026472](/images/image-configureproxy.png)

> Configure the Archive Mirror

![image-20200506160026472](/images/image-archivemirror.png)

## 3.4. Storage Layout
> The guided storage layout provides us the option to customize where we install Ubuntu. It is advisable to install Ubuntu on your redundant storage volume (RAID10 or RAID5). The single SSD drive will be used for swap and working folders.

1. Choose the option `Custom Storage Layout`.

![image-20200506160918064](/images/image-storagelayout1.png)

2. Configure Partitions

> Allocate the following capacity to these Partitions:

```
/home					3.0G
/					1.813T
/boot					1.0G
/boot/efi				1.0G
/opt/zeek/spool				local disk (SSD)
```

> Once completed your layout should look like this:

![image-20200506160918064](/images/image-storagelayout2.png)

> Confirm the storage

![image-20200506160918064](/images/image-storagelayout3.png)

## 3.5. Profile Setup

>Complete your administrator profile set up.

![image-20200506160918064](/images/image-userprofile.png)

## 3.6 Install OpenSSH

>Install OpenSSH server. This will allow you to administrate the server via SSH.  
>Optionally, use certificate based authentication.

![image-20200506160918064](/images/image-installopenssh.png)

## 3.5 Featured Server Snaps

>Do not select any Featured server Snaps

![image-20200506160918064](/images/image-featuredserversnaps.png)


## 3.7. Begin the installation

1.  Confirm the destructive operation warning to start installation

   > This is the last chance to modify options before they are written to disk.

![image-20200506162722251](/images/image-preconfirminstalllaunch.png)

2. The installation will start

![image-20200506162722251](/images/image-installlaunch.png)

3. Once the installation has completed, Click 'Reboot Now'

![image-20200506162722251](/images/image-postinstallreboot.png)

## 3.8. Zeek User and Group

> Participants are encouraged to follow their own account policy. Later steps will outline the creation of a `zeek` user and group that will have access to `Zeek Application` functions and files.
> Complete the following steps steps to create Zeek user and group

1. Login with the account created during the OS deployment, in this example 'csadmin', which will be represented with "(Root)" in this guide. 
2. (Root)  Use the following example to create the Zeek User

```
sudo adduser zeek
```
> You will be prompted to choose a 'password' and define the account's Full Name, Room, Work Phone, Home Phone, Other.  These fields are optional.

3. (Root) Create a Zeek Group
```
sudo addgroup zeek
```

4. (Root) Add the Zeek user to the Zeek group
```
usermod -a -G zeek zeek
```

## 3.9. Repositories

>We need to add the Zeek repositories to your Ubuntu installation in order to receive the latest version and updates.  In the below example, substitute the '22.04' for your version of Ubuntu.

>(Root) Execute the following commands to add required respositories:
```
echo 'deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_22.04/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
```
>
```
curl -fsSL https://download.opensuse.org/repositories/security:zeek/xUbuntu_22.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
```

>(Root)  Confirm the repositories have been added
```
sudo apt update
```
>Confirm you do not receive any errors

## 3.10 Install Dependancies

>(Root) Install the required dependancies for Zeek

```
sudo apt-get install cmake make gcc g++ flex bison libpcap-dev libssl-dev python3 python3-dev swig zlib1g-dev
```
>(Root) Install optional dependancies for Zeek

```
sudo apt-get install python3-git python3-semantic-version
```

## 3.11 Configure interfaces for Promiscuous mode

Zeek requires the interfaces on which it will sniff traffic be configured into PROMISCUOUS mode. By configuring your interfaces into this mode, you are allowing the network interface to receive packets that would normally be discarded. Execute these commands as (Root)

1. (Root) Create a file called `/etc/systemd/system/promisc.service` 

```
vi /etc/systemd/system/promisc.service
```

2. Populate the file using the below examles. Where each sniffing interface is defined under `[Service]`.  This will require one line per interface.
   In the below example, we have four interfaces: `ens2f1` , `ens2f2`, `ens2f3` and `ens2f4` configured to be in promiscuous mode. 
   
   Zeek will be able to use these as sniffing interfaces.  Substitute these interface names from your environment.

```multiple
[Service]

[Unit]
Description=Bring up an interface in promiscuous mode during boot
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ip link set dev ens2f1 promisc on
ExecStart=/usr/sbin/ip link set dev ens2f2 promisc on
ExecStart=/usr/sbin/ip link set dev ens2f3 promisc on
ExecStart=/usr/sbin/ip link set dev ens2f4 promisc on

TimeoutStartSec=0
RemainAfterExit=yes

[Install]
WantedBy=default.target

```

Save your changes (:wq!)

3. (Root) Make the changes permanent and start on boot.

```
 chmod u+x /etc/systemd/system/promisc.service
```

```
systemctl start promisc.service
```
```
systemctl enable promisc.service
```
4. (Root) Enable `network` service and restart it.

```enable network service
sudo systemctl restart systemd-networkd
```

5. Confirm Pre-Reboot

> You should see that `PROMISC` exists in the options for the sniffing interface(s).

```ip a show | grep permisc
ip a | grep PROMISC
...
ens2f1: <BROADCAST,MULTICAST,###PROMISC###,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
ens2f2: <BROADCAST,MULTICAST,###PROMISC,UP###,LOWER_UP> mtu 1500 qdisc mq state UP 
...
```

6. Reboot your server and re-confirm Step 4 to ensure interfaces come up with the 'PROMISC' flag

## 3.12 Optimize Sniffing Interfaces

> Coming soon

## 3.13 Install CRONTAB
> Ubuntu 22.04 does not come with CRONTAB installed.  We will need this feature to be installed to schedule some recurring tasks with regards to Zeek and its plugins

(Root) Execute the following command to install Crontab

```
sudo apt-get -y install cron
```

# 4 Install and Configure Zeek 

## 4.1  New Install

> Zeek-lts is installed to the prefix `/opt/zeek` When installing from the official repository.

1. Use `apt` to install `zeek`.

>(Root) This will install Zeek 5.0X
```
sudo apt install zeek
```
>You will be prompted to optionally configure the Mail Server options. You can choose to do this now or later.

2. (Root) Add Zeek binary files to path for Zeek.  

Enter Root bash:
```
sudo bash
```
(Root) Execute the following command

```add zeek to path
echo "export PATH=/opt/zeek/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin" >> /etc/profile.d/zeek.sh
```

3.  Grant the Zeek user and group ownership and permissions

Execute the following command to recursively assign ownership to the zeek user and group

**(!) (Root) Bookmark this command for use in any troubleshooting**

```
chown -R zeek:zeek /opt/zeek
```

Execute the following command to give the zeek process permissions to read raw packet captures

**(!) (Root) Bookmark this command for use in any troubleshooting**

```
setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
```
4. Login as `zeek` to confirm the path entry is working.

```su - zeek
su - zeek
```
5. (Zeek) Confirm the path has been applied with the bash script

Execute the command 'which zeek' to query for the path.  You should receive the results: ''/opt/zeek/bin/zeek' .
If the command returns `/opt/zeek/bin/zeek`, your path has been updated successfully.

```
which zeek


...
/opt/zeek/bin/zeek
...
```
(Zeek) You should also be able to execute 'zeekctl' for any working directory
```
zeekctl

Welcome to ZeekControl 2.3.0
Type "help" for help.
[ZeekControl] >
```

6. Configure node.cfg

Zeek is initially configure to run in Standalone mode, which means it will only listen on **one interface**.  That single interface is defined in /opt/zeek/etc/node.cfg in the top section.

```
...
[zeek]
type=standalone
host=localhost
interface=eno1
...
```
You will need to modify the ***interface=*** and provide at least one valid interface for Zeek to bind.  You can get a list of your interfaces by running 'ip a' at the CLI.

If you wish to configure Zeek to listen to multiple interfaces simultaneously (highly recommended), you must configure Zeek to run in cluster mode.  For now, you can specify one interface, or jump to step 4.3.6 to configure node.cfg for cluster mode with multiple interfaces.  

Once you have completed configuration of Standalone or Cluster mode, proceed to the next step.

7. (Zeek) Deploy Zeek
You are now ready to complete your first deployment of Zeek.  On each deployment, Zeek re-examines its configuration and scripts and deploys them when starting the services.  In the future, as you modify your Zeek configuration, you will need to complete a deployment each time.

>(Zeek) Execute the following command to deploy Zeek
```
zeekctl deploy
```

>Or within the Zeek Control shell

```
zeekctl


Welcome to ZeekControl 2.3.0
Type "help" for help.
[ZeekControl] > deploy

```

>If all is working well, you should get this output
```
zeekctl deploy
checking configurations ...
installing ...
removing old policies in /opt/zeek/spool/installed-scripts-do-not-touch/site ...
removing old policies in /opt/zeek/spool/installed-scripts-do-not-touch/auto ...
creating policy directories ...
installing site policies ...
generating cluster-layout.zeek ...
generating local-networks.zeek ...
generating zeekctl-config.zeek ...
generating zeekctl-config.sh ...
stopping ...
stopping workers ...
stopping proxy ...
stopping manager ...
stopping logger ...
starting ...
starting logger ...
starting manager ...
starting proxy ...
starting workers ...



```

## 4.2 Start Zeek with system

To start Zeek when the operating system starts, create a file and place it into `/etc/systemd/system` .

1. (Root) Create a file called `/etc/systemd/system/zeek.service`
```
vi /etc/systemd/system/zeek.service
```

```
vi /etc/systemd/system/zeek.service
```
2. Populate the file as follows:

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

3. (Root) Make the file executable, `start` the service and `enable` it.

```
chmod u+x /etc/systemd/system/zeek.service
systemctl start zeek.service
systemctl enable zeek.service
```

## 4.3 Node Crash Recovery `zeekctl cron`

To ensure reliable and resilient collection of your network traffic, it is recommended to add Zeekctl Cron to Crontab.  This will allow Zeek to recover a node from a crashed state.  Add to `zeekctl cron` to crontab for automatic recovery of crashed nodes.

1. (Zeek) Open the Crontab Editor

```
crontab -e
```
2. Add the following entry

```crontab1
*/5 * * * * /opt/zeek/bin/zeekctl cron
```


## 4.4 Zeek Configuration Options


### 4.4.1 MAC address logging `local.zeek`

When you enable Link-Layer (MAC) address logging, Zeek will add two fields to the conn.log: 'orig_l2_addr' and 'resp_l2_addr'. This is especially useful when using asset tracking.  

1. (Zeek) Edit the local.zeek configuraiton file in the site folder (Manager)
```
vi /opt/zeek/share/zeek/site/local.zeek
```

2. Locate the section to enable Mac logging. Uncomment '@load policy/protocols/conn/mac-logging'

```
...
# Uncomment the following line to enable logging of link-layer addresses. Enabling
# this adds the link-layer address for each connection endpoint to the conn.log file.
@load policy/protocols/conn/mac-logging
...
```

### 4.4.2 VLAN ID logging `local.zeek`

When you enable VLAN logging, Zeek will add two additional fields to the conn.log: 'vlan' and 'inner_vlan'.  

1. Edit the local.zeek configuraiton file in the site folder (Manager)
```
vi /opt/zeek/share/zeek/site/local.zeek
```

2. Locate the section to enable VLAN logging. Uncomment '@load policy/protocols/conn/vlan-logging'
```
...
# Uncomment the following line to enable logging of connection VLANs. Enabling
# this adds two VLAN fields to the conn.log file.
@load policy/protocols/conn/vlan-logging
...
```

### 4.4.3 Load packages `local.zeek`

To load packages added manually or by the ZKG package manager, located in your site folder, you must uncomment out the @load packages line.

1. Edit the local.zeek configuraiton file in the site folder (Manager)
```
vi /opt/zeek/share/zeek/site/local.zeek
```

2. Locate the section to load packages. Remove the comment that is placed their by default.  Please note this will load all packages contained in your /opt/zeek/share/zeek/site/ folder.
```
...
# Uncomment this to source zkg's package state
@load packages
...
```


### 4.4.4. Local and Public networks`networks.cfg`

Defining your networks to Zeek allows for you to differentiate between local and remote traffic.  Add all your netwoks and public networks referencing the example below.

(Zeek)
```
vi /opt/zeek/etc/networks.cfg
```

```networks.cfg
# List of local networks in CIDR notation, optionally followed by a
# descriptive tag.
# For example, "10.0.0.0/8" or "fe80::/64" are valid prefixes.

10.0.0.0/8      		User Workstations
172.16.0.0/12       	        Server Farm A
172.16.10.0/24			Management Network
10.0.100.0/22			Campus A
10.0.101.0/21			Campus B
205.198.10.20/24		Public Network
```

### 4.4.5 Email `zeekctl.cfg`

1. Edit Zeekctl.cfg (Default location: /opt/zeek/etc/zeekctl.cfg)

```
vi /opt/zeek/etc/zeekctl.cfg
```

This option enables Zeek to send email.  

- If not configured, you will get notified when running `zeekctl start` or `zeekctl deploy`.
- Not a dependency and Zeek will start without it.
- By default, Zeek will use sendmail when:
  - `zeekctl deploy` or  `zeekctl start` has been initiated.
  - A node crashes.
- Can be extended to send mail on events.
- Not installed by default but is included in the dependency section. participants can configure per environment.

2. Locate the MailTo line and define an email address

> #Recipient address for all emails sent out by Zeek and ZeekControl.
> MailTo = security@your-org.ca


### 4.4.6 Log Retention and Collection `zeekctl.cfg`

Zeek automatically rotates and archives runtime logs from `/opt/zeek/logs/current`into `/opt/zeek/logs/yyyy-mm-dd/` on a configurable interval.

1. (Zeek) Edit Zeekctl.cfg (Default location: /opt/zeek/etc/zeekctl.cfg)

```
vi /opt/zeek/etc/zeekctl.cfg`
```

2. Locate the LogRotationInterval, LogexpireInterval lines and modify to your requirements.
> `LogRotationInterval` is measured in seconds.  The default value `3600`, or one hour.
> `LogExpireInterval` is measured in days.  `30` days is the recommended value.

```
...
LogRotationInterval = 3600
...
LogExpireInterval = 30
...
```

### 4.4.7 Zeek Cluster Mode `node.cfg`

Zeek by default is configured to run in Standalone mode.  This means that only one interface can be configured to monitor traffic.  To use more than one port and provide a scalable solution, it is **HIGHLY**  recommended to configure Zeek to run in Cluster mode. 

1. (Zeek) Edit node.cfg
```
vi /opt/zeek/etc/node.cfg
```

2. **Comment out** the standalone configuration portion at the top.
```
#[zeek]
#type=standalone
#host=localhost
#interface=eno1
```

3. Define your work node configuration. 

A worker node is a type of thread that can be assigned to one or more interfaces.  For ease of administration, we recommend creating a worker node for each interface you will be using for traffic monitoring and analsys on your IDS.  For example you will have 4 interfaces you would create 4 workers (Example below).

Please note, you do not have to create a worker for each interface.  If you only intend to use two interfaces, you only would need to create two workers (one for each interface).

Example:
```
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
interface=ens2f1

[worker-2]
type=worker
host=localhost
interface=ens2f2

[worker-3]
type=worker
host=localhost
interface=ens2f3

[worker-4]
type=worker
host=localhost
interface=ens2f4

```
Please note that you will need to update ***interface=*** for each worker node to reflect a unique and valid interface. To get a list of interfaces available, execute ***ip a*** from the CLI.

### 4.4.8. Apply the Configuration Files

With the completion of the previous steps, its now time to re-deploy Zeek.  Anytime you make changes similar to the above, you will need to re-deploy. 

When configuration files are modified, execute:
(Zeek)
```
zeekctl deploy
```

# 5 `sendmail`(optional)

1. Install Sendmail

```
sudo apt-get install sendmail
```
2. Configure Sendmail
```
vi /etc/mail/sendmail.mc
```
If you need to configure a smart host to relay:

```
define(`RELAY_MAILER_ARGS',`TCP $h 587')dnl
define(`SMART_HOST','smtp.smarthost.com')dnl
```
3. Make Sendmail configuration
```
sudo sendmailconfig
```

4. Test Sendmail configure
```
echo "test message" | sendmail -v your@email.ca
```

# 6 UFW - Uncomplicated Firewall (Optional)

Install UFW

```
sudo apt install ufw -y
```

Confirm UFW service running

```
sudo ufw status
```

Allow SSH

```
sudo ufw allow 22
```
Enable the UFW service

```
sudo ufw enable
```
If you need to disable the UFW service, use the command 'sudo ufw disable'

# 7 Log Processing

## 7.1 RSYSLOG

**RSYSLOG portion under development**

It is recommended to have your Zeek logs sent to a more sophisticated analytics platform, such as a SIEM, Elastic Search, Splunk or simliar platforms.  

RSYSLOG, the 'rocket-fast system for log processing' is installed by default and can send syslog messages to remote systems.

Below is an example configuration you can use to adapt to your target analytics platform.  Please replace 'XXX.XXX.XXX.XXX' with your platform's IP address.

Create/Edit an RSYSLOG  configuration file:
>RSYSLOG by default is configured to load any and all .conf files located in /etc/rsyslog.d/

```
vi /etc/rsyslog.d/rsyslog-zeek00.conf
```
```
#### ZEEK IDS configuration file ####

#### MODULES ####
module(load="imfile")

#### Templates ####
#template (name="ZEEK_Logs" type="string"
#          string="<%PRI%>%PROTOCOL-VERSION% %TIMESTAMP:::date-rfc3339% %HOSTNAME% %APP-NAME% %PROCID% %MSGID% %STRUCTURED-DATA% %$!msg%\n"
#         )

#### RULES for where to send Log Files ####
# Send messages over TCP using the ZEEK_Logs template
ruleset(name="sendZEEKLogs") {
#
#if $msg startswith not "#" then {
#        set $!msg = replace($msg, "|", "%7C"); # Handle existing pipe char
#        set $!msg = replace($!msg, "\t", "|");

        action (
        type="omfwd"
        protocol="udp"
        target="10.20.30.40"
        port="514"
#       template="ZEEK_Logs"
      )
#    }
}


input (
    type="imfile"
    File="/opt/zeek/logs/current/dns.log"
    Tag="zeek-dns:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/conn.log"
    Tag="zeek-conn:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)


input (
    type="imfile"
    File="/opt/zeek/logs/current/ssl.log"
    Tag="zeek-ssl:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)


input (
    type="imfile"
    File="/opt/zeek/logs/current/dhcp.log"
    Tag="zeek-dhcp:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/http.log"
    Tag="zeek-http:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/weird.log"
    Tag="zeek-weird:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/zeek-kerberos.log"
    Tag="zeek-kerberos:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/ntlm.log"
    Tag="zeek-ntlm:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/files.log"
    Tag="zeek-files:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)


input (
    type="imfile"
    File="/opt/zeek/logs/current/known_hosts.log"
    Tag="zeek-known_hosts:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)


input (
    type="imfile"
    File="/opt/zeek/logs/current/notice.log"
    Tag="zeek-notice:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/ssh.log"
    Tag="zeek-ssh:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/intel.log"
    Tag="zeek-intel:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/log4j.log"
    Tag="zeek-log4j:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)

input (
    type="imfile"
    File="/opt/zeek/logs/current/smbfiles.log"
    Tag="zeek-smbfiles:"
    Facility="local7"
    Severity="debug"
    RuleSet="sendZEEKLogs"

)
```

## 7.2. SYSLOG-NG

As an alternative option, Syslog-NG can be used instead of RSYSLOG.  

1. (Root) Install syslog-NG

```
sudo apt-get install syslog-ng
```

2. (Root) Edit the syslog-NG configuration file

```
vi /etc/syslog-ng/syslog-ng.conf
```
Example configuration, insert below @include "scl.conf"
Take note the program-override assigns a tag to each log type.  This will be important when developing a parser for your analystics/SIEM platform.
```
 source s_local {
     system(); internal();
 };

 source s_bro_file {
    file("/opt/zeek/logs/current/dns.log" program-override("zeek-dns") flags(no-parse));
    file("/opt/zeek/logs/current/conn.log" program-override("zeek-conn") flags(no-parse));
    file("/opt/zeek/logs/current/ssl.log" program-override("zeek-ssl") flags(no-parse));
    file("/opt/zeek/logs/current/dhcp.log" program-override("zeek-dhcp") flags(no-parse));
    file("/opt/zeek/logs/current/http.log" program-override("zeek-http") flags(no-parse));
    file("/opt/zeek/logs/current/weird.log" program-override("zeek-weird") flags(no-parse));
    file("/opt/zeek/logs/current/kerberos.log" program-override("zeek-kerberos") flags(no-parse));
    file("/opt/zeek/logs/current/ntlm.log" program-override("zeek-ntlm") flags(no-parse));
    file("/opt/zeek/logs/current/files.log" program-override("zeek-files") flags(no-parse));
    file("/opt/zeek/logs/current/known_hosts.log" program-override("zeek-known_hosts") flags(no-parse));
    file("/opt/zeek/logs/current/notice.log" program-override("zeek-notice") flags(no-parse));
    file("/opt/zeek/logs/current/ssh.log" program-override("zeek-ssh") flags(no-parse));
    file("/opt/zeek/logs/current/intel.log" program-override("zeek-intel") flags(no-parse));
    file("/opt/zeek/logs/current/log4j.log" program-override("zeek-log4j") flags(no-parse));
    file("/opt/zeek/logs/current/smb_files.log" program-override("zeek-smbfiles") flags(no-parse));

 };
 destination d_syslog_fsm {
     network("10.20.30.40" transport("udp") port(514));
 };
 log {
  source(s_bro_file);
  destination(d_syslog_fsm);
 };
```

Take note of the Zeek log files above.  If you want to reduce your EPS sent to your SIEM, you can comment out certain logs.  For example, some institutions only want to receive intel.log and notice.log.

3. (Root) Restart SyslogNG

```
service syslog-ng restart
```

Due to a bug in Syslog-NG when log files are rotate, we have a script to reload Syslog NG every 30 seconds. The reload of Syslog-NG needs to be done as the Root user.

4. (Root) Edit Crontab

```
crontab -e
```
Paste the following example to schedule Syslog NG to reload every 30 seconds

```
# Reload Syslog-ng
*/30 * * * * /usr/sbin/syslog-ng-ctl reload
```

# 8.0 Zeek Plugins

## 8.1 Install `AF_Packet` plugin

AF Packet allows you to control how processor cores are assigned to specific interfaces. This allows you to have granular controller on CPU allocation. In some environments, certain interfaces could require more CPU resouces. Optimizing core allocation can allow you to finely tune your Zeek IDS.

Ideally, this plugin is installed via the package manager (zkg).  

1. (Zeek) Execute the command:

```
zkg install zeek/zeek-af_packet-plugin
```

2. (Root) Re-Apply permissions for the Zeek user and group

```
chown -R zeek:zeek /opt/zeek
```
```
setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
```

3. Determine your desired core allocation design

With AF Packet now installed, we must reconfigure the node.cfg file to use the AF Packet plugin.  The following items will need to be defined in the node.cfg file:

- Define af_packet parameters (interfaces) and dedicate cpu threads to nodes.
- Configure assign interfaces and AF_PACKET parameters to workers.
- Assign workers to interfaces.
- Pin core(s) to cluster elements, such as workers, to ensure dedicated compute resources.  
- The general rule is 250MiB throughput per thread.
- Well distributed load makes more efficient work.

Fetch the `processors` and `core ids`of the server.  Use the distribution of processors to core for similar distribution between workers.

```awksed
awk '/core id/ || /processor/' /proc/cpuinfo | sed 'N;s/\n/\t/'
...
processor       : 0     core id         : 0
processor       : 1     core id         : 4
...
processor       : 39    core id         : 26
```

Workers: The fastest memory and CPU core speed you can afford is recommended since all of the protocol parsing and most analysis will take place here.

Now you're ready to reconfigure`node.cfg` to use AF_PACKET


5. (Zeek) Edit node.cfg

```
vi /opt/zeek/etc/node.cfg
```

The below sample is an example only.  If you are only using 2 interfaces, you can allocate more processors to these interfaces.  You do not have to specify every interface in node.cfg.  If you are not using a physical interface, you can omit it altogether here.

 Example for **JSP3 Hardware**: Five workers, each with their own interface:

- 8  threads per worker
- Theoretical throughput: (Megabit) = (#Threads)(250) 
- 8 * 250=2Gb/sec per worker
- Remaining processors researved for system

```
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
lb_procs=8
pin_cpus=0,1,2,3,4,5,6,7
af_packet_fanout_id=1

[worker-2]
type=worker
host=localhost
interface=af_packet::ens2f1
lb_method=custom
lb_procs=8
pin_cpus=8,9,10,11,12,13,14,15
af_packet_fanout_id=2

[worker-3]
type=worker
host=localhost
interface=af_packet::ens2f2
lb_method=custom
lb_procs=8
pin_cpus=16,17,18,19,20,21,22,23
af_packet_fanout_id=3

[worker-4]
type=worker
host=localhost
interface=af_packet::ens2f3
lb_method=custom
lb_procs=8
pin_cpus=24,25,26,27,28,29,30,31
af_packet_fanout_id=4

[worker-5]
type=worker
host=localhost
interface=af_packet::ens2f4
lb_method=custom
lb_procs=8
pin_cpus=32,33,34,35,36,37,38,39
af_packet_fanout_id=5
```

(Zeek) Once you have completed you configuration, save your file.
Re-Deploy Zeek
```
zeekctl deploy
```

## 8.2 Install `ADD_INTERFACES` plugin

Adds cluster node interface to logs. Useful when sniffing multiple interfaces to identify source of log.

1.(Zeek) Execute the command
```
zkg install zeek/j-gras/add-interfaces
```

2.(Zeek) Edit config file and modify two lines 
```
vi /opt/zeek/share/zeek/site/packages/add-interfaces/add-interfaces.zeek
```

3.  

a)  Change the `...enable_all_logs = F...` to `...enable_all_logs = T...

b)  Remove text inside the curly brackets in 

`...include_logs: set[Log::ID] = { }...` .


Example when updated:
```
export {
        ## Enables interfaces for all active streams
        const enable_all_logs = T &redef;
        ## Streams not to add interfaces for
        const exclude_logs: set[Log::ID] = { } &redef;
        ## Streams to add interfaces for
        const include_logs: set[Log::ID] = { } &redef;
		}
```

4. (Root) Re-Apply permissions for the Zeek user and group
```
chown -R zeek:zeek /opt/zeek
```

```
setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
```
5. (Zeek) Re-Deploy Zeek

```
zeekctl deploy
```

# 9.0 Aggregrate Portal File Transfer - CANIDS

## 9.1 Setup

- Participants should be prepared to provide the public address that will access the remote (Concordia) server.

1. (Zeek) From the server run

```
curl ifconfig.me

```
Example output 
```
205.XXX.XXX.XXX
```
2. (Zeek) Generate ssh-keys: 

   > Passwordless authentication requires key exchange.  This is necessary for automation.

```
ssh-keygen -t rsa -b 4096
```

Example interaction:
```
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

3. (Zeek) Copy the contents of  your new public key:  

```
cat ~/.ssh/id_rsa.pub
```

4. Contact Anis via email `anis.lounis@mail.concordia.ca` or via the JSP Slack channel (#jsp-tech) for registration and access to remote server. 

Please include the following information:   

   a) Include the public (egress) IP of your IDS server (Step 1)  
   b) Include your SSH Public Key of your IDS server (Step 3)  
   c) Include the public (egress) IP address(s) of workstations that will be accessing the CANIDS portal.  


Concordia will reply with the participant's username for access to the server and credentials for accessing the web interface "https://portal.canids.ca/".  Once you receive these credentials, proceed to the next step to test the connection.


5. (Zeek) Test connectivity `$ssh -p 56320 <username>@push.jointsecurity.ca`.
   - Connectivity to the remote server should be established:

```
ssh -p 56320 <username>@push.jointsecurity.ca
Welcome
...
username@feedserver:~$
```
Now that we have confirmed your account is set up and authentication is working, Proceed to installing RSYNC to set up the secure file transfer.


## 9.2. Install RSYNC 

> Synchronizes files between two systems and only copies what is different.

- Faster and more efficient when compared to SCP.
- Only copies what is different.
- Can use compression.
- Can operate over ssh protocol with all of the benefits.
- Can force hash checking.

> - *Local*:  JSP Server where logs reside in `/opt/zeek/logs/`.
> - *Remote*: system which will receive logs.

1. (Root) Install `rsync`.

```
sudo apt install rsync
...
Complete!
```

2. (Zeek) Transfer files.

Create an `rsync` script and add it to the crontab of the zeek user.

a) Create a script and populate it:

```
vi /home/zeek/rsync.sh
```
The `rsync` command in this script can be tuned to the institution's preference in terms of what files are uploaded.  This script does not upload files that may contain  personally identifiable information (PII).

Please make sure to update <username> with the username provided by Concordia.

The example below contains a list of excluded logs.  Some of these logs contain cleartext traffic. We recommend keeping these logs excluded as they may contain sensitive information (credentials) and/or PII.

```
rootDir="/opt/zeek/logs/"
for day in 0 1 2 3 4 5 6 7
do
    dirName="${rootDir}"$(date  --date=${day}" days ago" +%Y-%m-%d);
    echo $dirName;

# Upload all zeek logs except those that may contain PII.
rsync -av --progress -e "ssh -p 56320" --exclude={"http.*.log.gz","ftp.*.log.gz","ntlm.*.log.gz","irc.*.log.gz","sip.*.log.gz","radius.*.log.gz","smtp.*.log.gz","rdp.*.log.gz","files.*.log.gz","syslog.*.log.gz","snmp.*.log.gz",stats,current} $dirName username@push.jointsecurity.ca:

done
```

b) (Zeek) make the script executable:
```
chmod +x rsync.sh
```

c) (Zeek) Automate transfer of files with crontab: 
```
crontab -e
```

Example script:

```rsync_crontab_b
# Sync Zeek logs every hour to CANIDS.
0 * * * * /usr/bin/sh /home/zeek/rsync.sh
```

# 10 Deploy and Troubleshoot

Below are a list of commands that need to be re-applied when modifications to the system have been applied and the system doesn't operate as expected (for example, if Zeek is crashing on startup/deployment)

> Further information can be found in the CANARIE FAQ file, [located in the JSP portal](https://jspportal.canarie.ca).

1. (Zeek) Deploy Zeek `zeekctl deploy` to apply plugin installations and config modifications and run Zeek.

>If you encounter errors, follow step 2 and 3 'Give the Zeek application permission to capture packets`.

```zeekctl deploy
zeekctl deploy
```

2. Make sure the `zeek` user and group have correct permissions.

New applications and system modifications can result in a change of permissions.  The `zeek` user and group should maintain control of `/opt/zeek`.  


(Root)  Run the following as ROOT

```
chown -R zeek:zeek /opt/zeek
```

3. Give the Zeek application permission to capture packets.

(Root) Run the following as ROOT

```
setcap cap_net_raw=eip /opt/zeek/bin/zeek && setcap cap_net_raw=eip /opt/zeek/bin/capstats
```

4. Add Zeek binaries to path

Verify that the path to the Zeek binaries is available globally.  You can verify if this is set correctly by executing the command 'which zeek' as the Zeek user.  You should receive the folowing response:  "/opt/zeek/bin/zeek"  If you do not, proceed below.

To resolve this issue, we recommend creating a profile bash script for the Zeek user.  To complete this, complete these steps:

(Root) Run the following as ROOT

```add zeek to path
echo "export PATH=/opt/zeek/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin" >> /etc/profile.d/zeek.sh
```

>Logout and login again as Zeek

5. (Zeek) Confirm path update with `which` command.

   Run the following as ZEEK

> If the command returns `/opt/zeek/bin/zeek`, your path has been updated.


```
which zeek
/opt/zeek/bin/zeek
```

# Extra: Use Cases

a) (Zeek) Ensure that zkg packages are being loaded 
```
vi /opt/zeek/share/zeek/site/local.zeek
```

Uncomment this to source zkg's package state

'@load packages'


b) (Zeek) Refresh zeek package manager repository
```
zkg refresh
```

c) (Zeek) Keep packages updated 
```
zkg upgrade
```


## (Optional) Write logs to TSV & JSON

- Simultaneously write log files in both TSV and JSON formats

- Allows participants to use add-ons that utilize JSON logging format:

  - Splunk forwarder
  - Logstash
  - Filebeat

- Each log will have a prefix of `json_streaming_`.

- Rotates logs within `/opt/zeek/logs/current` directory so the log shipper has time to catch up.

  ```
  ls json_streaming_ftp*
  json_streaming_ftp.1.log
  json_streaming_ftp.2.log
  json_streaming_ftp.3.log
  json_streaming_ftp.4.log
  json_streaming_ftp.log
  ```

- Compressed and archived per the value of `LogRotationInterval` in  `/opt/zeek/etc/zeekctl.cfg`

1. (Zeek) Install the plugin

```
zkg install zeek/corelight/json-streaming-logs
```

2.  (!) Ensure `@load packages` is NOT commented out in `/opt/zeek/share/zeek/site/local.zeek`

3. (Zeek)  Re-Deploy Zeek 

```
zeekctl deploy
```
4. Verify: 
```
ls /opt/zeek/logs/current | grep json_streaming
```

5. Duplicate data:
   - Both `TSV` & `JSON` saved and rotated resulting in duplicate data
   - Consider managing disk space
   - Routinely delete redundant data
6. (Zeek) Exclude `JSON` from upload to analytics platform 

```
vi /home/zeek/rsync.sh
```
   - add `json_streaming_*` to exclusion list

(Zeek) When not in use, unload plugin to save disk space.

   ```
   zkg unload json-streaming-logs
   ```
(Zeek) Re-Deploy Zeek
```
zkg deploy
```

Verify:

```
zkg list unloaded
```

>Expected output:

>zeek/corelight/json-streaming-logs (installed: v3.0.0) - JSON streaming logs
>zeek/hosom/file-extraction (installed: 2.0.3) - Extract files from network traffic with Zeek.



## Examine offline packet traces

> Use Zeek to process captured packet traces. 

(Optional) Generate a PCAP file



1. (Root) Install `tcpdump`:

```
sudo apt install tcpdump
```

2. Capture packet options:

```
sudo tcpdump -i ens2f1 -c 100 -s 65535 -w 100-packets.trace
sudo tcpdump -i ens2f1 -s 0 -w packet.trace
```

- `ens2f1` should be replaced by the correct interface for your system, for example as shown by the `ip a` command. 
- `-c 100` designates how many packets to capture.  If not defined you must `ctrl+c` to interrupt `tcpdump` and halt data accumulation.
- `-s 0` capture whole packets. 
  - If not supported use `-s 65535`.


3. Use Zeek to examine packet trace

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



**For questions/feedback, please contact our team at idstech@canarie.ca**



### 
