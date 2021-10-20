# vm.max_map_count（Win10环境）

参考文章：[vm.max_map_count [65530] is too low 问题解决（WSL 2、Docker Desktop）](https://segmentfault.com/a/1190000038912881)

---

#### 环境说明

Win10 + WSL2 + DockerDesktop

#### 问题描述

运行ElasticSearch容器时抛出错误信息：“[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]”

#### 解决方案

主要问题就是 Linux Distro  Virtual Memory Areas 默认的 vm.max_map_count 值是65530，太小不足以支撑ES的运行，需要将该值增大到至少262144。

##### 方案一

进入到你所使用的WSL 2 distro中（本人使用的是Ubuntu 20）

1.  以管理员身份进入

    ```crystal
    ~$ sudo -i
    ```

2.   编辑sysctl.conf，增加参数

     ```crystal
     ~$ vim /etc/sysctl.conf (需要是root账户)
     
     ~$ vm.max_map_count=262144 (文件最后添加一行)
     ```

3.   重启

     ```crystal
     ~$ sysctl -p (重启生效)
     ```

     >   该方法有一个问题，一旦系统重启或者Docker Desktop重启，就需要重新手动执行以上命令，比较麻烦。

##### 方案二

1.  打开Window 10 的CMD

2.  执行以下命令：

    ```shell
    wsl -d docker-desktop
    echo 262144 >> /proc/sys/vm/max_map_count
    ```

    >   通过这个方法，即使操作系统重启，参数仍然有效。