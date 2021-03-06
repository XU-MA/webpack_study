# webpack_study
webpack 3.5.6 版本学习总结
1.（1）什么是webpack ： Webpack => 模块打包机 => exports / module.exports
用途 ：分析你的项目结构，找到JavaScript模块以及其他一些浏览器不能直接运行的扩展语言；例如(less)并将其转换和打包为合适的格式供浏览器使用
  （2）为什么使用webpack ： 把js、css、img等(模块)打包，减少请求
  （3）怎么安装webpack : 
       1.npm init => package.json
       2.（1）局部安装(推荐) => npm i --save-dev webpack@(版本号)3.5.6
         （2）全局安装(不推荐 => 原因 ：框架更新太快) => npm i(install) -g webpack@3.5.6(版本号)
2.终端命令实现webpack打包
  node_modules\.bin\webpack app\main.js public\build_main.js(被合并成一个文件所起的名字)
（1）手动创建配置（JS）文件（webpack.config.js(规定)）
（2）然后可以直接使用node_modules\.bin\webpack来进行打包 => 它会搜索是否存在webpack.config.js
（3）在package.json 里面的script对象是一个自定义指令的位置 我们可以简化命令实现打包
  1.npm 直接 设置 key 是有限制的 ：是npm关键字 => npm key（例如 ："scripts": {"start": "webpack"}）
    npm自带的关键字:
    (access, adduser, bin, bugs, c, cache, completion, config,ddp, dedupe, deprecate, dist-tag, docs, doctor,edit,explore,                   get, help, help-search, i, init, install,install-test, it, link, list, ln, login, logout, ls,outdated, owner, pack,                     ping, prefix, profile, prune,publish, rb, rebuild, repo, restart, root, run, run-script,s, se, search,set,shrinkwrap,star, stars,       start, stop, t,team, test, token, tst, un, uninstall, unpublish, unstar,up, update, v, version, view, whoami)
  2.如果不是 npm 自带的关键字 需要 => npm run key
（4）webpack 的强大功能 调试更简单 => source maps => 打包之后的文件 如果配置了 source maps 可以看到依赖的模块代码
  到底在哪里配置？=> webpack.config.js => devtool: "eval-source-map" => eval-source-map ：偏中小型的一种
  使用eval 打包源文件模块，在同一文件中生成干净的完整的source-map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的   执行具有性能和安全的隐患。再开发阶段这是一个非常好的选项，在生产阶段一定不要启用这个选项对小到中型的项目中，eval-source-map是一个非常好的选项，再次强   调你只应该开发阶段使用它，我们继续对上文新建的webpack.config.js，进行如下配置：
  
    module.exports = {
      devtool: "eval-source-map",
      // 唯一的入口文件
      entry : __dirname + '/app/main.js',
      // 打包之后的文件该输出到哪个位置
      output : {
          path : __dirname + '/public',
          filename : 'build_main.js'
      },
    }
（5）代码热更新 => 创建自己的开发测试服务器 webpack 提供一个本地开发服务器，这个服务器是基于node.js不过这个模块需要下载并且依赖一下
  ===> npm i --save-dev webpack-dev-server（版本可能会出现错误需要你手动降低版本 npm i --save-dev webpack-dev-server@2.9.5 ）
  在package.json中配置 ："scripts": { "server": "webpack-dev-server --open"},
     webpack-dev-server => 3.0以上的版本 => webpack 4.0服务的
  在webpack.config.js中配置
    // devServer 2.9.5 启动自带热更新
    // 并且它会监测所依赖的模块是否修改
    // 我们update code => 状态触发 => 重新打包 => 触发刷新
    devServer: {
        // 本地服务器 所加载的页面所在的目录
        contentBase : './public',
        // 服务器端口 => 默认端口 8080
        port : '8888',
    }
（6）webpack 又一强大功能 ---loaders ====> 通过使用不同的 loader，webpack 有能力调用外部的工具和模块，实现对不同格式的文件的处理，
  比如说把 ES6 => ES5；转化成现代浏览器兼容的js文件 ====> 下载 babel 处理模块包
  babel 的安装和配置
    babel的三个核心模块
      babel-loader     ===> 让webpack知道babel如何运行
      babel-core       ===> 让babel知道如何解析代码
      babel-preset-env ===> 可以根据不同环境转换代码
    安装：cnpm i --save-dev babel-loader babel-core babel-preset-env
  babel-loader ===> ES6 进行转义 ==> (前端的 export 与 export default 规范)
  在webpack.config.js里配置 ： 
  
    module:{
      rules:[
        {
        
          // 必须要有的 一个用以匹配loaders所处理文件的扩展名
          // 正则表达式
          test:/\.js$/,
          // 后面配置的是 loader的名称
          use:'babel-loader',
          // include / exclude 必须处理的文件和屏蔽的文件
          exclude:/node_modules/
      },
    ]
  }
    
