---
title: Docker & Dockerfile
author: Krirou Zeng
date: 2018/10/16
---

# Docker & Dockerfile

## Docker

### 是什么

Docker 是一个开源的应用容器引擎，基于 Go 语言 并遵从Apache2.0协议开源。

Docker 可以让开发者打包他们的应用以及依赖包到一个轻量级、可移植的容器中，然后发布到任何流行的 Linux 机器上，也可以实现虚拟化。

容器是完全使用沙箱机制，相互之间不会有任何接口（类似 iPhone 的 app）,更重要的是容器性能开销极低。

### 命令大全

1. `docker info`:显示 Docker 系统信息，包括镜像和容器数

  ```sh
     docker info
  ```

2. `docker version`:显示 Docker 版本信息

  ```sh
     docker version
  ```

3. `docker images`:列出本地镜像

  ```sh
     docker images [OPTIONS] [REPOSITORY[:TAG]]
  ```

  OPTIONS说明：

    -a:列出本地所有的镜像(含中间映像层，默认情况下，过滤掉中间映像层)

    --digests:显示镜像的摘要信息

    -f:显示满足条件的镜像

    --format:指定返回值的模板文件

    --no-trunc:显示完整的镜像信息

    -q:只显示镜像ID

4. `docker rmi`:删除本地一个或多个镜像

  ```sh
     docker rmi [OPTIONS] IMAGE [IMAGE...]
  ```

  OPTIONS说明：

    -f:强制删除

    --no-prune:不移除该镜像的过程镜像，默认移除

5. `docker tag`:标记本地镜像，将其归入某一仓库

  ```sh
     docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
  ```

6. `docker build`:使用Dockerfile创建镜像

  ```sh
     docker build [OPTIONS] PATH | URL | -
  ```
   
  OPTIONS说明：

    -f:指定要使用的Dockerfile路径

    -t:指定了镜像名

    --rm:设置镜像成功后删除中间容器

    ...

7. `docker history`:查看指定镜像的创建历史

  ```sh
     docker history [OPTIONS] IMAGE
  ```

8. `docker save`:将指定镜像保存成 tar 归档文件

  ```sh
     docker save [OPTIONS] IMAGE [IMAGE...]
  ```

  OPTIONS说明：

    -o:输出到的文件

9. `docker import`:从归档文件中创建镜像

  ```sh
     docker import [OPTIONS] file|URL|- [REPOSITORY[:TAG]]
  ```

  OPTIONS说明：

    -c:应用docker指令创建镜像

    -m:提交时的说明文字

10. `docker pull`:从镜像仓库中拉取或者更新指定镜像

  ```sh
     docker pull [OPTIONS] NAME[:TAG|@DIGEST]
  ```

  OPTIONS说明：

     -a:拉取所有 tagged 镜像

     --disable-content-trust:忽略镜像的校验,默认开启

11. `docker push`:将本地的镜像上传到镜像仓库，要先登录到镜像仓库

  ```sh
     docker push [OPTIONS] NAME[:TAG]
  ```
  
  OPTIONS说明：

     --disable-content-trust:忽略镜像的校验,默认开启

12. `docker login/logout`:登录/登出一个Docker镜像仓库，如果未指定镜像仓库，默认为官方仓库Docker Hub

  ```sh
     docker login [OPTIONS] [SERVER]

     docker logout [OPTIONS] [SERVER]
  ```

  OPTIONS说明：

    -u:登录的用户名

    -p:登录的密码

13. `docker search`:从Docker Hub查找镜像

  ```sh
     docker search [OPTIONS] TERM
  ```

  OPTIONS说明：

    --no-trunc :显示完整的镜像描述

    ...


14. `docker commit`:从容器创建一个新的镜像

  ```sh
     docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
  ```

  OPTIONS说明：

    -a:提交的镜像作者

    -c:使用Dockerfile指令来创建镜像

    -m:提交时的说明文字

    -p:在commit时，将容器暂停

15. `docker cp`:用于容器与主机之间的数据拷贝

  ```sh
     docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-

     docker cp [OPTIONS] SRC_PATH|- CONTAINER:DEST_PATH
  ```

16. `docker diff`:检查容器里文件结构的更改

  ```sh
     docker diff [OPTIONS] CONTAINER
  ```

17. `docker ps`:列出容器

  ```sh
     docker ps [OPTIONS]
  ```

  OPTIONS说明：

    -a:显示所有的容器，包括未运行的

    -f:根据条件过滤显示的内容

    -s:显示总的文件大小

    -q:静默模式，只显示容器编号

    ...

18. `docker inspect`:获取容器/镜像的元数据

  ```sh
     docker inspect [OPTIONS] NAME|ID [NAME|ID...]
  ```

19. `docker top`:查看容器中运行的进程信息

  ```sh
     docker top [OPTIONS] CONTAINER [ps OPTIONS]
  ```

20. `docker events`:从服务器获取实时事件

  ```sh
     docker events [OPTIONS]
  ```

  OPTIONS说明：

    -f:根据条件过滤事件

    --since:从指定的时间戳后显示所有事情

    --until:流水时间显示到指定时间为止

