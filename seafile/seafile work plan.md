# 2018-12-10 ~ 2018-12-14
## 萧络元
- 网页无用元素的修改
- seafile 分享禁用
- Openthos ID网页元素完善
## 罗浩
- seaf-cli更新至最新版本
## 王之旭
- 自动备份按钮状态保存
- 备份还原建议除每小时自动备份以外增加手动备份按钮
- 备份还原增加机型
- 备份还原的选择还原文件按钮不明显
- 设置中修改openthos服务器为235或158，无法再将服务器切回dev，仍显示之前的服务器
- 点击绑定235服务器的帐号后，长时间没有任何提示响应，且之后再进入别的服务器也不会有响应，建议设置15秒超时限制
- 多次绑定解绑会出现云服务停止运行错误
- 特殊符号也能成为id
- clone过程中断的异常处理
## 卢宁
- 了解seaf-cli源码, 修改脚本，去掉密码
- 首次配置账号页面可换地址
## 刘晓旭
- 了解seaf-cli源码, 修改脚本，增加容量检查机制
## 张善民
- seafile windows client端编译
- seafile windows client修改UI一版桌面版
## 赵鹏宇
- 根据seafile的流程，出一套seafile client的ui图(配合完善细节）
## 董鹏
- openthos cloud
  - 需求分析和设计文档
  - 提供初版桌面版UI效果demo
## 范津
- seaf-cli去掉python的代码。使seaf-cli可以在android环境运行
## 王春琦
- 和萧络元完成seahub的分析


# 2018-12-03 ~ 2018-12-07
## 萧络元
- 新建用户的初始模板默认创建DATA和UserConfig
## 罗浩
- bug： Oauth验证踢掉其他客户端的问题
- feature：  服务器单用户设置空间限制，单用户默认2G
- feature： DATA和UserConfig设置特殊属性
- clone状态打断后不能正常工作
## 王之旭
- bug： 云服务备份还原
- bug： 目前云服务无法通过文件管理器上传、删除和变更文件，可能和当前版本的kernel没有inotify有关
- bug： Mount的图片和文档导致系统不能进入
- bug： Mount的图片和文档不能上传或者下载文件
- bug： 外网无法使用云服务
- bug： 文件管理器中点击云服务，光标不会选中云服务
## 卢宁
- 了解seaf-cli源码, 修改脚本，去掉密码
## 刘晓旭
- 了解seaf-cli源码, 修改脚本，增加容量检查机制
## 张善民
- seafile windows client端编译
## 赵鹏宇
- 根据seafile的流程，出一套seafile client的ui图 （windows和android）
## 董鹏
- openthos cloud
  - 增加注册入口
  - 配置文件里锁定服务器域名，gui中不提供更改，但通过某些方法可以手工修改
## 范津 王春琦
- seaf-cli 去python

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
