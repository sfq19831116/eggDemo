# 页面渲染

* 浏览器

    浏览器的主要功能是将用户选择的web资源呈现出来，它需要从服务器请求资源，并将其显示在浏览器窗口中，资源的格式通常是HTML，也包括PDF、image及其他格式。用户用URI（Uniform Resource Identifier统一资源标识符）来指定所请求资源的位置，通过DNS查询，将网址转换为IP地址。
 
　　1、输入网址。 

　　2、浏览器查找域名的IP地址。
 
　　3. 浏览器给web服务器发送一个HTTP请求 

　　4. 网站服务的永久重定向响应 

　　5. 浏览器跟踪重定向地址 现在，浏览器知道了要访问的正确地址，所以它会发送另一个获取请求。 

　　6. 服务器“处理”请求，服务器接收到获取请求，然后处理并返回一个响应。 

　　7. 服务器发回一个HTML响应 

　　8. 浏览器开始显示HTML 

　　9. 浏览器发送请求，以获取嵌入在HTML中的对象。在浏览器显示HTML时，它会注意到需要获取其他地址内容的标签。这时，浏览器会发送一个获取请求来重新获得这些文件。这些文件就包括CSS/JS/图片等资源，这些资源的地址都要经历一个和HTML读取类似的过程。所以浏览器会在DNS中查找这些域名，发送请求，重定向等；

* 浏览器的主要组件包括

![Image text](https://img-blog.csdn.net/20171127183436695?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2xhcmU1MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

    1. 用户界面： 包括地址栏、后退/前进按钮、书签目录等，也就是你所看到的除了用来显示你所请求页面的主窗口之外的其他部分；

    2. 浏览器引擎：用来查询及操作渲染引擎的接口；

    3. 渲染引擎： 用来显示请求的内容，例如，如果请求内容为html，它负责解析html及css，并将解析后的结果显示出来；

    4. 网络：用来完成网络调用，例如http请求，它具有平台无关的接口，可以在不同平台上工作；

    5. UI 后端：用来绘制类似组合选择框及对话框等基本组件，具有不特定于某个平台的通用接口，底层使用操作系统的用户接口；

    6. JS解释器：用来解释执行JS代码；

    7. 数据存储：H5定义了web database技术，这是一种轻量级完整的客户端存储技术；

![Image text](https://img-blog.csdn.net/20171127183451459?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2xhcmU1MDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

* 页面生成过程

    1、DNS服务器通过域名查找对应的web 服务器ip地址；

    2、浏览器访问web服务器；这里涉及到客户端与服务器的tcp 三次握手与四次挥手；

    3、服务器处理完成返回html;

    4、浏览器解析、加载页面

　　  解析html 构建dom树 -> 构建render树 -> 布局render树 -> 绘制render树 ：

![Image text](https://images2015.cnblogs.com/blog/825196/201703/825196-20170328150055436-1910402537.png)

    浏览器为了体验友好，并不是文档全部都解析才绘制到屏幕上，而是从上至下开始解析html，遇到css 会开启线程下载css；

* 解析：

　　1、将HTML构建成一个DOM树（DOM = Document Object Model 文档对象模型），DOM 树的构建过程是一个深度遍历过程：当前节点的所有子节点都构建好后才会去构建当前节点的下一个兄弟节点。
 
　　2、将CSS解析成CSS去构造CSSOM树( CSSOM = CSS Object Model CSS对象模型)

　　3、根据DOM树和CSSOM来构造 Rendering Tree（渲染树）。注意：Rendering Tree 渲染树并不等同于 DOM 树，因为一些像 Header 或 display:none 的东西就没必要放在渲染树中了。

　　4.有了Render Tree，浏览器已经能知道网页中有哪些节点、各个节点的CSS定义以及他们的从属关系。

　　5.下一步操作称之为Layout，顾名思义就是计算出每个节点在屏幕中的位置 layout render tree。 

　　6.再下一步就是绘制，即遍历render树，并使用浏览器UI后端层绘制每个节点。

![Image text](https://images0.cnblogs.com/blog/412020/201409/201506295501803.jpg)

* 重绘、重排： 

>> Reflow（回流/重排）：当它发现了某个部分发生了变化影响了布局，渲染树需要重新计算。
 
>> Repaint（重绘）：改变了某个元素的背景颜色，文字颜色等，不影响元素周围或内部布局的属性，将只会引起浏览器的repaint，根据元素的新属性重新绘制，使元素呈现新的外观。重绘不会带来重新布局，并不一定伴随重排；

* reflow的原因：

（1）页面初始化的时候； 

（2）操作DOM时； 

（3）某些元素的尺寸变了； 

（4）如果 CSS 的属性发生变化了。

  
