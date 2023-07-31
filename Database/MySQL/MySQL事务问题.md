MySQL 事务问题





OperationalError("(pymysql.err.OperationalError) (1205, 'Lock wait timeout exceeded; try restarting transaction')")

原因：后提交的事务等待前面处理的事务释放锁，但是在等待的时候超过了mysql的锁等待时间，就会引发这个异常。