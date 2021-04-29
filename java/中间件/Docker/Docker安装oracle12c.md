# Docker 安装 Ubuntu

### 1、安装Docker-Desktop 

> windows10 以上建议安装Docker-Desktop,而不是Docker Toolbox

Docker是一个非常有用的开发工具，用来创建容器化的应用。Docker Desktop为我们学习Docker提供了一套完整的一个桌面环境，Docker Desktop包含了Docker Engine, Docker CLI client, Docker Compose, Docker Machine和Kitematic。官网地址：https://hub.docker.com/

Docker-Desktop 提供收费，免费两个版本，收费比免费增强了安全功能，个人开发仅用免费版即可

Docker-Desktop-windows 有两种底层依赖模式，Hyper-V和WSL2，需要对应的开启相关的windows功能，wsl是windows下的liunx子系统，依然是依赖于hyper-v的， 建议使用WSL2，安装完成后可能会提示WSL2过时的问题，此时只需要根据提示下载对应更新安装即可

### 2、使用阿里云容器服务进行镜像加速

进入阿里云搜索**容器镜像服务**，并按提示开通，配置镜像仓库，命名空间，配置完成后，在左侧菜单栏中找到镜像中心>镜像加速器 并按操作文档进行配置。入口如下https://help.aliyun.com/product/60716.html

### 3、拉取镜像

oracle 官方镜像地址：https://hub.docker.com/u/hermite/content/sub-f1d885a1-a202-4d09-9101-6ebc4a71d1f5

拉取命令：

```shell
docker pull store/oracle/database-enterprise:12.2.0.1
```

镜像启动：

```
docker run -d -it --name {containerName} store/oracle/database-enterprise:12.2.0.1
```

SQL*PLUS 连接

```
sqlplus sys/Oradoc_db1@ORCLCDB as sysdba
```

赋予oracle用户文件夹权限用于创建表空间

```shell
#查看oracle的用户与用户组
id oracle
#切换到root
su root
#或者直接以root进入容器
docker exec -ti -u root {containerName} /bin/bash
#此命令可修改root密码：docker exec -itu root {containerName} passwd
#创建文件夹
mkdir {dir}
#赋权
 chown -Rf oracle:oinstall {fulldir}           
 chmod -Rf 777 {fulldir}  
```

### 4、建表空间及用户

> 由于Oracle12c的特性在CDB中新建用户需要以C##开头，当配置用户空间时需要CDB和PDB中都建立表空间

链接到sqlplus，建表空间

```shell

#建立CDB表空间
CREATE TABLESPACE "ekp15" DATAFILE 
  '/ekpdb/ekp15_CDB' SIZE 104857600
  AUTOEXTEND ON NEXT 104857600 MAXSIZE 32767M
  LOGGING ONLINE PERMANENT BLOCKSIZE 8192
  EXTENT MANAGEMENT LOCAL AUTOALLOCATE DEFAULT 
 NOCOMPRESS  SEGMENT SPACE MANAGEMENT AUTO
 
#查询pdb有哪些
select pdb_name,dbid,status from dba_pdbs;
#开启PDB数据库连接
alter session set container={pdb_name};
#启动PDB
startup
#建立CDB表空间，表空间名字相同，文件路径不同
CREATE TABLESPACE "ekp15" DATAFILE 
  '/ekpdb/ekp15_PDB' SIZE 104857600
  AUTOEXTEND ON NEXT 104857600 MAXSIZE 32767M
  LOGGING ONLINE PERMANENT BLOCKSIZE 8192
  EXTENT MANAGEMENT LOCAL AUTOALLOCATE DEFAULT 
 NOCOMPRESS  SEGMENT SPACE MANAGEMENT AUTO
```

按如下文章进行用户配置 https://www.cnblogs.com/Archer-Fang/p/9204265.html

