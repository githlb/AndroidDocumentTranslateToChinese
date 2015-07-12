随便翻译，仅供娱乐
#Application Fundamentals

安卓app采用Java语言编写。Android SDK tools这套工具会编译你的代码，然后把所有数据和资源文件一起打包到一个后缀是.apk的压缩文件。一个Apk文件包含一个app的所有内容，并且这个就是安卓设备用来安装文件的安装包。

一旦被装到设备上了，每个安卓app会生存在它自己的安全沙盒里面：

* 安卓系统是一个多用户的linux系统，其中每个app是一个单独的用户
* 一般情况下，系统给每个app分配一个唯一的linux用户id(这个id仅仅是系统使用，app不知道id是啥)。系统会给app中所有文件设置权限，使得只有这个id的用户才能够访问这些文件
* 每个进程有它自己的虚拟机，所以一个app的代码是和其它app隔离运行的
* 一般情况下，每个app运行在自己单独的进程中。每当这个app的组件要运行时，都会先起个进程，不运行时或者系统要回收内存时就会把这个进程关掉

就这样，安卓实现了最小权限。也就是说，每个app正常情况下只能运行需要的组件，其它啥也干不了。这样就创建了一个非常安全的环境，因为如果系统没给权限的话，这个app就不能访问系统的其它部分

然而，有很多种方式可以和其它app共享数据以及访问系统服务：
* 你可以给两个app设置相同的用户id，这样这两个app就可以访问彼此的文件。为了节省系统资源，设置相同用户id的app运行在同一个进程、使用同一个虚拟机(前提是两个app签名是一样的)
* 一个app可以请求访问设备数据的权限，比如通讯录、短信、可移动的存储设备（SD卡）、相机、蓝牙等等。所有的权限必须在安装的时候就确定。

以上内容是关于一个安卓app在系统是怎么存在的，这篇文章的下面部分介绍：
* 定义你的app的核心框架组件
* 用于声明组件、权限等的manifest文件
* 独立于代码并且能让你的app优雅地跑在各种不同设备的资源文件

##APP组件App Components
APP组件是每个app的基本单元。每个组件都是系统进入app的一个不同的入口。 并不是所有的组件都是用户的入口，看具体情况，但是每一个组件都是一个单独的实体并且扮演特殊的角色——每个都是独立的编译单元用来定义你的app的全部行为 

一共有四种App组件。每一个都是由特定的目的并且有特定的生命周期定义这个组件是怎么创建、怎么销毁。
    
###Activities
* 一个activiy代表一个用户界面。例如，一个邮件的app可以会有一个activity用来显示新邮件列表，另一个activity用来写邮件，再有一个activity用来读邮件。虽然在email app中每个activity共同组成一种内聚的用户体验，每个仍然是保持独立的。比如，其它的app可以其中这些activity中任意一个(如果email app允许的话)。举个栗子，相机app可以为了分享照片而启动email app中的activity来编写新邮件
* 一个activity作为Activity的子类来实现，你可以从Activities开发指南学到更多

###Services
* service是运行在后台为远程进程执行长时间操作的组件。service 没有用户界面。举个栗子，一个service可能在后台播放因为，而这时候用户在玩另一个app，或者可能从网络获取数据而不影响activity的用户交互。其它控件，例如activity，可以启动service或者绑定service以用于交互。
* 一个service作为service子类来实现，你可以从Services开发指南了解到更多

###Content providers
* 一个content provider 管理一组用于分享的app数据。你可以存储这些数据在文件，sqlite数据库或者网上或者你的app可以访问到任意一个持久化存储的位置。通过content provider，其它app可以查询甚至修改这些数据(如果content provider允许的话)。举个栗子，系统本身提供了一个content provider用来管理用户的联系人信息。这样，随便一个有相应权限的app都能读写某个特定联系人的信息了。
* Content provider同样也适用于你的私有app进行读写而不进行分享。举个栗子，Note Pad例子app使用content provider来保存notes
* 一个Content provider 作为ContentProvider的子类来实现，并且必须实现一系列能跟其它app交互的标准的api。你可以从Content Providers开发指南了解到更多。

###Broadcast receivers
* broadcast receiver 是一个能够响应整个系统广播通知的组件。很多广播都是系统发的，比如说，屏幕关闭广播，电量低广播，或者拍照广播。App也能发广播——例如，要让其它app知道数据已经下载到设备并且其它app可以使用了的时候。虽然虽然广播接收器没有显示用户界面，当接收到广播时它们可能会创建一个状态栏通知来提醒用户。更普遍的是，一个广播接收器只是一个路由，跳转到别的组件去做一些小量的工作。例如，会跟据广播事件去启动service来执行一些操作
* 一个Broadcast receiver 作为BroadcastReceiver的子类实现，并且每个广播当作一个Intent对象来分发。你可以从BroadcastReceiver类了解到更多

安卓系统设计独特的一面是任意一个app可以启动其它的app中的组件。例如，如果你想让用户使用相机设备进行拍照，这事其它的app早就已经做了，并且你可以直接使用，而不必自己开发一个activity用来拍照。你也不必包含甚至链接到相机app的代码。相反的，你可以很简单地启动相机app的activity去拍照。当拍照完成的时候，照片就会返回到你的app并且你能够使用。对于用户来说，拍照功能好像就是你的app的一部分一样。

当系统启动一个组件时候，会为这个app启动一个进程（如果这个app不是已经在运行的话）并且实例化组件需要的那些类。例如，如果你的app启动camera app中的activity来拍照，这个activity会跑在camera app的独立进程而不是在你的app的进程中。所以，不像其它系统的app，安卓app并没有一个单一的入口(例如没有main方法)
    
由于每个app都跑在不同的进程，通过权限来限制其它app的访问，你的app是不能够直接激活其它app中的组件的。但是Android 系统能够做到。所以，激活其它app的组件，你必须要发个消息给系统，指定启动一个特定组件的intent。系统会帮你启动。
    
##激活组件Activating Components

四种组件中的三个——activities、services、broadcast receviers都是通过一个叫intent的异步消息来激活的。Intents在运行时绑定其它的组件（你可以将它们当作从别的组件发来的请求动作的消息）不管那个组件是不是属于你的app的。

一个intent通过Intent对象来创建，它定义了一个消息去启动一个指定的组件或者某一个类组件，可以是显示的，也可以是隐式的

对于activities和services来说，一个intent定义了执行的动作(例如，去显示或者发送一些东西)，也可能指定作用的数据的URI（除了其它东西外，组件可能在启动的时候需要知道）。例如，一个intent可能表达一个显示一个图片或者打开一个网页的activity的请求。有时候，你可以启动一个activity去接受结果，这种情况下，activity会在intent中返回结果（例如你可以发起一个intent让用户选择联系人并且让它返回选中的那个联系人的URI）

对于广播接收器，intent只是简单地定义广播通知（例如，一个广播提示电量低的广播仅仅包含一个已知的action 字符串表明"battery is low"）

其它的组件，content provider并不是通过intent激活。而是有ContentResolver来的请求时才激活。
    

    




