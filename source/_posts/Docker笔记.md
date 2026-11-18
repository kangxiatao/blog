---
title: Docker笔记
date: 2023-04-10 22:28:16
updated: 2023-04-10 22:28:16
categories: 码农笔记
tags:
  - Docker
  - Gazebo
  - PX4
toc: true
---

## 千万不要用docker

嘿嘿，docker真香，🤤🤤

<!--more-->


## docker note

### 镜像和容器操作

- 移除镜像

    ```docker rmi 85e62d9e0586```

- 打包容器

    ```docker commit 7b2d0ec9a289 deepidea:kxt```

- 保存镜像

    ```docker save > synergy.tar deepidea:synergy```


### 集群

部分指令

```
# 创建swarm
docker swarm init --advertise-addr=192.168.1.30
# 获取新的token标记文本
docker swarm join-token manager
# 查看节点列表
docker node ls 
# 离开集群
docker swarm leave --force
```

示例
```
# 创建swarm
docker swarm init --advertise-addr=192.168.1.30
# 超过24小时需要重新获取token
docker swarm join-token manager
# 用输出的token加入到swarm
pass
# 为容器建立集群网络
docker network create --driver=overlay --attachable my-overlay-net
# docker run时选到集群网络中即可
pass
```

### 其他

- 访问权限

    ```xhost local:root```  ```xhost +```

- 新建容器命令行

    ```docker exec -it melodic_world bash```

- 获取容器长ID

    ```docker inspect -f '{{.ID}}' px4_test```

- 复制文件到docker中

    ```docker cp 文件路径 容器长ID:容器文件路径```


### dockerfile

dockerfile 构建环境，塔门说PX4子模块有问题，我没试

```
首先进入robot/PX4_Firmware
git clone http://192.168.1.10:3000/robot/PX4_Firmware.git
克隆后进入
git checkout xtdrone/dev-v1.13.2
文件就显示完毕了
点击.gitmodules文件
修改[submodule "Tools/sitl_gazebo"]的地址

[submodule "Tools/sitl_gazebo"]
path = Tools/sitl_gazebo
url = http://192.168.1.10:3000/robot/PX4-SITL_gazebo.git
branch = master

保存退出后
git submodule update --init --recursive

之后把PX4_Firmware和GeographicLib放入Dockerfile同级目录中
运行docker build -t name .指令，其中name为你自己的docker名字

在run_melodic_px4_world.sh文件中
修改--volume="/home/xxx/catkin_ws/src:/root/catkin_ws/src" \，xxx为你自己的用户名

使用docker network create --driver=bridge --subnet=10.8.0.2/16 locros创建docker网络

对于无法启动gazebo这一问题，用xhost local:root解锁权限，再./run_melodic_px4_world.sh测试
```

### docker gpu

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
```

## px4 gazebo note

- test

    ```
    # 启动仿真
    roslaunch synergy multi_vehicle_v1.launch
    
    # 无人机控制
    docker exec -it px4_test bash
    cd ~/catkin_ws/src/synergy/scripts/
    python3 control_mul.py 

    # 无人车控制
    docker exec -it px4_test bash
    cd ~/catkin_ws/src/synergy/scripts/self_driving/
    python2 ugv_hector_driving.py 

    # 图传和轨迹
    docker exec -it px4_test bash
    rviz rviz
    ```

- 换源

    ```-i https://pypi.tuna.tsinghua.edu.cn/simple```

- PX4 1.13版本修改

    ```https://blog.csdn.net/weixin_44537885/article/details/125946076```

- gazebo问题

    ```export LIBGL_ALWAYS_INDIRECT=1```
    ```sudo apt install libnvidia-gl-525```

## linux note

- 权限

    ```chmod 777 install.sh```

- 当前路径

    ```pwd```

- 环境变量问题

    全局 ```./etc/profile```  (```source /etc/profile```)

    用户 ```~/.bashrc ```

- AppImage问题

    Ubuntu 22.04 缺少 FUSE（用户空间中的文件系统）库
    ```sudo apt install libfuse2```
