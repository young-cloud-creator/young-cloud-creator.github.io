---
title: Android常用基础组件
updated: 2022/8/23 0:50
tags: 
    - Android
categories: 
    - Android
index_img: /illustration/android-component/index.png
excerpt: 在Android开发中需要用到各种组件来实现程序功能，这里介绍一些常用的基础组件知识。Android基础组件可以分为界面组件、服务组件、广播组件和数据组件四大类，一个正常运行的Android App至少需要用到这些组件中的一种或多种，所以这些组件是Android开发的基础知识。
---

在Android开发中需要用到各种组件来实现程序功能，这里介绍一些常用的基础组件知识。

Android基础组件可以分为界面组件、服务组件、广播组件和数据组件四大类，一个正常运行的Android App至少需要用到这些组件中的一种或多种，所以这些组件是Android开发的基础知识。

# 界面组件

界面组件有界面容器，也有TextView、Button等界面元素，这里来介绍界面容器。

界面容器承担着为界面提供容器的重要作用，我们打开任何一个App，都会看到程序界面，界面中包括各种各样的文字、按钮、图形等等元素；而有时通过一些操作，又会打开新的界面（例如：在聊天软件中点击某人头像，打开与ta的私聊界面；又例如：相册总览界面、大图页面和图片编辑页面就是三个不同的界面）。用户通过界面与程序进行交互，而界面容器为界面提供了容身之地，可见界面容器在Android开发中的重要性。

下面介绍Activity和Fragment两种界面容器。

## Activity

Activity是Android系统中最基础的界面容器，它承担着前台交互、程序入口和布局容器的功能。前台交互，也就是用户直接通过Activity提供的界面与程序进行交互；与通常C/C++命令行程序从main函数开始运行不同，Android程序总是从MainActivity中的成员函数开始运行，所以Activity承担了程序入口的功能；此外，Android中若干UI组件（例如文本TextView、图片ImageView、按钮Button等）的位置依赖于布局，而Activity充当着布局容器的角色。

### Activity基本用法

1. 注册：在AndroidManifest.xml中注册
2. 布局：在layout中配置布局文件
3. 绑定：将布局绑定到Activity

### Activity生命周期

前面提到Activity具有程序入口的功能，Android程序启动时总遵从一定的顺序去调用MainActivity的一些成员函数来完成程序的初始化、界面的加载等等任务并最终将界面呈现在用户面前，最后关闭程序。Activity从创建到最终销毁全过程的函数调用顺序就是Activity的**生命周期**，系统在Activity生命周期的不同阶段调用不同的函数，所以Android开发者必须了解Activity的生命周期，程序才能在预期的时候做出预期的行为。

下图展示了在Activity生命周期不同阶段系统的行为：

![Activity生命周期](/illustration/android-component/activity_lc.png)


```Text
                                 +-------------+
                   +-----------> |   Started   +-------> onResume() <-------------+
                   |             +-------------+            |                     |
                   |                    ^                   |                     |
               onStart()                |                   v                     |
                   ^                    |               +---------+               |
                   |                    |               | Resumed |               |
                   |                    |               +---+-----+               |
                   |                 onStart()              |                 +---+----+
             +-----+-----+              ^                   +--->onPaused()-->| Paused |
onCreate()-->|  Created  |              |                                     +---+----+
             +-----------+              |             +---------+                 |
                                     onRestart()<-----+ Stopped |<------onStop()<-+
                                                      +----+----+
                     +-----------+                         |
                     | Destroyed |<------- onDestroy() <---+
                     +-----------+
```

- `onCreate()` 在Activity创建时被调用，通常在这里创建视图、绑定数据
- `onStart()` 即将进入前台
- `onResume()` 与用户进行交互
- `onPause()` 被其他Activity部分覆盖，Activiy部分可见时被调用
- `onStop()` 被其他Activity完全覆盖，Activiy不可见时被调用
- `onRestart()` 重启已经Stop的Activity时被调用
- `onDestroy()` Activity被销毁时被调用，用于释放Activity占用的所有资源
- `onSaveInstanceState()` 在非正常关闭时被调用，用于保存运行数据
- `onRestoreInstanceState()` 用于恢复保存的数据

