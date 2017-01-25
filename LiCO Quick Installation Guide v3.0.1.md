LiCO Quick Installation Guide v3.0.1
===============================

## Table of contents

[TOC]

#1. Prerequisites

##OS

LiCO supports both CentOS 6.5/6.8 and RedHat 6.5/6.8, download the OS you prefer. 

**RedHat:**
https://www.redhat.com/en

**CentOS:**
https://www.centos.org/download/ 


##LiCO package

**LiCO  Package**
ftp://10.240.208.41/Releases/ 

When finished downloading, please extract LiCO to a local folder e.g. your home folder. (we will use the home folder as the default folder for example. Please note that you can put LiCO package anywhere you want.)

##3rd party packages

LiCO utilizes many 3rd party software, please download and put them into corresponding "packages" folder (e.g. "~/lico_3.x/packages/confluent/" for confluent package) before installing LiCO. 

>**NOTE:**
>Some of the softwares requires local compilation, you need to compile them and put the compiled package into "packages" folder. 

### confluent
Download packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/confluent/". 

***pyparsing-2.1.0-1.noarch.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/

***python-crypto-2.6.1-3.x86_64.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/

***python-dnspython-1.14.0-1.noarch.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/

***python-ecdsa-0.13-1.noarch.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/

***python-eventlet-0.18.4-2.noarch.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/

***python-greenlet-0.4.9-1.x86_64.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/

***python-paramiko-1.16.0-1.noarch.rpm***
http://xcat.org/lenovo/16a/downloads/el6/x86_64/


###ganglia
Download ganglia packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/ganglia/". 

***ganglia-3.6.1.tar.gz***
https://sourceforge.net/projects/ganglia/files/ganglia%20monitoring%20core/3.6.1

***ganglia-web-3.6.2.tar.gz***
https://sourceforge.net/projects/ganglia/files/ganglia-web/3.6.2/

***rrdtool-devel-1.3.8-6.el6.x86_64.rpm***
http://rpm.pbone.net/index.php3?stat=26&dist=74&size=27912&name=rrdtool-devel-1.3.8-6.el6.x86_64.rpm

***rrdtool-devel-1.3.8-7.el6.x86_64.rpm***
http://mirror.centos.org/centos/6/os/x86_64/Packages/

***libconfuse-2.7-4.el6.x86_64.rpm***
https://dl.fedoraproject.org/pub/epel/6/x86_64/

***libconfuse-devel-2.7-4.el6.x86_64.rpm***
https://dl.fedoraproject.org/pub/epel/6/x86_64/

ganglia requires rpm-build to do local compilation and packaging, if you have not install rpm-build, follow commands below:

    [root@mgt ~]# yum install rpm-build

After installation of rpm-build, you can find folder rpmbuild under root folder, enter into rpmbuild folder and you will see folders named SPECS、SOURCES、RPMS、SRPMS、BUILD、BUILDROOT, if not, create these folder by using mkdir.

Follow command below to compile ganglia:

    [root@mgt ~]# yum install rrdtool 
    [root@mgt ~]# cd ..
    [root@mgt /]# cd ~/lico_3.0.1/packages/ganglia
    [root@mgt ganglia]# rpm -ivh rrdtool-devel-1.3.8-7.el6.x86_64.rpm
    [root@mgt ganglia]# yum install apr-devel 
    [root@mgt ganglia]# yum install apr-util-devel
    [root@mgt ganglia]# rpm -ivh libconfuse-2.7-4.el6.x86_64.rpm
    [root@mgt ganglia]# rpm -ivh libconfuse-devel-2.7-4.el6.x86_64.rpm
    [root@mgt ganglia]# yum install pcre*
    [root@mgt ganglia]# yum install zlib*
    [root@mgt ganglia]# yum install gcc-c++
    [root@mgt ganglia]# yum install libpng-devel.x86_64
    [root@mgt ganglia]# yum install libart_lgpl-devel.x86_64
    [root@mgt ganglia]# yum install python-devel.x86_64
    [root@mgt ganglia]# yum install autoconf.noarch
    [root@mgt ganglia]# yum install automake.noarch
    [root@mgt ganglia]# yum install libtool.x86_64
    [root@mgt ganglia]# yum install freetype-devel.x86_64
    [root@mgt ganglia]# tar -xvf ganglia-3.6.1.tar.gz
    [root@mgt ganglia]# cd ganglia-3.6.1
    [root@mgt ganglia-3.6.1]# cp ../ganglia-3.6.1.tar.gz  /root/rpmbuild/SOURCES
    [root@mgt ganglia-3.6.1]# rpmbuild -ba ganglia.spec
    [root@mgt ganglia-3.6.1]# cd ../../
    [root@mgt ~]# cd rpmbuild/RPMS/x86_64
    [root@mgt x86_64]# cp ganglia-devel-3.6.1-1.x86_64.rpm ganglia-gmetad-3.6.1-1.x86_64.rpm  ganglia-gmond-3.6.1-1.x86_64.rpm ganglia-gmond-modules-python-3.6.1-1.x86_64.rpm ~/lico_3.0.1/packages/ganglia


