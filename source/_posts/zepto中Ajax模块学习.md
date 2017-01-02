title: zepto中Ajax模块学习
comments: true
date: 2015-10-23 13:47:40
updated:
categories: 前端
tags: Ajax
---

在[Ajax学习笔记](http://lizijie1993.github.io/2015/10/22/Ajax%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/)中，已经了解了Ajax跨浏览器的解决方案，以及其基本流程。在此，对[zepto.js](http://zeptojs.com/)库中的Ajax模块进行初步了解，源码可以到官网去下载。

# Ajax核心部分

该模块中，我认为最关键的为两个部分`$.ajaxSettings`和`$.ajax`。

## $.ajaxSettings

`$.ajaxSettings`定义了Ajax的默认设置，包括请求方法、各阶段回调函数、允许的MIME类型等。

```javascript
$.ajaxSettings = {
    // Default type of request
    type: 'GET',
    // Callback that is executed before request
    beforeSend: empty,
    // Callback that is executed if the request succeeds
    success: empty,
    // Callback that is executed the the server drops error
    error: empty,
    // Callback that is executed on request complete (both: error and success)
    complete: empty,
    // The context for the callbacks
    context: null,
    // Whether to trigger "global" Ajax events
    global: true,
    // Transport
    xhr: function() {
        return new window.XMLHttpRequest()
    },
    // MIME types mapping
    accepts: {
        script: 'text/javascript, application/javascript',
        json: jsonType,
        xml: 'application/xml, text/xml',
        html: htmlType,
        text: 'text/plain'
    },
    // Whether the request is to another domain
    crossDomain: false,
    // Default timeout
    timeout: 0
}
```

## $.ajax

`$.ajax`方法，它是所有其他Ajax方法的底层实现，比如之后的`$.get`、`$.post`等。在此，我将源码拆分，一步一步进行分析：

首先，是Ajax参数的设置。用户的设置覆盖默认设置，作为最终的设置。

```javascript
var settings = $.extend({}, options || {})
for (key in $.ajaxSettings)
    if (settings[key] === undefined) settings[key] = $.ajaxSettings[key]
```

对比设置中的url与当前页面url主机名，来判断是否跨域。（个人认为还需要检测协议与端口）

如果设置的返回值类型`dataType`为`jsonp`，或者请求路径中含有`callback=?`，那么就通过jsonp获取数据。（注意，jsonp跟Ajax并没有关系）

```javascript
if (!settings.crossDomain) settings.crossDomain = /^([\w-]+:)?\/\/([^\/]+)/.test(settings.url) &&
    RegExp.$2 != window.location.host

var dataType = settings.dataType,
    hasPlaceholder = /=\?/.test(settings.url)
if (dataType == 'jsonp' || hasPlaceholder) {
    if (!hasPlaceholder) settings.url = appendQuery(settings.url, 'callback=?')
    return $.ajaxJSONP(settings)
}
```

`serializeData`方法，会对`settings.data`（用户传递的数据）进行处理。若为`GET`方法，将会将其转换为正常的请求字符串，附在url后。

```javascript
if (!settings.url) settings.url = window.location.toString()
serializeData(settings)
```

之后，根据各参数，创建一个请求头的对象。

其中的`xhr.overrideMimeType`是XMLHttpRequest2的方法，用于在发送端指定接受内容的mime-type。可以避免客户端与服务器端编码不同，产生乱码的问题。

```javascript
var mime = settings.accepts[dataType],
    baseHeaders = {},
    protocol = /^([\w-]+:)\/\//.test(settings.url) ? RegExp.$1 : window.location.protocol,
    xhr = $.ajaxSettings.xhr(),
    abortTimeout

if (!settings.crossDomain) baseHeaders['X-Requested-With'] = 'XMLHttpRequest'
if (mime) {
    baseHeaders['Accept'] = mime
    if (mime.indexOf(',') > -1) mime = mime.split(',', 2)[0]
    xhr.overrideMimeType && xhr.overrideMimeType(mime)
}
if (settings.contentType || (settings.data && settings.type.toUpperCase() != 'GET'))
    baseHeaders['Content-Type'] = (settings.contentType || 'application/x-www-form-urlencoded')
settings.headers = $.extend(baseHeaders, settings.headers || {})
```

监听状态改变，设置回调函数。

```javascript
xhr.onreadystatechange = function() {
    if (xhr.readyState == 4) {
        clearTimeout(abortTimeout)
        var result, error = false
        if ((xhr.status >= 200 && xhr.status < 300) || xhr.status == 304 || (xhr.status == 0 && protocol == 'file:')) {
            dataType = dataType || mimeToDataType(xhr.getResponseHeader('content-type'))
            result = xhr.responseText

            try {
                if (dataType == 'script')(1, eval)(result)
                else if (dataType == 'xml') result = xhr.responseXML
                else if (dataType == 'json') result = blankRE.test(result) ? null : JSON.parse(result)
            } catch (e) {
                error = e
            }

            if (error) ajaxError(error, 'parsererror', xhr, settings)
            else ajaxSuccess(result, xhr, settings)
        } else {
            ajaxError(null, 'error', xhr, settings)
        }
    }
}
```

建立连接，并设置请求头。

```javascript
var async = 'async' in settings ? settings.async : true
xhr.open(settings.type, settings.url, async)

for (name in settings.headers) xhr.setRequestHeader(name, settings.headers[name])
```

触发`beforeSend`事件与检测是否超时。

```javascript
if (ajaxBeforeSend(xhr, settings) === false) {
    xhr.abort()
    return false
}

if (settings.timeout > 0) abortTimeout = setTimeout(function() {
    xhr.onreadystatechange = empty
    xhr.abort()
    ajaxError(null, 'timeout', xhr, settings)
}, settings.timeout)
```

最后，发送数据。

```javascript
// avoid sending empty string (#319)
xhr.send(settings.data ? settings.data : null)
return xhr
```

可见，基本流程，依旧是：

1. 建立连接
2. 设置回调
3. 发送数据

只是，在此基础上，增加了对数据的校验、对跨域的支持、添加请求头、增加各阶段触发的函数等，使整个Ajax请求更加健壮。

# 其余部分

`$.get`、`$.post`、`$.getJSON`、`$.fn.load`等均是以`$.ajax`为基础进行的简化抽象。

`ajaxStart`、`ajaxStop`、`ajaxBeforeSend`、`ajaxSuccess`、`ajaxError`、`ajaxComplete`则是对各个阶段触发的回调函数的封装。除触发用户设置的回调函数，还将触发设置在全局的对应的Ajax函数。

`$.ajaxJSONP`的原理并不基于Ajax，而是基于动态创建script标签。具体的，等之后总结跨域方法的时候再谈。

# 小结

只要明白了Ajax的基本流程，再去看各库中的Ajax部分，相信会轻松许多。如若本文有何错误及模糊的地方，恳请指正～