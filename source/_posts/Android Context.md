---
title: Android Context对象学习
---

## 相关的类

### [Context](http://developer.android.com/reference/android/content/Context.html)

*Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.*

就是说Context是用来**处理应用级别的事务**的，比如想调用resources里的图片、文字，又或是想用SharedPreferences来存取一些配置等，这些都是属于应用级别的资源，通过Context可以对它们进行调用。又比如想启动另外一个Activity或者是一个Service，也需要通过Context来实现。  
下面是Context的部分源码（[source code](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/content/Context.java)）：

```java
public abstract class Context {
    ...
    /** 用于获取应用的资源，如字符串，图片等 */
    public abstract Resources getResources();
    /** 启动Activity */
    public abstract void startActivity(Intent intent);
    /** 启动Service */
    public abstract ComponentName startService(Intent service);
    /** 获取SharedPreferences */
    public abstract SharedPreferences getSharedPreferences(String name, int mode);
    ...
}
```
通过源码可以看出，Context是一个抽象类，里面定义了一堆方法，但是都没有具体的实现。按照官方文档中所说具体实现会由android系统提供的，一个例子就是下面提到的ContextImpl。   

### ContextImpl

*Common implementation of Context API, which provides the base context object for Activity and other application components.*

实现Context接口的类，为Activity和其他组件提供了基础的Context对象。

下面是ContextImpl的部分源码（[source code](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/app/ContextImpl.java#)）:

```java
class ContextImpl extends Context {
    ...
    @Override
    public void startActivity(Intent intent, Bundle options) {
        warnIfCallingFromSystemProcess();
        if ((intent.getFlags()&Intent.FLAG_ACTIVITY_NEW_TASK) == 0) {
            throw new AndroidRuntimeException(
                    "Calling startActivity() from outside of an Activity "
                    + " context requires the FLAG_ACTIVITY_NEW_TASK flag."
                    + " Is this really what you want?");
        }
        mMainThread.getInstrumentation().execStartActivity(
                getOuterContext(), mMainThread.getApplicationThread(), null,
                (Activity) null, intent, -1, options);
    }
    ...
}
```
通过源码看出，ContextImpl实现了Context接口里的方法。

### [ContextWrapper](http://developer.android.com/reference/android/content/ContextWrapper.html)

*Proxying implementation of Context that simply delegates all of its calls to another Context. Can be subclassed to modify behavior without changing the original Context.*

ContextWrapper的所有方法的都委托给了委托给了一个Context类的实现（例如ContextImpl）。这是采用了装饰模式。

下面是ContextWrapper的部分源码（[source code](https://android.googlesource.com/platform/frameworks/base/+/master/core/java/android/content/ContextWrapper.java)）:

```java
public class ContextWrapper extends Context {
    Context mBase;
    /** 绑定一个mBase的Context对象，调用方法时会直接委托给mBase来处理 */
    protected void attachBaseContext(Context base) {
        if (mBase != null) {
            throw new IllegalStateException("Base context already set");
        }
        mBase = base;
    }
    ...
    @Override
    public Resources getResources() {
        return mBase.getResources();
    }

    @Override
    public void startActivity(Intent intent) {
        mBase.startActivity(intent);
    }
    ...
}
```

通过源码看出，ContextWrapper持有了一个mBase的Context对象，然后代理了mBase中的方法。mBase就是一个ContextImpl的实例。

### [ContextThemeWrapper](http://developer.android.com/reference/android/view/ContextThemeWrapper.html)

*A ContextWrapper that allows you to modify the theme from what is in the wrapped context.*

ContextThemeWrapper包含了主题相关的一些功能，Activity就是继承自ContextThemeWrapper，而Service是继承自ContextWrapper。


## 参考文档
[Android中Context详解 ---- 你所不知道的Context](http://blog.csdn.net/qinjuning/article/details/7310620)
[Context, What Context?](https://possiblemobile.com/2013/06/context/)
[Android应用程序窗口（Activity）的运行上下文环境（Context）的创建过程分析](http://blog.csdn.net/luoshengyang/article/details/8201936)