总而言之，Activity总是在Active、Pause、Stop和Killed四种状态之间转换，当它被创建并和用户交互时，处于Active状态；当它失去焦点时，处于Pause或Stop状态；当它被销毁后，处于Killed状态。

一些常见场景中的Activity生命周期流转：

1. 在配置发生改变时（例如：屏幕发生旋转，显示方向由竖屏变为横屏），将会调用`onConfigurationChanged()`方法，可以通过对该方法的重写实现一些特殊功能。在通常情况下，配置发生改变将引起Activty的重建，重建的函数调用过程如下所述：
    - 销毁：Resumed -> onSaveInstanceState() -> onPause() -> onStop() -> onDestroy() -> Destroyed
    - 重建：Destroyed -> onCteate() -> onStart() -> onRestoreInstanceState() -> onResume() -> Resumed
    - 可以通过配置AndroidManifest中的configChange属性来实现不重建Activity
2. 启动：onCreate() -> onStart() -> onResume() -> Resumed 
3. 退出：Resumed -> onPause() -> onStop() -> onDestroy() -> Destroyed
4. 部分覆盖：Resumed -> onPause() -> Paused 
5. 部分遮挡恢复：Paused -> onResume() -> Resumed 
6. 完全覆盖：Resumed -> onPause() -> onSaveInstanceState() -> onStop() -> Stoped 
7. 完全遮挡恢复：Stoped -> onStart() -> onResume() -> Resumed 
8. 后台回收：Stoped -> Killed 
9. 回收恢复：Killed -> onCreate() -> onStart() -> onRestoreInstanceState() -> onResume() -> Resumed 

### Activity启动模式

Activity具有4种启动模式，分别是Standard、SingleTop、SingleTask和SingleInstance。

- Standard
    - 这种启动模式允许Activity重复，这也是默认的启动模式
- SingleTop
    - 这种启动模式不允许Activity与栈顶Activity重复，也就是不允许连续的重复Activity
- SingleTask
    - 这种启动模式不允许同一栈内Activity有重复，如果要启动的Activity已在栈内，将会从栈中移除该Activity之上的所有Activity
- SingleInstance
    - 这种启动模式不允许全局有重复的Activity

## Fragment

Fragment是Android系统为解决屏幕尺寸碎片化问题而提供的一种轻量级页面容器。

### Fragment基本用法

1. 创建Fragment布局文件
2. 创建Fragment子类，在适当函数中加载布局文件
3. Activity加载Fragment
    1. 静态加载
        - 在Activity的onCreate()函数中声明Fragment
        - 或在Activity的布局文件中声明Fragment
    2. 动态加载
        - 使用Fragment Manager进行加载
        
### Fragment生命周期

如下所示：

![Fragment生命周期](/illustration/android-component/fragment_lc.png)

一些常见场景中的Fragment生命周期流转：

1. 启动：onAttach() -> onCreate() -> onCreateView() -> onActivityCreated() -> onStart() -> onResume() -> Resumed
2. 退出：Resumed -> onPause() -> onStop() -> onDestoryView() -> onDestory() -> Destroyed
3. 部分覆盖：Resumed -> onPause() -> Paused
4. 部分遮挡恢复：Paused -> onResume() -> Resumed
5. 完全覆盖：Resumed -> onPause() -> onSaveInstanceState() -> onStop() -> Stoped 
6. 完全遮挡恢复：Stoped -> onStart() -> onResume() -> Resumed
7. 后台回收：Stoped -> Killed
8. 回收恢复：Killed -> onCreate() -> onStart() -> onRestoreInstanceState() -> onResume() -> Resumed 

此外，Fragment生命周期也可以通过FragmentTransaction.setMaxLifecycle()进行手动干预

## Fragment与Activity的交互

- 组件获取
    - Fragment获取Activity中的组件：`getActivity().findViewById(id)`
    - Activity获取Fragment中的组件：`getFragmentManager.findFragmentById(id)`

