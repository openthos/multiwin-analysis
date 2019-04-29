## Openthos Cloud 设计与实现

  - 内容
    - 项目简介
    - 功能需求
    - Openthos 设计与实现
    - 项目进展
    - 存在问题

# 项目简介
  - openthos云 是依赖开源企业云盘seafile ,进行页面调整，以及部分功能的重构，定制的一款桌面和手机两种不同风格的企业云盘.
  
# [功能需求](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/openthos_cloud_desktop_and_mobile_documents.md)
|完成|描述|模块|完成度|
|---|---|---|---|
|√| 编译seafile源码生成可用apk |编译|100%|
|√| 添加帐号|界面＆功能|100％|
|√| 选择服务器页面|界面＆功能|100％|
|√| 登录页面 |页面|100%|
|√| openthos云 界面风格替换|界面|100％|
|√| 添加app启动动画|界面|100%|
||以上为桌面版本和手机版本登录相关页面完成的进度
||以下是桌面版本当前的进展
|×| 实现openthos 云 主页面布局左右分栏效果|界面|70%|
|×| 主页面ui搭建|界面|30%|
|×| 右边菜单数据展示ui调整|界面|0%|
|×| title 按照ui添加对应的功能|页面|20%|
|×| 文件上传下载ui调整，及逻辑的迁移|界面＆功能|0％|
|×| 设置页面调整及功能|界面＆功能|0％|
|√| 星标记功能隐藏|界面＆功能|100％|
|×| 最近修改页面展示调整|界面＆功能|0％|



# [设计](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/openthos_cloud_design_document.md)

# 项目进展
  - 2018／12／03-2018／12／07
    - 完成openthos云 （seafile） 源码编译 ，生成可用apk
    - 完成app启动动画的实现
    - 完成登录页面的布局的重构
    - 完成了openthos云 （seafile） app 页面启动的顺序，规避了原有逻辑
    - [项目效果图](https://github.com/openthos/multiwin-analysis/blob/master/multiwindow/dongpeng/seafile_%E7%9B%AE%E5%89%8D%E8%BF%9B%E5%B1%95%E6%95%88%E6%9E%9C%E5%9B%BE.md)
    
  - 2018／12／10-2018／12／14
    - 主要任务 实现openthos云 （seafile） app主页面 左右布局分离
    - 实现上传下载功能
    
  - 2018/12/17-2018／12/21
    - 调研openthos云 （seafile）资料库实现的逻辑，以及函数之间的调用关系
    - 尝试修改原来主页面布局，进行数据的左右分离
    - 拆分部分主页面功能，但还存在很多问题
    
  - 2018/12/24-2018/12/29
    - 根据需求改进一版opentho云 手机版本已满足需求
    - 完成初步openthos桌面版本的左右布局分离需求，功能按键有异常
    - 完成openthos 云 需求文档
    
  - 2019/01/03/-2019/01/04
    - 设计openthos云 布局适配方案

# 存在问题
  -  由于对seafile 是边学习边开发 ，存在未知的风险，对项目预估的进展会有一定的影响。