（7）对css进行打包!!!
  下载:cnpm i --save-dev style-loader css-loader@0.28.11
  写法:在webpack.config.js: 
  
    module:{
        rules:[
        
            // 安装 style-loader 与 css-loader
            {
                test:/\.css$/,
                // 配置多个loader时候 use => value 是数组
                // 如果没有额外配置 直接string
                // 如果有额外配置 为json对象
                use:[
                    'style-loader',
                    {
                        loader:'css-loader',
                        options: {
                            // 把css当成 模块 => json 对象来看待
                            modules : true,// 指定启用css modules
                            localIdentName:'[name]__[local]--[hash:base64:5]'
                        },

                    }
                ]
            }
        ]  
    }
    
  在需要打包的JS文件里接收  import 自定义名字 from '文件路径'（例如 ：import mx_css from './todoList.css';）
    在组件里返回:
    
    data(){
          return {
              mx_css : mx_css, 
          }
      }
  自定义组件:<div :class="mx_css.wrap"></div>
  
（8）对图片进行打包
  下载:cnpm i --save-dev file-loader url-loader
  写法:
  
  module:{
        rules:[
        
            // 打包图片 file-loader / url-loader
            {
                test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
                use: [
                    {
                        loader: 'url-loader',
                        // 配置 url-loader 的可选项
                        options: {
                            // 限制 图片大小 10000B，小于限制会将图片转换为
                            // base64格式
                            limit: 10000,
                            // 超出限制，创建的文件格式
                            // build/image/[图片名].[hash].[图片格式]
                            name: 'images/[name].[hash].[ext]'
                        }
                    }
                ]
            }
        ]
     }
    在需要打包的组件js文件里接收 let 变量名 = require('图片路径')（例如 ：let big = require('../image/big.jpg');）
    在data里返回:
    
      data(){
          return {
             big:big,
          }
      }
      
    在自定义组件:<img :src="big" alt="">
    
(9)插件：
  注意:插件需要用require引入,用的是webpack自带插件
  1.版权所有，翻版必究（在build_main最上面的注释）
    安装:cnpm install --save-dev html-webpack-plugin  
    引入:let webpack = require('webpack');
    写法:
      plugins:[
        new webpack.BannerPlugin('版权所有，翻版必究')
      ]
  2.HtmlWebpackPlugin:依据一个简单的index.html模板，生成一个自动引用你打包后的JS文件的新index.html。
    安装:npm install --save-dev html-webpack-plugin
    引入:const HtmlWebpackPlugin = require('html-webpack-plugin');
    写法:
        new HtmlWebpackPlugin({
            template: __dirname + "文件路径"
        })
 3.变化格式类型
  安装:cnpm i --save-dev uglify-js-plugin
  引入:let UglifyJsPlugin = require('uglify-js-plugin');
  写法:
      new webpack.optimize.UglifyJsPlugin()
 4.去除build文件中的残余文件
  安装:cnpm install clean-webpack-plugin --save-dev
  引入:const CleanWebpackPlugin = require("clean-webpack-plugin");
  写法:
      new CleanWebpackPlugin('build/*.*', {
            // 绝对路径
            root: __dirname,
            //将日志写入控制台。
            verbose: true,
            ////使用布尔“true”来测试/模拟删除。 （不会删除文件）。
            //默认值：false - 删除文件
            dry: false
        })
5.JS.css代码分离
 安装:cnpm install --save-dev extract-text-webpack-plugin
 引入:const ExtractTextPlugin = require('extract-text-webpack-plugin');
 写法:new ExtractTextPlugin("style.css")
6.缓存:filename: "bundle-[hash].js"
  在文件名最后加上[hash]散列函数
  
  所有插件的版本号：
    "devDependencies": {
    "babel-core": "^6.26.3",
    "babel-loader": "^7.1.1",
    "babel-preset-env": "^1.7.0",
    "babel-preset-es2015": "^6.24.1",
    "clean-webpack-plugin": "^0.1.19",
    "css-loader": "^1.0.0",
    "extract-text-webpack-plugin": "^3.0.2",
    "file-loader": "^2.0.0",
    "html-webpack-plugin": "^3.2.0",
    "style-loader": "^0.23.0",
    "uglify-js-plugin": "^0.0.6",
    "url-loader": "^1.1.1",
    "webpack": "^3.5.6"
  }
  
  
  
  大总结：
  1.npm init  ==> package.json（终端输入命令）

