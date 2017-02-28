Lustre installation and configuration
==============================

# Install lustre package

    # cd ~/Lico/packages/luster/6.5/server
    #rpm -Uvh kernel-firmware-2.6.32-431.23.3.el6_lustre.x86_64.rpm
    #rpm -ivh kernel-2.6.32-431.23.3.el6_lustre.x86_64.rpm
    #reboot
    查看kernel版本
    #uname -a
	Linux localhost.localdomain 2.6.32-431.23.3.el6_lustre.x86_64 #1 SMP Thu Aug 28 20:20:13 PDT 2014 x86_64 x86_64 x86_64 GNU/Linux
    #rpm -ivh lustre-modules-2.5.3-2.6.32_431.23.3.el6_lustre.x86_64.x86_64.rpm
    #yum -y remove e2fsprogs-1.41.12-18.el6.x86_64
    #yum -y remove e2fsprogs-libs-1.41.12-18.el6.x86_64
    #rpm -ivh e2fsprogs-libs-1.42.12.wc1-7.el6.x86_64.rpm
    #yum -y remove libss-1.41.12-18.el6.x86_64
    #rpm -Uvh libcom_err-1.42.12.wc1-7.el6.x86_64.rpm
    #rpm -ivh libss-1.42.12.wc1-7.el6.x86_64.rpm
    #rpm -ivh e2fsprogs-1.42.12.wc1-7.el6.x86_64.rpm
    #rpm -ivh lustre-osd-ldiskfs-2.5.3-2.6.32_431.23.3.el6_lustre.x86_64.x86_64.rpm
    #rpm -ivh lustre-2.5.3-2.6.32_431.23.3.el6_lustre.x86_64.x86_64.rpm


#Disable SELINUX    
修改/etc/selinux/config文件中设置SELINUX=disabled ，然后重启服务器。

#Install MDT

Create a combined MGS/MDT file system on the block device. On the MDS node, run:

    #mkfs.ext4 /dev/sdb
    #mkfs.lustre --reformat --device-size=300000 --fsname cluster_name --mdt --mgs /dev/sdb

Create a mount point

    mkdir -p /mnt/mds 

Mount (start) the combined MGS/MDT file system on the block device. On the MDS node, run:

     mount -t lustre -o loop /dev/sdb /mnt/mds

##Setup MGS/MDS lnet:
Then prepare network, and edit /etc/modprobe.d/lnet.conf. Here the network used for data is 172.16.0.x and from equipment its is linked to interface eth0.

    options lnet networks=tcp0(eth0)
    modprobe -v lnet
    lctl net up

If a server with multiple IP addresses (multihome server) is connected to a Lustre network, certain configuration setting are required. An example illustrating these setting consists of a network with the following nodes:

Server svr1 with three TCP NICs (eth0, eth1, and eth2) and an InfiniBand NIC.
Server svr2 with three TCP NICs (eth0, eth1, and eth2) and an InfiniBand NIC. Interface eth2 will not be used for Lustre networking.
TCP clients, each with a single TCP interface.
InfiniBand clients, each with a single Infiniband interface and a TCP/IP interface for administration.
To set the networks option for this example:

On each server, svr1 and svr2, include the following line in the modprobe.conf file:
options lnet networks=tcp0(eth0),tcp1(eth1),o2ib
For TCP-only clients, the first available non-loopback IP interface is used for tcp0. Thus, TCP clients with only one interface do not need to have options defined in the modprobe.conf file.
On the InfiniBand clients, include the following line in the modprobe.conf file:
options lnet networks=o2ib

#Install OSS
Create an OST node for an OSS. For each OST, run this command on the OSS node

    mkfs.lustre --reformat --fsname cluster_name --ost --index=0 --mgsnode=192.168.128.128@tcp0 /dev/sdb

Create a mount point

    mkdir -p /mnt/ost1

Mount the OST. On the OSS node, run:

    mount -t lustre -o loop /tmp/lustre-ost1 /mnt/ost1 

如果出现错误如：is MGS running？则尝试关闭防火墙（不建议）：

    service iptables stop
    service ip6tables stop

或者设置端口的通过规则。


