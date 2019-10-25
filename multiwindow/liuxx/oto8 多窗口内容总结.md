# oto8多窗口内容总结

## 多窗口

1.设计oto8 窗口resize方式
  - 相关提交的patches
    - ![相关提交的patches](https://github.com/openthos/multiwin-analysis/tree/master/multiwindow/liuxx/oto8-patches/multi-resize)
    
2.设计oto8 窗口移动到屏幕左、右、上方Docked，且unDocked后，恢复窗口Docked之前Rect。
  - 相关提交的patches
    - 0016-Save-rect-before-window-docked-and-reset-to-it-after.patch
    - 0017-Now-window-can-docked-to-top.-And-when-a-docked-wind.patch
    - 0074-oto8-frameworks-base-Now-window-can-maximize.patch
    - 0083-oto8-frameworks-base-Optimize-the-codes-about-window.patch
    
3.设计oto8 窗口大小resize的最小Rect，关闭窗口resize触发的应用relaunched。
  - 相关提交的patches
    - 0018-void-the-small-frame-change-when-close-a-window-has-.patch
    - 0020-setStatusbarVisibility-and-unpausing-activity-in-fre.patch
    
4.设计oto8 窗口DecorCaption，完成窗口回退、窗口Rotate、窗口设置、最小化、最大化、关闭功能需求。
  - 相关提交的patches
    - 0021-initial-commit-of-DecorCaption.patch
    - 0022-using-RESIZE_MODE_USER_FORCED-to-resizeTask-during-o.patch
    - 0048-frameworks-base-Auto-show-or-hide-the-DecorCaption-b.patch
    - 0049-frameworks-base-Optimize-the-codes-that-show-or-hide.patch
    - 0050-frameworks-base-Modify-the-issue-that-the-Rotate-but.patch
    - 0051-frameworks-base-Change-the-DecorCaption-Icons-to-Ope.patch
    - 0052-frameworks-base-Whether-task-is-docked-to-show-or-hi.patch
    - 0053-frameworks-base-Whether-app-ResizeMode-matches-the-r.patch
    - 0058-frameworks-base-Keep-the-ViewTreeObserver-to-update-.patch
    - 0059-frameworks-base-Decreased-side-margin-for-showing-do.patch
    - 0070-oto8-frameworks-base-Resize-task-maximize-in-Freefor.patch
    - 0075-oto8-frameworks-base-Add-fullscreen-button-in-decor-.patch
    - 0079-oto8-frameworks-base-Fix-the-crash-caused-by-restart.patch
    - 0080-oto8-frameworks-base-Support-auto-hide-show-window-c.patch
    - 0086-oto8-frameworks-base-Fix-some-bugs-about-decor-capti.patch
    
5.设计oto8 launcher在FreeformStack中显示且始终保持在屏幕最底层。
  - 相关提交的patches
    - 0054-frameworks-Fix-bug-of-Launcher3-and-scrollBar.patch
    - 0061-oto8-frameworks-base-Release-a-beta-version-of-keep-.patch
    - 0072-oto8-frameworks-base-Fix-bug-that-AMS-only-find-home.patch
    - 0085-oto8-frameworks-base-For-openthos-moveActivityTaskTo.patch
    - 0089-oto8-frameworks-base-Fix-the-issue-that-the-launcher.patch
    
6.设计oto8 Alt-Tab需求功能。
  - 相关提交的patches
    - 0073-oto8-frameworks-base-Support-delete-and-clear-all-ap.patch
    - 0082-oto8-frameworks-base-Complete-the-function-of-return.patch
    
7.设计oto8 应用窗口启动默认大小，已记忆大小，设置Floationg窗口全屏显示。
  - 相关提交的patches
    - 0065-oto8-frameworks-base-Set-launchBounds-to-more-resona.patch
    - 0066-oto8-frameworks-base-Run-app-will-use-task-bounds-wh.patch
    - 0077-oto8-frameworks-base-Always-set-launch-rect-to-fulls.patch
    - 0078-oto8-frameworks-base-Fix-bug-about-minimization-of-F.patch
    - 0106-oto8-frameworks-base-StartActivity-in-the-same-stack.patch

8.设计oto8 窗口orientation依据应用request更改。
  - 相关提交的patches
    - 0067-oto8-frameworks-base-Judge-if-orientation-need-chang.patch
    

9.设计oto8 适配特殊应用的CompatPhoneWindow，保证其能正常运行在oto8上。
  - 相关提交的patches
    - 0084-oto8-frameworks-base-Release-a-beta-version-of-a-new.patch
    - 0099-oto8-frameworks-base-Using-CompatPhoneWindow-to-solv.patch

10.设计oto8 应用运行兼容模式。
  - 相关提交的patches
    - 0093-oto8-frameworks-base-Initialize-commit-for-compatibl.patch
    - 0094-oto8-frameworks-base-Initialize-commit-for-compatibl.patch
    - 0095-oto8-frameworks-base-Initialize-commit-for-compatibl.patch
    - 0097-oto8-frameworks-base-Add-runmode-for-context-and-Dis.patch
    - 0098-oto8-frameworks-base-Change-method-of-drawColor.patch
    - 0100-oto8-frameworks-base-Set-isInMultiWindowMode-in-Acti.patch
    

11.设计oto8 窗口透明、模糊样式。

## SystemUI

#### StartupMenu

1. oto5.1 StartupMenu功能移植到oto8，设计oto8 StartupMenu功能需求。

2.相关oto8 StartupMenu提交的patches：
  - 0001-Oreo-frameworks-base-SystemUI-Add-StartupMenu-java-c.patch
  - 0002-Oreo-frameworks-base-StartupMenu-add-res-files.patch
  - 0003-Oreo-frameworks-base-StartupMenu-add-colors-dimens-s.patch
  - 0028-Oreo-frameworks-base-SystemUI-Add-Power-function.patch
  - 0032-Oreo-frameworks-base-add-sleep-feature-to-PowerSourc.patch
  - 0034-Oreo-frameworks-base-SystemUI-Rewrite-the-logic-of-l.patch
  - 0035-Oreo-frameworks-base-1.-add-lock-screen-feature-but-.patch
  - 0042-Oreo-frameworks-base-SystemUI-clean-startupmenu-s-co.patch
  - 0057-oto8-frameworks-base-SystemUI-Fix-the-bug-that-reboo.patch
  - 0109-oto8-frameworks-base-Allow-system-window-get-focus-f.patch

#### OpenthosStatusBar

1. 设计oto8独立OpenthosStatusBar UI，完成打开应用列表显示、输入法、电源、WIFI、音量、通知中心、日历、HOME键等功能需求。

2.相关oto8 StatusBar提交的patches
  - 0004-Oreo-frameworks-base-SystemUI-add-simple-layout-to-o.patch
  - 0005-frameworks-base-SystemUI-java-Add-dialog-of-inputmet.patch
  - 0006-Oreo-frameworks-base-SystemUI-java-Add-dialog-of-inp.patch
  - 0007-Oreo-frameworks-base-StatusBar-add-control-notificai.patch
  - 0008-Oreo-frameworks-base-SystemUI-1.Change-the-popup-ani.patch
  - 0009-Oreo-frameworks-base-SystemUI-add-longclick-for-star.patch
  - 0010-Oreo-frameworks-base-SystemUI-hide-old-status-bar-an.patch
  - 0011-Oreo-frameworks-base-SystemUI-add-notification.patch
  - 0012-Oreo-frameworks-base-SystemUI-1.add-home-layout-2.-i.patch
  - 0023-Oreo-frameworks-base-SystemUI-StatusBarIcon-init-com.patch
  - 0024-Oreo-frameworks-base-SystemUI-Adapter-to-Chinese-and.patch
  - 0025-Oreo-frameworks-base-SystemUI-Set-status-bar-show-di.patch
  - 0026-Oreo-frameworks-base-Fixed-bug-1.-click-ethnet-confi.patch
  - 0027-Oreo-frameworks-base-SystemUI-notification-panel-add.patch
  - 0029-Oreo-frameworks-base-SystemUI-notification-panel-add.patch
  - 0030-Oreo-frameworks-base-SystemUI-Add-dialog-about-show-.patch
  - 0031-Oreo-frameworks-base-SystemUI-Format-code.patch
  - 0033-Oreo-frameworks-base-SystemUI-Let-openthos-status-ba.patch
  - 0036-Oreo-frameworks-base-SystemUI-Fix-a-bug-that-it-will.patch
  - 0037-Change-the-way-to-remove-statusbarIcon.patch
  - 0038-Oreo-frameworks-base-SystemUI-Support-language-switc.patch
  - 0039-Oreo-frameworks-base-SystemUI-Support-language-switc.patch
  - 0040-Oreo-frameworks-base-SystemUI-When-click-empty-statu.patch
  - 0041-Oreo-frameworks-base-SystemUI-Modify-the-pop-up-posi.patch
  - 0043-Oreo-frameworks-base-SystemUI-Add-a-function-that-de.patch
  - 0044-Oreo-frameworks-base-temp-commit-change-notification.patch
  - 0045-Oreo-frameworks-base-SystemUI-Save-taskbar-icon.patch
  - 0046-Oreo-frameworks-base-SystemUI-The-batter-icon-will-c.patch
  - 0047-Oreo-frameworks-base-SystemUI-Fix-a-focus-problem-ab.patch
  - 0055-Oreo-frameworks-base-SystemUI-Optimize-the-logical-b.patch
  - 0056-oto8-frameworks-base-SystemUI-Modify-all-icons-backg.patch
  - 0062-oto8-frameworks-base-SystemUI-Bind-taskbarIcon-to-Pa.patch
  - 0063-oto8-frameworks-base-SystemUI-Restruct-the-code-abou.patch
  - 0064-oto8-frameworks-base-SystemUI-Fix-bug-that-a-new-ico.patch
  - 0069-oto8-frameworks-base-Change-exception-method-of-seri.patch
  - 0071-oto8-frameworks-base-SystemUI-Fix-bug-that-locked-ic.patch
  - 0081-oto8-frameworks-base-SystemUI-Fix-some-bugs-like-tas.patch
  - 0087-oto8-frameworks-base-Status-bar-icons-maybe-occlusio.patch
  - 0088-oto8-frameworks-base-Modify-the-status-bar-layout.patch
  - 0102-oto8-frameworks-base-Change-notification-to-show-the.patch
  - 0105-oto8-frameworks-base-Change-touch-insets-of-StatusBa.patch  
  - 0110-oto8-frameworks-base-Set-the-systemui-notification-w.patch
