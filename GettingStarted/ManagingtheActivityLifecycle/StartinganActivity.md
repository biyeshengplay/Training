#启动一个activity
不像那些应用从一个main()方法启动的程序范例一样，Android系统通过调用指定的回调方法来初始化代码，
这些回调方法对应这具体的activity生命周期的各个阶段。
启动一个activity有一个回调方法的序列，销毁一个activity也有一个回调方法的序列。

本课程提供了最终要的生命周期方法的概述，并将展示怎样处理创建一个activity实例后的第一个生命周期回调。
## 理解生命周期回调
在一个activity的生命中，系统会有序的调用一系列的生命周期方法，这类似一个阶梯金字塔。即activity的
每一个生命周期的阶段就是金字塔上每一个独立的台阶。当系统创建一个新的activity实例时，每一个回调方法
向塔顶移动一步也移动了activity的状态。塔顶就是activity运行在前台并且用户可以与之交互的地点。

当用户离开activity时,系统会调用其他的方法让activity的状态在金字塔上向下移动,以销毁这个activity。
在一些情形下，activity将只会向下移动部分台阶然后等待（例如当用户切换到其他应用的时候）,在这个阶段上activity
可以重新移动会金字塔顶（如果用户返回这个activity时）并且恢复用户离开时候的现场。
[basic_lifecycle.png]

图 1. 这是一个简要的Activity生命周期的说明, 类似一个阶梯形金字塔的表示.它显示了对每一个回调是怎样将activity朝着resume
的方向带上每一个阶段，这里也有将activity带回某个阶段的回调。activity也可以从pause和stop的状态返回resume状态。

根据你的activity的复杂度, 你可能不需要实现所有的生命周期方法. 然而, 你了解了每个方法并且实现这些方法来确保应用做用户
希望它做的事是重要的。实现activity的生命周期方法会让你的应用在几个情况上表现的更好：

当用户使用你的应用过程中用户收到电话请求或者切换到其他应用时你的应用不会崩溃
当用户没有正在活跃的使用应用时你的应用不会消耗珍贵的系统资源。
如果用户离开你的应用并且之后又返回应用时不会丢失用户的进度。
当屏幕在横屏竖屏之间切换旋转时不会崩溃或者丢失用户进度。

正如你将会在之后的课程学到的，这里有activity在不同状态之间过度的在图1中显示的几种状态。
然后他们中只有三个状态是可以静止的状态。也就是说，activity只能在三个状态中长时间存在。

Resumed
在这个状态下, activity存活在前台，用户可以与activity交互（有时候也称它为“运行中”状态）.
Paused
在这个状态下, activity会被其他activity部分遮盖-其他的activity这时正在前台，他可能是半透明的或者没有盖住整个屏幕。
这个被暂停的activity不能接收用户输入也不能执行任何代码。
Stopped
在这个状态下, activity 完全被盖住对用户也是不可见的; 
activity会进入后台状态. 用户进入停止状态，activity的实例和所有它的状态信息（例如成员变量）会被保持住，但是
它不可以执行任何代码。其他的状态（Created 和 Started）是短暂停留的，系统会调用其他的生命周期方法快速从这些状态移动到
下一个状态。也就是，在系统调用onCreate()方法之后，会快速地调用onStart()方法，然后紧接着调用onResume（）方法。

这些就是关于activity生命周期的基本内容.现在你将开始学习一些具体的生命周期的操作。

## 指定你应用的启动页面
当用户从主屏幕中选择你的应用图标时，系统会调用你的应用里已经声明成“launcher(或者main)”的activity的onCreate()方法。
这是给你的应用用户界面充当主入口的activity。你可以在manifest文件(存放在项目的根目录下)中定义哪个activity可以作为你应用的主activity.
  