#Lustre Client Installation
Lico/packages/luster/client

    rpm -Uvh kernel-firmware-2.6.32-431.23.3.el6_lustre.x86_64.rpm
    rpm -ivh kernel-2.6.32-431.23.3.el6.x86_64.rpm
    reboot
    rpm -ivh lustre-client-modules-2.5.3-2.6.32_431.23.3.el6.x86_64.x86_64.rpm
    rpm -ivh lustre-client-2.5.3-2.6.32_431.23.3.el6.x86_64.x86_64.rpm

    mkdir /mnt/lustre
    mount -t lustre mds1@tcp0:/toto /mnt/lustre

#Install LMT

##Install Cerebro for LMT

Download latest cerebro rpm binary:
https://github.com/chaos/cerebro/releases

    #tar -xzvf cerebro-1.18.tar.gz
    #cp cerebro.spec ~/rpmbuild/SPECS/.
    #cp cerebro-1.18.tar.gz /root/rpmbuild/SOURCES/cerebro-1.18.tar.gz
    #vi ~/rpmbuild/SPECS/cerebro.spec

29 line에 %define debug_package %{nil} 추가

if rpm-build is not installed, install with following command:
yum install rpm-build

    # rpmbuild -ba ~/rpmbuild/SPECS/cerebro.spec
    ...
    + umask 022
    + cd /root/rpmbuild/BUILD
    + cd cerebro-1.18
    + /bin/rm -rf /root/rpmbuild/BUILDROOT/cerebro-1.18-1.x86_64
    + exit 0


# ls ~/rpmbuild/RPMS/x86_64/*
/root/rpmbuild/RPMS/x86_64/cerebro-1.18-1.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/cerebro-clusterlist-hostsfile-1.18-1.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/cerebro-event-updown-1.18-1.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/cerebro-metric-boottime-1.18-1.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/cerebro-metric-loadavg-1.18-1.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/cerebro-metric-memory-1.18-1.x86_64.rpm
/root/rpmbuild/RPMS/x86_64/cerebro-metric-network-1.18-1.x86_64.rpm
# rpm -qpi /root/rpmbuild/RPMS/x86_64/cerebro-1.18-1.x86_64.rpm
Name        : cerebro                      Relocations: (not relocatable)
Version     : 1.18                              Vendor: (none)
Release     : 1                             Build Date: Tue 17 May 2016 02:37:46 PM KST
Install Date: (not installed)               Build Host: server0
Group       : System Environment/Base       Source RPM: cerebro-1.18-1.src.rpm
Size        : 1277815                          License: GPL
Signature   : (none)
Summary     : Cerebro cluster monitoring tools and libraries
Description :
Cerebro is a collection of cluster monitoring tools and libraries.
 

##Build LMT RPM
download LMT source from here:


###Install Lua


    # yum install lua-devel
    # yum install mysql-devel
    # rpm -Uvh ~/rpmbuild/RPMS/x86_64/cerebro-1.18-1.x86_64.rpm

 
###Build LMT rpm

    # git clone https://github.com/chaos/lmt.git
    Initialized empty Git repository in /usr/src/lmt/.git/
    remote: Counting objects: 3404, done.
    remote: Compressing objects: 100% (16/16), done.
    remote: Total 3404 (delta 0), reused 0 (delta 0), pack-reused 3388
    Receiving objects: 100% (3404/3404), 6.09 MiB | 1.29 MiB/s, done.
    Resolving deltas: 100% (1709/1709), done.

    # cd lmt
    # ./autogen.sh
    # ./configure
    # cp lmt.spec ~/rpmbuild/SPECS/.
    # make distclean
    # cd ..
    # mv lmt lmt-3.2.1    ==> configure 후 생성된 spec file내의 version 확인
    # tar -czvf lmt-3.2.1.tar.gz lmt-3.2.1/
    # cp lmt-3.2.1.tar.gz ~/rpmbuild/SOURCES/
    # export LIBS=-ltinfo   
    # rpmbuild -ba ~/rpmbuild/SPECS/lmt.spec
    + umask 022
    + cd /root/rpmbuild/BUILD
    + cd lmt-3.2.1
    + rm -rf /root/rpmbuild/BUILDROOT/lmt-3.2.1-1.el6.x86_64
    + exit 0
    # ls  ~/rpmbuild/RPMS/x86_64/lmt-*
    /root/rpmbuild/RPMS/x86_64/lmt-3.2.1-1.el6.x86_64.rpm
    /root/rpmbuild/RPMS/x86_64/lmt-server-3.2.1-1.el6.x86_64.rpm
    /root/rpmbuild/RPMS/x86_64/lmt-server-agent-3.2.1-1.el6.x86_64.rpm

    # rpm -qpi /root/rpmbuild/RPMS/x86_64/lmt-3.2.1-1.el6.x86_64.rpm
    Name        : lmt                          Relocations: (not relocatable)
    Version     : 3.2.1                             Vendor: (none)
    Release     : 1.el6                         Build Date: Tue 17 May 2016 03:48:08 PM KST
    Install Date: (not installed)               Build Host: server0
    Group       : Applications/System           Source RPM: lmt-3.2.1-1.el6.src.rpm
    Size        : 0                                License: GPL
    Signature   : (none)
    Packager    : Ned Bass <bass6@llnl.gov>
    URL         : http://github.com/chaos/lmt
    Summary     : Lustre Montitoring Tool
    Description :
    Lustre Monitoring Tool

 
