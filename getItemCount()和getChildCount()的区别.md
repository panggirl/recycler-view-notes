RecyclerView中LayoutManager里面的getItemCount()和getChildCount()的区别
```
// getItemCount()这个方法是获取所有的条目数
public int getItemCount() {
            RecyclerView.Adapter a = this.mRecyclerView != null ? this.mRecyclerView.getAdapter() : null;
            return a != null ? a.getItemCount() : 0;
        }
     
 // getChildCount()是获取可视的条目数
public int getChildCount() {
            return this.mChildHelper != null ? this.mChildHelper.getChildCount() : 0;
        }
        
...
// ChildHelper this.mChildHelper.getChildCount()
int getChildCount() {
        return this.mCallback.getChildCount() - this.mHiddenViews.size();
    }
```
