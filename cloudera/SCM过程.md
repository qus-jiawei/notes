# SCM 单机安装 #

### 1.准备 ###

- 关闭seliunx

- 开放相应端口或关闭防火墙

- 配置/etc/hosts

### 2.安装 ###

- 上传`cloudera-manager-installer.bin`

- 修改权限`a+x`; 执行`./cloudera-manager-installer.bin`;照着向导一步一步往下安装。

- 打开浏览器`http://192.168.56.101:7180/`进行页面安装。

- 选择Cloudera Standard进行安装

- 使用内嵌数据库，因为使用的网络特别是虚拟化系统时,如果数据主机名为空，则需要手动修改`vim /etc/cloudera-scm-server/db.mgmt.properties`文件把数据库主机名添加上。再`/etc/init.d/cloudera-scm-server restart` 重启SCM Server服务。

    > 不过这个有问题，就是重启后重新安装时因为已有数据界面上有操作会进行不下去，解决的方式是。。。