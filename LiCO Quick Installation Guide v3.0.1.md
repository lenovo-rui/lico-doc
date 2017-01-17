LiCO Quick Installation Guide v3.0.1
===============================

### Table of contents

[TOC]

#1. Download the Package

##Download OS and Installation files
LiCO supports CentOS 6.5/6.8 and RedHat 6.5/6.8. If required system is CentOS, please download both CentOS-*-DVD1.iso ( for head node installation) and CentOS-*-DVD.iso ( for LiCO yum source)  from team's FTP.  

>**System Package URL**
>
>ftp://10.240.208.41/packages/CentOS-6.5-x86_64-bin-DVD1.iso
>
>ftp://10.240.208.41/packages/CentOS-6.5-x86_64-DVD.iso
>
>ftp://10.240.208.41/packages/CentOS-6.8-x86_64-bin-DVD1.iso
>
>ftp://10.240.208.41/packages/CentOS-6.8-x86_64-DVD.iso

To install RedHat, just download one ISO file, since both head node and yum source are packaged in a single ISO file.  

##Download LiCO package

>**LiCO  Package URL**
>
>ftp://10.240.208.41/Releases/ 

#2. For installation on an existed Cluster, please reference Appendix 2

LiCO supports installing Web server on an existed cluster, but it still needs to meet some minimum requirements, please reference Appendix 2 for more information.  

#3. Config IMM IP and create IPMI account for nodes in cluster

Manually config IMM IP and Create IPMI account.

>**NOTES:**
>
>1. IMM and IPMI account is the same for Rack Server and Next Scale, so user does not need to create the account for each of them. But for Flex server, user needs to create IPMI account separately.
>
>2. If Flex Server is setup up with IMM IP and IPMI account, we will treat Flex Server as Rack Server.

#4. Install Head Node
###Step 0: Config raid of Head node, Raid1 is suggested. Skip if the raid is already reconfigured.

###Step 1: Install OS (CentOS 6.5/6.8 or RedHat 6.5/6.8) for Head node.

>**NOTES:** 

>1. If you are using CentOS, please download image CentOS-*-DVD1.iso to install Head node.

>2. Please select English as the System language.

###Step 2: Config hostname of Head node. Open "/etc/sysconfig/network" and change hostname to "mgt".

    NETWORKING=yes
    HOSTNAME=mgt

###Step 3: Setup IP of Head node's network card. There will be 3 IP addresses for a Head node.

> Management Network (OS) IP, e.g. 172.20.0.1 

> Application Network IP, e.g. 172.22.0.1 

> Public Network IP (optional, for remote access to Head node).

Edit "/etc/sysconfig/network-scripts/ifcfg-ethX" 
or "/etc/sysconfig/network-scripts/ifcfg-ibX" to setup network card. (replace X with the number of the network card, e.g. ifcfg-eth1)

    ONBOOT=yes
    NM_CONTROLLED=no
    BOOTPROTO=static
    IPADDR=10.240.212.28
    NETMASK=255.255.255.0
    GATEWAY=10.240.212.1
please use command below to restart network service. 

    #service network restart

>**NOTE:**
>
>If using IB for Application network configuration, config will show ***fail*** when restart network service. You see this error because IB driver and service is not well installed, LiCO will take care of them later, So don't worry about it.   

###Step 4: Copy LiCO package and extract it to a directory. 

###Step 5: Copy OS ISO image to LiCO folder, e.g. "/lico_3.x/packages/os"

>**NOTE:**
>
>If installing CentOS, please download CentOS-*-DVD.iso to LiCO folder, e.g. "/lico_3.x/packages/os".

###Step 6: Modify the configuration file
    
    [root@mgt ~]# cd /lico_3.x/etc/
    [root@mgt etc]# vi cluster.conf 
    DOMAIN="hpc.com"
    OS="centos6.5" #OS version
    OSISO="CentOS-6.5-x86_64-DVD.iso" # ISO image filename 

>**NOTE:**
>
>Both CentOS6.5/6.8 and Red Hat 6.5/6.8 is supported by LiCO. If you are using RedHat, use following configuration. 
>`OS="rhels6.5"#OS version` 
>
>`OSISO="RHEL6.5-20131111.0-Server-x86_64-DVD1.iso"  #ISO Image filename`

