https://dev.mysql.com/downloads/mysql/到这个网址选择下面的

![image-20211208145540424](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208145540424.png)

点击download后进入下载页面，复制下载链接地址。

![image-20211208145640584](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208145640584.png)

使用命令wget https://dev.mysql.com/get/Downloads/MySQL-8.0/mysql-8.0.27-linux-glibc2.12-x86_64.tar.xz将压缩包下载到本地。

![image-20211208145850749](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208145850749.png)

使用tar -xvf命令解压压缩包

![image-20211208150241628](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208150241628.png)

解压完成之后重命名并移动到 /usr/local目录下

![image-20211208153749865](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208153749865.png)

进入/usr/local/mysql目录下创建data目录

![image-20211208153844846](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208153844846.png)

创建mysql用户和用户组，更改mysql文件的权限

![image-20211208153927166](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208153927166.png)

![image-20211208153945081](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208153945081.png)

使用命令 ./mysqld --initialize --user=mysql --datadir=/usr/local/mysql/data --basedir=/usr/local/mysql初始化mysql

如果出现错误./mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory说明乜有安装libaio.so.1，使用命令 yum install  libaio-devel.x86_64安装libaio

![image-20211208154115889](C:\Users\lfl\AppData\Roaming\Typora\typora-user-images\image-20211208154115889.png)

配置/etc/my.cnf

```sql
[mysqld]
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
socket=/tmp/mysql.sock
character-set-server=utf8
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

[mysqld_safe]
# log-error=/var/log/mariadb/mariadb.log
# pid-file=/var/run/mariadb/mariadb.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
```

启动mysql

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'liu';

 flush privileges;

update User set Host = '%' where User = 'root';

好像不用开放3306端口。

MmoQYjK&>9?g

