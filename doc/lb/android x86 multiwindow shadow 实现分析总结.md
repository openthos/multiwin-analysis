
# android x86 multiwindow 边框阴影相关技术分析总结

## １.最新的阴影的实现方式的总结分析
- 首先从页面入手分析，我们要在multiwindow的应用周边增加一圈阴影，最新的实现方式是以图片的方式，通过相对布局构成一个“阴影边框”，由它去套各个需要阴影的应用
- 边框的布局文件：mw_shadow.xml（core/res/res/layout/mw_shadow.xml）<br \>由八张图片组成：shadow_bottom.png，shadow_left.png，shadow_leftbottom.png，shadow_lefttop.png，shadow_right.png，shadow_rightbottom.png，shadow_righttop.png，shadow_top.png（图片包括四个角以及四个遍）<br \>
　mw_shadow.xml请查看：[mw_shadow.md](https://github.com/openthos/multiwin-analysis/new/master/doc/lb/mw_shadow.md)
- 因为我们所有应用涉及到的布局文件类共有九种，分别为其增加上述mw_shadow <br \>九种布局：screen_action_bar_mw.xml，screen_custom_title_mw.xml，screen_progress_mw.xml，screen_simple_mw.xml，screen_simple_overlay_action_mode_mw.xml，screen_swipe_dismiss_mw.xml，screen_title_icons_mw.xml，screen_title_mw.xml，screen_toolbar_mw.xml<br \>我们以其中一种为例：<br \>
screen_action_bar_mw.xml<br \>
具体代码请查看：[screen_action_bar_mw.md](https://github.com/openthos/multiwin-analysis/new/master/doc/lb/screen_action_bar_mw.md)<br \>
我们可以看到，我们通过<include layout="@layout/mw_shadow"/>的方式为九种布局都引入了该阴影边框。
- 根据上述的代码，我们可以看到三个border：<br \>
mw_shadow_outsider,mw_shadow_middle,mw_shadow_inside<br \>
多层border值的设置，与之前的实现方式有些关联，我们的处理方式，将mw_shadow_outsider_border和mw_shadow_middle_border的颜色设为黑色，宽度变为０，只留下mw_shadow_inside_border用来为阴影border("mw_shadow_padding）占位，代码如下：<br \>
core/res/res/values/colors.xml
``` 
    <color name="mw_shadow_outside">#00000000</color>
    <color name="mw_shadow_middle">#00000000</color>
    <color name="mw_shadow_inside">#00000000</color>
``` 
core/res/res/values/mw_values.xml
``` 
    <dimen name="mw_shadow_outside_border">0dip</dimen>
    <dimen name="mw_shadow_middle_border">0dip</dimen>
    <dimen name="mw_shadow_inside_border">25dp</dimen>
    <dimen name="mw_shadow_padding">25dp</dimen>
``` 
以后阴影的效果由border的宽度以及组成border的图片来控制
- 我们看到其中有布局mw_cover.xml 的引入：<br \>
cover的作用是为出现dialog 的情形增加半透明的灰色背景（根据dialog的出现情况，当相遇应用大小时，需要cover则添加，针对于特殊dialog(例如wps\word等，不需要则不用添加)）<br \>
该方式涉及到两个方法：enableMultiWindowToWindowManager（）和disableMultiWindowToWindowManager（），上面两个方法决定，当dialog 出现时由谁来具体处理，当dialog存在时enable 交由TaskStack处理，当dialog消失时disable，交由phonewindow处理
- 注意点：我们给引用添加了border值，虽然此时的mw_shadow_outsider_border和mw_shadow_middle_border的padding值为０，但mw_shadow_inside_border是有宽度的，所以当我们的应用最大化的时候，为了不显示阴影，并且布局不出错，我们需要更改全屏的默认位置，不在时（０，０），而需要减去添加的padding值

