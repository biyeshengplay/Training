#启动另一个activity
完成之前的课程后，你已经有了一个能够显示带有一个文本域和一个按钮的的activity了（单屏）。
在这节课你将添加一些代码让应用能够在用户点击发送按钮时启动一个新的activity。
##响应发送按钮
1.在 Android Studio  res/layout目录下,编辑content_my.xml 文件.
2.给Button标签添加android:onClick属性。
res/layout/content_my.xml
<Button
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/button_send"
    android:onClick="sendMessage" />

android:onClick属性的值:sendMessage是activity中的一个方法的名字,当用户点击这个按钮时候,系统就会调用这个方法.
3.在 java/com.mycompany.myfirstapp 目录下, 打开 MyActivity.java 文件.
4.在MyActivity 类里,添加 sendMessage() 方法,如下:
java/com.mycompany.myfirstapp/MyActivity.java
/** 当用户点击发送按钮时被调用 */
public void sendMessage(View view) {
    // Do something in response to button
}
为了让系统把这个方法和在android:onClick属性提供的名字匹配起来,方法必须完全如上面显示的那样.具体的,这个方法必须:
-是公开方法(public)
-返回值为void
-只有一个view作为参数(这个view就是那个被点击的view)
下一步,你将补全这个方法的功能,用于读取文本域的内容并且把文本传递给另一个activity.

