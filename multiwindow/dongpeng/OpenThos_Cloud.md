# openthos云需求文档
  - 介绍
  - 功能需求

# 介绍
  - openthos云是基于seafile实现的一个网盘存储app，在保留seafile主页面功能同时，对其部分功能进行了拆分和扩展，提高用户使用习惯
# 功能需求
|功能模块|描述|
|---|---|
|登录|登录页面内置一个网址，不在展示，账户和密码均来自openthos 账户联名登录|
|主页面|原有功能只有文件一个library,扩展功能为：文件，传输，我的三个模块|

# 登录
![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_cloud_login.png)
  - 账户和密码的正确与错误，现阶段是与内网192.168.0.158服务器校验的，默认外网为dev.openthos.org
# 主页面
![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_cloud_home.png)
  - 文件
    - 文件 文件夹数据来自openthos服务器数据
    - 文件常规功能
      - 下载：将其添加到下载列表中，并进行下载。
      -  删除： 删除此library&云端，的文件，并保持同步
  - 传输
    - 下载中 
      - 下载标识符
      - 暂停开始按钮
      - 文件删除功能
    - 上传中
      - 上传标识符 
      - 暂停 开始按钮
      - 文件删除功能
    - 已经完成 
      - 文件删除功能
  - 我的
    - 展示网盘使用量
    - 账户名称
    - 注销账户



