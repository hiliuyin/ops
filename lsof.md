# lsof - list open files

#### 获取用户ubuntu的所有连接
```sudo lsof -u ubuntu```
```
COMMAND   PID   USER   FD   TYPE             DEVICE SIZE/OFF     NODE NAME
sshd    13436 ubuntu  cwd    DIR              202,1     4096        2 /
sshd    13436 ubuntu  rtd    DIR              202,1     4096        2 /
sshd    13436 ubuntu  txt    REG              202,1   770944     8713 /usr/sbin/sshd
```

#### 获取系统的所有连接
```sudo lsof -i```
```
COMMAND     PID     USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
dhclient    701     root    5u  IPv4     8596      0t0  UDP *:bootpc 
dhclient    701     root   20u  IPv4     8551      0t0  UDP *:1065 
dhclient    701     root   21u  IPv6     8552      0t0  UDP *:8707 
nginx       917     root    6u  IPv4     8941      0t0  TCP *:http (LISTEN)
nginx       917     root    7u  IPv6     8942      0t0  TCP *:http (LISTEN)
sshd       1139     root    3u  IPv4     9174      0t0  TCP *:ssh (LISTEN)
sshd       1139     root    4u  IPv6     9176      0t0  TCP *:ssh (LISTEN)
redis-ser  1216    redis    4u  IPv6     9834      0t0  TCP *:6379 (LISTEN)
```

#### 获取所有基于TCP协议的连接
```sudo lsof -i tcp```
```
COMMAND     PID     USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
nginx       917     root    6u  IPv4     8941      0t0  TCP *:http (LISTEN)
nginx       917     root    7u  IPv6     8942      0t0  TCP *:http (LISTEN)
sshd       1139     root    3u  IPv4     9174      0t0  TCP *:ssh (LISTEN)
sshd       1139     root    4u  IPv6     9176      0t0  TCP *:ssh (LISTEN)
redis-ser  1216    redis    4u  IPv6     9834      0t0  TCP *:6379 (LISTEN)
redis-ser  1216    redis    5u  IPv4     9835      0t0  TCP *:6379 (LISTEN)
redis-ser  1216    redis    6u  IPv4  1621608      0t0  TCP ip-172-31-5-0.ap-southeast-1.compute.internal:6379->66.111.41.251:48623 (ESTABLISHED)
```

#### 获取所有基于UDP协议的连接
```sudo lsof -i udp```
```
COMMAND  PID USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
dhclient 701 root    5u  IPv4   8596      0t0  UDP *:bootpc 
dhclient 701 root   20u  IPv4   8551      0t0  UDP *:1065 
dhclient 701 root   21u  IPv6   8552      0t0  UDP *:8707 
```

#### 获取所有80端口(http)的连接
```sudo lsof -i :http```
```sudo lsof -i :80```
```
COMMAND   PID     USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
nginx     917     root    6u  IPv4   8941      0t0  TCP *:http (LISTEN)
nginx     917     root    7u  IPv6   8942      0t0  TCP *:http (LISTEN)
nginx   27968 www-data    6u  IPv4   8941      0t0  TCP *:http (LISTEN)
nginx   27968 www-data    7u  IPv6   8942      0t0  TCP *:http (LISTEN)
nginx   27969 www-data    6u  IPv4   8941      0t0  TCP *:http (LISTEN)
nginx   27969 www-data    7u  IPv6   8942      0t0  TCP *:http (LISTEN)
```

#### 获取所有基于TCP协议并且端口为5432的连接
```sudo lsof -nP -i tcp:5432```
```
COMMAND     PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
docker-pr 29076 root    4u  IPv6 9954082      0t0  TCP *:5432 (LISTEN)
```

#### 不显示host 也 不显示 端口俗称
```sudo lsof -nP -i tcp```
```
COMMAND     PID USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
docker-pr 28640     root    4u  IPv6  9951706      0t0  TCP *:8889 (LISTEN)
docker-pr 28990     root    4u  IPv6  9953769      0t0  TCP *:8080 (LISTEN)
docker-pr 29076     root    4u  IPv6  9954082      0t0  TCP *:5432 (LISTEN)
docker-pr 29155     root    4u  IPv6  9955393      0t0  TCP *:9696 (LISTEN)
```
```sudo lsof -i tcp```
```
docker-pr 28640     root    4u  IPv6  9951706      0t0  TCP *:8889 (LISTEN)
docker-pr 28990     root    4u  IPv6  9953769      0t0  TCP *:http-alt (LISTEN)
docker-pr 29076     root    4u  IPv6  9954082      0t0  TCP *:postgresql (LISTEN)
docker-pr 29155     root    4u  IPv6  9955393      0t0  TCP *:9696 (LISTEN)
```

#### 获取到指定主机的连接
```sudo lsof -nP -i @117.185.42.210```
```
COMMAND     PID   USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
redis-ser  1216  redis   16u  IPv4  6644066      0t0  TCP 172.31.5.0:6379->117.185.42.210:53396 (ESTABLISHED)
redis-ser  1216  redis   19u  IPv4  9624098      0t0  TCP 172.31.5.0:6379->117.185.42.210:58607 (ESTABLISHED)
redis-ser  1216  redis   22u  IPv4  9612776      0t0  TCP 172.31.5.0:6379->117.185.42.210:50054 (ESTABLISHED)
redis-ser  1216  redis   23u  IPv4 10142564      0t0  TCP 172.31.5.0:6379->117.185.42.210:50993 (ESTABLISHED)
redis-ser  1216  redis   26u  IPv4 10145821      0t0  TCP 172.31.5.0:6379->117.185.42.210:52816 (ESTABLISHED)
sshd      13280   root    3u  IPv4 10189607      0t0  TCP 172.31.5.0:22->117.185.42.210:61613 (ESTABLISHED)
sshd      13436 ubuntu    3u  IPv4 10189607      0t0  TCP 172.31.5.0:22->117.185.42.210:61613 (ESTABLISHED)
```

#### 获取监听端口
```sudo lsof -i -sTCP:LISTEN```
```
COMMAND     PID     USER   FD   TYPE   DEVICE SIZE/OFF NODE NAME
nginx       917     root    6u  IPv4     8941      0t0  TCP *:http (LISTEN)
nginx       917     root    7u  IPv6     8942      0t0  TCP *:http (LISTEN)
sshd       1139     root    3u  IPv4     9174      0t0  TCP *:ssh (LISTEN)
sshd       1139     root    4u  IPv6     9176      0t0  TCP *:ssh (LISTEN)
redis-ser  1216    redis    4u  IPv6     9834      0t0  TCP *:6379 (LISTEN)
redis-ser  1216    redis    5u  IPv4     9835      0t0  TCP *:6379 (LISTEN)
```

#### 获取某进程打开的连接
```lsof -p 917```
```
COMMAND PID USER   FD   TYPE             DEVICE SIZE/OFF   NODE NAME
nginx   917 root  cwd    DIR              202,1     4096      2 /
nginx   917 root  rtd    DIR              202,1     4096      2 /
nginx   917 root  txt    REG              202,1  1053616  35988 /usr/sbin/nginx
nginx   917 root  DEL    REG                0,4            8940 /dev/zero
```
