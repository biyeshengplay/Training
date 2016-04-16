# 停止和重启activity
正确的停止和重启activity是activity生命周期中重要的过程，它会让用户感觉应用总是存活的而且没有丢失用户进度。
在下面这些场景中activity是会被停止和被重新启动的：
-用户打开了最近使用应用的窗口然后从你的应用切换到其他应用，你的应用的当前在前台的activity就会被停止。
 如果用户从主屏幕的启动图标点击返回到你的应用或者从最新使用应用窗口返回到你的应用，activity会重新启动。
- 用户在你的应用里执行了一个启动一个新activity的操作。当第二个activity创建后当前activity会被停止。
  如果用户这时候点击返回按钮，第一个activity会重新启动。
- 当用户使用你的应用时接收到一个通话请求。

activity提供的两个生命周期方法onStop（）和onRestart（)可以方便定制你的activity怎么停止和重新启动。
 与表明了部分UI被遮盖的暂停状态不同，stop状态说明UI不再可见并且用户焦点也在其他activity上（或者在一个完全不同的应用上）。

注释: 因为在activity停止时系统会保留内存中activity中的实例，所以你可以都不需要实现onStop（）和onRestart（）方法
(甚至onStart()方法)。对于大多数相对简单的activity来说，activity将自己处理stop和restart方法就好，你可能只需要
使用onPause()方法去暂停正在进行的操作还有释放系统资源。
[basic-lifecycle-stopped.png]
图 1. 当用户离开activity，系统调用onStop（）方法来停止activity（1）。如果用户返回了处在stop状态的activity，系统
调用onRestart（）（2），紧跟着调用onStart（）（3）和onResume（）（4）。注意无论什么行为让activity进入stop状态，
系统总会在调用onStop（）之前调用onPause（）。

# 停止你的activity
当activity收到了onStop（）方法的调用，activity将不再可见而且应该释放所有用户不再需要的资源。
一旦activity进入stop状态，系统可能会在它需要回收系统内存时销毁这个activity的实例。在极端的情况下，系统可能会在
activity还没有调用最后的onDestroy（）时就杀掉了你的应用进程，所以在onStop（）中释放可能会导致内存泄露的资源是非常重要的。

因为onPause（）方法已经在onStop（）之前被调用了，你应该在onStop方法中关闭更大更消耗cpu的的操作，例如写数据库。

例如，这是一个保存草稿内容到固态存储的onStop方法实现：

@Override
protected void onStop() {
    super.onStop();  // 总是最先调用父类方法

    // 保存笔记的当前草稿, 因为activity马上就要停止我们希望当前笔记不会丢失。
    ContentValues values = new ContentValues();
    values.put(NotePad.Notes.COLUMN_NAME_NOTE, getCurrentNoteText());
    values.put(NotePad.Notes.COLUMN_NAME_TITLE, getCurrentNoteTitle());

    getContentResolver().update(
            mUri,    // 笔记要更新的URI地址.
            values,  // 要设置的列明和新值得键值对.
            null,    // 没有使用的select条件.
            null     // 没有使用的WHERE条件.
            );
}

当activity进入stop状态，activity对象会在内存中常驻，在activity恢复时重新被调起。你不需要重新初始化在Resume状态前
的回调方法中创建的组件。系统也会保持布局中每一个view的当前状态，所以如果用户在editext控件中输入文本，这些文本会被保存
而不需要你来保存和恢复它。

注释:即使系统销毁了stop状态的activity，系统仍然会在一个Bundle（一种键值对）中存储视图对象的状态（例如edittext中的文本），
在用户返回同一个activity实例时恢复数据（下节课将讨论使用Bundle来保存其他的状态数据用于activity被销毁和重新创建）。

# 启动和重新启动activity
当activity从stop状态返回前台，它会接收到一个onRestart（）回调。系统也会调用onStart（）方法，因为每次activity变得
可见时都会调用它（无论是重新启动的还是第一次创建的）。然而onRestart()方法只会在activity从stop状态恢复时才会调用。
所以你可以使用这个方法执行一些只当activity前一状态是stop而不是destroy时才执行的特殊的恢复操作。

应用使用onRestart（）方法来恢复activity的状态不是很常见，所以没有其他的应用到常用APP中的可借鉴指导了。然而因为
onStop（）方法必要时会清理activity的资源，你可能需要在restart时重新实例化他们。你也需要在activity第一次创建时实例化他们
（当这个activity没有存在的实例时）。因此，你应该经常使用onStart（）回调作为onStop（）方法的对应方法，因为系统
在创建activity和从stop状态重新启动activity时都会调用onStart（）方法。

例如，用户在返回你的应用前可能已经离开很长时间了，onStart（）方法是一个确认系统功能是否可用的好地方：
@Override
protected void onStart() {
    super.onStart();  // 总是最先调用父类方法
    
    // activity可能被重新启动或者是第一次被创建
    // 所以这应该是确认GPS是否可用的地方
    LocationManager locationManager = 
            (LocationManager) getSystemService(Context.LOCATION_SERVICE);
    boolean gpsEnabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);
    
    if (!gpsEnabled) {
        // 在这创建一个请求用户让GPS可用的对话框
        //然后当用户点击ok时使用一个action为android.provider.Settings.ACTION_LOCATION_SOURCE_SETTINGS的intent
        //带用户跳转到设置页去设置GPS可用。
    }
}

@Override
protected void onRestart() {
    super.onRestart();  // 总是最先调用父类方法
    
    //activity会从stop状态重新启动
}

当系统销毁你的activity，系统会调用activity中的onDestroy（）方法。因为你可能已经在onStop（）中释放了大部分资源，
这时当你收到onDestroy（）的回调时已经没有很多需要做的了。这个方法是你清理可能导致内存泄露的资源的最后机会，所以
你应该保证其他的线程被销毁，其他的长期运行的操作例如method tracing(方法追踪)也被停止。