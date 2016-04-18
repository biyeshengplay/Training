# 保存文件
Android使用的文件系统和其他基于磁盘的平台的文件系统相似。本课程描述了怎样使用File API操作Android的文件系统来读写文件。

File对象适合用于读写规模较大的数据，这些数据应该保证有序而不会来回跳跃地读取。例如，对于图片文件或者其他的通过网络交换的数据使用
文件是很好的选择。

本课程教你怎样在你的应用中执行基本的文件相关的任务。课程前提是你已经对基本的Linux文件系统和java.io中标准的文件输入输出api
有了解了。

## 选择内部存储或者外部存储
所有的Android设备有两个文件存储区域：“内部”和“外部”存储。这两个名字来自于早期的Android，那个时候大多数设备提供内置的非易失
性的存储（内部存储）,和一个可移除的存储介质例如一个micro sd卡（外部存储）。一些设备会把永久存储分成“内部”和“外部”两个分区，
尽管没有可移除的存储介质也总是有两个存储空间，而且无论外部存储是否可移除，api行为是完全相同的。下面的列表总结了每种存储空间的情况。


内部存储:

- 总是可获得的
- 保存在这的文件默认只能被你的应用访问。
- 当用户卸载了你的应用，系统会移除内部存储的所有有关你应用的文件。

内部存储是你保证用户和其他应用都不能访问你的文件的最好方法。

外部存储：
- 它不总是可获得的，因为用户可以挂载外部存储为USB存储，在一些情况下也会从设备中移除。
- 它是 world-readable（全部可读）的，所以保存在这的文件可能会在你的控制之外被读取。
- 当用户卸载你的应用时，系统只移除使用getExternalFilesDir()获得的目录中的应用文件。
 
外部存储在不要求访问权限、想和其他应用分享文件或者允许用户通过电脑访问时是最好的存储位置。

提示：尽管应用默认被安装在内部存储，你可以在manifest中指定Android：installLocation属性让你的应用可以安装在外部存储上。
当安装包比较大并且外部存储比内部存储更大时这个选项会更好。

## 获取外部存储的权限
为了写入外部存储，你必须在manifest文件中申请WRITE_EXTERNAL_STORAGE权限。

<manifest ...>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    ...
</manifest>

注意：目前，所有的应用在没指定权限时都有读取外部存储的能力。然而这个可能会在之后的版本中改变。如果你的应用需要读取外部存储（但是
不向外存中写入），你需要声明READ_EXTERNAL_STORAGE权限。为了保证你的应用可以继续有效工作，你应该在那个改变生效前声明这个
权限。
<manifest ...>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    ...
</manifest>

然而，如果你的应用使用了WRITE_EXTERNAL_STORAGE权限，那么它就隐式具有了读取外部存储的权限。
保存文件到内部存储不需要任何权限，你的应用总是有对应用的内部存储目录的读写权限。

## 保存文件到内部存储
当保存文件到内部存储时，你可以通过调用下面两个方法中的一个来获得合适的文件目录：

 - getFilesDir()
   返回你的应用的内部存储的目录
 - getCacheDir()
   返回一个用于app临时缓存文件的目录。务必要在文件不再需要时删除每一个文件，你需要实现一个合理的存储大小范围给你的内存，例如
   1MB。如果系统的运行处在存储很低的情况下，可能会没有任何警告的条件下删除你的缓存文件。

为了在上面这些目录中创建一个新的文件，你可以使用File（）构造函数，给它传递一个由上面的指定内部存储目录的方法提供的文件名。例如：


File file = new File(context.getFilesDir(), filename);

另外，你可以调用openFileOutput()来获得一个FileOutputStream来写文件到内部目录中。例如，这是个写入一些文本到文件中的方法：

String filename = "myfile";
String string = "Hello world!";
FileOutputStream outputStream;

try {
  outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
  outputStream.write(string.getBytes());
  outputStream.close();
} catch (Exception e) {
  e.printStackTrace();
}

或者，如果你需要缓存一些文件，你应该取而代之使用createTempFile()方法。例如，下面的方法从URL中提取文件名，然后用这个名字在
应用的内部存储中创建文件。