###Step 7: Update Kernel of Head Node

    [root@mgt ~]# cd /lico_3.x/setup/
    [root@mgt ~]#./0_upgrade_head_node_kernel.sh（don't execute thie command through ssh, execute it locally. ）
    Reboot

###Step 8: Config Network

>**NOTE:**
>
>There is no BMC network configuration in following config sample. If you are using BMC network, please make sure your Head node has access to BMC network. 

>If there isn't "Login" node in cluster,  you don't need to config "public network".

[RUI] What's the different between dynamic range and static range in config?
[RUI] Why application network does not require dynamic range

    [root@mgt ~]# cd /lico_3.x/etc/
    [root@mgt etc]# vi networks.conf   

    net 172.20.0.0 {
        networktype     management-network
        nictype         Ethernet
        mgtifname       eth0  # the nicname of management network on the head node
        netmask       255.255.0.0
        gateway       <xcatmaster>   #no need to change 
        staticrange     172.20.0.11-172.20.1.255
        dynamicrange  172.20.10.1-172.20.10.254
    }
    net 172.22.0.0 {
        networktype     application-network
        nictype         Infiniband
        mgtifname       ib0	 # the nicname of app network on the head node
        netmask        255.255.0.0
        gateway        <xcatmaster>  #no need to change
        staticrange     172.22.0.11-172.22.0.255
    }
    net 10.240.212.0 {
        networktype     public-network
        nictype         Ethernet
        netmask       255.255.255.0			# Note: It must match the customer network environment
        gateway       10.240.212.1			# Note: It must match the customer network environment
        staticrange     10.240.212.29-10.240.212.40		# Note: It must match the customer network environment
    }
    
###Step 9: Setup Head Node

    [root@mgt ~]# cd /lico_3.x/setup/
    [root@mgt setup]# ./1_head_node_setup.sh （don't execute thie command through ssh, execute it locally.）
    [root@mgt setup]# ssh mgt（ssh Head node itself）

Check configuration of Head node: 

    [root@mgt setup]# source /etc/profile.d/xcat.sh
    [root@mgt setup]# cd ~/lico_3.x/bin/
    [root@mgt bin]# ./service_manager.py --check head


#5.	Deploy Cluster

###Step 1: Modify Cluster configuration file
Please reference Appendix 1

###Step 2: Add node to xcat 

    [root@mgt bin]# ./add_nodes.py

 
###Step 3: Config Raid of Node
Remotely connect to IMM of the node through ssh channel, and use following command to configure the Raid. Skip if Raid was already configured. 

    system> storage -config vol -add -R 1 -D disk[9-0]:disk[9-1] -target ctrl[9]
    
    system> storage -list volumes

>**NOTE:**
>
>This command of Raid configuration is only supported by m5 machine, please use UEFI for other type of machines.

###Step 4: Retrieve MAC Addresses of Nodes (Reference ThinkServer Support of Appendix 3 for ThinkServer deployment)

Use following command to get mac addresses of all nodes. The results of mac addresses returned are stored in macs.txt (folder path is: "/lico_3.x/bin/"). 
The first NIC is  marked as "selectednic" by default. You can change it to the NIC which you used to install OS, the NIC you used should be connected to Management Network.

    [root@mgt bin]# ./discover_node_macs_using_imm.py --node "nodes1,nodes2,node[3-5]" --immuser "USERID" --immpwd "Passw0rd"

Here is a sample of a macx.txt: 

    Node1;
      selectednic=PXE.NicPortMacAddress.1;
      PXE.NicPortMacAddress.1=00:90:FA:0D:39:3C;
      PXE.NicPortMacAddress.2=00:90:FA:0D:39:40;
      PXE.NicPortMacAddress.3=00:90:FA:0D:39:44;
      PXE.NicPortMacAddress.4=00:90:FA:0D:39:48
    
    Node2;
      selectednic=PXE.NicPortMacAddress.1;
      PXE.NicPortMacAddress.1=00:00:C9:F5:6D:08;
      PXE.NicPortMacAddress.2=00:00:C9:F5:6D:0C;
      PXE.NicPortMacAddress.3=00:00:C9:F5:6D:10;
      PXE.NicPortMacAddress.4=00:00:C9:F5:6D:14

