# 创建一个Android工程

一个Android工程包括了一个应用的所有源代码文件.
本课程将展示怎样通过Android studio 和 在命令行使用sdk tools 来创建一个应用.


>注意:
你应该保证已经安装了Android sdk,或者如果你准备使用Android studio,
那应该保证Android studio 已经安装了.如果这两个你都没有完成,请在本课程开始之前跟着指南的步骤安装Android sdk.

## 使用Android Studio创建工程

  1. 在Android studio中,创建一个工程:
   - 如果你还没有打开任何一个工程,在欢迎界面里,点击New Project.
   - 如果你已经打开过工程了,在菜单栏的File里,选择 New Project,创建新工程的界面就会出现.

  2. 填写界面上需要输入的内容,点击下一步.
     如果你使用和下面一样的值来输入,跟进这些课程会更容易一些.
   - **应用名(Application Name)** 指的是将显示给用户看到的应用名字.在这个工程里使用"My First App".
   - **公司域(Company domain)** 提供一个追加在包名上的修饰标识; Android Studio 将会记住这个标识并在每次创建新工程的时候追加.
   - **包名(Package name)** 是可完全标识工程的名字(遵循和java编程语言一样的命名规则). 你的包名一定要保证是在Android系统中所有安装应用包中独一无二的.
     你可以独立于应用名和公司域修改包名.
   - **工程地址(Project location)** 是你的系统存储工程文件的目录.

  3. 在 **Select the form factors your app will run on** 下面, 检查一下选择框 **Phone and Tablet**.
  4. 对于**Minimum SDK(最小sdk)**选项,选择**API 8: Android 2.2 (Froyo)**.
     最小要求sdk指的是你的应用能够支持的Android的最早的版本, 表明了你使用的API level.为了支持尽可能多的设备,
     你应该在保证能提供核心功能的情况下尽可能小的设置这个值.如果有一些功能只可能在一个大于最小sdk的Android版本上实现,
     这些功能对于核心功能是不要紧的,那么你可以使这些特性只当运行在支持这些特性的版本上的时候调用(会在支持不同平台版本
     ([Supporting Different Platform Versions](http://developer.android.com/training/basics/supporting-devices/platforms.html))一章中介绍).
  5. 所有其他的选项(TV, Wear, and Glass)不用选中,点击下一步.
  6. 在**Add an activity to <template>** 选项下面,选择Blank Activity,点击下一步.
  7. 在**Customize the Activity**选项下面,修改activity的名字为MyActivity.layout名字改成activity_my,标题改成
  MyActivity.菜单资源名改成menu_my.
  8. 点击完成按钮,创建工程完成.

>Activities
 activity是Android框架的特色之一.activity提供给用户访问app的入口,app里面可能有很多的activity.
 一个应用通常会有一个主activity在用户启动应用时候显示,其他的activity用于当用户选择一些内容查看时,例如,当用户想在应用内执行其他的任务时.
 想了解更多关于activity的内容点击[activities](http://developer.android.com/guide/components/activities.html).


 你的Android工程现在是一个简单的"Hello World"应用了,这个工程包含了一些默认文件,我们来花一点复习这些文件里最重要的部分:
 - app/src/main/res/layout/activity_my.xml
   这个xml文件是给在Android studio中创建工程时添加进来的activity使用的.在创建工程的流程里,Android studio展示了这个包含了一个文本框的布局文件,
   并显示了一个屏幕界面的预览.这个文件包含了一些默认的来自meterial design库的界面元素,包括应用栏和一个浮动的操作按钮,还有一个独立于主面板的布局文件.

 - app/src/main/res/layout/content_my.xml
   这个xml文件处在activity_my.xml文件内,这个文件包含了一些设置和一个显示着"Hello world"信息的文本控件.
 - app/src/main/java/com.mycompany.myfirstapp/MyActivity.java
   当创建工程的流程完成时,当你选择了一个你看到的创建的activity的类,这个文件会在Android studio的选项卡中出现.
   当你构建并且运行应用,Activity类运行起了MyActivity.java文件并且加载了那个显示了"Hello world"的布局文件.

 - app/src/main/AndroidManifest.xml

   manifest文件描述了应用的基本特征并且定义了应用中的每一个组件.在跟进这些课程过程中你会多次看到这个文件,并且会添加更多的组件到你的应用中.

 - app/build.gradle
   Android studio 使用 Gradle来编译和构建应用.对于每一个工程里的每一个模块都有一个build.gradle文件,对于整个工程还有一个build.gradle文件.
   通常情况下你只对模块下的build.gradle文件有兴趣,在这个例子里是app或者application模块下的build.gradle.这是应用配置构建依赖的地方,也包括一些默认配置设置:

   - **compiledSdkVersion** 是指针对你要编译的应用的平台版本.默认情况被设定成你的Android sdk中最新的版本. (可能是4.1或者更高的版本,你必须通过SDK Manager安装其中的一个.)
    你仍然可以构建一个支持老版本的应用,但是这种设置允许你在最新的版本和设备上使用新特性和优化应用来获得更好的体验.
   - **applicationId** 指的是你在创建工程的流程中确认的完全包名.
   - **minSdkVersion** 指的是你在创建工程的流程中确认的最小SDK.这是你的应用可以支持的最早版本.
   - **targetSdkVersion** 指的是已经测试过的应用使用过的最高版本.当有新的Android版本可获得时,你应该在新的版本上测试你的应用,并且更新这个值来匹配
   最新的api level,然后就可以利用新平台的特性.想了解更多信息,请阅读[支持不同平台版本](http://developer.android.com/training/basics/supporting-devices/platforms.html).

   关于Gradle的更多信息请看[使用Gradle构建工程](http://developer.android.com/sdk/installing/studio-build.html).

 还有/res的子目录包含了一些应用中的资源:
   - drawable-<density>/  存放drawable资源的目录,不同于启动图标,它专为不同各种手机的密度设计.
   - layout/ 存放定义用户界面的文件的目录,例如上面讨论的用来描述 MyActivity类的基本布局的activity_my.xml文件.
   - menu/ 定义应用菜单项的文件目录.
   - values/ 这个文件目录包含了其余的XML文件,例如字符串xml文件和颜色xml文件.

 要把应用运行起来,请继续下一节课程.
