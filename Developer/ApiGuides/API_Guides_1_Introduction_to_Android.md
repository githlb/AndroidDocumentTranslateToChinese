随便翻译，仅供娱乐
#Introduction to Android

Android 提供了丰富的应用框架，你可以通过JAVA语言为移动设备开发有新意的应用和游戏。这个文档左边的导航列表列出怎样使用Android提供的丰富的API来开发应用。

##应用提供多个入口Apps provide multiple entry points

安卓应用就是一堆能够被独立调用的组件构成的。举个栗子，一个单独的activity提供了一个单独的屏幕用于用户交互，而一个service则是在后台独立执行。

你可以使用Intent从一个组件启动另一个组件。甚至还可以启动另一个应用的组件，比如说从当前的应用启动个地图app用来显示地址。这种模型给一个单独的应用提供了多个入口并且允许随便哪个app调用。

Learn more:

* App Fundamentals
* Intents and Intent Filters
* Activities

##应用兼容不同的设备Apps adapt to different devices

安卓提供一种适配app框架可以让你用唯一的资源满足实现不同设备的兼容。举个栗子，你可以为不同的屏幕尺寸的设备创建不同的xml布局文件，系统会自动根据当前屏幕尺寸来切换不同的布局。

有些app功能需要使用到特定的硬件设备（比如相机），那么你可以在运行时查询能否访问这个硬件设备。如果有必要的话，你还可以声明你的app需要用到哪些功能，那么app市场如Google Play Store就会禁止那些不支持这个功能的设备安装这个app<br>

Learn more:

* Device Compatibility</br>
* Resources Overview</br>
* User Interface Overview</br>