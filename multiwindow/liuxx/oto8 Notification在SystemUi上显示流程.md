# Notification在SystemUi上的显示流程

不管是发出一个新的通知还是对已经存在的通知进行更新，调用的都是NotificationManager.notify(int id，Notification notification)。最后走到SystemUI的时候首先调用StatusBar中的成员变量mNotificationListener的onNotificationPosted(final StatusBarNotification sbn, final RankingMap rankingMap)方法。

来看mNotificationListener对onNotificationPosted(final StatusBarNotification sbn,final RankingMap rankingMap)方法的实现：
```
6583                 public void onNotificationPosted(final StatusBarNotification sbn,
6584                                                  final RankingMap rankingMap) {
6586                     if (sbn != null && !onPluginNotificationPosted(sbn, rankingMap)) {
6587                         mHandler.post(new Runnable() {
6588                             @Override
6589                             public void run() {
6590                                 if (sbn.getNotification().contentView != null) {
6591                                     mRankingMap = rankingMap;
6592                                 }
6593                                 processForRemoteInput(sbn.getNotification());
6594                                 String key = sbn.getKey();
6595                                 mKeysKeptForRemoteInput.remove(key);
6596                                 boolean isUpdate = mNotificationData.get(key) != null;
6606                                 ......
6607                                     // Remove existing notification to avoid stale data.
6608                                     if (isUpdate) {
6609                                         removeNotification(key, rankingMap);
6610                                     } else {
6611                                         mNotificationData.updateRanking(rankingMap);
6612                                     }
6613                                     return;
6614                                 }
6615                                 try {
6616                                     if (isUpdate) {
6618                                         updateNotification(sbn, rankingMap);
6619                                     } else {
6621                                         addNotification(sbn, rankingMap);
6622                                     }
6623                                 } catch (InflationException e) {
6624                                     handleInflationException(sbn, e);
6625                                 }
6626                             }
6627                         });
6628                     }
6629                 }

```
首先来看方法中的两个参数：1.StatusBarNotification sbn；2.RankingMap rankingMap。
StatusBarNotification点进去看，发现其实是由Notification组装而成，里面比较重要的属性有String pkg，int id，String key，Notification notification，保存着通知的内容，发出通知的报名信息，以及id等。

在onNotificationPosted()方法中，先对传过来的sbn进行空判断，不为空，则用mHandler发一个新的runnable来处理

String key = sbn.getKey();

mKeysKeptForRemoteInput.remove(key);

boolean isUpdate = mNotificationData.get(key) != null;

先拿到sbn里面key属性，再根据这个key去mNotificationData取对象。

跳过一部分代码，直接来到重点。
```
6616                                     if (isUpdate) {
6618                                         updateNotification(sbn, rankingMap);
6619                                     } else {
6621                                         addNotification(sbn, rankingMap);
6622                                     }
```

如果mNotificationData能通过sbn的key拿到的Entry不为空，说明这个通知已经存在了，isUpdate为true走更新流程，否则走添加流程。到此，onNotificationPosted方法就结束了。

接下来看添加流程addNotification(sbn, rankingMap)。在StatusBar中，这个方法是被这么实现的：
```
1762     public void addNotification(StatusBarNotification notification, RankingMap ranking)
1763             throws InflationException {
1764         String key = notification.getKey();
1765         if (DEBUG) Log.d(TAG, "addNotification key=" + key);
1766 
1767         mNotificationData.updateRanking(ranking);
1768         Entry shadeEntry = createNotificationViews(notification);
1769         boolean isHeadsUped = shouldPeek(shadeEntry);
1770         ......
1806     }
```

首先通过传来的StatusBarNotification notification封装构造出一个Entry对象(注意，这的notification是StabusBarNotification不是Notification，也就是onNotificationPosted传入的sbn)

Entry shadeEntry = createNotificationViews(notification);

跟过去看createNotificationViews(notification)方法实现：
```
7637     protected NotificationData.Entry createNotificationViews(StatusBarNotification sbn)
7638             throws InflationException {
7639         if (DEBUG) {
7640             Log.d(TAG, "createNotificationViews(notification=" + sbn);
7641         }
7642         NotificationData.Entry entry = new NotificationData.Entry(sbn);
7643         Dependency.get(LeakDetector.class).trackInstance(entry);
7644         entry.createIcons(mContext, sbn);
7645         // Construct the expanded view.
7646         inflateViews(entry, mStackScroller);
7647         return entry;
7648     }
```

