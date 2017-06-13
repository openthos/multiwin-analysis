## 7.1多窗口功能的三种模式
  - 1.分屏模式
    - 该模式将屏幕一分为二，同时显示两个应用的界面
  - 2.画中画模式
    - 这种模式主要在TV上使用，在该模式下视频播放的窗口可以一直在最顶层显示
  - 3.Freeform模式
    - 这种模式类似于我们常见的桌面操作系统，应用界面的窗口可以自由拖动和修改大小
## Android从API Level24开始，配合多窗口功能的新增机制
  - 1.Manifest新增属性
    - android:resizeableActivity=["true" | "false"]
    这个属性可以用在<activity>或者<application> 上。置为true，表示可以以分屏或者Freeform模式启动。false表示不支持多窗口模式。对于API目标Level为24的应用来说，这个值默认是true。
  - 2.Layout新增属性
    - android:defaultWidth，android:defaultHeight Freeform模式下的默认宽度和高度
    - android:gravity Freeform模式下的初始Gravity
    - android:minWidth, android:minHeight 分屏和Freeform模式下的最小高度和宽度
  - 3.新增API
    - Activity.isInMultiWindowMode() 查询是否处于多窗口模式
    - Activity.isInPictureInPictureMode() 查询是否处于画中画模式
    - Activity.onMultiWindowModeChanged() 多窗口模式变化时进行通知(进入或退出多窗口)
    - Activity.onPictureInPictureModeChanged() 画中画模式变化时进行通知（进入或退出画中画模式）
    - Activity.enterPictureInPictureMode() 调用这个接口进入画中画模式，如果系统不支持，这个调用无效
    - ActivityOptions.setLaunchBounds() 在系统已经处于Freeform模式时，可以通过这个参数来控制新启动的Activity大小，如果系统不支持，这个调用无效

