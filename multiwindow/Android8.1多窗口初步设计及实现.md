# 初步实现思路

## 一、窗口Header

### Android8.1原生Header已实现功能
  - 1.Android8.1原生Header，功能按钮有：关闭、全屏；
### 对比Openthos5.1，需增加功能
  - 1.增加功能按钮：最小化（前提PhoneStatusBar完成）、最大化、横竖切换、回退；
  - 2.Header自动隐藏、显示：窗口最大化时隐藏Header，鼠标指针Hover时显示Header；
  - 3.Header双击：窗口最大化时，双击变为窗口化；当窗口化时，双击变为最大化；

## 二、窗口层级结构
  - Android8.1窗口自带阴影效果；
  - 设计窗口层级结构逻辑：DecorCaptionView与DecorView层级关系

## 三、窗口的移动、缩放
  - Android8.1原生已实现窗口的拖动、缩放；
  - 需解决窗口缩放存在的问题：缩放过程中窗口抖动；

## 四、窗口全屏显示
  - 设计窗口全屏显示逻辑：设计全屏窗口所属ActivityStack（FullScreen_Stack或者FreeForm_Stack）。

## 五、应用运行适配
  - 适配第三方应用

# 多窗口设计问题

## 窗口的层级结构
  - DecorCaptionView与DecorView层级关系设计；

## 窗口拖动、缩放
  - Android8.1 原生缩放存在抖动问题，对比Openthos5.1缩放没有此问题，考虑设计“线缩”；

## 窗口全屏显示
  - 全屏窗口所属Activitystack设计：FullScreen_Stack或者FreeForm_Stack

## PhoneStatusBar整体设计
  - 任务栏整体设计

## Alt + Tab显示应用
  - 对比原生Android8.1的Alt + Tab窗口显示，考虑重新设计或者在原有基础上改动；
