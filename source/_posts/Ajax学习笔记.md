title: Ajax学习笔记
comments: true
date: 2015-10-22 22:20:00
updated:
categories: 前端
tags: Ajax
---

# 什么是Ajax

从字面意义来说，Ajax（Asynchronous JavaScript and XML）为异步的 JavaScript 和 XML利用。它并不是一种新的编程语言，而是*利用现有标准，无阻塞地与服务器进行数据交换*的方法。

# 不同浏览器的实现

## 创建XMLHttpRequset对象

```javascript
var xhr = createXHR();

function createXHR(){
	var xhr;

	if(window.XMLHttpRequset){
		xhr = new XMLHttpRequset();
	}else {
		try{
			//IE6
			xhr = new ActiveXObject('Microsoft.XMLHTTP');
		}catch(e){
			xhr = false;
		}
	}

	return xhr;
}
```

## 请求的基本流程

1. 建立连接
2. 设置回调
3. 发送数据

```javascript
//建立连接
xhr.open(method,url,async);
//设置回调
xhr.onreadystatechange = function(e){

};
//发送数据
xhr.send(string);
```

建立连接的method常用为`GET`，`POST`。

当使用`POST`方法时，需要在`send()`前添加HTTP请求头。例如，若想像HTML表单那样POST数据，需要：

```javascript
xhr.setRequsetHeader('Content-Type','application/x-www-form-urlencoded');
xhr.send('name=1&age=18');
```

## 监听当前状态

通过`xhr.readyState`可当前状态，有以下几种：

值 | 状态 | 描述
--- | --- | ---
0 | UNSENT (未打开) | open()方法还未被调用.
1 | OPENED  (未发送) | send()方法还未被调用.
2 | HEADERS_RECEIVED (已获取响应头) | send()方法已经被调用, 响应头和响应状态已经返回.
3 | LOADING (正在下载响应体) | 响应体下载中; responseText中已经获取了部分数据.
4 | DONE (请求完成) | 整个请求过程已经完毕.

在此，我们主要关注4。

通过`xhr.status`可获得响应的状态码，例如	200 表示成功的请求。

每次请求状态发生改变，均会触发`onreadystatechange`，因此，在该回调中，通过判断`xhr.readyState`和`xhr.status`来执行不同的业务。

```javascript
//请求成功
xhr.onreadystatechange = function(e){
	if(xhr.readyState == 4 && xhr.status == 200){
		console.log('success!');
	}
};
```

请求成功后，若返回的数据格式为字符串，可通过`xhr.responseText`获得；若返回的数据格式为xml，可通过`xhr.responseXML`获得。

# XMLHttpRequset 2.0

老版本缺点：
* 只支持文本数据的传送，无法用来读取和上传二进制文件。
* 传送和接收数据时，没有进度信息，只能提示有没有完成。
* 受到"同域限制"（Same Origin Policy），只能向同一域名的服务器请求数据。

新版本功能：
* 可以设置HTTP请求的时限。
* 可以使用FormData对象管理表单数据。
* 可以上传文件。
* 可以请求不同域名下的数据（跨域请求）。
* 可以获取服务器端的二进制数据。
* 可以获得数据传输的进度信息。

对于文件传输，我暂时还不太了解。目前我比较关注*进度信息*和*跨域请求*。

## 进度信息

新版本的XMLHttpRequest对象，传送数据的时候，有一个progress事件，用来返回进度信息。它分成上传和下载两种情况。下载的progress事件属于XMLHttpRequest对象，上传的progress事件属于XMLHttpRequest.upload对象。
我们先定义progress事件的回调函数。

```javascript
　　xhr.onprogress = updateProgress;
　　xhr.upload.onprogress = updateProgress;
```

然后，在回调函数里面，使用这个事件的一些属性。

```javascript
function updateProgress(event) {
	if (event.lengthComputable) {
		var percentComplete = event.loaded / event.total;
	}
}
```
上面的代码中，`event.total`是需要传输的总字节，`event.loaded`是已经传输的字节。如果`event.lengthComputable`不为真，则`event.total`等于0。

与progress事件相关的，还有其他五个事件，可以分别指定回调函数：

* load事件：传输成功完成。
* abort事件：传输被用户取消。
* error事件：传输中出现错误。
* loadstart事件：传输开始。
* loadEnd事件：传输结束，但是不知道成功还是失败。

## 跨域请求——跨域资源共享（CORS）

Ajax请求数据的时候，遵循同源策略。

使用自定义的HTTP头部让浏览器与服务器进行沟通，从而决定请求或响应是应该成功还是失败。

服务器端对于CORS的支持，主要就是通过设置`Access-Control-Allow-Origin`来进行的。如果浏览器检测到相应的设置，就可以允许Ajax进行跨域的访问。

服务器端响应头需要设置的参数：

```
Access-Control-Allow-Origin:http://a.com;
Access-Control-Allow-Methods:POST,GET;
```

# 参考

[MDN XMLHttpRequest](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)

[【初窥javascript奥秘之Ajax】简述下你所知道的Ajax？](http://www.cnblogs.com/yexiaochai/archive/2013/04/24/3037069.html)

[XMLHttpRequest Level 2 使用指南](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)