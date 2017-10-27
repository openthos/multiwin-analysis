# Android 8.0多窗口之DecorCaption功能需求与实现文档

## 内容：
  - 功能需求
  - 设计实现
  - 存在问题
  
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
      -> TaskRecord.java: reparent(int preferredStackId, boolean toTop, @ReparentMoveStackMode int moveStackMode,boolean, animate, boolean deferResume, String reason)
      
  
