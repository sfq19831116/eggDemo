# 前端
    
* 什么是前端

    前端：针对浏览器的开发，代码在浏览器运行
    
    后端：针对服务器的开发，代码在服务器运行
    
* 前端的前世
    
    1. 后端收到浏览器请求
    
    2. 生成静态页面
    
    3. 发送到浏览器当时网站开发普遍采用MVC模式：
    
    Model(模型层)：提供数据
    
    Controller(控制层)：数据处理，实现业务逻辑
    
    View(视图层)：用户页面，展示数据前端属于视图层，前端工程师只是负责编写页面模板

    WWW（World Wide web，万维网）：Tim Berners-Lee博士开发出了第一台web服务器和web客户端，
    
    W3C（World Wide web Consortinm，万维网联盟）成为了 HTML 后续标准的制定者
    
    前端三剑客HTML，CSS，JavaScript

* HTML
    HTML——Hyper Text Markup Language，超文本标记语言
    
* CSS
    CSS（Cascading Style Sheets，层叠样式表）主要作用就是为文档中的标签添加大小、颜色、定位等特性。
   
* JavaScript
    这是前端技术核心的核心，这个由Brendan Eich经过10天开发出来的语言，如今基于它，催生出无数的框架和工具.
    
* 浏览器
    浏览器作为 web 的唯一入口，竞争是难以避免的。

    早期网景和微软的浏览器战争，以微软用 windows 操作系统捆绑 IE 的形式取得胜利而告终。
    
    而两家公司置 W3C 文档标准于不顾，互相不兼容对方浏览器，造成了如今网页开发需要处理棘手的兼容性问题，甚至现代浏览器也存在不同的差异性
    
    2008年Google Chrome的推出，第二次浏览器大战开始，紧接着Firefox推出，IE被围杀，又加上高速增长的移动互联网，IE 正在被彻底边缘化
    
    ​浏览器作为呈现web文档的唯一工具，内部有许多组件，浏览器暴露了自己的一些组件给JS语言，比如BOM，而其它比较重要的几个组件如下：
    
    网络：使用HTTP（Hypertext Transfer Protocol，超文本传输协议）请求web文档涉及到的所有资源文件
    
    渲染引擎：用以解析HTML和CSS并呈现
    
    JS解释器：解释执行JS代码
    
* 运行原理

​   一个网页涉及的所有内容全部是从服务器端请求而来，请求方式采用HTTP，还有相对于HTTP比较安全的协议：HTTPS。

​   而将网页所需的资源（html、css、js等）文件获取到之后，将会调用渲染引擎和JS引擎等来解释这些文件以完整的呈现web文档。

* 渲染流程

![Image text](https://upload-images.jianshu.io/upload_images/6429336-965adf0b51cddc72.png)


* 渲染大概流程

    将文档中所有标签写法解析成 DOM Tree（Document Object Model，文档对象模型），标签此时成为node（节点），并提供接口让我们可以在JS代码中读写这些标签
    
    读取CSS解析成 CSSOM
    
    DOM Tree和CSSOM合并成Render Tree，此时每个node的位置、大小、颜色等属性全部确定
    
    开始绘制Render Tree
    
​    除了网络请求之外，浏览器的工作是单线程的，如果在解析过程中遇到JS代码，则需要等JS引擎完全读取并运行代码之后才会继续进行渲染流程。

* 库和框架

    UI框架有Bootstrap、Amaze UI等，
    
    视图框架有React、Vue等，
    
    基于Node.js的后台服务器应用框架 egg 等。
    
    而库嘛，举个都知道的吧：jQuery。
    
    库和框架需要你按照它的既定规则编写代码，但是这些代码浏览器的JS引擎是无法理解的，所以必须通过框架的解析才能用于生产环境中。
