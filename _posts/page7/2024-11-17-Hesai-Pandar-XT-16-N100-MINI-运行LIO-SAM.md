---
layout: page
title: "Hesai-Pandar-XT-16/N100-MINI 运行LIO-SAM"

subtitle: "LIO-SAM"
date: 2024-10-27 22:15:13 -0400
background: '/img/posts/04.jpg'

---

# LIO-SAM 运行

---


**相关链接：**

[*LIO-SAM source code repositories*](https://github.com/TixiaoShan/LIO-SAM)

[*LIDAR-DRIVER - HesaiLidar_General_ROS*](https://github.com/HesaiTechnology/HesaiLidar_General_ROS)


[*Hesai Lidar 产品资料下载*](https://www.hesaitech.com/cn/download?sid_for_share=99125_3)


---


# content
 - [**测试条件**](#1.测试条件)
 - [**安装**](#2.安装)
    - [*1.设备驱动安装*](#1.1驱动安装)
      - [*激光雷达驱动安装*](#1.1.1激光雷达驱动安装)
      - [*IMU驱动安装*](#1.1.2IMU驱动)
    - [*2.LIO-SAM安装*](#1.2LIO-SAM安装)

 - [**系统配置**](#3.运行)
    - [*各ROS参数包配置*](#3.1参数配置)
    - [*传感器参数标定*](#3.2参数标定)
    - 
 - [**总结**](#总结)
 - [**Some Reference**](#reference)


---


## 一. 测试条件{#1.测试条件}


- **系统 :** ***Ubuntu 18.04 DJI-manifold2-C***
- **激光雷达 :** ***Hesai-Pandar-XT-16***
- **IMU :** ***WHEELTEC-N100-mini***

--- 

## 二. 安装{#2.安装}


### 1.设备驱动安装{#1.1-驱动安装}

#### 激光雷达驱动安装{#1.1.1激光雷达驱动安装}

```bash
    ...
    mkdir -p ~/catkin_ws/src
    cd catkin_ws/src
    git clone https://github.com/HesaiTechnology/HesaiLidar_General_ROS.git --recursive
    
    #back to the catkin_ws directory
    cd ../
    caktin_make
    ...
```

**详细安装步骤见：**
[*LIDAR-DRIVER - HesaiLidar_General_ROS*](https://github.com/HesaiTechnology/HesaiLidar_General_ROS)

**bug编译排查**
笔者在编译 ***HesaiLidar_General_ROS package*** 过程中遇到了一些问题，具体如下：
```log
    fatal error: hesai_lidar/PandarScan.h: No such file or directory #include "hesai_lidar/PandarScan.h"
```
参照 [驱动仓库issue - PandarScan.h：没有此文件或目录](https://github.com/HesaiTechnology/HesaiLidar_General_ROS/issues/24)

```bash
    cd ~/catkin_ws/src/HesaiLidar_General_ROS
    vi CMakeLists.txt
    #48行：添加依赖
    add_dependencies(PandarGeneral hesai_lidar_generate_messages_cpp)

```

#### IMU驱动安装{#1.1.2IMU驱动}

**驱动下载:**
[*惯导&GNSS/GPS模块资料*](https://pan.baidu.com/s/1vyT1Qqz5RC151n-E1nxggw)

解压到工作空间 ***src/*** 目录下
```bash
    ...
    cd catkin_ws/src
    unzip fdilink_ahrs_ROS1.zip
    ...
```
**详细安装步骤见：**
[*轮趣IMU-N100 ROS驱动安装*](https://blog.csdn.net/qq_57061492/article/details/136722204?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-1-136722204-blog-124779753.235%5Ev43%5Epc_blog_bottom_relevance_base4&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-1-136722204-blog-124779753.235%5Ev43%5Epc_blog_bottom_relevance_base4&utm_relevant_index=2)

### 2.LIO-SAM安装{#1.2LIO-SAM安装}

同样在 ***src/*** 目录下克隆仓库
```bash
    ...
    cd catkin_ws/src
    git clone https://github.com/TixiaoShan/LIO-SAM.git
    ...
```
**详细安装步骤见：**
[*LIO-SAM source code repositories*](https://github.com/TixiaoShan/LIO-SAM)

--- 

## 三. 系统运行{#3.运行}

### 1. 配置修改{#3.1配置修改}


```bash
    cd catkin_ws
    catkin_make
```

### 2.传感器校准{#3.2传感器校准}

#### IMU校准：

```bash
rosbag info /home/dji/Work_Space/FAST_LIO/tools/datasheet/N100-mimini-2h.bag 
```

```bash
path:        /home/dji/Work_Space/FAST_LIO/tools/datasheet/N100-mimini-2h.bag
version:     2.0
duration:    2hr 9:09s (7749s)
start:       Nov 18 2024 13:13:08.98 (1731906788.98)
end:         Nov 18 2024 15:22:18.18 (1731914538.18)
size:        279.4 MB
messages:    774928
compression: none [361/361 chunks]
types:       sensor_msgs/Imu [6a62c6daae103f4ff57a132d6f95cec2]
topics:      /imu   774928 msgs    : sensor_msgs/Imu
```

***(1)* 安装校准工具**

仓库地址：[*校准工具包 imu_utils*](https://github.com/gaowenliang/imu_utils)

```bash
    cd catkin_ws/src
    git clone https://github.com/gaowenliang/code_utils.git
    git clone https://github.com/gaowenliang/imu_utils.git
    cd ~/catkin_ws

    # 依次单独编译
    catkin_make -DCATKIN_WHITELIST_PACKAGES=code_utils
    catkin_make -DCATKIN_WHITELIST_PACKAGES=imu_tuils
```
编译bug修复：

```bash
    #openCV版本不匹配

    # edit for build bug fix 
    row 84/107: CV_LOAD_IMAGE_GRAYSCALE 改为 cv::IMREAD_GRAYSCALE
    row 94/117: CV_MINMAX 改为 NORM_MINMAX

    vim src/code_utils/src/mat_io_test.cpp
    row 33: CV_LOAD_IMAGE_UNCHANGED 改为 cv::IMREAD_UNCHANGED

    # 头文件缺失
    error: aggregate ‘std::ofstream out_t’ has incomplete type and cannot be defined
    std::ofstream out_t;

    vim src/imu_utils/src/imu_utils.cpp
    row22: #include<fstream>

    fatal error: backward.hpp: No such file or directory
    #include "backward.hpp"

    vim src/code_utils/src/sumpixel_test.cpp
    row2: #include "backward.hpp" 改为 #include "code_utils/backward.hpp" 

```
**详细参考:** *[Pandar40P Lidar + H30 IMU 外参标定](https://blog.yanjingang.com/?p=9414)*



### 2. 运行

```bash
    source devel/setup.bash
    roslaunch PandarGeneral pandargeneral.launch
    roslaunch lio_sam run.launch
```


