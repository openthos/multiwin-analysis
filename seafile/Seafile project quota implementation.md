# Seafile 云容量quota需求、实现文档

### 需求
  - seafile云容量配额2G，seafile 本地空间增加容量检查 80% 90% 两道门槛
    - seafile 本地空间使用占比超过80%，提醒用户
    - seafile 本地空间使用占比超过90%，seafile 本地空间不允许再写入文件。
    
### 实现
  - openthos 2.0 增加Ext4 Project Quota磁盘配额支持
    - 通过project quota 对seafile本地空间设置soft limit、hard limit，以达到seafile本地空间增加容量检查 80% 90% 两道门槛需求
  - 具体实现：
    - 1.准备文件系统启用project和quota属性
      - 用户可以通过mkfs.ext4来格式化新的文件系统，也可以通过tune2fs对老的文件系统进行update而不影响到现有文件系统中的文件：
        - mkfs.ext4 -O project,quota /dev/sdb3   //格式化新文件系统
        - tune2fs -O project,quota /dev/sdb3     // 对老的文件系统进行update
      - 文件系统格式化完毕以后，使用dumpe2fs工具确认该文件系统已经支持pqoject和quota属性：
        - dumpe2fs -h /dev/sdb3
            
              ...
              Filesystem features:      has_journal ext_attr resize_inode dir_index filetype extent 64bit flex_bg sparse_super large_file huge_file uninit_bg dir_nlink extra_isizequota project
              ...
    - 2.挂载文件系统
      - mount -o prjquota /dev/block/sdb1 /mnt/
      - mount | grep sdb1
      
            /dev/block/sdb1 on /mnt type ext4 (rw,relatime,prjquota,data=ordered)
    - 3.文件和目录设置project id
      - 创建一个目录dir和文件file，然后设置它们的project id为123：
        - chattr -p 123 /mnt/dir 
        - lsattr -p /mnt
        
              123 --------------e---- /mnt/dir
              此时可以看到dir project id已经设置为123了，而没有明确设定的project id的文件默认为0。与此同时，此时的dir目录并没有project id的继承属性，在该目录下创建的文件不会继承父目录的project id
      - 下面通过设置P属性开启dir目录的project id继承属性：
        - chattr +P /mnt/dir, 这样在dir目录下新创建的文件或子目录都将有用dir的project id，P继承属性也会同时继承，不过设置之前的不会改变。
    - 配置project quota限额
      - setquota -P 123 soft_limit hard_limit inode-softlimit inode-hardlimit /dev/block/sdb1
    - 限额溢出演示
      - repquota -P /dev/block/sdb1
      
            *** Report for project quotas on device /dev/block/sdb1
            Block grace time: 00:05; Inode grace time: 00:05
                        Block limits                File limits
            Project         used    soft    hard  grace    used  soft  hard  grace
            ----------------------------------------------------------------------
            #0        --      20            0           0              2     0     0       
            #123      --       4   10240   20480              2     5    10
            --------------------- 

      