- 数据传递
    - Activity传数据给Fragment：`setArguments(bundle)`
    - Fragment传数据给Activity：
        - 通过对象（`getActivity()`）直接传递（方法调用/接口调用）
        - 通过viewmodel/handler/broadcast/eventbus等通信


## 服务组件 Service

Android服务组件（Service）是一个后台运行的组件，通过服务组件可以使Android程序在未在前台运行时仍然可以完成一些任务（例如后台下载、后台音频播放、后台邮件检查等）。Service可以由其他应用启动，并能将组件绑定到Service上进行交互，通过Service，甚至可以实现进程间通信（IPC）。

### Service基本形式

Service的基本形式分为启动和绑定两种状态。

- 当应用组件通过调用`startService()`启动服务时，服务处于启动状态，这种状态的服务可以无限期运行（无论启动它的应用组件是否被销毁），直到任务完成由服务自行结束运行（例如：下载服务在下载完成后自行停止，在下载完成前不会停止）。通常来说，以这种状态运行的服务执行单一操作，且不会将相关信息返回给调用方。
- 当应用组件通过调用`bindService()`绑定到服务时，服务处于绑定状态，这种状态的服务提供一个client-server（即客户-服务器）接口，组件可以通过发送请求给服务来实现与服务的交互，服务可以返回信息给绑定了服务的组件。多个组件可以绑定同一个服务，而在绑定到服务的全部组件都解绑后，服务将被销毁。

服务既可以以启动状态运行，也可以以绑定状态运行，也可以同时以两种状态运行（即服务既提供交互接口，又可以无限期运行）。一个服务可以以何种状态运行取决于是否实现了相应的回调方法：`onStart()`（允许组件启动服务）和 `onBind()`（允许组件绑定服务）。当内存过低且系统必须回收资源供具有用户焦点的Activity使用时，Android 系统可能会强制停止服务。

### Service基本用法

1. 注册
    - 在AndroidManifest中使用`<service.../>`标签，例如：
    ```XML
    <manifest ... >
    ...
        <application ... >
            <service android:name=".DemoService" />
            ...
        </application>
    </manifest>
    ```
    - 可以在此时声明服务为私有服务，阻止其他应用访问服务
2. 创建
    - 建立相应的Service实现类
3. 加载
    - 通过`startService()`/`bindService()`启动服务

### Service生命周期

两种方式（启动状态/绑定状态）启动的Service生命周期如下所示：

![Service生命周期](/illustration/android-component/service_lc.png)

通过`onStart()`启动的服务，将通过`stopSelf()`或`stopService()`停止服务。`onBind()`方法需要返回一个`IBinder`来提供交互接口，以供客户与服务器通信。

### Service与Activity通信