这里面的重点是inflateViews(entry, mStackScroller)。第二个参数mStackScroller，就是SystemUI中的下拉通知栏里面所有通知以及一些其他view的父view，是StatusBar中一个成员变量。下面看inflateViews(entry, mStackScroller)的实现：
```
7204     protected void inflateViews(Entry entry, ViewGroup parent) {
7205         PackageManager pmUser = getPackageManagerForUser(mContext,
7206                 entry.notification.getUser().getIdentifier());
7207 
7208         final StatusBarNotification sbn = entry.notification;
7209         if (entry.row != null) {
7210             entry.reset();
7211             updateNotification(entry, pmUser, sbn, entry.row);
7212         } else {
7213             new RowInflaterTask().inflate(mContext, parent, entry,
7214                     row -> {
7215                         bindRow(entry, pmUser, sbn, row);
7216                         updateNotification(entry, pmUser, sbn, row);
7217                     });
7218         }
7219 
7220     }
```
看RowInflaterTask().inflate方法,该方法在RowInflaterTask中：
```
 40     public void inflate(Context context, ViewGroup parent, NotificationData.Entry entry,
 41             RowInflationFinishedListener listener) { 
 42         mListener = listener;
 43         AsyncLayoutInflater inflater = new AsyncLayoutInflater(context);
 44         mEntry = entry;
 45         entry.setInflationTask(this);
 46         inflater.inflate(R.layout.status_bar_notification_row, parent, this);
 47     }
 ```
这个row(ExpandableNotificationRow)就是最终添加到通知栏上的通知对应的view，它的布局文件是R.layout.status_bar_notification_row。

再看bindRow(entry, pmUser, sbn, row)方法实现：
```
7222     private void bindRow(Entry entry, PackageManager pmUser,
7223                          StatusBarNotification sbn, ExpandableNotificationRow row) {
7224         row.setExpansionLogger(this, entry.notification.getKey());
7225         row.setGroupManager(mGroupManager);
7226         row.setHeadsUpManager(mHeadsUpManager);
7227         row.setAboveShelfChangedListener(mAboveShelfObserver);
7228         row.setRemoteInputController(mRemoteInputController);
7229         row.setOnExpandClickListener(this);
7230         row.setRemoteViewClickHandler(mOnClickHandler);
7231         row.setInflationCallback(this);
7232         row.setSecureStateProvider(this::isKeyguardCurrentlySecure);
7233         ......
7258     }
```
看ExpandableNotificationRow.setInflationCallback(this)的方法实现：
```
1222     public void setInflationCallback(InflationCallback callback) {
1223         mNotificationInflater.setInflationCallback(callback);
1224     }
```
InflationCallback这个类,它是NotificationInflater的静态内部类,其中有方法onAsyncInflationFinished，而setInflationCallback(callback)把StatusBar做为InflationCallback的具体实现类，查看StatusBar的onAsyncInflationFinished方法实现：
```
1837     @Override
1838     public void onAsyncInflationFinished(Entry entry) {
1839         mPendingNotifications.remove(entry.key);
1840         // If there was an async task started after the removal, we don't want to add it back to
1841         // the list, otherwise we might get leaks.
1842         boolean isNew = mNotificationData.get(entry.key) == null;
1843         if (isNew && !entry.row.isRemoved()) {
1844             addEntry(entry);
1845         } else if (!isNew && entry.row.hasLowPriorityStateUpdated()) {
1846             mVisualStabilityManager.onLowPriorityUpdated(entry);
1847             updateNotificationShade();
1848         }
1849         entry.row.setLowPriorityStateUpdated(false);
1850     }
```
addEntry(entry)是重点，通过此方法把消息添加到SystemUi上，查看具体实现：
```
1820     private void addEntry(Entry shadeEntry) {
1821         boolean isHeadsUped = shouldPeek(shadeEntry);
1822         if (isHeadsUped) {
1823             mHeadsUpManager.showNotification(shadeEntry);
1824             // Mark as seen immediately
1825             setNotificationShown(shadeEntry.notification);
1826         }
1827         addNotificationViews(shadeEntry);
1828         // Recalculate the position of the sliding windows and the titles.
1829         setAreThereNotifications();
1830     }
```
addNotificationViews(shadeEntry)的方法内容如下
```
7650     protected void addNotificationViews(final Entry entry) {
7651         if (entry == null) {
7652             return;
7653         }
7663         ......
7664         // Add the expanded view and icon.
7665         mNotificationData.add(entry);
7666         updateNotifications();
7667     }
```
mNotificationData.add(entry);对应了前面分析的boolean isUpdate = mNotificationData.get(key) != null;

下面查看updateNotifications()方法的具体实现：
```
2349     protected void updateNotifications() {
2350         mNotificationData.filterAndSort();
2351 
2352         updateNotificationShade();
2353     }
```
重点在updateNotificationShade()方法上，其最终完成只把接收的最新消息的ExpandableNotificationRow添加到通知栏上面。



















