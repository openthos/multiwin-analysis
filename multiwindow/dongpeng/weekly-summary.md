# 2018/12/10-2018/12/14  周报告（董鹏）
  - 本周主要任务分析了openthos云（seafile） Main Activity 以及相关的布局和代码逻辑问题
  - 从登录页面到main activity 架构用了viewpager+fragment 和多个的布局嵌套，其耦合度比较高，需要梳理逻辑进行解耦
  - 在开发中遇到改动主页面的时候，影响到登录页面的显示和正常使用，
