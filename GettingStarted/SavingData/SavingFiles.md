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

## Save a File on External Storage
Because the external storage may be unavailable—such as when the user has mounted the storage
 to a PC or has removed the SD card that provides the external storage—you should always verify that 
 the volume is available before accessing it. You can query the external storage state by 
 calling getExternalStorageState(). If the returned state is equal to MEDIA_MOUNTED, then you can
  read and write your files. For example, the following methods are useful to determine the storage 
  availability:

/* Checks if external storage is available for read and write */
public boolean isExternalStorageWritable() {
    String state = Environment.getExternalStorageState();
    if (Environment.MEDIA_MOUNTED.equals(state)) {
        return true;
    }
    return false;
}

/* Checks if external storage is available to at least read */
public boolean isExternalStorageReadable() {
    String state = Environment.getExternalStorageState();
    if (Environment.MEDIA_MOUNTED.equals(state) ||
        Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
        return true;
    }
    return false;
}
Although the external storage is modifiable by the user and other apps, there are two categories
 of files you might save here:

 - Public files
Files that should be freely available to other apps and to the user. When the user uninstalls your 
app, these files should remain available to the user.
For example, photos captured by your app or other downloaded files.

 - Private files
Files that rightfully belong to your app and should be deleted when the user uninstalls your app.
 Although these files are technically accessible by the user and other apps because they are on the
  external storage, they are files that realistically don't provide value to the user outside your app. 
  When the user uninstalls your app, the system deletes all files in your app's external private directory.
For example, additional resources downloaded by your app or temporary media files.

If you want to save public files on the external storage, use the getExternalStoragePublicDirectory()
 method to get a File representing the appropriate directory on the external storage. The method 
 takes an argument specifying the type of file you want to save so that they can be logically 
 organized with other public files, such as DIRECTORY_MUSIC or DIRECTORY_PICTURES. For example:

public File getAlbumStorageDir(String albumName) {
    // Get the directory for the user's public pictures directory. 
    File file = new File(Environment.getExternalStoragePublicDirectory(
            Environment.DIRECTORY_PICTURES), albumName);
    if (!file.mkdirs()) {
        Log.e(LOG_TAG, "Directory not created");
    }
    return file;
}
If you want to save files that are private to your app, you can acquire the appropriate directory
 by calling getExternalFilesDir() and passing it a name indicating the type of directory you'd like.
  Each directory created this way is added to a parent directory that encapsulates all your app's
   external storage files, which the system deletes when the user uninstalls your app.

For example, here's a method you can use to create a directory for an individual photo album:

public File getAlbumStorageDir(Context context, String albumName) {
    // Get the directory for the app's private pictures directory. 
    File file = new File(context.getExternalFilesDir(
            Environment.DIRECTORY_PICTURES), albumName);
    if (!file.mkdirs()) {
        Log.e(LOG_TAG, "Directory not created");
    }
    return file;
}
If none of the pre-defined sub-directory names suit your files, you can instead call
 getExternalFilesDir() and pass null. This returns the root directory for your app's private 
 directory on the external storage.

Remember that getExternalFilesDir() creates a directory inside a directory that is deleted when 
the user uninstalls your app. If the files you're saving should remain available after the user
 uninstalls your app—such as when your app is a camera and the user will want to keep the photos—you 
 should instead use getExternalStoragePublicDirectory().

Regardless of whether you use getExternalStoragePublicDirectory() for files that are shared or 
getExternalFilesDir() for files that are private to your app, it's important that you use directory 
names provided by API constants like DIRECTORY_PICTURES. These directory names ensure that the
 files are treated properly by the system. For instance, files saved in DIRECTORY_RINGTONES are 
 categorized by the system media scanner as ringtones instead of music.

Query Free Space
If you know ahead of time how much data you're saving, you can find out whether sufficient space
 is available without causing an IOException by calling getFreeSpace() or getTotalSpace(). These 
 methods provide the current available space and the total space in the storage volume, respectively.
  This information is also useful to avoid filling the storage volume above a certain threshold.

However, the system does not guarantee that you can write as many bytes as are indicated by 
getFreeSpace(). If the number returned is a few MB more than the size of the data you want to save,
 or if the file system is less than 90% full, then it's probably safe to proceed. Otherwise, 
 you probably shouldn't write to storage.

Note: You aren't required to check the amount of available space before you save your file.
 You can instead try writing the file right away, then catch an IOException if one occurs.
  You may need to do this if you don't know exactly how much space you need. For example, 
  if you change the file's encoding before you save it by converting a PNG image to JPEG, 
  you won't know the file's size beforehand.

## Delete a File
You should always delete files that you no longer need. The most straightforward way to delete 
a file is to have the opened file reference call delete() on itself.

myFile.delete();
If the file is saved on internal storage, you can also ask the Context to locate and delete a 
file by calling deleteFile():

myContext.deleteFile(fileName);
Note: When the user uninstalls your app, the Android system deletes the following:
 - All files you saved on internal storage
 - All files you saved on external storage using getExternalFilesDir().
However, you should manually delete all cached files created with getCacheDir() on a regular
 basis and also regularly delete other files you no longer need.