## 构建一个intent
 1.在 MyActivity.java类 的sendMessage()方法里, 用下面的代码来创建一个intent启动一个叫DisplayMessageActivity的activity:
 java/com.mycompany.myfirstapp/MyActivity.java
 public void sendMessage(View view) {
   Intent intent = new Intent(this, DisplayMessageActivity.class);
 }
 
 >注释:如果你使用的是类似Android studio这样的ide,这个对 DisplayMessageActivity的引用将会引发一个报错,因为这个类还不存在.
  现在先忽略这个报错;你将在后面的课程里创建它.
 >intent:intent是一个能够提供相互独立的组件(例如两个activity)之间的运行时绑定的对象.
  intent代表了一个应用的"想去做某件事"的意图.你可以使用intent做各种各样的任务,但是他们最经常被用于启动另一个activity.
  想了解更多信息,查看intent和 intent filters(http://developer.android.com/guide/components/intents-filters.html).
  
  这里使用的构造方法有两个参数:
 - 第一个参数是一个Context(使用this是因为Activity类是Context的子类)
 - 第二个参数是一个应用程序组件的类名,系统将会把intent传递给这个类(在这个例子中,这个activity将会被启动).
  Android Studio提示你必须引入Intent 类.
 2.在这个文件的上部,引入Intent 类:
 java/com.mycompany.myfirstapp/MyActivity.java
 import android.content.Intent;
 
 >小贴士: 在Android Studio中, 按 Alt + Enter (mac系统上按option + return) 键来引入缺少的类.
 
 3.在sendMessage() 方法里, 使用 findViewById()获得EditText控件.
 java/com.mycompany.myfirstapp/MyActivity.java
 public void sendMessage(View view) {
   Intent intent = new Intent(this, DisplayMessageActivity.class);
   EditText editText = (EditText) findViewById(R.id.edit_message);
 }
 4.在这个文件的上部,引入Edittext 类.
  在Android Studio中, 按 Alt + Enter (mac系统上按option + return) 键来引入缺少的类.
 5.把text赋给一个局部变量message,然后使用putExtra()方法将text值添加到intent中.
 java/com.mycompany.myfirstapp/MyActivity.java
 public void sendMessage(View view) {
   Intent intent = new Intent(this, DisplayMessageActivity.class);
   EditText editText = (EditText) findViewById(R.id.edit_message);
   String message = editText.getText().toString();
   intent.putExtra(EXTRA_MESSAGE, message);
 }
 一个intent可以携带名叫extras的数据,这种数据类型类似键-值对.putExtra()方法第一个参数为键名,第二个参数存值.
 
 6.在MyActivity 类的上面添加EXTRA_MESSAGE的声明,如下:
 java/com.mycompany.myfirstapp/MyActivity.java
 
 ```
 public class MyActivity extends AppCompatActivity {
     public final static String EXTRA_MESSAGE = "com.mycompany.myfirstapp.MESSAGE";
     ...
 }
 ```
 
 为了让下一个activity查询extra数据,你需要使用共有常亮来为intent的extar定义一个key.
 使用应用包名作为前缀来定义extra的key通常是一个很好的做法,这确保了在你的应用和其他应用进行交互的情况下,你的key是独一无二的.
 
7.在 sendMessage() 方法里, 调用 startActivity() 方法,传递在第一步创建的intent对象,intent创建完成.
在新代码中,被发送按钮调用的完整的sendMessage() 方法代码将如下:
java/com.mycompany.myfirstapp/MyActivity.java
```
/** 当用户点击发送按钮是被调用 */
public void sendMessage(View view) {
    Intent intent = new Intent(this, DisplayMessageActivity.class);
    EditText editText = (EditText) findViewById(R.id.edit_message);
    String message = editText.getText().toString();
    intent.putExtra(EXTRA_MESSAGE, message);
    startActivity(intent);
}
```

系统接收到这个调用然后启动intent指定的activity的一个实例.现在为了让代码能够运行你需要创建DisplayMessageActivity类.

## 创建第二个activity
所有activity的子类必须实现onCreate()方法.这个方法是activity接收带有信息的intent的地方,而且oncreate方法里必须要通过setContentView()
方法声明activity的布局文件,这是activity展示activity组件初始配置的地方.
### 使用Android studio创建一个新的activity
当你创建一个新的activity时,Android studio包含了一个oncreate()方法的存根.创建新的activity窗口出现.
1.在Android Studio中java 目录里,选中包名com.mycompany.myfirstapp,右键,选择 New > Activity > Blank Activity.
2.在 Choose options 窗口中, 填写 activity细节:
- Activity Name(activity名称): DisplayMessageActivity
- Layout Name(布局名称): activity_display_message
- Title(标题): My Message
- Hierarchical Parent(父界面): com.mycompany.myfirstapp.MyActivity
- Package name(包名): com.mycompany.myfirstapp
点击完成.
3.打开DisplayMessageActivity.java文件.
 这个类已经包含了一个必要的Oncreate方法的实现.之后你将会修改这个实现.
 
 如果你正在使用Android Studio开发, 你现在就可以运行应用了,但是将不会发生什么情况.
 点击发送按钮开启第二个activity,但是第二个activiyt使用的是模板提供的默认的"Hello World"的布局文件.
 一会你将修改这个activity,改成显示出一个可以定制的文本视图.
 
 ###不通过Android studio创建新的activity
 如果你正在使用不同IDE或者使用命令行,按照下面的步骤操作:
 1.在工程的src/目录下创建一个新的名为DisplayMessageActivity.java的文件, 它将存放在之前的MainActivity.java文件的旁边.
 2.在文件中添加如下代码:
 ```
  public class DisplayMessageActivity extends AppCompatActivity {
     @Override
     protected void onCreate(Bundle savedInstanceState) {
         super.onCreate(savedInstanceState);
         setContentView(R.layout.activity_display_message);
         if (savedInstanceState == null) {
             getSupportFragmentManager().beginTransaction()
                 .add(R.id.container, new PlaceholderFragment()).commit();
         }
     }
     @Override
     public boolean onOptionsItemSelected(MenuItem item) {
         // 在这里处理应用栏项目的点击事件Handle app bar item clicks here. The app bar
         // 只要你在AndroidManifest.xml中指定父activity,应用栏就会自动处理返回键的点击.
         int id = item.getItemId();
         if (id == R.id.action_settings) {
             return true;
         }
         return super.onOptionsItemSelected(item);
     }
 
     /**
      * 包含了一个简单view的Fragment
      */
     public static class PlaceholderFragment extends Fragment {
 
         public PlaceholderFragment() { }
 
         @Override
         public View onCreateView(LayoutInflater inflater, ViewGroup container,
                   Bundle savedInstanceState) {
               View rootView = inflater.inflate(R.layout.fragment_display_message,
                       container, false);
               return rootView;
         }
     }
 }
 ```
 >注释:如果你正在使用 Android Studio以外的IDE,你的工程将不会包含这个被setContentView()请求的 activity_display_message布局文件.
   这是不要紧的因为之后你将修改这个方法并且将不会再用到这个布局文件.
3.在你的strings.xml 文件里,添加如下的新activity的标题:
<resources>
    ...
    <string name="title_activity_display_message">My Message</string>
</resources>
4.在manifest文件 AndroidManifest.xml中,在 Application 标签里, 为DisplayMessageActivity类添加 <activity> 标签,如下:
<application ... >
    ...
    <activity
        android:name="com.mycompany.myfirstapp.DisplayMessageActivity"
        android:label="@string/title_activity_display_message"
        android:parentActivityName="com.mycompany.myfirstapp.MyActivity" >
        <meta-data
            android:name="android.support.PARENT_ACTIVITY"
            android:value="com.mycompany.myfirstapp.MyActivity" />
    </activity>
</application>

android:parentActivityName 属性声明了在应用逻辑层的activity的父activity的名字.
系统使用这个值来实现默认的导航行为,例如在Android 4.1及以上的版本提供向上的导航功能.
你也可以像这里展示的一样,通过使用support库和添加meta-data标签来在低版本上提供相同的导航行为.

>注释:你的Android SDK应该已经包含了最新的Android support 库,你在添加Android SDK 软件包的步骤中安装了它.
当在Android studio中使用模板时,support库被自动添加到应用项目中(你可以在Android Dependencies下看到库的jar文件).
如果你没有使用Android studio,你需要手动添加库到你的项目中---按照安装support库的指南做,然后再回来这里继续下面的步骤.

如果你在使用 Android Studio外的IDE,不要担心你的应用还不能编译.一会将修改这个activity显示一个自定义的文本视图.

## 接收Intent
任何一个activity都可以被一个intent调用,不管用户是怎样导航到那的.你可以通过调用getIntent()方法获取到启动activity的Intent
并且获取保存在intent中的数据.
1.在res/layout 目录里, 编辑content_display_message.xml 文件.
2.给RelativeLayout添加android:id属性.你需要这个属性从代码引用到这个对象. 
< RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
...
android:id="@+id/content">
</RelativeLayout>
3.切换回编辑DisplayMessageActivity.java.
4.在onCreate() 方法中创建一个TextView对象.
  TextView textView = new TextView(this);
5.设置文本大小和使用setText()设置文本信息.
textView.setTextSize(40);
textView.setText(message);
6.把textview添加到通过R.id.content标记出的RelativeLayout中.
RelativeLayout layout = (RelativeLayout) findViewById(R.id.content);
layout.addView(textView);
7.在文件的上面,引入TextView类.
在Android Studio中, 按 Alt + Enter (mac系统上按option + return) 键来引入缺少的类.
完成的onCreate()方法将如下:
```
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   setContentView(R.layout.activity_display_message);
   Toolbar toolbar = (Toolbar) findViewById(R.id.toolbar);
   setSupportActionBar(toolbar);

   FloatingActionButton fab = (FloatingActionButton) findViewById(R.id.fab);
   fab.setOnClickListener(new View.OnClickListener() {
       @Override
       public void onClick(View view) {
           Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
                   .setAction("Action", null)
                   .show();
       }
   });
   getSupportActionBar().setDisplayHomeAsUpEnabled(true);

   Intent intent = getIntent();
   String message = intent.getStringExtra(MyActivity.EXTRA_MESSAGE);
   TextView textView = new TextView(this);
   textView.setTextSize(40);
   textView.setText(message);

   RelativeLayout layout = (RelativeLayout) findViewById(R.id.content);
   layout.addView(textView);
```

你现在可以运行应用了.当应用打开后,在文本域里输入一个消息,点击发送.
第二个activity会代替第一个显示在屏幕上,显示着你在第一个activity中输入了的信息.

就酱,你已经建立了你的第一个应用!

想了解更多内容,请继续学习下面的课程.