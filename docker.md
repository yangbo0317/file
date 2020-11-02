

#Docker常用命令

## 总结

<img src="/Users/allin264/Library/Application Support/typora-user-images/image-20201101210424995.png" alt="image-20201101210424995" style="zoom:50%;" />

```shell
Commands:
  attach      Attach local standard input, output, and error streams to a running container
  build       Build an image from a Dockerfile
  commit      Create a new image from a container's changes
  cp          Copy files/folders between a container and the local filesystem
  create      Create a new container
  diff        Inspect changes to files or directories on a container's filesystem
  events      Get real time events from the server
  exec        Run a command in a running container
  export      Export a container's filesystem as a tar archive
  history     Show the history of an image
  images      List images
  import      Import the contents from a tarball to create a filesystem image
  info        Display system-wide information
  inspect     Return low-level information on Docker objects
  kill        Kill one or more running containers
  load        Load an image from a tar archive or STDIN
  login       Log in to a Docker registry
  logout      Log out from a Docker registry
  logs        Fetch the logs of a container
  pause       Pause all processes within one or more containers
  port        List port mappings or a specific mapping for the container
  ps          List containers
  pull        Pull an image or a repository from a registry
  push        Push an image or a repository to a registry
  rename      Rename a container
  restart     Restart one or more containers
  rm          Remove one or more containers
  rmi         Remove one or more images
  run         Run a command in a new container
  save        Save one or more images to a tar archive (streamed to STDOUT by default)
  search      Search the Docker Hub for images
  start       Start one or more stopped containers
  stats       Display a live stream of container(s) resource usage statistics
  stop        Stop one or more running containers
  tag         Create a tag TARGET_IMAGE that refers to SOURCE_IMAGE
  top         Display the running processes of a container
  unpause     Unpause all processes within one or more containers
  update      Update configuration of one or more containers
  version     Show the Docker version information
  wait        Block until one or more containers stop, then print their exit codes
```



##一、镜像命令

**docker images** 查询所有镜像

**docker search** 搜索镜像

```
➜  ~ docker search mysql --filter=stars=3000
NAME                DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql               MySQL is a widely used, open-source relation…   10121               [OK]
mariadb             MariaDB is a community-developed fork of MyS…   3716                [OK]
➜  ~
```

**docker pull** 拉去镜像

**docker pull:tag** 指定版本下载

**docker rmi -f $(docker images -aq)**  删除所有的容器

##二、容器命令

**docker pull centos**

```
Using default tag: latest
latest: Pulling from library/centos
3c72a8ed6814: Pull complete
Digest: sha256:76d24f3ba3317fa945743bb3746fbaf3a0b752f10b10376960de01da70685fbd
Status: Downloaded newer image for centos:latest
docker.io/library/centos:latest
```

**docker run**

```
dicker run [可选参数] image
# 参数名字
-name="name" 容器名称，用来区分容器
-d 后台方式启动
-it 使用交互方式运行，进入容器查看
-p 指定容器的端口 -p 8080:8080
	-p ip:主机端口：容器端口
	-p 主机端口：容器端口【映射-常用】
	-p 容器端口
-P 指定随机端口

# 测试
➜  ~ docker run -it centos /bin/bash
```

**列出正在运行的容器**

```
docker ps 
-a 历史的运行过的容器也会显示
```

**退出容器**

```shell
exit #退出直接停止
Ctrl + p+q # 退出容器，不停止
```

删除容器

```shell
docker rm 容器ID
docker rm -f $(docker ps  -aq) #删除所有的容器
docker ps -a -q |xargs docker rm # 删除所有的容器
```

容器的启动和停止

```shell
docker start 容器ID # 启动容器
docker restart 容器ID # 重启容器
docker stop 容器ID # 停止当前正在运行的容器
docker kill 容器ID # 强制停止当前的容器
```

## 三、常用的其他命令

**后台启动**

```shell
➜  ~ docker run -d centos
9268513f7827b179bc571eb7a0bb8f7afbdd0d6f7d0d3feadf36b91f0ff6b627
# docker ps 没有运行的容器
# 常见的坑：docker容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止
```

**查看日志**

