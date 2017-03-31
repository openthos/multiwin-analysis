## Android 窗口管理服务WindowManagerService切换Activity窗口的过程分析
  - ActivityStack.startActivityLocked 方法进行切换操作，是否切换根据情况来定。

    if (!r.mLaunchTaskBehind && (taskForIdLocked(taskId) == null || newTask)) {// 最后的task 和 AMS 正在 进行任务。
          insertTaskAtTop(rTask);
          mWindowManager.moveTaskToTop(taskId);
    }
  - 在startActivityLocked的方法中 有较多的关于Activity栈的处理。

## AMS 调用架构
  - 代理模式
    - 代理类 和 被代理类共同实现一个类： IActivityManager.java 接口类
    - 代理类： ActivityManagerProxy 被代理类： ActivityManagerNative(操作)
    - Client : ActivityManager.java
  - 逻辑图：
  ![ams -](ams.png)
  - AMS: [AMS大概分析](AMS.md)

## Activity窗口的四个按键（最大 最小 返回 关闭） 
  - Activity组件的UI是使用一个PhoneWindow对象来描述的。PhoneWindow:
      - PW继承 Window类，因此它可以保存Activity类的成员变量mWindow中。
  - 实现方案（模仿Openthos)
   **ActivityManagerNative.java中添加关闭的方法**

   public boolean closeActivity(int stackId) throws RemoteException {

        Parcel data = Parcel.obtain();
        Parcel reply = Parcel.obtain();
        data.writeInterfaceToken(IActivityManager.descriptor);
        data.writeInt(stackId);
        mRemote.transact(CLOSE_ACTIVITY_WITH_WINDOW_TRANSACTION, data, reply, 0);
        reply.readException();
        boolean[] ret = new boolean[1];
        reply.readBooleanArray(ret);
        data.recycle();
        reply.recycle();
        return ret[0];
    }

  **AMS extends AMN**

    @Override
    public boolean closeActivity(int stackId) {
              boolean succeed;
              long ident = Binder.clearCallingIdentity();
             StackInfo stack = getStackInfo(stackId);
               if (stack != null) {
                      for (int next = stack.taskIds.length - 1; next >= 0; --next) {
                             removeTask(stack.taskIds[next]);
                           }
                     succeed = true;
                  } else {
                           succeed = false;
                       }
               Binder.restoreCallingIdentity(ident);
              return succeed;
    }

  **PhoneWindow 中进行调用**

     mCloseBtn = (ImageButton)mDecorView.findViewById(com.android.internal.R.id.mwCloseBtn);
     mCloseBtn.setOnClickListener(new OnClickListener() {
         ...
         ActivityManagerNative.getDefault().closeActivity(getStackId());
         ...
     }

***

### 阶段一：
         对于最大化和最小化的逻辑主要是对窗口的添加组件。

***

### multiWindow的 focus
    - setFocusStack

    问题： 当APP 窗口缩小时，点击桌面，这是桌面获得焦点，APP窗口就会隐藏。不符合多窗口的特点
         Openthos 采用的策略是 将 桌面放入栈底。但是  在桌面重命名的开发中仍有桌面获不到焦点的问题.
    
    - 代码： 位置 ActivityStackSupervisor.java

    void setFocusStack(ActivityRecord r, String reason) {
        ...
        moveHomeStack(isHomeActivity, reason);
    }

    void moveHomeStack(boolean toFront, String reason) {
        ArrayList<ActivityStack> stacks = mHomeStack.mStacks;//放入集合中
        //这里进行逻辑判断处理。
        //Openthos中仍有一个疑惑在： StartupMenu中应用打开全是全屏情况。
    }

### stack 创建
         - adjustStackFocus 每次生成新窗口，强制启动一个新的stack。 位置： ActivityStackSupervisor.java
         - 代码
    ActivityStack adjustStackFocus(ActivityRecord r, boolean newTask) {
        //让所有的Activity在一个栈中。
        ...
        final ActivityContainer container = r.mInitialActivityContainer;
        if (container != null) {
            //第一次把它放在所需的堆栈,这之后放在任务堆栈。
            r.mInitialActivityContainer = null;
            return container.mStack;
        }
        ArrayList<ActivityStack> homeDisplayStacks = mHomeStack.mStacks;
        for (int stackNdx = homeDisplayStacks.size() - 1; stackNdx >= 0; --stackNdx) {
            final ActivityStack stack = homeDisplayStacks.get(stackNdx);
            if (!stack.isHomeStack()) {
                ...
                mFocusedStack = stack;
                return mFocusedStack;
            }
        }
    }

### stack 创建大小调整
  - android 6.0 : resizeStack调整 全屏； getInitializingRect函数 生成窗口大小和位置，再调用resizeStack
  - getInitializingRect 使窗口层叠出现。同时调用WMS的getDisplayMetrics依据屏幕进行窗口大小位置的调整。
  - 在WMS中添加getDisplayMetrics方法。


