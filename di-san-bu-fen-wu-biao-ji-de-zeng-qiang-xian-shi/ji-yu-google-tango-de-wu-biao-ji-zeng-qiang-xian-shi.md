# Tango简介

Project Tango包含三块技术：运动追踪（Motion Tracking），深度感知（Depth Perception）和区域学习（Area Learning）。当这三大技术汇聚，Project Tango为移动平台带来了一种全新的空间感知技术，它可以让移动设备像人眼一样感知你所在的空间，找到行走的路，并且感知到哪里是墙，哪里是地，以及所有你身边的物体。

## 运动跟踪

Project Tango的第一个核心技术”运动追踪“的三维动态捕捉就是利用了上面提到的单目视觉技术的升级版Feature Tracking（Google称为特征追踪）。特征追踪如何理解呢？再回到John神的例子里，我们不再固定接收器的位置了，反而是拿着接收器（这里我们不再叫它接收器了，叫采集设备或相机）移动；而对面只有两个光点的发射器也变成一个一个的光点贴在John神家里屋子的各个角落。

当我们移动相机时不断地一帧一帧进行拍摄，因为拍摄到的光点的相对位置在不断变化（这里的“变化”是指拍摄到的两帧之间同一个光点的相对位置变化），通过计算我们可以得到相机的移动距离。简单来说，Tango设备在不断循环的一个过程就是：拍摄—识别特征点（个人估计是SIFT点）—匹配特征点—筛去错误匹配—坐标换算。当然Project Tango的运动追踪不仅如此，他还能通过一个内置的6轴惯性传感器（加速度计和陀螺仪）来捕捉相机的加速度和运动方向。

当融合了以上两类传感器的数据之后，Project Tango就“完美”（其实还有瑕疵）实现了三维运动追踪。

## 区域识别

为什么说还有瑕疵？因为运动追踪只是单纯得到了相机 移动的轨迹，然而对于相机所处的场景是零认知。所以一旦设备被关掉，它之前的运动轨迹就会被“忘掉”。最大的问题还是运动跟踪中所累积的误差，或者叫漂移，在长距离使用后，真实位置会和运算位置有很大差异。

为解决这个问题，John神团队为Tango设备设定了一种学习模式。这应该是他在Google X里面无人驾驶汽车研究成果的冰山一角。这种学习模式理解起来就简单很多了，为了让Tango设备具有一定记忆，而不再像一个被蒙着眼睛的人一样需要靠自己走了多少步来计算距离，Project Tango可以让用户预先录入某个场景（这里要特别提醒的是，录入的数据不光包括了运动跟踪里面所识别的特征点，还包含了场景本身），当用户重回这个场景的时候Tango设备会用自动录入的数据来纠正运动跟踪的数据，这个纠正的过程中录入场景点的那些特征点会被当作观测点，一旦发现与当下特征点匹配的观测点，系统便会修正当下的追踪数据。这就是Project Tango的第二大核心技术——区域识别。

## 深度知觉

如果说把深度感知作为Project Tango的第三大核心技术，我觉得有点虚，毕竟Google也是采用的第三方的深度传感器。不过对此Google并没有藏着掖着，而是很大方的表明Tango设备可以选择任意一种现今流行的深度感知技术。而对这项技术的顺利应用多半也要归功于John神在Project Natal的经历了。Kinect一代的推出震动了业界，震动了学术界，同时也把结构光（Structured Light）的概念推广了出去。

# Tango设备

## 联想Phab 2 Pro手机

全球第一款消费级的Tango设备。联想Phab 2 Pro手机采用6.4英寸2560×1440分辨率IPS屏幕，内置4050mAh容量电池，搭载骁龙652处理器，安卓6.0操作系统，4GB内存和64GB机身存储空间，最高支持128GB SD卡扩展，支持快充以及指纹识别，后置摄像头为1600万像素。

## 华硕Zenfone AR手机

全球第一款消费级的Tango+Daydream设备，使用骁龙821来保证用户得到Daydream体验，第一款Daydream手机Google Pixel和Asus Zenfone 3也使用的是骁龙821.由于Daydream Viewer现在兼容的手机尺寸是5.0到5.7寸屏，那么Zenfone AR搭配Android 7（Nougat）的5.7寸屏用起来应当是相当顺手的。

# 基于Tango的增强现实开发

## Tango开发之软件下载与设置

使用Unity为Tango设备开发应用，你需要做好以下准备：

* [Unity](https://unity3d.com/get-unity/download)（5.2.1—5.6.3），目前尚未支持2017.x版本。
* [Tango Unity SDK](https://developers.google.com/tango/downloads)。
* [Android SDK](https://developer.android.com/studio/index.html) 版本高于17 。
* 在Windows平台上，如果你的Tango设备没有自动识别，需要安装[Google USB Driver](https://developer.android.com/studio/run/win-usb.html)。这个是通用的USB驱动器，支持绝大多数Android手机。

除此之外，你还需要进行一些其他设置。

当你将Tango设备插到计算机之后，应该可以在消息通知区域看到三个竖条和“USB debugging connected”提示文字。如果没有看到的话，需要按照该[链接](https://developer.android.com/studio/run/device.html#setting-up)中的步骤2来开启手机的USB调试功能。

在Unity中第一次构建Android项目时，Unity会提示你设置Android SDK的安装路径，可以手动设置为Unity→Preferences→External Tools→Android SDK根目录。

Unity中Tango开发用的最低Android API是17，需要在Build Settings中进行设置。依次打开File→Build Settings→Android，点击PlayerSettings...按钮，在Other Settings下找到Minum API Leve，将其l设置为Android 4.2 ‘Jelly Bean’ \(APK Level 17\)或者更高。

## 针对所有Unity Tango应用的设置

### 在Unity中创建一个新的工程

1. 选择File→New Project。这样会打开一个Projects窗口。
2. 在Project Name文本框中，为你的新项目输入一个名字。
3. 在Location文本框中，要么接受其默认路径，要么点击右侧的图标将其设置为其他路径。
4. 确保项目设置为3D类型。
5. 点击Create Project。

### 导入Tango SDK

如果你还没有从[Tango官网](https://developers.google.com/tango/downloads)下载Tango SDK相关的.unitypackage包，现在就将其下载下来。

1. 选择Assets→Import Package→Custom Package。
2. 找到下载好的TangoSDK.unitypackage文件，将其打开。
3. 在Import Unity Package对话框中，确保勾选所有复选框（默认全部勾选），然后点击右下方的Import按钮。

![](/assets/01-unity-import.png)

这样，Tango相关的资源包就会出现在Project面板中的Assets文件夹下。

### 删除主相机

在Hierarchy面板，选择并删除Main Camera游戏对象。

我们将要开发的Apps会使用Tango SDK中自定义的“prefab”相机。然而，对于某些应用来说，Main Camera也可能会用到（例如，一个解谜/探秘游戏中，你会以第三视角观察自己的行为）。如果你计划的这种类型的游戏，在项目中保留Main Camera。







































































