#和其他fragment通信
为了重用fragment UI组件，你应该把每一个fragment都构建成可以完全独立模块化的组件，让fragment定义
自己的布局和行为。一旦你已经定义这些可重用的fragment，你可以通过activity建立他们的关联，按照应用
的逻辑连接他们，以实现整体的复合UI.

通常你会想让一个fragment和另一个fragment通信，例如给予一个用户事件来改变内容。所有的fragment对fragment
的通信都是通过关联的activity来完成的。两个fragment是不可能直接通信的。

##定义一个接口
为了让fragment向上和它的activity通信，你可以在fragment中定义一个接口然后在activity中实现它。fragment
在它的onattach()生命周期方法中获取到接口的实现者，然后fragment可以调用接口方法来实现和activity的通信。

这是一个fragment和activity通信的例子：

public class HeadlinesFragment extends ListFragment {
    OnHeadlineSelectedListener mCallback;

    // fragment的容器 Activity 必须实现这个接口
    public interface OnHeadlineSelectedListener {
        public void onArticleSelected(int position);
    }

    @Override
    public void onAttach(Activity activity) {
        super.onAttach(activity);

        //这里保证了容器activity已经实现了回调接口，如果没有回抛出异常
        try {
            mCallback = (OnHeadlineSelectedListener) activity;
        } catch (ClassCastException e) {
            throw new ClassCastException(activity.toString()
                    + " must implement OnHeadlineSelectedListener");
        }
    }

    ...
}
现在fragment可以通过调用OnHeadlineSelectedListener接口的实例mCallback的onArticleSelected()方法
（或者其他的接口中的方法）来给activity传递信息了。

例如，下面的fragment中的方法会在用户点击列表条目时被调用。fragment使用回调接口把事件传递给父activity。

    @Override
    public void onListItemClick(ListView l, View v, int position, long id) {
        // 把这个事件发送给宿主activity
        mCallback.onArticleSelected(position);
    }
##实现这个接口
为了接收来自fragment的事件回调，宿主的activity必须实现在fragment类中定义的接口。

例如，下面的activity实现了上面例子中的接口。

public static class MainActivity extends Activity
        implements HeadlinesFragment.OnHeadlineSelectedListener{
    ...

    public void onArticleSelected(int position) {
        //用户从HeadlinesFragment中选择了文章的标题
        //在这里要做一些操作来显示那篇用户选择的文章
    }
}
##传递一个消息给fragment
宿主activity可以通过findFragmentById()获取fragment的实例来传递消息给fragment，然后可以直接调用
fragment的公有方法。

例如，想象上面显示的activity可以包含另一个fragment，这个fragment用来显示在上面的回调方法中返回的数据详情。
在这个例子中，activity可以把在上面的回调中收到的信息传递给另一个将要显示这个信息的fragment：


public static class MainActivity extends Activity
        implements HeadlinesFragment.OnHeadlineSelectedListener{
    ...

    public void onArticleSelected(int position) {
        //用户从HeadlinesFragment中选择了文章的标题
        //在这里要做一些操作来显示那篇用户选择的文章

        ArticleFragment articleFrag = (ArticleFragment)
                getSupportFragmentManager().findFragmentById(R.id.article_fragment);

        if (articleFrag != null) {
            //如果articleFrag可见，证明我们正在有两个窗格的布局中
            //调用ArticleFragment中的方法来更新它显示的内容
            articleFrag.updateArticleView(position);
        } else {
            //否则，我们正在具有一个窗格的布局中，然后必须要切换fragment
            //创建一个fragment然后把选择的文章作为参数传给fragment
            ArticleFragment newFragment = new ArticleFragment();
            Bundle args = new Bundle();
            args.putInt(ArticleFragment.ARG_POSITION, position);
            newFragment.setArguments(args);

            FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

           //用这个fragment替换 fragment_container视图中的任何内容，
           //然后添加这个事务进返回栈以便用户可以导航返回
            transaction.replace(R.id.fragment_container, newFragment);
            transaction.addToBackStack(null);

            //提交事务
            transaction.commit();
        }
    }
}