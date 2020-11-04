

# 镜像加速

阿里云 `https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors`

#Docker常用命令

## 总结

![Alt text](https://upload-images.jianshu.io/upload_images/12842279-f5d4c22882f4a649.png)

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
docker run -it --rm tomcat:9.0
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

##三、具名挂载和匿名挂载

```SHELL
# -P 随机端口 -v 挂载
docker run -d -P --name nginx01 -v /etc/nginx nginx
# 查看所有的卷情况
docker volume ls
➜  ~ docker volume ls
DRIVER              VOLUME NAME
local               00a443e281a4a31e87efc2f8ef5b9e83d19c3719e9b28935093a63b73ddd615a
local               2c5c6b3c246f24e25565b9dab3dd6488a2d16d1854e2e8b82b6c761f5368244b
local               2cae6ec7a2a3d45e9a4597cc58b7693f010afa21cde3c29c4c7b1a6287924c9b
local               2ee4ed9f6fc19dd1d6f3765a250ed32fe7b05401b878fe66bbae7336c9b6a376
local               5ffc1dd7fbf05d131d9e99e0b2d2a8c07dccd4ddc45dcef3755344856c0395ca
local               9c8000eea259e07e4df1a533dc907b9bf42ca16bf033bd5afd92c26e418f4bf7
local               43ce19b919cc5e7069f16da6317bf2839b7e4f1decc920d11ca32328beebb840
local               374e20ad87426efd90d3d41029573742daed3233640969a16830fb2d6d1749fd
local               1604f7de3773e6506fc6c5a22419b555d9b49d7315887854321c67e49880e936
local               4547eb4feb4d29b2835a02b59a25b325701a353f85bc96f3da1815e6698fb417
local               997821d513b9b0da0696b34c24eb831237e1ae36f8e9519d76c09b136608707c
local               c6ad8c011d6ff45b8026065280c79b6b5e306cc38714ca083e2987b74d7d0c08
local               d13807c9e1935ec64eb65e72499cc07fc373a61516232e352d2f5575dbe7f617
local               wordpress

# 上面的每一行都是一个路径，匿名挂载 -v 只写了容器内的路径
```

```SHELL
# 具名挂载 -v 卷名:容器内路径
➜  ~ docker run -d -P --name=nginx02 -v juming-nginx:/etc/nginx nginx
48d4cb4a9f83abf581c3722b5f36d71865f32285bd90565bb62c1f1c4884eba0
➜  ~ docker volume ls
DRIVER              VOLUME NAME
local               00a443e281a4a31e87efc2f8ef5b9e83d19c3719e9b28935093a63b73ddd615a
local               2c5c6b3c246f24e25565b9dab3dd6488a2d16d1854e2e8b82b6c761f5368244b
local               2cae6ec7a2a3d45e9a4597cc58b7693f010afa21cde3c29c4c7b1a6287924c9b
local               2ee4ed9f6fc19dd1d6f3765a250ed32fe7b05401b878fe66bbae7336c9b6a376
local               5ffc1dd7fbf05d131d9e99e0b2d2a8c07dccd4ddc45dcef3755344856c0395ca
local               9c8000eea259e07e4df1a533dc907b9bf42ca16bf033bd5afd92c26e418f4bf7
local               43ce19b919cc5e7069f16da6317bf2839b7e4f1decc920d11ca32328beebb840
local               374e20ad87426efd90d3d41029573742daed3233640969a16830fb2d6d1749fd
local               1604f7de3773e6506fc6c5a22419b555d9b49d7315887854321c67e49880e936
local               4547eb4feb4d29b2835a02b59a25b325701a353f85bc96f3da1815e6698fb417
local               997821d513b9b0da0696b34c24eb831237e1ae36f8e9519d76c09b136608707c
local               c6ad8c011d6ff45b8026065280c79b6b5e306cc38714ca083e2987b74d7d0c08
local               d13807c9e1935ec64eb65e72499cc07fc373a61516232e352d2f5575dbe7f617
local               juming-nginx
local               wordpress

# 查看容器的具体挂载地点
➜  ~ docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2020-11-03T13:04:25Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
```

```shell
# 如何区分具名和匿名
-v 容器内路径   匿名挂载
-v 卷名:容器内路径 具名挂载
-v /容器外路径:/容器内路径  指定路径挂载
```

拓展

```shell
# 通过 -v 容器内路径:容器外路径:ro rw 改变读写权限
docker run -d -P --name=nginx -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name=nginx -v juming-nginx:/etc/nginx:rw nginx
ro 只能通过宿主机来操作
```

##四、容器数据卷

dockerfile是用来构建docker镜像文件

###脚本文件构建

```shell
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "====end======="

CMD /bin/bash
```

###docker build 命令构建

```shell
# -f  Name of the Dockerfile (Default is 'PATH/Dockerfile') 脚本文件地址
# -t  target string           Set the target build stage to build.  目标
docker build -f centos-file -t yangbo/centos
```

```shell
#查看构建好的镜像
docker images
➜  docker-test-volume docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
yangbo/centos         latest              772b59d94dbb        6 seconds ago       215MB
mysql                 latest              db2b37ec6181        11 days ago         545MB
kibana                7.9.3               f9f7fac59a10        2 weeks ago         1.18GB
elasticsearch         7.9.3               1ab13f928dc8        2 weeks ago         742MB
nginx                 latest              f35646e83998        3 weeks ago         133MB
centos                latest              0d120b6ccaa8        2 months ago        215MB
portainer/portainer   latest              62771b0b9b09        3 months ago        79.1MB
```

###运行容器

```shell
➜  docker-test-volume docker run -it yangbo/centos /bin/bash
[root@e728dc38a446 /]# ls -l
total 56
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  360 Nov  3 13:22 dev
drwxr-xr-x   1 root root 4096 Nov  3 13:22 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Aug  9 21:40 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 155 root root    0 Nov  3 13:22 proc
dr-xr-x---   2 root root 4096 Aug  9 21:40 root
drwxr-xr-x  11 root root 4096 Aug  9 21:40 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  12 root root    0 Nov  3 13:22 sys
drwxrwxrwt   7 root root 4096 Aug  9 21:40 tmp
drwxr-xr-x  12 root root 4096 Aug  9 21:40 usr
drwxr-xr-x  20 root root 4096 Aug  9 21:40 var
drwxr-xr-x   2 root root 4096 Nov  3 13:22 volume01 # 脚本中挂载的目录【匿名挂载】
drwxr-xr-x   2 root root 4096 Nov  3 13:22 volume02
```

###数据卷容器

运行容器1

```shell
# 启动第一个容器
docker run -it --name=docker01 yangbo/centos /bin/bash
```

```shell
# 启动第二个容器
docker run -it --name=docker02 --volumes-from docker01 yangbo/centos /bin/bash
```

```shell
# 在第一个容器volume01中创建文件，查看第二个容器中的数据是否会同步创建
[root@8c78fd9f8d46 volume01]# touch yangbo.txt
[root@8c78fd9f8d46 volume01]# ls
yangbo.txt
# 第二个容器
➜  ~ docker run -it --name=docker02 --volumes-from docker01 yangbo/centos
[root@82913c6d2643 var]# cd ../volume01
[root@82913c6d2643 volume01]# ls
[root@82913c6d2643 volume01]# ls
yangbo.txt
```

多个MySQL实现数据共享

```shell
# mysql01
docker run -d -p 3310:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
# mysql02
docker run -d -p 3311:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01 mysql:5.7
```

## 五、dockerfile

构建步骤

1. 编写脚本文件
2. docker build 构建成一个镜像
3. docker run 
4. Docker push 发布镜像

![image-20201104111333895](/Users/allin264/Library/Application Support/typora-user-images/image-20201104111333895.png)

很多官方镜像只提供最基本的功能，我们需要自己定制！

### 一、Dockerfile的构建过程

1. 每个保留关键字都是大写
2. 从上到下执行
3. "#"标识注释
4. 每个指令都会创建一层镜像

docker企业交付的标准---------开发、部署、运维

dockerfile ：构建文件，定义了步骤，源代码

DockerImages：通过dockerfile生成的镜像，最终发布的产品，从之前的war包、jar包变到现在的镜像

docker容器：容器是镜像运行起来提供服务

### 二、dockerfile指令

![Alt text](https://img2018.cnblogs.com/i-beta/631711/201912/631711-20191220153130957-348455400.png)

```shell
FROM 			# 基础镜像
MINTAINER # 镜像是谁写的，姓名+邮箱
RUN 			# docker镜像需要运行的命令
ADD				# 步骤，Tomcat镜像Tomcat压缩包就是要添加的内容
WORKDIR   # 镜像的工作目录
VOLUME		# 挂载的目录
EXPOSE		# 暴露端口配置 与-p是一样的
RUN				# 最终要运行的
CMD				# 容器启动的时候要运行的命令	只有最后一个会生效
ENTRYPOINT# 指定这个容器启动的时候运行的命令 可以追加命令
ONBUILD 	# 当构建一个被继承的dockerfile 这个时候会运行ONBUILD 。触发指令
COPY 			# 将文件拷贝到镜像中
ENV				# 构建的时候设置环境变量 ES设置JVM大小
```

###三、实战测试----构建自己centos

####一、创建一个自己的centos

```shell
➜  dockerfile cat mydockerfile-centos
FROM centos
MAINTAINER yangbo<yangbo@gmail.com>

ENV MYPATH /usr/local
WORKDIR $MYPATH
RUN yum -y install vim
RUN yum -y install net-tools
EXPOSE 80

CMD echo $MYPATH

CMD echo "----end----"

CMD /bin/bash
```

>通过文件构建镜像

```
Successfully built c5533b5222d6
Successfully tagged mycentos:0.1
```

> 测试运行

```shell
# 默认是工作目录
# 可以使用ifconfig\vim 等命令
➜  dockerfile docker run -it mycentos:0.1 /bin/bash
[root@62e0113e1827 local]# pwd
/usr/local
[root@62e0113e1827 local]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.6  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:06  txqueuelen 0  (Ethernet)
        RX packets 8  bytes 656 (656.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@62e0113e1827 local]# vim test
[root@62e0113e1827 local]#
```

####二、nginx镜像的构建过程

```shell
➜  dockerfile docker history c5533b5222d6
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
c5533b5222d6        4 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B
44870844492c        4 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
3fe3539c0cd8        4 minutes ago       /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
3ecf5563876c        4 minutes ago       /bin/sh -c #(nop)  EXPOSE 80                    0B
4aee5148c344        4 minutes ago       /bin/sh -c yum -y install net-tools             22.8MB
114fb3d4d17e        5 minutes ago       /bin/sh -c yum -y install vim                   57.3MB
d26f6bd9005f        7 minutes ago       /bin/sh -c #(nop) WORKDIR /usr/local            0B
ee000c864720        7 minutes ago       /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B
f1ec13093dff        7 minutes ago       /bin/sh -c #(nop)  MAINTAINER yangbo<yangbo@…   0B
0d120b6ccaa8        2 months ago        /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>           2 months ago        /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>           2 months ago        /bin/sh -c #(nop) ADD file:538afc0c5c964ce0d…   215MB
```

####三、MySQL的构建过程

```shell
➜  dockerfile docker history db2b37ec6181
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
db2b37ec6181        12 days ago         /bin/sh -c #(nop)  CMD ["mysqld"]               0B
<missing>           12 days ago         /bin/sh -c #(nop)  EXPOSE 3306 33060            0B
<missing>           12 days ago         /bin/sh -c #(nop)  ENTRYPOINT ["docker-entry…   0B
<missing>           12 days ago         /bin/sh -c ln -s usr/local/bin/docker-entryp…   34B
<missing>           12 days ago         /bin/sh -c #(nop) COPY file:f9202f6b715c0e78…   13.1kB
<missing>           2 weeks ago         /bin/sh -c #(nop) COPY dir:2e040acc386ebd23b…   1.12kB
<missing>           2 weeks ago         /bin/sh -c #(nop)  VOLUME [/var/lib/mysql]      0B
<missing>           2 weeks ago         /bin/sh -c {   echo mysql-community-server m…   410MB
<missing>           2 weeks ago         /bin/sh -c echo "deb http://repo.mysql.com/a…   55B
<missing>           2 weeks ago         /bin/sh -c #(nop)  ENV MYSQL_VERSION=8.0.22-…   0B
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV MYSQL_MAJOR=8.0          0B
<missing>           3 weeks ago         /bin/sh -c set -ex;  key='A4A9406876FCBD3C45…   2.61kB
<missing>           3 weeks ago         /bin/sh -c apt-get update && apt-get install…   52.2MB
<missing>           3 weeks ago         /bin/sh -c mkdir /docker-entrypoint-initdb.d    0B
<missing>           3 weeks ago         /bin/sh -c set -eux;  savedAptMark="$(apt-ma…   4.17MB
<missing>           3 weeks ago         /bin/sh -c #(nop)  ENV GOSU_VERSION=1.12        0B
<missing>           3 weeks ago         /bin/sh -c apt-get update && apt-get install…   9.34MB
<missing>           3 weeks ago         /bin/sh -c groupadd -r mysql && useradd -r -…   329kB
<missing>           3 weeks ago         /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>           3 weeks ago         /bin/sh -c #(nop) ADD file:0dc53e7886c35bc21…   69.2MB
```

####四、CMD---ENTRYPOINT测试

```shell
➜  dockerfile cat docker-cmd-test
FROM centos
CMD ["ls","-a"]
# 测试追加命令---结果报错
➜  dockerfile docker run docker-cmd-test -l
docker: Error response from daemon: OCI runtime create failed: container_linux.go:349: starting container process caused "exec: \"-l\": executable file not found in $PATH": unknown.
```

```shell
➜  dockerfile cat docker-expose-test
FROM centos
ENTRYPOINT ["ls","-a"]
# 测试追加命令
➜  dockerfile docker run -it docker-expose-test -l
total 56
drwxr-xr-x   1 root root 4096 Nov  4 04:12 .
drwxr-xr-x   1 root root 4096 Nov  4 04:12 ..
-rwxr-xr-x   1 root root    0 Nov  4 04:12 .dockerenv
lrwxrwxrwx   1 root root    7 May 11  2019 bin -> usr/bin
drwxr-xr-x   5 root root  360 Nov  4 04:12 dev
drwxr-xr-x   1 root root 4096 Nov  4 04:12 etc
drwxr-xr-x   2 root root 4096 May 11  2019 home
lrwxrwxrwx   1 root root    7 May 11  2019 lib -> usr/lib
lrwxrwxrwx   1 root root    9 May 11  2019 lib64 -> usr/lib64
drwx------   2 root root 4096 Aug  9 21:40 lost+found
drwxr-xr-x   2 root root 4096 May 11  2019 media
drwxr-xr-x   2 root root 4096 May 11  2019 mnt
drwxr-xr-x   2 root root 4096 May 11  2019 opt
dr-xr-xr-x 158 root root    0 Nov  4 04:12 proc
dr-xr-x---   2 root root 4096 Aug  9 21:40 root
drwxr-xr-x  11 root root 4096 Aug  9 21:40 run
lrwxrwxrwx   1 root root    8 May 11  2019 sbin -> usr/sbin
drwxr-xr-x   2 root root 4096 May 11  2019 srv
dr-xr-xr-x  12 root root    0 Nov  3 13:22 sys
drwxrwxrwt   7 root root 4096 Aug  9 21:40 tmp
drwxr-xr-x  12 root root 4096 Aug  9 21:40 usr
drwxr-xr-x  20 root root 4096 Aug  9 21:40 var
```

#### 五、制作Tomcat镜像

#####一、编写脚本

```shell
FROM centos
MAINTAINER yangbo<yangbogod@gmail.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u271-linux-x64.tar.gz /usr/local/

ADD apache-tomcat-9.0.39.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local

WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_271

ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.39

ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.39

ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/apache-tomcat-9.0.39/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.39/bin/logs/catalina.out

```

#####二、构建镜像

```shell
# 制作镜像
docker build -t diytomcat .

Sending build context to Docker daemon  149.1MB
Step 1/15 : FROM centos
 ---> 0d120b6ccaa8
Step 2/15 : MAINTAINER yangbo<yangbogod@gmail.com>
 ---> Running in a7ff5b67249a
Removing intermediate container a7ff5b67249a
 ---> c45c430479f8
Step 3/15 : COPY readme.txt /usr/local/readme.txt
 ---> 915735a9cddc
Step 4/15 : ADD jdk-8u271-linux-x64.tar.gz /usr/local/
 ---> c3c705b4af3e
Step 5/15 : ADD apache-tomcat-9.0.39-src.tar.gz /usr/local/
 ---> d7b8431db4b8
Step 6/15 : RUN yum -y install vim
 ---> Running in 8146d31b4fc2
CentOS-8 - AppStream                            730 kB/s | 5.8 MB     00:08
CentOS-8 - Base                                 1.3 MB/s | 2.2 MB     00:01
CentOS-8 - Extras                               3.3 kB/s | 8.1 kB     00:02
Dependencies resolved.
================================================================================
 Package             Arch        Version                   Repository      Size
================================================================================
Installing:
 vim-enhanced        x86_64      2:8.0.1763-13.el8         AppStream      1.4 M
Installing dependencies:
 gpm-libs            x86_64      1.20.7-15.el8             AppStream       39 k
 vim-common          x86_64      2:8.0.1763-13.el8         AppStream      6.3 M
 vim-filesystem      noarch      2:8.0.1763-13.el8         AppStream       48 k
 which               x86_64      2.21-12.el8               BaseOS          49 k

Transaction Summary
================================================================================
Install  5 Packages

Total download size: 7.8 M
Installed size: 31 M
Downloading Packages:
(1/5): vim-enhanced-8.0.1763-13.el8.x86_64.rpm  1.1 MB/s | 1.4 MB     00:01
(2/5): gpm-libs-1.20.7-15.el8.x86_64.rpm         29 kB/s |  39 kB     00:01
(3/5): vim-common-8.0.1763-13.el8.x86_64.rpm    4.2 MB/s | 6.3 MB     00:01
(4/5): vim-filesystem-8.0.1763-13.el8.noarch.rp  68 kB/s |  48 kB     00:00
(5/5): which-2.21-12.el8.x86_64.rpm              42 kB/s |  49 kB     00:01
--------------------------------------------------------------------------------
Total                                           2.0 MB/s | 7.8 MB     00:03
warning: /var/cache/dnf/AppStream-02e86d1c976ab532/packages/gpm-libs-1.20.7-15.el8.x86_64.rpm: Header V3 RSA/SHA256 Signature, key ID 8483c65d: NOKEY
CentOS-8 - AppStream                            1.1 MB/s | 1.6 kB     00:00
Importing GPG key 0x8483C65D:
 Userid     : "CentOS (CentOS Official Signing Key) <security@centos.org>"
 Fingerprint: 99DB 70FA E1D7 CE22 7FB6 4882 05B5 55B3 8483 C65D
 From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-centosofficial
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                        1/1
  Installing       : which-2.21-12.el8.x86_64                               1/5
  Installing       : vim-filesystem-2:8.0.1763-13.el8.noarch                2/5
  Installing       : vim-common-2:8.0.1763-13.el8.x86_64                    3/5
  Installing       : gpm-libs-1.20.7-15.el8.x86_64                          4/5
  Running scriptlet: gpm-libs-1.20.7-15.el8.x86_64                          4/5
  Installing       : vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/5
  Running scriptlet: vim-enhanced-2:8.0.1763-13.el8.x86_64                  5/5
  Running scriptlet: vim-common-2:8.0.1763-13.el8.x86_64                    5/5
  Verifying        : gpm-libs-1.20.7-15.el8.x86_64                          1/5
  Verifying        : vim-common-2:8.0.1763-13.el8.x86_64                    2/5
  Verifying        : vim-enhanced-2:8.0.1763-13.el8.x86_64                  3/5
  Verifying        : vim-filesystem-2:8.0.1763-13.el8.noarch                4/5
  Verifying        : which-2.21-12.el8.x86_64                               5/5

Installed:
  gpm-libs-1.20.7-15.el8.x86_64         vim-common-2:8.0.1763-13.el8.x86_64
  vim-enhanced-2:8.0.1763-13.el8.x86_64 vim-filesystem-2:8.0.1763-13.el8.noarch
  which-2.21-12.el8.x86_64

Complete!
Removing intermediate container 8146d31b4fc2
 ---> afc59a01253b
Step 7/15 : ENV MYPATH /usr/local
 ---> Running in 052e655d222d
Removing intermediate container 052e655d222d
 ---> 931ea5a59529
Step 8/15 : WORKDIR $MYPATH
 ---> Running in a6a0fd42e925
Removing intermediate container a6a0fd42e925
 ---> c84d3577d755
Step 9/15 : ENV JAVA_HOME /usr/local/jdk1.8.0_271
 ---> Running in 26f2999dc5fa
Removing intermediate container 26f2999dc5fa
 ---> ac059874519b
Step 10/15 : ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
 ---> Running in 5b73421990e4
Removing intermediate container 5b73421990e4
 ---> b3e7b8cf1539
Step 11/15 : ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.39-src
 ---> Running in f69c349c386a
Removing intermediate container f69c349c386a
 ---> c04c48aaf1b7
Step 12/15 : ENV CATALINA_BASE /usr/local/apache-tomcat-9.0.39-src
 ---> Running in 5424a60fd234
Removing intermediate container 5424a60fd234
 ---> 0269c2c61713
Step 13/15 : ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin
 ---> Running in bf8223144155
Removing intermediate container bf8223144155
 ---> f25c2b57d233
Step 14/15 : EXPOSE 8080
 ---> Running in 3edef9e8f723
Removing intermediate container 3edef9e8f723
 ---> e564448058a9
Step 15/15 : CMD /usr/local/apache-tomcat-9.0.39-src/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.39-src/bin/logs/catalina.out
 ---> Running in 3f145cffcc17
Removing intermediate container 3f145cffcc17
 ---> 1e4c4bc41d7b
Successfully built 1e4c4bc41d7b
Successfully tagged diytomcat:latest
```

##### 三、启动容器

```shell
docker run -d -p 9090:8080 --name yangbotomcat -v /Users/allin264/docker/tomcat/test:/usr/local/apache-tomcat-9.0.39/webapps/test -v /Users/allin264/docker/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.39/logs diytomcat
```

##### 四、发布项目

```xml
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
         http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
         version="3.1">
    
</web-app>
```

```html
<html>
<head><title>Hello World</title></head>
<body>
Hello World!<br/>
<%
out.println("Your IP address is " + request.getRemoteAddr());
%>
</body>
</html>
```

##### 五、访问

Localhost:9090/test

![image-20201104133935788](/Users/allin264/Library/Application Support/typora-user-images/image-20201104133935788.png)

### 四、发布镜像到阿里云

![image-20201104134737512](/Users/allin264/Library/Application Support/typora-user-images/image-20201104134737512.png)



## 六、docker搭建集群

### 一、redis集群

#### 创建启动配置

```shell
for port in $(seq 1 6);\
do \
mkdir -p /Users/allin264/docker/mydata/redis/node-${port}/conf
touch /Users/allin264/docker/mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/Users/allin264/docker/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file node.conf
cluster-node-timeout 5000
cluster-announce-ip 192.168.0.1${port}
cluster-announce-port 6379
cluster-announce-port 16379
appendonly yes
EOF
done
```

#### 启动

```
for num in $(seq 1 6);\
do \
docker run -p 637${num}:6379 -p 1637${num}:16379 --name redis-${num} -v /Users/allin264/docker/mydata/redis/node-${num}/data:/data -v /Users/allin264/docker/mydata/redis/node-${num}/conf/redis.conf:/etc/redis/redis.conf -d --net redis --ip 192.168.0.1${num} redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
done
```

#### 构建集群

```shell
redis-cli --cluster create 192.168.0.11:6379 192.168.0.12:6379 192.168.0.13:6379 192.168.0.14:6379 192.168.0.15:6379 192.168.0.16:6379 --cluster-replicas 1
```

