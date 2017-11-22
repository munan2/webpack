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
	
	```
	webpack zhangyan$ webpack app/main.js public/bundle.js//全局已经装了webpack
	或者
	node_modules/.bin/webpack app/main.js public/bundle.js//全局没有装webpack，只有用node_modules里的
	```
2. 通过配置文件来使用