###Build LMT-GUI rpm

    # yum install java-devel
    # git clone https://github.com/chaos/lmt-gui.git 
    # cd lmt-gui/
    # ./autogen.sh
    # ./configure
    # cp lmt-gui.spec ~/rpmbuild/SPECS/.
    # make distclean
    # cd ..
    # mv lmt-gui/ lmt-gui-3.0.0  ==> 생성된 spec file에서 version 확인
    # tar -czvf lmt-gui-3.0.0.tar.gz lmt-gui-3.0.0/
    # cp lmt-gui-3.0.0.tar.gz ~/rpmbuild/SOURCES/.
    # rpmbuild -ba ~/rpmbuild/SPECS/lmt-gui.spec
    + umask 022
    + cd /root/rpmbuild/BUILD
    + cd lmt-gui-3.0.0
    + rm -rf /root/rpmbuild/BUILDROOT/lmt-gui-3.0.0-1.x86_64
    + exit 0


    # ls ~/rpmbuild/RPMS/x86_64/lmt-gui*
    /root/rpmbuild/RPMS/x86_64/lmt-gui-3.0.0-1.x86_64.rpm
    [root@server0 src]# rpm -qpi /root/rpmbuild/RPMS/x86_64/lmt-gui-3.0.0-1.x86_64.rpm
    Name        : lmt-gui                      Relocations: (not relocatable)
    Version     : 3.0.0                             Vendor: (none)
    Release     : 1                             Build Date: Tue 17 May 2016 04:00:41 PM KST
    Install Date: (not installed)               Build Host: server0
    Group       : Applications/System           Source RPM: lmt-gui-3.0.0-1.src.rpm
    Size        : 2347309                          License: GPL
    Signature   : (none)
    Packager    : Jim Garlick <garlick@llnl.gov>
    URL         : http://code.google.com/p/lmt
    Summary     : Lustre Montitoring Tools Client
    Description :
    Lustre Monitoring Tools (LMT) GUI Client

 
###Install LMT on management node

    # yum install mysql-server
    # wget http://195.220.108.108/linux/centos/6.7/os/x86_64/Packages/perl-YAML-Syck-1.07-4.el6.x86_64.rpm
    # rpm -Uvh perl-YAML-Syck-1.07-4.el6.x86_64.rpm
    # wget http://195.220.108.108/linux/centos/6.7/os/x86_64/Packages/perl-Date-Manip-6.24-1.el6.noarch.rpm
    # rpm -Uvh perl-Date-Manip-6.24-1.el6.noarch.rpm

### Install LMT server 

    # rpm -Uvh ~/rpmbuild/RPMS/x86_64/lmt-server-3.2.1-1.el6.x86_64.rpm
    # tail /etc/cerebro.conf
    #####################################################################################################
    cerebro_metric_server   192.168.80.11
    cerebrod_heartbeat_frequency 10 20
    cerebrod_speak  on
    cerebrod_speak_message_config   192.168.80.11
    cerebrod_listen on
    cerebrod_listen_message_config  192.168.80.11
    cerebrod_metric_controller      on
    cerebrod_event_server   on
    cerebro_event_server    192.168.80.11
    # cat /etc/hostsfile    
    server0
    server1
    server2
    server3
    server4

 


 mysqld start

 #  service mysqld start


 cerebrod start


