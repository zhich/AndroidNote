## 目录
- [UI](#ui)
- [数据](#数据)
- [系统](#系统)
- [优化](#优化)
- [其它](#其它)




### UI
- setBackgroundResource(0) 可以移除 View 的背景色.

- Resources.getSystem().getDisplayMetrics().density 可以不用 Context 也能获取屏幕密度.

- android:clipToPadding 设置父view是否允许其子view在它的padding（这里指的是父View的padding）中绘制。是不是有点绕？举    个实际场景吧：假如有个ListView，我们想要在初始位置时，第一项Item离顶部有10dp的距离，就可以在ListView的布局中加入android:clipToPadding="false" android:paddingTop="10dp"即可。是不是很方便呢？

- 使用 GridView或ListView时 android:padding 和 android:clipToPadding="false" 配合使用效果更好.