# webpack
### webpack的使用
#### webpack特征
- 插件化 提供了丰富的插件接口，webpack开发了很多插件作为内置功能
- 速度快 使用异步IO以及多级缓存机制
- 丰富的loaders loaders用来对文件做预处理，这样webpack就可以打包任何静态文件
- 高适配性 webpack支持AMD/CommonJS/ES6模块方案，webpack会静态解析代码，自动帮你管理文件之间的依赖，其对第三方库的兼容性很好
- 代码拆分 将代码分片，按需打包，这种机制很好保证页面只加载需要的JS代码，减少首次请求时间
- webpack提供了优化机制来减少打包输出的文件大小，提供hash机制，解决浏览器缓存问题
- 开发模式友好： webpack为开发模式提供了很多辅助功能，比如SourceMap、热更新等

#### 安装
>```
	npm install -g webpack//全局安装
	npm install --save-dev webpack //安装到项目目录
>```

1. 在文件夹里创建package.json文件，在终端上使用npm init命令自动创建这个package.json文件

	```
	npm init
	```
2. package.json文件就绪后，在本项目中安装webpack作为依赖包
	
	```
	npm install --save-dev webpack
	```
3. 前面两歩安装完成可以看到文件夹的目录结构为一个node_modules文件夹里面存放依赖，一个package.json文件(npm说明文件)。现在在文件里创建两个文件夹,app文件夹和public文件夹。
	
	```
	app文件用来存放原始数据和我们将写的JavaScript模块
	public文件存放浏览器读取的文件(包括使用webpack打包生成的js文件以及一个index.html文件)
	```
	- public文件夹内的index.html文件中写入最基础的代码，它在这里目的在于引入打包后的js.
	- app文件夹存放Greet.js和main.js文件
	
	>
	假设在index.html文件里写入最基础的代码,bundle.js是打包之后的js文件
	
	```
	<!DOCTYPE html>
	<html lang="en">
	<head>
	    <meta charset="UTF-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1.0">
	    <meta http-equiv="X-UA-Compatible" content="ie=edge">
	    <title>webpack project</title>
	</head>
	<body>
	    <div id="root"></div>
	    <script src="bundle.js"></script>
	</body>
	</html>
	```
	在Greet.js中定义一个函数，并根据CommonJS将其导出
	
	```
	module.exports = function () {
	    var greet = document.createElement('div');
	    greet.textContent = "hi there and greetings";
	    return greet;
	}
	```
	在main.js引入Greet.js，将Greet返回的节点插入页面
	
	```
	const greeter = require('./Greeter.js');
 	document.querySelector('#root').appendChild(greeter());
	```
	
#### 使用webpack的方式
1. 在终端中敲入命令行

	```dash
	{extry file}出填写入口文件的路径，本文中就是上述main.js的路径，
   {destination for bundled file}处填写打包文件的存放路径
    填写路径的时候不用添加{}
   webpack {entry file} {destination for bundled file}
	```
	
	```
	webpack zhangyan$ webpack app/main.js public/bundle.js//全局已经装了webpack
	或者
	node_modules/.bin/webpack app/main.js public/bundle.js//全局没有装webpack，只有用node_modules里的
	```
2. 通过配置文件来使用
	在根目录下定义一个配置文件webpack.config.js，在其中写上一些简单配置，主要涉及入口文件路径和打包后文件的存放路径
	
	```
	module.exports = {
	    entry: __dirname + '/app/main.js', //唯一入口文件
	    output: {
	        path: __dirname + '/public', //打包后文件存放的地方
	        filename: 'bundle.js' //打包后输出文件的文件名
	    }
	}
	```
	>__dirname是nodejs中的一个全局变量，指向当前执行脚本所在的目录
	
	有了这个配置后，在终端里运行webpack(非全局安装需要使用node_moudules/.bin/webpack)命令，这条命令会自动引用webpack.config.js文件中的配置选项
	![](imgs/webpack02.png)
3. 更快捷的执行打包任务
	对npm进行配置后可以在命令行中使用简单的npm start来替换上面繁琐的node_module/.bin/webpack这样的命令，在package.json中对scripts对象进行配置  
	
	```
	"scripts": {
	   "start": "webpack"
	 },
	```
	在命令行里输入npm start 
	![](imgs/webpack03.png)
	
