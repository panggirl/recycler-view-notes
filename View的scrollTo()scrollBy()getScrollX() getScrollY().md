# View的scrollTo(),scrollBy(),getScrollX(), getScrollY()

mScrollX和mScrollY是View类中专门用于记录滑动位置的变量。这两个函数最终调用onScrollChanged()函数;
scroll本身就是滚动的内容，view本身的位置没有改变。至于正负关系的问题，可以这样解释： 在移动平台中，
要明确知道“滑动”与“滚动”的不同，具体来说，滑动和滚动的方向总是相反的 移动：英文翻译为 translate，
在Android的动画中有移动的动画(但是其中的参数直接使用的from-to的方式)，表示将对象移动至目的地 
滚动：英文翻译为scroll，其实我们通常看滚动条就可以看出来，当滚动条向下移动时，屏幕原本显示的内容向上移动；
当滚动条向左移动时，屏幕原本显示的内容向右移动。这也就可以解释为什么scrollTo(int x, int y)中
为什么 x>0 时，view向左移动；y>0 时，view，向上移动了


```
    /**
     * Set the scrolled position of your view. This will cause a call to
     * {@link #onScrollChanged(int, int, int, int)} and the view will be
     * invalidated.
     * @param x the x position to scroll to
     * @param y the y position to scroll to
     */
    public void scrollTo(int x, int y) {
        if (mScrollX != x || mScrollY != y) {
            int oldX = mScrollX;
            int oldY = mScrollY;
            mScrollX = x;
            mScrollY = y;
            invalidateParentCaches();
            onScrollChanged(mScrollX, mScrollY, oldX, oldY);
            if (!awakenScrollBars()) {
                postInvalidateOnAnimation();
            }
        }
    }
    
    /**
     * Move the scrolled position of your view. This will cause a call to
     * {@link #onScrollChanged(int, int, int, int)} and the view will be
     * invalidated.
     * @param x the amount of pixels to scroll by horizontally
     * @param y the amount of pixels to scroll by vertically
     */
    public void scrollBy(int x, int y) {
        scrollTo(mScrollX + x, mScrollY + y);
    }
 ```
 
