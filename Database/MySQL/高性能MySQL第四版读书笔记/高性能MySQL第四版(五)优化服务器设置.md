MySQL 的服务器配置

查询MySQL的配置

```shell
which mysqld
```

根据找到的mysqld 在进行获取配置文件

```shell
/usr/sbin/mysqld --verbose --help | gerp -A 1 'Default options'
```

```shell
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf 
```

