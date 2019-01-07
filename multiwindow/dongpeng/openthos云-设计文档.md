# openthos云 设计文档
  - openthos云 设计继续沿用seafile原有逻辑，在不改动大的原有逻辑基础上进行调整来满足需求<br>
  设计主要分为：
  - 登录 （登录当前的进展完成12月26号节点进度100％）
      - 添加账户页面
      - 选择服务器页面
      - 登录页面
  - 主页面（主页面当前进度 完成12月26号节点进度的50％）
      - 左侧菜单栏
      - 右侧数据展示
    
### 登录
  - 首次登录继续使用原有逻辑，登录到主页面进行第一次token为空判断，代码设计如下：
    - 判断令牌token是否为空 ，为空进入 添加账户页面，选择服务器页面， 登录页面
      ```java
      if (account == null | !account.hasValidToken()) { //return !textUtils.isEmpty(token)
          finishAndStartAccountActivity();
          return;
      }
      ```
        - 添加账户页面
          - 添加openthos云 logo
          - 页面左对齐 
          - 添加账户按钮位置微调
          ```java
          //触发添加帐号按钮,由系统AccountManager处理帐号安全问题 （原有逻辑）
          android.accounts.AccountManager.addAccount(string,string,string[] ,Bundle,activity,AccountManagerCallback<Bundle>);
          ```
        - 选择服务器页面
          - 去掉seafile 服务器
        - 登录页面
          - 默认展示openthos云服务器网址 
  - flash页面
    - 替换原seafile main action 每次打开app展示FlashActivity 2000毫秒
  
            
### 主页面
  - 主页面布局设计:
    - 线性布局的横向展示
    - 添加title 快捷操作条目
    - 左右分栏 ，左边展示资料库，最近修改，暂定
    - 右边展示: 对应左边条目的data
    
  ```java
  <LinearLayout
      ... 
      android:orientation="horizontal" >
      
      <!-- left menu-->
      <LinearLayout>
      </LinearLayout>
      
      <!-- right menu -->
      <LinearLayout>
      </LinearLayout>
      
   </LinearLayout>   
  ```
   
   
  - 资料库
    - viewpager + fragment
    - ReposFragment  资料库对应的fragment
    - ActivitiesFragment 最近修改的fragment
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
    
  - 资料库
    - 根据登录帐号展示所有数据(和服务器数据同步)分为： 个人数据 和 共享数据
      - 个人数据 (展示服务器个人目录下的文件夹)
      - 文件夹二级目录分为以下几种选中状态
      - 长安状态
      - 单击状态
      - 右边功能按钮列表   
      - 共享数据
    - 星标记
      - 展示在资料库中进行标记过的文件
        - 根据星标记数据大于1的条件，来决定是否显示和隐藏，主页面头部排序按钮
    - 最近修改
      - 展示在资料库中最近修改的数据
 
  - 主页面分为资料库，星标记，最近修改(默认隐藏状态)
    - 资料库展示所有的服务器中所拥有和创建的目录
    - 星标记 展示在资料库中进行标记的文件
    - 最近修改 展示资料库中最近修改过的文件
