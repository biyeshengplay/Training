# 支持不同的语言
把UI字符串从代码中分离出去放在一个外部文件中总是一个很好的做法实践。
Android使用工程下resources目录让这种分离变得更容易。

如果你用SDK tools创建的工程（阅读 创建一个Android工程（http://developer.android.com/training/basics/firstapp/creating-project.html）),
tools会在工程的顶级目录创建一个res/目录。
在res目录下是各种各样资源类型的子目录。
里面也有很多的默认文件例如res/values/strings.xml,它存储了你的字符串值。

## 创建一个地区目录和string文件
为了添加对更多语言的支持，在res目录里创建另一个value目录，目录名后面追加连字符和ISO语言代码。
例如，values-es/ 是存储语言代码为“es”的区域的资源的目录。Android会在运行时根据设备的区域设置加载合适的资源，
可以查看 “提供可选择的资源”（http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources）.

当你决定你要支持的语言种类后，创建资源的子目录和string资源文件，例如：
MyProject/
    res/
       values/
           strings.xml
       values-es/
           strings.xml
       values-fr/
           strings.xml

为每一个区域添加字符串值到相应的文件里。
在运行时，Android系统会基于用户设备当前的区域设置使用相应的字符串资源。
例如，下面就是为支持不同语言添加的不同的字符串资源文件。

英语 (默认的区域配置), /values/strings.xml:
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="title">My Application</string>
    <string name="hello_world">Hello World!</string>
</resources>
西班牙语, /values-es/strings.xml:

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="title">Mi Aplicación</string>
    <string name="hello_world">Hola Mundo!</string>
</resources>
法语, /values-fr/strings.xml:

<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="title">Mon Application</string>
    <string name="hello_world">Bonjour le monde !</string>
</resources>
>注释：你可以在任何资源类型上使用区域化的修饰（或者任何配置修饰）,例如如果你想为你的位图的绘制提供本地化版本。
 了解更新信息请查看 本地化（http://developer.android.com/guide/topics/resources/localization.html）。

## 使用字符串资源
使用定义在string标签的名字属性可以引用字符串资源到你的源代码中或其他的xml文件中。
在你的源代码中，你可以使用语法R.string.<字符串名字>来引用字符串。
用这种方式有很多接收字符串资源的方法。
例如：

// 从应用资源中获得一个字符串
String hello = getResources().getString(R.string.hello_world);

//或者给需要一个字符串的方法提供一个字符串资源
TextView textView = new TextView(this);
textView.setText(R.string.hello_world);
在其他的xml文件中，当xml属性需要一个字符串值的时候，你都可以使用语法@string/<字符串名字>来引用字符串资源。

例如:

<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/hello_world" />