21. `docker logs`:获取容器的日志

  ```sh
     docker logs [OPTIONS] CONTAINER
  ```

  OPTIONS说明：

    -f:跟踪日志输出

    --since:显示某个开始时间的所有日志

    -t:显示时间戳

    ...

22. `docker wait`:阻塞运行直到容器停止，然后打印出它的退出代码

  ```sh
     docker wait [OPTIONS] CONTAINER [CONTAINER...]
  ```

23. `docker export`:将文件系统作为一个tar归档文件导出到STDOUT

  ```sh
     docker export [OPTIONS] CONTAINER
  ```

  OPTIONS说明：

    -o:将输入内容写到文件

24. `docker port`:列出指定的容器的端口映射，或者查找将PRIVATE_PORT NAT到面向公众的端口

  ```sh
     docker port [OPTIONS] CONTAINER [PRIVATE_PORT[/PROTO]]
  ```

25. `docker run`:创建一个新的容器并运行一个命令

  ```sh
     docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
  ```

  OPTIONS说明：

    -d:后台运行容器，并返回容器ID

    -i:以交互模式运行容器，通常与 -t 同时使用

    -t:为容器重新分配一个伪输入终端，通常与 -i 同时使用

    --name:为容器指定一个名称

    -p:指定端口映射

    -P:映射到主机随机端口

    ...

26. `docker start/stop/restart`:启动/停止/重启容器

  ```sh
     docker start/stop/restart [OPTIONS] CONTAINER [CONTAINER...]
  ```    

27. `docker kill`:杀掉一个运行中的容器

  ```sh
     docker kill [OPTIONS] CONTAINER [CONTAINER...]
  ``` 

28. `docker run`:删除容器

  ```sh
     docker rm [OPTIONS] CONTAINER [CONTAINER...]
  ``` 

  OPTIONS说明：

    -f:强制删除一个运行中的容器

    -v:删除与容器关联的卷

29. `docker pause/unpause`:暂停/容器中所有的进程。

  ```sh
     docker pause/unpause [OPTIONS] CONTAINER [CONTAINER...]
  ``` 

30. `docker create`:创建一个新的容器但不启动它

  ```sh
     docker create [OPTIONS] IMAGE [COMMAND] [ARG...]
  ``` 

  命令同docker run

31. `docker exec`:在运行的容器中执行命令


  ```sh
     docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
  ``` 

  OPTIONS说明：

    -d:分离模式，在后台运行

    -i:即使没有附加也保持STDIN 打开

    -t:分配一个伪终端


## Dockerfile

### 是什么

Dockerfile是一个文本文件，其内包含了一条条的指令，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。

通过Dockerfile自动构建Docker镜像

### 语法说明

1. FROM:指定基础镜像

   定制镜像的时候都是以一个镜像为基础，在这个基础上面进行定制。

   `FROM`在`Dockerfile`中是必须的指令，而且必须是第一条指令。

2. RUN:执行命令

   告诉docker在镜像内执行什么命令，安装什么。

   有两种格式：
   - shell格式
     ```sh
        RUN 命令
     ```
     就像直接在命令行中输入的命令一样，如：
     ```sh
        RUN echo 'hello' > hello.txt
     ```
   - exec格式
     ```sh
        RUN ['可执行文件','参数1','参数2']
     ```
     类似于函数调用，将可执行文件和参数分开，如：
     ```sh
        RUN ['sh','-c','echo $HOME']
     ```

3. CMD:容器启动命令

   有两种格式：
   - shell格式
     ```sh
        CMD 命令
     ```
   - exec格式
     ```sh
        CMD ['可执行文件','参数1','参数2'...]
     ```
    
   在指定了`ENTRYPOINT`指令后，用CMD指定具体的参数。参数列表格式:`CMD ['参数1','参数2'...]`

4. COPY:复制文件
   
   两种格式：
   - 类似于命令行
     ```sh
        COPY <源路径>...<目标路径>
     ```
   - 类似于函数调用
     ```sh
        COPY [<源路径>,...<目标路径>]
     ```
   COPY指令从构建上下文目录中<源路径>的文件复制到新的一层的镜像内的<目标路径>位置

5. ENV:设置环境变量
   两种格式：
   - `ENV <key> <value>`
   - `ENV <key1>=<value1> <key2>=<value2>...`

6. EXPOSE:声明端口

   声明运行时容器提供服务端口，这只是一个声明，在运行时并不会因为这个声明应用就会开启这个端口的服务。

   ```sh
      EXPOSE <端口1>[<端口>]
   ```
   
7. WORKDIR:指定工作目录

   ```sh
      WORKDIR <工作目录路径>
   ```

   指定工作目录（或者称为当前目录），以后各层的当前目录就被改为指定的目录。

8. ENTRYPOINT:配置容器启动后执行的命令，并且不可被docker run提供的参数覆盖。而CMD是可以被覆盖的。如果需要覆盖，则可以使用docker run --entrypoint选项。

   ```sh
      ENTRYPOINT["executable","param1","param2"]
      ENTRYPOINT command param1 param2 
   ```

9. VOLUME: 将本地主机目录挂在到目标容器中

   ```sh
      VALUME ["/data"]
   ```