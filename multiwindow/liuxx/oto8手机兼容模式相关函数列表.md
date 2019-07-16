# 兼容模式相关函数列表

1.DisplayMetrics.java记录屏幕相关信息，诸如尺寸，密度和字体缩放等信息。
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
