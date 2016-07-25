---
title: NavigationView的简单使用
date: 2016/07/12
---
 
## 简介
>SDK文档：Represents a standard navigation menu for application. The menu contents can be populated by a menu resource file.

NavigationView可以用来快速的创建一个导航菜单。菜单的内容可以通过一个menu资源文件来配置。

## 使用示例
### 构建布局文件
NavigationView一般会以[DrawerLayout](https://developer.android.com/reference/android/support/v4/widget/DrawerLayout.html)做为容器，然后和[Toolbar](https://developer.android.com/reference/android/support/v7/widget/Toolbar.html)配合使用。

```xml
<?xml version="1.0" encoding="utf-8"?>
<android.support.v4.widget.DrawerLayout android:id="@+id/dlyt_container"
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="false">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <android.support.v7.widget.Toolbar
            android:id="@+id/view_toolbar"
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            android:background="?attr/colorPrimary"
            android:theme="@style/ThemeOverlay.AppCompat.ActionBar"
            app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />

        <FrameLayout
            android:id="@+id/flyt_content"
            android:layout_width="match_parent"
            android:layout_height="match_parent" />
    </LinearLayout>

    <android.support.design.widget.NavigationView
        android:id="@+id/view_navigation"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        app:headerLayout="@layout/drawer_header"
        app:menu="@menu/menu_main" />
</android.support.v4.widget.DrawerLayout>
```

下面我们分析一下几个布局和View的具体作用：

* DrawerLayout
这里以DrawerLayout做为最外层的容器，需要注意的是这里面*android:fitsSystemWindows*这个参数，这个参数的作用是根据系统的窗口调整布局，例如系统的状态栏。关于这个参数的详细说明可以参考[fitsSystemWindows参数的详解](https://medium.com/google-developers/why-would-i-want-to-fitssystemwindows-4e26d9ce1eec#.e72sx8h7e)。一般情况下我们不会将这个参数设置为true，除非需要一些特定的效果，例如实现透明的系统状态栏并且覆盖在我们的布局之上。

* LinearLayout
DrawerLayout里面的LinearLayout用以摆放我们要展示的内容，其中的Toolbar是一个顶端的action bar，用来摆放一个侧边抽屉的触发按钮。之后的FrameLayout才是我们展示真正的内容的地方，可以使用Fragment来填充这个Layout。

* NavigationView
侧边抽屉控件，其中*app:headerLayout="@layout/drawer_header"*用于向侧边抽屉中添加添加一个Header布局，*app:menu="@menu/menu_main"*则是一个菜单文件，用于编辑菜单的内容。

drawer_header:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="150dp"
    android:background="@android:color/holo_blue_dark"
    android:orientation="vertical">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button" />

</LinearLayout>
```

menu_main:

```xml
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <group android:checkableBehavior="single">
        <item
            android:id="@+id/navi_item_moments"
            android:icon="@drawable/ic_public_white_24dp"
            android:title="@string/moments" />

        <item
            android:id="@+id/navi_item_settings"
            android:icon="@drawable/ic_settings_white_24dp"
            android:title="@string/settings" />
    </group>
</menu>
```

运行结果：
![Snip20160713_5](/images/Snip20160713_5.png)
### 在Activity中配置
```java
public class MainActivity extends BaseActivity {

    private DrawerLayout mDlytContainer;
    private Toolbar mViewToolbar;
    private NavigationView mViewNavigation;
    private ActionBarDrawerToggle mDrawerToggle;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ...
        mDrawerToggle = new ActionBarDrawerToggle(this, mDlytContainer, mViewToolbar, R.string.open,
                R.string.close);

        mViewNavigation.setCheckedItem(R.id.navi_item_moments);
        // 添加侧边菜单的点击事件
        mViewNavigation.setNavigationItemSelectedListener(item -> {
            switch (item.getItemId()) {
                case R.id.navi_item_moments:
                    ...
                    break;
                case R.id.navi_item_settings:
                    ...
                    break;
            }
            mDlytContainer.closeDrawers();
            return true;
        });
    }

    @Override
    protected void onPostCreate(@Nullable Bundle savedInstanceState) {
        super.onPostCreate(savedInstanceState);
        mDrawerToggle.syncState();
    }
}
```

* ActionBarDrawerToggle是在action bar中侧边抽屉的触发器，绑定了DrawerLayout和Toolbar。
* 在onPostCreate()方法中调用ActionBarDrawerToggle.syncState()是官方文档中的建议。
* 如果要获取在drawer_header布局中的控件，直接使用findViewById(int id)是不行的，需要使用NavigationView.getHeaderView(int index)获取到Head View，然后使用View.findViewById(int id)方法来获取。

## 总结 
NavigationView的使用方法还是比较简单的，也符合Material Design。