```
docker logs
➜  ~ docker logs --help
Usage:	docker logs [OPTIONS] CONTAINER
Fetch the logs of a container
Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)
      --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37) or relative (e.g. 42m for 42 minutes)
```

```shell
docker 
-tf 显示日志
-tail 10 显示的条数
docker -tf -tail 10 容器ID
```

**查看容器中的进程信息**

```shell
top命令
➜  ~ docker top 60c7c52716c6
PID                 USER                TIME                COMMAND
2901                root                0:00                /bin/bash -c while true;do echo yangbo;sleep 1;done
3131                root                0:00                {sleep} /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 1
➜  ~
```

**查看容器元数据**

```shell
docker inspect 容器ID

➜  ~ docker inspect 60c7c52716c6
[
    {
        "Id": "60c7c52716c692ad0334cc7187773fff3e630cefa34a9c99e07adf83ded1b168",
        "Created": "2020-11-01T12:43:39.1197814Z",
        "Path": "/bin/bash",
        "Args": [
            "-c",
            "while true;do echo yangbo;sleep 1;done"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 2901,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2020-11-01T12:43:39.4010348Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:0d120b6ccaa8c5e149176798b3501d4dd1885f961922497cd0abef155c869566",
        "ResolvConfPath": "/var/lib/docker/containers/60c7c52716c692ad0334cc7187773fff3e630cefa34a9c99e07adf83ded1b168/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/60c7c52716c692ad0334cc7187773fff3e630cefa34a9c99e07adf83ded1b168/hostname",
        "HostsPath": "/var/lib/docker/containers/60c7c52716c692ad0334cc7187773fff3e630cefa34a9c99e07adf83ded1b168/hosts",
        "LogPath": "/var/lib/docker/containers/60c7c52716c692ad0334cc7187773fff3e630cefa34a9c99e07adf83ded1b168/60c7c52716c692ad0334cc7187773fff3e630cefa34a9c99e07adf83ded1b168-json.log",
        "Name": "/cool_rosalind",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "Capabilities": null,
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/78b76f9e534112eadd2afe8820edccb1a657d2201828847b8468dd9c4e2847a6-init/diff:/var/lib/docker/overlay2/f5e0b281c089f27288ecd5b3f5a5c145793ce5eb527c55ac97c7844a7fa731a5/diff",
                "MergedDir": "/var/lib/docker/overlay2/78b76f9e534112eadd2afe8820edccb1a657d2201828847b8468dd9c4e2847a6/merged",
                "UpperDir": "/var/lib/docker/overlay2/78b76f9e534112eadd2afe8820edccb1a657d2201828847b8468dd9c4e2847a6/diff",
                "WorkDir": "/var/lib/docker/overlay2/78b76f9e534112eadd2afe8820edccb1a657d2201828847b8468dd9c4e2847a6/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "60c7c52716c6",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash",
                "-c",
                "while true;do echo yangbo;sleep 1;done"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20200809",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "6061492afd319648d2f2ad449c3630afa876d6a1aaa1ab5db80b3248a5ec1740",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/6061492afd31",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "e958a633c5dd134b034933b4f0322eb9169f533209e7c7647392f4841fb229de",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "529c77efbcda5227b48b5d2a17197fd5dc965c5a548e52639579fca0ecccb2a4",
                    "EndpointID": "e958a633c5dd134b034933b4f0322eb9169f533209e7c7647392f4841fb229de",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

**进入当前正在运行的容器**

```shell
# 进入当前容器
#方式一
docker exec -it  容器ID /bin/bash # 进入容器后开启一个新的终端，可以在里面操作
#方式二
docker attach 容器ID # 进入容器正在执行的终端，不会启动新的进程
```

**从容器中拷贝文件到宿主机**

```shell
# docker cp 容器ID:容器路径 宿主机路径
docker cp ef5c591f0239:/home/yangbo.java /Users/allin264
#拷贝是手动过程 -V 可以实现自动同步
```

##四、练习

###（1）nginx安装部署

>Docker 安装Nginx

```shell
#搜索镜像
docker search nginx
# 下载镜像
docker pull nginx
# 运行容器 -d 后台启动 --name 指定名称 -p 指定端口 
docker run -d --name nginx01  -p 3344:80 nginx
# 进入容器 
docker exec -it 6ba2ebd7ab6a
```

<img src="/Users/allin264/Library/Application Support/typora-user-images/image-20201101212159894.png" alt="image-20201101212159894" style="zoom:50%;" />

### （2）Tomcat安装部署

```shell
# 用完即删除
doceker run -it --rm tomcat:9.0
# 下载启动
➜  ~ docker pull tomcat
Using default tag: latest
latest: Pulling from library/tomcat
Digest: sha256:753eb1ac3f014a0fe5ba66d5563ff6523b2da5cb683f8b7d6c02f9f671335abe
Status: Downloaded newer image for tomcat:latest
docker.io/library/tomcat:latest
# 进入容器
docker exec -it tomcat01 /bin/bash
# 发现问题
1：Linux命令少了
2：没有webApps a阿里云镜像的原因，默认是最小的镜像，提出了所有不必要的文件，只保证最小可运行的环境
解决
cd tomcat 
cp -r webapps.dist/* webapps
访问：解决
```

```shell
#下载镜像
docker pull tomcat
➜  ~ docker pull tomcat
Using default tag: latest
latest: Pulling from library/tomcat
Digest: sha256:753eb1ac3f014a0fe5ba66d5563ff6523b2da5cb683f8b7d6c02f9f671335abe
Status: Image is up to date for tomcat:latest
docker.io/library/tomcat:latest
# -d 后台启动 --name 服务名称 -p 映射宿主机与docker容器的端口 -it 交互执行
➜  ~ docker run -d --name="tomcat" -p 8080:8080 -it tomcat
0d3dbb74609e88edb8744eac6825911ffd46d95d14e0613e7e4e573b193e145c
#将webapps填充
root@0d3dbb74609e:/usr/local/tomcat# cp -r webapps.dist/* webapps
访问localhost:8080
```



###（3）部署ES+Kibana

官方文档https://hub.docker.com/_/elasticsearch

**ES**

```shell
# ES 暴露的端口多
# ES 耗内存
# --net somenetwork 网络配置
# 下载部署
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.9.3
#访问
curl localhost:9200
{
  "name" : "c71fe638e59f",
  "cluster_name" : "docker-cluster",
  "cluster_uuid" : "3PSZ5PZSQl2OeKp0waZreg",
  "version" : {
    "number" : "7.9.3",
    "build_flavor" : "default",
    "build_type" : "docker",
    "build_hash" : "c4138e51121ef06a6404866cddc601906fe5c868",
    "build_date" : "2020-10-16T10:36:16.141335Z",
    "build_snapshot" : false,
    "lucene_version" : "8.6.2",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
# 启动后，服务器很卡

docker stats #查看内存占用
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
c71fe638e59f        elasticsearch       0.71%               1.245GiB / 1.945GiB   64.03%              1.57kB / 942B       38.5MB / 1.25MB     48
```

```shell
# 增加内存限制启动
docker run -d --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e ES_JAVA_OPTS="-Xms64m -Xmx512m" elasticsearch:7.9.3
# docker stats 
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
053ad0399c91        elasticsearch       187.03%             337.8MiB / 1.945GiB   16.96%              766B / 0B           11.4MB / 377kB      27


curl localhost:9200
```

###（4）可视化

```
docker run -d -p 8088:9000 \
--restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

<img src="/Users/allin264/Library/Application Support/typora-user-images/image-20201101221520211.png" alt="image-20201101221520211" style="zoom:50%;" />

# Docker镜像

## 一、镜像是什么

**镜像是一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，它包含运行某个软件所需的所有内容，包括代码、运行时、库、环境变量和配置文件**

###（1）UnionFS(联合文件系统)

UnionFS（联合文件系统）：Union文件系统是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下，Union文件系统是Dokcer镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统加载起来，这样最终的文件系统会包含所有的底层文件和目录

###（2）镜像加载原理

docker的镜像实际上是由一层一层的文件系统构成，这种层级的文件系统UnionFS。

主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这一层与我们典型的linux/unix系统是一样的，包含boot加载器内核。当boot加载完之后整个内核就都在内存中了，此时内存的使用权已经由bootfs交给内核了，此时系统也会卸载bootfs

**平时我们安装进虚拟机的CentOS都是好几个G，为什么docker这里才200M**

![img](https://pic1.zhimg.com/80/v2-7c51994a1949ec6f591b29a06ca8d118_1440w.png)

对以一个精简的OS，rootfs可以很小，只需要包括最基本的命令、工具和程序库就行，因为底层直接用host和kernel，自己只需要提供rootfs就行。由此可见对于不同的Linux发行版，bootfs基本是一致的，rootfs会有差别，因此不同的发行版可以公用bootfs。

###（3）分层的镜像

以pull为例，在下载的过程中我么可以看到docker的镜像好像是在一层一层的下载



![img](https://pic1.zhimg.com/80/v2-f07098668bddef29bc2113143c01bcf0_1440w.jpg)

###（4）为什么Docker镜像要采用这种分层的结构

最大一个好处就是——共享资源

比如：有多个镜像都从相同的base镜像构建而来，那么宿主机只需要在磁盘上保存一份base镜像，同时内存中也需要加载一份base镜像，就可以为所有服务器服务了。而且镜像的每一层都可以被共享。

##二、镜像特点

docker镜像都是只读的

当容器启动时，一个新的可写层被加载到镜像的顶部。

这一层通常被称作“容器层”，“容器层”之下都叫“镜像层”

![image-20201101223243028](/Users/allin264/Library/Application Support/typora-user-images/image-20201101223243028.png)

## 三、提交

提交tomcat

```shell
# -a 提交的作者 -m 提交备注
docker commit -a="yangbo" -m="测试提交" 0d3dbb74609e tomcat:1.0
➜  ~ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
tomcat              1.0                 13f87e9c144e        13 seconds ago      653MB
tomcat              latest              625b734f984e        10 days ago         648MB
```

# 容器卷

## 一、使用数据卷

>方式一、使用命令挂在

```shell
# 使用的命令
	1、挂载启动。docker run -d -it -v /Users/yangbo/docker:/home centos /bin/bash
	2、查看运行的容器。 docker ps 
	3、进入容器 docker exec -it 容器ID /bin/bash
# 宿主机/Users/yangbo/docker 目录下心间yangbo.txt文件 ，在容器/home里可以看到文件
# 停止容器 exit
# 在宿主机/Users/yangbo/docker下修改yangbo.txt文件
# 进入容器（使用docker ps -a 查看运行之前启动的容器）
docker exec -it 容器ID /bin/bash
# 查看容器内的文件，也是被修改的
```

```shell
➜  ~ docker exec -it a1b0489fb810 /bin/bash
[root@a1b0489fb810 /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@a1b0489fb810 /]# cd home/
[root@a1b0489fb810 home]# ls
yangbo.txt
[root@a1b0489fb810 home]#
```

## 二、mysql实战

```shell
# 获取镜像
docker pull mysql:5.7

➜  ~ docker pull mysql:5.7
5.7: Pulling from library/mysql
bb79b6b2107f: Pull complete
49e22f6fb9f7: Pull complete
842b1255668c: Pull complete
9f48d1f43000: Pull complete
c693f0615bce: Pull complete
8a621b9dbed2: Pull complete
0807d32aef13: Pull complete
f15d42f48bd9: Pull complete
098ceecc0c8d: Pull complete
b6fead9737bc: Pull complete
351d223d3d76: Pull complete
Digest: sha256:4d2b34e99c14edb99cdd95ddad4d9aa7ea3f2c4405ff0c3509a29dc40bcb10ef
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

```shell
# 官方demo
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
```

```shell
# 启动参数 -d 后台启动 -p 端口映射 -v 挂载 -e 密码 --name
docker run -d -p 3306:3306 -v /Users/yangbo/docker/mysql/comf:/etc/mysql/conf.d -v /Users/yangbo/docker/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
```

**测试连接**

![image-20201102213409963](/Users/yangbo/Library/Application Support/typora-user-images/image-20201102213409963.png)

**测试数据同步**

使用ni vaca t创建数据库，在docker中可以看到数据更新

