# 高效显示Bitmap
学习怎样使用常见的技术来处理和加载bitmap对象来保证用户UI界面的可响应和避免超过应用的内存限制。如果不小心的使用bitmap会很快
消耗你可用的内存预算导致可胖的应用异常java.lang.OutofMemoryError: bitmap size exceeds VM budget而致崩溃。

这有一些导致在Android应用中加载bitmap要很严谨的原因：
- 移动设备技术上系统资源是有限制的。Android设备给每一个应用仅分配16MB可获得内存。Android就兼容定义文件（CDD）3.7节虚拟
设备兼容性一文中给出了各种屏幕尺寸和密度所需的最小应用内存。应用应该为了保证处在最小内存限制下做优化。然而，要知道很多设备被
配置为比最小限制高一些的内存。
- bitmap占用了大量内存，特别是对那些类似照片的丰富的图片。例如，在Galaxy Nexus上相机拍照高达2592x1936像素（五百万像素）。
如果bitmap的配置是ARGB_8888（在Android2.3及以上系统的默认配置）那么加载这张图片到内存中会占用大概19MB的内存(2592*1936*4 
比特)，立刻超过了一些设备上单app的内存限制。
- Android应用的UI频繁请求一些bitmap及时加载。例如ListView，GridView和ViewPager等组件通常包括一些在屏上的需要立马加载
的bitmap和更多在屏外的潜在的准备在用户手指滑动时显示的bitmap.


## 课程
- 有效加载较大的Bitmap
  将教你在没有超过单个应用内存限制的情况下解码较大的bitmap
- 在非UI线程处理Bitmap
  Bitmap 处理（调整大小，从远端数据源下载，等等）应该绝不会占用主UI线程。本课程教你使用AsyncTask在后台处理bitmap并且解释
   怎样处理并发问题。
- Bitmap缓存
  教你使用内存和磁盘bitmap缓存来提高加载大量bitmap时的响应速度和流畅性能。
- 管理 Bitmap 内存
  本课程将展示怎样管理bitmap内存来是应用的展现最优化。
- 在UI上显示bitmap
  这节课将所有的内容融合在一起，向你展示怎样使用后台线程和bitmap缓存在类似 ViewPager 和 GridView的组件上加载大量bitmap.