Use following command to add selected NIC to database of xcat, after finished editing macx.txt. 

    [root@mgt bin]# ./set_node_mac_in_xcat.py -c "macs.txt"

###Step 5: Deploy Node (Reference ThinkServer Support of Appendix 3 for ThinkServer deployment) 

User following scripts to deploy nodes:

    [root@mgt bin]# ./deploy_nodes.py "node1,node2,node[3-5]"

>**NOTE:**
>
>When meeting problems below, press 'ctrl+c' to termindate, then re-run the scripts. 
>
>`Error: Unable to find an IP for c01n001-data in hosts table or via system lookup (i.e. /etc/hosts)`


>**NOTE:**
>
>Please be patient, since it takes long time installing software packages to compute nodes. 
>
>the root password of each node is 'Passw0rd'

Please use following commands to check if all nodes are deployed successfully. 

    [root@mgt bin]# ./service_manager.py  --check torque
    [root@mgt bin]# ./service_manager.py  --check ganglia 
    [root@mgt bin]# ./service_manager.py  --check clusterping 
    [root@mgt bin]# ./service_manager.py  --check mpi

Please login to ganglia and check if ganglia is correctly deployed. 

    http://172.20.0.1/ganglia

###Step 6: Rename Application NIC of a Node (optional)

> **NOTE:**
> 
>Skip this step if Application NIC name is same in different nodes, e.g. eth1. 

It is possible that application NIC names are different in each nodes. So that high performance job can be assigned to a certain application NIC, it is better to use same NIC name for all nodes of a cluster. 

Use following script to rename application NIC name of non-Head node to the same name:

    [root@mgt bin]# ./rename_node_nic.py --node nodes1,nodes2,node[3-5] --nictype ib --old ib0 --new ibdata 

Use following script to rename application NIC name of Head node to the same name:

    [root@mgt bin]# ./rename_node_nic.py --node localhost --nictype ib --old ib0 --new ibdata

Use command "rpower all reset" of xcat to reboot all nodes except Head node.
Use "Reboot" to reboot Head node. 

>**NOTE:**
>
>- If Head node hangs in rebooting, please do it manually.
>
>- Please check whether config is applied successfully after reboot.

#6. Install Lustre, a parallel file system

>**NOTE:**
>
>Skip this part if your cluster will not use lustre.

Apply following steps to install lustre for your cluster: 
### Step 1: Modify Lustre Configuration File

    [root@mgt setup]# vi ../etc/lustre.conf

    file-system userfs {
    	interface       ibdata	 #application nic
    	shareddir       /share  #lustre will provide share folder name, you don't need to create it now.
        datanet_type	 Infiniband  #Infiniband or Ethernet for interface
    	datanet_hostnamemap io01:io01-data,io02:io02-data	#the map between normal nodename and nodename for interface
    	mds {
    	   io01    /dev/sdb1    #hard disk, you can use fdisk or parted to check or re-layout the partitions
           io02    /dev/sdb1	
    	};
    	oss {
    		io01    /dev/sdb2
    		io02    /dev/sdb2
    	};
         #### The following items is NOT required to input if no HA required.###
    	 ha_heartbeat_net {
    	 io01[eth1,192.168.1.11,255.255.255.0]:io02[eth1,192.168.1.12,255.255.255.0] 
    	 };
    	 ha_mdt {
    	  io01:io02
    	 };
    	 ha_ost {
    	  io01:io02
    	 };
    }

###Step 2: Install Lustre

Use following script to install lustre.

    [root@mgt bin]# ./lustre_setup.py

After installation, issue following command to check if share folder is correctly mounted or not. 

    [root@mgt bin]# df –h

# 7.	Install LDAP to Manage Users of a Cluster
LiCO utilize LDAP to manage users of a cluster
There are 2 scenarios :

 1. use the script to build a new ldap environment 
 2. use LiCO in an existed ldap environment

We strongly suggest using LiCO script to build a new ldap environment for HPC cluster and here are steps:

