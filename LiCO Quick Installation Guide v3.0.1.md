LiCO Quick Installation Guide v3.0.1
===============================

### Table of contents

[TOC]

#1. Download the Package

##1.	Download OS and Installation files下载操作系统安装文件
LiCO support CentOS 6.5/6.8 and RedHat 6.5/6.8. If required system is CentOS, please download both CentOS-*-DVD1.iso ( for head node installation) and CentOS-*-DVD.iso ( for LiCO yum source)  from team's FTP.  
LiCO支持的操作系统版本是CentOS 6.5/6.8和Redhat 6.5/6.8。如果要安装的操作系统是CentOS, 从开发team的ftp上需要下载两个CentOS的iso文件，其中CentOS-*-DVD1.iso用来安装头节点的操作系统，CentOS-*-DVD.iso用来在LiCO中作为yum 源。

>**System Package URL**
>
>ftp://10.240.208.41/packages/CentOS-6.5-x86_64-bin-DVD1.iso
>ftp://10.240.208.41/packages/CentOS-6.5-x86_64-DVD.iso
>ftp://10.240.208.41/packages/CentOS-6.8-x86_64-bin-DVD1.iso
>ftp://10.240.208.41/packages/CentOS-6.8-x86_64-DVD.iso

If user is going to install RedHat, user just needs to download one ISO file, since both head node and yum source are packaged in a single ISO file.  
如果要安装的操作系统是redhat，需要用户提供redhat的iso文件，安装头结点的iso文件和作为LiCO的yum源的文件是同一个iso，因此只需要一个iso文件。

##2.Download LiCO 下载LiCO

>**LiCO  Package URL 下载地址**
>
>ftp://10.240.208.41/Releases/ 

#2. If install on an existed Cluster, please reference Appendix 2. 如果集群已经存在，请参考附录2

LiCO support installing Web server on an existed cluster, but it still needs to meet some minimum requirements, please reference Appendix 2 for more information.  
LiCO支持集群已经存在的情况下部署LiCO的web界面。当然已经存在的集群还是要满足一定的要求，请直接参考附录2。

#3. Config IMM IP and create IPMI account for nodes in cluster 配置集群中节点的IMM IP并创建IPMI账号

Manually config IMM IP and Create IPMI account. 
手动配置IMM IP并创建IPMI账号

>**NOTES:**
>
>1. IMM and IPMI account is the same for Rack Server and Next Scale, so user does not need to create the account for each of them. But for Flex server, user needs to create IPMI account separately. 对于RackServer和NextScale，IMM的账号和IPMI账号是一个账号，因此不需要单独创建IPMI账号，但是对于Flex Server来说，IPMI账号需要单独创建。
>2. If Flex Server is setup up with IMM IP and IPMI account, we will treat Flex Server as Rack Server.  Flex Server设置过imm的ip和创建ipmi账号以后，我们后续是将Flex server当做Rack Server 一样看待的。

