# 面试题整理
## 清浮动的几种方法
- 额外标签法
```
<div style="clear:both"></div>
```
> 缺点：增加额外的标签，使html结果看起来不简洁
- 使用`after`或者`before`伪类
```
p:after{
	content:'';
	height:0;
	display:block;
	clear:both;
}
```
> 优点：不容易出现奇怪问题，目前很多大型网站都用这个
> 缺点：IE8以上的浏览器才支持
- 浮动外部元素
> 缺点：会产生新的浮动问题，这个不建议使用
- 给父级元素设置`overflow:hidden`
> 优点：简单，代码少，浏览器支持好
> 缺点：不能和position配合使用，因为超出的尺寸会被隐藏

## css hack
#### css hack 的原理
由于不同的浏览器和浏览器各版本对CSS的支持及解析结果不一样，以及CSS优先级对浏览器展现效果的影响，我们可以针对不同的浏览器情景来应用不同的CSS
#### CSS hack的分类
- CSS属性前缀法
> 属性前缀法是在CSS样式属性名前加上一些只有特定浏览器才能识别的hack前缀，从而达到预期的页面效果
![Alt text](./1505970054580.png)
```
“-″减号是IE6专有的hack
“\9″ IE6/IE7/IE8/IE9/IE10都生效
“\0″ IE8/IE9/IE10都生效，是IE8/9/10的hack
“\9\0″ 只对IE9/IE10生效，是IE9/10的hack
```
- 选择器前缀法
> 选择器前缀法是针对一些页面表现不一致或者需要特殊对待的浏览器，在CSS选择器前加上一些只有某些特定浏览器才能识别的前缀进行hack
```
目前最常见的是

*html *前缀只对IE6生效
*+html *+前缀只对IE7生效
@media screen\9{...}只对IE6/7生效
@media \0screen {body { background: red; }}只对IE8有效
@media \0screen\,screen\9{body { background: blue; }}只对IE6/7/8有效
@media screen\0 {body { background: green; }} 只对IE8/9/10有效
@media screen and (min-width:0\0) {body { background: gray; }} 只对IE9/10有效
@media screen and (-ms-high-contrast: active), (-ms-high-contrast: none) {body { background: orange; }} 只对IE10有效
```
- IE条件注释法
```
只在IE下生效
	<!--[if IE]>
	这段文字只在IE浏览器显示
	<![endif]-->
	
	只在IE6下生效
	<!--[if IE 6]>
	这段文字只在IE6浏览器显示
	<![endif]-->
	
	只在IE6以上版本生效
	<!--[if gte IE 6]>
	这段文字只在IE6以上(包括)版本IE浏览器显示
	<![endif]-->
	
	只在IE8上不生效
	<!--[if ! IE 8]>
	这段文字在非IE8浏览器显示
	<![endif]-->
	
	非IE浏览器生效
	<!--[if !IE]>
	这段文字只在非IE浏览器显示
	<![endif]-->
```
## 对CSS盒模型的认识
### 标准模型和ie模型的区别
- 标准模型中属性宽高值不包含padding和border值
- IE盒子模型中属性宽高包含padding和border（IE9以下的浏览器）
### 如何设置这两个模型
一般HTML文档的头部有文档类型声明DOCTYPE的是标准模型，没有的就是IE盒子模型了，可以用jQuery中的方法进行测试
```
$.support.boxModel 如果该方法执行返回true就表示是w3c盒子模型
```
### js如何设置和获取盒模型的宽高
- 内容的宽高
```
width:真实的内容的宽度
height：真实的内容的高度
```
- client系列：当前元素的几个私有属性
```
clientWidth: 内容的宽度+左右padding值
clientHeight：内容的高度+左右padding值
clientLeft：左边框的宽度
clientRight：右边框的宽度
```
- offset系列
```
offsetWidth:clientWidth+左右边框
offsetHeight：clientHeight+上下边框
```
- scroll系列
	- 内容无溢出的时候
```
scrollWidth：跟clientWidth一样
scrollHeight：跟clientHeight一样
```
	- 内容有溢出的时候
```
scrollWidth：真实内容的宽度+左padding
scrollHeight：真实的内容的高度+上padding
```
- 滚动条的移动距离
```
scrollLeft：滚动条卷曲的宽度
scrollTop：滚动条卷曲的高度
```
> 只有`scrollLeft`和`scrollTop`是可写属性，其他都是可读的

#### 优雅降级和渐进增强
- 　优雅降级 graceful degradation：一开始就构建完整的功能，然后再针对低版本浏览器进行兼容。
- 渐进增强 progressive enhancement：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。

### BFC
#### 什么是BFC
- BFC（block formatting context ：块级格式化上下文）是W3CCS规范中的一个概念，它决定了元素如何对其内容进行定位，以及与其他元素的关系和相互作用，在CSS3中，BFC叫做Flow  Root
-  形成 BFC 的条件(符合以下任一条件即可)： 
	-	float 的值不为 none；
	- overflow 的值不为 visible；
	-  display 的值为  table-cell、table-caption 和 inline-block 之一；
	- position 的值不为  static 或  relative 中的任何一个；

