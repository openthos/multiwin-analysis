#编译android-x86 6.0
##使用docker，当遇到下列编译错误时
out/host/linux-x86/framework/jack-launcher.jar
JACK_JAR="out/host/linux-x86/framework/jack.jar" 
out/host/linux-x86/bin/jack-admin: line 27: USER: unbound variable
##解决方案
在进入自己的docker容器之前，定义环境变量USER，例如在～/.bashrc中添加 export USER=$(whoami)此问题解决
