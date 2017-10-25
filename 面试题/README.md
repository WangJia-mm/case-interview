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

### 浏览器的兼容问题（js）
> http://www.cnblogs.com/duenyang/p/6066737.html

## js原生
1、 js中有哪些数据类型：
- 基本数据类型
	- number、string、Boolean、null、undefined、
- 引用数据类型
	- array、object、function、RegExp、Date

2、什么是闭包？闭包作用？应用
> http://blog.csdn.net/sunlylorn/article/details/6534610

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
- 构造函数模式
```
function Person() {
    this.name = "dongjc";    //通过this关键字设置默认成员
    var worker = 'coding';    //没有this关键字，对象创建后，该变量为非成员
    this.age = 32;
    this.Introduce = function () {
         alert("My name is " + this.name + ".I'm " + this.age);
      };
     alert("My name is " + this.name + ".I'm " + this.age);
 };
 var person = new Person();
 person.Introduce();
```
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
- 2、`{...{name:2},...{age:3}}`
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
更详细的戳下面
http://www.mamicode.com/info-detail-1598100.html
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

###  $.data()和$('#aaa').data()各自作用是什么？有什么区别
-` $.data() `方法允许我们在DOM元素上绑定任意类型的数据,避免了循环引用的内存泄漏风险。
- $('#aaa')取得的是一个jQuery对象

## es6
### 什么时候应该用箭头函数？什么时候不能用？ 
- 不获取函数的arguments的时候可以用箭头函数
- 不使用函数的this的时候可以用箭头函数
- 如果函数中使用了this，且需要this指向为上一级作用域时，可以用箭头函数
- 在需要动态上下文的场景中使用箭头函数你要格外的小心，这些场景包括：定义对象方法、定义原型方法、定义构造函数、定义事件回调函数。
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
#### Promise常用方法
- promise.all：实现并行
接受一个数组，数组内都是Promise实例，返回一个Promise实例，这个Promise实例的状态转移取决于参数的Promise实例的状态变化。当参数中所有的实例都处于resolve状态时，返回的Promise实例会变为resolve状态。如果参数中任意一个实例处于reject状态，返回的Promise实例变为reject状态
```
let fs = require('fs');
let p1 =  new Promise((resolve,reject)=>{
    fs.readFile('./name.txt','utf8',function (err,data) {
        resolve(data);
    });
});
let p2 = new Promise((resolve,reject)=>{
    fs.readFile('./age.txt','utf8',function (err,data) {
        resolve(data);
    });
});
Promise.all([p1,p2]).then(([res1,res2])=>{
    console.log(res1);
});
```
- promise.race：实现选择
- promise.resolve：返回一个Promise的实例，这个实例处于resolve状态
```
Promise.resolve('失败').then(data=>{
	console.log(data)
},err=>{
	console.log(err)
})
```
- Promise.reject：返回一个实例，这个实例处于reject状态
```
Promise.resolve('成功').then(data=>{
	console.log(data)
})
```
#### Promise.race的作用：实现选择  
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
#### then方法里reject和catch的区别
reject是当代码有异常时，会执行该方法里的逻辑，
catch是捕获异常，而且是全局异常捕获

## 工程化
### 什么叫模块化？你用过哪些模块化解决方案？
模块化：我们把每一个 .js 文件都视为一个 模块，模块内部有自己的作用域，不会影响到全局。并且，我们 约定一些关键词来进行依赖声明和 API 暴露。而这些约定的关键词就是通过制定一些 规范 去进行规范的。
用过的模块化开发：Swiper、fs、http、path、url、isScroll、less、mime、express、等，比较多，就不一一列举了
#### 什么叫组件化？你在工作中是如何实现组件化的？
组件化：将模板、样式和逻辑都抽象出来独立出来的做法称之为 组件化。
工作中实现组件化：比如我们在开发button组件的时候，不再需要分别在几个文件夹之间切来切去，去修改他们的模块、样式。逻辑，我们只需要在button组件的文件夹里修改就好了
现在比较常用的组件化库就是vue和react
#### gulp和webpack的相同点和不同点?
- 相同点：都能实现文件的合并与打包
- 不同点：
	- 侧重点不同：
		- gulp侧重于前端开发的整个过程的控制管理
		- webpack侧重于模块打包
	- 模块化开发：两者的配置不同
	- 文件合并与压缩
		- gulp压缩css和js都要安装插件
		- webpack只需要在配置文件中进行配置即可
