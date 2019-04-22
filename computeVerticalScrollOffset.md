（1）computeVerticalScrollOffset()：

已经向下滚动的距离即顶部已经滑出屏幕的区域，为0时表示已处于顶部。

（2）computeVerticalScrollRange()：

整体的高度，注意是整体，包括在显示区域之外的。

（3）computeVerticalScrollExtent()：

显示区域的高度即可视区域。



图示如下：

![img](https://img-blog.csdn.net/20150203160638170)



对比图示很容易知道：

当Offset大于0时，可以继续下拉，当Offset等于0时，不可以。

当Range大于Offset加上Extent时，可以继续上拉，当Range等于Offset加上Extent时，不可以。
