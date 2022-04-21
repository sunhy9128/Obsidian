# 					 Abis-match 镜像构建文档
​	Atlas500不仅支持docker原生命令进行镜像的导入和容器的部署，同时也支持使用altas端管理软件以及Fusion管理软件进行镜像部署和容器管理。altas端管理软件支持的最大镜像为1G，Fusion管理软件支持的最大镜像为4G。
- ## Abis-match镜像的构建
  
  1. Atlas500需要在其提供的基础镜像构建的操作环境上进行第三方服务的构建。华为提供的最新的基础镜像为：
  
  ```bash
  	Atlas500_EulerOS2.8.0.500_64bit_aarch64_basic.tar
  	md5:b2f4ef9c8b53708eb026dcf3c184d1e0
  ```
  
  ​	进入存放基础镜像Atlas500_EulerOS2.8.0.500_64bit_aarch64_basic.tar的目录，使用命令导入Atlas基础镜像
  
  ```bash
   docker load <Atlas500_EulerOS2.8.0.500_64bit_aarch64_basic.tar
  ```
  
  ![](/Users/sunhuayu/tem_download/截屏2020-06-23 14.04.43.png)
  
  出现类似上图的输出则表示基础镜像导入完成;
  
  2. 根据Dockerfile和abis-match制作docker镜像
  
  制作abis-match镜像需要使用提供的Dockerfile文件;
  
  Dockerfile
  
  ```dockerfile
  # VERSION 1.0
  # Author: qango
  #基础镜像使用的是华为服务官方镜像公用的
  [[FROM]] euler
  FROM atlas500_euleros2.8.0.500_64bit_aarch64_basic #这里指的是上文导入的基础镜像名，若无镜像升级请勿更改
  #作者
  MAINTAINER qango <liqiang1@eyecool.cn>
  [[这里的文件夹为abis-match]]安装包名称          [[这里的路径为docker]]容器内的安装包放置路径，若无镜像升级请勿更改
  ADD abis-match-service-provider-1.0.6.2  /opt/abis-match-service-provider-1.0.6.2
  [[jdk]]文件目录，请下载解压包，并进行相应的文件名修改
  ADD jdk1.8.0_212 /opt/jdk1.8.0_212
  RUN chown -R HwHiAiUser.HwHiAiUser /opt/jdk1.8.0_212 && \
    chown -R HwHiAiUser.HwHiAiUser /opt/abis-match-service-provider-1.0.6.2
  #容器对外暴露的服务接口，一般情况下不要更改
  EXPOSE 29991
  ENV LD_LIBRARY_PATH  /opt/abis-match-service-provider-1.0.6.2:/lib64:/home/miniD/lib64
  ENV JAVA_HOME /opt/jdk1.8.0_212
  ENV PATH $JAVA_HOME/bin:/opt/abis-match-service-provider-1.0.6.2:/lib64:$PATH
  ENV CLASSPATH .:JAVA_HOME/lib/rt.jar:JAVA_HOME/lib/rt.jar:JAVA_HOME/lib/rt.jar:JAVA_HOME/lib/ext:/opt/abis-match-service-provider-1.0.6.2:/lib64:/home/miniD/lib64
  STOPSIGNAL SIGTERM
  [[若之前docker]]容器内的安装包路径修改，则此处也要进行相应的修改
  WORKDIR  /opt/abis-match-service-provider-1.0.6.2
  ENTRYPOINT  ["bin/abis-match-service-provider"]
  ```
  
  > 注意，请提前将申请好的ssnow.lic授权文件放入abis-match-service-provider-1.0.6.2路径下
  >
  > 注意，Dockerfile文件需要跟abis-match-service-provider-1.0.6.2和jdk1.8.0_212文件处在统一路径下
  
  使用原生命令进行镜像构建(需要在Dockerfile文件所在路径下执行)
  
  ```bash
  docker build -t abis-match-atlas:20200623 . 
  [[这里的abis-match-atlas]]:20200623为构建出的镜像名和版本信息，可以修改，但注意之后启动镜像也要进行相应的修改
  ```
  
  出现类似输出表示镜像构建成功，使用
  
  ```bash
  docker images
  ```
  
  可以看到镜像信息
  
  ![](/Users/sunhuayu/tem_download/截屏2020-06-23 14.41.39.png)
  
  使用原生docker指令启动abis-match容器
  
  ```bash
  docker run \
  -p 29991:29991 \ [[这里是端口号相关设置，第一个为你的主机要提供服务的端口号，后一个为Dockerfile]]设置的端口号
  -d \
  --name abis-match-service-provider \
  -v /etc/localtime:/etc/localtime \
  --device=/dev/davinci_manager \
  --device=/dev/hisi_hdc \
  --device=/dev/davinci0 \
  abis-match-service-provider:1.0.3.A3-atlas500 #这里的镜像名需要与上文构建镜像时的镜像名版本号对应，用:连接
  ```
  
  出现类似输出表示容器启动成功
  
  ![](/Users/sunhuayu/tem_download/截屏2020-06-23 15.08.14.png)
  
  3. 验证
  
  使用原生命令查看容器信息
  
  ```sh
  docker ps
  CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS              PORTS                      NAMES
  82080fc6b8a9        abis-match-atlas:20200623   "bin/abis-match-serv…"   2 minutes ago       Up 2 minutes        0.0.0.0:29991->29991/tcp   abis-match-service-provider
  Euler:/opt/data/eyecool/docker-compile/abis-match/abis-match-service-provider-1.0.6.2 #
  ```
  
  82080fc6b8a9为这次启动的容器ID，不是固定的，使用命令查看容器内部日志
  
  ```bash
  docker exec -it 82080fc6b8a9 bash
  tail -f runtime/log/abis.log
  ```
  
  出现如下日志为启动成功
  
  ![](/Users/sunhuayu/tem_download/截屏2020-06-23 15.11.58.png)