更多详细的区别请参考：http://www.jianshu.com/p/b1022d224817
#### 热加载
当你在开发环境修改代码后，不用刷新整个页面就可以看到修改后的效果，如果使用了webpack，在package.json文件中对script进行配置`"dev": "webpack-dev-server --open",`，就可以实现热加载

## 框架
### 前端路由的实现原理
根据不同的路径切换到不同 的页面，返回不同的内容

### MVVM框架解决了什么问题？带来了什么问题？
#### 解决的问题：
- 分工明确：
	- View: 对应于Activity和XML，负责View的绘制以及与用户交互。
	- Model: 实体模型。
	- ViewModel: 负责完成View与Model间的交互，负责业务逻辑。
- 可复用
- 不用再操作DOM了，用数据该变视图
#### 带来的问题：
- 重复代码
- 混乱代码
- 更多精力用在UI的交换细节和渲染细节处理
- 不宜扩充
- 用户交互触发事件
- 数据处理不在框架之内
- 面向网页元素编程

### 浏览器地址栏里面的'＃' 如何清除？mode共有几个参数，参数有什么区别？
#### 浏览器地址栏里面的'＃' 如何清除
在URL地址后面加随机数或者时间戳
#### mode共有几个参数，参数有什么区别？
目前我用到的就是`mode:'history'`，实现路由的切换其他的暂时还没用到
### vue中父组件如何给子组件传递值
父组件通过属性将值传递给子组件，子组件通过props进行接收
```
  //子与父：父亲到儿子  默认组件是独立的，相互不能引用数据，可以通过属性的方式传递给儿子
    let vm=new Vue({
        el:'#app',
        data:{money:100},//根实例中的数据data都是对象，组件中的data都是函数
        template:'<child :m="money" o="美女"/>',//也可以这样写，把自己本身当做根元素
        components:{
            child:{
                //如果传递多个可以在数组中写多个 定义一个属性来接收父亲给的属性
                props:['m','o'],//这个m会挂载在儿子的实例上，相当于挂在了data上，父亲的数据儿子不能更改
                computed:{
                    b(){return '大'+this.o}//this是当前组件的实例
                },
                template:'<div>child {{m}} {{b}}</div>'
            }
        }
    })
```
### react的优缺点

### react组件中props和state的区别
- props是属性，属性是外界传入的(一般是父组件传递给子组件的)，内部不可改变的，属性是自定义的，可以随便写
- state是状态，状态是内部初始化的，可以修改，而且状态改变后会重新刷新视图

