---
layout: page
title: "ubuntu18.04(20通用)安装REALSENSE D435深度相机驱动"

subtitle: "Intel RealSense SDK"
date: 2024-10-20 22:15:13 -0400
background: '/img/posts/03.jpg'
---

# 安装 REALSENSE D435 深度相机驱动


---


**相关链接：**

[安装 REALSENSE D435 深度相机驱动](https://www.joycekirkland.site/index.php/2021/07/22/ubuntu18-0420通用安装realsense-d435深度相机驱动/)

[Intel RealSense 官方安装指南](https://github.com/IntelRealSense/librealsense/blob/master/doc/installation.md)


---


# content
 - [**更新**](#更新)
 - [**克隆/下载对应 SDK**](#克隆下载对应储存库)


---


## 一、更新 {#更新}

```
sudo apt-get update
sudo apt-get upgrade
```

---


## 二、安装SDK {#克隆下载对应储存库}

**一般来说选择稍微稳定一点的版本，可以查看对应的支持列表：** 

[*Intel® RealSense™ SDK支持列表*](https://github.com/IntelRealSense/librealsense/releases)

**下载对应版本的SDK (2024-10-21)：** 

[*librealsense-2.55.1储存库-other version*](https://github.com/IntelRealSense/librealsense/releases/tag/v2.55.1)           
[*librealsense-2.55.1.zip*](https://github.com/IntelRealSense/librealsense/archive/refs/tags/v2.55.1.zip)  

**解压下载的SDK：**

```
unzip librealsense-2.55.1.zip
```
