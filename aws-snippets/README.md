# AWS Admin Snippets
A collection of useful AWS administration snippets.

## Table of Contents
### OPS的某日经历
某日，开发询问OPS，为啥某台测试ec2上的容器的log里有大量显示连接RDS超时的异常，
登录该ec2, 查询了容器的log，果然持久层在连接数据库时报了超时异常

```
 ### The error occurred while executing a query
 ### Cause: org.springframework.jdbc.CannotGetJdbcConnectionException: Could not get JDBC Connection; nested exception is java.sql.SQLTransientConnectionException: HikariPool-1 - Connection is not available, request timed out after 30000ms.
        at org.apache.ibatis.exceptions.ExceptionFactory.wrapException(ExceptionFactory.java:30)
        at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:150)
        at org.apache.ibatis.session.defaults.DefaultSqlSession.selectList(DefaultSqlSession.java:141)
        at org.apache.ibatis.session.defaults.DefaultSqlSession.selectOne(DefaultSqlSession.java:77)
        at org.mybatis.spring.SqlSessionTemplate$SqlSessionInterceptor.invoke(SqlSessionTemplate.java:433) [3 skipped]
        at com.sun.proxy.$Proxy77.selectOne(Unknown Source)
        at org.mybatis.spring.SqlSessionTemplate.selectOne(SqlSessionTemplate.java:166)
        at org.apache.ibatis.binding.MapperMethod.execute(MapperMethod.java:82)
        ... 17 common frames omitted
```

在该ec2上尝试用psql连接数据库，发现可以正常连接
```
psql -h test-db.ct3lmpqmpvcq.ap-southeast-1.rds.amazonaws.com -U test testprod
```
奇怪的是，为啥容器内连接数据库会突然出现这种连接超时的问题呢？
难道是docker daemon出问题了？ 查询了docker daemon的log，里面一切正常
```
sudo cat /var/log/upstart/docker.log
Ubuntu 16.4+: sudo journalctl -u docker.service
```
尝试重启容器，无效；
尝试重启docker daemon，还是无效。
奇怪的问题！一筹莫展，只能把docker的debug模式打开，看看有啥线索没有
```
将{"debug": true}加入到/etc/docker/daemon.json里面，即可以打开docker daemon的debug信息
```

在docker daemon的log里面发现了如下几行输出
```
level=debug msg="Query test-db.ct3lmpqmpvcq.ap-southeast-1.rds.amazonaws.com.[1] from 172.18.0.3:50334, forwarding to udp:8.8.8.8"
level=debug msg="Name To resolve: test-db.ct3lmpqmpvcq.ap-southeast-1.rds.amazonaws.com."
```

看来docker使用了8.8.8.8作为DNS server，这样按理说也没啥问题啊
在shell里面用psql直连RDS的ip地址试试看，先
```dig @8.8.8.8 test-db.ct3lmpqmpvcq.ap-southeast-1.rds.amazonaws.com```
然后用psql直连该ip，果然连接失败，看来可以从此处顺藤摸瓜了。

查询该ec2的DNS server```cat /etc/resolv.conf```果然，输出的是```nameserver 172.31.0.2```而非```nameserver 8.8.8.8```

docker使用的DNS server居然和host机器的DNS server不一致，究竟是docker本身的问题还是我们自身的配置错误呢？

借助强大的google，果然有人碰到过类似的问题
https://github.com/moby/moby/issues/23910

现在貌似只有workaround，手动指定docker或者container的DNS server
对于docker，可以修改```/etc/docker/daemon.json```，增加DNS server的配置```{"dns": ["172.31.0.2"]}```
或者针对每个container，指定其使用的DNS server，我们使用的是docker-compose做的容器编排，于是修改docker-compose.yml
```
 container-1:
    image: test
    container_name: test-1
    dns: 172.31.0.2
    ports:
     - "1234:1234"
```

果然，这么改完以后，整个系统就恢复正常了。

但是为什么用公网IP就无法连接RDS呢，只能去看看RDS的配置，看看有啥线索没有？
看了一下安全组的配置，貌似没啥问题啊，没办法，难道对安全组的配置理解有误么？再仔细看一遍文档吧。
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html

发现了如下两段话：
```
Another security group. This allows instances associated with the specified security group to access instances associated with this security group. This does not add rules from the source security group to this security group. 
...
When you specify a security group as the source or destination for a rule, the rule affects all instances associated with the security group. Incoming traffic is allowed based on the private IP addresses of the instances that are associated with the source security group (and not the public IP or Elastic IP addresses). 
...
```
果然，配置有问题，在安全组内引用其他安全组并不会将其他安全组的rule引用过来。
哎，任何事情不能想当然啊。自责一下。

#### TODO:




