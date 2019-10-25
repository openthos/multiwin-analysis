# oto8多窗口内容总结

## 多窗口

1.设计oto8 窗口resize方式
  - frameworks/base代码结构
  ```
  core/java/android/app/IActivityManager.aidl
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/wm/ResizingFrame.java
  services/core/java/com/android/server/wm/TaskPositioner.java
  services/core/java/com/android/server/wm/WindowManagerDebugConfig.java
  services/core/java/com/android/server/wm/WindowState.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-resize)
    
2.设计oto8 窗口移动到屏幕左、右、上方Docked，且unDocked后，恢复窗口Docked之前Rect。
  - frameworks/base代码结构 
  ```
  services/core/java/com/android/server/wm/Task.java
  services/core/java/com/android/server/wm/TaskPositioner.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-docked)
    
3.设计oto8 窗口大小resize的最小Rect，关闭窗口resize触发的应用relaunched。
  - frameworks/base代码结构  
  ```
  core/java/android/view/WindowManager.java
  core/java/com/android/internal/statusbar/IStatusBar.aidl
  packages/SystemUI/src/com/android/systemui/statusbar/CommandQueue.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBar.java
  services/core/java/com/android/server/am/ActivityStack.java
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/statusbar/StatusBarManagerInternal.java
  services/core/java/com/android/server/statusbar/StatusBarManagerService.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-bounds)
    
4.设计oto8 窗口DecorCaption，完成窗口回退、窗口Rotate、窗口设置、最小化、最大化、关闭功能需求。
  - frameworks/base代码结构
  ```
  core/java/android/app/Activity.java
  core/java/android/app/IActivityManager.aidl
  core/java/android/view/IWindowSession.aidl
  core/java/android/view/Window.java
  core/java/android/view/View.java
  core/java/android/view/ViewRootImpl.java
  core/java/com/android/internal/policy/DecorView.java
  core/java/com/android/internal/widget/DecorCaptionView.java
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/am/ActivityRecord.java
  services/core/java/com/android/server/am/ActivityStarter.java
  services/core/java/com/android/server/am/LaunchingTaskPositioner.java
  services/core/java/com/android/server/am/TaskRecord.java
  services/core/java/com/android/server/wm/TaskPositioner.java
  services/core/java/com/android/server/wm/Session.java
  services/core/java/com/android/server/wm/WindowManagerService.java
  services/core/java/com/android/server/wm/WindowState.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-decorcaption)
    
5.设计oto8 launcher在FreeformStack中显示且始终保持在屏幕最底层。
  - frameworks/base代码结构
  ```
  core/java/android/app/Activity.java
  core/java/android/app/ActivityManager.java
  core/java/android/app/IActivityManager.aidl
  core/java/android/view/ViewGroup.java
  core/java/com/android/internal/policy/DecorView.java
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/am/ActivityStackSupervisor.java
  services/core/java/com/android/server/am/ActivityRecord.java
  services/core/java/com/android/server/am/ActivityStack.java
  services/core/java/com/android/server/am/ActivityStarter.java
  services/core/java/com/android/server/am/LaunchingTaskPositioner.java
  services/core/java/com/android/server/am/TaskRecord.java
  services/core/java/com/android/server/wm/Task.java
  services/core/java/com/android/server/wm/TaskTapPointerEventListener.java
  services/core/java/com/android/server/wm/WindowManagerDebugConfig.java
  services/core/java/com/android/server/wm/WindowManagerService.java
  services/core/java/com/android/server/wm/StackWindowController.java
  services/core/java/com/android/server/policy/PhoneWindowManager.java
  services/core/java/com/android/server/policy/SystemGesturesPointerEventListener.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-launcher)
    
6.设计oto8 Alt-Tab需求功能。
  - frameworks/base代码结构
  ```
  core/java/android/app/Activity.java
  core/java/android/app/ActivityManager.java
  core/java/android/app/IActivityManager.aidl
  packages/SystemUI/src/com/android/systemui/dialog/BaseDialog.java
  packages/SystemUI/src/com/android/systemui/recents/taskview/TaskViewDialog.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/OpenthosStatusBarView.java
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/am/ActivityStackSupervisor.java
  services/core/java/com/android/server/am/ActivityStack.java
  services/core/java/com/android/server/policy/PhoneWindowManager.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-alt-tab)
    
