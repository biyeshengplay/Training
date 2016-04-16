#构建一个流畅的UI
当考虑应用的跨多种屏幕尺寸的兼容性时，你可以在不同的布局中重用fragment以基于可获得的屏幕空间优化用户体验。

例如在手持设备上，一次只显示一个单面板的用户界面即一个fragment可能是比较合适的。相反在具有更宽的屏幕尺寸的平板
上，你可能想让fragment逐个排布来显示更多信息给用户。
[fragments-screen-mock.png]

图1.同一个activity上的在不同屏幕上根据不同的配置显示出来的两个Fragment。在大屏上，两个fragment并排正好够显示，
但是在手持设备上，一次只能够显示一个fragment所以当用户浏览时fragment必须要互相替换。

为了创建动态的体验FragmentManager类提供了允许你在运行时给activity添加、移除、替换fragment的方法。

##在运行时给activity添加fragment
与在activity的布局文件中定义fragment（正如上节课展示的那样使用<framgment>标签）不同，你能够在activity运行时
给activity添加fragment。如果你想在activity的生命周期中修改fragment，那这个动态的添加是很必要的。

为了执行类似添加移除fragment的事务，你必须使用FragmentManager来创建一个FragmentTransaction,它提供了
添加、移除、替换和执行其他fragment事务的api.

如果你的activity允许fragment被移除和被替换，你应该在activity的oncreate()方法里给activity添加初始的
fragment。

处理fragment时需要一个重要的角色-尤其在运行时添加fragment时-是你的activity布局必须包含的一个你能往里插入
fragment的view容器。

下面的布局是上一节课一次只显示一个fragment的布局的可替代版本。为了让fragment可以相互替换，activity的布局文件
包含了一个空的framelayout来扮演fragment容器的角色。

注意这个文件名和上一节课的布局文件名相同，但是布局目录没有large修饰符，所以这个布局会在比large小的屏幕设备上
使用因为这个屏幕不能同时放下两个fragment。

res/layout/news_articles.xml:

<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/fragment_container"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />

在activity中，调用Support库的API getSupportFragmentManager()来获得FragmentManager。然后调用
beginTransaction()来创建一个FragmentTransaction，调用add()来添加fragment。

你可以使用同一个FragmentTransaction执行多个fragment事务。当你准备让这些事务生效时要调用 commit()。

例如，这里展示了怎样在前面的布局中添加一个fragment：

import android.os.Bundle;
import android.support.v4.app.FragmentActivity;

public class MainActivity extends FragmentActivity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.news_articles);

        //检查activity正在使用带有名为fragment_container的framelayout的布局版本
        if (findViewById(R.id.fragment_container) != null) {

            //然而，如果我们正在从一个之前的状态恢复，那么我不需要做任何事直接返回
            //否则会和之前的fragment重叠
            if (savedInstanceState != null) {
                return;
            }

            //创建一个新的fragment放在activity的布局中
            HeadlinesFragment firstFragment = new HeadlinesFragment();

            //假如这个activity是带着intent的一些特殊说明被启动的，那么把intent的extras当做参数传给fragment
            firstFragment.setArguments(getIntent().getExtras());

            // 添加fragment到名为'fragment_container' 的FrameLayout中
            getSupportFragmentManager().beginTransaction()
                    .add(R.id.fragment_container, firstFragment).commit();
        }
    }
}

因为这个fragment已经在运行时被添加到FrameLayout容器中-不同于在布局中使用<fragment>标签来定义-activity能够
移除这个fragment或者用不同的fragment来替换它。

##用另一个fragment来替换它
替换一个fragment的过程和添加fragment很相似，只是请求的是replace()方法而不是add()方法。

记住当你执行fragment事务时，例如替换或者移除一个fragment，通常允许用户可以导航返回撤销行为是比较合适的。
为了允许用户能够导航返回，你必须在提交FragmentTransaction前调用addToBackStack()方法。

注释：当你移除或者替换一个fragment然后把这个事务添加到返回栈时，移除的fragment进入stop状态（不是destroy状态）。
如果用户浏览返回来恢复这个fragment，fragment会重新启动。如果你没有把这个事务添加进返回栈中，那么当fragment
被移除或者替换时是被销毁的（destroyed）.

这时用fragment替换的例子：

//创建了fragment，传递给它一个参数指定了它应该显示的文章内容
ArticleFragment newFragment = new ArticleFragment();
Bundle args = new Bundle();
args.putInt(ArticleFragment.ARG_POSITION, position);
newFragment.setArguments(args);

FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

//用这个fragment替换 fragment_container视图中的任何内容，然后添加这个事务进返回栈以便用户可以导航返回
transaction.replace(R.id.fragment_container, newFragment);
transaction.addToBackStack(null);

// 提交事务
transaction.commit();
addToBackStack()提供了一个可选的字符串类型的参数，它给这个事务指定了一个独一无二的名字。这个名字一般是不需要
的除非你计划使用FragmentManager的BackStackEntry api（http://developer.android.com/reference/android/support/v4/app/FragmentManager.BackStackEntry.html）
来执行高级的fragment操作。