Follow command below to compile Ganglia-web:

    [root@mgt ~]# cd ..
    [root@mgt /]# cd opt/lico_3.0.1/packages/ganglia
    [root@mgt ganglia]# yum install httpd
    [root@mgt ganglia]# yum install php*
    [root@mgt ganglia]# tar -xvf ganglia-web-3.6.2.tar.gz
    [root@mgt ganglia]# cd ganglia-web-3.6.2
    [root@mgt ganglia-web-3.6.2]# cp ../ganglia-web-3.6.2.tar.gz /root/rpmbuild/SOURCES/
    [root@mgt ganglia-web-3.6.2]# cp apache.conf /root/rpmbuild/SOURCES/
    [root@mgt ganglia-web-3.6.2]# rpmbuild -ba ganglia-web.spec
    [root@mgt /]# cd /root/rpmbuild/RPMS/noarch
    [root@mgt noarch]# cp ganglia-web-3.6.2-1.noarch.rpm ~/lico_3.0.1/packages/ganglia

>**Note: **
>All the rpm packages are available in /root/rpmbuild/RPMS/noarch/


###cuda
Download cuda package through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/gpu/".
 
***cuda_6.5.14_linux_64.run***
https://developer.nvidia.com/cuda-toolkit-65


###torque

Download torque package through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/torque/". 

***torque-5.0.1.tar.gz***
https://github.com/adaptivecomputing/torque/tree/5.0.1

Follow commands below to compile torque:

    [root@mgt ~]# yum install openssl*
    [root@mgt ~]# yum install libxml*
    [root@ mgt ~]# yum install boost-devel
    [root@ mgt ~]# yum install gcc*
    [root@ mgt ~]# cd ~/lico_3.0.1/packages/torque/
    [root@mgt torque]# cp torque-5.0.1.tar.gz /root/rpmbuild/SOURCES/
    [root@mgt torque]# tar -xvf torque-5.0.1.tar.gz
    [root@mgt torque]# cd torque-5.0.1
    [root@mgt torque-5.0.1]# chown root:root torque.spec 
    [root@mgt torque-5.0.1]# rpmbuild -ba torque.spec
    [root@mgt torque-5.0.1]# cd /root/rpmbuild/RPMS/x86_64/
    [root@mgt x86_64/]# rpm -ivh torque-5.0.1-1.adaptive.el6.x86_64.rpm
    [root@mgt x86_64/]# rpm -ivh torque-server-5.0.1-1.adaptive.el6.x86_64.rpm
    [root@mgt x86_64/]# rpm -ivh torque-devel-5.0.1-1.adaptive.el6.x86_64.rpm
    [root@mgt x86_64/]# cp torque-5.0.1-1.adaptive.el6.x86_64.rpm torque-client-5.0.1-1.adaptive.el6.x86_64.rpm torque-devel-5.0.1-1.adaptive.el6.x86_64.rpm torque-scheduler-5.0.1-1.adaptive.el6.x86_64.rpm torque-server-5.0.1-1.adaptive.el6.x86_64.rpm  ~/lico_3.0.1/packages/ganglia


###maui
Download maui package through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/maui/". 

***maui-3.3.1.tar.gz***
http://www.adaptivecomputing.com/products/open-source/maui/

>**NOTE:**
>to compile maui, please compile torque first. 

