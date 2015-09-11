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
  *var doc=document;*//把document对象 放到局部变量doc中
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
