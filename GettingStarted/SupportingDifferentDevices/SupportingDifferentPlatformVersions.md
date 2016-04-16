# 支持不同的平台版本
尽管最新版本的Android通常会为你的应用提供很棒的api,但是你需要继续支持Android的旧版本直到有更多的设备更新。
本课程将展示怎样在发挥出最新api优势的同时还继续支持旧版本Android。

平台版本的仪表板（http://developer.android.com/about/dashboards/index.html）
会基于访问google play商店的设备数量经常更新，来显示运行的每个Android版本的活跃设备的分布。
通常当你的应用目标版本为最新版本时，支持活跃设备的90%左右是一个很好的做法。

>提示：
Tip: 为了在多个Android版本上提供最好的特性和功能，你应该在你的应用中使用Android支持库（ Support Library），
它允许你在旧版本中使用一些特定的新平台版本的api。

## 指定最小和目标的api级别

 AndroidManifest.xml 文件描述了应用的详细情况 和应用支持的Android版本的标识。
具体地,  对于min<uses-sdk 标签的SdkVersion 和 targetSdkVersion属性，他们用于标识应用
可以兼容的最低api版本和你的应用为之设计和测试的最高版本。

例如:

<manifest xmlns:android="http://schemas.android.com/apk/res/android" ... >
    <uses-sdk android:minSdkVersion="4" android:targetSdkVersion="15" />
    ...
</manifest>

伴随Android新版本的发布，一些风格和特性可能会改变。为了允许你的应用可以充分利用这些改变并且确保你的应用可是匹配上每一个用户设备的风格，
你应该设置targetSdkVersion的值为可获得的最新的Android版本。

## 在运行时检查系统版本
在Build常量类中Android为每一个平台版本提供了一个独一无二的代号码。
在应用中使用这些代号码来构建条件，确保依赖更高api的代码只在这些api可以获得的系统上运行。

private void setUpActionBar() {
    // 确保我们正在使用 Honeycomb 或者更高的版本 才使用ActionBar 的api
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
        ActionBar actionBar = getActionBar();
        actionBar.setDisplayHomeAsUpEnabled(true);
    }
}
注释:当解析 XML 资源的时候, Android会忽略掉那些不被当前设备支持的属性。
所以你可以安全的使用那些只在最新版本上支持的属性而不用担心当旧版本遇到这些代码时会被中断。
例如，如果你设置了targetSdkVersion="11", 你的应用在Android3.0或者更高版本上默认包含了ActionBar.
然后给ActionBar添加菜单项，你需要在你的菜单资源xml文件中设置android:showAsAction="ifRoom",
在跨版本的xml文件中这样做是安全的，因为Android的旧版本会简单地忽略掉showAsAction属性（
也就是说你不需要在res/menu_v11目录里创建xml文件的另一个兼容版本)。

## 使用平台的风格和主题
android提供了能够给你的应用添加和底层操作系统一样外观和感觉风格的用户体验主题。
这些主题可以在manifest文件里设置来被应用到你的app中。
通过使用这些内置的风格和主题，你的应用可以自然的沿袭每一个最新发布的Android版本的外观和感觉风格。

为了使你的activity看起来像一个对话框:
<activity android:theme="@android:style/Theme.Dialog">
为了使你的activity有一个透明背景：
<activity android:theme="@android:style/Theme.Translucent">
为了应用你定义在/res/values/styles.xml文件中的自定义主题：
<activity android:theme="@style/CustomTheme">
为了把这个主题应用到你整个app中（全部的activity），在application标签中添加android:theme属性:
<application android:theme="@style/CustomTheme">

了解更多关于创建和使用主题的内容，请看 风格和主题（http://developer.android.com/guide/topics/ui/themes.html） 指南。

