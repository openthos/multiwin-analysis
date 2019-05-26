# 应用强制手机模式设计、实现文档
   针对部分应用在大屏、窗口化下运行不理想情况，openthos8.1上设计强制手机模式（横屏、竖屏），强制手机模式实现窗口结构兼容性、系统API兼容性，模拟应用在手机设备上的运行环境，从而避免不支持桌面化的应用在OPENTHOS上运行所带来的问题。

## 内容
  - 功能需求
  - 设计实现
  - 工作进展
  - 重点、难点
  
### 功能需求
  - 强制手机模式（竖屏）
  - 强制手机模式（横屏）
  - 应用在强制手机模式下运行，窗口可拖动，不可缩放
  - 模拟应用手机运行环境，实现部分应用在大屏、窗口化下运行兼容性
  
### 设计实现
一、窗口结构兼容性设计

   ![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/liuxx/prc/multiwindow.jpg)
   
在AOSP中，多窗口情况下和单窗口情况下的窗口结构存在的显著的差异，当应用运行在多窗口模式下时，则会由于对自身所处窗口结构认知不同导致窗口结构达不到应用的预期效果，从而发生显示错位等问题。针对这种情况，改造一套桌面化程度较低而运行稳定性更高的窗口结构

1、兼容性窗口结构实现

设计兼容性PhoneWindow（NewPhoneWindow），在NewPhoneWindow中构造一个兼容性DecorView（NewDecorview），同时构造一个虚拟的DecorView（VirtualDecorView），并对getDecorView的结果进行修正，系统调用getDecorview时返回真实的DecorView，第三方应用调用getDecorview时返回虚拟Decorview（VirtualDecorView），保证应用依然可以拿到一个子View仅有ContentView的DecorView作为自己的RootView。当我们启动一个用户希望以兼容模式启动的应用时，便会主动加载这一构造，即便这一构造会一定程度上牺牲目标应用的运行效率但由于分离化的设计，其他以标准模式运行的应用并不会收到兼容行为所带来的额外开销的影响。

新的兼容性窗口结构：

![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/liuxx/prc/newphonewindow.PNG)

具体的代码实现路径：frameworks/base/core/com/java/android/internal/policy/NewPhoneWindow.java
 
二、系统API兼容性设计


  
   
  
  
   
   
  
 

 







