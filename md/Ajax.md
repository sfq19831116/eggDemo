# 什么是Ajax

* Ajax的来历
    Asynchronous JavaScript and XML
    
    套用一句不恰当的话，Ajax就是中间商（并不赚取差价），换一个专业点的说话，现在它就是脚本发起HTTP通信的代名词
    
    当我们在浏览器地址栏键入一个网址，或者通过网页表单向服务器提交内容的时候,我们就开始与浏览器进行交互。
    
    而浏览器在这个时候，开始与服务器进行交互。传统的Web应用交互由用户触发一个HTTP请求到服务器,服务器对其进行处理后再返回一个新的HTML页到客户端。
    
    每当服务器处理客户端提交的请求时,客户都只能空闲等待,并且哪怕只是一次很小的交互、只需从服务器端得到很简单的一个数据都要返回一个完整的HTML页,而客户端每次都要浪费时间和带宽去重新读取整个页面。
    
    1999年，微软公司发布IE浏览器5.0版，第一次引入新功能：允许JavaScript脚本向服务器发起HTTP请求。
    
    这个功能当时并没有引起注意,为什么？实际上，人们一直在寻求改善传统的web应用交互方式。
    
    1999年以前，JS可以通过Java applet或Flash电影等中间层向服务器发送请求。
    
    在Ajax被提出之前，这种技术就已经存在很长时间了，那时候人们通常将这种技术叫做远程脚本(remote scripting)
    
    Ajax技术的核心是XMLHttpRequest（简称XHR），这是由微软首先引入的一个特性，最早应用在它的IE5浏览器上。
    
    XHR为向浏览器发送请求和解析服务器响应提供了流畅的接口。
    
    随着XHR、JS等各项技术应用得越来越广泛，2005年，依赖XHR的Ajax技术被提出
    
![Image text](https://upload-images.jianshu.io/upload_images/7995515-e0b1d1eba72c93e7.png)
![Image text](https://upload-images.jianshu.io/upload_images/7995515-bace42f0161c959d.png)

* 运行机制
    
    Ajax依赖CSS/HTML/Javascript，而其中最核心的依赖是浏览器提供的XMLHttpRequest简称（XHR）对象，是这个对象使得浏览器可以发出HTTP请求与接收HTTP响应。
    
    Ajax的工作原理相当于在用户和服务器之间加了—个中间层(AJAX引擎),使用户操作与服务器响应异步化。
    
    Ajax就这样集齐CSS、JavaScript、XMLHTTPRequest、DOM，通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用JavaScript来操作DOM、改变CSS，更新页面。
    
    Ajax重要影响是使WEB中的界面与应用分离,数据与呈现分离的分离.
    
* 代码示例
    
    一段使用XMLHttpRequest发送Ajax请求的简单示例代码。
``` 
    function sendAjax() {
        //构造表单数据
        var formData = new FormData();
        formData.append('username', 'johndoe');
        formData.append('id', 123456);
        //创建xhr对象
        var xhr = new XMLHttpRequest();
        //设置xhr请求的超时时间
        xhr.timeout = 3000;
        //设置响应返回的数据格式
        xhr.responseType = "text";
        //创建一个 post 请求，采用异步
        xhr.open('POST', '/server', true);
        //注册相关事件回调处理函数
        xhr.onload = function(e) {
            if(this.status == 200||this.status == 304){
                alert(this.responseText);
            }
        };
        xhr.ontimeout = function(e) { ... };
        xhr.onerror = function(e) { ... };
        xhr.upload.onprogress = function(e) { ... };
        //发送数据
        xhr.send(formData);
    }
```

  
