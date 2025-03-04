# IBM WebSphere Application Server 7.0

Building an IBM WebSphere Application Server 7.0 for Developers image.

IBM's official [Docker build](https://github.com/WASdev/ci.docker.websphere-traditional) only supports WebSphere Application Server 8.5.5+. However, sometimes you can't really get rid off something for a while.

## Built image

我已经打好了镜像，可通过如下途径直接获取使用：
```sh
docker pull allen88/ibm-was:7.0.0
```

## Prerequisites

You have to download WebSphere Application Server v7.0 installation package and updates from IBM as following.

下载地址参考：https://www.ibm.com/support/pages/security-bulletin-ibm-intelligent-operations-center-15-websphere-application-server-oracle-java-cpu-april-2013
其中描述了相关补丁的FTP下载地址。更改相关版本即可。

### Download installation files into `source` folder

1. Download `was.7000.wasdev.nocharge.linux.amd64.tar.gz` form [IBM WebSphere Application for Developers](https://www-01.ibm.com/marketing/iwm/iwm/web/dispatcher.do?source=swg-wsasfd) page (link from this [StackOverflow answer](https://stackoverflow.com/a/17523649/3440376)), and place into `source` folder.

**已存储至百度网盘，通过百度网盘下载。**

2. Download IBM Update Installer for WebSphere Software for Linux form [IBM web site](http://www-01.ibm.com/support/docview.wss?rs=180&uid=swg24020212) (IBMid required), or direct download from [IBM's FTP](ftp://public.dhe.ibm.com/software/websphere/appserv/support/tools/UpdateInstaller/7.0.x/LinuxAMD64/). Place the download file `7.0.0.*-WS-UPDI-LinuxAMD64.tar.gz` into `source` folder.

此处下载：ftp://public.dhe.ibm.com/software/websphere/appserv/support/tools/UpdateInstaller/7.0.x/LinuxAMD64/7.0.0.21-WS-UPDI-LinuxAMD64.tar.gz
如需下载其他版本，例如，29版本，则直接将`tar.gz`文件名称中的21改为29即可。

最好通过`wget`下载，通过迅雷下载时，发现解压缩报错。

### Download updates into `udpate` folder

Choose desired fix pick version and download from [IBM web site](http://www-01.ibm.com/support/docview.wss?rs=180&uid=swg27014463) (IBMid required), or link to [IBM's FTP](ftp://public.dhe.ibm.com/software/websphere/appserv/support/fixpacks/was70/cumulative/) navigate to `cf700*/LinuxX64/` and download WAS and WASSDK fix pack.

Take Fix Pack 21 for example, the direct download location is:

- 此处下载：ftp://public.dhe.ibm.com/software/websphere/appserv/support/fixpacks/was70/cumulative/cf70021/LinuxX64/7.0.0-WS-WAS-LinuxX64-FP0000021.pak
- 此处下载：ftp://public.dhe.ibm.com/software/websphere/appserv/support/fixpacks/was70/cumulative/cf70021/LinuxX64/7.0.0-WS-WASSDK-LinuxX64-FP0000021.pak
- WASSDK下载的备用地址：http://delivery04.dhe.ibm.com/sar/CMA/WSA/03tha/0/7.0.0-WS-WASSDK-LinuxX64-FP0000021.pak

如需下载其他版本，例如，29版本，则直接将`tar.gz`文件名称中的21改为29即可。

可通过迅雷下载，速度快。

### Resulting directory structure

If you choose to build image for Fix Pack 21, make sure your diretory structure looks like:

```text
├── source
│   ├── 7.0.0.21-WS-UPDI-LinuxAMD64.tar.gz
│   └── was.7000.wasdev.nocharge.linux.amd64.tar.gz
└── update
    ├── 7.0.0-WS-WAS-LinuxX64-FP0000021.pak
    └── 7.0.0-WS-WASSDK-LinuxX64-FP0000021.pak
```

## Build image

After manually download all required files and updates, run following command to build image:

```bash
docker image build -t websphere:7 .
```

A WebSphere 7.0 installation with `AppSrv01` profile will then be created and exposing 9060 port for admin console, and 9090 port for web application server HTTP access.

## Run container

Run following command to start container:

```bash
docker container run -p 8880:8880 -p 9060:9060 -p 9080:9080 --name was7 websphere:7
```

Navigate to <http://localhost:9060/ibm/console/> for the admin console, enter any username and you can then confugre your application server or install applications.

### Accessing WebSphere CLIs

If you need do some configuration using `wsadmin` or some other WebSphere's CLIs, execute following command while container is running:

```bash
docker container exec -it -e COLUMNS=$COLUMNS -e LINES=$LINES -e TERM=$TERM was7 bash
```

Or run as `root` user:

```bash
docker container exec -it -u 0 -e COLUMNS=$COLUMNS -e LINES=$LINES -e TERM=$TERM was7 bash
```

In the container's shell interpretor, you can access WebSphere Application Server's admin scripts from any directoy, like:

```bash
wsadmin.sh -lang jython -javaoption "-Dfile.encoding=UTF-8" -f my_admin_script.py
```