public File getTempFile(Context context, String url) {
    File file;
    try {
        String fileName = Uri.parse(url).getLastPathSegment();
        file = File.createTempFile(fileName, null, context.getCacheDir());
    catch (IOException e) {
        // 当创建文件报Error
    }
    return file;
}

注释：你的应用的内部存储目录存储在Android文件系统的特定位置并由你的应用的包名来指定。技术上说，如果你把文件的模式设置为可读，
那么其他的应用是可以访问你的内部存储文件的。然而，其他的应用也需要知道你应用的包名和文件名。其他的应用不能够浏览你的内部目录也没有
读写权限除非你明确的指定了文件可读或可写。所以只要你使用了MODE_PRIVATE修饰了内部存储中的文件，他们就不可能会被其他应用访问。

## 保存文件到外部存储
因为外部存储可能是不可用的-例如当用户已经把存储挂载到pc上或者已经移除了提供外部存储的SD卡-你应该总是在访问外部存储之前核对磁盘卷
是否可用。你可以通过调用getExternalStorageState()方法来查询外部存储的状态。如果它返回的状态是MEDIA_MOUNTED，那么你能够
对文件做读写了。例如，下面的方法可用于证明存储的可获得性：

/* 检查外存是否可用于读写*/
public boolean isExternalStorageWritable() {
    String state = Environment.getExternalStorageState();
    if (Environment.MEDIA_MOUNTED.equals(state)) {
        return true;
    }
    return false;
}

/* 检查外部存储是否可获得，至少是可读权限 */
public boolean isExternalStorageReadable() {
    String state = Environment.getExternalStorageState();
    if (Environment.MEDIA_MOUNTED.equals(state) ||
        Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
        return true;
    }
    return false;
}

尽管外存可以被用户和其他应用修改，这有两类文件你可以在这里保存：

 - 共有文件
   应该被其他应用和用户自由获取的文件。当用户卸载你的应用时，这些文件应该继续保持对用户的可用。例如，使用你的应用拍摄的照片
   或者其他下载的文件。

 - 私有文件
   理所应当地属于你的应用的文件，在用户卸载应用时这些文件应该被删除。尽管这些文件在技术上说可以被用户和其他应用访问因为他们存储
   在外存，但是他们会真实地不会给除你的应用之外的用户提供文件内容。当用户卸载你的应用，系统会删除在你的应用的外部私有目录中的
   所有文件。例如，通过你的应用被下载的其他的资源或者临时的媒体文件。

如果你想要保存共有文件到外部存储中，使用getExternalStoragePublicDirectory()方法获得一个代表外部存储的相应目录文件。
这个方法带了一个参数用来指定你想要保存成的文件类型以便和其他的共有文件一起被合乎逻辑的组织起来，例如音乐目录或者图片目录。
例如：

public File getAlbumStorageDir(String albumName) {
    // 得到用户的共有的图片目录
    File file = new File(Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_PICTURES), albumName);
    if (!file.mkdirs()) {
        Log.e(LOG_TAG, "Directory not created");
    }
    return file;
}

如果你想保存私有文件给你的应用，你可以通过调用getExternalFilesDir()来获得合适的目录，给方法传递一个名字表明你想要的目录类型。
每一个用这种方法创建的目录会被添加到一个封装了所有应用的外存文件的目录中，在用户卸载你的应用时系统就会删除这些文件。

例如，这就是你可以用来给独立的相册创建目录的方法：

public File getAlbumStorageDir(Context context, String albumName) {
    // 获得应用的私有图片目录
    File file = new File(context.getExternalFilesDir(
            Environment.DIRECTORY_PICTURES), albumName);
    if (!file.mkdirs()) {
        Log.e(LOG_TAG, "Directory not created");
    }
    return file;
}
如果没有预定义的子目录名字能够和你的名字匹配，你可以调用getExternalFilesDir()并且传null为参数来代替。这会返回你应用在外存的私有
目录的根目录。

记住getExternalFilesDir()创建的目录在一个当用户卸载你的应用时会被删除的目录中。如果你要保存的文件需要在用户卸载你的应用
后保持可获得的状态---例如当你的应用是一个相机时用户会将要保留那些照片---你应该取而代之使用getExternalStoragePublicDirectory()
方法。

不管你是使用getExternalStoragePublicDirectory()方法来获得可共享的文件还是getExternalFilesDir() 获得对于你的应用
是私有的文件，使用提供了类似DIRECTORY_PICTURES的API常量的目录是重要的。这样的目录名字确保了文件会被系统合理的对待。例如，
保存为DIRECTORY_RINGTONES类型的文件会被系统媒体扫描，被视为铃声而不是音乐。

## 查询可用空间
如果你能提前知道你要保存的数据是多少，你可以通过调用getFreeSpace() 或者 getTotalSpace()方法得知是否有充足的存储空间
可获得而不是引起IO异常。这两个方法分别提供了当前可获得的空间和存储盘卷的总空间。这个信息也可以有效避免填充存储盘卷超过某个
特定阈值。

然而，系统不保证你可以写入和getFreeSpace()表明的一样多的byte.如果返回的数量比你想要保存的数据多几个MB,或者文件系统的充满
成都少于90%，那么继续处理是比较安全的。否则，你不应该写入存储。

注释：不要求你在保存文件前检查可获得空间的大小。你可以作为替代尝试立即写文件，然后当异常发生时捕获异常。如果你不明确的知道
你需要多少空间时你可能需要这样做。例如，在保存文件之前你把png图片转换成jpeg改变了文件的编码,你在保存之前将不会知道文件的
大小。

## 删除一个文件
你总是应该在文件不再被需要时删除它。删除一个文件最直接的方法是让打开的文件的引用自己调用delete（）方法。

myFile.delete();

如果文件被保存在内部存储中，你也可以让Context调用deleteFile()来定位删除文件。

myContext.deleteFile(fileName);

注释：当用户卸载你的应用时，Android系统会删除下面的文件：
 - 保存在内部存储的所有文件
 - 所有使用getExternalFilesDir()来保存的外部存储中的文件
 
 然而，你应该定期手动删除由getCacheDir()创建的所有缓存文件和其他你不再需要的文件。