### 什么是JSX
是JavaScript+xml的一种混合写法
### 说一下angular、vue、react的相同点和不同点?各适用于什么样的项目场景?
#### 相同点
- vue与angular的相同点：
都支持指令：内置指令和自定义指令。
都支持过滤器：内置过滤器和自定义过滤器。
都支持双向数据绑定。
都不支持低端浏览器。
- vue与angular的不同点
1.AngularJS的学习成本高，比如增加了Dependency Injection特性，而Vue.js本身提供的API都比较简单、直观。
2.在性能上，AngularJS依赖对数据做脏检查，所以Watcher越多越慢。
Vue.js使用基于依赖追踪的观察并且使用异步队列更新。所有的数据都是独立触发的。
对于庞大的应用来说，这个优化差异还是比较明显的。
- vue与react的相同点：
React采用特殊的JSX语法，Vue.js在组件开发中也推崇编写.vue特殊文件格式，对文件内容都有一些约定，两者都需要编译后使用。
中心思想相同：一切都是组件，组件实例之间可以嵌套。
都提供合理的钩子函数，可以让开发者定制化地去处理需求。
都不内置列数AJAX，Route等功能到核心包，而是以插件的方式加载。
在组件开发中都支持mixins的特性
- vue与react的不同点：
React依赖Virtual DOM,而Vue.js使用的是DOM模板。React采用的Virtual DOM会对渲染出来的结果做脏检查。
Vue.js在模板中提供了指令，过滤器等，可以非常方便，快捷地操作DOM
### React中不同组件传递数据的方式有哪些？至少说出三种
- 父组件-->子组件 props
- 子组件-->父组件 callback
- 父组件-->非常深入的子组件 context
### 请描述React的组件加载生命周期函数以及shouldComponentUpdate方法的实际使用场景?
#### React的组件加载生命周期函数
```
import React,{Component} from 'react';
import ReactDOM from 'react-dom';

/**
 * 1、组件的完整的生命周期
 * 2、一些代码的编写顺序
 */
class Counter extends Component{
    constructor(){
        super();
        this.state={num:0}
    }
    //组件将要被挂载
    //可以在该函数里进行一些ajax的获取或者定时器的启动工作
    componentWillMount(){
        console.log('1.组件将要被挂载')
    }
    handleClick=()=>{
        this.setState({//setState()这个方法是异步的，所以不能在赋值之后立刻获取最新的state的值，可以在回调函数中获取最新的状态值
            num:this.state.num+1
        },()=>{
            console.log(this.state.num);
        });
        // console.log(this.state.num);
    };

    /**
     * @param newProps 新的属性对象
     * @param newState 新的状态对象
     * @returns {boolean}
     */
    shouldComponentUpdate(newProps,newState){//返回一个boolean值，为true，要更新
        console.log('4.组件是否进行更新');
        if(newState.num%5===0){
            return true;//如果返回true，组件会进行更新，且刷新页面，会再次执行render方法
        }else {
            return false
        }
    }
    componentWillUpdate(){
        console.log('5.组件将要更新');
    }
    componentDidUpdate(){
        console.log('6.组件更新完成');
    }
    render(){
        console.log('2.组件挂载');
        return (
            <div style={{border:'1px solid red',padding:'5px'}}>
                <p>{this.state.num}</p>
                <button onClick={this.handleClick}>+</button>
                <SubCounter num={this.state.num}/>
            </div>
        )
    }
    //组件挂载完成
    componentDidMount(){
        console.log('3.组件挂载完成')
    }
}

class SubCounter extends Component{
    //组件将要接收新的属性对象
    componentWillReceiveProps(newProps){
        console.log('7.组件将要收到新的属性对象');
    }
    shouldComponentUpdate(newProps,newState){
        if(newProps.num%3===0){
            return true
        }else {
            return false
        }
    }
    render(){
        return (
            <div style={{border:'1px solid black'}}>
                <p>{this.props.num}</p>
            </div>
        )
    }
}
ReactDOM.render(<Counter/>,document.querySelector('#root'));

```
#### shouldComponentUpdate方法的实际使用场景
判断组件是否要更新的时候使用

