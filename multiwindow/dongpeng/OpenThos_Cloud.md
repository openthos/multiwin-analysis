# openthos云需求文档
  - 介绍
  - 功能需求
  - 项目进展

# 介绍
  - openthos云是基于seafile实现的一个网盘存储app，在保留seafile主页面功能同时，对其部分功能进行了拆分和扩展，提高用户使用习惯
# 功能需求
  - 项目功能图
![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_xmind_icon.png)

|功能模块|描述|
|---|---|
|登录|登录页面内置一个网址，不在展示，账户和密码均来自openthos 账户联名登录|
|主页面|原有功能只有文件一个library,扩展功能为：文件，传输，我的三个模块|
|文件|从云端获取数据，分为个人，公共资源， 共享|
|传输|从项目中的传输集合中获取数据，分为全部传输数据，传输中， 传输失败，传输暂停|
|我的|提供了注销账户，账户名展示，可用空间展示|

# 登录
![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_cloud_login.png)
  - 账户和密码的正确与错误，现阶段是与内网192.168.0.158服务器校验的，默认外网为dev.openthos.org
# 主页面
  - 文件
    - ![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_cloud_home.png)
    - 文件 文件夹数据来自openthos服务器数据
    - 文件常规功能
      - 下载：将其添加到下载列表中，并进行下载。
      -  删除： 删除此library&云端，的文件，并保持同步
  - 传输：打开此library 自动获取下载&上传列表中的数据并展示
    - ![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_transs_icon.png)
    - 下载中 ： 
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
    - ![](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_img/openthos_account.png)
    - 展示网盘使用量
    - 账户名称
    - 注销账户
# 项目进展
  - 登录模块已完成
  - 主页面整体框架已完成
  - 文件：文件功能沿用seafile，部分细节功能待优化
  - 传输列表：待完成
  - 我的模块：ui布局已实现，功能待完善
