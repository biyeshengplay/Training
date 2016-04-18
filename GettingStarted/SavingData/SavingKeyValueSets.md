# 保存键值对集合
如果你有一些相对比较小的键值对集合想要保存，你应该使用SharedPreferences api.一个SharedPreferences对象指向一个存储
键值对的文件，它提供了简单的读写方法。每一个SharedPreferences文件由framework框架层管理并且可以是私有的或者共享的。

本节课会教你怎样使用SharedPreferences api存储和获取简单的值。

注释：SharedPreferences api只用于读写键值对，不能和Preference api混淆，Preference是用来帮助你为应用的设置创建
用户界面的（尽管他们使用了SharedPreferences作为他们保存应用设置的实现）。了解更多关于Preference的内容请看 设置
（http://developer.android.com/guide/topics/ui/settings.html）。

## 获得一个SharedPreferences的句柄
你可以通过调用这两个方法中的一个来创建一个新的shared preference文件或者进入一个已存在的shared preference文件：

- getSharedPreferences() — 如果你需要多个shared preference文件时使用这个方法，文件名字要在第一个参数上指定，
你可以从应用中任何一个Context调用它。
 
- getPreferences() — 如果对于activity只需要一个shared preference文件时使用这个。因为它获取的默认的shared preference
文件是属于这个activity的，你不需要提供名字。

例如，下面的代码在fragment中执行，它访问了由资源字符串R.string.preference_file_key标识的shared preferences文件，
并且使用私有模式打开文件这样文件只可以被你的应用访问。

Context context = getActivity();
SharedPreferences sharedPref = context.getSharedPreferences(
        getString(R.string.preference_file_key), Context.MODE_PRIVATE);

当给shared preference文件命名时，你应该使用可以唯一标识你的应用的名字，例如"com.example.myapp.PREFERENCE_FILE_KEY".


另外，如果你需要一个shared preference文件给你的activity，你可以使用getPreferences()方法:

SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);

注意：如果你用 MODE_WORLD_READABLE 或者 MODE_WORLD_WRITEABLE模式创建一个 shared preferences文件时，其他的知道
这个文件名字的应用可以访问你的数据。


## 向Shared Preferences中写数据
为了向shared preferences文件中写数据，在你的SharedPreferences对象上调用edit()方法来创建一个SharedPreferences.Editor
对象。

传递你想要写入的键和值给方法（例如putInt() 和 putString()方法）。然后调用commit()保存修改。例如：

SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
SharedPreferences.Editor editor = sharedPref.edit();
editor.putInt(getString(R.string.saved_high_score), newHighScore);
editor.commit();

## 从Shared Preferences中读取
为了获取shared preferences文件中的值，调用方法例如 getInt() 和 getString()，给方法提供你想要取得值的键，然后可选地提供
在键不存在时的默认值。例如：

SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
int defaultValue = getResources().getInteger(R.string.saved_high_score_default);
long highScore = sharedPref.getInt(getString(R.string.saved_high_score), defaultValue);