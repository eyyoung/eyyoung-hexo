title: Android UI Tips Log
---
积累更为高效的Android编码习惯。
  - 9图编辑必须做全，如只做左侧不做上册是不标准的，最新BuildTool将无法支持该类型图片
  - PullToRefresh过时，慎用（扩展性差，定制的ROM有弹性效果易产生冲突）
  - ActionBarPullToRefresh过时，慎用（ActionBar只有一个，界面内可能有多个Page需要进度）
使用SwipeRefreshLayout，Support包，已支持Material Design风格
SwipeRefreshLayout
  - 2.x 4.x 5.x风格
Android版本不断更新的情况下，风格是不断变化的
2.x 风格大乱斗
4.x Holo风格
5.x Material Design风格
Dialog风格不一致
解决方案：各主题使用style、style-v14、style-v21做适配
<!-- more -->
  - Support v4包（不仅仅是ViewPager）
   1. SwipeRefreshLayout
如上
   2. NotificationCombat
通知栏兼容
   3. LocalBroadcastManager
单应用广播，类EventBus，但依然只支持String
   4. PagerTitleStrip，PagerTabStrip
类TabPageIndicator，但原生支持ViewPager Api，更方便
   5. DrawerLayout
抽屉菜单，支持ActionBarToggle，使用MD风格后动画效果极赞
   6. SlidingPaneLayout
滑动面板（音乐播放器中常见的）
   7. RoundedDrawableFactory
圆角图像生成
  - Support appcompat v7
UI兼容库，类似于ActionBarSherlock
最新版本开始使用MaterialDesign作为主题
  - RecyclerView support
ListView替代品
http://developer.android.com/training/material/lists-cards.html
  - CardView support
阴影效果
圆角效果
  - MultiDex support
官方DEX分包支持库
解决65535数量限制
需确保BuildTool版本处于最新状态
  - 使用ListView 的setEmptyView方法
  - 可使用attr让主公程与子工程公用资源
  -  ImageAssets Generator
http://romannurik.github.io/AndroidAssetStudio
