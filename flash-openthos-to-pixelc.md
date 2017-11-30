# 给pixel C刷openthos-8.0步骤详解

## 源码获取：

1.在准备放置源码的目录中执行：

repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-8.0.0_r28

2.将[local_manifest](https://github.com/openthos/multiwin-analysis/tree/master/pixel-c/local_manifests)放入.repo目录中

3.执行
	
repo sync

## 编译并获取升级包：

1.在源码目录下执行：

	. build/envsetup.sh
	lunch aosp_dragon-userdebug
	make -j$(线程数) otapackage

编译完成后的otapackage为在out/target/product/dragon/aosp_dragon-ota-eng.root.zip

之后将[update-binary](https://github.com/openthos/multiwin-analysis/blob/master/pixel-c/update-binary)文件替换压缩包中位于META-INF\com\google\android目录下的同名文件。

到此我们得到了用于刷写openthos的otapackage

## 刷写(我们默认设备处于出厂状态)：

1.通过USB线连接PC，进入设置开启开发者选项，进入开发者选项开启OEM unlocking和USB debugging并认证PC

2.PC端通过adb reboot bootloader进入fastboot模式

3.执行fastboot flashing unlock并在平板端确认，以防刷写失败。

4.下载[TWRP](https://github.com/openthos/multiwin-analysis/blob/master/pixel-c/twrp-3.1.1-1-dragon.img)

5.执行fastboot flash recovery twrp.img

6.同时长按电源键和音量-键，进入菜单，之后按音量-键移动高亮条到“Reboot into Android Recovery”项，按电源键确认，进入recovery也就是TWRP

7.进入TWRP的Wipe菜单项，选择format data，输入yes，清空设备，之后回到TWRP的主菜单

8.进入Advance项目，进入ADB Sideload功能项，滑动开启ADB Sideload

9.PC端执行 adb sideload 修改好的otapackage.zip并等待刷写完成

10.选择reboot system并确认，重启进入openthos

## 开启多窗口：

进入系统，开启开发者模式并打开USB debugging，之后在PC端执行

adb shell "settings put global enable_freeform_support 1"

重启设备

## 已知问题：

键盘需要物理安装3次才能使用，第一次安装无响应，第二次安装可配对但无法连接，第三次安装之后正常使用。

启动系统时会提示系统有问题，原因尚不明

开始菜单的关机项崩溃

日历仍会崩溃

开始菜单偶尔会发生崩溃
