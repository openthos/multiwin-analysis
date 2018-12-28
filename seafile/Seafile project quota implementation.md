# Seafile 云容量quota需求、实现文档

### 需求
  - seafile云容量配额2G，seafile 本地空间增加容量检查 80% 90% 两道门槛
    - seafile 本地空间使用占比超过80%，提醒用户
    - seafile 本地空间使用占比超过90%，seafile 本地空间不允许再写入文件。
    
### 实现
  - openthos 2.0 增加Ext4 Project Quota磁盘配额支持
    - 通过project quota 对seafile本地空间设置soft limit、hard limit，以达到seafile本地空间增加容量检查 80% 90% 两道门槛需求
  - 具体实现：
    