Follow commands below to compile maui:

    [root@mgt /]# cd ~/lico_3.0.1/packages/maui
    [root@mgt maui]# cp maui-3.3.1.tar.gz /root/rpmbuild/SOURCES/
    [root@mgt maui]# tar -xvf maui-3.3.1.tar.gz
    [root@mgt maui]# cd maui-3.3.1
    [root@mgt maui-3.3.1]# vi maui.spec
    ***********************************************************
    Summary: MAUI
    Name: maui
    Version: 3.3.1
    URL: http://www.adaptivecomputing.com/products/open-source/maui/
    License: BSD
    Release: 1
    Vendor: Lenovo system x solution team
    Group: System Environment/Base
    Source: %{name}-%{version}.tar.gz
    BuildRoot: %{_tmppath}/%{name}-%{version}-buildroot
    Obsoletes: maui
    Prefix: %{_prefix}
    
    %description
    maui from lenovo
    
    %prep
    %setup -n %{name}-%{version}
    
    %build
    %configure --with-pbs --with-spooldir=/var/spool/torque
    %{__make}
    
    %install
    # Flush any old RPM build root
    %__rm -rf $RPM_BUILD_ROOT
    %{__mkdir_p} $RPM_BUILD_ROOT/%{_initddir}
    cp ./etc/maui.d $RPM_BUILD_ROOT/%{_initddir} 
    install -d $RPM_BUILD_ROOT/
    %{__make} install BUILDROOT=$RPM_BUILD_ROOT/$BuildRoot
    
    
    %files
    #%defattr(-,root,root)
    %{_bindir}/*
    %{_sbindir}/maui
    %{_includedir}/moab.h
    %{_usr}/lib/libmcom.a
    %{_usr}/lib/libmoab.a
    /var/spool/torque/*
    %{_initddir}/maui.d
    
    %clean
    %__rm -rf $RPM_BUILD_ROOT
    
    %post
    
    %changelog
    ***********************************************************
    Note: Create the maui.spec mually
    
    [root@mgt maui-3.3.1]# rpmbuild -ba maui.spec 
    [root@mgt maui-3.3.1]# cd /root/rpmbuild/RPMS/x86_64/


###mpich
Download mpich package through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/mpich/". 

***mpich-3.1.3.tar.gz***
http://www.mpich.org/static/downloads/3.1.3/


###mvapich
Download mvapich package through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/mvapich/". 

***mvapich2-2.0.1.tar.gz***
http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.0.1.tar.gz


###openmpi
Download openmpi package through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/openmpi/". 

***openmpi-1.8.4.tar.gz***
https://www.open-mpi.org/software/ompi/v1.8/


###OS for other Nodes
Download the OS you need through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/os/". 

***Redhat Enterprise Linux***
https://www.redhat.com/en

***CentOS***
https://www.centos.org/download/ 

###xcat
Download xcat packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/xcat/". 

***xcat-core-2.8.5.tar.bz2***
https://sourceforge.net/projects/xcat/files/xcat/2.8.x_Linux/

***xcat-dep-201612051642.tar.bz2***
https://xcat.org/files/xcat/xcat-dep/2.x_Linux/?C=M;O=D

open constants config file, find `XCATDEP`, change it to `xcat-dep-201612051642.tar.bz2`

###postgreSQL
Download postgresql packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/portal_deps/postgresql/."
 
***postgresql84-8.4.18-1PGDG.rhel6.x86_64.rpm:***
https://yum.postgresql.org/8.4/redhat/rhel-6-x86_64/

***postgresql84-contrib-8.4.18-1PGDG.rhel6.x86_64.rpm:***
https://yum.postgresql.org/8.4/redhat/rhel-6-x86_64/

***postgresql84-devel-8.4.18-1PGDG.rhel6.x86_64.rpm:***
https://yum.postgresql.org/8.4/redhat/rhel-6-x86_64/

***postgresql84-libs-8.4.18-1PGDG.rhel6.x86_64.rpm:***
https://yum.postgresql.org/8.4/redhat/rhel-6-x86_64/

***postgresql84-plpython-8.4.18-1PGDG.rhel6.x86_64.rpm:***
https://yum.postgresql.org/8.4/redhat/rhel-6-x86_64/

***postgresql84-server-8.4.18-1PGDG.rhel6.x86_64.rpm:***
https://yum.postgresql.org/8.4/redhat/rhel-6-x86_64/

###Lustre
Download lustre packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/lustre/"
> Please **note** that lustre packages are different for server and client, we need to put there into different folder. 

For Redhat 6.5 Lustre client files:
**~/lico_3.x/packages/lustre/client/:**
***lustre-client-2.5.3-2.6.32_431.23.3.el6.x86_64.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/client/RPMS/x86_64/

***lustre-client-modules-2.5.3-2.6.32_431.23.3.el6.x86_64.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/client/RPMS/x86_64/

***kernel-firmware-2.6.32-431.23.3.el6_lustre.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/server/RPMS/x86_64/

***kernel-2.6.32-431.23.3.el6.x86_64.rpm:***
https://rhn.redhat.com/errata/RHSA-2014-0981.html

**server:**
***e2fsprogs-libs-1.42.12.wc1-7.el6.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/e2fsprogs/1.42.12.wc1/el6/RPMS/x86_64/

***e2fsprogs-1.42.12.wc1-7.el6.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/e2fsprogs/1.42.12.wc1/el6/RPMS/x86_64/

***libss-1.42.12.wc1-7.el6.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/e2fsprogs/1.42.12.wc1/el6/RPMS/x86_64/

***libcom_err-1.42.12.wc1-7.el6.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/e2fsprogs/1.42.12.wc1/el6/RPMS/x86_64/

***kernel-firmware-2.6.32-431.23.3.el6_lustre.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/server/RPMS/x86_64/

***kernel-2.6.32-431.23.3.el6_lustre.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/server/RPMS/x86_64/

***lustre-osd-ldiskfs-2.5.3-2.6.32_431.23.3.el6_lustre.x86_64.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/server/RPMS/x86_64/

***lustre-modules-2.5.3-2.6.32_431.23.3.el6_lustre.x86_64.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/server/RPMS/x86_64/

***lustre-2.5.3-2.6.32_431.23.3.el6_lustre.x86_64.x86_64.rpm:***
https://downloads.hpdd.intel.com/public/lustre/lustre-2.5.3/el6/server/RPMS/x86_64/

For Redhat 6.8: 
Since there is no existed packages for Redhat 6.8, you need to download and compile it. Follow the instructions below:

**client:**

**server:**


###Infinite Band
Download infinite band packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/ib/"

***MLNX_OFED_LINUX-2.2-1.0.1.1-rhel6.5-x86_64.iso:***
http://www.mellanox.com/page/mlnx_ofed_eula?mtag=linux_sw_drivers&mrequest=downloads&mtype=ofed&mname=MLNX_OFED_LINUX-2.2-1.0.1.1-rhel6.5-x86_64.iso

***MLNX_OFED_LINUX-3.3-1.0.4.0-rhel6.8-x86_64.iso:***
http://www.mellanox.com/page/mlnx_ofed_eula?mtag=linux_sw_drivers&mrequest=downloads&mtype=ofed&mver=MLNX_OFED-3.3-1.0.4.0&mname=MLNX_OFED_LINUX-3.3-1.0.4.0-rhel6.8-x86_64.


###RabbitMQ
Download rabbitmq packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/portal_deps/rabbitmq/"

***rabbitmq-server-3.5.3-1.noarch.rpm:***
http://www.rabbitmq.com/releases/rabbitmq-server/v3.5.3/

***erlang-17.4-1.el6.x86_64.rpm:***
http: http://www.rabbitmq.com/releases/erlang/


###MongoDB
Download mongodb packages through the URLs below and put them into **target folder:**
> "~/lico_3.x/packages/portal_deps/mongodb/"

***mongodb-org-tools-2.6.12-1.x86_64.rpm:***
http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/RPMS/

***mongodb-org-shell-2.6.12-1.x86_64.rpm:***
http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/RPMS/

***mongodb-org-server-2.6.12-1.x86_64.rpm:***
http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/RPMS/

***mongodb-org-mongos-2.6.12-1.x86_64.rpm:***
http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/RPMS/

***mongodb-org-2.6.12-1.x86_64.rpm:***
http://downloads-distro.mongodb.org/repo/redhat/os/x86_64/RPMS/


#2. To install LiCO for an existed cluster, please reference appendix 2

LiCO supports installing web server on an existed cluster, but it still needs to meet some minimum requirements, please reference appendix 2 for more information.  

#3. Config IMM IP and create IPMI account for nodes in cluster

Manually config IMM IP and create IPMI account.

>**NOTES:**
>
>1. IMM and IPMI account is the same for Rack Server and Next Scale, so user does not need to create the account for each of them. But for Flex server, user needs to create IPMI account separately.
>
>2. If Flex Server is setup up with IMM IP and IPMI account, we will treat Flex Server as Rack Server.

#4. Install head node
##Step 0: Config raid of head node

Raid1 is suggested. 
Skip if the raid is already configured.

##Step 1: Install OS (CentOS 6.5/6.8 or RedHat 6.5/6.8) for head node.

>**NOTES:** 

>1. If you are using CentOS, please download image CentOS-*-DVD1.iso to install head node.

>2. Please select English as the system language.

##Step 2: Config hostname of head node. 

Open "/etc/sysconfig/network" and change hostname to "mgt".

    NETWORKING=yes
    HOSTNAME=mgt

##Step 3: Setup IP of head node's network card. 

There will be 3 IP addresses for a head node as below:

> Management network (OS) IP, e.g. 172.20.0.1 

> Application network IP, e.g. 172.22.0.1 

> Public network IP (optional, for remote access to Head node).

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
>If using IB for application network configuration, config will show ***fail*** when restart network service. You see this error because IB driver and service is not well installed. LiCO will take care of them later, don't worry about it.   

##Step 4: Copy LiCO package and extract it to a directory. 

##Step 5: Copy OS iso image to LiCO folder, "/lico_3.x/packages/os"

>**NOTE:**
>
>If installing CentOS, please download CentOS-*-DVD.iso to LiCO folder, e.g. "/lico_3.x/packages/os".

##Step 6: Modify cluster configuration file
    
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

##Step 7: Update kernel of head node

    [root@mgt ~]# cd /lico_3.x/setup/
    [root@mgt ~]#./0_upgrade_head_node_kernel.sh（don't execute this command through ssh, execute it locally. ）
    Reboot

##Step 8: Config network

>**NOTE:**
>
>There is no BMC network configuration in following config sample. If you are using BMC network, please make sure your Head node has access to BMC network. 

>If there isn't "Login" node in cluster,  you don't need to config "public network".

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
    
>**NOTE:**
>
>***staticrange:*** defines the IP range for host IP of nodes. All IP specified for nodes must be in this range.  
>***dynamicrange:*** IP range which will be dynamically allocated for OS installation. 


##Step 9: Setup head node

    [root@mgt ~]# cd /lico_3.x/setup/
    [root@mgt setup]# ./1_head_node_setup.sh （don't execute this command through ssh, execute it locally.）
    [root@mgt setup]# ssh mgt（ssh Head node itself）

Check configuration of head node: 

    [root@mgt setup]# source /etc/profile.d/xcat.sh
    [root@mgt setup]# cd ~/lico_3.x/bin/
    [root@mgt bin]# ./service_manager.py --check head


#5.	Deploy cluster

##Step 1: Modify configuration file of nodes
Please reference Appendix 1

##Step 2: Add nodes to xcat 

    [root@mgt bin]# ./add_nodes.py

 
##Step 3: Config raid of node
Remotely connect to IMM of the node through ssh channel, and use following command to configure the Raid. Skip if raid was already configured. 

    system> storage -config vol -add -R 1 -D disk[9-0]:disk[9-1] -target ctrl[9]
    
    system> storage -list volumes

>**NOTE:**
>
>This command of raid configuration is only supported by M5 machine, please use UEFI for other type of machines.

##Step 4: Retrieve MAC addresses of nodes (reference ThinkServer support in appendix 3 for ThinkServer deployment)

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

##Step 5: Deploy node (reference ThinkServer support in appendix 3 for ThinkServer deployment) 

User following scripts to deploy nodes:

    [root@mgt bin]# ./deploy_nodes.py "node1,node2,node[3-5]"

***node1, node2 ...*** must be set to the node names you specified in nodes.csv

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

##Step 6: Rename application NIC of a node (optional)

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

#6. Install Lustre (optional)

>**NOTE:**
>
>Skip this part if your cluster will not use lustre.

Apply following steps to install lustre for your cluster.
 
## Step 1: Modify Lustre configuration file

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

##Step 2: Install Lustre

Use following script to install lustre.

    [root@mgt bin]# ./lustre_setup.py

After installation, issue following command to check if share folder is correctly mounted or not. 

    [root@mgt bin]# df –h

# 7.	Install LDAP to manage users of a cluster

LiCO utilize LDAP to manage users of a cluster
There are 2 scenarios :

 1. use the script to build a new ldap environment 
 2. use LiCO in an existed ldap environment

We strongly suggest using LiCO script to build a new LDAP environment for HPC cluster and here are steps:

##Step 1: Use script to create LDAP environment

    [root@mgt packages/openldap]# tar -xvf openldap.tar 
    [root@mgt openldap]# cd openldap 
    [root@mgt openldap]# ./setup_openldap.sh 172.20.0.1(ip of head node)

After finishing the scripts above, you have LDAP service installed on Head node and have LDAP client installed for all other nodes. 

> **Note:**
> 
> You need to restart torque related service after installing LDAP environment. 

User following script to restart torque:

    [root@mgt  bin]#./service_manager.py --restart torque

##Step 2: Modify configuration file 

Change "/lico_3.x/etc/conf.yaml" as following:

    [root@mgt etc] vi conf.yaml

    user_management_method: ldap
    ldap_user_home_base: /share1/users_home
    ldap_server: 172.20.0.1(ip of head node)
    user_rootdir: /share1/users_root
    cluster_sharedir: ["/share2"]

***user_home_base:*** user_home_base is the home directory of a LDAP user, and it must be shared across the cluster.

***user_rootdir:*** user_rootdir is the root folder of a LDAP user, as known as the root foder for web page. user_rootdir must be shared across the cluster.

***cluster_sharedir:*** cluster_sharedir is the shared folder of a cluster besides user_home_base and user_rootdir.

> **Note:**
> 
> The shared folders listed above must exist in system. If using nfs as the shared file system, nfs version must be version 3. 
> Use following command to mount version 3 nfs folder: 
> `mount -t nfs -o  vers=3 nfsserverip:/share  /share`

Here is a sample folder structure for user hpcadmin: 

> /share1/users_root/hpcadmin <-root directory of hpcadmin, aka the root folder for web. 
> 
> /share1/users_root/hpcadmin/home <- symbolic link to user's home directory 
> 
> /share1/users_root/hpcadmin/share2 <- symbolic link to user's sharedir
> 
> /share1/users_home/hpcadmin <- home directory of the user
> 
> /share2 <- public shared folder: sharedir

#8.	Install LiCO in web portal

##Step 1: Edit config file "lico_3.**/etc/conf.yaml". 