###Step 1: Use script to create LDAP service

    [root@mgt packages/openldap]# tar -xvf openldap.tar 
    [root@mgt openldap]# cd openldap 
    [root@mgt openldap]# ./setup_openldap.sh 172.20.0.1(ip of head node)

After finishing the scripts above, you have LDAP service installed on Head node and have LDAP client installed for all other nodes. 

> **Note:**
> 
> You need to restart torque related service after installing LDAP environment. 

User following script to restart torque:

    [root@mgt  bin]#./service_manager.py --restart torque

###Step 2: Modify Configuration File 

Change "/lico_3.x/etc/conf.yaml" as following:

    [root@mgt etc] vi conf.yaml

    user_management_method: ldap
    ldap_user_home_base: /share1/users_home
    ldap_server: 172.20.0.1(ip of head node)
    user_rootdir: /share1/users_root
    cluster_sharedir: [“/share2”]

user_home_base：user_home_base is the home directory of a LDAP user, and it must be shared across the cluster.

user_rootdir：user_rootdir is the root folder of a LDAP user, as known as the root foder for web page. user_rootdir must be shared across the cluster.
是ldap用户的根目录，也即用户在web页面上看到的顶级目录。user_rootdir也必须是一个能共享访问的目录。[RUI] 什么人可以共享访问，需要设定什么样的权限？

cluster_sharedir：cluster_sharedir is the shared folder of a cluster besides user_home_base and user_rootdir.

> **Note：**
> 
> The shared folders listed above must exist in system. If using nfs as the shared file system, nfs version must be version 3. 
> Use following command to mount version 3 nfs folder: 
> `mount -t nfs -o  vers=3 nfsserverip:/share  /share`

Here is a sample folder structure for use hpcadmin: 
例如系统中创建一个用户hpcadmin，其目录如下：

> /share1/users_root/hpcadmin <-root directory of hpcadmin, aka the root folder for web. 用户的根目录，也即web上显示的最顶层目录
> 
> /share1/users_root/hpcadmin/home <- symbolic link to user's home directory 软连接到用户的home目录 
> 
> /share1/users_root/hpcadmin/share2 <- symbolic link to user's sharedir
> 
> /share1/users_home/hpcadmin <- home directory of the user
> 
> /share2 <- public shared folder: sharedir

#8.	安装LiCO的web portal

###1.	编辑配置文件lico_3.**/etc/conf.yaml. 修改cluster相关的内容，修改domain为集群的名字。
###2.	安装GUI portal的依赖包

>[root@mgt lico_3.x]# python portal_package_install.py

> **Note：**安装完后需要关闭当前session，然后再开一个session来运行下面的命令
###3.	配置系统初始化信息，可以不修改，使用默认配置.
> [root@mgt lico_3.x]# vi portal_init.yaml

    username: hpcadmin
    password: Passw0rd
    email: admin@admin.com
    billgroup: default_bill_group
    credits: 1000
    osgroup: default_os_group
    recreate_os_user: True
    recreate_bill_group: True
    recreate_os_group: True


###4.	初始化 GUI portal
>[root@mgt lico_3.x]# python portal_init.py

###5.	启动 GUI portal 的service
启动service：在启动service的shell中会不断有屏幕输出，所以最好在一个一直存在的shell session里面启动这个service。

比如可以在screen里面启动这个service，screen -help可以查看screen命令的使用。

> [root@mgt lico_3.x]# screen 
> [root@mgt lico_3.x]# ./lico start

这时候如果关闭这个shell（不要用命令exit退出这个shell，这样的话这个screen也不存在了），screen和shell还在，可以用screen -r重新进入这个screen，可以看到原来的shell还在。

或者在vnc图形桌面里面开一个shell，在这个shell里面启动service。
停止service：命令为:
>[root@mgt lico_3.x]# ./lico stop

###6.	查看GUI portal是否安装配置成功

>URL: http://172.20.0.1:8080/login/   
>Username/Password: hpcadmin/Passw0rd
> 
> **Note：** 如果GUI portal上显示的node的状态不正确，通过route命令查看ganglia是否监控在正确的网口上，ganglia应该监控在管理网的网口上。如果不在，在每台机子上通过ip route add 239.2.11.71 dev eth0（管理网口）来设置，然后在所有节点重启gmond的服务service gmond restart, 然后重启LiCO(./lico stop; ./lico start)

