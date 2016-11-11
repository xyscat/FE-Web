# 前端Web开发list

## 打包（静态资源）

打包就是在你把应用交给客户之前把所有静态资源都放在一起。这样就可以在一次HTTP响应中处理所有事务。

> 底层网络协议《High Performance Browser Networking》

简言之，打包就是把多个文件打包为一个文件。这样可以将所有CSS或JavaScript打包到一起。降低了HTTP请求的数量，提升了性能。

即，打包静态资源，减少HTTP请求数量。

很多预处理器都可以实现静态资源打包。

**Grunt实现打包**

基于Grunt的Less实现打包：使用grunt-contrib-concat包

配置拼接任务:

```
grunt.initConfig({
	// concat任务配置
	concat: {
		// 配置任务目标
		js: {
			files: {
				// 使用通配模式拼接文件
				'build/js/bundle.js': 'public/js/**/*.js'
			}
		}
	}
});
```

concat.js任务会读取public/js文件夹及其子文件夹中的所有JS文件，打包后写入build/js/bundle.js文件。

## 简化（静态资源）

简化和打包类似，目的都是减轻网络连接的负担，不过采取的方式不一样。简化不会把多个文件的内容打包为一个文件，而是删除空白、缩短变量名，以及优化代码的句法树。简化后的代码会明显变小，但付出了可读性的代价。

简化静态资源，占用空间减小，提升性能。

**Grunt实现简化**

使用Grunt的grunt-contrib-uglify包简化JS文件：

配置简化静态资源的任务：

```
grunt.initConfig({
	// 配置uglify任务
	uglify: {
		// 配置任务目标
		cobra: {
			files: {
				// 简化的文件路径
				'build/js/cobra.min.js': 'public/js/cobra.js'
			}
		}
	}
});
```

执行grunt uglify：cobra命令会简化cobra.js文件。

简化刚才打包之后的文件：

```
grunt.initConfig({
	// 配置uglify任务
	uglify: {
		// 配置任务目标
		bundle: {
			files: {
				// 简化的文件路径
				'build/js/bundle.min.js': 'public/js/bundle.js'
			}
		}
	}
});
```
这样可以按顺序执行两个任务，grunt concat:js uglify:bundle命令。

任务别名实现任务合并：

```
grunt.registerTask ('js', 'Concatenate and minify static JavaScript assets'，
	['concat:js', 'uglify:bundle']);
```

现在，js就是一个合并后的任务了。执行grunt js命令会自动执行打包和简化任务。

## 子图集

子图集是由多张图像合并而成的一个图像文件。也就是实现了图像文件的打包。sprite图就是子图集，操作CSS使用sprite图可以使用图像。在游戏开发中，通常将小图像合并为一个大图像，提升游戏性能。在Web开发中，一些小图标等可以使用此技术。

如果你使用过sprite图的话，你会发现使用和维护子图集是非常麻烦的。

**Grunt实现子图集**

使用Grunt插件grunt-spritesmith：

配置任务：

```
grunt.initConfig ({
	sprite: {
		icons: {
			src: 'public/img/icons/*.png',
			destImg: 'build/img/icons.png',
			destCSS: 'build/css/icons.css'
		}
	}
});
```
destImg属性的值是生成的子图集表单文件路径，destCSS属性的值是在HTML中渲染子图集表单时各子图的CSS文件。

> UX中的速度问题： [http://bevacqua.io/bf/speed-matters](http://bevacqua.io/bf/speed-matters)

## 检查代码（代码完整性）

**清理工作目录**

在构建一个项目的时候，项目目录内会存在一些额外的代码内容，如预处理代码。一般来说，可以把编译后的源码放在build目录。

Grunt实现清理目录：

使用Grunt中的grunt-contrib-clean包：

```
grunt.initConfig ({
	clean: {
		// 直接删除目录
		js: 'build/js',
		css: 'build/css',
		// 删除目录的指定文件
		less: 'public/**/*.css'
	}
});
```

**lint程序检查代码**

基于Grunt使用grunt-contrib-jshint包：

```
grunt.initConfig ({
	jshint: {
		client: [
			'public/js/**/*.js',
			'!public/js/vendor'
		]
	}
});
```

## 单元测试

单元测试的作用是确保代码中各个组件能正常运作。开发测试良好的应用的范例流程：
- 为功能编写测试；
- 运行测试；
- 修改代码；
- 再次运行测试；

这个过程叫测试驱动开发（Test-Driven Development,TDD）。


## jQuery的不足

jQuery库实现了浏览器的兼容性，提供了灵活的API。

## MVC

M:保存渲染视图所需的信息。

V:负责渲染模型中的数据，让用户和模型交互。

C:在渲染视图前查询模型，管理用户和各组件之间的交互。

## Backbone

Backbone、Angular、React。

**安装Backbone**

`npm init`创建package.json文件;

`npm install backbone --save`安装backbone；

Backbone需要一个处理DOM的库，jQuery、Zepto。`npm install jquery --save`安装jQuery；

使用Backbone之前，要把jQuery库赋值给Backbone.$：

```
var Backbone = require('backbone');
Backbone.$ = require('jquery');
```

Backbone使用jQuery和DOM交互，处理事件和Ajax请求。

## 使用Grunt和Browserify编译Backbone模块

```
// Gruntfile.js文件中Browserify的配置
{
	browserify: {
		debug: {
			files: {'build/js/app.js': 'js/app.js'},
			options: {
				debug: true
			}
		}
	}
}
```

改动，监视变动，Grunt重新构建，打包应用。

```
// 监视变动使用grunt-contrib-watch包
{
	watch: {
		app: {
			// tasks属性的值是files中监视的文件发生变化时要执行的任务
			files: 'app/**/*.js',
			tasks: ['browserify']
		}
	}
}
```

使用brfs（浏览器文件系统）的转换方式，让Browserify把模块中的源码转换成适合在浏览器中运行的格式。

```
var fs = require('fs');
var template = fs.readFileSync(_dirname + '/template.html', {encoding: 'utf8'});

console.log(template);
```

上面的代码不能在浏览器运行，因为浏览器不能访问服务器文件系统中的文件。可以在grunt-browserify包的配置选项中添加brfs转换方式。

```
options: {
	transform: ['brfs'],
	debug: true
}
```

`npm install brfs --save-dev`在本地安装brfs包。

现在Grunt会使用Browserify编译CommonJS模块。

