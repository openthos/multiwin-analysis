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
    
    这个属性可以用在activity或者application上。置为true，表示可以以分屏或者Freeform模式启动。false表示不支持多窗口模式。对于API目标Level为24的应用来说，这个值默认是true。
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

## 多窗口与Stack
  - 1.Android为了支持多窗口，在运行时创建了多个Stack，每个Stack会有一个唯一的Id，在ActivityManager.java中定义了这些Stack的Id：
    - /** First static stack ID. */
    
      public static final int FIRST_STATIC_STACK_ID = 0;
      
    - /** Home activity stack ID. */
    
      public static final int HOME_STACK_ID = FIRST_STATIC_STACK_ID;
      
    - /** ID of stack where fullscreen activities are normally launched into. */
      
      public static final int FULLSCREEN_WORKSPACE_STACK_ID = 1;
    
    - /** ID of stack where freeform/resized activities are normally launched into. */
    
      public static final int FREEFORM_WORKSPACE_STACK_ID = FULLSCREEN_WORKSPACE_STACK_ID + 1;
    
    - /** ID of stack that occupies a dedicated region of the screen. */
    
      public static final int DOCKED_STACK_ID = FREEFORM_WORKSPACE_STACK_ID + 1;
    
    - /** ID of stack that always on top (always visible) when it exist. */
    
      public static final int PINNED_STACK_ID = DOCKED_STACK_ID + 1;

