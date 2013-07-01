### 1.架构 ###

### 2.组件 ###

### 3.安装 ###

- 使用本地库

    Cloudera网速有时不稳定，下载失败后需要重下，所以可以使用其它下载工具先下载到本地，再使用本地库进行安装。

    [如何使用本地库][1]
    [1]: http://www.cloudera.com/content/cloudera-content/cloudera-docs/CM4Ent/latest/Cloudera-Manager-Installation-Guide/cmig_create_local_repo.html#cmig_topic_21_3

- 需要sudo权限的操作

        yum (Red Hat/CentOS/Oracle)
        zypper (SLES)
        apt-get (Debian/Ubuntu)
        apt-key (Debian/Ubuntu)
        sed
        service
        /sbin/chkconfig (Red Hat/CentOS/Oracle)
        /usr/sbin/update-rc.d (Debian/Ubuntu)
        id
        rm
        mv
        chown
        install 

- CM server通过root SSH发现集群的主机，在向导中可以使用IP段或主机名

- CM默认使用嵌入postgreSql -- 使用者：Cloudera Manager, Service Monitor,
Activity Monitor, Host Monitor, Report Manager, Cloudera Navigator, and the Hive Metastore。如果要使用外部数据库如mysql需要预先准备好。

- CM相关的安装升级只有CM server需要连接外网下载软件包，其它主机则通过使用CM server的本地库（/opt/cloudera/parcels）。

- CM相关目录

        /var/log/% : 相关日志文件
        /usr/share/cmf/： 程序安装目录
        /var/lib/cloudera-scm-server-db/data/: 内嵌数据库存储目录
        /usr/bin/postgres: 内嵌数据库程序

- 所有主机都必须配置一致的主机名与IP 配置`/etc/hosts`

- 需要关闭SELinux和开发所用到的端口。

- CM为相应的服务组件创建相应的用户与群组，不要去删除与改变它们。

- CM angent以root用户运行，因为它需要创建相应的目录，确定相应的用户运行相应的服务。

- CM 会创建多个数据库用于存储信息：数据库需要支持UTF-8

        1. configured services, role assignments, configuration history, commands, users, and running processes
        2. Activity Monitor, Service Monitor, Report Manager, and Host Monitor
        3. Oozie, Hue, and Hive Metastore，Cloudera Navigator

- 数据库使用：
        
    - Cloudera Manager database: This is the most important database to back up. This database contains all the information about what services you have configured, their role assignments, all configuration history,commands, users, and running processes. This is a relatively small database, typically smaller than 100MB.
    - Activity Monitor database: Contains information about past activities. In large clusters, this database can become very large.
    - Service Monitor database: Contains monitoring information about daemons. In large clusters, this database can become very large.
    - Report Manager database: Keeps track of disk utilization over time. This database is typically medium-sized.
    - Host Manager database: Contains information about host status. The number of hosts in the cluster affects this database's size, so the database size varies, but the database is typically large in deployments with many hosts.
         

- 如果使用外部Mysql为了防止死锁Mysql需要把隔离设置为`read committed`, 存储引擎为`InnoDB`而不能为`MyISAM`，设置`innodb_flush_method 为 O_DIRECT`， 需要安装JDBC驱动：`sudo yum install mysql-connector-java` 。

- 使用外部数据库需要先创建下如数据库，数据名，用户名，密码随便

    - Activity Monitor
    - Service Monitor
    - Report Manager
    - Host Monitor
    - Cloudera Navigato
    还有就是hive的数据库


### 4.配置 ###

- 

### 5.使用 ###

- 启动集群时CM启动相应依赖。

### 6.维护 ###

### 7.升级 ###

- 可以在CM中升级CDH，Impala, Search，升级过程比较简单就是就是CM Server从远程库或本地库中拉取下载软件，再分发到各个主机中安装，升级采用软链接的方式进行，最后重启集群，升级完成。

- 对于CM的升级需要在每台主机上手动进行，其它会涉及到数据库schma的更改。过程也简单就是更新软件源，进行安装更新即可，主要是要对数据库进行备份。