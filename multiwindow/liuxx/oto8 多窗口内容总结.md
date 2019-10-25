# oto8多窗口内容总结

## 多窗口

1.设计oto8 窗口resize方式
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-resize)
    
2.设计oto8 窗口移动到屏幕左、右、上方Docked，且unDocked后，恢复窗口Docked之前Rect。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-docked)
    
3.设计oto8 窗口大小resize的最小Rect，关闭窗口resize触发的应用relaunched。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-bounds)
    
4.设计oto8 窗口DecorCaption，完成窗口回退、窗口Rotate、窗口设置、最小化、最大化、关闭功能需求。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-decorcaption)
    
5.设计oto8 launcher在FreeformStack中显示且始终保持在屏幕最底层。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-launcher)
    
6.设计oto8 Alt-Tab需求功能。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-alt-tab)
    
7.设计oto8 应用窗口启动默认大小，已记忆大小，设置Floationg窗口全屏显示。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-def-bounds)

8.设计oto8 窗口orientation依据应用request更改。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-orientation)

9.设计oto8 适配特殊应用的CompatPhoneWindow，保证其能正常运行在oto8上。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-phonewindow)

10.设计oto8 应用运行兼容模式。
  - [相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-compatmode)
    
11.设计oto8 窗口透明、模糊样式。

## SystemUI

#### StartupMenu

1. oto5.1 StartupMenu功能移植到oto8，设计oto8 StartupMenu功能需求。

  - [相关oto8 StartupMenu提交的patches：](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/systemui-startupmenu)


#### OpenthosStatusBar

1. 设计oto8独立OpenthosStatusBar UI，完成打开应用列表显示、输入法、电源、WIFI、音量、通知中心、日历、HOME键等功能需求。

  - [相关oto8 StatusBar提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/systemui-statusbar)

