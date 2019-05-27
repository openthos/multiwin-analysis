# 应用强制手机模式设计、实现文档
   针对部分应用在大屏、窗口化下运行不理想情况，openthos8.1上设计强制手机模式（横屏、竖屏），强制手机模式实现窗口结构兼容性、系统API兼容性，模拟应用在手机设备上的运行环境，从而避免不支持桌面化的应用在OPENTHOS上运行所带来的问题。

## 内容
  - 功能需求
  - 设计实现
  - 工作进展
  
### 功能需求
  - 强制手机模式（竖屏）
  - 强制手机模式（横屏）
  - 应用在强制手机模式下运行，窗口可拖动，不可缩放
  - 兼容大屏下适配差的应用
  
### 设计实现
一、窗口结构兼容性设计

   ![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/liuxx/prc/multiwindow.jpg)
   
在AOSP中，多窗口情况下和单窗口情况下的窗口结构存在的显著的差异，当应用运行在多窗口模式下时，则会由于对自身所处窗口结构认知不同导致窗口结构达不到应用的预期效果，从而发生显示错位等问题。针对这种情况，改造一套桌面化程度较低而运行稳定性更高的窗口结构

1、兼容性窗口结构实现

设计兼容性PhoneWindow（NewPhoneWindow），在NewPhoneWindow中构造一个兼容性DecorView（NewDecorview），同时构造一个虚拟的DecorView（VirtualDecorView），并对getDecorView的结果进行修正，系统调用getDecorview时返回真实的DecorView，第三方应用调用getDecorview时返回虚拟Decorview（VirtualDecorView），保证应用依然可以拿到一个子View仅有ContentView的DecorView作为自己的RootView。当我们启动一个用户希望以兼容模式启动的应用时，便会主动加载这一构造，即便这一构造会一定程度上牺牲目标应用的运行效率但由于分离化的设计，其他以标准模式运行的应用并不会收到兼容行为所带来的额外开销的影响。

新的兼容性窗口结构：

![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/liuxx/prc/newphonewindow.PNG)

具体的代码实现路径：
    
      frameworks/base/core/java/com/android/internal/policy/NewPhoneWindow.java
 
二、系统API兼容性设计

窗口发生更新时，会依据当前窗口的状态来构造一系列虚拟的系统API返回数据。当兼容模式运行的应用请求这些数据时，系统会截获这一行为并将能够让其正常运行的基于窗口状态构造的虚拟数据代替返回，以保证应用的运行不会出现由屏幕大小和窗口大小的差异造成的冲突，更不会因此引发错误。

1、坐标API兼容性

目前的坐标API兼容性设计是对所有的点击事件和对View进行的屏幕位置判断方法都采取参照RootView偏移值的获取方式，而兼容性RootView所返回的这些内容统一规定为看作自己处于（0,0）点的方式，以此来避免应用对自身所处屏幕位置的错误认识带来的不良运行效果。

具体的代码实现路径：

      frameworks/base/java/core/com/android/internal/policy/NewPhoneWindow.java
      
2、屏幕数据API兼容性
 
应用在手机设备上运行，屏幕的尺寸与应用窗口的尺寸一致；而在多窗口环境下，屏幕的尺寸与应用窗口化的尺寸不一致，因此当兼容模式运行的应用请求屏幕数据时，系统需要截获这一行为并将能够让其正常运行的基于窗口状态构造的虚拟数据代替返回。 应用获取屏幕分辨率、密度的方法：
   - context.getResources().getDisplayMetrics返回DisplayMetrics对象;
   - WindowManager windowmanager = (WindowManager) (context.getSystemService(Context.WINDOW_SERVICE));<br />windowmanager.getDefaultDisplay().getMetrics(New DisplayMetrics())。

两种方式都需要context作为入口调用其相关API获取DisplayMetrics，而context的方法最终是在ContextImpl实现类中完成，通过设计兼容性ContextImpl（NewContextImpl继承于ContextImpl），并重写相关获取屏幕数据调用的API，伪装应用正常运行基于窗口状态构造的虚拟数据代替返回。

（1）获取屏幕数据的第一种方法：context.getResources().getDisplayMetrics返回DisplayMetrics对象。通过截获getResources返回的Resources资源，并伪装Resources持有的DisplayMetrics屏幕虚拟数据。应用持有的Context类型主要有三种：
   - Application
   - Activity
   - Service
   
一个应用Context的数量为1个Application + Activity个数 + Service个数总和，而context.getResources获取的Resources实例只有一个，所以只需要截获Resources实例并伪装应用基于窗口状态构造的虚拟数据。ContextImpl创建Activity、Service类型context的方法是createActvityContext、createAppContext，在ActivityThread的performLaunchActivity、handleCreateService方法中构造ContextImpl实例并绑定到Activity、Service中，这样Activity、Service中context的相关接口通过调用ContextImpl方法实现。针对此种方法获取屏幕数据的情况，openthos设计兼容性ContextImpl（NewContextImpl继承于ContextImpl），并重写相关获取屏幕数据调用的API，伪装应用正常运行基于窗口状态构造的虚拟数据代替返回，同时在getResources方法中截获、伪装未处理的屏幕数据，且在兼容模式下运行的应用，拦截Resources调用updateConfiguration方法更新DisplayMetrics数据。

（2）获取屏幕数据的第二种方法：windowmanager.getDefaultDisplay().getMetrics(New DisplayMetrics())。还没调研分析通用的拦截、伪装API方式。
   
### 工作进展
以微信为例，目前工作进展：

ID|任务名称|状态
----|----|----
1|窗口结构兼容性设计|已完成提交
2|坐标API兼容性设计|已完成提交
3|截获context.getResources().getDisplayMetrics方式获取的屏幕数据，伪装虚拟数据提供返回给应用|已完成，未提交
4|截获windowmanager.getDefaultDisplay().getMetrics(New DisplayMetrics())方式获取的屏幕数据，伪装虚拟数据提供返回给应用|进度0%
5|窗口标题栏UI重构，增加窗口兼容性按钮|完成50%
6|应用在兼容模式下运行，窗口可拖动，不可缩放|进度0%

### 问题
已微信为例，存在的问题：
   - 1.微信的初始页面，登录、注册按钮重叠问题，其是根据屏幕尺寸设置按钮margin值，拦截、伪装DisplayMetrics数据导致按钮重叠显示，而当获取真实屏幕尺寸时，按钮正常显示。
   - 2.微信朋友中图片显示问题，拦截、伪装DisplayMetrics数据导致朋友圈图片无法满铺窗口显示。已定位到原因。
   - 3.当前应用访问其他应用界面时，拦截失效。
   - 4.部分popupwindow弹窗位置不正确。
   


  
   
   
  
 

 







