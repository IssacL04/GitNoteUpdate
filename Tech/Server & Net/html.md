# HTML

## 基础

### 1.基本信息

HTML：超文本标记语言（超文本：流媒体，声音，视频，图片）

任何一个标签都有开始和结束：

<标签>,</标签>

### 2.基本标签

```html
<html>
    <head><!--这是HTML文件头--> 
        <title>这是标题</title>
    </head>
    <body>
        页面主体内容
    </body>
</html>
<!--这是注释格式--> 
<p>
    段落分隔符
</p>
<h1>这是标题，数字表示标题等级</h1>
<br>换行标记
<hr>水平线（独目标签）
<hr color="red"><!--颜色为红色，其中双引号单引号均可，也可以不使用引号--> 
<pre></pre><!--保留源码的格式-->
<b>粗体字</b>
<i>斜体字</i>
<ins>插入</ins>
<del>删除</del>
<sup>上标</sup>
<sub>下标</sub>
<font color="red" size="12" >字体</font>
```

#### 实体符号

```html
<!--空格-->
&nbsp;
<!--小于号-->
&lt;
<!--大于号-->
&gt;
```

#### 表格

```html
<table border="1px" width="50%" height="30%">
    <tr align="center">
        <td>1</td>
        <td colsapan='2'>23</td>
    </tr>
    <tr>
        <td>4</td>
        <td>5</td>
        <td rowspan = '2'>King</td>
    </tr>
    <tr>
        <td>4</td>
    </tr>
</table>
<th></th>可以代替<td></td>，其中内容自动居中并且加粗、一个table可以被thead tbody tfoot分为三部分
```

#### 背景

```html
<body background="路径"></body>
<body bgcolor = "red"></body>
```

#### 图片

```html
title属性用来设置鼠标悬停时的提示信息
alt属性用来设置图片加载失败时的提示信息
<img src="path" width="200px" title = "" alt=""/>
```

#### 超链接

```html
<a href="website">网站</a>
超链接嵌套图片：
<a href="website">
    <img src="path" width="200px" title = "" alt=""/>
</a>
<a href="website" target = "_self">网站（当前窗口）</a>
<a href="website" target = "_blank">网站（新窗口）</a>
_parent：当前窗口的父窗口,_top：当前窗口的顶级窗口
<iframe>
    一个窗口中的内部窗口
</iframe>（也可以使用src）
```

#### 列表

```html
<!--无序列表-->
<ul>
    <li>A</li>
    <li>B</li>
    <li>C</li>
</ul>
<!--有序列表-->
<ol type="a">
    <li>apple</li>
    <li>xiaomi</li>
</ol>
```

## 表单

### 1.基本信息

表单发送请求并且携带数据传达给服务器；相比之下超链接只能发送请求。

### 2.语法

```html
<form action="/path" >
    <input type="text" name="username"/>
    <br>
    <input type="password" name="password"/>
    <br>
    性别
    <input type="radio" name="sex" value="0"/>男
    <input type="radio" name="sex" value="1"/>女
    <br>
    兴趣
    <input type="checkbox" name="interest" value=""/><!--复选框-->
    <br>
    学历
    <select name="grade">
        <option>1</option>
        <option>2</option>
        <option>3</option>
    </select>
    <br>
    简介
    <textarea rows="10" cols="60" name="description"></textarea>
    <br>
    <input type="submit" value = "login"/><!--用来提交申请,提交按钮中不能包含name，否则该按钮的value也会被提交-->
</form>


<!--列表的显示与多选-->
<select name="temp" size="3" multiple>
    <!--size是单次显示的条目数量,multiple指支持多选-->
</select>


<!--文件控件-->
<input type="file"/>

<!--隐藏域:后端可以接受但前端不可见-->
<input type="hidden" name="usercode" value="111"/>
<!--readonly修饰的表单项可以被提交给服务器，但disabled不行（前端显示为不可修改的表单项）-->
<!--maxlength可定义单个表单提交项能提交的最多字符-->
```

## id属性

HTML中的任何一个节点上都可以存在id属性。在单一网页中id属性不能重复；id代表了这个节点。javascript可以通过捕获id属性对节点进行操作。

## div和span

div代表一个独立的图层；div和span都有x，y坐标，可以定位正网页中的位置。div默认占用一行,但span只会占用一小块区域。

# CSS

## 概述

CSS有三种定义方式

1.内联定义；2.定义内部样式块对象；3.链入外部样式表文件

## 定义

### 1.内联定义

```html
<html>
    <head>
        <title>CSS1</title>
    </head>
    <body>
        <!--内联定义：在标签内部添加style="样式名：样式值"-->
        <div id="div1" style="">
            
        </div>
    </body>
</html>
```

### 2.内部样式块

```html
<html>
    <head>
        <title>CSS1</title>
        <style type="text/css">
            /*这是CSS的注释
            内部样式块定义：
            选择器{样式名：样式值}
            CSS中常见的选择器：标签/id/class选择器
            */
            /*标签选择器*/
            div{
                background-color:aqua;
                width:100px;
                border-color:red;
                border-width:1px;
            }
            /*id选择器*/
            #textarea1{
                width:200px;
                height:200px;
                border-color:black;
                border-style:solid;
            }
            /*class选择器*/
            .default{
                font-size:12px;
                color:#FF0000;
            }
        </style>
    </head>
    <body>
        <div id="div1">
            <a href="www.baidu.com">WEBSITE</a>
        </div>
        <br>
        <textarea name="description" id="textarea1"></textarea>
        <br>
        <span class="default">default text 1</span>
        <p class="default">
            default text 2
        </p>
    </body>
</html>
```

### 3.引入CSS文件

```html
<link rel="stylesheet" type="txt/css" href="/path"/>
```

*.选择器优先级:id>class>tag

## 常用样式

### 1.隐藏样式

```html
display: none;/*隐藏*/
display: block;/*显示*/
```

### 2.字体

```
text-decoration:underline/overline/line-through
```

### 3.列表

```
list-style-type:
```

### 4.鼠标悬停

```html
[tagname]:hover{
    color:red;
    font-size:100px;
}
```

### 5.内外补丁

https://www.cnblogs.com/LloydDracarys/articles/8995867.html

```html
<!DOCTYPE html>
<html>
    <head>
        <title></title>
        <style type="text/css">
            #div1{
                width:200px;
                height:200px;
                background-color:aqua;
                border:solid 1px green;
                padding-left:20px;
            }
            #div2{
                width:100px;
                height:100px;
                background-color:red;
                border:solid 1px black;
                margin-top:20px;
            }
        </style>
    </head>
    <body>
        <div id="div1">
            <div id="div2">
                HELLO
            </div>
            world
        </div>
    </body>
</html>
```

### 6.定位

```css
<style type="text/css">
    #div1{
        position:absolute;
        top:300px;
        left:300px;
    }
    #tag1{
        cursor:pointer;/*指针形状*/
        tezt-decoration:underline;
        hover:blue;
    }
</style>
```

# Javascript

## 概述

1.脚本语言，目标程序以普通文本形式保存

2.