2.局部安装webpack  ==> npm i --save-dev webpack@(版本号)3.5.6（终端输入命令）

3.新建文件夹： 
	app    ==> main.js
	public ==>index.html
4.全局定义一个文件：webpack.config.js（配置文件）
5.在webpack.config.js里输入：
module.exports = {
    devtool: "eval-source-map",
    // 唯一的入口文件(__dirname:绝对路径)
    entry : __dirname + '/app/main.js',
    // 打包之后的文件该输出到哪个位置
    output : {
        path : __dirname + '/public',
        filename : 'build_main.js'
    },
}
6.打包方式：（一般用第三种简单好记，但一定要配置"scripts": {"key": "webpack"}）
	（1）node_modules\.bin\webpack app\main.js public\build_main.js（终端输入命令）
	（2）node_modules\.bin\webpack（终端输入命令）
	（3）在package.json 里面的script对象是一个自定义指令的位置 我们可以简化命令实现打包
  
		1）npm 直接 设置 key 是有限制的 ：是npm关键字 => npm key（例如 ："scripts": {"start": "webpack"}）
    
		     npm自带的关键字:
    (access, adduser, bin, bugs, c, cache, completion, config,ddp, dedupe, deprecate, dist-tag, docs, doctor,edit,explore,                   get, help, help-search, i, init, install,install-test, it, link, list, ln, login, logout, ls,outdated, owner, pack,ping, prefix, profile, prune,publish, rb, rebuild, repo, restart, root, run, run-script,s, se, search,set,shrinkwrap,star, stars,start, stop, t,team, test, token, tst, un, uninstall, unpublish, unstar,up, update, v, version, view, whoami)
  
		2）如果不是 npm 自带的关键字 需要 => npm run key
7.webpack的强大功能 ：调试更简单（source maps）
	打包之后的文件 如果配置了 source maps 可以看到依赖的模块代码 ===> webpack.config.js ===> devtool: "eval-source-map"
	<!-- eval-source-map ：偏中小型的一种 -->
        	使用eval 打包源文件模块，在同一文件中生成干净的完整的source-map。这个选项可以在不影响构建速度的前提下生成完整的sourcemap，但是对打包后输出的JS文件的执行具有性能和安全的隐患。再开发阶段这是一个非常好的选项，在生产阶段一定不要启用这个选项对小到中型的项目中，eval-source-map是一个非常好的选项，再次强调你只应该开发阶段使用它，我们继续对上文新建的webpack.config.js，进行如（5）配置
8.代码热更新 => 创建自己的开发测试服务器 ： webpack提供一个本地开发服务器，这个服务器是基于node.js，不过这个模块需要下载并且依赖一下
        	npm i --save-dev webpack-dev-server（终端输入命令) ===> webpack-dev-server => 3.0以上的版本 => webpack 4.0服务的
	在package.json中配置 ："scripts": { "server": "webpack-dev-server --open"},
	在webpack.config.js中配置
 :
	// devServer 2.9.5 启动自带热更新
    	// 并且它会监测所依赖的模块是否修改
    	// 我们update code => 状态触发 => 重新打包 => 触发刷新
    	devServer: {
        		// 本地服务器 所加载的页面所在的目录
        		contentBase : './public',
        		// 服务器端口 => 默认端口 8080
        		port : '8888',
    	},
