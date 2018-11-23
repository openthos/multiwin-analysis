## GFXBench apk 反汇编调用流程

### 反汇编com.glbenchmark.glbenchmark.apk, 出现错误提示“Uploading device information:MISSING_PARAMETER”代码调用流程：

1.检查UploadDeviceInfoTask.java：status.good()，
  - Status.java：good()具体实现{netmanlibJNI.Status_good(this.swigCPtr(1), this)}，this.swigCPtr (1)默认是Flag.DEVICEINFO_FAILED
  ，然后通过下面赋值：
    - this.swigCPtr(1) = Services.java：netmanlibJNI.Services_getDeviceInfo(this.swigCPtr(2), this, deviceInfo, recievedInfo)；
    - this.swigCPtr(2) = Services.java：netmanlibJNI.Services_instance() ；

2.如果status.good()返回false，则界面会提示“Uploading device information:”+ status.toString();
  - Status.java：status.toString()表示不同的出错状态其中包括“MISSING_PARAMETER”。
    - status.toString()具体实现{netmanlibJNI.Status_toString(this.swigCPtr(1), this)}。

3.status的赋值
  - UploadDeviceInfoTask.java：Status status = services.getDeviceInfo(props.toJsonString(), recievedInfo);
    - props赋值，MinimalProps.java：props.collect(new AndroidDeviceInfoCollector(context), this.sysinf),    
      - collect具体实现Properties.java：systeminfolibJNI.Properties_collect(this.swigCPtr, this,
        DeviceInfoCollector.getCPtr(deviceInfoCollector), deviceInfoCollector, SystemInfo.getCPtr(systeminfo), systeminfo)
  - AndroidDeviceInfoCollector.java中收集device info，包括OsInfo、DisplayInfo、CpuInfo、GpuInfo、MultiGpuInfo、MemoryInfo
  、StorageInfo、BatteryInfo等信息，应用获取CpuInfo，StorageInfo方式，
  
4.通过demo测试，在运行不正常的设备S1上，同样可以获取到CpuInfo，StorageInfo等的信息，且通过对比，在正常、不正常版本上java 方法的调用过程相同，
  获取的device info也相同，最终决定 应用能否初始化成功的方法“status.goog()“是libnetman_jni.so库中的函数，方法”status.goog()“在so库中的具体
  实现无法抓取，从而无法定位到问 题的关键点。
