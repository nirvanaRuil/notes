# flex(弹性布局)

## 传统布局和flex布局的优缺点

传统布局:

- 兼容性好
- 布局繁琐
- **局限性是==不能在移动端很好布局==**

flex布局:

- 操作方便,布局极简单
- **PC端支持情况较差**
- IE11或更低版本,不支持或仅部分支持

如果PC端布局就用传统布局;如果移动端或者不考虑兼容问题的PC端布局就用flex布局

## 布局原理

flex:flexible Box,意为弹性布局,用来为盒装模型提供最大的灵活性,任何一个容器都可以指定为flex布局.

当为父盒子设定为flex布局后,子元素的float,clear,vertical-align属性将失效.

伸缩布局=弹性布局=伸缩盒布局=弹性盒布局=flex布局.

==**通过给父盒添加flex,来控制子盒的位置和排列方式**==.

**==flex中默认子元素不换行==**,如果子元素太多,一行装不开,就会缩小子元素宽度强行放到一行中.可以用flex-wrap强行换行,此时flex写的就是占父级宽度百分比--`flex:20%;`.

如果要水平和垂直都居中,就要同时设置主轴和侧轴的居中.

**align-items适用于单行**,有上对齐,下对齐,居中和拉伸.

**align-content适用于换行(多行)**情况,在单行下无效,可以上对齐,下对齐,拉伸,居中以及平均分配剩余空间值. 



采用Flex布局的元素，称为Flex容器（flex  container），简称"容器"。它的所有子元素自动成为容器成员，称为Flex项目（flex  item），简称"项目"。容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（侧轴cross  axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross  start，结束位置叫做cross end。项目默认沿主轴排列。单个项目占据的主轴空间叫做main size，占据的交叉轴空间叫做cross  size。

![flex](https://img-blog.csdnimg.cn/20200528002137251.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQxNTU5NjQ3,size_16,color_FFFFFF,t_70)

## 父盒(容器)属性

**flex-direction**　**决定主轴的方向(横向或纵向),剩下的一个就是侧轴(交叉轴)**.容器内项目的排列方向(默认横向排列).　　
 **flex-wrap**　容器内项目换行方式.
 **flex-flow**　以上两个属性的简写方式.
 **justify-content**　项目**在==主轴==上的对齐方式**.
 **align-items**　项目在交叉轴(侧轴)上如何对齐.
 **align-content**　定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用.



**flex-direction**  决定主轴的方向（即项目的排列方向）.
 *.box {  flex-direction: row | row-reverse | column | column-reverse;  }*
 属性可选值的范围为**row(默认)**沿水平主轴由左向右排列、row-reverse沿水平主轴由右向左排列、column沿垂直主轴右上到下和column-reverse。  reverse-翻转
 row（默认值）：主轴为水平方向，起点在左端。
 row-reverse：主轴为水平方向，起点在右端。
 column：主轴为垂直方向，起点在上沿。
 column-reverse：主轴为垂直方向，起点在下沿。



**flex-wrap**    **默认情况下，项目都排在一条线**（又称"轴线"）上。**flex-wrap属性定义，如果一条轴线排不下，如何换行**。
 *.box{  flex-wrap: nowrap | wrap | wrap-reverse;  }*
 属性可选值的范围为nowrap(默认)不换行、wrap换行（第一行在上方）和wrap-reverse（换行,第一行在下方）   ==**可以和flex配合使用**==.



**flex-flow**

```css
 .box {  flex-flow: <flex-direction> || <flex-wrap>;  } 
```

flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。写法属性中，将上述两种方法的值用||连接即可,也可以直接空格隔开.



**justify-content**  定义项目在主轴上的对齐方式.
 .box {  justify-content: flex-start | flex-end | center | space-between | space-around;  }
 项目在主轴上的对齐方式(主轴究竟是哪个轴要看属性flex-direction的设置了)
 flex-start：在主轴上由左或者上开始排列
 flex-end：在主轴上由右或者下开始排列
 ==**center：在主轴上居中排列(设置垂直/水平居中)**==.
 ==**space-between：在主轴上左右两端或者上下两端开始排列(在两遍贴边,再给其他元素分配剩余空间)**==  .  重要! 这样每个元素间隔相等
 **==space-around==**：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。



**align-items**  **定义项目在交叉轴(侧轴)上如何对齐**。
 *.box {  align-items: flex-start | flex-end | center | baseline | stretch;  }*
 它可能取5个值。具体的对齐方式与交叉轴的方向有关，下面假设交叉轴从上到下:
 flex-start：交叉轴的起点对齐。(左,上)
 flex-end：交叉轴的终点对齐。(右,下)
 center：交叉轴的中点对齐。
 baseline: 项目的第一行文字的基线对齐。
 stretch（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。



**align-content**  定义了**多根轴线的对齐方式**。如果项目**只有一根轴线，该属性不起作用**。怎么确定多根轴线?换行呀,子项出现**==换行==**情况就是多行了.
 *.box {  align-content: flex-start | flex-end | center | space-between | space-around | stretch; }*
 flex-start：与交叉轴的起点对齐。
 flex-end：与交叉轴的终点对齐。
 center：与交叉轴的中点对齐。
 space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
 space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
 stretch（默认值）：轴线占满整个交叉轴。



## 容器(父)中项目(子)的属性

**order**项目的排列顺序。数值越小，排列越靠前，**默认为0**。(可以有负值)
 **flex-grow**项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
 **flex-shrink**项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
 **flex-basis**在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
 **==flex==**属性**==定义子项目分配剩余空间,用flex来表示占多少份数,默认值0,还可以写百分比,表示每个子项目占父级宽度的百分比==**.
 **align-self** **允许==单个项目有与其他项目不一样==的对齐方式**，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。



**order**

```javascript
 .item {
   order: <integer>;
 }
```



**flex-grow**

```javascript
   .item {
       flex-grow: <number>;   /* default 0 */
    }
```



**flex-shrink**

```javascript
 .item {
   flex-shrink: <number>;   /* default 1 */
 }
```



**flex-basis**  它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。

```javascript
.item {
    flex-basis: <length> | auto;   /* default auto */
}
```



**==flex==**  非常非常常用的属性,**==定义子项目分配剩余空间==**.除了写数字0,1,2等,还可以写百分比,==**百分比表示在一行之内每个子项目占父级宽度的百分比**==.

```javascript
.item {
    flex: <number>;   /* default 0 */
}
```



**align-self**  该属性可能取6个值，除了auto，其他都与align-items属性完全一致。

```
 .item {
 	align-self: auto | flex-start | flex-end | center | baseline | stretch;
 }
```





容器属性和项目属性是可以配合使用的，用法类似于CSS的行内式和嵌入式的道理一样。



```css
background: -webkit-linear-gradient(left,#FB6A5B,#F77DAA);
/*渐变色,必须加-webkit-*/
```