- Change the cluster related content. 
- Change the domain name to cluster name. 

##Step 2: Install dependent packages for GUI Portal

    [root@mgt lico_3.x]# python portal_package_install.py

> **Note:**
> 
> Close current session after installation, then open another session to execute following commands. 

##Step 3: Config portal initialization

>**NOTE:**
>
>You can use the default configuration without touch the Init file .

    [root@mgt lico_3.x]# vi portal_init.yaml

    username: hpcadmin
    password: Passw0rd
    email: admin@admin.com
    billgroup: default_bill_group
    credits: 1000
    osgroup: default_os_group
    recreate_os_user: True
    recreate_bill_group: True
    recreate_os_group: True


##Step 4: Initialize GUI portal 

Use following script to initialize GUI Portal

    [root@mgt lico_3.x]# python portal_init.py

##Step 5: Start service of GUI portal

While starting service, there will has output information in interactive console, so we suggest you this service in a separate shell session. 
For example, you can start the service in sreen session, use sreen -help for more details. Here is an example to use screen

    [root@mgt lico_3.x]# screen 
    [root@mgt lico_3.x]# ./lico start

If you are going to close the shell, don't use "exit", otherwise your screen session is closed. 
Since the screen session exists even when you close the shell, you can use `screen -r` to enter screen session anytime. 

