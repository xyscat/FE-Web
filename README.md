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

# 简化（静态资源）

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


