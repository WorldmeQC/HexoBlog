---
title: jQuery的使用
date: 2022-08-14 20:15:38
tags: 
- JavaScript
- jQuery
---

## 初识 jQuery

### 什么是 jQuery ？

jQuery 是一个高效、精简并且功能丰富的 JavaScript 工具库。它提供的 API 易于使用且兼容众多浏览器，这让诸如 HTML 文档遍历和操作、事件处理、动画和 Ajax 操作更加简单。

**优势**：

- 轻量级。体积小，不会影响页面加载速度
- 强大的选择器
- 方便的选择页面元素（模仿 css 选择器更精确、更灵活）
- 出色的 DOM 操作的封装
- 对事件、样式、动画支持，大大简化了 DOM 操作
- 跨浏览器兼容。基本兼容了现在主流的浏览器
- 链式操作、隐式迭代
- 支持插件扩展开发。有着丰富的第三方插件。
- 免费、开源

## jQuery 基本使用

### jQuery 下载

① 官网地址：[jQuery](https://jquery.com/)
② 版本区别：

- 1x：兼容IE678，使用最为广泛，官网只做BUG维护，功能不再新增。因此一般项目来说，使用1.X版本就可以了。
- 2x：不兼容IE678，很少有人使用，官方只做BUG维护，功能不再新增。如果不考虑兼容低版本的浏览器可以使用2.x。(过渡)
- 3x：不兼容IE678，只支持最新的浏览器。除非特殊要求，一般不会使用3.x版本的，很多老的jQuery插件不支持这个版本。

③ 文档：[jQuery API 中文文档](https://www.jquery123.com/)

### 导入方式

方式一：本地导入
直接在需要使用 jQuery 的页面引入 js 文件：

```html
<script src="jQuery.js 文件路径"></script>
```

方式二：在线导入
我们可以通过在 script 的 src 属性中写一个网址来导入在线的 jquery 代码：

```html
<script src="http://code.jquery.com/jquery-latest.js"></script>
```

### jQuery 的入口函数

- JS 入口函数会在 DOM 元素加载完毕并且图片也加载完毕之后再执行
- jQuery 入口函数会等到 DOM 元素加载完毕，但不会等到图片加载完毕再执行

```js
$(document).ready(function (){
    alert("hello jquery");
})
jQuery(document).ready(function () {
    alert("hello jquery");
})

// 简化方式，推荐
$(function () {
    alert("hi jquery");
})
jQuery(function () {
    alert("hi jquery");
})
```

### jQuery 的顶级对象 $

- `$`是 jQuery 的别称，在代码中可以使用` jQuery` 代替`$`，但为了方便，通常都使用`$`。
- 冲突——**多库共存**
  原因：随着jQuery的流行，采用`jQuery`和`$`符为命名空间的 js 库越来越多，当然 jQuery 的`$`符也是参照的 Prototype库的，所以当多个库同时以`$`符或者` jQuery` 为命名空间时，那么此时，就会产生冲突。
  解决方法：

1. 释放使用权

   ```js
   jQuery.noConflict();
   //1.释放操作在其他函数之前
   //2.释放后不能用dollar符
   ```

2. 修改访问符号

   ```js
   var suiBian = jQuery.noConflict();
   //此时suiBian为新的访问符号
   ```

### DOM对象 和 jQuery 对象

DOM 对象：用原生 js 获取过来的对象就是 DOM 对象。

jQuery 对象：用 jQuery 方式获取过来的对象就是 jQuery 对象。本质：通过 $ 把 DOM 元素进行了封装（伪数组形式存储）。

**区别**：jQuery 对象只能使用 jQuery 方法，反之亦然。

相互转换：
DOM –> jQuery

```js
$('DOM对象');
```

jQuery –> DOM（两种方式）

```js
$('DOM对象')[index]; // index 是索引号
$('DOM对象').get(index);
```

## jQuery 常用 API

### jQuery 选择器

#### jQuery 基础选择器

jQuery选择器虽然很多，但是选择器之间可以相互替代，就是说获取一个元素，你会有很多种方法获取到。所以我们平时真正能用到的只是少数的最常用的选择器。

```
$("css选择器");//与css选择器格式一致
```

| 名称       | 用法           | 描述                           |
| ---------- | -------------- | ------------------------------ |
| ID选择器   | $(“#id”);      | 获取指定 ID 的元素             |
| 类选择器   | $(“.class”);   | 获取同一类 class 的元素        |
| 标签选择器 | $(“div”);      | 获取同一类标签的所有元素       |
| 并集选择器 | $(“div,li,p”); | 使用逗号分隔，获取多个元素     |
| 交集选择器 | $(“div.show”); | 获取 class 为 show 的 div 元素 |
| 全选选择器 | $(“*”);        | 匹配所有元素                   |

#### jQuery 层级选择器

| 名称       | 用法        | 描述                                                |
| ---------- | ----------- | --------------------------------------------------- |
| 子代选择器 | $(“ul>li”); | 使用 > 号，获取直接子元素，并不会获取孙子层级的元素 |
| 后代选择器 | $(“ul li”); | 使用空格，获取 ul 下的所有 li 元素，包括孙子等      |

#### 筛选选择器（方法）

> 这些方法的功能与选择器类似，但是用法不一样，筛选选择器主要是方法。

| 名称               | 用法                        | 描述                                                    |
| ------------------ | --------------------------- | ------------------------------------------------------- |
| parent()           | $(“.first”).parent();       | 查找父亲                                                |
| children(selector) | $(“ul”).children(“li”)      | 相当于$(“ul>li”)，子类选择器                            |
| find(selector)     | $(“ul”).find(“li”);         | 相当于$(“ul li”)，后代选择器                            |
| siblings(selector) | $(“.first”).siblings(“li”); | 查找兄弟节点，不包括自己本身。                          |
| next()             | $(“.first”).next();         | 查找当前元素之后的下一个兄弟                            |
| nextAll()          | $(“.first”).nextAll();      | 查找当前元素之后的所有同辈元素                          |
| prev()             | $(“.last”).prev();          | 查找当前元素之前的上一个兄弟                            |
| prevAll()          | $(“last”).prevAll();        | 查找当前元素之前的所有同辈元素                          |
| hasClass(class)    | $(“div”).hasClass(“show”)   | 检查当前的元素是否含有某个特定的类，如果有，则返回 true |
| eq(index)          | $(“li”).eq(2);              | 相当于$(“li:eq(2)”)，index 从0开始                      |

#### 排他思想

```js
$(function() {
  $("button").click(function(){
      $(this).css();  // 对自己设置变化
      $(this).siblings("button").css();  // 给其他兄弟去掉变化
  })
})
```

#### 链式编程

```js
$(function() {
  $("button").click(function(){
   	$(this).css().siblings("button").css();  // 对自己设置变化,给其他兄弟去掉变化
  })
})
```

#### 隐式迭代

遍历 jQuery 对象内部 DOM 元素（伪数组形式存储）的过程就叫隐式迭代
**简单理解：给匹配的所有元素进行循环遍历，执行相应的方法，而不是我们再进行循环，简化我们的操作，方便我们的调用。**

```js
// 给四个div设置背景颜色为粉色 jquery对象不能使用style
$("div").css("background", "pink");
// 隐式迭代就是把匹配的所有元素内部进行遍历循环，给每一个元素添加css这个方法
$("ul li").css("color", "red");
```

### jQuery 样式操作

#### 修改样式方法

jQuery 可以使用 css 方法来修改简单元素样式；也可以操作类，修改多个样式。

```js
//参数只写属性名，则返回属性值
$(this).css("color");
//操作某一组样式，属性必须加引号，值如果是数字可以不用跟单位和引号
$(this).css("color","red");
//参数可以是对象形式，设置多组样式。属性名和属性值用冒号隔开，属性可以不用加引号
$(this).css({"color":"red","font-size":"20px"});
```

#### 设置样式类方法

```js
$("div").addClass("current");//添加类
$("div").removeClass("current");//移除类
$("div").toggleClass("current");//添加或移除类（切换）
```

### jQuery 属性操作

#### 元素的固有属性 prop()

固有属性就是元素本身自带的属性，如a标签元素里面的herf，input标签元素里面的type。
① 获取属性语法

```js
prop("属性");
```

② 设置属性语法

```js
prop("属性","属性值");
```

③ 移除属性语法

```js
removeProp("属性");
```

#### 元素的自定义属性 attr()

自定义属性就是用户自己给元素添加的属性。比如给li添加index=”1”。
① 获取属性语法

```js
attr("属性");
```

② 设置属性语法

```js
attr("属性","属性值");
```

> 该方法也可用于获取 H5 自定义属性。

③ 移除属性语法

```js
removeAttr("属性");
```

### jQuery 元素内容文本操作

#### 普通元素内容 html()

相当于原生 js 中的 innerHTML

```js
html();  // 获取元素的内容
html("内容");  // 设置元素的内容
```

#### 普通元素文本 text()

相当于原生 js 中的 innerText

```js
text();  // 获取元素的内容
text("内容");  // 设置元素的内容
```

#### 表单值 val()

相当于原生 js 中的 value

```js
val();  // 获取元素的内容
val("内容");  // 设置元素的内容
```

### jQuery 元素操作

#### 遍历元素

隐式迭代：对同一类元素做相同操作。如果做不同操作，需要使用遍历：

```js
$("div").each(function (index, domEle) {xxx;})
```

> - index 是每个元素的索引号，domEle 是每个 DOM 元素对象，不是 jQuery 对象
> - 想要使用 jQuery 方法，就要给这个 dom 元素转换为 jQuery 对象 $(domEle)

#### 创建元素

```js
var divElement = $("<div></div>");//创建一个div
```

#### 操作元素

- append 向每个匹配的元素内部追加内容。
- appendTo 把所有匹配的元素追加到另一个指定的元素元素集合中。
- after 在每个匹配的元素之后插入内容。
- before 在每个匹配的元素之前插入内容。
- insertAfter 把所有匹配的元素插入到另一个、指定的元素元素集合的后面。
- inserBefore 把所有匹配的元素插入到另一个、指定的元素元素集合的前面。
- wrap 把所有匹配的元素用其他元素的结构化标记包裹起来。
- unwrap 这个方法将移出元素的父元素。这能快速取消 .wrap()方法的效果。
- empty 清空元素的内容。
- remove 删除当前元素。
- clone 复制当前元素。

### jQuery 尺寸、位置操作

#### jQuery 尺寸

- width()/height() 取得匹配元素宽度和高度值，只算width/height
- innerWidth()/innerHeight() 取得匹配元素宽度和高度值，包括padding
- outerWidth()/outerHeight() 取得匹配元素宽度和高度值，包括padding和border
- outerWidth(true)/outerHeight(true) 取得匹配元素宽度和高度值，包括padding、border和margin
  - 以上参数为空，则获取响应值，返回的是数字型
  - 如果参数未数字，则是修改相应值
  - 参数可以不写单位

#### jQuery 位置

① offset() 设置或获取元素偏移

- offset() 方法设置或返回被选元素相对于文档的偏移坐标，跟父级没有关系。
- 该方法有2个属性 left 和 top。offset().top 用于获取距离文档顶部的距离，offset().left 用于获取距离文档左侧的距离。
- 可以设置元素的偏移：offset({top:10,left:10})。

② position() 获取元素偏移

- position() 方法用于返回被选元素相对于带有定位的父级偏移坐标，如果父级没有定位，则以文档为准。
- 该方法只能获取不能设置偏移

③ scrollTop()/scrollLeft() 设置或获取元素被卷去的头部或左侧

- scollTop() 方法设置或返回被选元素卷去的头部。
- scollLeft() 方法设置或返回被选元素卷去的左侧。

## jQuery 事件

### 事件注册

单个事件注册

```js
element.事件(function);
$("div").click(function(){ 事件处理程序 })  // 其他事件和原生事件基本一致
```

### 事件处理

#### 绑定事件 on()

```js
on(events,[selector],[data],fn)
```

- 概述
  - 在选择元素上绑定一个或多个事件的事件处理函数。
  - on() 方法绑定事件处理程序到当前选定的 jQuery 对象中的元素。
- 显示参数
  - events:一个或多个用空格分隔的事件类型和可选的命名空间，如”click”或”keydown.myPlugin” 。
  - selector:一个选择器字符串用于过滤器的触发事件的选择器元素的后代。如果选择的< null或省略，当它到达选定的元素，事件总是触发。
  - data:当一个事件被触发时要传递event.data给事件处理函数。
  - fn:该事件被触发时执行的函数。 false 值也可以做一个函数的简写，返回false。

#### 解绑事件 off()

```js
off(events,[selector],[fn])
```

- 概述
  - 在选择元素上移除一个或多个事件的事件处理函数。
  - off()方法移除用on()绑定的事件处理程序
- 显示参数
  - events:一个或多个空格分隔的事件类型和可选的命名空间，或仅仅是命名空间，比如”click”, “keydown.myPlugin”, 或者 “.myPlugin”。
  - selector:一个最初传递到 .on() 事件处理程序附加的选择器。
  - fn:事件处理程序函数以前附加事件上，或特殊值 false。

#### 绑定一次性事件 one()

```js
one(type,[data],fn)
```

- 概述
  - 为每一个匹配元素的特定事件（像click）绑定一个一次性的事件处理函数。
- 显示参数
  - type:添加到元素的一个或多个事件。由空格分隔多个事件。必须是有效的事件。
  - data:将要传递给事件处理函数的数据映射。
  - fn:每当事件触发时执行的函数。

#### 自动触发事件 trigger()

有些事件希望自动触发，比如轮播图自动播放功能与点击右侧按钮功能一致，可以利用定时器自动触发右侧按钮点击事件，不必鼠标点击触发。

```js
el.click();  // 简写形式
el.trigger("type");  // 自动触发形式
el.triggerHandler("type");  // 自动触发形式，不会触发元素的默认行为
```

### 事件对象

事件被触发，就会有事件对象的产生。

```js
on(events,[selector],function(even) { })
even.preventDefault()  // 阻止默认行为 或者用 return false
even.stopPropagation()  // 阻止冒泡
```
