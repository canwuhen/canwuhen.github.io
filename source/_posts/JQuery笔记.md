---
title: JQuery笔记
date: 2022-07-11 13:29:40
cover: /gallery/20230626212619.jpg
tags: js
toc: true
---

jQuery时一个快速简洁的JavaScript库，其设置宗旨是“write Less，Do More”，即提倡写更少的代码。j就是Javascript；Query 查询；意思是查询js，把js中的DOM操作做了封装，我们可以快速查询使用里面的功能。

jQuery封装了JAvaScript常用的功能代码，优化了Dom操作、处理事件、动画设计和Ajax交互

<!-- more -->

# jQuery概述

jQuery时一个快速简洁的JavaScript库，其设置宗旨是“write Less，Do More”，即提倡写更少的代码。j就是Javascript；Query 查询；意思是查询js，把js中的DOM操作做了封装，我们可以快速查询使用里面的功能。

jQuery封装了JAvaScript常用的功能代码，优化了Dom操作、处理事件、动画设计和Ajax交互

<!-- more -->

**常见的JavaScript库**

- jQuery
- Prototype
- YUI
- Dojo
- Ext JS
- 移动端

**jQuery优点**：

- 轻量级

- 跨浏览器兼容

- 链式编程

- 对事件样式动画支持，大大简化了DOM操作

- 支持插件扩展开发：树形菜单，轮播图等

- 开源，免费

官网：https://jquery.com

# jQuery基本使用

## 1.入口函数

等着DOM结构完成即可执行内部代码，不必等所有摆布资源加载完成，jQuery帮我们完成了封装。相当于原生js中的`DOMContentLoaded`。

传统方式：

```js
$(document).ready(function (){
        $('div').hide();
    ··· //此处是页面DOM加载完成的入口
    })
```

**常用方式：**

```js
 $(function (){
        $('div').hide();
    ··· //此处是页面DOM加载完成的入口
    })
```

## 2.jQuery的顶级对象$

`$`是jQuery的别称，在代码中可以使用jQuery代替$,但为了方便通常都是直接使用$

## 3.jQuery对象和DOM对象

```js
   //1.DOM对象：
   var myDiv = document.querySelector('div');
   //2.jQuery对象:
   $('div');
```

- 用原生JS获取来的对象就是DOM对象

- jQuery方法获取来的元素就是jQuery对象

- jQuery对象的本质是：利用$对DOM对象包装产生的对象（伪数组形式存储）

- **注:**jQuery对象只能使用jQuery方法，DOM对象则使用原生的JavaScript属性和方法

  

DOM和jQuery对象之间是可以相互转换的，因为js比jQuery更大，jQuery只是封装了常用的属性和方法，想要使用js的方法需要将jQuery对象转换为DOM对象才能使用。

```js
    /**
     * 1.DOM对象转换为jQuery对象
     */
    var myvideo = document.querySelector("video");
    // $(myvideo).play();//报错jquery没有play方法
    /**
     * 2.jQuery对象转换为DOM对象
     */
    //video 便签加入muted属性才能自动播放
    $(myvideo)[0].play();
    $(myvideo).get(0).play();
```

# jQuery常用API

## 	jQuery选择器

​	原生js获取元素方式很多，很杂，而且兼容性情况不一致，因此jQuery给我们做了封装，使获取元素统一标准。

```js
$("选择器")//里面直接写CSS选择器即可，但是要加引号
```

jQuery设置样式

```js
// 2.给四个div设置背景颜色
$("div").css("background","pink")
```

### 隐式迭代（重要）

遍历内部DOM元素(伪数组信息存储)的过程就叫隐式迭代

简单理解：给匹配到的所有元素进行遍历循环，执行相应的方法，而不用我们再进行循环，简化我们的操作，方便我们调用

### 筛选选择器

