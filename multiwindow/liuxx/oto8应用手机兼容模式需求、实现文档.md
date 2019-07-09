# 应用手机兼容模式设计、实现文档
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

设计兼容性PhoneWindow（NewPhoneWindow继承PhoneWindow），在NewPhoneWindow中构造一个兼容性DecorView（NewDecorview继承DecorView），同时构造一个虚拟的DecorView（VirtualDecorView继承FrameLayout），并对getDecorView的结果进行修正，系统调用getDecorview时返回真实的DecorView，第三方应用调用getDecorview时返回虚拟Decorview（VirtualDecorView），保证应用依然可以拿到一个子View仅有ContentView的DecorView作为自己的RootView。当我们启动一个用户希望以兼容模式启动的应用时，便会主动加载这一构造，即便这一构造会一定程度上牺牲目标应用的运行效率但由于分离化的设计，其他以标准模式运行的应用并不会收到兼容行为所带来的额外开销的影响。

新的兼容性窗口结构：

![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/liuxx/prc/newphonewindow.PNG)

2、代码设计实现

ID|API|兼容性设计
---|---|---
1|Activity.java<br />attach()|根据用户需求，加载、构造不同的PhoneWindow，渲染不同的窗口结构；对于不同窗口结构的代码设计，放到相应的PhoneWindow中完成，降低代码的耦合性。

具体的代码实现路径：
    
      frameworks/base/core/java/android/app/Activity.java
      frameworks/base/core/java/com/android/internal/policy/NewPhoneWindow.java
 
二、系统API兼容性设计

窗口发生更新时，会依据当前窗口的状态来构造一系列虚拟的系统API返回数据。当兼容模式运行的应用请求这些数据时，系统会截获这一行为并将能够让其正常运行的基于窗口状态构造的虚拟数据代替返回，以保证应用的运行不会出现由屏幕大小和窗口大小的差异造成的冲突，更不会因此引发错误。

1、坐标API兼容性

目前的坐标API兼容性设计是对所有的点击事件和对View进行的屏幕位置判断方法都采取参照RootView偏移值的获取方式，而兼容性RootView所返回的这些内容统一规定为看作自己处于（0,0）点的方式，以此来避免应用对自身所处屏幕位置的错误认识带来的不良运行效果。

代码设计实现

坐标API设计

ID|API|兼容性设计
---|---|---
1|View.java<br />getLocationOnScreen()|采取参照RootView偏移值方式，应用获取的兼容性VirtualDecorView的屏幕位置，返回的屏幕位置统一规定为处于(0,0(或者DecorCaptionView的Height))点
2|MotionEvent.java<br />getRawX()、getRawY()|采取View点击事件获取的RawX、RawY偏移值方式，在兼容性NewDecorView的点击事件拦截onInterceptTouchEvent方法中，规定RawX、RawY的偏移值为NewDecorView在屏幕上的位置。

具体的代码实现路径：

      frameworks/base/java/core/android/view/View.java
      frameworks/base/java/core/android/view/MotionEvent.java
      frameworks/base/java/core/com/android/internal/policy/NewPhoneWindow.java
      
2、屏幕数据API兼容性
 
应用在手机设备上运行，屏幕的尺寸与应用窗口的尺寸一致；而在多窗口环境下，屏幕的尺寸与应用窗口化的尺寸不一致，因此当兼容模式运行的应用请求屏幕数据时，系统需要截获这一行为并将能够让其正常运行的基于窗口状态构造的虚拟数据代替返回。 屏幕分辨率、密度等相关信息都保存在DisplayMetrics对象中，应用获取DisplayMetrics的方式有两种：

ID|API|
---|---
1|context.getResources().getDisplayMetrics()
2|WindowManager windowmanager = (WindowManager) (context.getSystemService(Context.WINDOW_SERVICE));<br />windowmanager.getDefaultDisplay().getMetrics()

两种方式都需要context作为入口调用其相关API获取DisplayMetrics，而context的方法最终是在ContextImpl实现类中完成，通过设计兼容性ContextImpl（NewContextImpl继承于ContextImpl），并重写相关获取屏幕数据调用的API，伪装应用正常运行基于窗口状态构造的虚拟数据代替返回。

（1）获取屏幕数据的第一种方法：应用通过context.getResources()获取Resources资源对象，在Resources资源对象中持有DisplayMetrics屏幕相关参数。

   - 手机兼容模式屏幕缩放设计：

      - 首先通过虚拟Resources持有的DisplayMetrics屏幕相关数据，返回给应用在兼容模式下运行需要的屏幕分辨率、密度等虚拟数据；Resources是通过context.getResources()方法获取的，只要截获返回的Resources对象，并虚拟DisplayMetrics屏幕数据，实现兼容模式屏幕缩放。
      - 其次拦截应用对Resources持有的DisplayMetrics数据的更新。
 
   - 手机兼容模式代码设计：

context.getResources()具体实现是在ContextImpl.java中完成的，ContextImpl是Context的方法实现类，根据代码的分离化、低耦合理念，针对手机兼容模式设计NewContextImpl（继承ContextImpl），在NewContextImpl中完成对Resources的截获，以及虚拟Resources持有的DisplayMetrics屏幕数据。

应用获取Context的类型主要有三种类型：

