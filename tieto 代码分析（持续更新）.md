#tieto am(activity manager)代码分析  
tieto多窗口目前开放的源码是4.4版本，目前做的工作是比较AOSP和tieto的源码差异，窥探其运行机理  
##ActivityStackSupervisor.java  
tieto主要修改了下列三个函数，并在多个地方调用setFocusedStack函数，在google源码中的Activity只有两种状态，STACK_STATE_HOME_TO_BACK,STACK_STATE_HOME_TO_FRONT,而多窗口情况下，只有被激活状态  
### ActivityStack adjustStackFocus(ActivityRecord r)  
### public void setFocusedStack(ActivityRecord r)  
### public void setFocusedStack(ActivityStack stack)  
##AcitivityStack.java  
Multiwindow cannot be behind fullscreen  
将home activity置于底部  
添加了多窗口activity的检测函数  
public boolean isMultiwindowStack()    
##ActivityManagerService.java
添加了relayoutWindow函数，在函数里面为resizeStackBox申请了管理Stacks的权限  
添加了设置和获取最大窗口大小的函数  


#tieto wm(windows manager)代码分析
##DisplayContent.java
addStackBox函数取消了对stackbox数量的限制，可以无限制的添加  
调整moveStackBoxToTop函数，原有的moveHomeStackBox函数失去了意义，因为stackBox的数量可以>2  
自定义了relayoutStackBox，具体实现在StackBox.java里  
修改stackIdFromPoint函数，核心内容实现在StackBox.java里
##WindowManagerService.java
用PointerEventDispatcher的SparseArray来代替单个  
重要工作是添加了relayoutWindow，负责调整窗口大小
