---
title: ROS与Gazebo联合仿真平台搭建
date: 2023-03-14 13:30:00
categories: 码农笔记
tags:
  - ROS
  - Gazebo
  - PX4
  - 仿真
toc: true
---

# 软硬件规格

1、装有Ubuntu 18.04的电脑（最好不要用虚拟机）

2、多机仿真对cpu要求很高，最好有显卡

3、CMake请使用最新版本！！！！

<!--more-->

# 一、依赖安装

1、先安装python运行环境

2、安装以下依赖包
```bash
sudo apt install ninja-build exiftool ninja-build protobuf-compiler libeigen3-dev genromfs xmlstarlet libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev python3-pip gawk
```
```bash
pip install pandas jinja2 pyserial cerberus pyulog==0.7.0 numpy toml pyquaternion empy pyyaml 
pip3 install packaging numpy empy toml pyyaml jinja2 pyargparse
```

- 如果出现如下报错情况，可先更新 setuptools 和 pip
```bash
Collecting pandas
  Using cached https://files.pythonhosted.org/packages/64/f1/8fdbd74edfc31625d597717be8c155c6226fc72a7c954c52583ab81a8614/pandas-1.1.2.tar.gz
    Complete output from command python setup.py egg_info:
    Traceback (most recent call last):
      File "<string>", line 1, in <module>
      File "/tmp/pip-build-qtvsjq8t/pandas/setup.py", line 349
        f"{extension}-source file '{sourcefile}' not found.\n"
                                                             ^
    SyntaxError: invalid syntax
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-qtvsjq8t/pandas/
```
```bash
#若未报错不需要输入这两行命令
pip install --upgrade setuptools
python -m pip install --upgrade pip
```