## HTTP
### HTTP报文的组成部分
请求头、请求行、请求体、
响应头、响应行、响应体
### GET和POST的区别
- get请求通常是用来查询和获取，post请求一般是发送或者更新
- get请求有缓存，一般都是在问号后面拼接随机小数或者时间戳，post没有缓存
- 安全问题，post比较安全，数据放在请求体中，传输过程中看不到，相对较安全，而get请求是将数据拼接到URL问号后面，容易被劫持
- 传输数据的大小限制：getURL长度限制（Chrome 8k  IE 2k），post没有大小限制
### HTTP常见状态码
|状态短语|状态码|解析|
|200|OK|请求成功|
|206|Partial content|部分内容，下载的时候会用到|
|301|Moved Permanently|永久重定向|
|302|Found|临时重定向|
|304|Not Modified|缓存发现|
|400|Bad Request|客户端请求错误|
|401|Unauthorized|未授权的（未登录）|
|403|Forbidden|无权访问（登录了，但权限不够）|
|404|Not Found|资源未找到|
|500|Internal Server Error|服务器内部错误|
|503|Service Unavailable|服务暂时不可用|
### 什么是Restful API?
每个资源都使用 URI (Universal Resource Identifier) 得到一个唯一的地址。所有资源都共享统一的接口，以便在客户端和服务器之间传输状态。
### HTTPS和HTTP的区别
- HTTP：超文本传输协议，除了传输文本外还可以传输其他东西，例如：XML等（日常生活中的小东西，快递员）**最常用**
- HTTPS：更加安全的http，经过加密的 比如SSL
### 从在浏览器中输入URL到页面渲染出来都经过了什么过程？
当在浏览器中输入URL时，会先通过DNS进行解析，把传递进来的域名解析为IP，然后再到远程的服务器上找到对应的IP，并将对应的文件返回给浏览器，浏览器接收数据并结合页面源码，从而渲染到页面上
### JSON和JSONP 区别是什么？JSONP的原理是？
- JSON:是一种基于文本的数据交换方式，或者叫做数据描述格式;
- JSONP:由于同源策略的限制，XmlHttpRequest只允许请求当前源（域名、协议、端口）的资源，为了实现跨域请求，可以通过script标签实现跨域请求，然后在服务端输出JSON数据并执行回调函数，从而解决了跨域的数据请求，这就是jsonp的核心
- JSONP的原理：利用script标签的src不受同源限制，只要给他一个能找到的脚本，就能引入进来，src会将引入进来的内容当做js脚本来运行
- jsonp原理：
动态创建一个script标签，script标签不存在同源跨域这一说
1）把需要请求的服务端跨域地址赋值给script标签的src属性
2）把当前页面某一个函数名当做参数值，传递给需要跨域请求的服务端(url问号传参?:callback=fn)
3）跨域请求的服务端，接收到请求后，需要做特殊处理（把你传进来的函数名和它要给你的数据拼接成json一个字符串）例如：我们传递进去函数名：fn 它准备的数据：fn([{"name:"xiaohong""}])  -->我们传进来的函数名(需要给我们的数据)
4）跨域请求的服务端，把准备好的数据通过http协议返回给我们客户端，其实就是让fn函数执行，还fn函数传递了一堆数据，就是我们想要用的数据
jsonp后端返回什么数据格式
按照理论来说返回什么格式的字符串都行(xml、字符串、json格式字符串)，只要是拼接过的，但是项目中一般返回的是json格式的字符串

优点：
兼容性好，简单易用，支持浏览器与服务器双向通信。
缺点：
1.需要后端配合提供jsonp接口（需要对方的服务器做支持）
2、只能用get请求，安全性大大低于post请求，不适合发送机密信息

### 用过那些跨域技术
CORS：
HTML5 里有个window.postMessage也支持跨域请求
JSONP 插件
### ajax的参数
- URL ：请求路径
- type：请求类型 ，默认是GET
- data：请求数据
- dataType：请求回来的数据类型 ,默认是text
- cache：是否缓存，默认是true
- async：是否异步，默认是true
- context：指定this
- success：成功的回调
- error：错误的回调
- ontimeout：响应超时时间

## 前后端通信
### 什么是同源策略及限制
- 同源策略：
	- 协议相同、
	- 端口号相同、
	- 域名相同
- 限制：
	- cookie localStorage 信息共享
	- ajax限制跨域
