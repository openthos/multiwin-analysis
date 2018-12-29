# Seafile 云容量quota需求、实现文档

### 需求
  - seafile云容量配额2G，seafile 本地空间增加容量检查 80% 90% 两道门槛
    - seafile 本地空间使用占比超过80%，提醒用户
    - seafile 本地空间使用占比超过90%，seafile 本地空间不允许再写入文件。
    
### 实现
  - openthos 2.0 增加Ext4 Project Quota磁盘配额支持
    - 通过project quota 对seafile本地空间设置soft limit、hard limit，以达到seafile本地空间增加容量检查 80% 90% 两道门槛需求
  - openthos现内核配置quota netlink选项已经打开，安装时使用project quota特性格式化data分区，启动时将使用prjquota特性挂载data分区
  ，prjquota挂载选项表示project quota的limit功能已经自动开启，毋需quotaon或quotaoff进行额外开关操作，具体实现：
    - 1.先配置好运行环境
      - 设置环境变量：
        - export LD_LIBRARY_PATH=$PWD/system/lib （只在找不到运行库时设置，默认不用设置）
        - export TMPDIR=/data/local/tmp
      - 创建data分区设备的软链接：
        - [  -h /dev/sdb3 ] || ln -s /dev/block/sdb3 /dev
  - 配额创建
    - 2.文件和目录设置project id
      - 对待容量配额的文件夹，创建project id，如666，之后在该文件夹增加的文件和目录都将继承该project id
        - chattr -p 666 /mnt/dir 
        - lsattr -p /mnt
        
              123 --------------e---- /mnt/dir
              此时可以看到dir project id已经设置为123了，而没有明确设定的project id的文件默认为0。与此同时，此时的dir目录并没有project id的继承属性，在该目录下创建的文件不会继承父目录的project id
      - 下面通过设置P属性开启dir目录的project id继承属性：
        - chattr +P /mnt/dir, 这样在dir目录下新创建的文件或子目录都将有用dir的project id，P继承属性也会同时继承，不过设置之前的不会改变。
    - 3.配置project quota限额
      - setquota -P 123 soft_limit hard_limit inode-softlimit inode-hardlimit /dev/block/sdb1
    - 4.限额溢出演示
      - repquota -P /dev/block/sdb1
      
            *** Report for project quotas on device /dev/block/sdb1
            Block grace time: 00:05; Inode grace time: 00:05
            Project         used    soft    hard  grace    used  soft  hard  grace
            ----------------------------------------------------------------------
            #0        --      20            0           0              2     0     0       
            #123      --       4   10240   20480              2     5    10

      

### 参考文档
[Ext4 Project Quota磁盘配额使用参考](https://blog.csdn.net/luckyapple1028/article/details/75754591)
