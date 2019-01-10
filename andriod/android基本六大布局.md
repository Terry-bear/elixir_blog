# android六大布局

今天leader让我们前端的了解下Android的画页面工作。最近Android的页面比较多，又正好碰上年会，那我们front-coder也只能搭把手了。

声明Android程序布局有两种方式：

1. 使用XML文件描述界面布局；
2. 在Java代码中通过调用方法进行控制。

我们既可以使用任何一种声明界面布局的方式，也可以同时使用两种方式。

使用XML文件声明有以下3个特点：

1. 将程序的表现层和控制层分离；
2. 在后期修改用户界面时，无须更改程序的源程序；
3. 可通过WYSIWYG可视化工具直接看到所设计的用户界面，有利于加快界面设计的过程。
建议尽量采用XML文件声明界面元素布局。在程序运行时动态添加界面布局会大大降低应用响应速度，但依然可以在必要时动态改变屏幕内容。

## 六大界面布局方式包括： 线性布局(LinearLayout)、框架布局(FrameLayout)、表格布局(TableLayout)、相对布局(RelativeLayout)、绝对布局(AbsoluteLayout)和网格布局(GridLayout) 。

## LinearLayout线性布局

LinearLayout容器中的组件一个挨一个排列，通过控制android:orientation属性，可控制各组件是横向排列还是纵向排列。

**LinearLayout的常用XML属性及相关方法**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:gravity	|setGravity(int)|	设置布局管理器内组件的对齐方式
|android:orientation	|setOrientation(int)	|设置布局管理器内组件的排列方式，可以设置为horizontal、vertical两个值之一

> 其中，gravity属性支持top, left, right, center_vertical, fill_vertical, center_horizontal, fill_horizontal, center, fill, clip_vertical, clip_horizontal。也可以同时指定多种对齐方式的组合。

**LinearLayout子元素支持的常用XML属性及方法**

XML属性	说明
android:layout_gravity	指定该子元素在LinearLayout中的对齐方式
android:layout_weight	指定子元素在LinearLayout中所占的权重

## TableLayout表格布局

TableLayout继承自Linearout，本质上仍然是线性布局管理器。表格布局采用行、列的形式来管理UI组件，并不需要明确地声明包含多少行、多少列，而是通过添加TableRow、其他组件来控制表格的行数和列数。

每向TableLayout中添加一个TableRow就代表一行；

每向TableRow中添加一个一个子组件就表示一列；

如果直接向TableLayout添加组件，那么该组件将直接占用一行；

在表格布局中，可以为单元格设置如下三种行为方式：

- Shrinkable：该列的所有单元格的宽度可以被收缩，以保证该表格能适应父容器的宽度；
- Strentchable：该列所有单元格的宽度可以被拉伸，以保证组件能完全填满表格空余空间；
- Collapsed：如果该列被设置为Collapsed，那么该列的所有单元格会被隐藏；

**TableLayout的常用XML属性及方法**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:collapseColumns	|setColumns(int, boolean)	|设置需要被隐藏的列的序号，多个序号间用逗号分隔
|android:shrinkColumns	|setShrinkAllColumns(boolean)	|设置需要被收缩的列的序号
|android:stretchColumns	|setStretchAllColumns(boolean)	|设置允许被拉伸的列的序号

## FrameLayout帧布局

FrameLayout直接继承自ViewGroup组件。帧布局为每个加入其中的组件创建一个空白的区域(称为一帧)，每个子组件占据一帧，这些帧会根据gravity属性执行自动对齐。

**FrameLayout的常用XM了属性及方法**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:foreground	|setForeground(Drawable)	|设置该帧布局容器的前景图像
|android:foregroundGravity	|setForeGroundGraity(int)	|定义绘制前景图像的gravity属性

## RelativeLayout相对布局

**RelativeLayout的XML属性及相关方法说明**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:gravity	|setGravity(int)|
|android:ignoreGravity	|setIgnoreGravity(int)	|设置哪个组件不受gravity属性的影响

> 为了控制该布局容器的各子组件的布局分布，RelativeLayout提供了一个内部类：RelativeLayout.LayoutParams。