#### 创建BFC
- 浮动元素会形成BFC

#### BFC常见作用
- 包含浮动元素 ：BFC会根据元素的情况自 适应 高度 ，这个 特性 是 对父元素 使 用overflow:hidden/auto/scroll、 float:left/right 样式可以闭合浮动的原理。
- 不被浮动元素覆盖：浮动元素会无视兄弟元素的存在， 覆盖在兄弟元素的上面， 为该兄弟元素创建BFC 后可以阻止这种情况的出现
- 阻止父子元素的margin折叠：仅当两个块级元素毗
邻并且在同一个块级格式化上下文时，它们垂直方向之间的外边距才会叠加。也就是说，即便两个块级元素相邻，但当它们不在同一个块级格式化上下文时它们的边距也不会折叠

## js原生
1、 js中有哪些数据类型：
- 基本数据类型
	- number、string、Boolean、null、undefined、RegExp、Date
- 引用数据类型
	- array、object、function
2、什么是闭包？闭包作用？应用
- 函数执行形成的私有作用域，可以保护定义的私有变量不受外界干扰，这种机制叫做闭包 
- 保护函数内的变量不受外界干扰
- 起到隔离作用，可以防止与其他代码冲突

3、创建对象的三种方式
- 字面量创建
```
var obj={}
```
- 实例创建
```
var obj=new Object()
```
- 原型方式：不常用
4、new Person()时发生了什么?
- 生成了一个Person类的实例
5、什么是深拷贝和浅拷贝？自己不用JSON.parse实现一个深拷贝的方法
- 深拷贝指的是对象中，里面存放的对象和以前存放的对象毫无关系，但是长得一样（地址不一样了）
- 浅拷贝指的是对象中，里面存放的内容和以前的是同一个引用地址
- 循环递归拷贝实现深拷贝
```
var obj={name:'mm',id:1};
let arr={...obj};
console.log(arr);
```
6、手工模拟完整的bind方法
```
 Function.prototype._bind= function (that) {
        var fn=this;
        var params=[].slice.call(arguments,1);
        return function () {
            var arr=params.length?params:arguments;
            fn.apply(that,arr);
        }
    }
```
7、什么是节流和防抖?
函数节流和函数防抖，两者都是优化高频率执行js代码的一种手段：把js代码的执行次数控制在合理的范围。既能节省浏览器CPU资源，又能让页面浏览更加顺畅，不会因为js的执行而发生卡顿。这就是函数节流和函数防抖要做的事
- 函数节流是指一定时间内js方法只跑一次，比如人的眨眼睛，就是一定时间内眨一次，
	- 函数节流应用的实际场景，多数在监听页面元素滚动事件的时候会用到。因为滚动事件，是一个高频触发的事件
- 函数防抖是指频繁触发的情况下，只有足够的空闲时间，才执行代码一次，比如生活中的坐公交，就是一定时间内，如果有人陆续刷卡上车，司机就不会开车。只有别人没刷卡了，司机才开车，
	- 函数防抖的应用场景，最常见的就是用户注册时候的手机号码验证和邮箱验证了。只有等用户输入完毕后，前端才需要检查格式是否正确，如果不正确，再弹出提示语

