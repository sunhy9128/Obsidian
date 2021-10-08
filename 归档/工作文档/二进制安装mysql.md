# 二进制安装mysql

[VUE项目构建问题](VUE项目构建问题.md)
```shell
tar -xvf mysql-5.7.23-linux-glibc2.12-x86_64.tar.gz
mv mysql-5.7.23-linux-glibc2.12-x86_64 /usr/local/mysql
cd /usr/local/mysql/
mkdir data
chown  -R mysql:mysql .
./bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data
// -he/uTBx#2=o
cp support-files/mysql.server /etc/init.d/mysql
vi /etc/init.d/mysql
vi /etc/my.cnf
/etc/init.d/mysql start
ln -s /usr/local/mysql/bin/mysql /usr/bin/

## /etc/my.cnf
[mysqld]
lower_case_table_names=1
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
log-error=/usr/local/mysql/mysql.err
user=mysql
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
pid-file=/usr/local/mysql/mysql.pid
symbolic-links=0
```

