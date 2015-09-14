# 编写高性能js阅读笔记
#第一章,加载和运行
管理浏览器中的js代码是个棘手问题，因为代码执行阻塞其他浏览器处理过程，例如用户界面绘制。每次遇到\<script\>标签的时候
，页面必须停下来等待代码下载并且执行，然后再去执行页面其他部分，但是有一下几种方法优化:
###一.
把\<script\>尽量放到body的关闭标签\</body\>上方，这样就能保证页面不会因为下载和执行大量的js而造成的假死现象，不过，也可以把一些特效、执行执行和用户交互的代码放到head里面
###二.
js代码打包,因为每下载一个js文件都是一个独立的Http请求,而http请求除了文件以外，还有http本身的一些信息。所以如果文件多了的话，反而会影响性能.比如说另可一个100k的文件也不要4个25k的文件
###三.
非阻塞下载:
####1.用在\<script\>标签里面添加defer属性，但是只能用于ie和fx3.5以上版本
####2.动态创建\<script\>标签.即是,documenet.createElement('script'),这种方法的的有点就是无论在什么地方，都不会阻塞页面处理其他程序。如果js代码是“自运行”类型的话，代码会自动运行，如果是供其他页面调用的api类型的话，那么就必须得确认js代码创建完成、执行完毕以后才能用。
####3.用XMLHTTPRequest,即ajax，这主方法最大的好处就是，代码不会立即执行，而且所有浏览器都兼容

#第二章,数据存储
###一.
在没有优化的浏览器中,最好尽可能的使用局部变量.因为变量的查找是一层层的网上找的，知道顶级作用域即全局作用域，而这一次次的查找是需要消耗资源的.所以一个好的经验则是:用局部变量存储本地范围以外的值，特别是要多次使用的情况可以考虑使用以下几种写法:
```
function initUI(){
  var db=document.body,
  links=document.getElementByTagName('a'),
  i=0,
  len=lenks.length,
  while(i<len){
    update(links[i++])
  },
  documentgetElementById("aaa").onclick(){
    start();
  };
  bd.className="active";
}
```
以上代码3次用到了document对象，所以可以优化下:
```
function initUI(){
  var doc=document;//把document对象 放到局部变量doc中
  var db=doc.body;
  links=doc.getElementByTagName('a'),
  i=0,
  len=lenks.length,
  while(i<len){
    update(links[i++])
  },
  doc.getElementById("aaa").onclick(){
    start();
  };
  bd.className="active";
}
```
##二.
一般来说运行时的上下文作用域不会被改变，但是有两个表达式例外。
####with语句
with语句可以用来避免重复的书写,
```
function initUI(){
  with(document){
  var links=getElementByTagName("a")//这里直接用方法了
  getElemetnById("aaa").onclick=function(){
    ...
  }
  }
}
```
这样写貌似更简洁了，但是实际上代价更大，因为执行到with语句的时候，会创建一个新新的临时新的，这个对象包含了当前当前对像的所有属性，并且把当前对象推向到作用域的前端，也就是说，他把当前对象的所有属性推入到了第二个作用域中。所以这样做的代价更大了.
###try-catch
当程序发生错误后，会跳转到catch语句块中,并且将异常对象推入到作用域前端的一个可变对象中，在catch中函数所有变量被放到第二个作用域中，例如:
```
try{
  //dosomething
}
catch(ex){
  //在这里函数作用域发生而来改变
}
```

当catch语句执行完毕以后作用域才会回到原来的状态。由此可见try-cathc是一把双刃剑，得得当的使用，如果程序中的错误是可预见的，那么就在代码中去修复，而不要依赖catch去捕获。或者把错误的处理交给一个函数，这样可以尽可能的减少catch带来的影响.
```
try{
}
catch(ex){
  handError(ex)//自定义方法
}
function handError(obj){
  //dosomething
}
```
#第三章,DOM编程
##此章是谈dom的操作,没什么兴趣。所以随便看了下，就懒得写笔记了



#第四章,算法和流程控制
##一.if-else和swith
大多数情况下swith要比if-else要快，但是再条件数量很大的情况下才明显.二者性能的主要区别在于，当条件数量增加以后if-else性能负担增加的程度要比wsith多。所以条件少的时候用if-else,反之则用swith.
if-else最简单的优化方法就是，把概率最大的条件放到最前面，然后依次类推。这样就可以保证理论上计算速度最快.
##二.查表法
如果条件分支多，而且零散的情况下 用查表法速度更快.而这个方法我自己没用过，按照我的理解就是把计算结果事先算出来，然后放到一个数组或者对象里面去，需要的时候直接调用(感觉非常的鸡肋).此处略过...
##三.循环
一般来说for,while,do-while这三种循环要比for-in要快(其实，我基本只用for和for-in着两种).因为for-in 每次迭代都要去搜索实例和原型的属性，这样就要付出更多的开销。但是也因为如此for-in可以去操作数目不详的对象属性。
