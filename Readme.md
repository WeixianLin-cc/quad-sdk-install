# 安装Quad-sdk步骤

### 1. 安装Ubuntu20.04

[双系统启动效果_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1554y1n7zv?p=1&vd_source=260424bd71c9bf7b220c24f1e7de16d7)

安装完之后要装显卡驱动，在软件更新界面把原有的驱动换成Nvidia的专属驱动（会出现一大段英文，安装说的重启即可）

### 2.安装ROS noetic

```shell
wget http://fishros.com/install -O fishros && . fishros
```

安装ROS1noetic，rosdep

### 3.安装Ipopt

[Ubuntu20.04安装Ipopt的流程介绍及报错解决方法（亲测简单有效）-CSDN博客](https://blog.csdn.net/qq_44339029/article/details/133679131)

### 4.编译HSL

**安装依赖**

```text
sudo apt-get install libblas3 libblas-dev liblapack3 liblapack-dev gfortran
```

将coinhsl-archive-2021.05.05文件复制到HSL文件夹中

```text
cd Ipopt-3.12.7/ThirdParty/HSL  
ln -s ./coinhsl-archive-2021.05.05/ coinhsl
sudo chmod +x ./configure
./configure --enable-loadable-library
```

**编译：**

```text
make
sudo make install
```

**复制库：**

```text
cd Ipopt-3.12.7/lib
sudo cp -r * /usr/local/lib/ 
```

**复制头文件：**

```text
cd Ipopt-3.12.7/include
sudo cp -r * /usr/local/include/ 
sudo ldconfig

在bashrc中加入
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib
```

### 5.编译Quad-SDK

```text
cd ~/catkin_ws
cd src
catkin_init_workspace
git clone https://github.com/robomechanics/quad-sdk.git//（默认是18.04）
需要到github主页 https://github.com/robomechanics/quad-sdk main中改成noetic_devel，再下载或者gitclone
```

在quad-sdk功能包下external/ipopt/中新建**coinhsl**文件夹

将之前ipopt HSL下创建出的coinhsl文件夹内的文件复制进去

运行功能包根目录setup.sh （网络不行就反复调试）

```text
sudo chmod +x setup.sh 
./setup.sh 
```

最后在工作空间下编译功能包（可能会有warning，不用管）

```text
cd ~/catkin_ws
catkin build
```



**附快速使用命令**

Launch the simulation with:

```
roslaunch quad_utils quad_gazebo.launch
```

Stand the robot with:

```
rostopic pub /robot_1/control/mode std_msgs/UInt8 "data: 1"
```

Run the stack with twist input:

```
roslaunch quad_utils quad_plan.launch reference:=twist logging:=true
rosrun teleop_twist_keyboard teleop_twist_keyboard.py cmd_vel:=/robot_1/cmd_vel
```

Run the stack with global planner:

```
roslaunch quad_utils quad_plan.launch reference:=gbpl logging:=true
```

