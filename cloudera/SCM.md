# SCM #

### 配置 ###

1. 安装目录

    - `/var/log/cloudera-scm-installer`: 安装日志目录
    - `/var/log/%` : 相关日志文件
    - `/usr/share/cmf/`： 程序安装目录
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

3. SCM结构

    SCM分为server与agent两部分及数据库（自带更改过的嵌入Postgresql）

4. 卸载

    `sudo /usr/share/cmf/uninstall-scm-express.sh`, 然后删除`/var/lib/cloudera-scm-server-db/`目录，不然下次安装可能不成功。

5. 权限

    SCM在安装各服务时会为各服务创建相应的用户，出现问题时首先查看是否为权限问题。

6. 数据库

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

7. 升级

    在SCM中可以通过界面向导升级相关服务。升级过程为三步：1.下载服务软件包；2.把所下载的服务软件包分发到集群中受管的机器上；3. 安装服务软件包，使用软链接的方式把服务程序目录链接到新安装的软件包目录上。

8. 监控

    todo

9. 告警

    todo
    
### 快速命令 ###

- kill 所有java进程
    
    `ps -eaf|grep -in 'java' |awk '{print $2}' |xargs kill -9[/b]`

- Postgresql命名

    连接数据库： psql -U 用户名 -h 主机 -p 端口 -W 密码
    查看所有库：\l
    查看所有表： \dt
    查看某个表的结构： \d 表名
    查看表数据： select * from 表名