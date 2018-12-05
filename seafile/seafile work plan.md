# 2018-12-03 ~ 2018-12-07
## 萧络元
- 新建用户的初始模板默认创建DATA和UserConfig
## 罗浩
- bug： Oauth验证踢掉其他客户端的问题
- feature：  服务器单用户设置空间限制，单用户默认2G
- feature： DATA和UserConfig都有特殊属性
    - 特殊属性的Library不可删除，不可重命名
    - 增加接口返回特殊属性的Library，增加list-remote-a
    - UserConfig的特殊属性具备隐藏功能
## 王之旭
- bug： 云服务备份还原
- bug： 目前云服务无法通过文件管理器上传、删除和变更文件，可能和当前版本的kernel没有inotify有关
## 卢宁
- 了解seaf-cli源码, 修改脚本，去掉密码
## 刘晓旭
- 了解seaf-cli源码, 修改脚本，去掉密码
## 张善民
- seafile windows client端编译
## 赵鹏宇
- 根据seafile的流程，出一套seafile client的ui图 （windows和android）
## 董鹏
- openthos cloud
  - 增加注册入口
  - 配置文件里锁定服务器域名，gui中不提供更改，但通过某些方法可以手工修改

# 2018-11-26 ~ 2018-11-30
## 萧络元
- bug： 重复Mount问题，导致空间大量被占用
- feature: 默认mount图库，文档文件夹
## 罗浩
- feature： System分区只读，使Seafile可以正常工作
## 王之旭
- 云服务备份还原
- 云服务绑定帐号逻辑修改，调用新API，防止绑定OpenthosId时，踢掉其他客户端的问题
## 卢宁
- Seafile 的 异常测试，压力测试，提供汇总报告
