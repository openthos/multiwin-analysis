# 特殊应用窗口层次结构初步设计（例如微信）
  - 第三方应用通过窗口根视图DecorView可以更改窗口View的树状结构，通常的处理包括addView、removeView以及更改窗口Theme等。openthos8.1多窗口下，窗口结构增加了DecorCaptionView(控制窗口移动、关闭、最小化、最大化等)，在多窗口下，为了避免应用修改窗口结构，导致DecorCaptionView不可见或者Theme异常，初步设计了特殊应用的窗口结构。
  
### 窗口结构初步设计
1.PhoneWindow是窗口Window的唯一实现类，针对特殊应用，设计特殊的Window实现类special-Phonewindow（继承于PhoneWindow）。
  
  - phonewindow的初始化是在Activity方法attach中实现的，针对特殊应用，可以在此处判断、过滤，初始化不同的PhoneWindow。

2.DecorView是activity窗口的根视图，针对特殊应用，设计两个特殊的DecorView，一个real-DecorView（继承DecorView）作为activity窗口的根视图，被WindowManager管理，另一个fake-DecorView（继承Framelayout），应用getDecorView时提供此fake-DecorView。这样可以避免应用更改real-DecorView的结构。
