# docker-snippets

### How to verify the status and the health of the Docker Daemon
#### sudo service docker status
```
ubuntu@iZk1a3fdjhn44xmmyyyhp8Z:~$ service docker status
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2018-04-28 14:38:17 CST; 2 months 7 days ago
     Docs: https://docs.docker.com
 Main PID: 1348 (dockerd)
    Tasks: 46
   Memory: 392.2M
      CPU: 8h 15min 21.934s
   CGroup: /system.slice/docker.service
           ├─ 1348 /usr/bin/dockerd -H fd://
           ├─ 1355 docker-containerd --config /var/run/docker/containerd/containerd.toml
           ├─29578 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8889 -container-ip 172.18.0.2 -container-port 8889
           └─29584 docker-containerd-shim -namespace moby -workdir /var/lib/docker/containerd/daemon/io.containerd.runtime.v1.linux/moby/28e3e760f5f0e7fce358bdf5af5466a9cffee7915c1a4c0011678c8df618bb8d -a
Warning: Journal has been rotated since unit was started. Log output is incomplete or unavailable.
```

#### sudo systemctl status docker
```
ubuntu@iZk1a3fdjhn44xmmyyyhp8Z:~$ systemctl status docker
● docker.service - Docker Application Container Engine
   Loaded: loaded (/lib/systemd/system/docker.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2018-04-28 14:38:17 CST; 2 months 7 days ago
     Docs: https://docs.docker.com
 Main PID: 1348 (dockerd)
    Tasks: 46
   Memory: 392.5M
      CPU: 8h 15min 22.542s
   CGroup: /system.slice/docker.service
           ├─ 1348 /usr/bin/dockerd -H fd://
           ├─ 1355 docker-containerd --config /var/run/docker/containerd/containerd.toml
           ├─29578 /usr/bin/docker-proxy -proto tcp -host-ip 0.0.0.0 -host-port 8889 -container-ip 172.18.0.2 -container-port 8889
           └─29584 docker-containerd-shim -namespace moby -workdir /var/lib/docker/containerd/daemon/io.containerd.runtime.v1.linux/moby/28e3e760f5f0e7fce358bdf5af5466a9cffee7915c1a4c0011678c8df618bb8d -a
Warning: Journal has been rotated since unit was started. Log output is incomplete or unavailable.
```

### Where is the Docker Daemon log?
```
Ubuntu (old using upstart)              - /var/log/upstart/docker.log
Ubuntu (new using systemd, from 15.04+) - sudo journalctl -fu docker.service
Boot2Docker                             - /var/log/docker.log
Debian GNU/Linux                        - /var/log/daemon.log
CentOS                                  - /var/log/daemon.log | grep docker
CoreOS                                  - journalctl -u docker.service
Fedora                                  - journalctl -u docker.service
Red Hat Enterprise Linux Server         - /var/log/messages | grep docker
OpenSuSE                                - journalctl -u docker.service
OSX                                     - ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/log/docker.log
Windows                                 - Get-EventLog -LogName Application -Source Docker -After (Get-Date).AddMinutes(-5) | Sort-Object Time
```
 
### Devicemapper: Error starting daemon with "devicemapper: Can't set cookie dm_task_set_cookie failed"
Start daemon with the following error:
```
level=error msg="[graphdriver] prior storage driver devicemapper failed: devicemapper: Can't set cookie dm_task_set_cookie failed
Error starting daemon: error initializing graphdriver: devicemapper: Can't set cookie dm_task_set_cookie failed
```
Output of `docker version`
```
Client:
 Version:      17.06.0-ce
 API version:  1.30
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:19:16 2017
 OS/Arch:      linux/amd64

Server:
 Version:      17.06.0-ce
 API version:  1.30 (minimum version 1.12)
 Go version:   go1.8.3
 Git commit:   02c1d87
 Built:        Fri Jun 23 21:17:13 2017
 OS/Arch:      linux/amd64
 Experimental: false
```
This failure to do so results in leaks of semaphores in the LVM code, eventually leading to semaphore exhaustion.
This problem has been as identified in 17.06.0, and has been fixed in 17.06.2.

Refer the following links to solve this issue:

https://help.replicated.com/community/t/error-message-can-not-set-cookie-dm-set-cookie-failed/54/1

https://github.com/docker/for-linux/issues/85

### How to enable DEBUG log level for docker daemon?
Add ```{"debug": true}``` into ```/etc/docker/daemon.json```