### 前后端如何通信
前端利用ajax向后台发送请求，当请求成功后，会将请求到的数据返回给前端
![Alt text](./前后端交互.png)
### 用原生JS模拟一下jquery的ajax方法
```
~function () {
    //处理合并，将默认参数与传递进来的参数进行合并
    Object.objAssign=function (tar,source) {//tar 是默认参数  source是传递进来的参数
        for(let k in source){
            if(source.hasOwnProperty(k)){
                tar[k]=source[k];
            }
        }

    };

    //创建一个ajax实例
    function createXhr() {
        let arrFn=[
            function () {
                return new XMLHttpRequest();
            },function () {
                return new ActiveXObject('Microsoft.XMLHTTP');
            },function () {
                return new ActiveXObject('Msxml2.XMLHTTP');
            },function () {
                return new ActiveXObject('Msxml3.XMLHTTP');
            }
        ];
        let curFn=null,xhr=null;
        for(let i=0;i<arrFn.length;i++){
            curFn=arrFn[i];
            try{
                xhr=curFn();
                createXhr=curFn;
                break;
            }catch (e){}
        }
        if(!xhr){
            throw new Error('请升级浏览器');
        }
        return xhr;
    }


    /**
     *
     * @param options
     */
    function ajax(options) {
        //默认参数对象
        let _options={
            url:null,
            type:'GET',
            cache:true,
            async:true,
            data:null,
            context:null,
            dataType:'text',
            success:null,
            error:null,
            timeout:null
        };

        //将传递进来options和_options进行合并
        Object.objAssign(_options,options);
        //处理URL
        if(!_options.url){
            console.warn('缺少URL');
            return;
        }
        //处理问号，看传递进来的URL是否有?，如果有问号就将其截取掉
        if(_options.url.indexOf('?')>-1){
            _options.url=_options.url.slice(0,-1);
        }
        //判断请求方式 get方式中需要处理的cache和data
        let flag=false;//如果当前URL没有问号就是false
        if(_options.type.toUpperCase()==='GET'){
            if(!_options.cache){//如果是false需要拼接随机数或者时间戳
                _options.url+='?_='+Date.now();
                flag=true;
            }

        //    处理data 拼接到URL问号后面
            for(let k in _options.data){
                if(_options.data.hasOwnProperty(k)){
                    if(flag){//有问号就直接开头拼接&
                        _options.url+='&'+k+'='+_options.data[k];
                    }else {
                        _options.url+='?'+k+'='+_options.data[k];
                        flag=true;
                    }
                }
            }

        }


    //    创建ajax实例
        let xhr=createXhr();
        let {url,type,async,data,dataType,timeout,error,success,context}=_options;
        context||(context=_options);//处理ajax的this问题
        xhr.open(type,url,async);
        xhr.responseType=dataType;
        xhr.timeout=timeout;
        xhr.onerror=xhr.ontimeout=error;
        xhr.onreadystatechange=function () {
            if(this.readyState===4&&/^2\d{2}$/.test(this.status)){
                typeof success==='function'?success.call(context,this.response):null;
            }

        };
        xhr.send(JSON.stringify(data));

    }

    window.$$={ajax};
}();

```
### 跨域通信的几种方式?
- jsonp
- cors：XMLHttpRequest无法跨源通信，但在 XMLHttpRequest level2中 只要服务器端许可就可以实现
- postMessage()：只有目标窗口的协议、主机地址或端口这三者全部匹配才会发送消息。
- beacon(ping)：ping协议其实是一种很古老的跨域方法。它的本质是利用了浏览器可以获取不同域名下图片的原理，把请求参数放在了图片地址的URL参数中发给后端。
- submit协议：适合大数据量或者文件的请求发送，需要浏览器支持FormData

## 安全
### CSRF的原理以及如何防御
#### 原理：图示
![Alt text](./CSRF原理.jpg)
#### 防御：
- 加token验证（发一个验证码）
- 加验证码
- 绑定IP
- refer来源