| 语法       | 用法          | 描述                                                       |
| ---------- | ------------- | ---------------------------------------------------------- |
| :first     | $('li:first') | 获取第一个li元素                                           |
| :last      | $("li:last')  | 获取最后一个li元素                                         |
| :eq(index) | $("Ii:eq(2)") | 获取到的li元素中，选择索引号为2的元素，索引号inde从0开始。 |
| :odd       | $("li:odd")   | 获取到的li元素中，选择索引号为奇数的元素                   |
| :even      | $("li:even")  | 获取到的li元素中，选择索引号为偶数的元素                   |

### 筛选方法（重点）

| 语法               | 用法                              | 说明                                                   |
| ------------------ | --------------------------------- | ------------------------------------------------------ |
| parent()           | $("li").parent();                 | 查找父级                                               |
| children(selector) | $("ul").children("li")            | 相当于$("ul>li")，最近一级(亲儿子)                     |
| find(selector)     | $("ul").find("li");               | 相当于$("ul li"),后代选择器                            |
| siblings(selector) | s(".first").siblings("li"");      | 查找兄弟节点，不包括自己本身                           |
| nextAll([expr])    | $(".first" ) .nextAll()c          | 查找当前元素之后所有的同辈元素                         |
| prevtAll([expr])   | $(".last" ).prevAll()             | 查找当前元素之前所有的同辈元素                         |
| hasClass(class)    | $( 'div ' ).hasclass("protected") | 检查当前的元素是否含有某个特定的类，如果有，则返回true |
| eq(index)          | $("li").eq(2);                    | 相当于$("li:eq(2)" ) ,index从0开始                     |

**重点：parent() childern() find() siblings() eq()**

**补充：**

| 语法            | 用法                        | 说明             |
| --------------- | --------------------------- | ---------------- |
| parents([expr]) | $(div).parents(".p_number") | 返回指定祖先元素 |



## jQuery样式操作

> jQuery 可以使用 css 方法来修改简单元素样式； 也可以操作类，修改多个样式。  

### 操作css


```js
//1. 参数只写属性名，则是返回属性值
$(this).css("color");
//2. 参数是属性名， 属性值，逗号分隔， 是设置一组样式，属性必须加引号，值如果是数字可以不用跟单位和引号
$(this).css("color","red");
//3. 参数可以是对象形式， 方便设置多组样式。属性名和属性值用冒号隔开， 属性可以不用加引号
$(this).css({
    "color":"white",
    "font-size":"20px"
});
```

### 设置类样式方法

`addClass("类名");`：添加类

`removeClass("类名");`：移除类

`toggleClass("类名");`：切换类



### jQuery效果

jQuery封装了很多动画效果

#### 显示隐藏

```
show([speed,[easing],[fn]])  
```

```
hide([speed,[easing],[fn]])
```

```
toggle([speed,[easing],[fn]])
```

**参数：**

1. 参数都可以省略， 无动画直接显示。
2. speed：三种预定速度之一的字符串(“slow” ,“normal” , or “fast” )或表示动画时长的毫秒数值(如： 1000)。
3. easing： (Optional) 用来指定切换效果， 默认是“swing”， 可用参数“linear”。
4. fn: 回调函数，在动画完成时执行的函数，每个元素执行一次。  

#### 滑动效果

```
slideDown([speed,[easing],[fn]])
```

```
slideUp([speed,[easing],[fn]])
```

```
slideToggle([speed,[easing],[fn]])
```

#### 事件切换

```js
hover([over,]out)
$(".nav>li").hover(function(){
    $(this).children("ul").slideToggle();
})
```

**参数：**

1. over:鼠标移到元素上要触发的函数（相当于mouseenter）
2. out:鼠标移出元素要触发的函数（相当于mouseleave）
3. 如果只写一个函数，则鼠标经过和离开都会触发它    

#### 动画队列

> 动画或者效果触发就会执行，如果多次触发就会造成多个动画或者效果排队执行。

`stop()`停止排队

只需要在动画方法前添加stop()即可解决动画排队问题

#### 淡入淡出效果

淡入

```
fadeIn([speed,[easing],[fn]])
```

淡出

```
fadeIn([speed,[easing],[fn]])
```

切换

```
fadeIn([speed,[easing],[fn]])
```

更换透明度

```js
//speed和opacity必写
fadeTo([speed,opacity,[easing],[fn]])
```

**参数：**opacity：透明度，取值[0-1]

#### 自定义动画

```js
animate(params,[speed],[easing],[fn])
```

**参数：**

- params: 想要更改的样式属性，以对象形式传递，必须写。 属性名可以不用带引号， 如果是复合属性则需要采用驼峰峰命名法 borderLeft。 其余参数都可以省略。
- speed：三种预定速度之一的字符串(“slow” ,“normal” , or “fast” )或表示动画时长的毫秒数值(如： 1000)。
- easing： (Optional) 用来指定切换效果， 默认是“swing”， 可用参数“linear”。
- fn: 回调函数，在动画完成时执行的函数，每个元素执行一次  

## jQuery属性操作

> 所谓元素固有属性就是元素本身自带的属性，比如<a元素里面的href，比如<input元素里面的type。
>
> 用户自己给元素添加的属性， 我们称为自定义属性。 比如给 div 添加 index =“1” 。  

 **获取属性：**

`prop("属性")` 或`attr("属性")`

**修改属性：**

`prop("属性","属性值")` 或 `attr("属性")`

prop()只能获取元素的固有属性，而attr()能获取自定义属性，以及H5自定义属性。

### 数据缓存data()

data() 方法可以在指定的元素上存取数据，并不会修改 DOM 元素结构。一旦页面刷新，之前存放的数据都将被移除。  

```js
data("name","value") // 向被选元素附加数据
date("name") // 向被选元素获取数据
```

同时，还可以读取 HTML5 自定义属性 data-index ，得到的是数字型  

## jQuery文本内容

**普通元素内容**

`html()` 获取元素的内容

`html("内容")` 设置元素的内容

**普通元素文本内容**

`text()` 获取文本内容（忽略标签）

**获取设置表单值**

`val()` 获取表单值

`val("设置值")` 修改表单值

## jQuery元素操作

> 主要是遍历、创建、添加、删除元素

### 遍历元素

jQuery 隐式迭代是对同一类元素做了相同的操作。如果想要给同一类元素做不同操作，就需要用大遍历。

用法一：

```js
$("div").each(function(index,domEle){
...;
})
```

- each() 方法遍历匹配的每一个元素。主要用DOM处理。 each 每一个
- 里面的回调函数有2个参数： index 是每个元素的索引号; demEle 是每个DOM元素对象，不是jquery对象所以要想使用jquery方法，需要给这个dom元素转换为jquery对象 $(domEle)  

用法二：`.$each()`方法遍历元素，主要用于遍历数据、处理数据

```js
//1.遍历元素
$.each("div",function(i,ele){
...;
//i是标签 ele是标签内文本
})
//2.遍历数组
var arr=["red" ,"blue","pink"]
$.each(arr,function(index,domEle){
...;
})
//3.遍历对象
$.each({name:"张三"，
       age：18},function(index,domEle){
...;
//i是属性名 ele是属性值
})
```

### 创建元素

`$("<li><li>")`动态创建了一个li

### 添加元素

#### 1.内部添加

`element.append("内容")`把内容放入匹配元素内部最后面，类似原生appendChild

`element.prepend("内容")` 把内容放入匹配元素内部最前面

#### 2.外部添加

`element.after(''内容'')  ` 把内容放入目标元素后面

`element.before(''内容'')  ` 把内容放入目标元素前面

- **① 内部添加元素，生成之后，它们是父子关系。**
- **② 外部添加元素，生成之后，他们是兄弟关系。**  

## 删除元素

`element.remove()` 删除匹配元素（本身）

`element.empty()` 删除匹配的元素集合中所有的子节点

`element.html("")`清空匹配的元素内容

- **① remove 删除元素本身。**
- **② empt() 和 html('''') 作用等价，都可以删除元素里面的内容，只不过 html 还可以设置内容。**  

## jQuery尺寸、位置操作

### 尺寸

`width()/height()` 获取匹配元素宽度和高度值 只算width/height

`innerWidth()/innerHeight()` 获取匹配元素宽度和高度值 包括padding

`outerWidth()/outerHeight()` 获取匹配元素宽度和高度值 包括padding、borde

`outerWidth(true)/outerHeight(true)` 获取匹配元素宽度和高度值 包括padding、borde、margin

- 以上参数为空，则是获取相应值，返回的是数字型。
- 如果参数为数字，则是修改相应值。
- 参数可以不必写单位。

### 位置

1. `offset()` **设置或获取元素偏移**

- offset() 方法设置或返回被选元素相对于文档的偏移坐标，跟父级没有关系。

- 该方法有2个属性 left、 top 。 offset().top 用于获取距离文档顶部的距离， offset().left 用于获取距离文档左侧的距离。
- 可以设置元素的偏移： offset({ top: 10, left: 30 });  

2. `position()` **获取元素偏移**  

- position() 方法用于返回被选元素相对于带有定位的父级偏移坐标，如果父级都没有定位，则以文档为准。

- ② 该方法有2个属性 left、 top。 position().top 用于获取距离定位父级顶部的距离， position().left 用于获取距离定

- 位父级左侧的距离。

- ③ 该方法只能获取。  

3. `scrollTop()/scrollLeft()` 设置或获取元素被卷去的头部和左侧  

-  scrollTop() 方法设置或返回被选元素被卷去的头部。
-  不跟参数是获取，参数为不带单位的数字则是设置被卷去的头部。  

## 互斥锁



# jQuery事件

## 事件处理

### on()绑定事件

**`on()` 方法优势1：**在匹配元素上绑定一个或多个事件的处理函数

```
element.on(events,[selector],fn)
```

- events:一个或多个用空格分隔的事件类型，如"click"或"keydown" 。
- selector: 元素的子元素选择器 。
- fn:回调函数 即绑定在元素身上的侦听函数。  

```js
   //多个事件不同操作
        $("div").on({
            mouseenter: function () {
                    $(this).css("background", "skyblue")
            },
            click:function () {
                $(this).css("background", "red")
            },
            mouseleave: function () {
                $(this).css("background", "pink")
            },

            })

        //多个事件同一操作
        $("div").on("mouseenter mouseleave", function () {
            $(this).toggleClass("current")
        });
```

**`on()`方法优势2：**可以实现事件委派（事件委派就是把原来加给子元素身上的事件绑定在父元素身上，把事件委派给父元素）

```
$("ul").on("click","li",function(){
alert(11;)
})
```

在此之前有bind()，live()，delegate()等方法来处理或者委派事件，新版本推荐使用`on()`方法代替。

**`on()`方法优势3：**动态创建的元素click()没有办法绑定事件，on()可以给动态生成的元素绑定事件。

```js
 $("ol").on("click","li",function () {
            alert(22);
        })
        var li = $("<li>我是后来追加的小li</li>");
        $("ol").append(li)
```

### off()解绑事件

off() 方法可以移除通过 on() 方法添加的事件处理程序。  

```js
$("p").off() // 解绑p元素所有事件处理程序
$("p").off( "click") // 解绑p元素上面的点击事件 后面的 foo 是侦听函数名
$("ul").off("click", "li"); // 解绑事件委托
```

如果有的事件只想触发一次， 可以使用 **`one()`** 来绑定事件。  

### trigger()自动触发事件

有些事件希望自动触发, 比如轮播图自动播放功能跟点击右侧按钮一致。可以利用定时器自动触发右侧按钮点击事件，不必鼠标点击触发。  

```js
//1.div触发点击事件
$("div").click(function({
	alert("hello world");
}));
//2.自动触发div点击事件
$("div").on("click",function({
	alert("hello world");
});
$("div").trigger("click");
//3.triggerHandler() 不会触发元素的默认行为，例如焦点表单中不会有光标产生
$("input").on("focus",function(){
    $(this).val("你好");
});
$("input").triggerHandler("focus");
```



## 事件对象

只要有事件触发就会有事件对象的产生

阻止默认行为： `event.preventDefault()` 或者 `return false`
阻止冒泡： `event.stopPropagation()`  



# even 

## 其他方法

`toFixed(n)` 保留n位小数

`$.extend([deep], target, object1, [objectN])`  拷贝对象

> - deep: 如果设为true 为深拷贝， 默认为false 浅拷贝
>   - 浅拷贝，是把被拷贝的对象复杂数据类型中的地址拷贝给目标对象，修改目标对象会影响被拷贝对象。
>   - 深拷贝，前面加true， 完全克隆(拷贝的对象,而不是地址)，修改目标对象不会影响被拷贝对象。 
> - target: 要拷贝的目标对象
> - object1:待拷贝到第一个对象的对象。
> - objectN:待拷贝到第N个对象的对象。 

## 多库共存

jQuery使用$作为标示符， 随着jQuery的流行,其他 js 库也会用这$作为标识符， 这样一起使用会引起冲突。  

`$.noConflict()`  ：jQuery 变量规定新的名称

```js
var suibian=jQuery.noConfilect();
suibian("div")
```



## Jquery插件网站

1. jQuery 插件库 http://www.jq22.com/
2. jQuery之家 http://www.htmleaf.com/
