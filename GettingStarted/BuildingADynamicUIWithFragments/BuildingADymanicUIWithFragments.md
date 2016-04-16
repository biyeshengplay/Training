# 用fragment构建动态UI
为了创建一个动态的多窗格的用户界面，你需要将UI组件和activity行为包装成可以在activity上可进出替换的模块。
你可以使用Fragment类来实现这样的模块，fragment可以定义自己的布局和管理自己的声明周期，有点像一个嵌套的activity。

当一个fragment指定了自己的布局后，它可以在不同屏幕尺寸上和其他的fragment配置成不同的组合（在小屏上可能一次显示一个fragment，
但是在大屏上可以显示两个或者更多）。

本课程展示了怎样使用fragment创建一个动态的用户体验和为不同屏幕尺寸的设备优化用户体验，所有的将最老支持到Android1.6版本。

## 课程
- 创建一个fragment
  学习怎样构建一个fragment和在它的回调方法中实现基本的操作
  
- 构建一个流畅的UI
  学习怎样构建可以在不同屏幕上提供不同的fragment配置的布局文件
- 和其他的fragment通信
  学习怎样和activity中的其他fragment建立通信路径。