# css中的伪类和伪元素

## 伪类

属性 	描述 	
 **:active**  向被激活的元素添加样式。
 **:focus** 	向拥有键盘输入焦点的元素添加样式。 	
 **:hover** 	当鼠标悬浮在元素上方时，向元素添加样式。 	
 **:link** 	向未被访问的链接添加样式。 	
 **:visited**   向已被访问的链接添加样式。 	
 **:first-child**    向元素的第一个子元素添加样式。 	
 **:lang**    向带有指定 lang 属性的元素添加样式。

```css
q:lang(no)
   {
   quotes: "~" "~"
   }
```



提示：在 CSS 定义中，a:hover 必须被置于 a:link 和 a:visited 之后，才是有效的。单独写hover则不必注意.

提示：在 CSS 定义中，a:active 必须被置于 a:hover 之后，才是有效的。

提示：伪类名称对大小写不敏感。

提示：最常见的错误是认为 p:first-child 之类的选择器会选择 p 元素的第一个子元素。

## 伪元素

属性 	描述 	
 **:first-letter** 	向文本的第一个字母添加特殊样式。
 **:first-line** 	向文本的首行添加特殊样式。 	
 **:before** 	在元素之前添加内容。
 **:after** 	在元素之后添加内容。



"first-letter" 伪元素只能用于块级元素。

注释：下面的属性可应用于 "first-letter" 伪元素：

font    color    background    margin    padding    border    text-decoration    vertical-align (仅当 float 为 none 时)    text-transform    line-height    float    clear



"first-line" 伪元素只能用于块级元素。

注释：下面的属性可应用于 "first-line" 伪元素：

font    color    background    word-spacing    letter-spacing    text-decoration    vertical-align    text-transform    line-height    clear