这个主activity必须在manifest文件中用包含 MAIN action 和 LAUNCHER category的<intent-filter> 标签
来声明。例如：
  
  <activity android:name=".MainActivity" android:label="@string/app_name">
      <intent-filter>
          <action android:name="android.intent.action.MAIN" />
          <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
  </activity>
  注释：当你用sdk tools创建了新的Android工程时，默认的工程文件里面包括了一个activity类，这个类在manifest中使用了
  这个filter.
  
  如果所有的activity都没有声明 MAIN action 和 LAUNCHER category，那么你的应用图标将不会再主屏幕的应用列表中出现。
  
## 创建一个新的实例
大多数应用都包含了几个允许用户执行不同操作的activity。不管一个创建的activity是不是主activity，
当用户点击你的应用图标或者你的应用为了回应用户操作而启动的不同的activity，系统都会调用oncreate()方法
创建每一个新的实例。

你必须实现oncreate()方法来执行基本的应用启动逻辑，这些逻辑只会在activity的整个生命过程中运行一次。
例如，你的oncreate()的实现应该定义用户界面，可能的话还有初始化一些类成员变量。

例如下面的oncreate()方法，执行了一些activity基本启动内容的代码，例如声明用户界面（定义在xml布局文件中）,
定义成员变量，和一些界面的配置。

TextView mTextView; // 给布局文件中文本视图创建的成员变量

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    // 为activity设置用户界面布局
    // 这个布局文件定义在 res/layout/main_activity.xml 文件中
    setContentView(R.layout.main_activity);
    
    // 初始化成员变量TextView以便之后我们可以对它做些操作
    mTextView = (TextView) findViewById(R.id.text_message);
    
    //保证正运行在api Honeycomb 及以上来使用 ActionBar 的 api
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
        // For the main activity, make sure the app icon in the action bar
        // does not behave as a button
        ActionBar actionBar = getActionBar();
        actionBar.setHomeButtonEnabled(false);
    }
}
注意: 使用SDK_INT来阻止老系统执行新api的操作，否则旧版本会抛出运行时异常。

一旦oncreate()方法结束执行，系统会快速调用onstart()和onresume()方法。你的activity不会再created和started状态上停留，
技术上，activity在onstart()被调用时变成对用户可见的，但是onresume()紧跟着执行然后activity保持resumed状态直接一些情况发生改变了
状态，例如当接收到一个电话请求、用户移步去看了另一个activity、或者手机屏关闭。
后面的其他课程里，你将了解其他的方法onstart()和onresume()是怎样运行的，这些方法在pause和stop状态恢复为resume状态
时候是很有用的。

注释: oncreate方法包含了一个参数叫savedInstanceState，这个会在后面的关于重新创建activity的课程里讨论。
[basic_lifecycle_create.png]

图 2. 另一个生命周期结构的说明图，强调了创建一个新activity实例时候系统按次序调用的三个主要的回调方法：
 onCreate(), onStart(), and onResume(). 
 当回调按次序完成后，activity达到resume状态，这时候用户可以和activity进行交互知道用户切换到其他activity。
  
## 销毁activity
正如activity的第一个生命周期方法是oncreate(),它的最后一个回调是onDestroy().
系统调用这个方法作为activity实例完全从系统内存中移除的最后信号。

大多数应用不需要实现这个方法因为本地类引用会和这个activity一起被销毁，而且你的activity应该在onpause()和onstop()时
执行大部分的清理操作。然后如果你的activity里面包含了在oncreate()时创建的后台线程或者其他的如果没有合适关闭会有潜在
泄露内存风险的长期运行的资源，你应该在ondestroy()时销毁他们。

@Override
public void onDestroy() {
    super.onDestroy();  // 总是需要调用父类
    
    // 停止在activity在oncreate方法中启动的方法跟踪功能。
    android.os.Debug.stopMethodTracing();
}
注释:系统基本在所有情况下都是在已经调用onpause()和onstop()方法之后调用ondestroy(),除了：
当你在oncreate()方法中调用finish().在某些情况下例如当你的activity只作为一个临时的决定者去启动另一个activity，
你可能会需要在oncreate方法中调用finish()销毁activity，在这种情况下，系统会立刻调用ondestroy(),
不会调用任何其他的生命周期方法.

