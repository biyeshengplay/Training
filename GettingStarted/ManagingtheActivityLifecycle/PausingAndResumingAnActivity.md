# 暂停和恢复activity
在正常的应用使用过程中，前台的activity有时候会被其他的可见控件挡住，这会让activity进入pause状态。
例如，当一个半透明的activity打开（例如使用一种dialog的风格），之前的activity就会暂停。
只要activity一直部分可见而且activity没有得到焦点，activity会一直保持paused状态。

然而，一旦activity被全部盖住并且不可见，它就进入stop状态了（这个会在下面的课程讨论）。

当activity进入pause状态时，系统调用activity中的onPause()方法，这个时候允许终止那些在pause状态不应该继续运行的操作
（例如视频）或者以防用户之后离开你的应用需要保存一些应该被永久保存的信息。如果用户返回了pause状态的activity，
系统将恢复页面并且调用onresume()方法。

注释: 当用户收到onPause()的回调时，这可能表明activity一会将要被暂停，焦点可以返回给activity。
但是，这通常是用户马上就要离开activity的第一个迹象。
[basic-lifecycle-paused.png]

图 1.当一个半透明的activity盖住了你的activity，系统调用onpause方法然后activity等待进入paused状态（1）。
如果系统返回activity时activity还处在paused状态，系统调用onResume()(2).


## 暂停activity
当系统调用onPause方法，技术上表示activity仍然部分可见，但是大多数情况表明用户将要离开activity然后不久将
进入stoped状态。你通常应该使用onpause回调处理：
停止动画或者其他可以消耗CPU的正在进行的操作。
提交没有保存的修改，但只在用户希望这些修改在他们离开时会被长期保存的情况下（例如邮件草稿）。
释放系统资源，例如广播接收者、传感器的引用（像GPS），或者其他的在activity被暂停和用户不在需要的情况下影响电视寿命的资源。
例如，如果你的应用使用Camera对象，onpause方法就是一个释放他的很好的地方。

@Override
public void onPause() {
    super.onPause();  // 总会最先调用父类方法

    // 释放camera,因为pause状态时我们不需要它
    // 而且其他的activity可能需要它。
    if (mCamera != null) {
        mCamera.release();
        mCamera = null;
    }
}
一般情况你不应该使用onpause永久保存用户修改（例如输入进表单的私人信息）
只有一种情况你应该在onpause里永久存储用户的修改：你确定用户希望这种修改是可以自动保存的（例如编辑邮件时）。
然而你应该避免在onpause中执行会密集使用cpu的操作，例如写入数据库，因为他会减慢到其他activity的可见跳转。
（你应该在onstop中执行重负荷加载的关闭操作）。

你应该保持onpause()方法中的操作总量相对简单，为了在你的activity真的被stop时调转到其他地方的时候能有一个
快速的过渡。

注释: 当你的activity进入pause状态，activity的实例依然驻在内存中，在activity恢复时重新被调用。你不需要创建初始化
在resume状态前的所有回调方法中创建过的的控件。

## 恢复activity
当用户从pause状态恢复activity时，系统会调用onResume（）方法。

要知道系统在每次activity来到前台时都会调用这个方法，包括activity第一次被创建时。因此你应该实现onresume（）方法
初始化那些在onPause（）方法中释放的组件和执行其他的需要在activity每次进入resume状态时都会执行的初始化操作。
（例如开始动画和初始化一些只在activity获得焦点时才会被使用的组件）
  
下面onResume（）的例子是上面onPause（）例子的副本，所以它初始化了在pause状态时释放的camera.

@Override
public void onResume() {
    super.onResume();  // 总会最先调用父类方法

    // 当activity拿到全部的用户焦点时获得camera实例
    if (mCamera == null) {
        initializeCamera(); // 处理camera初始化的本地方法
    }
}
