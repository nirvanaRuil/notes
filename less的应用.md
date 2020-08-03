# 添加

在node.js中使用  cmp安装

`npm i less -g`

# 基本用法

## 变量

定义变量,封装样式:`@main-color:#acbd23;`

使用变量:`background:@main-color;`

## 混合使用

使用less混合功能,仅在所需要的.class类中声明即可.

```css
.padding{
    padding:3rem 0.23rem;
}
.nav{
    .padding;
    background:red;
}
```

### 带参数

设置所需的混合参数及变量,封装样式

```css
.padding{
    padding:3rem 0.23rem;
}
.nav(@bgcolor){
    .padding;
    background:red;
}
.naver(@bgcolor:red){
    .padding;
    @bgcolor;  //也可以有默认参数
}
```

带参使用方法

```css
.as{
    .nav(#35231c);
}
.as{
    .naver();  //戴默认参数使用时
}
```

渲染结果

```css
.as{
    .padding;
    background:#35231c;
}
```

也可以更改参数为已知定义变量

## `@_`匹配模式

配置基础样式,封装样式

@_是固定格式,表示不管你匹配到谁,都会带上这些内容.

`.as(@_)`

## 层级嵌套和变量计算

可以多层嵌套

```css
.lest{
    li{
        width:@k;
        .sd{   
            @k/2;  //表示二分之一的@k
        }
    }
    .lest h1{
        img{}
    }
    span{}
}
```

## 懒人必备arguments变量

使用arguments的好处就是可以帮你自动填充所有变量.

使用方法:`.border_arguments{}`	

## less小技巧

### 注释问题

`/* */`编译后会被保留

`//`编译后不会被保留

### 适用!important

`.nav{.pap(red)!important}`  .pap下所有样式都会带上!important

### 避免编译符号`~`

在自符号串前加一个`~`即可使用一些less不认识的专有语法或者一些不正确的css语法.

`content:~"@main-color" `     作用和innertext有些相同吧.