### webpack的强大功能
#### 生成Source Maps(个人称之为资源映射表)
![](imgs/webpack04.png) 

	devtool选项  | 配置结果
	------------|------
	eval        | 每个module会封装到eval里包裹起来执行，并且会在末尾追加注释 //@ sourceURL
	source-map  | 生成一个SourceMap文件，在一个单独文件中产生一个完整且功能完全的文件，这个文件具有最好的source map,但是它会减慢打包速度 
	hidden-source-map| 和source-map一样，但是不会再dubble末尾追加注释
	inline-source-map| 生成一个DataUrl形成的SourceMap文件
	eval-source-map | 每个module会通过eval()来执行，并且生成一个DataUrl形式的SourceMap。使用eval打包源文件模块，在同一文件中生成干净的完整的SourceMap。这个选项可以在不影响构建速度的前提下生成完整的SourceMap，但是对打包后输出的js文件的执行具有性能和安全的隐患， 在开发阶段这是一个很好的选择，但是在生产阶段一定不要启用这个选项
	cheap-source-map| 生成一个没有列信息的SourceMaps文件，不包含loader的SourceMap。你如babel的SourceMap
	cheap-module-source-map| 生成一个没有列信息的SourceMaps文件，同时loader的SourceMap也被简化为只包含对应行的。在一个单独的文件中生成一个不带列映射的map,不带列映射可以提高打包的速度，但是也是的浏览器开发者工具只能对应到具体的行，不能对应到具体的列，会对调试造成不便。
	

##### 不同的devtool的配置后,打包后bundle.js文件的区别
1. 使用devtool:'eval'

	```
	eval("const greeter = __webpack_require__(1);\ndocument.querySelector('#root').appendChild(greeter());\n\n//////////////////\n// WEBPACK FOOTER\n// ./app/main.js\n// module id = 0\n// module chunks = 0\n\n//# sourceURL=webpack:///./app/main.js?");
	
	```
	每个模块被转换成了字符串，用eval包起来了，而且末尾都有//# sourceURL的注释
2. 使用devtool: 'source-map'

	![](imgs/webpack05.png)
	会单独生成一个完整的bundle.js.map的文件，而且bundle.js末尾也会有sourceMappingURL的注释
3. 使用devtool: 'hidden-source-map'  
	和source-map一样，也会生成一个完整的bundle.js.map文件，但是不会在bundle.js末尾加上sourceMappingURL的注释
4. devtool: 'inline-source-map'

	```
	//# sourceMappingURL=data:application/json;charset=utf-8;base64,eyJ2ZXJzaW9uIjozLCJzb3VyY2VzIjpbIndlYnBhY2s6Ly8vd2VicGFjay9ib290c3RyYXAgNzBmODU1YWQ3Y2RhZjVhZTUxNjQiLCJ3ZWJwYWNrOi8vLy4vYXBwL21haW4uanMiLCJ3ZWJwYWNrOi8vLy4vYXBwL0dyZWV0ZXIuanMiX
	```
	为打包前的每个文件添加sourcemap的dataUrl,追加到打包后文件内容的结尾，dataUrl包含一个文件完整的sourcemap信息的Base64格式化后的字符串
5. eval-source-map  
	用eval把每个模块包起来了，并且将打包前的每个模块的sourcemap信息转换为Base64编码，拼接在了每个打包后的文件的结尾
6. 等等。不一一尝试了
##### 总结
对于小到中型的项目中，，eval-source-map是一个很好的选择。但是只能在开发阶段使用它，不能再生产阶段使用。cheap-module-eval-source-map方法构建速度更快，但是不利于调试，推荐在大型项目考虑时间成本时使用它。
#### 使用webpack构建本地服务器
让浏览器监听代码的修改，并自动刷新显示修改后的结果，webpack提供一个可选的本地开发服务器，这个本地服务器基于node.js构建，可以实现以上的功能,它是一个单独的组件，在webpack中进行配置之前需要单独安装它作为项目依赖。
	
	```
	npm install --save-dev webpack-dev-server
	```
deserver作为webpack配置选项中的一项，它的配置选项有
	
	1. contentBase 默认webpack-dev-server会为根文件夹提供本地服务器，如果想为另一个目录提供本地服务器，应该在这里设置其所在目录
	2. port: 设置默认端口，默认为8080
	3. inline 设置为true，当源文件改变时会自动刷新
	4. historyApiFallback 在开发单页应用时非常有用，它依赖于HTML5 history API，如果设置为true，所有的跳转将指向index.html
	
		```
		//webpack.config.js设置
		module.exports = {
		  devtool: 'eval-source-map',
		
		  entry:  __dirname + "/app/main.js",
		  output: {
		    path: __dirname + "/public",
		    filename: "bundle.js"
		  },
		  devServer: {
		    contentBase: "./public",//本地服务器所加载的页面所在的目录
		    historyApiFallback: true,//不跳转
		    inline: true//实时刷新
		  } 
		}
		//package.json中scripts对象添加命令
		"scripts": {
		    "test": "echo \"Error: no test specified\" && exit 1",
		    "start": "webpack",
		    "server": "webpack-dev-server --open"
		  },
		```
#### Loaders
使用不同的loader，webpack有能力调用外部的脚本和工具，实现对不同格式的文件处理，可以使得scss转成css,es6转成现代浏览器兼容的js文件等。

Loaders需要单独安装并且在webpack.config.js中modules关键字进行配置：
- test:用匹配loaders所处理文件的拓展名的正则表达式(必须)
- loader: loader的名称(必须)例如es6转成es5 用babel
- include/exclude 手动添加必须处理的文件（文件夹）或者屏蔽不需要处理的文件（文件夹） （可选）
- query: 为loaders提供额外的设置选型（可选）
