---
title: Android系统的软件栈
index_img: /illustration/android-basic/index.png
tags:
    - Android
    - 操作系统
categories: 
    - Android
excerpt: Android操作系统是一个基于Linux内核与其他开源软件的开放源代码的移动操作系统，由谷歌成立的开放手持设备联盟持续领导与开发，它是目前市场占有率最高的移动操作系统之一。这里简要介绍Android系统的软件栈构成。
---

Android操作系统是一个基于Linux内核与其他开源软件的开放源代码的移动操作系统，由谷歌成立的开放手持设备联盟持续领导与开发，它是目前市场占有率最高的移动操作系统之一。这里简要介绍Android系统的软件栈构成。

纵观Android系统软件栈，它从上到下包括System Apps、Java API Framework、Native C/C++ Libraries、Android Runtime、Hardware Abstraction Layer、Linux Kernel以及Power Management几个部分，下面逐一介绍这些部分的作用。

![Android软件栈](/illustration/android-basic/software-stack.png)

# System Apps （系统应用）

虽然叫System Apps，但是它并不仅仅代表系统应用的层次，在Android系统中，系统应用和用户安装的应用并没有本质区别。我们平时所使用的App都处于这个层次中，该层也是Android软件栈的最顶层，负责直接与用户进行交互。

# Java API Framework （Java API框架）

该层由Java编写的各种API构成，Android系统通过这些API向开发者提供了丰富的组件和服务。

通过这些API，开发者可以构建出各种各样的App。这些API形成创建Android应用所需的模块，它们可以简化核心模块化系统组件和服务的重复使用。例如，Content Providers提供了进程间通信服务；View System提供了一个丰富可拓展的视图系统，包括Android开发中常用的各种布局（Layout）、按钮（Button）、文本框（TextView）、图像（ImageView）等；Activity Manager管理了Activity的生命周期和Activity栈，Notification Manager管理了应用发出的通知，等等。

# Native C/C++ Libraries （原生C/C++库）

这一层次包括各种由C/C++编写的库，包括开源的Web浏览器引擎Webkit，用于播放、录制音视频的库，用于网络安全的SSL库等，许多Android核心组件就是基于这些C/C++编写的库构建的。Java API提供了一部分C/C++库的接口，例如OpenGL ES库是由C/C++编写的，但是开发者可以通过Android提供的Java OpenGL API来使用OpenGL ES库。如果要直接访问某些原生C/C++库或者使用C/C++语言实现某些特定功能，则可以通过Android NDK来完成。

# Android Runtime

这个层次包括Android Runtime（ART）虚拟机和一套核心运行时库。

ART虚拟机是一个为Android系统应用运行提供的虚拟机环境，对于Android系统，每个进程运行在独立的虚拟机进程中，通过执行DEX字节码而运行。ART除了提供Android应用运行环境外，还提供对Java代码的预先（AOT）和即时（JIT）编译、垃圾回收（GC）等功能。在Android 5.0之前，ART虚拟机是Dalvik虚拟机，后来的ART则逐渐脱离Dalvik，不断演进和引入新特性。

核心运行时库则为Java语言功能提供了支持。

# Hardware Abstraction Layer （硬件抽象层 HAL）

HAL封装了底层的各个硬件模块，向上层的Java API框架提供硬件接口，例如相机、蓝牙、传感器等。当Android应用访问这些硬件模块时，HAL将会加载对应的库模块。

# Linux Kernel （Linux内核）

Android平台的基础是Linux，ART依赖于Linux内核来执行诸如线程管理、底层内存管理的任务。此外，硬件生产商开发硬件驱动也依赖于Linux内核，设备的电源管理也处于该层。