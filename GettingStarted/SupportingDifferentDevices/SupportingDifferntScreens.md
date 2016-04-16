# 支持不同的屏幕
android使用两个常见的属性来区分设备屏幕：尺寸和密度。
你一定希望自己的应用可以安装在横跨所有尺寸和密度的设备上。正因如此，你需要包括一些可替换的资源来为不同的尺寸和屏幕来优化应用的表现。

尺寸概括来说分为四种：small,normal,large,xlarge(小，普通，大，超大)屏
密度也有四种：low (ldpi), medium (mdpi), high (hdpi), extra high (xhdpi)（低，中，高，超高）

为了声明你想给不同屏幕使用的不同的布局和位图，
你必须要吧这些可替换的资源放在相应的目录下，和为了配置不同语言的字符串资源做的操作很相似。

也要注意屏幕的方向（横向或者纵向），这也被认为是一种屏幕尺寸的变化，
所以很多应用需要为每一个方向的用户体验来优化布局文件。

##创建不同的布局文件
为了优化不同屏幕尺寸上的用户体验，你需要为你想支持的每一种屏幕尺寸创建独一无二的xml文件。
每一个布局文件需要保存到相对应的以-<屏幕尺寸>为后缀命名的资源目录下。
例如，为大屏适用的唯一的布局文件需要保存在res/layout_large目录下。

>注释：为了恰当的适配屏幕，Android会自动的放大你的布局文件。因此为不同尺寸屏幕设计的布局不需要担心UI控件的绝对尺寸，但是要
会影响用户体验的布局结构（例如相对于同级视图比较重要的视图的尺寸和位置）

例如，这个工程包括了默认的布局文件和一个为大屏做的可替换的布局文件：

MyProject/
    res/
        layout/
            main.xml
        layout-large/
            main.xml
文件名一定要完全相同的，但是为了给对应的屏幕尺寸提供优化的UI显示，内容是可以不同的。

像平常一样很简单的把布局文件引用到代码中：

@Override
 protected void onCreate(Bundle savedInstanceState) {
     super.onCreate(savedInstanceState);
     setContentView(R.layout.main);
}

系统会基于应用正在运行的设备的屏幕尺寸决定加载哪个布局目录中的布局文件更合适。
关于Android如果选择合适的资源的更多信息可以在 提供资源（http://developer.android.com/guide/topics/resources/providing-resources.html#BestMatch） 指南中获得。

另一个例子，这是为横屏方向做的带有替换布局的工程：

MyProject/
    res/
        layout/
            main.xml
        layout-land/
            main.xml

默认情况下，layout/main.xml文件用于竖屏方向。

如果你想为横屏提供一个不一样的布局，包括在大屏上的情况，那么你需要同时使用large和land两个修饰符：

MyProject/
    res/
        layout/              # 默认的(竖屏)
            main.xml
        layout-land/         # 横屏
            main.xml
        layout-large/        # 大屏 (竖屏)
            main.xml
        layout-large-land/   # 大屏 横屏
            main.xml
>注释：Android3.2及以上版本支持一个高级的定义屏幕尺寸的方法，它允许你在无关密度的像素方面基于最小宽高为屏幕尺寸指定资源。
本课程不会涵盖这个新技术的内容，想了解更多请看 为多种屏幕设计 （http://developer.android.com/training/multiscreen/index.html）。

## 创建不同的位图
你总是需要提供在每一种密度（低，中，高，超高密度）上都能正确缩放的位图资源。
本节课帮你实现在所有屏幕密度上的良好的图形质量和表现。

为了生成这个图片，你应该开始使用矢量格式的原始资源，然后使用下面的尺寸比例为每一种密度生成图片：

xhdpi: 2.0
hdpi: 1.5
mdpi: 1.0 (基准线)
ldpi: 0.75
这意味着如果你为xhdpi密度的设备生成了200x200的图片，那么你应该为hdpi密度的设备生成同样的资源在150x150下，为mdpi密度生成100x100的，
给ldpi设备生成75x75的。

然后，把这些文件放在对应的drawable资源目录下：

MyProject/
    res/
        drawable-xhdpi/
            awesomeimage.png
        drawable-hdpi/
            awesomeimage.png
        drawable-mdpi/
            awesomeimage.png
        drawable-ldpi/
            awesomeimage.png

在你引用@drawable/awesomeimage的时候，系统就会基于屏幕密度选择合适的位图了。
>注释：低密度（ldpi）资源不总是需要的。当你提供了hdpi的资源时，系统会将资源缩小一半正好适配了ldpi的屏幕。

更多关于为应用创建图标资源的提示和指南，请看 图标设计（http://developer.android.com/design/style/iconography.html）。