7.设计oto8 应用窗口启动默认大小，已记忆大小，设置Floationg窗口全屏显示。
  - frameworks/base代码结构
  ```
  core/java/android/app/ActivityOptions.java
  core/java/android/provider/Settings.java
  packages/SystemUI/src/com/android/systemui/startupmenu/LaunchAppUtil.java
  services/core/java/com/android/server/am/ActivityStarter.java
  services/core/java/com/android/server/am/ActivityStack.java
  services/core/java/com/android/server/am/ActivityStackSupervisor.java
  services/core/java/com/android/server/am/ActivityMetricsLogger.java
  services/core/java/com/android/server/am/LaunchingTaskPositioner.java
  services/core/java/com/android/server/am/TaskRecord.java
  services/core/java/com/android/server/wm/Task.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-def-bounds)

8.设计oto8 窗口orientation依据应用request更改。
  - frameworks/base代码结构
  ```
  services/core/java/com/android/server/am/ActivityRecord.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-orientation)

9.设计oto8 适配特殊应用的CompatPhoneWindow，保证其能正常运行在oto8上。
  - frameworks/base代码结构
  ```
  core/java/android/app/Activity.java
  core/java/android/app/ActivityThread.java
  core/java/android/view/Display.java
  core/java/android/view/MotionEvent.java
  core/java/android/view/View.java
  core/java/android/view/Window.java
  core/java/com/android/internal/policy/DecorView.java
  core/java/com/android/internal/policy/NewPhoneWindow.java
  core/java/com/android/internal/policy/PhoneWindow.java
  core/java/com/android/internal/widget/DecorCaptionView.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBarWindowView.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-phonewindow)

10.设计oto8 应用运行兼容模式。
  - frameworks/base代码结构
  ```
  core/java/android/app/Activity.java
  core/java/android/app/ActivityThread.java
  core/java/android/app/CompatContextImpl.java
  core/java/android/app/ContextImpl.java
  core/java/android/app/IActivityManager.aidl
  core/java/android/app/LoadedApk.java
  core/java/android/content/Context.java
  core/java/android/content/ContextWrapper.java
  core/java/android/content/res/Resources.java
  core/java/android/content/res/ResourcesImpl.java
  core/java/android/hardware/display/DisplayManager.java
  core/java/android/util/DisplayMetrics.java
  core/java/android/view/Display.java
  core/java/android/view/View.java
  core/java/android/view/ViewRootImpl.java
  core/java/android/view/Window.java
  core/java/com/android/internal/policy/DecorView.java
  core/java/com/android/internal/widget/DecorCaptionView.java
  core/java/com/android/internal/policy/NewPhoneWindow.java
  core/java/com/android/internal/view/FloatingActionMode.java
  core/java/com/android/internal/widget/DecorCaptionView.java
  packages/SystemUI/src/com/android/systemui/startupmenu/LaunchAppUtil.java
  packages/SystemUI/src/com/android/systemui/startupmenu/StartupMenuView.java
  packages/SystemUI/src/com/android/systemui/startupmenu/utils/AppOperateManager.java
  packages/SystemUI/src/com/android/systemui/statusbar/view/TaskBarIcon.java
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/am/ActivityStarter.java
  services/core/java/com/android/server/am/LaunchingTaskPositioner.java
  services/core/java/com/android/server/am/TaskRecord.java
  services/core/java/com/android/server/wm/Task.java
  services/core/java/com/android/server/wm/TaskPositioner.java
  services/core/java/com/android/server/wm/TaskStack.java
  services/core/java/com/android/server/wm/TaskWindowContainerController.java
  test-runner/src/android/test/mock/MockContext.java
  ```
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-compatmode)
    
11.设计oto8 窗口透明、模糊样式。

## SystemUI

#### StartupMenu

1. oto5.1 StartupMenu功能移植到oto8，设计oto8 StartupMenu功能需求。
  - frameworks/base代码结构
  ```
  packages/SystemUI/src/com/android/systemui/dialog/BaseDialog.java
  packages/SystemUI/src/com/android/systemui/dialog/MenuDialog.java
  packages/SystemUI/src/com/android/systemui/dialog/StartupMenuDialog.java
  packages/SystemUI/src/com/android/systemui/sql/SqliteOpenHelper.java
  packages/SystemUI/src/com/android/systemui/sql/SqliteOperate.java
  packages/SystemUI/src/com/android/systemui/sql/StartMenuDatabaseField.java
  packages/SystemUI/src/com/android/systemui/startupmenu/AppEntry.java
  packages/SystemUI/src/com/android/systemui/startupmenu/DialogType.java
  packages/SystemUI/src/com/android/systemui/startupmenu/ShowType.java
  packages/SystemUI/src/com/android/systemui/startupmenu/SortSelectPopupWindow.java
  packages/SystemUI/src/com/android/systemui/startupmenu/StartMenuAdapter.java
  packages/SystemUI/src/com/android/systemui/startupmenu/U.java
  packages/SystemUI/src/com/android/systemui/power/PowerSourceActivity.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBar.java
  packages/SystemUI/src/com/android/systemui/dialog/TaskbarIcon.java
  packages/SystemUI/src/com/android/systemui/LockReceiver.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBarKeyguardViewManager.java
  packages/SystemUI/src/com/android/systemui/startupmenu/LaunchAppUtil.java
  services/core/java/com/android/server/wm/WindowManagerService.java
  ```
  - [相关oto8 StartupMenu提交的patches：](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/systemui-startupmenu)

#### OpenthosStatusBar

1. 设计oto8独立OpenthosStatusBar UI，完成打开应用列表显示、输入法、电源、WIFI、音量、通知中心、日历、HOME键等功能需求。
  - frameworks/base代码结构
  ```
  packages/SystemUI/src/com/android/systemui/startupmenu/bean/AppInfo.java
  packages/SystemUI/src/com/android/systemui/startupmenu/AppAdapter.java
  packages/SystemUI/src/com/android/systemui/startupmenu/listener/OnMenuClick.java
  packages/SystemUI/src/com/android/systemui/startupmenu/utils/AppOperateManager.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/OpenthosStatusBarView.java
  packages/SystemUI/src/com/android/systemui/dialog/BatteryDialog.java
  packages/SystemUI/src/com/android/systemui/dialog/CalendarDialog.java
  packages/SystemUI/src/com/android/systemui/dialog/CalendarDisplayView.java
  packages/SystemUI/src/com/android/systemui/dialog/CalendarView.java
  packages/SystemUI/src/com/android/systemui/dialog/InputAppInfo.java
  packages/SystemUI/src/com/android/systemui/dialog/InputMethodDialog.java
  packages/SystemUI/src/com/android/systemui/dialog/VerticalSeekBar.java
  packages/SystemUI/src/com/android/systemui/dialog/VolumeDialog.java
  packages/SystemUI/src/com/android/systemui/dialog/WifiDialog.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBar.java
  packages/SystemUI/src/com/android/systemui/startupmenu/StartMenuAdapter.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBar.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBarWindowManager.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/NotificationPanelView.java
  packages/SystemUI/src/com/android/systemui/statusbar/view/TaskBarIcon.java
  packages/SystemUI/src/com/android/systemui/statusbar/policy/HeadsUpManager.java
  core/java/android/app/Activity.java
  core/java/android/app/ActivityManager.java
  core/java/android/view/Window.java
  core/java/android/view/WindowManager.java
  core/java/com/android/internal/policy/DecorView.java
  core/java/com/android/internal/statusbar/IStatusBar.aidl
  core/java/com/android/internal/widget/DecorCaptionView.java
  packages/SystemUI/src/com/android/systemui/dialog/BarDialog.java
  packages/SystemUI/src/com/android/systemui/statusbar/CommandQueue.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/NavigationBarFragment.java
  packages/SystemUI/src/com/android/systemui/statusbar/phone/StatusBar.java
  services/core/java/com/android/server/am/ActivityManagerService.java
  services/core/java/com/android/server/am/ActivityMetricsLogger.java
  services/core/java/com/android/server/am/ActivityStack.java
  services/core/java/com/android/server/statusbar/StatusBarManagerInternal.java
  services/core/java/com/android/server/statusbar/StatusBarManagerService.java
  services/core/java/com/android/server/wm/WindowManagerService.java
  
  ```
  - [相关oto8 StatusBar提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/systemui-statusbar)