Context类型|构造API|兼容设计
---|---|---
Application|ContextImpl.java<br />public Context createApplicationContext()|Application获取的Resources最终是通过Activity、Service创建的，可以之拦截Activity、Service获取的Resources对象，并虚拟DisplayMetrics数据；
Activity|ContextImpl.java<br />static ContextImpl createActivityContext()|在NewContextImpl的createActivityContext方法中拦截创建的Resources，并虚拟DisplayMetrics屏幕数据；
Service|ContextImpl.java<br />static ContextImpl createAppContext()|在NewContextImpl的createAppContext方法中拦截创建的Resources，并虚拟DisplayMetrics屏幕数据；

手机兼容模式下运行，需要提供给应用NewContextImpl创建的Activity，Service，需要在调用createAppContext、createActivityContext的地方，通过NewContextImpl的createAppContext、createActivityContext方法创建Activity、Service，这样应用通过Context.getResources()获取的屏幕数据是虚拟，伪装的。

ID|API|兼容设计
---|---|---
1|ActivityThread.java<br />createBaseContextForActivity()|ContextImpl.createActivityContext()根据应用运行模式提供NewContextImpl.createActivityContext()创建Context；
2|ActivityThread.java<br />handleCreateBackupAgent()|ContextImpl.createAppContext()根据应用运行模式提供NewContextImpl.createAppContext()创建Context；
3|ActivityThread.java<br />handleCreateService()|ContextImpl.createAppContext()根据应用运行模式提供NewContextImpl.createAppContext()创建Context；
4|ActivityThread.java<br />handleBindApplication()|ContextImpl.createAppContext()根据应用运行模式提供NewContextImpl.createAppContext()创建Context；
5|ActivityThread.java<br />attach()|ContextImpl.createAppContext()根据应用运行模式提供NewContextImpl.createAppContext()创建Context；
6|LoadedApk.java<br />makeApplication()|ontextImpl.createAppContext()根据应用运行模式提供NewContextImpl.createAppContext()创建Context；

通过上面NewContextImpl的createActivityContext、createAppContext方法完成对Resources构造时虚拟屏幕数据DisplayMetrics，基本上能适配大多数apps在兼容模式下运行时屏幕缩放渲染，而针对一些特殊应用（例如微信），应用运行的屏幕密度是根据设备分辨率动态计算，然后再缩放渲染，因此仅仅在Resources构建时虚拟屏幕数据DisplayMetrics，无法满足此类应用在兼容模式下运行时缩放渲染，同时需要拦截app动态计算的屏幕密度对app窗口密度的更新，涉及相关API如下：

ID|API|兼容设计
---|---|---
1|Resources.java<br />upateConfiguration(Configuration config, DisplayMetrics metrics)|根据应用是否兼容模式运行，拦截应用对DisplayMetrics的更新；
2|DisplayMetrics.java<br />setTo(DisplayMetrics metrics)|根据应用是否兼容模式运行，拦截应用对DisplayMetrics数据更新。

（2）获取屏幕数据的第二种方法：windowmanager.getDefaultDisplay().getMetrics(New DisplayMetrics())。还没调研分析通用的拦截、伪装API方式。

3、屏幕数据伪装原则

（1）返回虚拟的屏幕分辨率1080x1920， 5英寸的对角线尺寸，也就是应用在虚拟的5寸FHD设备上运行。

（2）返回虚拟的屏幕密度原则：
   - 按照虚拟像素密度和OTO设备实际像素密度，取整后缩放；
   - 根据不同的oto设备实际像素密度，固定相对应的虚拟密度，设计几个固定匹配虚拟密度。
   
### 工作进展
以微信为例，目前工作进展：

ID|任务名称|状态
----|----|----
1|窗口结构兼容性设计|已完成提交
2|坐标API兼容性设计|已完成提交
3|截获context.getResources().getDisplayMetrics方式获取的屏幕数据，伪装虚拟数据提供返回给应用|已完成，未提交
4|截获windowmanager.getDefaultDisplay().getMetrics(New DisplayMetrics())方式获取的屏幕数据，伪装虚拟数据提供返回给应用|进度0%
5|窗口标题栏UI重构，增加窗口兼容性按钮|已完成，未提交
6|窗口兼容性模式弹窗，提供普通、全屏、强制竖屏、强制横屏模式切换，默认普通模式高亮，模式切换弹出提示信息|完成70%
7|应用在兼容模式下运行，窗口可拖动，不可缩放|进度0%

### 问题
已微信为例，存在的问题：
   - 1.微信的初始页面，登录、注册按钮重叠问题，其是根据屏幕尺寸设置按钮margin值，拦截、伪装DisplayMetrics数据导致按钮重叠显示，而当获取真实屏幕尺寸时，按钮正常显示。
   - 2.微信朋友中图片显示问题，拦截、伪装DisplayMetrics数据导致朋友圈图片无法满铺窗口显示。已定位到原因。
   - 3.当前应用访问其他应用界面时，拦截失效。
   - 4.部分popupwindow弹窗位置不正确。
   - 5.微信（7.0.3）小程序界面，更改分辨率后，不显示内容，应用及进程关闭重启，仍然不显示，系统重启后正常显示。
   - 6.微信的部分小程序界面是webview，在强制手机模式下，屏幕密度缩放失效。
   - 7.微信（7.0.4）小程序打不开，窗口闪退。
   


  
   
   
  
 

 







