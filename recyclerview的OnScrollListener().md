# recyclerview 滚动检测 （上滑 up）（下滑 down）（顶部 top）（底部 bottom）
recyclerview.setOnScrollListener()或者 recyclerview.addOnScrollListener() set方法将会被弃用 deprecated 最好使用add方法但是这个也可以根据自己情况，如果自己当前的api中set方法没有被弃用，不存在add方法。就只能使用set方法了。

## 监听 上滑，下滑
这些都不是重点，接下来就分析如何监听，RecyclerView的滑动。
```
    /**
     * An OnScrollListener can be added to a RecyclerView to receive messages when a scrolling event
     * has occurred on that RecyclerView.
     * <p>
     * @see RecyclerView#addOnScrollListener(OnScrollListener)
     * @see RecyclerView#clearOnChildAttachStateChangeListeners()
     *
     */
    public abstract static class OnScrollListener {
        /**
         * Callback method to be invoked when RecyclerView's scroll state changes.
         *
         * @param recyclerView The RecyclerView whose scroll state has changed.
         * @param newState     The updated scroll state. One of {@link #SCROLL_STATE_IDLE},
         *                     {@link #SCROLL_STATE_DRAGGING} or {@link #SCROLL_STATE_SETTLING}.
         */
        public void onScrollStateChanged(RecyclerView recyclerView, int newState){}

        /**
         * Callback method to be invoked when the RecyclerView has been scrolled. This will be
         * called after the scroll has completed.
         * <p>
         * This callback will also be called if visible item range changes after a layout
         * calculation. In that case, dx and dy will be 0.
         *
         * @param recyclerView The RecyclerView which scrolled.
         * @param dx The amount of horizontal scroll.
         * @param dy The amount of vertical scroll.
         */
        public void onScrolled(RecyclerView recyclerView, int dx, int dy){}
    }
```

RecyclerView.OnScrollListener中有一个方法 叫 onScrolled（RecyclerView recyclerView, int dx, int dy){}
其中dx，dy分别表示 在x方向和y方向滑动的值，这个值有正负。

如果 dx>0 则表示 右滑 ， dx<0 表示 左滑 
dy <0 表示 上滑， dy>0 表示下滑 
通过这几个参数就可以监听 滑动方向的状态（参考滚动条理解）。

## 监听 顶部，底部
但是还有两种情况，不能通过 dx，dy直接判断出来。那就是 顶部 top状态，底部 bottom状态。需要借助一些其他参数，结合来判断。

##### 第一种方法
第一种方法，在ListView中也经常使用，就是 firstvisibleItem ， visibleItemCount,totalItemCount。 也就是当前屏幕
首个可见的 Item 的position，当前屏幕可见的 Item 个数，Item总共的个数。
```
private boolean loading = true;
int pastVisiblesItems, visibleItemCount, totalItemCount;

mRecyclerView.setOnScrollListener(new RecyclerView.OnScrollListener() {
    @Override
    public void onScrolled(RecyclerView recyclerView, int dx, int dy) {

            visibleItemCount = mLayoutManager.getChildCount();
            totalItemCount = mLayoutManager.getItemCount();
            pastVisiblesItems = mLayoutManager.findFirstVisibleItemPosition();

            if (loading) {
                if ( (visibleItemCount + pastVisiblesItems) >= totalItemCount) {
                // 判断点
                    loading = false;
                    Log.v("...", "Last Item Wow !");
                }
            }
     }
});
```
判断的依据就在 代码注释的地方，通过比较上面三个参数来判断 
如果 当前 第一个可见item的位置+ 当前可见的item个数 >= item的总个数 这样就可以判断出来，是在底部了，bottom。

同理，可以通过第一个可见的item的位置来判断 是否在 顶部 top。

loading只是一个flag 用来避免重复加载。

##### 第二种方法
通过View的滑动属性来判断，是否在顶部，或者底部。这种方法的实现非常简便。


通过重写 OnScrollListener来判断。
```
public abstract class OnVerticalScrollListener
        extends RecyclerView.OnScrollListener {

    @Override
    public final void onScrolled(RecyclerView recyclerView, int dx, int dy) {
        if (!recyclerView.canScrollVertically(-1)) {
            onScrolledToTop();
        } else if (!recyclerView.canScrollVertically(1)) {
            onScrolledToBottom();
        } else if (dy < 0) {
            onScrolledUp();
        } else if (dy > 0) {
            onScrolledDown();
        }
    }

    public void onScrolledUp() {}

    public void onScrolledDown() {}

    public void onScrolledToTop() {}

    public void onScrolledToBottom() {}
}
```
这个方法利用了View的一个方法。public boolean canScrollVertically (int direction) 
这个方法是判断View在竖直方向是否还能 向上，向下 滑动。


判断是否滑动到底部， recyclerView.canScrollVertically(1);返回false表示不能往上滑动，即代表到底部了；
判断是否滑动到顶部， recyclerView.canScrollVertically(-1);返回false表示不能往下滑动，即代表到顶部了；
view 自带方法源码：
```
/**
     * Check if this view can be scrolled horizontally in a certain direction.
     *
     * @param direction Negative to check scrolling left, positive to check scrolling right.
     * @return true if this view can be scrolled in the specified direction, false otherwise.
     * computeHorizontalScrollExtent()：可视区域的大小
     * computeHorizontalScrollRange()：滑动的整体区域包括不可见区域
     * computeHorizontalScrollOffset()： 滑出屏幕的区域
     */
    public boolean canScrollHorizontally(int direction) {
        final int offset = computeHorizontalScrollOffset();
        final int range = computeHorizontalScrollRange() - computeHorizontalScrollExtent();
        if (range == 0) return false;
        if (direction < 0) {
            return offset > 0;
        } else {
            return offset < range - 1;
        }
    }

    /**
     * Check if this view can be scrolled vertically in a certain direction.
     *
     * @param direction Negative to check scrolling up, positive to check scrolling down.
     * @return true if this view can be scrolled in the specified direction, false otherwise.
     */
    public boolean canScrollVertically(int direction) {
        final int offset = computeVerticalScrollOffset();
        final int range = computeVerticalScrollRange() - computeVerticalScrollExtent();
        if (range == 0) return false;
        if (direction < 0) {
            return offset > 0;
        } else {
            return offset < range - 1;
        }
    }
```



