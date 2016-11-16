---
title: RecyclerView使用总结
tags:
---

**在调用notifyDatasetChanged()方法后出现item闪烁的解决方案：**
原因是notifyDatasetChanged()调用之后会默认执行item改变的动画，导致了闪烁。
解决办法是禁用动画，调用：

```java
ItemAnimator animator = recyclerView.getItemAnimator();
if (animator instanceof SimpleItemAnimator) {
  ((SimpleItemAnimator) animator).setSupportsChangeAnimations(false);
}
```

参考：[StackOverflow](http://stackoverflow.com/questions/29331075/recyclerview-blinking-after-notifydatasetchanged)