# service cerebrod start
Starting cerebrod:                                         [  OK  ]


  * 로그는 /var/log/messages


3. MDS, OSS에 설치


  * 생성한 RPM 들을 가지고 설치 진행


 의존성 회피를 위한 설치


# yum install lua-devel mysql-devel
 
 


 Cerebro 설치


# rpm -Uvh cerebro-*
 
 


 lmt server 설치


# rpm -Uvh lmt-server-agent-3.2.1-1.el6.x86_64.rpm 

 


 cerebro 설정


# # tail -n 7 /etc/cerebro.conf
#####################################################################################################
cerebro_metric_server   192.168.80.11
cerebro_event_server    192.168.80.11
cerebrod_heartbeat_frequency 10 20
cerebrod_speak  on
cerebrod_speak_message_config   192.168.80.11
cerebrod_listen off
 
 


 cerebrod start


# service cerebrod start
Starting cerebrod:                                          [  OK  ]
# service cerebrod restart
Shutting down cerebrod:                                    [  OK  ]
Starting cerebrod:                                         [  OK  ]
 
 


4. Test



 Management node 에서


# cerebro-stat -m updown_state
server0: 1
server1: 1
server2: 1
server3: 1
server4: 1
#
# cerebro-stat -l
bytesout
metric_names
boottime
memused
cluster_nodes
rxerrs
lmt_mdt
memtotal
loadavg1
packetsin
txerrs
updown_state
loadavg5
swaptotal
lmt_ost
memfree
swapused
loadavg15
packetsout
bytesin
swapfree
#
# ltop
Filesystem: lfs_test
    Inodes:      4.000m total,      0.000m used (  0%),      4.000m free
     Space:      0.022t total,      0.001t used (  3%),      0.022t free
   Bytes/s:      0.000g read,       0.000g write,                 0 IOPS
   MDops/s:      0 open,        0 close,       0 getattr,       0 setattr
                 0 link,        0 unlink,      0 mkdir,         0 rmdir
                 0 statfs,      0 rename,      0 getxattr
>OST S        OSS   Exp   CR rMB/s wMB/s  IOPS   LOCKS  LGR  LCR %cpu %mem %spc
0000      server2     3    0     0     0     0       0    0    0    0   88    7
0001      server3     3    0     0     0     0       0    0    0    0   44    0
0002      server4     3    0     0     0     0       0    0    1    0   43    0
 
 


 MDS 에서


# lmtmetric -m mdt
mdt: 2;server1;0.122956;44.509140;lfs_test-MDT0000;4194053;4194304;5908936;5955496;INACTIVE  0s remaining;10;0;0;10;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;0;1;0;0;0;0;0;0;0;0;0;0;0;0;0;0;5;0;0;0;0;0;0;0;0;5;0;0;20;0;0;0;0;0;0;0;0;0;0;0
 
 


 OSS1 에서

 
# lmtmetric -m ost
ost: 2;server2;0.269485;87.832200;lfs_test-OST0000;524024;524288;7343420;7922104;2048000000;4531027968;6270;3;0;0;0;3;0;INACTIVE  0s remaining;

 


 OSS2 에서

 
# lmtmetric -m ost
ost: 2;server3;0.121685;43.895123;lfs_test-OST0001;524024;524288;7884092;7922104;0;0;0;3;0;0;0;3;0;INACTIVE  0s remaining;

 

 OSS3 에서
 
# lmtmetric -m ost
ost: 2;server4;0.124232;43.434415;lfs_test-OST0002;524024;524288;7884088;7922104;4096;14;2;3;0;0;1;3;0;INACTIVE  0s remaining;

 


5. LMT GUI


  * Monitoring Node 에서 수행

  * mysql 사용자 생성

    - /usr/share/lmt/mkusers.sql  내용 참고하여 생성

 # Example script for creating LMT MySQL users.
#
# To improve security,
# 1. change the lwatchadmin password!
# 2. instead of granting to *.*, grant privileges to to filesystem_yourfs.*
#
# To run lwatch remotely, add 'lwatchclient'@'hostname' users for each host.
#
# Assumes /etc/lmt/lmt.conf contains:
#   lmt_db_rouser = "lwatchclient"
#   lmt_db_ropasswd = nil
CREATE USER 'lwatchclient'@'localhost';
GRANT SHOW DATABASES        ON *.* TO 'lwatchclient'@'localhost';
GRANT SELECT                ON *.* TO 'lwatchclient'@'localhost';