Another way is to open a shell in vnc and start the service there. 


To stop service, issue command below: 

    [root@mgt lico_3.x]# ./lico stop

##Step 6: Check if GUI portal is successfully configured

Open following URL and login: 

    URL: http://172.20.0.1:8080/login/   
    Username/Password: hpcadmin/Passw0rd

> **Note:** 
> 
> If node status in GUI portal is incorrect, use route command to see if ganglia is working on correct port. ganglia should listening on management network port, if not, in every nodes, use `ip route add 239.2.11.71 dev eth0` (eth0 should be the management nic) to config and start gmond `service gmond restart`, then restart LiCO by using `./lico start`. 


#9. Deploy alarm module:  Nagios (optional, not suggested)

Nagios module is not included in LiCO release version. If you want to deploy Nagios in cluster, we provide a script for automatic deployment. Just execute following script in Head node:

    [root@mgt lico_3.x] cd  packages/nagios/setup 
    [root@mgt setup] ./setup_nagios.sh <mgt_management_ip>

  
> e.g. [root@mgt setup]./setup_nagios.sh 172.20.0.1

Then, restart Nagios by using `service nagios restart` and httpd. 
Under LiCO management portal, you can also find alam information of Nagios and use account "nagiosadmin/nagiosadmin" to login to Nagios home page.

