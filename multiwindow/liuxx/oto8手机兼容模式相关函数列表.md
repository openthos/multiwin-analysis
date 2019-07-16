# 兼容模式相关函数列表

### DisplayMetrics.java记录屏幕相关信息，诸如像素尺寸，密度和字体缩放等信息。
  - 路径：frameworks/base/core/java/android/util/DisplayMetrics.java

ID|API|函数意义
---|---|---
1|density|设备显示器逻辑密度
2|scaledDensity|字体缩放逻辑密度
3|widthPixels|屏幕的像素宽度
4|heightPixels|屏幕的像素高度
5|densityDpi|屏幕的像素密度Dpi
6|setToDefault()|设置默认屏幕相关信息
7|setTo(DisplayMetrics o)|设置指定屏幕相关信息


### 获取、修改DisplayMetrics对象，相关函数列表

1.Resources.java
  - 路径：frameworks/base/core/java/android/content/res/Resources.java

ID|API|函数意义
---|---|---
1|getDisplayMetrics()|获取DisplayMetrics对象
2|updateConfiguration(Configuration config, DisplayMetrics metrics)|更新指定的DisplayMetrics屏幕信息
3|Resources(AssetManager assets, DisplayMetrics metrics, Configuration config)|创建包含指定DisplayMetrics信息的Resources，过时函数
4|Resources()|创建系统Resources，并创建默认屏幕信息DisplayMetrics对象。@hide函数，系统调用

2.Display.java
  - 路径：frameworks/base/core/java/android/view/Display.java
  
ID|API|函数意义
---|---|---
1|getMetrics(DisplayMetrics outMetrics)|获取DisplayMetrics屏幕信息
2|getRealMetrics(DisplayMetrics outMetrics)|获取DisplayMetrics屏幕信息

3.DisplayInfo.java，@hide类，只提供系统访问，第三方应用无法访问
  - 路径：frameworks/base/core/java/android/view/DisplayInfo.java
  
ID|API|函数意义
---|---|---
1|logicalDensityDpi|设备像素密度Dpi
2|getAppMetrics(DisplayMetrics outMetrics)|获取DisplayMetrics屏幕信息
3|getAppMetrics(DisplayMetrics outMetrics, DisplayAdjustments displayAdjustments)|获取DisplayMetrics屏幕信息
4|getAppMetrics(DisplayMetrics outMetrics, CompatibilityInfo ci,Configuration configuration)|获取DisplayMetrics屏幕信息
5|getLogicalMetrics(DisplayMetrics outMetrics, CompatibilityInfo ci,Configuration configuration)|获取DisplayMetrics屏幕信息

4.DisplayContent.java，只可系统访问，第三方应用无法访问
  - 路径：frameworks/base/services/core/java/com/android/server/wm/DisplayContent.java

ID|API|函数意义
---|---|---
1|getDisplay()|获取设备Display，通过Display可以获取DisplayMetrics屏幕信息
2|getDisplayInfo()|获取设备相关信息DisplayInfo，通过DisplayInfo可以获取DisplayMetrics屏幕信息
3|getDisplayMetrics()|获取DisplayMetrics屏幕信息


### 获取Resources、Display对象，函数列表

1.Activity、Service、Application
  - 路径：frameworks/base/core/java/android/app/Activity.java
  - 路径：frameworks/base/core/java/android/app/Service.java
  - 路径：frameworks/base/core/java/android/app/Application.java

ID|API|函数意义
---|---|---
1|getResources()|获取应用Resources对象，最终调用的是Context.getResources()函数返回Resources对象。
2|getDisplay()|获取Display对象，最终调用的是Context.getDisplay()。

2.WindowManager
  - 路径：frameworks/base/core/java/android/view/WindowManager.java

ID|API|函数意义
---|---|---
1|getDefaultDisplay()|获取Display对象，最终调用的是Context.getDisplay()。

3.DisplayManager
  - 路径：frameworks/base/core/java/android/hardware/display/DisplayManager.java

ID|API|函数意义
---|---|---
1|getDisplay(int displayId)|获取Display对象。


### Context.java只是定义了一系列常量及抽象方法，其本身是abstract类，其抽象方法的具体实现是在ContextImpl中完成

1.context.getResources()、context.getDisplay()方法最终在ContextImpl中实现
  - 路径：frameworks/base/core/java/android/app/ContextImpl.java

ID|API|函数意义
---|---|---
1|getResources()|获取Resources对象
2|getDisplay()|获取Display对象
3|setResources(Resources r)|更改Resources对象，根据创建不同的ContextImpl，并设置ContextImpl持有的mResources对象。
4|createAppContext(ActivityThread mainThread, LoadedApk packageInfo)|创建Service绑定的ContextImpl，并设置相对应的Resources对象。
5|createActivityContext(ActivityThread mainThread,LoadedApk packageInfo, ActivityInfo activityInfo, IBinder activityToken, int displayId,Configuration overrideConfiguration)|创建Activity绑定的ContextImpl，并设置相对应的Resources对象。






