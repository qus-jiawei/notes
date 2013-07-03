# SCM #

### 配置 ###

1. 安装目录

    - `/var/log/cloudera-scm-installer`: 安装日志目录
    - `/var/log/%` : 相关日志文件
    - `/usr/share/cmf/`： 程序安装目录
    - `/usr/lib64/cmf/` : agent程序代码
    - `/var/lib/cloudera-scm-server-db/data`： 内嵌数据库目录
    - `/usr/bin/postgres`: 内嵌数据库程序
    - `/etc/cloudera-scm-agent/`: agent的配置目录
    - `/etc/cloudera-scm-server/` : server的配置目录
    - `/opt/cloudera/parcels/` : Hadoop相关服务安装目录
    - `/opt/cloudera/parcel-repo/` : 下载的服务软件包数据，数据格式为parcels
    - `/opt/cloudera/parcel-cache/` : 下载的服务软件包缓存数据

2. Hadoop配置文件
    
    配置文件放置于`/var/run/cloudera-scm-agent/process/`目录下。如：`/var/run/cloudera-scm-agent/process/193-hdfs-NAMENODE/core-site.xml`。这些配置文件是通过Cloudera Manager启动相应服务（如HDFS）启动时生成的，内容从数据库中获得（即通过界面配置的参数）。
    
    > 在SCM界面上更改配置是不会立即反映到配置文件中，这些信息会存储于数据库中，等下次重启服务时才会生成配置文件。且每次启动时都会产生新的配置文件。

    SCM Server主要数据库为scm基中放置配置的表为scm.configs.里面包含了服务的配置信息，每一次配置的更改，它更是把当前页面的所有配置全部添加到数据库中，以此保存配置修改历史。

    > scm数据被配置成只能从localhost访问，如果需要从外部查看数据，数据修改`vim /var/lib/cloudera-scm-server-db/data/pg_hba.conf`文件,之后重启数据库。运行数据库的用户为cloudera-scm

    1. 配置修改

        SCM对于需要修改的配置预先定义，对于没有预先定义的配置,则通过在高级配置项中使用xml配置片段的方式进行配置。而对于/etc/hadoop/下的配置文件进行的修改不会生效。

    2. 配置生成方式
    
        SCM为每个服务进程生成独立的配置目录（文件）。所有配置统一在服务端查询数据统一生成（因为scm数据库只能在localhost下访问），再由agent通过网络下载包含配置的zip包到本地解压到指定的目录。

3. 数据库(这里使用默认的数据名)

    - scm: cloudera manager数据库,存储cloudera manager运行所需要的信息：配置，主机，启动脚本等。

    - amon:

    - hmon:

    - rman:

    - smon:

    - nav:


4. SCM结构

    SCM分为server与agent两部分及数据库（自带更改过的嵌入Postgresql）

    1. Server端主体使用Java实现。

    
    2. Agent端主体使用Python, 服务的启动通过调用相应的shell脚本进行启动，如果启动失败会重复4次调用启动脚本。


5. 服务启动

    - 服务启动为重试4次.


5. 卸载

    `sudo /usr/share/cmf/uninstall-scm-express.sh`, 然后删除`/var/lib/cloudera-scm-server-db/`目录，不然下次安装可能不成功。

6. 权限

    SCM在安装各服务时会为各服务创建相应的用户，出现问题时首先查看是否为权限问题。

7. 数据库

    CM数据（scm）： `psql -U scm -h localhost -p 7432 -W `。 密码： `66pSWqfKO0` 这个数据库最为重要，它存储了服务的配置、角色、配置历史、用户、运行的进程。

        #数据库
         amon      | amon         | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         hmon      | hmon         | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         nav       | nav          | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         postgres  | cloudera-scm | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         rman      | rman         | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         scm       | scm          | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         smon      | smon         | UTF8     | en_US.UTF8 | en_US.UTF8 | 
         template0 | cloudera-scm | UTF8     | en_US.UTF8 | en_US.UTF8 | =c/"cloudera-scm"
                                                                       : "cloudera-scm"=CTc/"cloudera-scm"
         template1 | cloudera-scm | UTF8     | en_US.UTF8 | en_US.UTF8 | =c/"cloudera-scm"
                                                                       : "cloudera-scm"=CTc/"cloudera-scm"

8. 升级

    在SCM中可以通过界面向导升级相关服务。升级过程为三步：1.下载服务软件包；2.把所下载的服务软件包分发到集群中受管的机器上；3. 安装服务软件包，使用软链接的方式把服务程序目录链接到新安装的软件包目录上。

9. 监控

    todo

10. 告警

    todo
    
### 快速命令 ###

- kill 所有java进程
    
    `ps -eaf|grep -in 'java' |awk '{print $2}' |xargs kill -9[/b]`

- Postgresql命令

    连接数据库： psql -U 用户名 -h 主机 -p 端口 -W 密码

    查看所有库：\l

    查看所有表： \dt

    查看某个表的结构： \d 表名

    查看表数据： select * from 表名


- 开启postgresql远程访问

    - scm数据被配置成只能从localhost访问，如果需要从外部查看数据，数据修改`vim /var/lib/cloudera-scm-server-db/data/pg_hba.conf`文件,之后重启数据库。运行数据库的用户为cloudera-scm