#9.	部署报警模块Nagios(可选，不建议部署)
当前LiCO版本默认没有自动部署报警模块（Nagios），但提供了自动化部署Nagios的脚本，只需要在头结点上执行以下脚本就可以完成集群报警模块Nagios的部署。

> [root@mgt lico_3.x] cd  packages/nagios/setup [root@mgt setup]
> ./setup_nagios.sh <mgt_management_ip>  
> 比如：./setup_nagios.sh 172.20.0.1

最后，重启Nagios服务(service nagios restart) 和httpd服务后，在LiCO管理员界面点击查看报警信息Nagios并使用nagiosadmin用户（nagiosadmin/nagiosadmin）登陆到Nagios主页面。

![enter image description here](http://image.jpg)

#Appendix 1. Modify Cluster Configuration file: nodes.csv
Copy file in "/lico_3.*/etc/nodes.csv" to your local hard disk and open it with a corresponding editor, e.g. Microsoft Excel. 
Here are some guidelines for modifying the csv file. After finish, replace the csv file in Head node with the new one.

**Part 1**: Room Information, we support one room only for current release. 

| room     | name  | location_description |
| :------- | ----: | :------------------: |
|          | room1 |  2nd floor           |

 - ***name***: room name
 - ***location_description***: room location description

**Part 2**: Logical Group of the Cluster (mandatory)
| group    | name    | 
| :------- | :------:| 
|          | bigmen  | 
|          | compute | 
|          | gpu     | 
|          | login   |  

 - ***name***: Logical Group name

**Part 3**: Row Information (mandatory), the row which host the nodes of cluster.
| row      | name  | index | belonging_room  |
| :------- | ----: | :---: | :-------------: |
|          | row1  |  1    | room1           |
|          | row2  |  2    | room1           |        

 - ***name***: row name
 - ***index***: integer, can't be same with cluster.
 - ***belonging_room***: room information which the row is hosted. The name here must be same as specified in room.

**Part 4**: Rack Information (mandatory) , the racks which hosts the nodes of cluster.

| rack     | name  | column | belonging_row   |
| :------- | ----: | :----: | :-------------: |
|          | rack1 |  1     | row1            |
|          | rack2 |  2     | row1            |
|          | rack3 |  1     | row2            |  

 - ***name***: rack name
 - ***belonging_row***: the row which rack is sitting, the row name must be one of row name specified in Part 3. 
 - ***column***: the column the rack is sitting, integer, can't be duplicated with the row.

**Part 5**: Chassis Information (optional), must be specified if there is chassis in cluster
| chassis  | name      | belonging_rack | location   |  machine_type |
| :------- | :-------: | :------------: | :--------: |  :----------: |
|          | chassis1  |  rack1         | 7          | Flex_Chassis  |
|          | chassis2  |  rack2         | 20         | Flex_Chassis  |
|          | chassis3  |  rack1         | 7          | Nextscale_Chassis| 

 - ***name***: chassis name 
 - ***belonging_rack***: the rack name which chassis belongs to. the rack name must be one of the racks in Part 4. 
 - ***location***: location of chassis, it indicates which u the chassis is in，it is an integer value between 1-42. 
 - ***machine_type***: type of the chassis, either Flex_Chassis or Nextscale_Chassis. If the machine is Nextscale, use Nextscale_Chassis; if the machine is Flex, use Flex_Chassis.

**Part 6**: Switch Information (optional) 
Generally, we don't specify any information here.

**Part 7**: Server Information (mandatory)
| node | name      | nodetype | immip         |  hostip    | machine_type  | ipmi_user | ipmi_pwd |  belonging_rack |  belonging_chassis  |  location_u  |  width | height | groups  | application_network | application_network_nic | application_network_nic_type | public_nic | public_nic_ip | public_nic_type |     
| :--- | :-------: | :------: | :-----------: | :--------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | :-----------: | 
|      | head      |  head    | 10.240.212.97 | 172.20.1.1 |  ibm  | USERID | PASSWORD | rack1 |     |  2 |  1  |  1 |  login | 172.22.0.100 |  eth1  | Ethernet  |  eth2  | 10.240.212.22  | Ethernet |
|      | mgt       |  compute | 10.240.212.97 | 172.20.0.1 |  ibm  | xcat | Passw0rd  | rack1  |     | 3  | 1  | 1  |  compute  | 172.22.0.1  | eth1  | Ethernet  |  eth2  | 10.240.212.22  | Ethernet  |
|      | compute1  |  compute | 10.240.212.97 | 172.20.1.2 |  ibm  | USERID | PASSWORD | rack1 |     |  4  |  1  |  1 |  compute  | 172.22.0.1 |  eth1  | Ethernet  |  eth2  | 10.240.212.22  | Ethernet |

 - ***name***: server's hostname without domain name
 - ***nodetype***: type of the server, can be one of following node types: head,compute,io,gpu,login. 
 - ***immip***: IP address of the IMM in node.
 - ***hostip***: IP address for management network
 - ***machine_type***: type of the node server, valid value is listed in lico_3.x/etc/ nodes_image_conf.yaml. The images of the node can be found at lico_3.x/etc/ nodes_image.
 - ***ipmi_user***: IPMI account of the server.
 - ***ipmi_pwd***: IPMI password of the server.
 - ***belonging_rack***: the rack name which server is sitting. 
 - ***belonging_chassis***: the chassis name which server belongs to. The chassis name must be one of chassis listed in Part 5. If server does not belong to any chassis, leave it empty. 
 - ***location_u***: server localtion, if server belongs to a chassis, this value means the slot in chassis, otherwise it means u in rack.
 - ***width***: width of the server, full width is 1, half width is 0.5.
 - ***heigh***: height of the server, value is counted in u.
 - ***groups***: group name which the server belongs to, the value must be the one specified in Part 2. A server can belongs to multiple groups, separated the groups by comma.
 - ***application_network***: IP address for application network.
 - ***application_network_nic***: network card for application network.
 - ***application_network_type***: type of application network, can be either Ethernet or Infiniband.
 - ***public_network***: specify when nodetype is login, it is the IP address exposed to public network.
 - ***public _network_nic***: specify when nodetype is login, it is the card for public network.
 - ***public _network_type***: specify when nodetype is login, it is the public network type, the value can be either Ethernet or Infiniband.

#Appendix 2. 集群已经存在的情况下部署LiCO web portal
以下操作在头节点进行。
1.	确保所有节点OS（Rhel6.5/Rhel6.8/CentOS6.5/CenOS6.8）已经安装好
2.	确保节点间root账户可以无密码访问
3.	检查ganglia是否已经安装，如果没有的话，安装ganglia，访问ganglia的web页面，检查ganglia页面是否正确显示了集群中节点的监控信息。
4.	确保调度器（torque 或者 lsf）已经安装好。
5.	如果用户需要使用web vnc的功能，需要确保计算节点上安装有图形桌面（yum groupinstall "X Window System"； yum groupinstall "Desktop"），并安装vnc支持（yum install tiger*）.
6.	将lico_3.**/cluster_monitor_project/lico_monitor_agent目录拷贝到各个计算节点的/opt目录下.
7.	openLDAP，
在下面的情况下我们需要使用LiCO的脚本创建一个新的ldap环境：
--用户没有ldap的环境
--用户的ldap环境我们不能拿到管理员的密码，不能进行ldap用户的创建修改。
--用户的ladp环境使用了ssl
使用LiCO脚本创建一个新的ldap环境的过程如下：
[root@mgt packages/openldap]# tar -xvf openldap.tar
[root@mgt openldap]# cd openldap
[root@mgt openldap]# ./setup_openldap_server.sh 172.20.0.1(头节点的ip)
除了头节点外的其他节点需要像下面一样建立ldap的环境
[root@mgt openldap]# ./setup_openldap_client.sh compute1 172.20.0.1(头节点的ip)
[root@mgt openldap]# ./setup_openldap_client.sh compute2 172.20.0.1(头节点的ip)
ldap环境搭建好以后，如果集群用的调度器是torque，需要重启torque相关的服务：
头节点上torque相关的服务是：
Service trqauthd restart 
Service pbs_server restart
Service maui.d restart
计算节点和login节点上torque相关的服务是：
Service trqauthd restart
Service pbs_mom restart
8.	编辑lico_3.**/etc/conf.yaml ，修改LDAP相关内容
	编辑lico_3.**/etc/conf.yaml,
修改ldap_user_home_base为一个共享目录，如果是集群的ldap环境是原来就存在的，设置ldap_user_home_base为原来ldap用户的home目录的位置。如果集群的ldap环境是新搭建的，选取一个共享目录作为ldap_user_home_base目录，比如为/share1/users_home。
Note：如果使用NFS作为共享文件系统，那么所有节点在mount nfs的时候必须用v3,vers=3，例如：
[root@nfsclient  /]# mount -t nfs -o vers=3 nfsserverip:/share1  /share1
	编辑lico_3.**/etc/conf.yaml
修改user_rootdir为一个共享目录，这个共享目录不能与ldap_user_home_base相同，比如为/share1/users_root。
如果集群中有多个共享文件系统，修改cluster_sharedir为集群中的其它的共享目录，比如/share2。
当使用LiCO创建或将已经有的ldap用户user1导入LiCO的时候，LiCO会在/share1/ users_root创建一个user1的目录，在/share1/users_root/user1下面会创建一个home的软连接，连接到/share1/ users_home /user1，同时/share1/users_root/user1下面会创建一个share2的软连接，连接到/share2。/share1/ users_root /user1是user1在LiCO的web页面上看到的文件系统的最顶层目录。
最终user1的目录如下：
/share1/users_root/user1用户的根目录，也即web上显示的最顶层目录
/share1/users_root/hpcadmin/home 软连接到用户的home目录 
/share1/users_root/hpcadmin/share2软连接到sharedir
/share1/users_home/user1用户的home目录
/share2sharedir
	编辑lico_3.**/etc/conf.yaml, user_management下面的ldap相关的内容。
如ldap_server,ldap_manager,ldap_password等。 
Note：如果ldap的环境是使用LiCO的脚本搭建的，只需要修改ldap_server, ldap_manager,ldap_password不需要修改。
9.	编辑配置文件lico_3.**/etc/conf.yaml. 修改其中scheduler相关的内容。
选择使用的调度器,现在支持Torque和LSF。 #Note：这里需要保证root用户可以运行Torque和LSF的命令。如果queues_auto_get设置为True，那么queues的内容将被忽略，如果queues_auto_get设置为False，queues里面的内容必须设置。
10.	编辑配置文件lico_3.**/etc/conf.yaml. 修改cluster相关的内容。
修改domain为集群的domain。
11.	修改集群节点信息lico_3.**/etc/nodes.csv。
参考附录1编辑集群节点信息
12.	安装GUI portal的依赖包
•	[root@mgt lico_3.x]# python portal_package_install.py
Note：安装完后需要关闭当前session，然后再开一个session来跑下面的命令
13.	Web系统初始化
Note：
如果ldap环境是客户的环境，需要修改portal_init.yaml，确保portal_init.yaml中的username不能是客户的ladp中已经存在的用户，osgroup不能是客户的ldap中已经存在的组。
如果ldap环境是使用LiCO搭建，不需要对portal_init.yaml进行修改。
	[root@mgt lico_3.x]# vi portal_init.yaml
o	username: hpcadmin
o	password: Passw0rd
o	email: admin@admin.com
o	billgroup: default_bill_group
o	credits: 1000
o	osgroup: default_os_group
o	recreate_os_user: True
o	recreate_bill_group: True
o	recreate_os_group: True
	 [root@mgt lico_3.x]# python portal_init.py
	如果ldap环境是客户已经存在的环境，使用lico_3.**/bin/ load_users_and_groups_from_ldap.py将ldap中已经存在的用户导LiCO中，例如load_users_and_groups_from_ldap.py --adminusers admin1,admin2, 表示除了了admin1，admin2外的所有用户都被导入到lico中作为普通用户，admin1，admin2被导入到lico中作为管理员。
如果ldap环境是使用LiCO搭建，忽略此导入过程
14.	启动 GUI portal 的service
启动service：在启动service的shell中会不断有屏幕输出，所以最好在一个一直存在的shell session里面启动这个service。
	比如可以在screen里面启动这个service，screen -help可以查看screen命令的使用。[root@mgt lico_3.x]# screen
[root@mgt lico_3.x]# ./lico start
这时候如果关闭这个shell（不要用命令exit退出这个shell，这样的话这个screen也不存在了），screen和shell还在，可以用screen -r重新进入这个screen，可以看到原来的shell还在。
	或者在vnc图形桌面里面开一个shell，在这个shell里面启动service。
停止service：命令为[root@mgt lico_3.x]# ./lico stop
15.	查看GUI portal是否安装配置成功
http://172.20.0.1:8080/login/ 	hpcadmin/Passw0rd
Note: 如果GUI portal上显示的node的状态不正确，通过route命令查看ganglia是否监控在正确的网口上， ganglia应该监控在管理网的网口上。如果不在，在每台机子上通过ip route add 239.2.11.71 dev eth0（管理网口）来设置，然后在所有节点重启gmond的服务service gmond restart。

#附录3. ThinkServer Support（只针对ThinkServer）
ThinkServer的整体流程和RackServer一样，请参考RackServer的流程从第1大步开始，在流程中下面两点是ThinkServer与RackServer不一样的地方。
1. 获取ThinkServer的mac地址（整体流程的第4大步中的第4小步）
获取thinkserver的mac地址不能通过bin下面提供的./discover_node_macs_using_imm.py来获取mac地址。 Thinkserver获取mac地址的方式如下：
打开两个shell，shell A， shell B。
-- 在shell A里面
nodediscoverstart noderange=c01n[001-010]
-- 在shell B里面
运行LiCO的bin下面的thinkserver_bootmanager.py来设置节点从pxe启动：
如果这些节点的boot是uefi模式，使用下面的命令，正常情况下这些节点应该是uefi模式。
./thinkserver_bootmanager.py --nodes “c01n[001-010]” --bootingfrom “uefi-pxe”
如果这些节点的boot是legacy模式，使用下面的命令
./thinkserver_bootmanager.py --nodes “c01n[001-010]” --bootingfrom “legacy-pxe”

开始监控mac发现的结果：
cat /dev/null > /var/log/messages
tail -f /var/log/messages | grep DHCPDISCOVER
-- 在shell A里面
rpower c01n001 on，然后观察shellB中是否有新mac拿到，有mac拿到后rpower c01n001 off， 然后开始下一个节点 c01n002 
rpower c01n002 on，然后观察shellB中是否有新mac拿到，有mac拿到后rpower c01n002 off，然后开始下一个节点 c01n003 
。。。。。。。
rpower c01n010 on，然后观察shellB中是否有新mac拿到，有mac拿到后rpower c01n010 off

最后将拿到的mac地址写入xcat的数据库中，tabedit mac，像vi一样编辑保存。
node,interface,mac,comments,disable
"io01",,"40:F2:E9:75:35:78",,

2. ThinkServer节点的部署（整体流程的第4大步中的第5小步）
启动两个shell， shell A， shell B
-- shell A中，和RackServer一样通过调用LiCO的bin下面的deploy_nodes.py来部署节点，例如deploy_nodes.py c01n0[01-010] 。
-- 当在shellA中看到开始deploy_nodes.py 开始monitor deploy status的时候，在shell B中运行LiCO的bin下面的thinkserver_bootmanager.py来设置这些节点从pxe启动：
如果这些节点的boot是uefi模式，使用下面的命令，正常情况下这些节点应该是uefi模式。
./thinkserver_bootmanager.py --nodes “c01n[001-010]” --bootingfrom “uefi-pxe”
如果这些节点的boot是legacy模式，使用下面的命令
./thinkserver_bootmanager.py --nodes “c01n[001-010]” --bootingfrom “legacy-pxe”
-- 在shell B中重启这些节点
rpower c01n0[01-010] off
rpower c01n0[01-010] on 
-- 在shell A中观察deploy的过程

#附录4. 通过BMC设置bootorder (可选)
ssh 登录到节点的BMC，通过asu set BootOrder.BootOrder “CD/DVD Rom=USB Storage=Hard Disk 0=Legacy Only=PXE Network”来设置bootorder。

