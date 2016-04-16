# 创建一个fragment
你可以认为fragment是一个activity的模块的片段，一个拥有自己的生命周期、能够接收自己的输入事件、你可以在activity运行时
动态添加或移除（你可以在不同的activity上重复使用，有点像“子activity”）的片段。本课程教你怎样使用Support库扩展
Fragment类，这样你的应用可以保持对最老为Android1.6版本的设备的兼容。

在开始本课程之前，你必须先使用Support库来建立项目环境。如果你之前还没有使用过Support库，跟着Support库安装（
http://developer.android.com/tools/support-library/index.html） 文档的步骤引用v4库建立项目。
然而你也可以包括app bar在你的activity中取代使用v7兼容库，因为app bar兼容到Android2.1而且也包含Fragment api.

## 创建一个fragment类
为了创建一个fragment，你需要继承Fragment类，然后重写关键的生命周期方法来写入你的应用逻辑，这和对
Activity的使用方法很像。

有一个不同的地方是创建一个Fragment必须要使用onCreateView（）回调方法定义布局。事实上这是唯一一个用来
获得运行中Fragment的回调。例如，下面这个指定了布局的简单的Fragment:

import android.os.Bundle;
import android.support.v4.app.Fragment;
import android.view.LayoutInflater;
import android.view.ViewGroup;

public class ArticleFragment extends Fragment {
    @Override
    public View onCreateView(LayoutInflater inflater, ViewGroup container,
        Bundle savedInstanceState) {
        //为fragment加载布局文件
        return inflater.inflate(R.layout.article_view, container, false);
    }
}

就像activity一样，当fragment在activity中添加移出时、当activity在自己的生命周期之间切换时，fragment实现了其他的
生命周期回调来管理自己的状态。例如，当activity的onPause（）方法被调用，activity里的所有Fragment也会收到onPause（）
的调用。

更所关于Fragment生命周期和回调用法的信息可以在Fragments(http://developer.android.com/guide/components/fragments.html)
中找到。

##使用XML给Activity添加一个Fragment
虽然fragment是可重用的，模块化的UI组件，每一个fragment的实例必须和一个父类的fragmentActivity进行关联。
你可以通过在activity布局文件中定义每一个fragment来实现这种关联。

注释：FragmentActivity是Support库里提供的，它用来处理在Android版本小于11的系统上使用Fragment的问题。
如果你支持的最低系统版本是11或者更高，你可以使用普通的activity。

下面是一个在设备屏幕为large（目录名被large标识符指定的）时添加两个fragment给activity的布局文件示例。

res/layout-large/news_articles.xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="horizontal"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <fragment android:name="com.example.android.fragments.HeadlinesFragment"
              android:id="@+id/headlines_fragment"
              android:layout_weight="1"
              android:layout_width="0dp"
              android:layout_height="match_parent" />

    <fragment android:name="com.example.android.fragments.ArticleFragment"
              android:id="@+id/article_fragment"
              android:layout_weight="2"
              android:layout_width="0dp"
              android:layout_height="match_parent" />

</LinearLayout>

提示：了解更多关于为不同尺寸屏幕创建布局的内容，请看 支持不同屏幕尺寸（http://developer.android.com/training/multiscreen/screensizes.html）。

然后把布局文件应用到你的activity：

import android.os.Bundle;
import android.support.v4.app.FragmentActivity;

public class MainActivity extends FragmentActivity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.news_articles);
    }
}

如果你正在使用V7 appcompat库，你的activity应该继承AppCompatActivity，因为AppCompatActivity是FragmentActivity
的子类。了解更多内容请看 添加App Bar(http://developer.android.com/training/appbar/index.html).


注释：当你使用在xml布局文件中定义fragment的方法给activity添加fragment时，你不能够在运行时移除这个fragment。
如果你想在用户交互中让你的fragment可以进出切换，你必须在activity第一次启动时候添加这个fragment，这些将是下一节的内容。
