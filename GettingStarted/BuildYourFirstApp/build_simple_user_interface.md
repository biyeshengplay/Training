# 创建一个简单的用户界面
本节课将创建一个带有文本域和按钮的xml布局,在下一节课,将在你的应用添加按钮的点击事件,点击之后将会发送文本域的内容到另一个activity.

![图1](build_simple_interface_1.png "图1, 说明了viewgroup怎样构成了布局分支并包含了其他view对象")
android应用的图形用户界面是通过view的层次结构和viewgroup对象来构建的.view对象通常是类似按钮或者文本域的UI部件.
viewgroup对象是不可见的view容器,它定义了子view怎样排布,例如网格或者垂直的列表.

Android提供了一个xml词汇表对应view和viewgroup的子类,所以你可以在xml中使用ui元素的层级结构定义你的UI效果.


Layout是viewgroup的子类,在这个练习中,你将学习使用LinearLayout.

> 可替换的layout:因为一些原因在xml中声明UI布局比在运行代码中声明更有效,但是特别重要的是你可以为不同的屏幕尺寸创建不同的布局.
例如,例如,你可以创建两个版本的布局,并且告诉系统在小屏设备上使用其中一个版本的布局另一个在大屏设备上使用.想了解更多,查看[支持不同设备](http://developer.android.com/training/basics/supporting-devices/index.html).

## 创建一个LinearLayout
 1. 在Android studio中在res/layout目录里,打开content_my.xml 文件.创建工程时你选择的BlankActivity模板包含了这个含有一个RelativeLayout根view和一个textview子view的 content_my.xml文件.
 2. 在preview面板里,点击"隐藏"按钮来关闭preview面板.
     在Android studio中,当你打开一个布局文件,第一次会默认显示出preview面板.点击面板中的元素将会在设计面板中打开WYSIWYG工具.
     本课程里你将直接在xml中做操作.
 3. 删除 TextView元素.
 4. 改 RelativeLayout元素为LinearLayout
 5. 添加android:orientation 属性并且设值为 "horizontal"
 6. 移除android:padding 属性和tools:context属性.
 最终看起来就像这样:
 ```
 <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
     xmlns:app="http://schemas.android.com/apk/res-auto"
     xmlns:tools="http://schemas.android.com/tools"
     android:orientation="horizontal"
     android:layout_width="match_parent"
     android:layout_height="match_parent"
     app:layout_behavior="@string/appbar_scrolling_view_behavior"
     tools:showIn="@layout/activity_my">
  ```

  LinearLayout是一个view集合(是ViewGroup的子类),它可以以垂直或者水平方向排布子view,通过 android:orientation属性来说明.
  linearlayout的每一个子view都会按照它在xml中出现的顺序显示在屏幕上.

其他两个属性,android:layout_width 和 android:layout_height,用于明确控件的尺寸,对于所有的view来说都是需要的,

因为LinearLayout在这个布局中是根view,它需要通过设置width和height属性为"match_parent"来填满应用可获得的全部屏幕.
这个值声明了本元素应该扩展宽高来匹配的上父view的宽高.

想了解更多layout的属性,查看[Layout](http://developer.android.com/guide/topics/ui/declaring-layout.html)指南.

## 添加一个文本域
正如每一个视图对象一样,你必须定义确定的xml属性来明确EditText对象的属性.\
1. 在content_my.xml文件LinearLayou>元素里, 定义一个带有id属性设为@+id/edit_message的EditText元素.\
2. 设the layout_width 和 layout_height 属性为 wrap_content
3. 定义 hint 属性为一个叫edit_message的string对象.
EditText元素将如下:
```
<EditText android:id="@+id/edit_message"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:hint="@string/edit_message" />
```
下面是你添加到EditText中的属性:
- android:id
  它给你的view提供了唯一标识,你可以使用它在app代码中引用到这个对象.
  当你从xml中引用任何一个资源的时候,这个@符号都是需要的.它后面跟着资源类型(本例中是id),一个斜线,然后是资源名(edit_message).
  在资源类型前面的+号只在当你第一次定义一个资源Id的时候需要.当你编译应用时, the SDK tools用这个id名在gen/R.java文件中创建一个新的资源Id.
  这个id引用了edittext元素.通过这种方法声明了id之后,其他的对这个id的引用不需要+号,使用+号只在声明一个新资源id时候是必要的,
  并且对于像string或者layout这样具体的资源+号是不需要的.
- android:layout_width 和 android:layout_height
  取代了使用具体的宽高尺寸, "wrap_content"明确了这个view只和满足view中内容的大小一样大.
  如果你用"match_parent"替换它,那么 EditText元素将会填满屏幕,因为它讲匹配父亲LinearLayout的尺寸.
  想了解更多查看[布局](http://developer.android.com/guide/topics/ui/declaring-layout.html).

  >资源对象:一个资源对象是一个关联于一个app资源的唯一的整数型的名字,例如bitmap,布局文件或者string.
每一个资源都有一个对应的定义在gen/R.java文件中的资源对象.你可以使用R类文件中的对象名字来引用你的资源,
例如当你需要为android:hint属性明确一个string值的时候.你也可以创建任意的资源id来和使用android:id属性的view建立联系,
这个id允许你引用来自其他代码中的view.
sdk tools在你每次编译app时生成R.java文件,绝不可手工修改这个文件.
想了解更多,阅读[提供资源](http://developer.android.com/guide/topics/resources/providing-resources.html).

- android:hint
  这个是在文本域为空时用来显示默认字符串的.替代使用硬编码的string为值, "@string/edit_message"值
   引用了定义在另一个文件中的string资源.因为这引用到了一个具体的资源(不只是一个标识符),所以它不需要+号.
   然而,因为你还没有定义这个string资源,你将首先看到一个编译错误.你将在下一节修复这个错误通过定义这个string资源.

>注释:这个string资源和这个元素id拥有一样的名字: edit_message.然而资源的引用总是通过资源类型(例如id 和string)来划分作用域,
所以使用相同的名字不会导致冲突.

##添加字符串资源
默认情况下,Android工程会包括一个字符串资源文件叫做res/values/strings.xml.在这里你将添加一个新的字符串名字叫"edit_message",
 并且设值为"Enter a message."

1.在Android studio res/values目录下,打开 strings.xml文件.
2.为名为"edit_message",值为"Enter a message"的新字符串添加一行.
3.为名为"button_send",值为"Send"的新字符串添加一行.
  你将会在下一部分使用这个字符串创建一个按钮.
 strings.xml文件编辑后的结果就像这样:

 ```
 <?xml version="1.0" encoding="utf-8"?>
 <resources>
     <string name="app_name">My First App</string>
     <string name="edit_message">Enter a message</string>
     <string name="button_send">Send</string>
     <string name="action_settings">Settings</string>
 </resources>
 ```

 对于用户界面的文字，总是指定每个字符串作为一个资源。字符串资源让你在一个独立的位置管理起所有的UI文字,这会使文字更容易被查找和修改.
 外部化的字符串也允许通过提供对每个字符串的可替换的定义来实现应用对不同语言的本地化.

 想了解更多关于使用字符串资源实现对其他语言本地化的内容,请查看 支持不同设备(http://developer.android.com/training/basics/supporting-devices/index.html) 课程.

##添加一个按钮
在android studio res/layout目录下,编辑content_my.xml文件.
在Linearlayout标签里,紧跟在在Edittext标签后面定义一个button标签.
设置这个button的宽和高的属性为wrap_content,这样这个button就占用刚够显示button上文字的空间大小.
用android:text属性定义button的文字,设置button的值为你在上一节定义过的字符串资源button_send.

编辑后的LinearLayout标签代码如下:
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    tools:showIn="@layout/activity_my">
        <EditText android:id="@+id/edit_message"
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:hint="@string/edit_message" />
        <Button
          android:layout_width="wrap_content"
          android:layout_height="wrap_content"
          android:text="@string/button_send" />
</LinearLayout>

注释: 因为这个按钮不会在activity代码中引用,所以不需要android:id属性.
这个布局目前被设计为Editext和Button控件都是只占用够展示内容的空间大小,正如图2展示的样子.
The layout is currently designed so that both the EditText and Button widgets
 are only as big as necessary to fit their content, as Figure 2 shows.

图 2. 设置宽为"wrap_content"的edittext和button.

这种做法对button来说是很好的,但是对于文本框来说就没那么好了,因为用户可能输入更长的一些文字内容.
让没有利用上的空间给文本框来填满屏幕的宽度,这种方法可能会更好.这个可以通过在LinearLayout中指定layout_weight属性来实现.

weight值是一个数字,他指定了每一个view应该占用的剩余空间的数量.这个值也与view的兄弟view消耗的空间数量相关.这种操作类似于
在一个饮品食谱上写:2等份苏打,1等份的糖水,意味着这个饮品的2/3是苏打.例如,如果你给一个view赋值weight为2,另一个view赋weight为1,
总数是3,所以第一个view填充了剩余空间的2/3 第二个view填充了剩余的空间.如果你添加第三个view并且赋weight值为1,那么第一个weight值为2的view现在得到的是1/2的剩余空间,
剩下的两个view分别得到1/4.

对于所有view默认的weight值都为0,所以如果你只对一个view指定了任何一个大于0的数作为weight值,
那么在所有的view都被分配了他们所需的空间之后这个写了weight的view将会占满剩下的空间,无论剩下的空间是多少.

##让输入宽填满屏幕的宽
想要填满带有edittext标签的layout布局,需要做下面的操作:

1.在 content_my.xml 文件里,给edittext标签的layout_weight属性赋值为1.
2.同样给EditText标签的layout_width属性赋值为"0dp".
```
<EditText
    android:layout_weight="1"
    android:layout_width="0dp"
    ... />
```
为了提高布局的效率,当你指定这个weight值时,你应该将edittext的宽度改为0(0dp),设置宽度为0提高了布局的性能因为
如果使用wrap_content作为宽需要系统来计算一个最终还不相关的宽(因为weight值需要做另外一个宽度的计算).

图3显示了当你给edittect标签分配了全部的权重时的结果.
图3.edittext的weight被分配了所有的layout权重,所以他填满了LinearLayout的剩余空间.

现在你完成的content_my.xml布局文件应该像这样:
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   xmlns:app="http://schemas.android.com/apk/res-auto"
   xmlns:tools="http://schemas.android.com/tools"
   android:orientation="horizontal"
   android:layout_width="match_parent"
   android:layout_height="match_parent"
   app:layout_behavior="@string/appbar_scrolling_view_behavior"
   tools:showIn="@layout/activity_my">
    <EditText android:id="@+id/edit_message"
        android:layout_weight="1"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:hint="@string/edit_message" />
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/button_send" />
</LinearLayout>
```
#运行应用
这个布局是应用在当你创建项目时sdk tools生成的activity类中的,运行这个应用来查看结果:
1.在Android studio工具栏中点击Run.
2.或者在命令行里,切换目录到你到Android项目根目录下,执行:
```
$ ant debug
adb install -r app/build/outputs/apk/app-debug.apk
```

下一节课将继续学习怎样相应按钮的按压事件、从文本域中读取内容、启动另一个activity等。