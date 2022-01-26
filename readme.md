# dockerExample

## 参考链接

* [给前端的docker 10分钟真 · 快速入门指南给前端的docker 10分钟真 · 快速入门指南](https://juejin.cn/post/7050304120082661407)
* [docker入门：vue和可视化界面的部署，另附ngxin配置](https://juejin.cn/post/6999062566202769439)
* [关于Docker的若干问题（已解决）](https://blog.csdn.net/m0_43438893/article/details/110260427)
* [Docker 教程](https://www.runoob.com/docker/docker-tutorial.html)
* [Docker英文文档](https://docs.docker.com/get-started/)
* [Docker Hub 仓库](https://hub.docker.com/)
* [Docker教程前端有这篇就够了!!!](https://juejin.cn/post/6962848738645639175)
* [Docker+Nginx+Jenkins实现前端自动化部署](https://juejin.cn/post/6869736425953165319)

## 目录

* [docker简介](#docker简介)
* [docker安装](#docker安装)
    * [window10](#window10)
* [docker命令](#docker命令)
    * [镜像](#镜像)
    * [容器](#容器)
    * [连接](#连接)
    * [仓库](#仓库)
    * [Dockerfile定制镜像](#Dockerfile定制镜像)
    * [Compose](#Compose)
* [docker应用](#docker应用)
    * [部署vue2和vue3项目](#部署vue2和vue3项目)

---

## docker简介

* 概念

    * 镜像(Images)

        用于创建 Docker 容器的模板，比如 Ubuntu 、node、nginx、mysql

    * 容器(Container)

        容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等

    * 仓库(Repository)

        仓库可看成一个代码控制中心，用来保存镜像

    * 客户端(Client)

        通过命令行或者其他工具使用 Docker SDK 与 Docker 的守护进程通信

    * 主机(Host)

        一个物理或者虚拟的机器用于执行 Docker 守护进程和容器

* 场景

    从单操作系统推广到不同操作系统，在不同环境运行同一项目，抹平不同系统的应用程序安装差异，达到跨平台的效果。例如给新人、新服务器安装项目。

    * 为什么不用虚拟机？

        因为虚拟机启动慢、损耗大、占用资源多，在不同系统上的迁移/拓展比较复杂。

        docker启动是秒级的，占用资源少，香不香，跨平台还方便复制，写好配置后，它一个命令就在电脑上同时安装不同版本的nodej、msyql、nginx 等而且是互相隔离独立同时运行，这时候还不需要你手动来回切换。

## docker安装

### window10

* 图形化界面程序:[Docker Desktop](https://hub.docker.com/editions/community/docker-ce-desktop-windows)
* 控制面板开启windows程序:
    * Hyper-V全选，或管理员控制台输入命令:Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
    * 部分电脑需开启WSL2，docker如有报错，根据报错链接开启功能
* 在开机的BIOS中，需开启CPU虚拟化，在任务管理器-性能中，确认CPU虚拟化已开启
* 报错处理
    * Hardware assisted virtualization and data execution protection must be enabled in the BIOS 报错
        * 解决方案A（如果Hyper-V被完全禁用或未安装）：
            1. 以管理员身份打开PowerShell，然后
            2. 启用Hyper-V
            3. dism.exe /Online /Enable-Feature:Microsoft-Hyper-V /All
        * 解决方案B（如果Hyper-V功能已启用但不起作用）：
            1. 通过以下方式启用虚拟机监控程序
            2. bcdedit /set hypervisorlaunchtype auto
            3. 现在重新启动系统，然后重试。
        * 解决方案C：
            1. 如果问题仍然存在，则可能是系统上的Hyper-V已损坏，因此
            2. 进入控制面板-> 程序-> Windows功能，然后完全取消选中所有与Hyper-V相关的组件。重新启动系统。
            3. 再次启用Hyper-V。重新开始。
    * 更多:[关于Docker的若干问题（已解决）](https://blog.csdn.net/m0_43438893/article/details/110260427)
* 安装确认:docker -v

## docker命令

### 镜像

* 列出镜像列表:docker images
* 获取一个新的镜像:docker pull 镜像名[:tag(版本)]
* 查找镜像:docker search 镜像名
* 运行镜像:docker run -t -i 镜像名:版本 镜像里要执行的命令
    * -i: 交互式操作。
    * -t: 终端。
    * -d: 不进入容器。
    * -P :是容器内部端口随机映射到主机的端口。
    * -p : 是容器内部端口绑定到指定的主机端口。
    * --name : 标识容器名
* 删除镜像:docker rmi 镜像名
* 创建容器副本:docker commit -m="提交的描述信息" -a="指定镜像作者" 容器 ID 指定要创建的目标镜像名:tag(版本)
* 设置镜像标签:docker tag 镜像ID 新镜像名:新标签名

### 容器

* 查看所有容器:docker ps -a
* 启动一个已停止的容器:docker start 容器ID
* 停止一个容器:docker stop 容器ID
* 重启容器:docker restart 容器ID
* 进入容器:docker attach 容器ID
* 退出并停止容器:exit
* 在运行的容器中执行命令(从容器退出，不会导致容器停止):docker exec -it 容器ID 命令
* 导出容器:docker export 容器ID > 导出名称(如ubuntu.tar)
* 导入容器:docker import - test/ubuntu:v1 或 docker import http://example.com/exampleimage.tgz example/imagerepo
* 删除容器:docker rm -f 容器ID
* 查看指定（ID 或者名字）容器的某个确定端口映射到宿主机的端口号:docker port 容器ID或者名字
* 查看 WEB 应用程序日志:docker logs -f 容器ID
* 查看WEB应用程序容器的进程:docker top 容器ID
* 查看 Docker 的底层信息:docker inspect 容器ID

### 连接

* 新建网络:docker network create -d bridge 网络名 (-d：参数指定 Docker 网络类型，有 bridge、overlay(用于 Swarm mode)。)
* 运行一个容器并连接到新建的 test-net 网络:docker run -itd --name 名称 --network 网络名 容器 命令
* 配置 DNS

    宿主机的 /etc/docker/daemon.json 文件中增加以下内容来设置全部容器的 DNS，需要重启 docker 才能生效。
    ```json
    {
        "dns" : [
            "114.114.114.114",
            "8.8.8.8"
        ]
    }
    ```

    * 输出容器的 DNS 信息:docker run -it --rm  容器  cat etc/resolv.conf
    * 手动指定容器的配置:docker run -it --rm -h 容器的主机名  --dns=114.114.114.114 --dns-search=容器的搜索域 容器
        * --rm：容器退出时自动清理容器内部的文件系统。
        * -h HOSTNAME 或者 --hostname=HOSTNAME： 设定容器的主机名，它会被写到容器内的 /etc/hostname 和 /etc/hosts。
        * --dns=IP_ADDRESS： 添加 DNS 服务器到容器的 /etc/resolv.conf 中，让容器用这个服务器来解析所有不在 /etc/hosts 中的主机名。
        * --dns-search=DOMAIN： 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 host.example.com。

### 仓库

* 登录:docker login
* 退出:docker logout
* 推送镜像

    ```txt
    $ docker tag ubuntu:18.04 用户名/ubuntu:18.04
    $ docker image ls

    REPOSITORY      TAG        IMAGE ID            CREATED           ...  
    ubuntu          18.04      275d79972a86        6 days ago        ...  
    username/ubuntu 18.04      275d79972a86        6 days ago        ...  
    $ docker push 用户名/ubuntu:18.04
    $ docker search 用户名/ubuntu

    NAME             DESCRIPTION       STARS         OFFICIAL    AUTOMATED
    username/ubuntu
    ```

### Dockerfile定制镜像

用于创建新镜像

Dockerfile 文件
```shell
# 每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写
FROM    centos:6.7 # 指定使用哪个镜像源
MAINTAINER      Fisher "fisher@sudops.com" 

RUN     /bin/echo 'root:123456' |chpasswd # 执行命令,在 docker build 时运行
RUN     useradd runoob
RUN     /bin/echo 'runoob:123456' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
# COPY复制指令:COPY hom* /mydir/
# ADD复制指令:自动复制并解压(不推荐)
# CMD运行程序:类似于 RUN 指令,在docker run 时运行,如果存在多个 CMD 指令，仅最后一个生效。CMD ["/etc/nginx/nginx.conf"] # 变参 
# ENTRYPOINT:类似于 CMD 指令，执行 docker run 的时候可以指定 ENTRYPOINT 运行所需的参数,如果存在多个 ENTRYPOINT 指令，仅最后一个生效。ENTRYPOINT ["nginx", "-c"] # 定参
# ENV设置环境变量:ENV NODE_VERSION 7.2.0
# ARG构建参数:ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效。ARG <参数名>[=<默认值>]
# VOLUME定义匿名数据卷:避免重要的数据，因容器重启而丢失，这是非常致命的。避免容器不断变大。VOLUME ["<路径1>", "<路径2>"...]。VOLUME <路径>
# EXPOSE声明端口:EXPOSE <端口1> [<端口2>...]
# WORKDIR指定工作目录:WORKDIR <工作目录路径>
# USER指定执行后续命令的用户和用户组:USER <用户名>[:<用户组>]
# HEALTHCHECK指定某个程序或者指令来监控 docker 容器服务的运行状态:
    # HEALTHCHECK [选项] CMD <命令>：设置检查容器健康状况的命令
    # HEALTHCHECK NONE：如果基础镜像有健康检查指令，使用这行可以屏蔽掉其健康检查指令
    # HEALTHCHECK [选项] CMD <命令> : 这边 CMD 后面跟随的命令使用，可以参考 CMD 的用法。
# ONBUILD延迟构建命令的执行:Dockerfile 里用 ONBUILD 指定的命令，在本次构建镜像的过程中不会执行，当有新的 Dockerfile 使用了之前构建的镜像 FROM ***，才执行ONBUILD。ONBUILD <其它指令>
# LABEL给镜像添加键值对元数据:LABEL <key>=<value> <key>=<value> <key>=<value>
```

docker build -t runoob/centos:6.7 .

* -t :指定要创建的目标镜像名
* . :Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径

### Compose

Compose 是用于定义和运行多容器 Docker 应用程序的工具。通过 Compose，您可以使用 YML 文件来配置应用程序需要的所有服务。然后，使用一个命令，就可以从 YML 文件配置中创建并启动所有服务。

* Compose 使用的三个步骤：
    1. 使用 Dockerfile 定义应用程序的环境。
    2. 使用 docker-compose.yml 定义构成应用程序的服务，这样它们可以在隔离环境中一起运行。
    3. 最后，执行 docker-compose up 命令来启动并运行整个应用程序，想在后台执行该服务可以加上 -d 参数。
    4. docker-compose stop 停止服务

* Compose 安装

    Linux需下载安装，Mac和windows在桌面版docker已包含compose

* docker-compose.yml编写

    ```yml
    # yaml 配置
    version: '3' # 指定本 yml 依从的 compose 哪个版本制定
    services: # 定义了两个服务：web 和 redis
    web:
        build: . # 为构建镜像上下文路径，从 Dockerfile 当前目录中构建的镜像
        ports:
        - "5000:5000" # 将容器和主机绑定到暴露的端口 5000
    redis:
        image: "redis:alpine" # 使用 Docker Hub 的公共 Redis 映像
    ```

    ```yml
    version: "3.7"
    services:
    webapp:
        build:
        context: ./dir # 上下文路径
        dockerfile: Dockerfile-alternate # 指定构建镜像的 Dockerfile 文件名
        args: # 添加构建参数，这是只能在构建过程中访问的环境变量
            buildno: 1
        labels: # 设置构建镜像的标签
            - "com.example.description=Accounting webapp"
            - "com.example.department=Finance"
            - "com.example.label-with-empty-value"
        target: prod # 多层构建，可以指定构建哪一层
    ```

    ```yml
    version: "3.7"
    services:
    web: # web 服务不会等待 redis db 完全启动 之后才启动。
        build: .
        depends_on: # 设置依赖关系,以依赖性顺序启动/停止服务
        - db
        - redis
    redis:
        image: redis
    db:
        image: postgres
    ```

    ```yml
    version: "3.7"
    services:
    redis:
        image: redis:alpine
        deploy: # 指定与服务的部署和运行有关的配置。只在 swarm 模式下才会有用
        mode: replicated # 指定服务提供的模式
        # replicated：复制服务，复制指定服务到集群的机器上。
        # global：全局服务，服务将部署至集群的每个节点。
        replicas: 6 # replicas：mode 为 replicated 时，需要使用此参数配置具体运行的节点数量。
        endpoint_mode: dnsrr # endpoint_mode：访问集群服务的方式。
        # endpoint_mode: vip 
        # Docker 集群服务一个对外的虚拟 ip。所有的请求都会通过这个虚拟 ip 到达集群服务内部的机器。
        # endpoint_mode: dnsrr
        # DNS 轮询（DNSRR）。所有的请求会自动轮询获取到集群 ip 列表中的一个 ip 地址。
        labels: # 在服务上设置标签
            description: "This redis service label"
        resources: # 配置服务器资源使用的限制
            limits:
                cpus: '0.50'
                memory: 50M
            reservations:
                cpus: '0.25'
                memory: 20M
        restart_policy: # 配置如何在退出容器时重新启动容器
            condition: on-failure # 可选 none，on-failure 或者 any（默认值：any）
            delay: 5s # 设置多久之后重启（默认值：0）
            max_attempts: 3 # 尝试重新启动容器的次数，超出次数，则不再尝试（默认值：一直重试）
            window: 120s # 设置容器重启超时时间（默认值：0）
        # rollback_config：配置在更新失败的情况下应如何回滚服务。
        #     parallelism：一次要回滚的容器数。如果设置为0，则所有容器将同时回滚。
        #     delay：每个容器组回滚之间等待的时间（默认为0s）。
        #     failure_action：如果回滚失败，该怎么办。其中一个 continue 或者 pause（默认pause）。
        #     monitor：每个容器更新后，持续观察是否失败了的时间 (ns|us|ms|s|m|h)（默认为0s）。
        #     max_failure_ratio：在回滚期间可以容忍的故障率（默认为0）。
        #     order：回滚期间的操作顺序。其中一个 stop-first（串行回滚），或者 start-first（并行回滚）（默认 stop-first ）。
        #     update_config：配置应如何更新服务，对于配置滚动更新很有用。
        # parallelism：一次更新的容器数。
        #     delay：在更新一组容器之间等待的时间。
        #     failure_action：如果更新失败，该怎么办。其中一个 continue，rollback 或者pause （默认：pause）。
        #     monitor：每个容器更新后，持续观察是否失败了的时间 (ns|us|ms|s|m|h)（默认为0s）。
        #     max_failure_ratio：在更新过程中可以容忍的故障率。
        #     order：回滚期间的操作顺序。其中一个 stop-first（串行回滚），或者 start-first（并行回滚）（默认stop-first）。
        #     注：仅支持 V3.4 及更高版本。
    ```

    ```yml
    cap_add:
    - ALL # 开启全部权限

    cap_drop:
    - SYS_PTRACE # 关闭 ptrace权限

    cgroup_parent: m-executor-abcd # 为容器指定父 cgroup 组，意味着将继承该组的资源限制

    command: ["bundle", "exec", "thin", "-p", "3000"] # 覆盖容器启动的默认命令

    container_name: my-web-container # 指定自定义容器名称，而不是生成的默认名称

    devices: # 指定设备映射列表
        - "/dev/ttyUSB0:/dev/ttyUSB0"

    dns: 8.8.8.8 # 自定义 DNS 服务器，可以是单个值或列表的多个值。

    dns:
        - 8.8.8.8
        - 9.9.9.9

    dns_search: example.com # 自定义 DNS 搜索域。可以是单个值或列表

    dns_search:
        - dc1.example.com
        - dc2.example.com

    entrypoint: /code/entrypoint.sh # 覆盖容器默认的 entrypoint

    entrypoint:
        - php
        - -d
        - zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20100525/xdebug.so
        - -d
        - memory_limit=-1
        - vendor/bin/phpunit

    env_file: .env # 从文件添加环境变量。可以是单个值或列表的多个值。

    env_file:
        - ./common.env
        - ./apps/web.env
        - /opt/secrets.env

    environment: # 添加环境变量。您可以使用数组或字典、任何布尔值，布尔值需要用引号引起来，以确保 YML 解析器不会将其转换为 True 或 False。
        RACK_ENV: development
        SHOW: 'true'

    expose: # 暴露端口，但不映射到宿主机，只被连接的服务访问
        - "3000"
        - "8000"

    extra_hosts: # 添加主机名映射
        - "somehost:162.242.195.82"
        - "otherhost:50.31.209.229"

    healthcheck: # 用于检测 docker 服务是否健康运行
        test: ["CMD", "curl", "-f", "http://localhost"] # 设置检测程序
        interval: 1m30s # 设置检测间隔
        timeout: 10s # 设置检测超时时间
        retries: 3 # 设置重试次数
        start_period: 40s # 启动后，多少秒开始启动检测程序

    image: redis # 指定容器运行的镜像
    image: ubuntu:14.04
    image: tutum/influxdb
    image: example-registry.com:4000/postgresql
    image: a4bc65fd # 镜像id

    driver: "json-file" # 服务的日志记录配置。指定服务容器的日志记录驱动程序，默认值为json-file
    driver: "syslog"
    driver: "none"

    logging:
        driver: json-file # 仅在 json-file 驱动程序下，可以使用以下参数，限制日志得数量和大小。
        options: # 当达到文件限制上限，会自动删除旧得文件。
            max-size: "200k" # 单个文件大小为200k
            max-file: "10" # 最多10个文件

    logging:
        driver: syslog
        options: # syslog 驱动程序下，可以使用 syslog-address 指定日志接收地址
            syslog-address: "\\192.168.0.42:123"

    network_mode: "bridge" # 设置网络模式
    network_mode: "host"
    network_mode: "none"
    network_mode: "service:[service name]"
    network_mode: "container:[container name/id]"

    # 注：swarm 集群模式，请改用 restart_policy。
    restart: "no" # 默认的重启策略，在任何情况下都不会重启容器。
    restart: always # 容器总是重新启动。
    restart: on-failure # 在容器非正常退出时（退出状态非0），才会重启容器。
    restart: unless-stopped # 在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器

    # 指定在容器无法处理 SIGTERM (或者任何 stop_signal 的信号)，等待多久后发送 SIGKILL 信号关闭容器。
    stop_grace_period: 1s # 等待 1 秒
    stop_grace_period: 1m30s # 等待 1 分 30 秒 

    stop_signal: SIGUSR1 # 设置停止容器的替代信号。默认情况下使用 SIGTERM 。

    sysctls: # 设置容器中的内核参数
        net.core.somaxconn: 1024
        net.ipv4.tcp_syncookies: 0

    sysctls:
        - net.core.somaxconn=1024
        - net.ipv4.tcp_syncookies=0

    tmpfs: /run # 在容器内安装一个临时文件系统

    tmpfs:
        - /run
        - /tmp

    ulimits: # 覆盖容器默认的 ulimit
    nproc: 65535
    nofile:
        soft: 20000
        hard: 40000
    ```

    ```yml
    services:
        some-service:
            networks:
                some-network:
                    aliases: # 同一网络上的其他容器可以使用服务名称或此别名来连接到对应容器的服务
                        - alias1
                other-network:
                    aliases:
                        - alias2
    networks:
        some-network:
            # Use a custom driver
            driver: custom-driver-1
        other-network:
            # Use a custom driver which takes special options
            driver: custom-driver-2
    ```

    ```yml
    version: "3.1"
    services:

    mysql:
        image: mysql
        environment:
            MYSQL_ROOT_PASSWORD_FILE: /run/secrets/my_secret
        secrets:
            - my_secret

    secrets:
        my_secret:
            file: ./my_secret.txt
        security-opt:
            - label:user:USER   # 设置容器的用户标签
            - label:role:ROLE   # 设置容器的角色标签
            - label:type:TYPE   # 设置容器的安全策略标签
            - label:level:LEVEL  # 设置容器的安全等级标签
    ```

    ```yml
    version: "3.7"
    services:
        db:
            image: postgres:latest
            volumes: # 将主机的数据卷或着文件挂载到容器里
                - "/localhost/postgres.sock:/var/run/postgres/postgres.sock"
                - "/localhost/data:/var/lib/postgresql/data"
    ```

## docker应用

### 部署vue2和vue3项目

* vue2+webpack+nodejs15.0.1

    ```shell
    npm install -g @vue/cli

    vue --version
    # @vue/cli 4.5.15

    vue create my-app-vue2
    # Default ([Vue 2] babel, eslint) 
    ```

    先运行成功看到首页

* vue3+vite+nodejs16.11.1

    ```shell
    npm init vite@latest
    ```

    vite.config.js
    ```js
    //vite需要开启网络访问
    //vite.config.js 开启host
    export default defineConfig({
    plugins: [vue()],
    +  server: {
    +    host: '0.0.0.0',
    +  },
    });
    ```

    先运行成功看到首页

* 目录

    ```txt
    {路径}
    ├── my-app-vue2
    │   ├── public
    │   └── src
    │       ├── assets
    │       └── components
    └── my-app-vue3
        ├── public
        └── src
            ├── assets
            └── components
    ```

* 运行docker容器

    ```shell
    cd {路径}

    pwd
    # 查看绝对路径 {路径}

    docker run -it -d --name myvue2 --privileged -p 8081:8080 -v  {路径}/my-app-vue2:/app/vue node:15.0.1 /bin/bash -c "cd /app/vue && node -v && npm install && npm run serve"

    docker run -it -d --name myvue3 --privileged -p 8080:3000 -v {路径}/my-app-vue3:/app/vue node:16.11.1 /bin/bash -c "cd /app/vue && node -v && npm install && npm run dev"

    docker ps -a
    #运行成功后 查看容器运行情况
    #CONTAINER ID   IMAGE          COMMAND                  CREATED             STATUS             PORTS                    NAMES
    #0cedbbd50f73   node:16.11.1   "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:8080->3000/tcp   myvue3
    #2ce28d955d37   node:15.0.1    "docker-entrypoint.s…"   About an hour ago   Up About an hour   0.0.0.0:8081->8080/tcp   myvue2

    docker logs -f myvue2
    #调试myvue2项目，看具体报错

    docker inspect myvue2
    #打印出容器的端口映射、目录挂载、网络等等
    ```

* 参数解析

    ```shell
    docker run \
    -it \ # -i和 -t的缩写,-i：告诉 Docker 容器保持标准输入流对容器开放,即使容器没有终端连接
    -d \ # 以守护进程的方式让容器在后台运行，类似pm2
    --name myvue2 \ # 将容器命名为 myvue2，这样访问和操作容 器等就不需要输入一大串的容器ID
    --privileged \ # 让容器的用户在容器内能获取完全root权限
    -p 8081:8080 \ # 将容器的8080端口映射到宿主机的8081端口上,访问本机的localhost:8081,就是访问到容器的8080端口
    -v /Users/eric/my-repository/my-app-vue2:/app/vue \ # 将主机的my-app-vue2目录下的内容挂载到容器的目录/app/vue内,如果容器的指定目录有文件/文件夹，将被清空
    node:15.0.1 \ # 指定nodejs版本
    /bin/bash -c "cd /app/vue2 && node -v && npm install && npm run serve" # 需要执行的shell命令
    ```