# Assumes /etc/lmt/lmt.conf contains:
#   lmt_db_rwuser = "lwatchadmin"
#   lmt_db_rwpasswd = "mypass"  -- or use the separate file example
CREATE USER 'lwatchadmin'@'localhost' IDENTIFIED BY 'mypass';
GRANT SHOW DATABASES        ON *.* TO 'lwatchadmin'@'localhost';
GRANT SELECT,INSERT,DELETE  ON *.* TO 'lwatchadmin'@'localhost';
GRANT CREATE,DROP           ON *.* TO 'lwatchadmin'@'localhost';

FLUSH PRIVILEGES;


  * DB 초기화

    - lmtinit -a lfs_test -s /usr/share/lmt/create_schema-1.1.sql -c /usr/etc/lmt/lmt.conf

 
mysql> show databases;
+---------------------+
| Database            |
+---------------------+
| information_schema  |
| filesystem_lfs_test |
| mysql               |
| test                |
+---------------------+
4 rows in set (0.00 sec)

mysql> use filesystem_lfs_test;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------------------+
| Tables_in_filesystem_lfs_test |
+-------------------------------+
| EVENT_DATA                    |
| EVENT_INFO                    |
| FILESYSTEM_AGGREGATE_DAY      |
| FILESYSTEM_AGGREGATE_HOUR     |
| FILESYSTEM_AGGREGATE_MONTH    |
| FILESYSTEM_AGGREGATE_WEEK     |
| FILESYSTEM_AGGREGATE_YEAR     |
| FILESYSTEM_INFO               |
| MDS_AGGREGATE_DAY             |
| MDS_AGGREGATE_HOUR            |
| MDS_AGGREGATE_MONTH           |
| MDS_AGGREGATE_WEEK            |
| MDS_AGGREGATE_YEAR            |
| MDS_DATA                      |
| MDS_INFO                      |
| MDS_OPS_DATA                  |
| MDS_VARIABLE_INFO             |
| OPERATION_INFO                |
| OSS_DATA                      |
| OSS_INFO                      |
| OSS_INTERFACE_DATA            |
| OSS_INTERFACE_INFO            |
| OSS_VARIABLE_INFO             |
| OST_AGGREGATE_DAY             |
| OST_AGGREGATE_HOUR            |
| OST_AGGREGATE_MONTH           |
| OST_AGGREGATE_WEEK            |
| OST_AGGREGATE_YEAR            |
| OST_DATA                      |
| OST_INFO                      |
| OST_OPS_DATA                  |
| OST_VARIABLE_INFO             |
| ROUTER_AGGREGATE_DAY          |
| ROUTER_AGGREGATE_HOUR         |
| ROUTER_AGGREGATE_MONTH        |
| ROUTER_AGGREGATE_WEEK         |
| ROUTER_AGGREGATE_YEAR         |
| ROUTER_DATA                   |
| ROUTER_INFO                   |
| ROUTER_VARIABLE_INFO          |
| TIMESTAMP_INFO                |
| VERSION                       |
+-------------------------------+
42 rows in set (0.00 sec)

 


 cerebro restart


# service cerebrod restart 

 


 잘 설치 되었는지 검사


