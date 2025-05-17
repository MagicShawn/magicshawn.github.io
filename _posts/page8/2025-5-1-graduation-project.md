---
layout: page
title: "光伏清洁机器人定位与建图"

subtitle: "FAST-LIO-SAM"
date: 2025-5-1 22:15:13 -0400
background: '/img/posts/03.jpg'

---

# Graduation Project

---

**相关链接：**

[*LIO-SAM source code repositories*](https://github.com/TixiaoShan/LIO-SAM)

[*LIDAR-DRIVER - HesaiLidar_General_ROS*](https://github.com/HesaiTechnology/HesaiLidar_General_ROS)

[*Hesai Lidar 产品资料下载*](https://www.hesaitech.com/cn/download?sid_for_share=99125_3)

---


# content
 - [**测试条件**](#测试条件)
 - [**项目结构**](#项目结构)
    - [*1.文件夹结构*](#文件夹结构)

 - [**项目部署**](#项目部署)
    - [*1.仿真部分*](#仿真部分)
    - [*2.实际部署部分*](#实际部署部分)
  
 - [**Some Reference**](#reference)


---


## 一. 测试条件 {#测试条件}


- **ROS版本 :** ***Noetic***

- **系统 :** ***Ubuntu 20.04 Jetson AGX Orin***
- **激光雷达 :** ***Hesai-Pandar-XT-16***
- **IMU :** ***WHEELTEC-N100***
- **RTK :** ***WHEELTEC-G70***

--- 

## 二. 项目结构 {#项目结构}

### 1.文件夹结构 {#文件夹结构}
***核心文件目录如下：***
包含两个部分:
 - **graduation_slam_benchmark** : ***激光雷达建图定位算法,包含多种算法测试以及实际部署的代码与功能包***
 - **Photovoltaic_robot_simulator** : ***机器人仿真代码与功能包***

```bash
./Work_space
├── graduation_slam_benchmark
│   ├── build
│   ├── datasheet
│   ├── devel
│   ├── log
│   ├── logs
│   ├── scripts
│   └── src
├── Photovoltaic_robot_simulator
│   ├── assets
│   ├── build
│   ├── datasheets
│   ├── devel
│   ├── logs
│   ├── README.md
│   ├── scripts
│   └── src

```

## 三. 项目部署 {#项目部署}

### 1.仿真部分 {#仿真部分}

***核心文件目录如下：***   

```bash
./src
├── hardware_driver
│   └── livox_ros_driver
├── robot_simulator
│   ├── robot_control    # 机器人控制功能包
│   └── robot_description # 机器人描述文件
├── slam_algorithm
│   ├── ego-planner # ego-planner算法
│   ├── FAST_LIO
│   ├── FAST_LIO_LOCALIZATION # FAST_LIO_LOCALIZATION 重定位算法
│   └── FAST_LIO_SAM # FAST_LIO_SAM 定位建图算法
└── velodyne_simulator

```

#### 运行指令
为方便实际运行常用的启动指令都写在对应ROS工作空间下的 ***/script*** 文件夹中

***启动机器人人仿真***
```bash
roslaunch robot_description gazebo.launch
```

***启动机器人控制***
此时机器人接受键盘控制
```bash
# move control
roslaunch robot_control velocity_controller.launch 
```
***开启键盘控制***
此时机器人接受键盘控制
```bash
# start to control robot by Keyboard
rosrun teleop_twist_keyboard teleop_twist_keyboard.py
```
***开启激光雷达建图***
```bash
#算法运行
roslaunch fast_lio_sam mapping_solar_robot.launch
#开启可视化
rosrun rviz rviz -d $(rospack find fast_lio_sam)/rviz_cfg/loam_livox.rviz

```

***系统节点树***
![system_node](/img/posts/page8/rosgraph.png){: width="720" }

***实际效果演示***
![slam_result](/img/posts/page8/map_scans.gif){: width="720" }



#### 重定位模块
***开启重定位***
```bash
#reference
roslaunch fast_lio_localization localization_velodyne.launch
```
![slam_relocalization](/img/posts/page8/Relocalization.gif){: width="720" }


### 2.实际部署部分 {#实际部署部分}

***核心文件目录如下：***   
```bash
./src/
├── data_perception # 数据处理
│   ├── hesai_to_velodyne # 激光雷达预处理
│   ├── LiDAR_IMU_Init # IMU-LiDAR外参标定
│   └── odom_to_kitti # 里程计转kitti格式
├── hardware_driver # 传感器驱动
│   ├── fdilink_ahrs
│   ├── gnss
│   ├── HesaiLidar_General_ROS
│   ├── livox_ros_driver
│   └── usb_cam
└── localization # 定位建图算法
    ├── A-LOAM
    ├── FAST_LIO
    ├── FAST_LIO_SAM # 主要使用的算法
    ├── LeGO-LOAM
    └── LIO-SAM

```
#### 实际部署部分启动指令如下：
为方便实际运行常用的启动指令都写在对应ROS工作空间下的 ***/script*** 文件夹中

***一键启动脚本:***
***graduation_slam_benchmark*** 工作空间下的 ***/script*** 文件夹中
```bash
./script/start_slam_system.sh

```

***GPS驱动***

```bash
# 1. 启动 GPS 驱动节点
source devel/setup.bash && roslaunch wheeltec_gps_driver wheeltec_nmea_driver.launch
```

***AHRS驱动***
```bash
# 2. 启动 AHRS 驱动节点
source devel/setup.bash && sudo chmod 666 /dev/ttyUSB0 && roslaunch fdilink_ahrs ahrs_data.launch
```

***启动 PTP 时间同步服务***
```bash
# 3. 启动 PTP 时间同步服务（需要 sudo 权限）
sudo ptp4l -m -4 -i eth0 -S
```

***激光雷达驱动***
```bash
# 4. 启动 Hesai 激光雷达驱动
source devel/setup.bash && roslaunch hesai_lidar hesai_lidar.launch

```

***FAST-LIO-SAM 算法***
```bash
# 5. 启动 FAST-LIO-SAM 算法
source devel/setup.bash && roslaunch fast_lio_sam mapping_velodyne16_lio_sam_parking_dataset.launch
```


#### 效果演示:
![slam-fact](/img/posts/page8/circle.gif){: width="720" }

---

## 四. Some Reference {#reference}

[***1.FAST-LIO-SAM仓库***](https://github.com/kahowang/FAST_LIO_SAM)

[***2.重定位算法***](https://github.com/HViktorTsoi/FAST_LIO_LOCALIZATION)

[***3.RM仿真项目-pb_rm_simulation***](https://github.com/LihanChen2004/pb_rm_simulation)

[***4.卡耐基梅隆-自主探索开发环境***](https://www.cmu-exploration.com/)