### XSS的原理及防御
#### 原理
 XSS 属于被动式的攻击。攻击者先构造一个跨站页面，利用`script、<img>`、等
 #### 防御
 - 在HttpOnly防止截取Cookie
 - 输入检查
 - 输出检查
	 - 在HTML标签中输出
	 - 在HTML属性中输出
	 - 在`<script>`标签中输出
	 - 在事件中输出
	 - 在css中输出
	 - 在地址中输出
 - 处理富文本
 - 防御DOM Based XSS
## 渲染机制
### 什么是DOCTYPE及作用?标准模式和兼容模式有什么区别?
DOCTYPE是文档声明，如果HTML文档中有这个文档声明，代表了是标准盒子模型
标准模式与兼容模式的区别在于标准模式中盒子的宽高不包含padding和border，而兼容模式中盒子的宽高包含padding和border
### 浏览器是如何渲染页面的
域名解析 -> 建立连接 -> 发起请求 -> 等待响应 -> 接收数据
### 什么是重排？什么时候会触发重排?
重排是更明显的一种改变，可以理解为渲染树需要重新计算
触发重排的情况
- DOM元素的几何属性发生变化
- DOM树的结构发生变化
- 获取某些属性
### 什么是重绘？什么时候会触发重绘?
重绘：当页面中元素的部分样式（背景颜色、字体颜色）改变时，只会重新渲染这个元素的部分样式，针对于元素的外观（针对于视图）
#### 什么时候会触发重绘
- 被动方式有很多，比如当你的鼠标盖住窗口上的一块，其实这块区域就是重绘了。
- 主动的话就是调用诸如Refresh（）、Invalidate（）等函数强制使得一块区域无效，这样程序就会触发重绘函数重新绘制指定区域。

## js运行机制
### 如何理解js的单线程
就是代码从上到下执行，既然是单线程的，在某个特定的时刻只有特定的代码能够被执行，并阻塞其它的代码。而浏览器是事件驱动的（Event driven），浏览器中很多行为是异步（Asynchronized）的，会创建事件并放入执行队列中。javascript引擎是单线程处理它的任务队列，你可以理解成就是普通函数和回调函数构成的队列。当异步事件发生时，如mouse click, a timer firing, or an XMLHttpRequest completing（鼠标点击事件发生、定时器触发事件发生、XMLHttpRequest完成回调触发等），将他们放入执行队列，等待当前代码执行完成。
### 什么是Event Loop,请简述其过程
Event Loop（事件循环），会轮询大的事件队列并处理事件。例如，浏览器当前正在忙于处理onclick事件，这时另外一个事件发生了（如：window onSize），这个异步事件就被放入事件队列等待处理，只有前面的处理完毕了，空闲了才会执行这个事件。setTimeout也是一样，当调用的时候，js引擎会启动定时器timer，大约xxms以后执行xxx，当定时器时间到，就把该事件放到主事件队列等待处理（浏览器不忙的时候才会真正执行）。
## 错误处理
### 前端错误的分类?
- SyntaxError解析错误：解析代码时发生的语法错误
- ReferenceError引用错误：引入一个不存在的变量时发生的错误，将一个值分配给无法分配的对象，比如对函数的运行结果或者this赋值
- RangeError范围错误：当一个值超出有效范围时发生的错误，主要有几中情况：1、数组长度为负数，2、是Number对象的方法参数超出范围，3、函数堆栈超出最大值
- TypeError类型错误：变量或参数不是预期类型时发生的错误，比如：使用new字符串、布尔值、数值等原始类型和调用对象不存在的方法就会抛出这种错误，因为new命令的参数应该是一个构造函数
- URLError同一资源标识符函数错误：是URL相关函数的参数不正确时抛出的错误。主要涉及encodeURI()、decodeURI()、encodeURIComponent()、decodeURIComponent()、escape()和unescape()这六个函数
- EvalError eval()函数执行错误：当eval函数没有被正确执行时，会抛出这个错误，该类型错误在es5中已经不适应了

### 程序出现bug了，你是如何调试的
### 如何分类捕获不同的错误?
try catch   onerror  reject  catch

