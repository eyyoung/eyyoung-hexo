title: 使用Attribute使得界面可配置
categories: Android
tags:
- Android
---

由于项目的版本越来越多，分支越来越多，不同的版本可能在界面上需要使用不同的图标，不同的背景，不同的界面元素高度。
为了实现界面的可配置化，需要在子工程中定义一套方案，使得主工程中对这些界面元素可配置。

具体步骤如下：
 定义Lib主题的attr
```xml
    <attr name="libTheme" format="reference" />
```
Lib中使用libTheme指定的style
```java
protected void onCreate(Bundle savedInstanceState) {
    TypedArray typedArray = getTheme().obtainStyledAttributes(
            getThemeId(this), new int[]{R.attr.libTheme});
    setTheme(typedArray.getResourceId(0, 0));
    typedArray.recycle();
    super.onCreate(savedInstanceState);
}
```
<!--more-->
根据APP中配置的libTheme获取App中使用的主题

    /**
     * 获取主题ID
     * @param ctx 上下文
     * @return
     */
    private static int getThemeId(Context ctx) {
        try {
            int themeResId;
            Class<?> clazz = ContextThemeWrapper.class;
            Method method = clazz.getMethod("getThemeResId");
            method.setAccessible(true);
            themeResId = (Integer) method.invoke(ctx);
            return themeResId;
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (IllegalArgumentException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        }
        return R.style.LibDefaultStyle;
    }
定义需要配置的项在attr中
```xml
<attr name="libTitleBarHeight" format="dimension" />
<attr name="libTitleBackground" format="color" />
```
在相应的layout中使用attr来指向对应的资源

    <LinearLayout
            android:background="?libTitleBackground"
            android:layout_width="match_parent"
            android:layout_height="?libTitleBarHeight"></LinearLayout>

    <TextView
        android:text="@string/hello_world"
        android:layout_margin="16dp"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content" />

定义一套默认的风格，避免在主工程中没有配置相应的属性导致应用崩溃
```xml
<style name="LibDefaultStyle" parent="LibBaseTheme">
    <item name="libTitleBarHeight">48dp</item>
    <item name="libTitleBackground">@android:color/holo_blue_light</item>
</style>
```
因为在不同的API版本上面需要显示不同的风格，定义LibBaseTheme在value,value-v14文件夹。
 - value文件夹
```xml
<style name="LibBaseTheme" parent="@android:style/Theme.Light.NoTitleBar"></style>
```
 - value-v14文件夹：
```xml
<style name="LibBaseTheme" parent="@android:style/Theme.Light.NoTitleBar"></style>
```

----------

#### 主工程配置 ####

主工程使用的主题中增加配置libTheme并且配置指向一个继承LibDefaultStyle的项

    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
        <item name="libTheme">@style/LibTheme</item>
    </style>

    <style name="LibTheme" parent="LibDefaultStyle">
        <item name="libTitleBackground">@android:color/holo_green_light</item>
    </style>

如果有新的配置项，在Library中使用配置新的attr并且在layout中使用?attr/
并且在主工程的LibTheme中定义该attr指向的资源

https://github.com/eyyoung/AttributeDemo
