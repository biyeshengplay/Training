# 重新创建一个activity
有些场景里activity被销毁是源于正常的应用使用行为，例如当用户点击返回按钮或者activity通过调用finish销毁自己。
系统也会在当前stop的activity长时间没有使用或者前台activity需要更多资源时销毁activity，系统必须结束后台进程来恢复内存。

当activity因为用户点击返回键或者activity结束自己时，对于系统来说这个activity实力不再存在因为这种行为表示这个activity
不再被需要。然而，如果系统销毁activity时以为系统的限制（而不是正常的应用使用行为），那么尽管实际的activity实例已经不存在了，
系统会记得它存在过，如果用户返回这个activity，系统会使用一系列描述销毁时状态的数据来创建这个activity的新实例。
 系统保存的用来恢复之前状态的数据叫"instance state"，以键值对的集合形式存储在Bundle对象里。

注意：activity会在每次用户旋转屏幕时被销毁和重新创建。当屏幕改变方向时，系统销毁和重新创建前台activity是因为屏幕的配置
已经改变了，activity需要加载需要替换的资源（例如布局文件）。

默认情况下，系统使用Bundle实例保存activity中布局文件中视图对象的信息（例如输入到Edittext中的文本内容）。
所以如果activity实例被销毁和重新创建，布局的状态会存储之前的状态不需要你来写代码实现。然而，你的activity可能有更多状态
信息想要被存储，例如activity里跟踪用户进度的成员变量。

注释：为了让Android系统存储activity中视图的状态，每一个view必须有一个android：id属性提供的独一无二的id。

为了保存其他的关于activity状态的数据，你必须重写onSaveInstanceState()回调方法。系统会在用户要离开你的activity时
调用它，传递在activity被意外销毁时被保存的Bundle对象。如果系统会在之后重新创建这个实例，会在onRestoreInstanceState() 
和 onCreate()方法中传递相同的Bundle对象。
 
 [basic-lifecycle-savestate.png]
 图 2. 当系统暂停你的activity时, activity调用onSaveInstanceState() (1) 在这你可以声明其他的当
 activity被重新创建时你想要保存的状态数据。如果activity被销毁而且相同的实例需要被创建时，系统会传递在（1）中
 定义的状态数据给onCreate（）方法和onRestoreInstanceState()方法（3）。
 
 ##保存activity状态
 当activity开始进入stop状态，系统调用onSaveInstanceState方法所以activity能够使用键值对集合来保存状态信息。
 这个方法的默认实现保存了activity的view层的状态信息，例如edittext控件里面的文本或者listview滚动的位置。
 
 为了保存activity其他的状态信息，你必须实现onSaveInstanceState方法给Bundle对象添加键值对，例如：
 
 static final String STATE_SCORE = "playerScore";
 static final String STATE_LEVEL = "playerLevel";
 ...
 
 @Override
 public void onSaveInstanceState(Bundle savedInstanceState) {
     // 保存用户当前的游戏状态
     savedInstanceState.putInt(STATE_SCORE, mCurrentScore);
     savedInstanceState.putInt(STATE_LEVEL, mCurrentLevel);
     
     // 总是要调用父类方法，它可以保存view层的状态
     super.onSaveInstanceState(savedInstanceState);
 }
 注意：总是要调用父类方法让默认实现来保存view层的状态
 
 ##恢复activity状态
 当之前被销毁的activity被重新创建时，你可以从系统传递给你的Bundle中恢复你保存的数据。onCreate（）和onRestoreInstanceState（）
 方法回调都会收到同样的保存实例状态信息的Bundle.
 
 因为不管系统是创建了一个新的实例还是从之前的activity恢复的实例onCreate（）方法都会调用，所以你必须在读Bundle时检查Bundle
 是否为空。如果为空说明系统是创建了一个新的activity的实例而不是恢复了一个之前销毁的。
 
 例如，下面是在onCreate（）中存储状态数据的做法：
 
 @Override
 protected void onCreate(Bundle savedInstanceState) {
     super.onCreate(savedInstanceState); // 总是首先调用父类方法
    
     // 检查是不是重新创建一个之前销毁的实例
     if (savedInstanceState != null) {
         // 获取保存状态的成员数据
         mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
         mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
     } else {
         // 可能为一个新的实例的成员初始化数据
     }
     ...
 }
 替代在onCreate（）方法中获取状态，你可以选择实现onRestoreInstanceState（）方法，onRestoreInstanceState（）方法
 会在onStart()方法之后调用。系统只在有保存的状态可以获得时才会调用onRestoreInstanceState（）方法，
 所以你不需要判断Bundle是否为空:
 
 public void onRestoreInstanceState(Bundle savedInstanceState) {
     // 总是调用父类方法，因为它可以恢复view层数据
     super.onRestoreInstanceState(savedInstanceState);
    
     //从保存的实例中恢复成员状态
     mCurrentScore = savedInstanceState.getInt(STATE_SCORE);
     mCurrentLevel = savedInstanceState.getInt(STATE_LEVEL);
 }
 注意：总要调用onRestoreInstanceState()方法的父类实现，默认的实现能够恢复view层的状态。
 
 想了解更多关于在运行时（例如屏幕旋转）的重新启动事件中创建activity的内容，
 请看 处理运行时修改（http://developer.android.com/guide/topics/resources/runtime-changes.html）。