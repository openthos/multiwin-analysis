# Android [静态分析] smali 插桩调试apk
  - 1.通过apktool d com.glbenchmark.glbenchmark.apk 获取反编译代码
  - 2.插入smali代码（log输出采集）
  - 3.通过apktool b com.glbenchmark.glbenchmark回编译获取apk
  - 4.获取的apk需要重新签名才可以使用
  - 5.安装重新打包的apk，查看log输出。
  - 通过尝试，目前只能达到简单的log输出（输出的内容不涉及jni调用），如果输出的内容涉及到jni调用，或者直接修改smali代码，重新打包的apk打开会crash。
  
  - [Android 静态分析smali插桩 参考文档](https://www.bodkin.ren/index.php/archives/560/)
  - [Apktool 配置使用参考文档](https://ibotpeaches.github.io/Apktool/install/)