## 页面性能
### 前端性能优化的方法有哪些？至少说出10种以上
- 面向内容的优化
	- 减少HTTP请求
	- 减少DNS查找
	- 避免重定向
	- 使用ajax缓存
	- 延迟载入组件
	- 预先载入组件
	- 减少DOM元素数量
	- 切分组件到多个域
	- 最小化iframe的数量
	- 不要出现http 404错误
- 面向server
	- 缩小Cookie
	- 针对web组件使用域名无关性的
### 如何实现js的异步加载
> 使用回调函数或者promise

### async和defer的区别
```
<script src="script.js"></script>
没有 defer 或 async，浏览器会立即加载并执行指定的脚本，“立即”指的是在渲染该 script 标签之下的文档元素之前，也就是说不等待后续载入的文档元素，读到就加载并执行。
<script async src="script.js"></script>
有 async，加载和渲染后续文档元素的过程将和 script.js 的加载与执行并行进行（异步）。
<script defer src="myscript.js"></script>
有 defer，加载后续文档元素的过程将和 script.js 的加载并行进行（异步），但是 script.js 的执行要在所有元素解析完成之后，DOMContentLoaded 事件触发之前完成
```


## 缓存
### 请描述cookie、sessionStorage和localStorage的区别
- cookie：数据保存在客户端，是一种会话方式，只要不关闭浏览器，cookie中存储的值一直有效，但是cookie被攻击的可能性很大
- sessionStorage：数据存储在窗口对象中，会话存储，只要不关闭窗口，数据一直存在
- localStorage：本地存储永不失效，除非手动删除，浏览器关闭了数据仍然可以保存下来，并可用与所有同源窗口，永久保存，
> Cookie的作用是与服务器进行交互，作为HTTP规范的一部分而存在 ，而Web Storage仅仅是为了在本地“存储”数据而生。
- 使用 local storage和session storage主要通过在js中操作这两个对象来实现，分别为window.localStorage和window.sessionStorage. 这两个对象均是Storage类的两个实例，自然也具有Storage类的属性和方法。
- 但是cookie需要前端开发者自己封装setCookie，getCookie

/共同点：都是保存在浏览器端，且同源的。
    //区别:
    //1.cookie数据始终在同源的http请求中携带（即使不需要），即cookie在浏览器和服务器间来回传递。而sessionStorage和localStorage不会自动把数据发给服务器，仅在本地保存。cookie数据还有路径（path）的概念，可以限制cookie只属于某个路径下。
    //2.存储大小限制也不同，cookie数据不能超过4k，同时因为每次http请求都会携带cookie，所以cookie只适合保存很小的数据，如会话标识。sessionStorage和localStorage 虽然也有存储大小的限制，但比cookie大得多，可以达到5M或更大。
    //3.数据有效期不同，sessionStorage：仅在当前浏览器窗口关闭前有效，自然也就不可能持久保持；localStorage：始终有效，窗口或浏览器关闭也一直保存，因此用作持久数据；cookie只在设置的cookie过期时间之前一直有效，即使窗口或浏览器关闭。
    //4.作用域不同，sessionStorage不在不同的浏览器窗口中共享，即使是同一个页面；localStorage 在所有同源窗口中都是共享的；cookie也是在所有同源窗口中都是共享的。
    //5.Web Storage 支持事件通知机制，可以将数据更新的通知发送给监听者。
    //6.Web Storage 的 api 接口使用更方便。

### Accept-Encoding，HTTP Header中Accept-Encoding 是浏览器发给服务器,声明浏览器支持的编码类型
常见的有
Accept-Encoding: compress, gzip //支持compress 和gzip类型
Accept-Encoding:　//默认是identity
Accept-Encoding: *　//支持所有类型 Accept-Encoding: compress;q=0.5, gzip;q=1.0//按顺序支持 gzip , compress
Accept-Encoding: gzip;q=1.0, identity; q=0.5, *;q=0 // 按顺序支持 gzip , identity