**RelativeLayout.LayoutParams里只能设为boolean的XML属性**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:layout_centerHorizontal	||设置该子组件是否位于布局容器的水平居中
|android:layout_centerVertical
|android:layout_centerParent
|android:layout_alignParentBottom
|android:layout_alignParentLeft
|android:layout_alignParentRight
|android:layout_alignParentTop

**RelativeLayout.LayoutParams里属性值为其他UI组件ID的XML属性**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:layout_toRightOf	||控制该子组件位于给出ID组件的右侧
|android:layout_toLeftOf
|android:layout_above
|android:layout_below
|android:layout_alignTop
|android:layout_alignBottom
|android:layout_alignRight
|android:layout_alignLeft

## Android 4.0新增的网格布局GridLayout

GridLayout是Android4.0增加的网格布局控件，与之前的TableLayout有些相似，它把整个容器划分为rows × columns个网格，每个网格可以放置一个组件。性能及功能都要比tablelayout好，比如GridLayout布局中的单元格可以跨越多行，而tablelayout则不行，此外，其渲染速度也比tablelayout要快。

GridLayout提供了setRowCount(int)和setColumnCount(int)方法来控制该网格的行和列的数量。

**GridLayout常用的XML属性和方法说明**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:alignmentMode|setAlignmentMode(int)|设置该布局管理器采用的对齐模式
|android:columnCount	|setColumnCount(int)	|设置该网格的列数量
|android:columnOrderPreserved	|setColumnOrderPreserved(boolean)	|设置该网格容器是否保留序列号
|android:roeCount	|setRowCount(int)	|设置该网格的行数量
|android:rowOrderPreserved	|setRowOrderPreserved(boolean)	|设置该网格容器是否保留行序号
|android:useDefaultMargins	|setUseDefaultMargins(boolean)	|设置该布局管理器是否使用默认的页边距

> 为了控制GridLayout布局容器中各子组件的布局分布，GridLayout提供了一个内部类：GridLayout.LayoutParams，来控制Gridlayout布局容器中子组件的布局分布。

**GridLayout.LayoutParams常用的XML属性和方法说明**

| XML属性 |	相关方法 | 说明 |
| ---- | ------| -----|
|android:layout_column	|设置该组件在GridLayout的第几列
|android:layout_columnSpan	|设置该子组件在GridLayout横向上跨几列
|android:layout_gravity	|设置该子组件采用何种方式占据该网格的空间
|android:layout_row	|设置该子组件在GridLayout的第几行
|android:layout_rowSpan	|设置该子组件在GridLayout纵向上跨几行

## AbsoluteLayout绝对布局

即Android不提供任何布局控制，而是由开发人员自己通过X坐标、Y坐标来控制组件的位置。每个组件都可指定如下两个XML属性：

- layour_x;
- layout_y;

    绝对布局已经过时，不应使用或少使用。

**界面布局类型的选择和性能优化**

首先得明确，界面布局类型的嵌套越多越深越复杂，会使布局实例化变慢，使Activity的展开时间延长。建议尽量减少布局嵌套，尽量减少创建View对象的数量。

1 . 减少布局层次，可考虑用RelativeLayout来代替LinearLayout。通过Relative的相对其他元素的位置来布局，可减少块状嵌套；

2 . 另一种减少布局层次的技巧是使用 `<merge />` 标签来合并布局；

3 . 重用布局。Android支持在XML中使用 `<include />` 标签， `<include />` 通过指定android:layout属性来指定要包含的另一个XML布局。

如：    
```xml
    <include android:id="@+id/id1" android:layout="@layout/mylayout">
    <include android:id="@+id/id2" android:layout="@layout/mylayout">
    <include android:id="@+id/id3" android:layout="@layout/mylayout">
```

>对于安卓是在是不太了解,就转载了CSDN上的文章作为分享,也学习了不少关于Android的布局。

作者：冰糖葫芦三剑客

来源：CSDN

原文：[https://blog.csdn.net/shenggaofei/article/details/52450668](https://blog.csdn.net/shenggaofei/article/details/52450668)

版权声明：本文为博主原创文章，转载请附上博文链接！
