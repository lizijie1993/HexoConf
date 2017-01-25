title: fisp中使用es6开发
comments: true
date: 2016-08-11 23:28:55
categories: 前端
tags: 构建工具 fisp es6 template.js
---

## 背景
1. 当前浏览器对es6的支持不一致，且部分功能浏览器还未支持（如Module相关），因此需要引入babel转码器，转换为es5语法;
2. fisp默认支持的前端模版[bdtmpl](http://baidufe.github.io/BaiduTemplate/)，由于严格模式下，`eval`语句有其自己的作用域，因此使用babel转换的文件会出现错误，因此需要使用新的前端模板替换。错误如下：

	```javascript
	var tmpl = [function(_template_object
	    /**/
	) {
	    var _template_fun_array = [];
	    var fn = (function(__data__) {
	        var _template_varName = '';
	        for (var name in __data__) {
	            _template_varName += ('var ' + name + '=__data__["' + name + '"];');
	        };
	        eval(_template_varName); // 严格模式下, 作用域与非严格模式不一致，导致错误
	        _template_fun_array.push('');
	        _template_varName = null;
	    })(_template_object);
	    fn = null;
	    return _template_fun_array.join('');

	}][0];
	```

## 引入babel
这里使用[fis-parser-babel-5.x](https://www.npmjs.com/package/fis-parser-babel-5.x)进行解析。es6文件后缀为`.es6`

```javascript
fis.config.merge({
    project: {
        fileType: {
            text: 'es6'
        }
    },
    modules: {
        parser: {
            es6: 'babel-5.x'
        }
    },
    settings: {
        parser: {
            'babel-5.x': {
                blacklist: ['regenerator'],
                optional: ['asyncToGenerator'],
                sourceMaps: true,
                stage: 3
            }
        }
    },
    roadmap: {
        ext: {
            es6: 'js'
        }
    }
}
```

项目模块化加载器使用的mod.js，其为amd标准的不完全实现。babel默认的模块编译标准为`common`，不会为文件包裹上`define`。尝试将`setting.parser['babel-5.x']`中将`modules`设置为`amd`，但无效，因此需要在fis-conf.js为模块进行amd包装。

```javascript
fis.config.get('roadmap.path').unshift({
    reg: /.*\.es6$/i,
    useMap: true,
    useHash: true,
    isMod: true
});
```

## 替换前端模版
这里使用[fis-parser-template](https://github.com/yanhaijing/fis-parser-template)来解析模版文件，语法参考[template.js](https://github.com/yanhaijing/template.js/blob/master/template.js)。 项目中bdtmpl的文件后缀为`.tmpl`，为避免与之冲突，这里使用`.wtpl`作为es6文件的前端模版后缀。fis-conf.js做以下配置：

```javascript
fis.config.merge({
    project: {
        fileType: {
            text: 'es6, wtpl'
        }
    },
    modules: {
        parser: {
            es6: 'babel-5.x',
            wtpl: 'template'
        },
    },
    settings: {
        parser: {
            'babel-5.x': {
                blacklist: ['regenerator'],
                optional: ['asyncToGenerator'],
                sourceMaps: true,
                stage: 3
            },
            'template': {
                sTag: '<#',
                eTag: '#>',
                global: 'template'
            }
        }
    },
    roadmap: {
        ext: {
            es6: 'js'
        }
    }
}
```

然后，需要在页面中引入[template.js](https://github.com/yanhaijing/template.js/blob/master/template.js)，不使用`umd`包装，将其暴露在`window`上。如下修改：

```javascript
;(function(root, factory) {
    var template = factory(root);
    // if (typeof define === 'function' && define.amd) {
    //    // AMD
    //    define('template', function() {
    //        return template;
    //    });
    // } else if (typeof exports === 'object') {
    //    // Node.js
    //    module.exports = template;
    // } else {
        // Browser globals
        var _template = root.template;

        template.noConflict = function() {
            if (root.template === template) {
                root.template = _template;
            }

            return template;
        };
        root.template = template;
    // }
}(this, function(root) { ... }
```

最后，需要配置fis-conf.js，使所有`.wtpl`拥有js相关扩展，否则通过`__inline()`引入后，是个纯字符串。

```javascript
fis.config.get('roadmap.path').unshift({
    reg: /.*\.wtpl$/i,
    isJsLike: true
})
```

## 注意
fis编译机上已安装所需要的fis-parser-babel-5.x和fis-parser-template插件，所以不用在本地编译啦~