8、上拉刷新和下拉加载的实现原理？
- 利用 scrollTop  一屏的高度 和 元素最低端距离body的偏移值
9、写一个验证邮件的正则表达式
```
var reg=/^[\w.-]+@[0-9a-z]+(\.[a-zA-Z]{2,4}){1,2}$/
```
10、事件绑定和普通事件的区别（可以举例说明）
- 事件绑定就是针对DOM元素的事件，绑定在DOM元素上，绑定多次，执行多次
```
div.addEventListener('click',function(){
	alert(1)
})
div.addEventListener('click',function(){
	alert(2)
})
//会弹出 1 2
```
- 普通事件就是非针对DOM元素的事件，绑定多次，执行一次
```
div.onclick=function(){
	alert(1)
}
div.onclick=function(){
	alert(1)
}
会弹出 2 只会弹会最后一次的事件，因为后面的会把前面的覆盖掉
```
11、javascript 模版引擎用过哪些？实现原理是什么？
- 模板字符串 这是es6中的语法，借用反引号，`${变量}`利用这种方法取值 
- ejs  利用`<%=变量%>`取值
12、合并两个对象
- 1、`Object.assign(oldObj,newObj)`
- 2、`{...obj1,...obj2}`
13、动态向一个div中插入1000个div标签，如何实现？（考性能）
- 字符串拼接 ：只引发一次回流
```
var str='';
for(var i=0;i<1000;i++){
	str+='<div></div>'
}
div.innerHTML+=str;
```
- 动态创建
```
    var frg=document.createDocumentFragment();//创建文档碎片
    for(var i=0;i<1000;i++){
        var div=document.createElement('div');
        frg.appendChild(div);
    }
    console.log(frg);
    div.appendChild(frg);//将文档碎片里的内容一次性的添加到外层div中
    frg=null;//将文档碎片里的内容清空
```
14、h5的新特性
- 语义化标签：便于移动设备更好的读取
> article ：文章、博客、区域
> aside：对于侧边辅助广告信息等
> footer：底部、页脚
> header：头部（可以是页面中的，也可以是文章中的）
> hgroup：标题组
> main：唯一的主体部分
> section：划分区域的，效果同div类似
> nav：导航部分
> figure：图片的展示区
> figcaption：图片标题
- 媒介、音视频audio、video、object
- canvas绘画
- 地理定位
- 本地存储`localStorge`
- `websocket` 客户端与移动端的一中交互数据 一种通信协议
- 表单控件
- 功能标签
> progress ：进度条
> detalist：下拉列表
> mark：标记文本
> details：详情描述
> summary：展示详情信息
- 表单属性
> autofocus：自动获取焦点，默认为true
> autocomplete：自动完成功能提示，默认为on
> required：value为空验证，必须填写
> pattern：验证是否符合正则表达式（属性值是正则表达式）
> placeholder：填写提示信息（填写的时候，提示信息自动消)
15、严格模式和非严格模式的区别
- 严格模式是一种将更好的错误检查引入代码中的方法，在使用严格模式时，无法使用隐式声明的变量，将值赋给只读属性或者将属性添加到不可扩展的对象等
	- 严格模式的目的
		- 消除JavaScript语法的一些不合理，不严谨之处，减少一些怪异行为
		- 消除代码运行的一些不安全之处。包装代码运行的安全
		- 提高编译器效率，增加运行速度
		- 为未来新版本的JavaScript做好铺垫

> 使用严格模式，需要在代码开头添加`"use strict"`来声明是严格模式下的
16、对于js中浮点数计算会丢失精度的问题，你有什么解决思路？
可以使用`math.parser().eval()`来获取准确的值

## jQuery 
### jquery.extend , jquery.fn.extend的区别
- `jquery.extend` 是在jQuery的原型上扩展方法
- `jquery.fn.extend`是在jQuery的实例的原型上扩展方法
### 谈一下jquery中的bind，live，delegate，on区别
- bind是只负责事件绑定
- delegate只负责事件委托，将事件委托给指定容器
- live是实现事件委托的，可以直接实现当前元素调用，它会默认将事件委托给document，但是在1.9以上的版本已经废弃了，如果想使用live，需要安装插件`query-migrate.min.js`
- on 既可以实现事件绑定，也可以实现事件委托，可以指定事件委托的元素
### document.ready和document.load和$(function(){})有什么区别？
- docment.ready：表示文档结果已经加载完成（不包含图片等非文字文件）
- document.load：要等DOM结构、html页面中的文件、图片加载完成后才会执行
- $(function(){})：等DOM结构加载完成后，就会执行

### $.data()和$('#aaa').data()各自作用是什么？有什么区别
-` $.data() `方法允许我们在DOM元素上绑定任意类型的数据,避免了循环引用的内存泄漏风险。
- $('#aaa')取得的是一个jQuery对象

## es6
### 什么时候应该用箭头函数？什么时候不能用？ 
- 不获取函数的arguments的时候可以用箭头函数
- 不使用函数的this的时候可以用箭头函数
- 如果函数中使用了this，且需要this指向为上一级作用域时，可以用箭头函数

-  请写出ES6中Array.isArray()的实现代码
```
function myArray(arr) {
    if(Object.prototype.toString.call(arr)==='[object Array]'){
        return true
    }else {
        return false
    }
}
console.log(myArray([1,2]));
```
- 如何在项目中解析处理es6和es7代码
	- 在项目中建一个`.babelrc`文件，配置下面的代码即可
```
{
  "presets": ["es2015","stage-0"]
}
```
### Promise常用方法，Promise.race的作用，then方法里reject和catch的区别
- Promise常用方法
- Promise.race的作用：实现选择  
	- 接受一个数组，数组内都是Promise实例,返回一个Promise实例，这个Promise实例的状态转移取决于参数的Promise实例的状态变化。当参数中任何一个实例处于resolve状态时，返回的Promise实例会变为resolve状态。如果参数中任意一个实例处于reject状态，返回的Promise实例变为reject状态。
```
const fs = require('fs');
let p1 =  new Promise((resolve,reject)=>{
    fs.readFile('./name.txt','utf8',function (err,data) {
        resolve(data);
    });
})
let p2 = new Promise((resolve,reject)=>{
    fs.readFile('./age.txt','utf8',function (err,data) {
        resolve(data);
    });
})
Promise.race([p1,p2]).then(([res1,res2])=>{
    console.log(res1,res2);
})
```
