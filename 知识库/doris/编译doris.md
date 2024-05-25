# 编译doris
> 源码编译须在docker环境下进行
## 使用现成的docker镜像
### 下载docker镜像
```ssh
docker pull apache/doris:build-env-ldb-toolchain-latest
```
### 运行docker镜像
>建议以挂载本地 Doris 源码目录的方式运行镜像，这样编译的产出二进制文件会存储在宿主机中，不会因为镜像退出而消失。
>
>同时，建议同时将镜像中 maven 的 .m2 目录挂载到宿主机目录，以防止每次启动镜像编译时，重复下载 maven 的依赖库。
```ssh
docker run -it -v /your/local/.m2:/root/.m2 -v /your/local/doris-DORIS-x.x.x-release/:/root/doris-DORIS-x.x.x-release/ apache/doris:build-env-ldb-toolchain-latest
docker run -it -v /home/doris_docker/repo:/root/repo -v /home/doris_docker/.m2:/root/.m2 -v /home/doris_docker/doris-DORIS-x.x.x-release/:/root/doris-DORIS-x.x.x-release/ apache/doris:build-env-ldb-toolchain-latest
docker run -it -v /home/doris_docker:/root apache/doris:build-env-ldb-toolchain-latest
```
### 进入docker镜像内部
```ssh
docker exec -it <imageId> /bin/bash
```
### 下载doris源码
> 进入docker容器内部后，只能在/root下执行该操作
```ssh
git clone https://github.com/apache/doris.git
```
### 编译doris
>进入doris文件夹后执行以下操作
#### 查看是否支持avx2
```ssh
cat /proc/cpuinfo | grep avx2
```
#### 编译前的确认工作
##### 是否需要使用with_mysql ，若需要则编译语句为
```ssh
WITH_MYSQL=1 WITH_LZO=1 sh build.sh
```
>with_mysql 是直接建立mysql连接使用,以下为例：
```mysql
CREATE TABLE jcy_szjc_bqsxzcf_dp(
  `xh` varchar(255) DEFAULT NULL,
  `xm` varchar(255) DEFAULT NULL,
  `ab` varchar(255) DEFAULT NULL,
  `xb` varchar(255) DEFAULT NULL,
  `aqzy` varchar(255) DEFAULT NULL,
  `zcbqssj` varchar(255) DEFAULT NULL,
  `cfqk` varchar(255) DEFAULT NULL
)
ENGINE=mysql
PROPERTIES
(
 "host" = "10.1.3.142",
 "port" = "3306",
 "user" = "root",
 "password" = "123456",
 "database" = "jcys",
 "table" = "jcy_szjc_bqsxzcf_dp"
);
```
#### 注意:
1.   docker镜像中使用的resp仓库地址/root/.m2不能使用
```ssh
# 修改maven配置地址
vim /usr/share/maven/conf/settings.xml
# 修改
<localReposit>
```
2. docker映射到外部的地址不能使用
#### 不支持时使用以下命令进行编译
```ssh
USE_AVX2=0  sh build.sh
```
#### 支持可用的的编译命令
```ssh
sh build.sh
```
#### 产出文件位置*output/*

>注意:
>
>如果你是第一次使用 build-env-for-0.15.0 或之后的版本，第一次编译的时候要使用如下命令：
>
>sh build.sh --clean --be --fe
>
>这是因为 build-env-for-0.15.0 版本镜像升级了 thrift(0.9 -> 0.13)，需要通过 --clean 命令强制使用新版本的 thrift 生成代码文件，否则会出现不兼容的代码。

编译完成后，产出文件在 output/ 目录中。

## 特别声明
自 0.13 版本开始，默认的编译产出中将取消对 [1] 和 [2] 两个第三方库的依赖。这两个第三方库为 GNU General Public License V3 协议。该协议与 Apache License 2.0 协议不兼容，因此默认不出现在 Apache 发布版本中。

移除依赖库 [1] 会导致无法访问 MySQL 外部表。访问 MySQL 外部表的功能会在后续版本中通过 UnixODBC 实现。

移除依赖库 [2] 会导致在无法读取部分早期版本（0.8版本之前）写入的部分数据。因为早期版本中的数据是使用 LZO 算法压缩的，在之后的版本中，已经更改为 LZ4 压缩算法。后续我们会提供工具用于检测和转换这部分数据。

如果有需求，用户可以继续使用这两个依赖库。如需使用，需要在编译时添加如下选项：
```ssh
WITH_MYSQL=1 WITH_LZO=1 sh build.sh
```
注意，当用户依赖这两个第三方库时，则默认不在 Apache License 2.0 协议框架下使用 Doris。请注意 GPL 相关协议约束。

[1] mysql-5.7.18
[2] lzo-2.10