- 特别提醒，在整个环境配置中，不要轻易使用 apt-get autoremove，详见[慎用apt-get autoremove](https://blog.csdn.net/dc_show/article/details/41643915)

- 建议退出conda环境再编译


# 二、ROS安装

需注意

Ubuntu 16.04对应Kinetic，18.04对应Melodic，20.04对应Noetic。

安装步骤可见[ROS官网](http://wiki.ros.org/Installation/Ubuntu)

如果您的网络环境不佳，可以用[fishros](http://fishros.com/#/fish_home)工具一键安装。

## 方法一：

[fishros](http://fishros.com/#/fish_home)工具一键安装

使用wget命令进行ros安装(此方法会默认配置好环境，如果需要换源，请再次执行该指令):
```bash
wget http://fishros.com/install -O fishros && . fishros 
```
根据工具完成ros的安装后，再次使用上述wget命令来配置rosdep。rosdep是ros一个命令行工具，用于安装系统依赖，具体地说，就是ros包的依赖。

安装rosinstall:

```bash
sudo apt install python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool build-essential
```

初始化rosdep:
```bash
rosdepc update
```

安装完成，测试
```bash
roscore
```

如果出现如下输出,则说明安装成功。
```bash
sliver@zyc:~$ roscore
... logging to /home/sliver/.ros/log/8df53f9e-b89c-11ed-a703-38ca84472d52/roslaunch-zyc-13243.log
Checking log directory for disk usage. This may take a while.
Press Ctrl-C to interrupt
Done checking log file disk usage. Usage is <1GB.

started roslaunch server http://zyc:43825/
ros_comm version 1.14.13


SUMMARY
========

PARAMETERS
 * /rosdistro: melodic
 * /rosversion: 1.14.13

NODES

auto-starting new master
process[master]: started with pid [13272]
ROS_MASTER_URI=http://zyc:11311/

setting /run_id to 8df53f9e-b89c-11ed-a703-38ca84472d52
process[rosout-1]: started with pid [13302]
started core service [/rosout]
```

如果之前没有catkin_ws，则需要新建工作空间，之后除去PX4仿真环境启动外，其余ROS相关工程在此工作空间下管理。

```bash
mkdir -p ~/catkin_ws/src
mkdir -p ~/catkin_ws/scripts
cd catkin_ws && catkin init # 使用catkin_make话，则为cd catkin_ws/src && catkin_init_workspace
catkin build # 使用catkin_make话，则为 cd .. && catkin_make 
```

catkin build需要先装catkin-tools（sudo apt install python3-catkin-tools），其与catkin_make的区别见[Migrating from catkin_make](https://catkin-tools.readthedocs.io/en/latest/migration.html)，建议使用catkin build，因为它分别编译各个功能包，而catkin_make则是同时编译整个工作空间，当工作空间中功能包过多时，容易出现问题。

## 方法二：

安装见ROS官方网站：[http://wiki.ros.org/Installation/Ubuntu](http://wiki.ros.org/Installation/Ubuntu)
或者参考教程:[https://blog.csdn.net/qq_44830040/article/details/106049992](https://blog.csdn.net/qq_44830040/article/details/106049992)（这个教程是用的国内源安装，虽然可以使用ros，但是可能在和其他的一起使用时出现问题例如darknet_ros，虽然国内源下载会快很多，但还是建议从官网下载）

注意：Ubuntu18.04可以直接全部安装（即指令 sudo apt-get install ros-melodic-desktop 即对于ROS Melodic，选择desktop-full的方式安装,这将同时安装Gazebo9和感知相关库）但是我个人建议18.04的也不进行全部安装，直接安装的gazebo9.0存在缺陷可能在后面出现问题，建议也是选择安装desktop，然后在下面的步骤中安装gazebo9.13或者以上。Ubuntu16.04（ROS Kinetic）选择destop否则安装gazebo7无法进行后续仿真。


# 三、Gazebo和XTDrone安装

## 首先卸载之前的Gazebo
```bash
sudo apt-get remove gazebo* 
sudo apt-get remove libgazebo*
sudo apt-get remove ros-melodic-gazebo* #kinetic noetic对应修改
```
## Gazebo安装见[Gazebo官网](http://gazebosim.org/tutorials?tut=install_ubuntu&cat=install),需注意以下三点。

1、选用Alternative installation: step-by-step的安装方式，安装最新的gazebo9，不要安装gazebo11

2、如果安装有依赖问题，可以使用sudo aptitude install gazebo9，选择合理的依赖解决办法（别把ROS删了）

3、按步骤装完Gazebo后，升级所有的包 sudo apt upgrade，这样能保证gazebo所有依赖版本一致

4、安装步骤如下

```bash
sudo sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
cat /etc/apt/sources.list.d/gazebo-stable.list
#如果出现deb http://packages.osrfoundation.org/gazebo/ubuntu-stable xenial main表示没问题
# 设置键
wget https://packages.osrfoundation.org/gazebo.key -O - | sudo apt-key add -
# 这一步如果报错(见问题汇总,首先是否配置hosts,其次系统是否为ubuntu18.04)
sudo apt-get update
# 根据官方文档安装9最新版本
sudo apt-get install gazebo9=9.1*
# 升级包
sudo apt upgrade
# 测试
roscore  
rosrun gazebo_ros gazebo 
#若能打开Gazebo说明Gazebo和ROS间的插件也安装成功。
#输入gazebo --version也可以查看到gazebo的版本
```

## XTDrone安装

[XTDrone](https://gitee.com/robin_shaun/XTDrone)是基于PX4、ROS与Gazebo的无人机通用仿真平台。支持多旋翼飞行器（包含四轴和六轴）、固定翼飞行器、复合翼飞行器（包含quadplane，tailsitter和tiltrotor）与其他无人系统（如无人车、无人船与机械臂）。

XTDrone对Gazebo的ROS插件做了修改，因此需要源码编译。

首先装依赖
```bash
sudo apt-get install ros-melodic-moveit-msgs ros-melodic-object-recognition-msgs ros-melodic-octomap-msgs ros-melodic-camera-info-manager  ros-melodic-control-toolbox ros-melodic-polled-camera ros-melodic-controller-manager ros-melodic-transmission-interface ros-melodic-joint-limits-interface
```
然后编译（如果编译时还缺其他的依赖，同上方法安装），由于需要用到XTDrone的文件，需要先完成[XTDrone源码下载](https://gitee.com/robin_shaun/XTDrone)。
```bash
cd ~
git clone https://gitee.com/robin_shaun/XTDrone.git
cd XTDrone
git submodule update --init --recursive
```
```bash
cd ~/catkin_ws
cp -r ~/XTDrone/sitl_config/gazebo_ros_pkgs src/
catkin build #开发者测试使用catkin_make会出问题，因此建议使用catkin build
```
编译成功后执行如下两条指令，判断gazebo_ros是否安装成功
```bash
roscore 
```
```bash
source ~/catkin_ws/devel/setup.bash
rosrun gazebo_ros gazebo
```

如果出现:```ResourceNotFound: gazebo_ros```，安装gazebo_ros就好了：
```
sudo apt install ros-noetic-gazebo-ros-pkgs
```

下载[models.zip](https://deepideaer.yuque.com/attachments/yuque/0/2022/zip/34365726/1668767475649-59e805b5-59a9-4b86-824d-7f75603172d0.zip?_lake_card=%7B%22src%22%3A%22https%3A%2F%2Fdeepideaer.yuque.com%2Fattachments%2Fyuque%2F0%2F2022%2Fzip%2F34365726%2F1668767475649-59e805b5-59a9-4b86-824d-7f75603172d0.zip%22%2C%22name%22%3A%22models.zip%22%2C%22size%22%3A663612379%2C%22ext%22%3A%22zip%22%2C%22source%22%3A%22%22%2C%22status%22%3A%22done%22%2C%22download%22%3Atrue%2C%22type%22%3A%22application%2Fzip%22%2C%22mode%22%3A%22title%22%2C%22taskId%22%3A%22u7930f987-4c2d-4510-9f21-b7ade897f8a%22%2C%22taskType%22%3A%22transfer%22%2C%22id%22%3A%22bTigP%22%2C%22card%22%3A%22file%22%7D)
将该附件解压缩后放在`~/.gazebo`中，此时在`~/.gazebo/models/`路径下可以看到很多模型。如果不做这一步，之后运行Gazebo仿真，可能会缺模型，这时会自动下载，Gazebo模型服务器在国外，自动下载会比较久。

# 四、MAVROS安装

mavros是PX4官方提供的一个运行于ros下收发mavlink消息的工具

注意，mavros-extras一定别忘记装，否则视觉定位将无法完成
```bash
sudo apt install ros-kinetic-mavros ros-kinetic-mavros-extras 		# for ros-kinetic
sudo apt install ros-melodic-mavros ros-melodic-mavros-extras 		# for ros-melodic
wget https://gitee.com/robin_shaun/XTDrone/raw/master/sitl_config/mavros/install_geographiclib_datasets.sh

sudo chmod a+x ./install_geographiclib_datasets.sh
sudo ./install_geographiclib_datasets.sh #这步需要装一段时间
```
最后的sudo ./install_geographiclib_datasets.sh 这步需要装一段时间,请耐心等待

# 五、PX4配置（1.11）

由于国内的github下载速度较慢，XTrone的作者们把固件和submodule同步到了gitee，如果嫌github慢，可以从gitee下载。

github下载
```bash
git clone https://github.com/PX4/PX4-Autopilot.git
mv PX4-Autopilot PX4_Firmware
cd PX4_Firmware
git checkout -b xtdrone/dev v1.11.0-beta1
git submodule update --init --recursive
make px4_sitl_default gazebo
```

gitee下载
```bash
git clone https://gitee.com/robin_shaun/PX4_Firmware
cd PX4_Firmware
git checkout -b xtdrone/dev v1.11.0-beta1
```

后面步骤参考[Ubuntu18.04下基于ROS和PX4的无人机仿真平台的基础配置搭建](https://blog.csdn.net/qq_45067735/article/details/107303796?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522167688681016800182111233%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=167688681016800182111233&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-107303796-null-null.142)

有条件的建议科学上网下载。

# 六、安装地面站QGroundControl
点此[安装链接](https://docs.qgroundcontrol.com/en/getting_started/download_and_install.html)。启动后,将出现下图 所示画面。
安装之前输入以下命令
```bash
sudo usermod -a -G dialout $USER
sudo apt-get remove modemmanager -y
sudo apt install gstreamer1.0-plugins-bad gstreamer1.0-libav gstreamer1.0-gl -y
sudo apt install libqt5gui5 -y
sudo apt install libfuse2 -y
```
安装_QGroundControl_：

1. 下载[QGroundControl.AppImage](https://d176tv9ibo4jno.cloudfront.net/latest/QGroundControl.AppImage)。
2. 使用终端命令安装（并运行）：
```bash
chmod +x ./QGroundControl.AppImage
./QGroundControl.AppImage  (or double click)
```
![image.png](https://cdn.nlark.com/yuque/0/2022/png/34365726/1668764192214-4e77ce39-c1a8-4c80-81c2-73b778fdb070.png#averageHue=%234c4e2e&clientId=uba9b7b99-4d6f-4&from=paste&height=426&id=uf162abff&name=image.png&originHeight=426&originWidth=750&originalType=binary&ratio=1&rotation=0&showTitle=false&size=622918&status=done&style=none&taskId=uc46c46ae-9bbb-44d7-a8e6-52f2241204f&title=&width=750)
![image.png](https://cdn.nlark.com/yuque/0/2022/png/34365726/1668764229645-107ef766-dc96-4dcf-b112-4849357d3bd5.png#averageHue=%238c8a5a&clientId=uba9b7b99-4d6f-4&from=paste&height=412&id=u1c0374a7&name=image.png&originHeight=412&originWidth=750&originalType=binary&ratio=1&rotation=0&showTitle=false&size=104205&status=done&style=none&taskId=ubda96641-bb58-4e69-99bc-0b5974b1fa1&title=&width=750)

# 七、问题列表

## **安装ROS出现gpg: no valid OpenPGP data found.**

- 问题

执行：curl -s [https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc](https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc) | sudo apt-key add -
出现：gpg: no valid OpenPGP data found.

- 解决办法

将这条命令分两步执行。上述命令中有管道符号，curl是个类似下载的命令，因此尝试将上述命令分开两步执行。
1）curl -O [https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc](https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc) #该命令执行后在当前目录下保存一个ros.asc的文件
2）sudo apt-key add ros.asc #添加ros.asc

## github连接失败 443
[ubuntu 20.04 | 解决 Connecting to raw.githubusercontent.com :443... failed: Connection refused](https://blog.csdn.net/m0_52650517/article/details/119831630)

## 运行make px4_sitl_default gazebo报错
[https://blog.csdn.net/wxc__CSDN/article/details/121018458](https://blog.csdn.net/wxc__CSDN/article/details/121018458)
```bash
# 升级包
sudo apt-get upgrade
make clean 
make px4_sitl_default gazebo
```

## **找不到包: gazebo_ros**
[https://blog.csdn.net/m0_50848587/article/details/113104907](https://blog.csdn.net/m0_50848587/article/details/113104907)
```bash
sudo apt install ros-melodic-gazebo-ros-pkgs
```

## fx4通讯失败:connected: false

- 查看环境变量
```bash
# ros安装时设置
source /opt/ros/noetic/setup.bash

# px4配置
source ~/catkin_ws/devel/setup.bash
source ~/PX4_Firmware/Tools/setup_gazebo.bash ~/PX4_Firmware/ ~/PX4_Firmware/build/px4_sitl_default
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/PX4_Firmware
export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/PX4_Firmware/Tools/sitl_gazebo
```

- 查看版本
```bash
# 查询版本是否为9
gazebo --version
```
如果版本不是9,则升级或降级版本为9
[https://blog.csdn.net/JIEJINQUANIL/article/details/104056169](https://blog.csdn.net/JIEJINQUANIL/article/details/104056169)

-  注意gazebo_ros_pkgs包和gazebo9-ros-control时要选择noetic版本
```bash
$ sudo apt-get install ros-noetic-gazebo9-ros-pkgs ros-noetic-gazebo9-ros-control
```

## ROS包编译报错 No module named catkin_pkg.package

- 报错信息
```
ImportError: "from catkin_pkg.package import parse_package" failed: No module named catkin_pkg.package
Make sure that you have installed "catkin_pkg", it is up to date and on the PYTHONPATH.
```

- 解决方案
```
cd /
pip install catkin_pkg
pip install rospkg
```
## gazebo有时候打不开报错
用ctrl+c关闭仿真进程，有可能没有把Gazebo的相关进程关干净，这样再启动仿真时可能会报错。如果出现这种情况，可以用killall -9 gzclient，killall -9 gzserver 这两个命令强行关闭gazebo所有进程。
```
killall -9 gzclient
killall -9 gzserver
```
## darknet编译报错:/bin/sh: 1: nvcc: not found make: *** [obj/convolutional_kernels.o] Error
修改makefile
```bash
NVCC = /usr/local/cuda-10.0/bin/nvcc
```
## undefined reference to `cv::String::deallocate()'
:::info
image_opencv.cpp:(.text._ZN2cv6StringC2EPKc[_ZN2cv6StringC5EPKc]+0x30): undefined reference to `cv::String::allocate(unsigned long)'
/usr/bin/ld: obj/http_stream.o: in function `MJPG_sender::write(cv::Mat const&)':
http_stream.cpp:(.text._ZN11MJPG_sender5writeERKN2cv3MatE[_ZN11MJPG_sender5writeERKN2cv3MatE]+0x1d6): undefined reference to `cv::imencode(cv::String const&, cv::_InputArray const&, std::vector<unsigned char, std::allocator<unsigned char> >&, std::vector<int, std::allocator<int> > const&)'
/usr/bin/ld: http_stream.cpp:(.text._ZN11MJPG_sender5writeERKN2cv3MatE[_ZN11MJPG_sender5writeERKN2cv3MatE]+0x1de): undefined reference to `cv::String::deallocate()'
/usr/bin/ld: http_stream.cpp:(.text._ZN11MJPG_sender5writeERKN2cv3MatE[_ZN11MJPG_sender5writeERKN2cv3MatE]+0x738): undefined reference to `cv::String::deallocate()'
collect2: error: ld returned 1 exit status
make: *** [Makefile:169：darknet] 错误 1
:::

理由暂未确定，但目前我们不需要调用摄像头，所以将opencv设置为1，可以解决该报错

## **运行roslaunch px4 mavros_posix_sitl.launch**
:::info
RLException: [mavros_posix_sitl.launch] is neither a launch file in package [px4] nor is [px4] a launch file name
:::
```bash
# 在PX4目录下执行
source Tools/setup_gazebo.bash $(pwd) $(pwd)/build/px4_sitl_default

export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:$(pwd):$(pwd)/Tools/sitl_gazebo
```

## 运行github下载的无人车时出现：no such command [['/opt/ros/noetic/share/xacro/xacro.py'
:::info
RLException: Invalid <param> tag: Cannot load command parameter [robot_description]: no such command [['/opt/ros/noetic/share/xacro/xacro.py', '/home/lvrobot/ROSExample/src/ros_robotics/urdf/rrbot.xacro']]. 
Param xml is <param name="robot_description" command="$(find xacro)/xacro.py      '$(find ros_robotics)/urdf/$(arg model)'"/>
The traceback for the exception was written to the log file
:::
原文件中为
```bash
<param name="robot_description" command="$(find xacro)/xacro.py '$(find carsim_discription)/urdf/model.urdf'" />
```
改为
```bash
<param name="robot_description" command="$(find xacro)/xacro '$(find carsim_discription)/urdf/model.urdf'" />
```
如果之后仍然报错
进一步改为
```bash
<arg name="model" default="$(find carsim_discription)/urdf/model.urdf"/>
<param name="robot_description" command="$(find xacro)/xacro $(arg model)" />
```
## ubuntu 20.04 GPU 装darknet(未编译opencv)

## PX4出现git tag错误
原因应该是更换了git仓库指向，改回来即可

## 当运行无法打开gazebo， 显示正在等待加载模型
试试重新编译PX4
```bash
make px4_sitl_default gazebo
```
## 当编译px4的时候出现gazeboConfig.cmake     gazebo-config.cmake
```bash
sudo apt-get install libgazebo9-dev
```

## 安装gazebo的密钥的时候出错

E: 仓库 “[http://ppa.launchpad.net/v-launchpad-jochen-sprickerhof-de/pcl/ubuntu](http://ppa.launchpad.net/v-launchpad-jochen-sprickerhof-de/pcl/ubuntu) bionic Release” 没有 Release 文件。 N: 无法安全地用该源进行更新，所以默认禁用该源。 N: 参见 apt-secure(8) 手册以了解仓库创建和用户配置方面的细节。

更换系统源解决问题

