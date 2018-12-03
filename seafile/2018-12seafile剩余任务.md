# service：
  - 服务器不能创建同名库
  - DATA和UserConfig这两个库不可删除，不可重命名
  - 修改创建账号的默认模板（新建账号，默认创建DATA和UserConfig）
  - UserConfig设置隐藏属性，并修改list-remote和增加list-remote-a

# client：
## seafile keeper
  - 增加容量检查 80% 90% 两道门槛
    - 增加到80%，提醒用户清理
    - 达到90%同步停止，并告知用户
    - 降低到80%，重新启动运行
  - clone状态下被打断后，提供继续正常运行云服务功能的操作
  - 待机后无法自动运行云服务
  - 去掉密码
  - 增加list-remote-a
  
## seafile GUI
  - 配合seafile keeper针对容量做一套提醒机制
  - 云服务的守护进程
  - 通知中心显示有时和实际状态不一致
  - 设置中修改openthos服务器为235或158，无法再将服务器切回dev，仍显示之前的服务器
  - 去掉密码

# 综合：
  - Oauth验证踢掉其他客户端的问题
  - 目前云服务无法通过文件管理器上传、删除和变更文件，可能和当前版本的kernel没有inotify有关

# 备份还原
  - 应用的还原（由于应用商店的应用太少，暂时搁置）