详见[详解Android Service与Activity之间通信的几种方式](https://cloud.tencent.com/developer/article/1741577)

- 通过`Binder`
    1. 定义`Binder`子类，实现`getService()`方法，返回`Service`对象
    2. 实现`Service`类的`onBind()`方法，返回`Binder`对象
    3. Activity类中实例化`ServiceConnection`对象，并实现`onServiceConnected()`方法
    4. Activity调用`bindService()`方法得到`Service`实例，调用`Service`的方法实现通信

- 通过`Broadcast` 
    1. 在`Service`类中发送广播
    2. 在Activity类中实现广播接收器并注册广播

## 广播组件 Broadcast

广播（Broadcast）是Android系统中的一种进程间通信的手段。Android应用与Android系统、Android应用之间都可以相互收发广播消息，例如，Android系统会在各种系统事件发生时发送广播，像网络状态发生改变、开始充电等，Android应用也可以自定义广播并发送，从而实现进程间通信。

在Android中，存在两种Broadcast：

- 标准广播（无序广播）：这是一种完全异步执行的广播，效率较高，在广播发出后，所有广播接收者都会几乎在同时收到广播信息
- 有序广播：这是一种同步执行的广播，同一时刻只会有一个广播接收者收到广播信息，并会在广播接收者执行完成处理逻辑之后才会传送给下一个广播接收者。有序广播会按照广播接收者的优先级来传送，优先级高的接收者优先接收广播。除此之外，广播接收者可以选择拦截广播，使广播不再继续传播。

### 基本用法

广播接收者需要注册自己要接收的广播，在广播发出后，Android系统将会自动把广播信息推送给接收者。接收广播需要借助于BroadcastReceiver类来进行，通常做法是定义一个BroadcastReceiver的子类，并在其中写下广播处理逻辑。

#### 接收广播

有两种注册广播接收器的方法，分别是通过AndroidManifest文件进行静态注册和通过Java或Kotlin代码动态注册：

- 静态注册
    - 如下所示，其中`<intent-filter>`中的内容指定了接收器要接收的广播信息
    ```XML
    <receiver android:name=".MyBroadcastReceiver"  android:exported="true">
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED"/>
            <action android:name="android.intent.action.INPUT_METHOD_CHANGED" />
        </intent-filter>
    </receiver>
    
    ```
    - 在AndroidManifest文件中进行定义后，还需要在Kotlin或者Java代码文件中创建对应的BroadcastReceiver子类，例如上面的AndroidManifest文件中的`android:name`属性为`.MyBroadcastReceiver`，就需要创建一个名为`MyBroadcastReceiver`的BroadcastReceiver子类来接收定义的广播，如下所示
    ```Kotlin
    class MyBroadcastReceiver : BroadcastReceiver() {

        override fun onReceive(context: Context, intent: Intent) {
            //广播处理逻辑
        }
    }
    ```
- 动态注册
    - 除了上面的静态注册，还可以直接在Kotlin或Java代码中进行动态注册，而无需在AndroidManifest文件中定义。具体做法是，首先创建一个BroadcastReceiver（或者它的子类）的实例，然后创建`IntentFilter`并调用`registerReceiver(BroadcastReceiver, IntentFilter)`就成功注册广播接收器了，如下所示
    ```Kotlin
    val br: BroadcastReceiver = MyBroadcastReceiver()
    val filter = IntentFilter(ConnectivityManager.CONNECTIVITY_ACTION).apply {
        addAction(Intent.ACTION_AIRPLANE_MODE_CHANGED) }
    registerReceiver(br, filter)
    ```

在不需要接收之前注册的广播时，可以使用`unregisterReceiver(android.content.BroadcastReceiver)`来取消注册广播接收器。此外，如果是通过动态注册的方式注册了广播接收器，还需要在Activity生命周期结束时（`onDestroy()方法中`）取消注册广播接收器，否则将会导致接收器资源泄露的问题。

#### 发送广播

Android提供三种发送广播的方法，分别是`sendBroadcast(Intent)`、`sendOrderedBroadcast(Intent, String)`和`LocalBroadcastManager.sendBroadcast`

- `sendBroadcast(Intent)`
    - 发送标准广播，例如
    ```Kotlin
    Intent().also { intent ->
        intent.setAction("com.example.broadcast.MY_NOTIFICATION")
        intent.putExtra("data", "Notice me senpai!")
        sendBroadcast(intent)
    }
    ```
- `sendOrderedBroadcast(Intent, String)`
    - 发送有序广播
- `LocalBroadcastManager.sendBroadcast(Intent)`
    - 发送本地广播，这种广播只会发送给与发送方位于同一应用的广播接收器，而不会发送给其他应用

### 常用系统广播

- Intent.ACTION_CONNECTIVITY_CHANGE
    - 网络状态发生改变
- Intent.ACTION_BATTERY_CHANGED
    - 充电状态、电池电量发生变化
- Intent.ACTION_SCREEN_ON
    - 屏幕打开
- Intent.ACTION_SCREEN_OFF
    - 屏幕关闭
- Intent.ACTION_PACKAGE_INSTALL
    - 应用程序开始安装
- Intent.ACTION_BOOT_COMPLETED
    - 系统启动完成
- Intent.ACTION_PACKAGE_ADDED
    - 应用程序完成安装
- Intent.ACTION_PACKAGE_REMOVED
    - 应用程序被移除
- ...

未完待续...