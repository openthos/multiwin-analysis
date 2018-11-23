### GFXBench apk 反汇编调用流程

反汇编com.glbenchmark.glbenchmark.apk, 出现错误提示“Uploading device information:MISSING_PARAMETER”代码调用流程：
检查UploadDeviceInfoTask.java：status.good()，
Status.java：good()具体实现{netmanlibJNI.Status_good(this.swigCPtr(1), this)}，this.swigCPtr (1)默认是Flag.DEVICEINFO_FAILED，然后通过下面赋值：
this.swigCPtr(1) = Services.java：netmanlibJNI.Services_getDeviceInfo(this.swigCPtr(2), this, deviceInfo, recievedInfo)；
this.swigCPtr(2) = Services.java：netmanlibJNI.Services_instance() ；
如果status.good()返回false，则界面会提示“Uploading device information:”+ status.toString();
Status.java：status.toString()表示不同的出错状态其中包括“MISSING_PARAMETER”。
status.toString()具体实现{netmanlibJNI.Status_toString(this.swigCPtr(1), this)}。
status的赋值
UploadDeviceInfoTask.java：Status status = services.getDeviceInfo(props.toJsonString(), recievedInfo);
props赋值，MinimalProps.java：props.collect(new AndroidDeviceInfoCollector(context), this.sysinf), 
collect具体实现Properties.java：systeminfolibJNI.Properties_collect(this.swigCPtr, this, DeviceInfoCollector.getCPtr(deviceInfoCollector), deviceInfoCollector, SystemInfo.getCPtr(systeminfo), systeminfo)
AndroidDeviceInfoCollector.java中收集device info，包括OsInfo、DisplayInfo、CpuInfo、GpuInfo、MultiGpuInfo、MemoryInfo、StorageInfo、BatteryInfo等信息，应用获取CpuInfo，StorageInfo方式，
我通过demo测试，在运行不正常的设备S1上，同样可以获取到CpuInfo，StorageInfo的信息
