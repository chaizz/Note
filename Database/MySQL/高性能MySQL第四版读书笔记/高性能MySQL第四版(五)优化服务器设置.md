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


配置文件说明
配置文件采用标准INI格式，被分为多个部分，每个部分都以一行包含在方括号中的该部分名称开头。客户端程序也会读取client部分，服务器通常读取mysqld部分。

配置使用小写字母表示，单词之间使用下划线或者短横分隔，两种表示方式都是有效的。（建议使用同一种风格）

除了在配置文件中进行设置外，很多变量（但不是全部）还可以在服务器运行时进行更改。MySQL将这些称为动态配置变量。但是在服务器重启之后更改项就会失效。

MySQL 8.0引入了一个名为持久化系统变量的新功能，语法为：`SET PERSIST`允许在运行时设置一次值，MySQL将把这个设置写入磁盘，以便在下次重启后继续使用该值。

> 我们建议专注于优化峰值工作负载，然后在“足够好”的时候就可以停止优化。


最小示例文件

```ini
[mysqld]
# 数据存放的位置
datadir                   = /var/lib/mysql
# socket 配置，建议显示的配置此值，具体路径按需定义
socket                    = /var/lib/mysql/mysql.sock 
# pid 配置，建议显示的配置此值，具体路径按需定义
pid_file                  = /var/lib/mysql/mysql.pid
# 确保用户存在，且有相关路径的操作权限
user                      = mysql
# 端口，生产环境课适当的更改
port                      = 3306

# 在MySQL 8.0中引入了一些新的配置选项， 适用于云服务器环境中。
# InnnoDB
innodb_buffer_pool_size   = <value>
innodb_log_file_size      = <value>
innodb_file_per_table     = 1
innodb_flush_method       = O_DIRECT

# 
innodb_log_buffer_size    = 8M

# logging
log_error                 = /var/lib/mysql/mysql-error.log
log_slow_queries          = /var/lib/mysql/mysql-slow.log

# other
tmp_table_size            = 32M
max_heap_table_size       = 32M
# 最大连接数
max_connections           = <value>
# 线程缓冲大小
thread_cache_size         = <value>
table_open_cache          = <value>
# 在Linux系统中可以将值设置的尽可能的大，如果这个设置不够大，就会看到经典的24号错误，“too many open files”。
open_file_limit           = 65535
[client]                 
socket                    = /var/lib/mysql/mysql.sock
port                      = 3306
```