#4. Install Head Node 安装头节点
- Step 0: Config raid of Head node, Raid1 is suggested. Skip if the raid is already reconfigured.  配置头结点的Raid，建议为raid1，如果已经配置，请忽略。
- Step 1: Install OS (CentOS 6.5/6.8 or RedHat 6.5/6.8) for Head node. 安装头节点OS( Centos6.5/6.8或者Redhat6.5/6.8）

>**NOTE:** 
>1. If you are using CentOS, please download image CentOS-*-DVD1.iso to install Head node.  如果Centos，使用下载的CentOS-*-DVD1.iso安装头节点的操作系统。
>2. Please select English as the System language. 头结点系统请选择英文。

- Step 2:  Config hostname of Head node. Open "/etc/sysconfig/network" and change hostname to "mgt". 设置头节点的hostname, 在/etc/sysconfig/network中设置头节点hostname为mgt

- Step 3: Setup IP of Head node's network card. There will be 3 IP addresses for a Head node.  设置头节点网卡的ip地址，头节点包括3个ip：
Management Network (OS) IP, like 172.20.0.1
Application Network IP, like 172.22.0.1
Public Network IP (optional, for remote access to Head node). 
  
用于management-network（os）的ip，比如172.20.0.1
用于application-network的ip,比如172.22.0.1
用于public-network的ip地址，public ip是可选的，可以不设置，public ip用于在远程访问头结点。

Edit "/etc/sysconfig/network-scripts/eth*" or "/etc/sysconfig/network-scripts/ib*" to setup network card. 
编辑/etc/sysconfig/network-scripts/eth* 或者 /etc/sysconfig/network-scripts/ib*来设置ip地址。
[RUI] Errata, In Red Hat Linux, it uses files ifcfg-ethX for different network interface configuration. 
[RUI] What is IB? What is the difference>

    ONBOOT=yes
    NM_CONTROLLED=no
    BOOTPROTO=static
    IPADDR=10.240.212.28
    NETMASK=255.255.255.0
    GATEWAY=10.240.212.1
please use command below to restart network service. 
> \#service network restart
IP地址设置好以后重启网络服务：service network restart。
>**NOTE:**
>If using IB for Application network configuration, config will show ***fail*** when restart network service. You see this error because IB driver and service is not well installed, LiCO will take care of them later, So don't worry about it.   application-network是IB的时候，设置application-network的ip后，在service network restart的时候，这个ip会设置失败，这个不用担心，这是由于IB的驱动和服务没有安装，后续LiCO会自动的将这些安装好。

- Step 4:  Copy LiCO package and extract it to a directory. 将LiCO拷贝到OS的某一目录下并解压
- Step 5:  copy OS ISO image to LiCO folder, e.g. "/lico_3.x/packages/os". 将OS的ISO文件拷贝到LiCO的目录下/lico_3.x/packages/os

>**NOTE:**
>If installing CentOS, please download CentOS-*-DVD.iso to LiCO folder, e.g. "/lico_3.x/packages/os".如果Centos，将下载的CentOS-*-DVD.iso拷贝到/lico_3.x/packages/os目录下

- Step 6: Modify the configuration file 修改配置文件
    
    [root@mgt ~]# cd /lico_3.x/etc/
    [root@mgt etc]# vi cluster.conf 
    DOMAIN="hpc.com"
    OS="centos6.5" #OS version
    OSISO="CentOS-6.5-x86_64-DVD.iso" # ISO image filename 

>**NOTE:**
>Both CentOS6.5/6.8 and Red Hat 6.5/6.8 is supported by LiCO. If you are using RedHat, use following configuration. 当前支持 Red Hat 6.5/6.8 and CentOS 6.5/6.8。如果是Redhat，配置如下
>OS="rhels6.5"#OS version
>OSISO="RHEL6.5-20131111.0-Server-x86_64-DVD1.iso"  #ISO Image filename

- Step 7: Update Kernel of Head Node 更新headnode的kernel
>[root@mgt ~]# cd /lico_3.x/setup/
>[root@mgt ~]#./0_upgrade_head_node_kernel.sh（don't execute thie command through ssh, execute it locally. ）
>**Reboot重启机器**

- Step 8: Config Network 配置网络
>**NOTE:**
>There is no BMC network configuration in following config sample. If you are using BMC network, please make sure your Head node has access to BMC network. 在下面这个配置文件中没有定义BMC的网络，对BMC的网络只有一个要求那就是头结点能访问BMC的网络
>If there is no "Login" node in cluster, there is no need to config "public network" 如果集群没有规划登录节点，下面配置文件中的public网络不需要设置。

>[root@mgt ~]# cd /lico_3.x/etc/
>[root@mgt etc]# vi networks.conf   

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
    
- Step 9: Setup Head Node
>[root@mgt setup]# ./1_head_node_setup.sh （don't execute thie command through ssh, execute it locally.）
>[root@mgt setup]# ssh mgt（ssh Head node itself）
Check the configuration of Head node: 检查头节点setup是否成功
>[root@mgt setup]# source /etc/profile.d/xcat.sh
>[root@mgt setup]# cd /lico_3.x/bin/
>[root@mgt bin]# ./service_manager.py --check head

【是否需要让用户查看一些点，如果不ok就算失败？或者不存在这种情况】
[IMHO] we should automatically check all nodes we config'ed, asynchronously. If there is any failure or problem, report to admin console.  

#5.	Deploy Cluster部署集群
- 修改集群节点配置文件
参考附录1
- 添加节点到xcat
>[root@mgt bin]# ./add_nodes.py
 
- 配置节点的RAID
ssh到节点的imm, 用如下的命令来配置节点的raid，如果节点已经配置过raid，可以不配置。

>system> storage -config vol -add -R 1 -D disk[9-0]:disk[9-1] -target ctrl[9]
>system> storage -list volumes

>**NOTE:**
>这个命令只在m5的机子上支持，如果这个命令机子不支持，那么需要手动通过UEFI配raid。

- 获取节点的mac地址（Thinkserver参考附录3的ThinkServer Support方法）
>[root@mgt bin]# ./discover_node_macs_using_imm.py --node "nodes1,nodes2,node[3-5]" --immuser "USERID" --immpwd "Passw0rd"


命令获取节点的所有mac地址，命令执行结果保存在bin下面的macs.txt , 打开macs.txt可以看到默认的 “selectednic” 是第一个nic，编辑修改selectednic来为安装os的时候需要的nic(与头结点managemen-network的nic连通的nic)。

    Node1;selectednic=PXE.NicPortMacAddress.1;PXE.NicPortMacAddress.1=00:90:FA:0D:39:3C;PXE.NicPortMacAddress.2=00:90:FA:0D:39:40;PXE.NicPortMacAddress.3=00:90:FA:0D:39:44;PXE.NicPortMacAddress.4=00:90:FA:0D:39:48
    Node2;selectednic=PXE.NicPortMacAddress.1;PXE.NicPortMacAddress.1=00:00:C9:F5:6D:08;PXE.NicPortMacAddress.2=00:00:C9:F5:6D:0C;PXE.NicPortMacAddress.3=00:00:C9:F5:6D:10;PXE.NicPortMacAddress.4=00:00:C9:F5:6D:14

编辑完 macs.txt后，通过如下命令将选中的nic添加到xcat的数据库表中。
>[root@mgt bin]# ./set_node_mac_in_xcat.py -c "macs.txt"

- 部署节点（Thinkserver参考附录3的ThinkServer Support方法）
>[root@mgt bin]# ./deploy_nodes.py "node1,node2,node[3-5]"
>
>**NOTE:**
>如果命令执行碰到类似如下的错误，按ctrl c结束，然后重新跑就可以了。

Error: Unable to find an IP for c01n001-data in hosts table or via system lookup (i.e. /etc/hosts)

>**NOTE:**
>
>计算节点部署的时间较长，需要在系统启动过程中（booting）安装很多软件包，请耐心等待。
>
>部署后的节点的root账号是root/Passw0rd

节点部署完成后，可以通过如下命令确认节点部署成功与否

> [root@mgt bin]# ./service_manager.py  --check torque [root@mgt bin]#
> ./service_manager.py  --check ganglia [root@mgt bin]#
> ./service_manager.py  --check clusterping [root@mgt bin]#
> ./service_manager.py  --check mpi

登录Ganglia，查看ganglia是否成功

> http://172.20.0.1/ganglia

- 对节点的application nic进行重命名（可选）

> **NOTE:** 如果不同节点application网络对应的网口名字本来就是相同的，比如都是eth1等，就可以忽略此步骤。


不同的节点中application网络对应的网口名字可能不相同，我们需要将所有节点中application网络对应的网口改成同样的名字，以便有的高性能作业可以指定使用application网络的网口名。

重命名非头节点的application网络的网口为统一的名字:

> [root@mgt bin]# ./rename_node_nic.py --node nodes1,nodes2,node[3-5]
> --nictype ib --old ib0 --new ibdata 重命名头节点的application网络的网口为统一的名字: [root@mgt bin]# ./rename_node_nic.py --node localhost --nictype ib
> --old ib0 --new ibdata

通过xcat命令rpower all reset 来重启除头节点外的所有节点
Reboot命令重启头节点

>**NOTE:**
>
>- 头节点如果重启的时候hang住，请手动重启头节点。
>- 机子启动完成后请检查一下是否生效。

#6. 安装分布式文件系统lustre
如果集群要使用的分布式文件系统不是lustre，请忽略此步骤。
如果采用lustre文件系统，请遵循以下步骤：

### 1. 修改配置文件
>[root@mgt setup]# vi ../etc/lustre.conf

    file-system userfs {
    	interface       ibdata	 #application nic
    	shareddir       /share  #lustre部署好以后，提供的共享目录名称，不需要事先创建
        datanet_type	 Infiniband  #Infiniband or Ethernet for interface
    	datanet_hostnamemap io01:io01-data,io02:io02-data	#the map between normal nodename and nodename for interface
    	mds {
    	   io01    /dev/sdb1    #硬盘，在io01上通过fdisk或parted查看或分区
    io02    /dev/sdb1	
    	};
    	oss {
    		io01    /dev/sdb2
    io02    /dev/sdb2
    	};
         #### The following items is NOT required to input if no HA required.	###
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

###2. 安装lustre
>[root@mgt bin]# ./lustre_setup.py

安装后检查是否安装成功。df –h 查看/share是否已经挂载。

# 7.	安装ldap来管理集群用户
LiCO集群用户管理采用ldap来实现
用ldap来管理集群用户又分为两种：1.在hpc集群中使用我们的脚本搭建ldap环境， 2使用已经存在的外部ldap。我们推荐第一种方式，使用lico的脚本在hpc集群中搭建ldap环境：

### 1. 使用脚本创建ldap服务：

> [root@mgt packages/openldap]# tar -xvf openldap.tar [root@mgt
> openldap]# cd openldap [root@mgt openldap]# ./setup_openldap.sh
> 172.20.0.1(头节点的ip)


至此头节点上已经安装ldap的服务，同时集群中所有节点上安装了ldap的客户端。

> **Note：**安装好ldap环境以后需要重启torque相关的服务。
> [root@mgt  bin]#./service_manager.py --restart torque

### 2. 修改配置文件 /lico_3.x/etc/conf.yaml
>[root@mgt etc] vi conf.yaml

配置如下：

    user_management_method: ldap
    ldap_user_home_base: /share1/users_home
    ldap_server: 172.20.0.1(头节点的ip)
    user_rootdir: /share1/users_root
    cluster_sharedir: [“/share2”]

user_home_base：user_home_base是指ldap用户的home所在的目录，user_home_base必须是一个能共享访问的目录。
user_rootdir：user_rootdir是ldap用户的根目录，也即用户在web页面上看到的顶级目录。user_rootdir也必须是一个能共享访问的目录。
cluster_sharedir：cluster_sharedir设置集群中除了user_home_base和user_rootdir以外的其他的共享目录。

> **Note：**以上这些共享目录必须在系统中已经存在，同时要注意，如果是使用的nfs作为共享文件系统，必须使用vers=3，比如：mount -t nfs -o  vers=3 nfsserverip:/share  /share

例如系统中创建一个用户hpcadmin，其目录如下：

/share1/users_root/hpcadmin用户的根目录，也即web上显示的最顶层目录
/share1/users_root/hpcadmin/home 软连接到用户的home目录 
/share1/users_root/hpcadmin/share2软连接到sharedir
/share1/users_home/hpcadmin用户的home目录
/share2sharedir

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

#附录1. 集群节点配置文件nodes.csv的编辑
将头结点上文件/lico_3.*/etc/nodes.csv拷贝到本地笔记本上用excel打开进行编辑, 编辑好以后覆盖头节点上的/lico_3.*/etc/nodes.csv

![enter image description here](http://excel.jpg)

第一部分填写机房信息（必填）： 现在只支持填写一个机房。
-name：机房名字。
-location_description ：机房位置描述。
第二部分填写集群的逻辑组（必填）：
-name：逻辑组的名字。
第三部分填写机房的行信息（必填） ： 只需填写集群节点所在的行。
-name：行的名字。
-index：任意正整数，index不能重复。
-belonging_room：行所在的机房这里填写的值必须在机房信息里面的name里面存在。
第四部分填写机架信息（必填）：只需填写集群节点占用的机架。
-name：机架的名字。
-belonging_row：机架所属的行，这里填写的值必须在机房行信息的name里面存在。
-column：机架所在的列，任意正整数，同一行内这个值不能重复。
第五部分填写chassis信息(选填)：如果集群中有chassis需要填写chassis信息。
-name：chassis名字。
-belonging_rack：chassis所属机架，这里填写的值必须在机架信息的name里面存在。
-location：chassis的位置，表示chassis的底端在机架的第几u，值应该在 1-42之间。
-machine_type： chassis的类型。可选类型为值有Flex_Chassis或Nextscale_Chassis。Nextscale的机器用Nextscale_Chassis, Flex的机器用Flex_Chassis。
第六部分填写交换机信息(选填)：通常这里不用填任何信息。
第七部分填写服务器信息(必填)：
-name：服务器的hostname，不带域名。
-nodetype：服务器的类型，可选类型为head,compute,io,gpu,login中的一种，head表示这个节点是头节点，compute表示这个节点是计算节点，gpu表示这个节点是gpu节点，io表示这个节点是io节点，login表示这个节点是登录节点。
-immip：节点imm的ip地址。
-hostip：节点管理网络的ip地址。
-machine_type：服务器型号，可用值列在lico_3.x/etc/ nodes_image_conf.yaml中，这些服务器型号对应的图片可以在lico_3.x/etc/ nodes_image下查看。
-ipmi_user ：服务器的ipmi账号。
-ipmi_pwd ：服务器的ipmi密码。
-belonging_rack：服务器所属的机架，这里填写的值必须在机架信息的name里面存在。
-belonging_chassis：服务器所属的chassis，如果机器属于某一chassis，这里需要填写chassis的名字，填写的值必须在chassis信息的name里面存在。如果服务器不属于chassis，这个值可以空着。
-location_u：服务器的位置，如果服务器属于某一chassis，这里的值表示机器在chassis里面的槽位，如果机器不属于chassis，这里的值表示机器在机架中的u位置。
-width：服务器的宽度，全宽是1，半宽是0.5。
-heigh：服务器的高度，值表示高度是多少u。
-groups：服务器所属的组，这里填写的值必须在组信息的name里面存在。一个机器可以属于多个组，多个组之间用逗号分开。
-application_network ：服务器的数据网的ip地址。
-application_network_nic ：服务器的数据网的网卡。
-application_network_type ：服务器的数据网的类型，Ethernet或者Infiniband。
-public_network ：只有nodetype为login的节点需要填写，表示服务器的公网的ip地址。
-public _network_nic : 只有nodetype为login的节点需要填写，表示服务器的公网的网卡。
-public _network_type : 只有nodetype为login的节点需要填写，表示服务器的公网的类型，Ethernet或者Infiniband。

#附录2. 集群已经存在的情况下部署LiCO web portal
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