# lmtsh -f lfs_test
lfs_test> t
Available tables for lfs_test:
                            Table Name   Row Count
                            EVENT_DATA   0
                            EVENT_INFO   0
              FILESYSTEM_AGGREGATE_DAY   0
             FILESYSTEM_AGGREGATE_HOUR   0
            FILESYSTEM_AGGREGATE_MONTH   0
             FILESYSTEM_AGGREGATE_WEEK   0
             FILESYSTEM_AGGREGATE_YEAR   0
                       FILESYSTEM_INFO   1
                     MDS_AGGREGATE_DAY   0
                    MDS_AGGREGATE_HOUR   0
                   MDS_AGGREGATE_MONTH   0
                    MDS_AGGREGATE_WEEK   0
                    MDS_AGGREGATE_YEAR   0
                              MDS_DATA   202
                              MDS_INFO   1
                          MDS_OPS_DATA   4242
                     MDS_VARIABLE_INFO   7
                        OPERATION_INFO   81
                              OSS_DATA   608
                              OSS_INFO   3
                    OSS_INTERFACE_DATA   0
                    OSS_INTERFACE_INFO   0
                     OSS_VARIABLE_INFO   7
                     OST_AGGREGATE_DAY   0
                    OST_AGGREGATE_HOUR   0
                   OST_AGGREGATE_MONTH   0
                    OST_AGGREGATE_WEEK   0
                    OST_AGGREGATE_YEAR   0
                              OST_DATA   608
                              OST_INFO   3
                          OST_OPS_DATA   0
                     OST_VARIABLE_INFO   11
                  ROUTER_AGGREGATE_DAY   0
                 ROUTER_AGGREGATE_HOUR   0
                ROUTER_AGGREGATE_MONTH   0
                 ROUTER_AGGREGATE_WEEK   0
                 ROUTER_AGGREGATE_YEAR   0
                           ROUTER_DATA   0
                           ROUTER_INFO   0
                  ROUTER_VARIABLE_INFO   4
                        TIMESTAMP_INFO   204
                               VERSION   0



# lwatch

# set lustre repo
[lustre-server]
name=RHEL-$releasever - Lustre
baseurl=https://downloads.hpdd.intel.com/public/lustre/lustre-2.9.0/el7.3.1611/server/
gpgcheck=0

[e2fsprogs]
name=RHEL-$releasever - Ldiskfs
baseurl=https://downloads.hpdd.intel.com/public/e2fsprogs/1.42.13.wc5/el6/
gpgcheck=0

[lustre-client]
name=RHEL-$releasever - Lustre
baseurl=https://downloads.hpdd.intel.com/public/lustre/lustre-2.9.0/el7.3.1611/client/
gpgcheck=0

#install htop
wget http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
rpm -ihv epel-release-6-8.noarch.rpm
yum info htop
yum install htop


#Install Logstach on nodes (OSS, MDS)

Download and install the public signing key:

    rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

Add the following in your /etc/yum.repos.d/ directory in a file with a .repo suffix, for example logstash.repo
  
    [logstash-5.x]
    name=Elastic repository for 5.x packages
    baseurl=https://artifacts.elastic.co/packages/5.x/yum
    gpgcheck=1
    gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
    enabled=1
    autorefresh=1
    type=rpm-md

And your repository is ready for use. You can install it with:
    
    sudo yum install logstash

#Install ElasticSearch on service server

当遇到以下错误时：
启动报错1

es5.2无法启动bug
报错：
ERROR: bootstrap checks failed
system call filters failed to install; check the logs and fix your configuration or disable system call filters at your own risk


原因：
这是在因为Centos6不支持SecComp，而ES5.2.0默认bootstrap.system_call_filter为true进行检测，所以导致检测失败，失败后直接导致ES不能启动。


解决：
在elasticsearch.yml中配置bootstrap.system_call_filter为false，注意要在Memory下面:
bootstrap.memory_lock: false
bootstrap.system_call_filter: false


可以查看issues
https://github.com/elastic/elasticsearch/issues/22899


**max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]**
修改/etc/security/limits.conf 
    elastic hard nofile 65536
    elastic soft nofile  65536

>备注:elastic这里是指定用户elastic 当然可以用*表示所有人

    * hard nofile 65536
    * soft nofile 65536

2 、[2016-11-19T03:22:22,188][WARN ][o.e.b.BootstrapCheck     ] [4Ut8v_1] max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

更改sysctl.conf的配置

vim /etc/sysctl.d/99-sysctl.conf  或
vim /etc/sysctl.conf
增加：vm.max_map_count = 262144

sysctl -p //保存生效

当收到错误：max number of threads [1024] for user [**] is too low, increase to at least [2048]

    # cat /etc/security/limits.d/90-nproc.conf 
    # Default limit for number of user's processes to prevent
    # accidental fork bombs.
    # See rhbz #432903 for reasoning.

    *          soft    nproc     2048
    root       soft    nproc     unlimited

rpm -Uvh jdk-8u121-linux-x64.rpm
./bin/elasticsearch 


需要文件：
lustre 文件
collectl装好
