title: grunt使用入门
comments: true
date: 2015-07-08 00:04:19
updated:
categories: 工具分享
tags: grunt
---
在项目中，为了简化代码合并、压缩的流程，接触了Grunt。一入Grunt深似海，以后不用手动粘贴代码，再用软件或者在线工具压缩后另存为文件了！当然，这只是Grunt的一小部分作用~在此记录Grunt的使用。

# 什么是Grunt？
> Grunt 是一个基于任务的JavaScript工程命令行构建工具。一句话：自动化。对于需要反复重复的任务，例如压缩（minification）、编译、单元测试、linting等，自动化工具可以减轻你的劳动，简化你的工作。

# Grunt使用
[Grunt中文网](http://www.gruntjs.net/ 'Grunt中文网')有详细的教程，在这里，我只以CSS、Javascript文件合并与压缩做个简单的入门实例，方便以后查阅。

## 实例前的准备
Grunt和 Grunt 插件是通过 npm 安装并管理的，npm是 Node.js 的包管理器。

1. 首先安装 node.js，直接去[node.js官网](https://nodejs.org/ 'node.js')下载即可;
2. 安装 npm 。node.js包会集成 npm，因此不用手动安装，可以在系统的命令行中输入`npm -v`查看。为了使用Grunt，最好将 npm 更新至最新版本，输入`npm upgrade -g npm`即可（windows需要管理员模式，linux需要sudo等）；
3. 将Grunt命令行（CLI）安装到全局环境中 `npm install -g grunt-cli`。

    > 注意，安装grunt-cli并不等于安装了 Grunt！Grunt CLI的任务很简单：**调用**与Gruntfile在同一目录中 Grunt。这样带来的好处是，允许你在同一个系统上同时安装多个版本的 Grunt。

## CSS、Javascript文件合并压缩实例
1. 在项目根目录新建package.json文件，该文件列出了项目的相关信息及所需的依赖（可以通过`npm init`命令创建）。结构类似如下：
    
    ```json
    {
        "name": "my-project-name",
        "version": "0.1.0",
        "devDependencies": {
            "grunt": "~0.4.5",
            "grunt-contrib-jshint": "~0.10.0",
            "grunt-contrib-nodeunit": "~0.4.1",
            "grunt-contrib-uglify": "~0.5.0"
      }
    }
    ```

2. 安装Grunt和Grunt插件。`npm install <module> --save-dev`
    
    > 此命令不光安装了<module>，还会自动将其添加到 *devDependencies* 配置段中，遵循tilde version range格式。

    本例中需要使用上述方式安装 *grunt* 、 *grunt-contrib-cssmin* （用于合并、压缩CSS）及 *grunt-contrib-concat*、*grunt-contrib-uglify*（用于合并、压缩JS）。将会得到以下 *package.json*:

    ```json
    {
        "name": "demo",
        "version": "1.0.0",
        "author": "lizijie1993@gmail.com",
        "description": "demo",
        "license": "MIT",
        "devDependencies": {
            "grunt": "^0.4.5",
            "grunt-contrib-concat": "^0.5.1",
            "grunt-contrib-cssmin": "^0.12.3",
            "grunt-contrib-uglify": "^0.9.1"
         }
    }

    ```

3. 创建Gruntfile文件。该文件同 *package.json*一同放在根目录。由以下四个部分构成：
    
    * "wrapper"函数
    * 项目与任务配置
    * 加载grunt插件和任务
    * 自定义任务

    3.1 编写"wrapper"函数：
    
    ```javascript
        //wrapper函数
        module.exports=function(grunt){
        }
    ```

    3.2 项目与任务配置

    ```javascript
        //wrapper函数
        module.exports=function(grunt){
            //项目与任务配置
            grunt.initConfig({
                pkg:grunt.file.readJSON('package.json'),//将存储在package.json文件中的JSON元数据引入到grunt config中
                concat:{
                    options:{
                        //定义一个用于插入合并输出文件之间的字符
                        separator:'\n'
                    },
                    jsFile:{//任务名称可随意
                        //将要合并的文件
                        src:['js/**/*.js'],// /**/为任意路径
                        // 合并后的JS文件的存放位置
                        dest: 'js/<%= pkg.name %>.js'//<%= %>可以引入配置文件的各值
                    }
                },
                uglify:{
                    options: {
                     banner: '/*! <%= pkg.name %> <%= grunt.template.today("dd-mm-yyyy") %> */\n'
                    },
                    jsFile:{
                        files:{
                            'js/<%= pkg.name %>.min.js':['<%= concat.jsFile.dest %>']//另一种指定源路径与目标路径的方法
                        }
                    }
                },
                cssmin:{//cssmin插件配置
                    options:{//插件的选项
                        rebase:true,//合并CSS后，是否重新计算路径
                        target:'css/cssmin'//以该路径为基准，计算CSS中的路径
                    },
                    //CSS文件合并
                    concat:{
                        files:{
                            '<%= cssmin.options.target %>/all.css': ['css/*.css']
                        }
                    },
                    //CSS文件压缩
                    minify: {
                        files: [{
                            expand: true,
                            cwd: '<%= cssmin.options.target %>', //源文件路径
                            src: ['*.css', '!*.min.css'],//文件类型
                            dest: '<%= cssmin.options.target %>', //目标文件路径
                            ext: '.min.css'
                        }]
                    }
                }
            });
        };
    ```

    3.3 加载所需的Grunt插件

    ```javascript
        //加载grunt插件
        grunt.loadNpmTasks('grunt-contrib-concat');
        grunt.loadNpmTasks('grunt-contrib-uglify');
        grunt.loadNpmTasks('grunt-contrib-cssmin');
    ```
    3.4 设置任务

    ```javascript
        //设置任务
        //只需在命令行上输入"grunt"，就会执行default task
        grunt.registerTask('default', ['cssmin', 'concat', 'uglify']);
        //在命令行上输入"grunt jsmin"，就会执行jsmin task。
        grunt.registerTask('jsmin', ['concat', 'uglify']);
    ```

4. 命令行进入项目根目录，使用输入 `grunt` 即可完成css、js的合并与压缩~

## 注意

1. 合并文件时，如果要保证文件的顺序，需要依次手动指定文件，如：`src:['a.js','b.js','c.js']`；
2. 任务命名时，不要和任务配置中的名字相同，否则会出错，如 `grunt.registerTask('cssmin',['cssmin'])`。

## 总结
Grunt使用过程可以总结为：

1. 软件安装，包括 Node.js 、npm 、grunt-cli；
2. 编写 package.json，安装grunt及grunt插件；
3. 编写 gruntfile.js；
4. 命令行进入项目根目录，运行grunt。

使用Grunt，重点在于Grunt插件的使用，需参考插件的相关文档。利用 Grunt 自动化构建工具完成重复性工作，能极大地节省时间。

本人新手一枚，如有错误，恳请指正。

本文为原创文章，欢迎大力转载，但请保留原出处