## GFXBench OpenGL在oto2.0问题汇总

### 运行环境
1.openthos2.0，aosp8.1

### 运行问题
1.在S1、AMD设备上，打开应用，一段时间后出现“missing parameters”字段，然后提示“无法连接到服务器”.
    
    在kernel4.16正常，4.16.y不正常；
    在kernel4.17正常，4.17.y不正常；
    在kernel4.18正常，4.18.y不正常；
    在kernel4.19不正常。

2.在aosp8.1上，kernel4.19.3版本下，在S1、AMD设备上，此应用依然存在“无法连接到服务器”的问题。

3.openthos2.0版本2.0.0.181106 kernel版本>4.18, 在T45设备上，打开应用，在测试跑分过程中，弹出“应用停止运行”信息，在最后一项测试结果中显示“out of memory”字段。

### 问题定位
  - 问题1分析
    
    1.由于此问题没有明显的crash，单纯的从log中无法发现问题出在哪里，刚开始只发现在S1设备上有这个问题，而其他的同方设备上没有此问题，猜测是不是和硬件
    有关，然后在s1上安装凤凰Os，应用运行正常，证明此问题出在oto2.0上，但与设备也相关联。
    
    2.提示“无法连接到服务器”，猜测是否与网络有关，而s1设备没有Ethernet网卡，只有wifi，通过更换不同wifi测试，此问题依然存在；但在另一台x86平板
    上(无Ethernet网卡)测试，应用运行正常，不存在此问题；后来测试发现在AMD设备上也存在此问题，且AMD设备有Ethernet网卡。最终排除网络问题。
    
    3.凤凰Os上此应用能正常运行，对比运行环境，kernel、mesa有比较明显的区别。测试定位oto2.0在8月份版本已经存在此问题，8月份之前的版本在s1上无法
    安装，只能u盘启动，通过u盘启动测试，定位到在kernel4.15.18，Mesa13.0.0版本下，正常运行；在kernel4.17.2，Mesa13.0.6版本下，运行不正常。
    所以猜测此问题与kernel的版本有关联。最后通过kernel不同版本测试，得出以下结果：
    
        在kernel4.16正常，4.16.y不正常；
        在kernel4.17正常，4.17.y不正常；
        在kernel4.18正常，4.18.y不正常；
        在kernel4.19不正常。
    
  - 问题2分析
  
    1.测试发现在最新的kernel4.19镜像上存在此问题，而我在“小盒子”上运行kernel4.17版本，不存在此问题，初步判断问题出在kernel4.19版本上，通过用最
    新的代码编译不同的kernel版本镜像，定位问题到kernel>4.18时存在此问题。
    
    2.通过测试openthos2.0(版本2.0.0.181115)，发现此问题已经解决，不存在了，当前版本运行环境kernel4.19， Mesa 18.1.9，对比版本2.0.0.181106
    运行环境kernel4.19， Mesa 18.1.8，猜测是Mesa升级解决了此问题，通过编译最新版本镜像，Mesa切到版本18.1.8，此问题依然存在，所以猜测是Mesa
    18.1.8引起的此问题，而Mesa升级到18.1.9后，此问题又不存在了。

### 问题1调试方法
  - 通过logcat抓取正常版本和不正常版本的log，对比是否有diff来协助分析问题。
  
    通过logcat -v threadtime 分别抓取了设备S1上kernel4.18版本、kernel4.19版本log，对比两个log，两者没有区别，两个kernel版本下获取的device info（包括cpu、gpu、内存、显卡、电池等）都是相同的，排除device info与应用的关联。
    
  - 通过反汇编apk，协助分析问题
  
    使用jadx工具，反汇编apk，分析应用初始化方法调用流程，尝试定位问题，最终结果发现应用java代码调用流程，在正常、不正常版本下都一样，最终决定
    应用能否初始化成功的方法“status.goog()“是libnetman_jni.so库中的函数，方法”status.goog()“在so库中的具体实现无法抓取，从而无法定位到问
    题的关键点。
    
  - 通过反汇编apk获取应用全部文件，尝试通过Android studio运行应用，借助debug调试来协助分析问题，由于一些应用没有gradle，Android studio无法
  运行应用，可以尝试通过eclipse运行应用，协助调试。
  
  - 通过bisect定位kernel中bad commit
  
    刚开始测试kernel4.16.3不正常，kernel4.15正常，通过bisect kernel4.16.3 kernel4.15定位bad commit56913b1，但是通过测试kernel4.16
    、4.17、4.18都是正常的，kernel4.19不正，猜测bisect可能误判了，重新bisect kernel4.19 kernel4.18定位到bad commit301d328，最终定位的
    结果：
    
        *在kernel4.16正常，4.16.y不正常；
            First bad commit 56913b1
        *在kernel4.17正常，4.17.y不正常；
            First bad commit ?? (未確認)
        *在kernel4.18正常，4.18.y不正常；
            First bad commit dcd1b109
        *在kernel4.19不正常。
    基本上，每个branch的first bad commit都不同，而且直接将bad commit在该branch的tip做revert，也无法修正问题，这表示可能有多重原因造成此问题
 
 - 通过指令”strace -p `ps | grep com.glbenchmark.glbenchmark27 | awk '{print $2}'` -o /data/gl.txt“，来分析kernel4.18 和
 kernel4.19应用在不同kernel和不同硬件的syscall记录，通过对比syscall来协助分析问题。
 
        
        
        
        
        

