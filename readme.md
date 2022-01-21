# dockerExample

## 参考链接

* [给前端的docker 10分钟真 · 快速入门指南给前端的docker 10分钟真 · 快速入门指南](https://juejin.cn/post/7050304120082661407)
* [docker入门：vue和可视化界面的部署，另附ngxin配置](https://juejin.cn/post/6999062566202769439)
* [关于Docker的若干问题（已解决）](https://blog.csdn.net/m0_43438893/article/details/110260427)
* [Docker 教程](https://www.runoob.com/docker/docker-tutorial.html)
* [Docker英文文档](https://docs.docker.com/get-started/)

## 目录

* [docker简介](#docker简介)
* [docker安装](#docker安装)
* [docker应用](#docker应用)

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

* window10举例

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

## docker应用

* 部署vue2和vue3项目

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