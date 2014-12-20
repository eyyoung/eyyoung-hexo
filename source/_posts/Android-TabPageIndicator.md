title: Android TabIndicator
---
已实现非ViewPager的TabIndicator.

主要用于在页面非ViewPager情况下的指针展示
效果如下图：

![示例图](https://raw.githubusercontent.com/eyyoung/AndroidTabIndicator/master/sample.gif)

<!-- more -->

自定义属性包括：
paddingleft (right/top/bottom): 标题TextView padding
textAppearance : 标题样式，当继承于系统TextAppearance
defaultTextColor : 默认文字颜色（非选中态）
indicatorColor：指针颜色
indicatorBackColor：指针背景颜色
indicatorHeight：指针高度
dividerWidth：分割线宽度（默认为0）
dividerColor：分割线颜色
dividerMargin：分割线上下margin值
使用方法：
xml配置

    <cn.com.nd.tabindicator.library.TabIndicator xmlns:indicator="http://schemas.android.com/apk/res-auto"
            android:id="@+id/tabIndicator"
            android:layout_width="match_parent"
            android:layout_height="48dp"
            indicator:indicatorHeight="4dp"
            indicator:dividerWidth="1dp"
            indicator:dividerMargin="5dp"
            indicator:defaultTextColor="@color/color_3"
            indicator:dividerColor="@android:color/holo_red_light" />

代码中实现

    mTabIndicator = (TabIndicator) findViewById(R.id.tabIndicator);
    mTabIndicator.setUp(new String[] { "TITLE", "TITLE2", "TITLE3" }, this);

自带onPageChange

如果使用ViewPager可采用
https://github.com/JakeWharton/Android-ViewPagerIndicator
项目GitHub地址：
https://github.com/eyyoung/AndroidTabIndicator