#Appendix 1. Modify configuration file of nodes: nodes.csv
Copy file in "/lico_3.*/etc/nodes.csv" to your local hard disk and open it with a corresponding editor, e.g. Microsoft Excel. 
Here are some guidelines for modifying the csv file. After finish, replace the csv file in Head node with the new one.

![sample nodes.csv](https://github.com/lenovo-rui/lico-doc/raw/master/resources/nodes_csv.png)

##**Part 1**: Room Information

Only **one** room is supported in current releases. 

| room     | name  | location_description |
| :------- | ----: | :------------------: |
|          | room1 |  2nd floor           |

 - ***name***: room name
 - ***location_description***: room location description


##**Part 2**: Logical group of cluster (mandatory)

| group    | name    | 
| :------- | :------:| 
|          | bigmen  | 
|          | compute | 
|          | gpu     | 
|          | login   |  

 - ***name***: Logical Group name


##**Part 3**: Row Information (mandatory)

Rows which host the nodes of cluster.

| row      | name  | index | belonging_room  |
| :------- | ----: | :---: | :-------------: |
|          | row1  |  1    | room1           |
|          | row2  |  2    | room1           |        

 - ***name***: row name
 - ***index***: integer, can't be same with cluster.
 - ***belonging_room***: room information which the row is hosted. The name here must be same as specified in room.


##**Part 4**: Rack information (mandatory) 

Racks which host the nodes of cluster.

| rack     | name  | column | belonging_row   |
| :------- | ----: | :----: | :-------------: |
|          | rack1 |  1     | row1            |
|          | rack2 |  2     | row1            |
|          | rack3 |  1     | row2            |  

 - ***name***: rack name
 - ***belonging_row***: the row which rack is sitting, the row name must be one of row name specified in Part 3. 
 - ***column***: the column the rack is sitting, integer, can't be duplicated with the row.


##**Part 5**: Chassis Information (optional)

If there is any chassis in cluster, this information must be specified.

| chassis  | name      | belonging_rack | location   |  machine_type |
| :------- | :-------: | :------------: | :--------: |  :----------: |
|          | chassis1  |  rack1         | 7          | Flex_Chassis  |
|          | chassis2  |  rack2         | 20         | Flex_Chassis  |
|          | chassis3  |  rack1         | 7          | Nextscale_Chassis| 

 - ***name***: chassis name 
 - ***belonging_rack***: the rack name which chassis belongs to. the rack name must be one of the racks in Part 4. 
 - ***location***: location of chassis, it indicates which u the chassis is in.it is an integer value between 1-42. 
 - ***machine_type***: type of the chassis, either Flex_Chassis or Nextscale_Chassis. If the machine is Nextscale, use Nextscale_Chassis; if the machine is Flex, use Flex_Chassis.


##**Part 6**: Switch Information (optional) 

Generally, we don't specify any information here. 


##**Part 7**: Server/Node Information (mandatory)

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


#Appendix 2. Install LiCO web portal in an existed cluster. 

Follow steps below to install LiCO in Head node.

##Step 1: Make sure OS is correctly installed in every nodes. (Rhel6.5/Rhel6.8/CentOS6.5/CenOS6.8)


##Step 2: Make sure root account can access between the nodes without specifying password. 


##Step 3: Check if ganglia is installed successfully. If not, install ganglia and then open ganglia web page to check if monitoring information for each nodes is correct. 


##Step 4: Make sure the scheduler (either torque or lsf) correctly installed. 


##Step 5: Make sure corresponding VNC components correctly installed, if user is going to use Web VNC function.  

Use following commands on each nodes to install related components: 

    yum groupinstall "X Window System" 
    yum groupinstall "Desktop"
    yum install tiger*


##Step 6: Setup monitor agent for each nodes.

Copy folder "lico_3.**/cluster_monitor_project/lico_monitor_agent" to each compute nodes' /opt folder. 


##Step 7: Install LDAP for cluster. 

In following scenarios, we need to create a new LDAP environment:

 - user does not have LDAP environment 
 - user can't get LDAP admin right: can't create or modify LDAP 
 - user existed LDAP environment used ssl

Here are commands to use LiCO script to create a new LDAP environment: 

    [root@mgt packages/openldap]# tar -xvf openldap.tar
    [root@mgt openldap]# cd openldap
    [root@mgt openldap]# ./setup_openldap_server.sh 172.20.0.1(ip of Head node)
    
Except Head node, other nodes should build LDAP environment with following scripts:

    [root@mgt openldap]# ./setup_openldap_client.sh compute1 172.20.0.1(ip of Head node)
    [root@mgt openldap]# ./setup_openldap_client.sh compute2 172.20.0.1(ip of Head node)

After building LDAP environment, if torque is used as scheduler of cluster, we need to restart corresponding services. 

In Head node: 

    Service trqauthd restart 
    Service pbs_server restart
    Service maui.d restart

In Compute and Login node: 

    Service trqauthd restart
    Service pbs_mom restart


##Step 8: Update LDAP information.

Edit "lico_3.**/etc/conf.yaml" :
 
Change ldap_user_home_base to a shared folder. 
If working in an existing LDAP, set ldap_user_home_base to the existing LDAP user's home directory. 
If LDAP environment is newly created, select a shared folder as ldap_user_home_base, e.g.

    ldap_user_home_base='/share1/users_home'

>**Note:**
>
>if NFS is used, mount nfs with version 3, e.g.
>`[root@nfsclient  /]# mount -t nfs -o vers=3 nfsserverip:/share1  /share1`

Change user_rootdir to a shared folder (e.g. /share1/users_root), the folder used can't be same as specified for ldap_user_home_base.  
If there are different type of shared file system, change cluster_sharedir to other shared folder in cluster, e.g. /share2

When importing user1 to LiCO, LiCO will create a directory under /share1/users_root/, like /share1/users_root/user1. And create a symbolic link of home directory to /share1/users_home/user1. Meanwhile LiCO will create a symbolic link share2 under /share1/users_root/user1/ to link to /share2. 
'/share1/users_root/user1 is the top root folder for user to use in LiCO web page. 

So user1's LDAP directory structure is as following: 

    /share1/users_root/user1  <-  user's root directory, also the root directory in web.
    /share1/users_root/hpcadmin/home  <-  symbolic link to user's home directory 
    /share1/users_root/hpcadmin/share2  <-  symbolic link to user's sharedir 
    /share1/users_home/user1  <-  user's home directory
    /share2  <-  sharedir

Change corresponding variables under user_management, for example: ldap_server,ldap_manager,ldap_password, etc. 
 
>**Note:**
>
>If the LDAP environment is built through LiCO scripts, just change the settings for ldap_server. (don't change ldap_manager and ldap_password.)


##Step 9: Update Scheduler information.

LiCO supports both Torque and LSF. 

>**NOTE:**
>
>- Make sure that root user can execute commands of Torque and LSF.
>- If set queues_auto_get to True, queues' content will be ignored. 
>- If set queues_auto_get to False, queues' content must be set. 


##Step 10: Update Cluster Information 

Change domain to cluster's domain. 


##Step 11:  Update Nodes Information 

Reference Appendix 1 to update nodes' information in lico_3.**/etc/nodes.csv.


##Step 12: Install Dependencies of GUI Portal 

    [root@mgt lico_3.x]# python portal_package_install.py

>**Note:**
>Current session must be closed after installation. Please open a new session for successive commands. 


##Step 13: Initialize Web System

>**Note:**
>If working in existing LDAP, user needs to modify "portal_init.yaml" and make sure the username specified is an existed user.
>osgroup can't be the group existed before in LDAP. 
>If used LiCO to build LDAP environment, there is no need to change "portal_init.yaml"

Here is a sample config file: 

    [root@mgt lico_3.x]# vi portal_init.yaml    
    username: hpcadmin
    password: Passw0rd
    email: admin@admin.com
    billgroup: default_bill_group
    credits: 1000
    osgroup: default_os_group
    recreate_os_user: True
    recreate_bill_group: True
    recreate_os_group: True

After finish, issue following commands to apply changes:

    [root@mgt lico_3.x]# python portal_init.py

If working in existing LDAP environment, use " load_users_and_groups_from_ldap.py" to import existed user to LiCO. 

Here is am example to import all users to LiCO, please note that admin1 and admin2 are imported as admin user:

    [root@mgt lico_3.x]# ./bin/load_users_and_groups_from_ldap.py --adminusers admin1,admin2

>**NOTE:**
>Skip this step is LDAP environment is built from LiCO scripts. 


##Step 14: Start Service of GUI Portal

Since LiCO keeps outputing information to console, we suggest that user starts service of GUI Portal in a non-stopped shell session.

e.g. start LiCO in screen, here are the commands:

    [root@mgt lico_3.x]# screen
    [root@mgt lico_3.x]# ./lico start

>**NOTE:** user "screen -h" for more information about screen. 

If you are going to close the shell, don't use "exit", otherwise your screen session is closed. 
Since the screen session exists even when you close the shell, you can use `screen -r` to enter screen session anytime. 

Another way is to open a shell in vnc and start the service there. 

To stop service, issue command below: 

    [root@mgt lico_3.x]# ./lico stop


##Step 15: Verify Installation of GUI Portal

Open following URL and use hpcadmin/Passw0rd to login to LiCO web:
http://172.20.0.1:8080/login/ 	

> **Note:** 
> 
> If node status in GUI Portal is incorrect, use route command to see if ganglia is working on correct port. ganglia should listening on management network port, if not, in every nodes, use `ip route add 239.2.11.71 dev eth0` (eth0 should be the management nic) to config and start gmond `service gmond restart`, then restart LiCO by using `./lico start`. 


#Appendix 3. ThinkServer support（for ThinkServer only）

Most of steps are same between ThinkServer and RackServer, so you can follow the processes above and keep following 2 differences in mind. 

##1: Retrieve MAC address of a ThinkServer (Step 4 of Deploy Cluster)

You can't use "./bin/discover_node_macs_using_imm.py" to retrieve the MAC address of ThinkServers. Follow the steps below:

Open 2 shell console, say console A and console B 

In console A, use command below:

    nodediscoverstart noderange=c01n[001-010] 

In console B, go to lico folder and try to setup nodes through pxe :

>**NOTE:** If node uses uefi mode to boot, issue command below:
>
>`[root@mgt lico_3.x]# ./bin/thinkserver_bootmanager.py --nodes "c01n[001-010]" --bootingfrom "uefi-pxe"`
>"
>**NOTE:** if node uses legacy mode to boot, issue command below:
>
>`[root@mgt lico_3.x]# ./bin/thinkserver_bootmanager.py --nodes "c01n[001-010]" --bootingfrom "legacy-pxe"`

Monitoring the MAC discovery through following commands:

    cat /dev/null > /var/log/messages
    tail -f /var/log/messages | grep DHCPDISCOVER

In console A, issue command `rpower c01n001 on`, Check if there is a MAC address received in console B, if yes, issue `rpower c01n001 off`, then do it for next node c01n002, e.g. `rpower c01n002 on`, then go on and on until all MAC addresses are retrieved. 
Then write the MAC addresses information to xcat database by using `tabedit mac`. Here is an example:

    node,interface,mac,comments,disable
    "io01",,"40:F2:E9:75:35:78",,


##2: Deploy ThinkServer nodes (step 5 in deploying cluster)

Open 2 shell console, say console A and console B 

In console A, issue command below:

    [root@mgt lico_3.x]# ./bin/deploy_nodes.py c01n0[01-010] 

When you see "monitor deploy status", issue following command in console B:

>**NOTE:** If node uses uefi mode to boot, issue command below:
>
>`[root@mgt lico_3.x]# ./bin/thinkserver_bootmanager.py --nodes "c01n[001-010]" --bootingfrom "uefi-pxe"`
>
>**NOTE:** if node uses legacy mode to boot, issue command below:
>
>`[root@mgt lico_3.x]# ./bin/thinkserver_bootmanager.py --nodes "c01n[001-010]" --bootingfrom "legacy-pxe"`

Then restart these node in console B:

    rpower c01n0[01-010] off
    rpower c01n0[01-010] on 

Oberserve result through console A. 

#Appendix 4. Config bootorder through BMC (optional)

Use ssh to login to BMC of Login Node, use asu to set bootorder as below: 

    asu set BootOrder.BootOrder "CD/DVD Rom=USB Storage=Hard Disk 0=Legacy Only=PXE Network"



