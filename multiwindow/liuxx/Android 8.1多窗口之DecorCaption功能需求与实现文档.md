# Android 8.1多窗口之DecorCaption功能需求与实现文档

## 内容：
  - 功能需求
  - 设计实现
  - 重点问题
  
### 功能需求:
  - 原生Android8.0 DecorCaption上自带关闭和全屏功能，对比Openthos5.1需增加的功能如下:
    - 1.回退功能
    - 2.横竖屏切换功能
    - 3.最小化功能
    - 4.最大化功能
    - 5.双击DecorCaption在最大化与窗口化之间切换
    - 6.鼠标Hover控制DecorCaption的显示与隐藏
    - 7.在窗口全屏、Dock状态下，屏蔽横竖屏切换功能
    
### 设计实现：
  - 界面UI 位置：frameworks/base/core/res/res/layout/decor_caption.xml
  - 功能实现
    - 1.回退功能 位置：frameworks/base/core/java/com/android/internal/widget/DecorCaptionView.java
      - 方法实现：  
      -> DecorCaptionView.java: void keyBackWindow()<br />
      -> Activity.java: void pressKeyBack() //最终调用onBackPressed()实现回退
      
    - 2.横竖屏切换功能 位置：frameworks/base/core/java/com/android/internal/widget/DecorCaptionView.java
      - 方法实现:   
      -> DecorCaptionView.java: void rotateWindow()<br />
      -> Activity.java: void changeWindowOrientation()<br />
      -> ActivityManagerService.java: changeTaskOrientation(IBinder token)<br />
      -> TaskRecord.java: changeTaskOrientation(int taskId, Rect taskBounds)
    
    - 3.最大化功能 位置：frameworks/base/core/java/com/android/internal/widget/DecorCaptionView.java
      - 方法实现:    
      -> DecorCaptionView.java: void maximizeWindow()<br />
      -> Activity.java: switchWindowFreeformAndFullscreen()<br />
      -> ActivityManagerService.java: switchTaskFreeformAndFullscreen(IBinder token)<br />
      -> TaskRecord.java: reparent(int preferredStackId, boolean toTop, @ReparentMoveStackMode int moveStackMode,boolean, animate, boolean deferResume, String reason)<br />
      目前此功能实现的是全屏与窗口之间切换效果
    
    - 4.双击DecorCaption在最大化与窗口化之间切换 位置：frameworks/base/core/java/com/android/internal/widget/DecorCaptionView.java
      - 方法实现:    
      -> DecorCaptionView.java: onTouch(View v, MotionEvent e)<br />
          //在MotionEvent.ACTION_DOWN时通过判断两次点击时间间隔小于500毫秒实现最大化功能效果
  
    - 5.鼠标Hover控制DecorCaption的显示与隐藏: 位置: frameworks/base/core/java/com/android/internal/policy/DecorView.java
      - 方法实现:    
      -> DecorView.java: dispatchGenericMotionEvent(MotionEvent ev)<br />
          //只考虑全屏状态下，此功能实现<br />
          //全屏时在MotionEvent.ACTION_HOVER_MOVE状态下通过鼠标距离屏幕顶端的距离控制DecorCaption的显示和隐藏
          
    - 6.在窗口全屏、Dock状态下，屏蔽横竖屏切换功能 位置: frameworks/base/core/java/com/android/internal/widget/DecorCaptionView.java
      - 方法实现:    
        - 全屏：DecorCaptionView.java: onConfigurationChanged(boolean show)//当窗口全屏时会调用此方法，在此方法中通过判断当前窗口的StackId>1来控制横竖屏切换功能
        - 窗口Dock：
          - 判断窗口是否Dock：    
          -> View.java: isTaskDocked()<br />
          -> WindowManagerService.java: isTaskDocked(IWindow window)<br />
          -> Task.java: mIsDocked //通过变量mIsDocked判断窗口是否Dock
          
        - 窗口Docked:    
          -> DecorCaptionView.java: setPhoneWindow(PhoneWindow owner, boolean show)<br />
          //当窗口尺寸变化时，一定会调用此方法，所以在此方法中通过窗口是否Dock来控制横竖屏切换功能；<br />
          -> View.getViewTreeObserver().addOnGlobalLayoutListener(new OnGlobalLayoutListener());<br />
          //此方法需要注意：OnGlobalLayoutListener可能会被多次触发，因此在完成实现后，要将OnGlobalLayoutListener注销掉。

### 重点问题
  - 1.DecorCaptionView界面UI
    - 原生Android8.0：界面背景色是透明的，在focus和unfocus状态下，颜色会相应改变，关闭和全屏按钮的drawable使用的是矢量图片
    - Openthos5.1： 界面背景色是固定的颜色，且在focus和unfocus状态下，颜色不会相应改变，功能按钮的drawable使用的是png图片
  - 2.鼠标控制DecorCaption显示隐藏
    - DecorCaption不覆盖窗口内容，目前Android8.0实现了此效果
    - DecorCaption覆盖窗口内容，DecorCaptionView中留有接口，此效果也可以实现。
    - 在Android模拟器上由于没有鼠标Hover事件，目前是通过双指点击（Ctrl+鼠标右键）实现此效果
      - 实现方法:    
      -> dispatchTouchEvent(MotionEvent ev)<br />
      //全屏时在MotionEvent.ACTION_POINTER_UP状态下通过鼠标距离屏幕顶端点击的距离控制DecorCaption的显示和隐藏
  - 3.原生Android8.0点击窗口边框会实现全屏效果，此功能目前屏蔽掉了
  - 4.增加了在全屏和窗口Dock状态下，屏蔽掉窗口横竖屏切换功能。
  - 5.目前点击最大化按钮和双击DecorCaption的实现效果一致，都是进入全屏状态；    
      后续实现：双击DecorCaption进入全屏，而点击最大化按钮进入最大化状态
      
      
      
      
      
      









