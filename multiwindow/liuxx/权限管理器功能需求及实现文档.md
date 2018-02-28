# 权限管理器功能需求及实现文档

## 内容
  - 功能需求
  - 设计实现
  - 项目进展
  
### 功能需求
  - 开发权限管理器app，实现动态管理第三方应用权限
  
### 设计实现
  - 界面
    - 采用左右分栏结构，左侧显示应用，右侧显示应用权限信息列表
    - 权限列表最上面显示当前应用的name、packageName、version，下面根据权限类型归类成calendar、contacts、phone、sms、storage、device、bootup、personal、mms、media等集合，每种集合设统一开关控制当前集合下的所有权限开、关。
    - 代码实现位置：packages/apps/OtoAppOpsXposed/res/layout/app_ops_summary.xml
  - 功能
    - 左侧应用列表只显示第三方应用，实时监控应用的安装、卸载、更新，并及时更新列表信息
      - 代码实现位置：packages/apps/OtoAppOpsXposed/src/com/android/settings/applications/AppOpsSummary.java
    - 右侧权限列表显示应用的所有权限，分类别显示，同一类别权限设统一开关，控制全部所属权限
      - 代码实现位置：packages/apps/OtoAppOpsXposed/src/com/android/settings/applications/AppOpsDetails.java
    - 界面右上角添加“显示变更项”按钮，点击进入权限变化的应用列表界面，此界面右上角设“恢复全部权限”按钮，点击弹出提醒窗口
      - 代码实现位置：packages/apps/OtoAppOpsXposed/src/at/jclehner/appopsxposed/AppListFragment.java
  - 界面效果图
  ![](https://github.com/openthos/multiwin-analysis/blob/master/pictures/oto_privacy.png)
  
### 项目进展
  - 权限管理器第一个版本的界面已完成
  - 权限管理器第一个版本的动态控制权限“允许、拒绝”的需求已完成
  - 存在问题：
    - openthos1.1版本系统提供的可控制权限不全，比如“存储权限”未提供，所以权限管理器无法动态控制“存储权限”
    - 权限权限管理器部分权限更改无效，例如qq、微信禁掉录音权限，应用仍然可以正常录音
      - 调研发现，Android是从6.0增加动态控制权限的全面支持，6.0以下支持不完善，而有的第三方应用在6.0以下版本运行，对权限的管理只是判断权限是否注册，而不考虑权限是否allowed，所以权限管理器更改此类权限时，应用本身没影响。
    - 对于关闭权限，应用保证正常运行，同时获取一套指定的伪造数据；调研此需求发现：
      - 应用运行在Android6.0以上，应用会考虑权限是否注册且allowed，而6.0一下版本，有的应用会考虑权限是否注册且allowed，而有的应用只考虑权限是否注册，这些都是应用本身自己的处理，如果想要关闭权限，应用正常运行，那权限管理器就不能彻底关闭权限，只能是在操作关闭权限时，权限实际仍然allowed，只是在应用获取位置，或拍照时，给应用提供一套指定的伪造数据；但是应用获取位置、拍照等最终的数据都是从c层返回来的，所以想要在应用端控制c层返回的数据，难度就有些大了。