9.webpack又一强大功能 ---loaders 
	====> 通过使用不同的 loader，webpack 有能力调用外部的工具和模块，实现对不同格式的文件的处理，比如说把 ES6 => ES5；
	转化成现代浏览器兼容的js文件 ====> 下载 babel 处理模块包
  babel 的安装和配置
    
	babel的三个核心模块
	
      babel-loader     ===> 让webpack知道babel如何运行
	
      babel-core       ===> 让babel知道如何解析代码
	
      babel-preset-env ===> 可以根据不同环境转换代码

	      安装：cnpm i --save-dev babel-loader babel-core babel-preset-env
  babel-loader （终端输入命令) ===> ES6 进行转义 ==> (前端的 export 与 export default 规范)
	（1）在webpack.config.js里配置 ： （对所有js文件进行打包）
	module:{

	      rules:[
	
        {
        
 
	          // 必须要有的 一个用以匹配loaders所处理文件的扩展名
	
          // 正则表达式
	
          test:/\.js$/,
	
          // 后面配置的是 loader的名称
	
          use:'babel-loader',
	
          // include / exclude 必须处理的文件和屏蔽的文件
	
          exclude:/node_modules/
	
      },

    	    ]

  	}
	（2）对css进行打包!!!
	
  下载:cnpm i --save-dev style-loader css-loader@0.28.11 （终端输入命令) 
	
  写法:在webpack.config.js:
	 
  
    module:{
	
        rules:[
        
  
	          // 安装 style-loader 与 css-loader
	
            {
 
	               test:/\.css$/,
 
	               // 配置多个loader时候 use => value 是数组
	
                // 如果没有额外配置 直接string
	
                // 如果有额外配置 为json对象
	
                use:[

	                    'style-loader',

	                    {

	                        loader:'css-loader',
 
	                       options: {
 
	                           // 把css当成 模块 => json 对象来看待

	                            modules : true,// 指定启用css modules
	
                            localIdentName:'[name]__[local]--[hash:base64:5]'
	
                        },


	                    }

	                ]

	            }
	
        ]
	  
    }

	    
  在需要打包的JS文件里接收  import 自定义名字 from '文件路径'（例如 ：import mx_css from './todoList.css';）
	  
    在组件里返回:

	    
    data(){
	
          return {
	
              mx_css : mx_css,
	 
          }
	
      }
  
	    自定义组件:<div :class="mx_css.wrap"></div>
	（3）对图片进行打包
	
  下载:cnpm i --save-dev file-loader url-loader （终端输入命令) 
	
  写法:
	
  
  module:{
	        rules:[

	        
            // 打包图片 file-loader / url-loader
	
            {
	
                test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
	
                use: [

	                    {

	                        loader: 'url-loader',

	                        // 配置 url-loader 的可选项

	                        options: {
	
                            // 限制 图片大小 10000B，小于限制会将图片转换为
	
                            // base64格式	
	
                            limit: 10000,
	
                            // 超出限制，创建的文件格式

	                            // build/image/[图片名].[hash].[图片格式]

	                            name: 'images/[name].[hash].[ext]'

	                        }
	
                    }
	
                ]

	            }

	        ]

	     }

	在需要打包的组件js文件里接收 let 变量名 = require('图片路径')（例如 ：let big = require('../image/big.jpg');）
	
    在data里返回:	    
      data(){
          return {
	             big:big,
          }
      }

      
      在自定义组件:<img :src="big" alt="">
10.插件（注意:插件需要用require引入,用的是webpack自带插件）
	（1）版权所有，翻版必究（在build_main最上面的注释）
	
        安装:cnpm install --save-dev html-webpack-plugin （终端输入命令) 
	  
      引入:let webpack = require('webpack');

	        写法:
	
        plugins:[

	            new webpack.BannerPlugin('版权所有，翻版必究')
	
         ]
  
	（2）HtmlWebpackPlugin:依据一个简单的index.html模板，生成一个自动引用你打包后的JS文件的新index.html。
	    
    安装:npm install --save-dev html-webpack-plugin （终端输入命令) 
	    
    引入:const HtmlWebpackPlugin = require('html-webpack-plugin');

	        写法:
	     
        new HtmlWebpackPlugin({
	     
            template: __dirname + "文件路径"

	             })
 
	（3）变化格式类型
	      
  安装:cnpm i --save-dev uglify-js-plugin
  引入:let UglifyJsPlugin = require('uglify-js-plugin');
 （终端输入命令) 
	        写法:
      new webpack.optimize.UglifyJsPlugin()

	（4）去除build文件中的残余文件
	      
  安装:cnpm install clean-webpack-plugin --save-dev （终端输入命令) 
	      
  引入:const CleanWebpackPlugin = require("clean-webpack-plugin");
	      
  写法:

	    new CleanWebpackPlugin('build/*.*', {

	                 // 绝对路径
	    
            root: __dirname,
	    
            //将日志写入控制台。
	    
            verbose: true,
	    
            ////使用布尔“true”来测试/模拟删除。 （不会删除文件）。
		        //默认值：false - 删除文件
	    
            dry: false
	    
      })

	（5）JS.css代码分离
	    
 安装:cnpm install --save-dev extract-text-webpack-plugin
 （终端输入命令) 
	     引入:const ExtractTextPlugin = require('extract-text-webpack-plugin');
	    
 写法:new ExtractTextPlugin("style.css")
	（6）
缓存:filename: "bundle-[hash].js"
	      
  在文件名最后加上[hash]散列函数


