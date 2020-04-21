---
title: Webpack4.0 
top: false
cover: false
toc: true
comments: true
lang: zh-CN
summary: WebPack基础。
tags: [WebPack, Node]
categories: 基础教程
abbrlink: 60ec903d
date: 2019-10-18 22:11:21
updated:
password:
---
# webpack4.0

## 1.安装

```JS
webpack 安装首先需要安装两个东西
1.webpack 和 webpack-cli 工具  一般采用 -D(安装到开发依赖项目 等项目上线的时候就不需要了)
```

## 2.打包

```JS
1.用yarn init -y 初始化项目以后 然后我们就开始安装webpack 和webpack-cli等工具
2.打包启用命令 采用npx  webpack
3.这里启动了打包命令以后 他就会找打node_moudles下的bin文件  然后会找到webpck.cmd文件 里面是个判断语句
```

## 3.手动配置webpakc

```JS
1.刚刚我们所用的打包配置文件都是0配置 比如打包之后的文件目录名称 和打包路径都是系统自动生成的
2.所以这里我们需要手动配置 默认配置文件的名字叫webpack.config.js
```

## 4.webpack.config.js基本配置

```JS
//webpack 是node 写出来的 所需遵循node 的模块化规范 也就是common.js规范 比如文件的导入 require 导出 module.exports
let path = require('path') //node路径模块 这里要用因为是我们的出口文件 也就是打包之后的文件必须是绝对的路径
module.exports = {
    mode:'development' //模式决定你是打包之后是生产还是上线 压缩代码的体积不一样
    extry:'./src/index.js'//你要从哪个文件打包
    output:{
    filename:'bundle.js'//你打包之后要输出的文件名称
    path:path.resolve(__dirname,'build') //要输出的路径和文件加名称 resolve这个方法就是帮你解决绝对路径的
    publickPath:'' //这个配置项目就是为我们所有打包的资源统一生成一个路径 后期你的资源可能都传送到cdn上 这样就可以直接写网路地址的路径了
	}
}
```

## 4.更改配置文件名称

```JS
1.我们默认的配置文件是webpack.confi.js 如果我们希望改名字怎么办 
2.在打包的时候就可以写npx webpack --config '你更改过后的配置文件名称'
3.也可以在你的package.json中添加脚本
{
  "name": "webpack",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
   //下面就是你要添加的脚本
  "scripts":{
    "build":"webpack --config webpack.config.my.js"
  },
  "devDependencies": {
    "webpack": "^4.41.5",
    "webpack-cli": "^3.3.10"
  }
}
```

## 5.启动服务

```JS
1.我们传统的方式都是先打包 然后再新建一个html文件 去引入好你这个打包好的js文件 这样就十分的麻烦
所以这里我们就会启动一个本地服务 所以就需要安装 webpack-dev-server的插件 这个时候你就可以再package.json里写你的启动脚本命令
{
  "name": "webpack",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
   //下面就是你要添加的脚本
  "scripts":{
    "build":"webpack --config webpack.config.my.js"
     "dev":"webpack-dev-server"
  },
  "devDependencies": {
    "webpack": "^4.41.5",
    "webpack-cli": "^3.3.10"
  }
}
2.安装好了之后我们需要再我们的webpack.config.js里配置
let path = require('path') //node路径模块 这里要用因为是我们的出口文件 也就是打包之后的文件必须是绝对的路径
module.exports = {
    //开发服务器的配置
    devServer:{
        prot:3000, //服务启动的端口号
        progress:true, //进度条
        contentBase:'./build' //你要读取哪个文件
    }
    mode:'development'
    extry:'./src/index.js'
    output:{
    filename:'bundle.js'
    path:path.resolve(__dirname,'build') 
	}
}
3.以上文件配置好了之后呢 我们只要西东就好了 他就会再文件里生成一个目录 但是我们没有html文件 所以这个时候我们就需要html-webpack-plugin 这个时候他会把你的html文件生成再内存中 不过前提是你要再你的src下设置一个模板
下面是配置 首先还是要引入
let path = require('path')
//因为这里大写了 所以这是个构造函数也是es里的类 所以我们需要new出来
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development'
    extry:'./src/index.js'
    output:{
    filename:'bundle.js'
    path:path.resolve(__dirname,'build') 
	}
    //这里方式所有的webpack插件
    plguins:[
        new HtmlWebpackPlugin({
            template:'./src/index.html'//你模板的路径
            filename:'index.html'//你希望再内存路径中访问的文件地址
            //当我们打包以后 他会自动压缩你的js文件 不过也我们希望html文件也压缩 就需要下面的配置 比如你的html文件是双引号 之类的
            minify:{
            //去除双引号
            	removeAttributeQuotes:true,
            //折叠成一行
         		collapseWhitespace:true
        }
        })
    ]
}
```

## 6.样式处理

```JS
1.首先我们本来样式的处理就是css文件 还有一些预编译的比如scss和less 遵循webpack的宗旨就是模块化 所以我们需要再配置文件里添加一个module 模块的配置项里面是一些处理的规则rules:[]
```

## 7.处理js模块

```JS
1.我们最常用的处理就是写的es6的语法 我们需要打包成es5 所以这个时候就需要用到babel的插件
2.安装 
yarn add babel-loader @babel/core(核心模块) @babel/preset-env(转化模块)
```

```JS
配置babel  因为是有loader所以需要在module里的rules选项里去配置
let path = require('path')
//因为这里大写了 所以这是个构造函数也是es里的类 所以我们需要new出来
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development'
    extry:'./src/index.js'
    output:{
    filename:'bundle.js'
    path:path.resolve(__dirname,'build') 
	}
    module:{
        rules:[
            {test:'/\.js$/' //正则 以.js结尾的都需要走我这个loader    
             use:{   //使用什么样的loader去处理js文件
             	loader:'babel-loader',
             	options:{ //配置选项 你要用那个模块去转换JS
             		presets:[ //预设 把js的语法转换 这个是大插件的集合 他可能自己有常用的插件 如果我们需要转换更高级别的语法 可能就要在配置一些小的插件 例如es7语法的class类
             			'@babel/preset-env'
             ],
            		plugins:[
                        '@babel/plugin-proposal-class-properties'
                    ]
            }
            }
            }, 
        ]
        include:path.resolve(__dirname,'src') //指让src下的JS文件去执行这个loader
	    exclude:/node_modules/  //排除这个文件不执行这个loader
    },
    plguins:[
        new HtmlWebpackPlugin({
            template:'./src/index.html'//你模板的路径
            filename:'index.html'//你希望再内存路径中访问的文件地址
            minify:{
            	removeAttributeQuotes:true,
         		collapseWhitespace:true
        }
        })
    ]
}
```

```JS
1.当我们用插件进行转换的时候 在转换的过程中 可能会用到向东的函数 这个时候 如果你有两个新的语法去转换那么这个时候就换打包转换的时候 会把这个公用的转换语法的方法 打包两次 还有一些系统内置的API
2.比如我们的class类的语法 class A{} 和 class B{} 在转换的时候 都用了一个chencall的函数 那么这个时候这个函数就会生成两次 
3.使用@balbel/plugin-transform-runtime即可
```

```JS
配置babel  因为是有loader所以需要在module里的rules选项里去配置
let path = require('path')
//因为这里大写了 所以这是个构造函数也是es里的类 所以我们需要new出来
let HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
    mode:'development'
    extry:'./src/index.js'
    output:{
    filename:'bundle.js'
    path:path.resolve(__dirname,'build') 
	}
    module:{
        rules:[
            {test:'/\.js$/' //正则 以.js结尾的都需要走我这个loader    
             use:{   //使用什么样的loader去处理js文件
             	loader:'babel-loader',
             	options:{ //配置选项 你要用那个模块去转换JS
             		enforce:'pre' //强制最先执行我这个loader 不论书写顺序在哪里
             		presets:[ //预设 把js的语法转换 这个是大插件的集合 他可能自己有常用的插件 如果我们需要转换更高级别的语法 可能就要在配置一些小的插件 例如es7语法的class类
             			'@babel/preset-env'
             ],
            		plugins:[
                        '@babel/plugin-proposal-class-properties' //转换ES7class语法
                        '@balbel/plugin-transform-runtime' //提取转换语法过程中的公共代码或者用到的相同的方法
                    ]
            }
            }
            }, 
        ]
        include:path.resolve(__dirname,'src') //指让src下的JS文件去执行这个loader
	    exclude:/node_modules/  //排除这个文件不执行这个loader
    },
    plguins:[
        new HtmlWebpackPlugin({
            template:'./src/index.html'//你模板的路径
            filename:'index.html'//你希望再内存路径中访问的文件地址
            minify:{
            	removeAttributeQuotes:true,
         		collapseWhitespace:true
        }
        })
    ]
}
```

## 8.语法校验

```JS
1.这里我们在校验的时候 也是通过laoder去实现的 所以还需要配置laoder(eslint-loader) 校验JS文件
2.安装 eslint eslint-loader
3.去下载校验的文件 官网 前面加.
4.laoder执行顺序 是做右向左 从下向上 有的时候 但是有的时候我们需要最先执行的loader却写在了上面 
```

## 9.全局变量引入

```JS
1.有的时候我们需要引入的模块比如jquey 挂在到我们的window上
2.这个时候就需要安装expose-loader
3.有的时候我们引进的代码直接就是压缩完毕的 所以不需要他打包 这个时候就需要在export.module ={
    externals:{
        jquery:'jQuery'
    }
}
```

## 10.loader的使用

```JS
1.loader的分类
pre loader 比如你的这个打包文件的规则需要再其他loader先执行 或者必须再某个指定的规则loader前面执行
普通的loader 就是写好规则之后的那些 从右向左执行
内连的loader 直接写再JS里 不需要通过webpack的配置文件去执行的
比如 import $ from 'expose-loader?$!jquery' //这个就是再单独文件里写把jquery暴漏出来 挂在到了window上
postloader 再其他loader后面执行的
```

## 11.创建分类

```JS
1.当我们不同类型的文件比如css 或者 图片 在打包的时候 希望他生成不同的文件目录
2.可以在每个loader里的options里 配置outpuPath:'目录名称' 这里的outpuPath 就是生成一个文件目录夹
3.有的时候我们只需要我们的图片文件是cdn引入 所以就可以直接写publicPath
module:{
    rules:[
        {
            test:'/\.(png|jpg|gif)$/',
            use:{
                loader:'url-loader',
                options:{
                    outputPath:'你要生成的目录名称',
                    publicPath:'你要加载的前缀的网络地址'
                }
            }
        }
    ]
}
```

## 12.打包多页面应用

```JS
1.我们以往的都是打包单页面应用 只有一个js文件 然后webpack会从你这个入口文件里面去层层递归 最后打包
2.现在我们有两个入口的js文件 所以配置就需要配置两个入口 和两个出口文件

```

```JS
配置
let path = require('path')
let HtmlWebpackPlugin = require('HtmlWebpackPlugin')
module.exports = {
    mode:'development',
    entry:{
        home:'./src/index.js' //主页的入口js文件
        other:'./src/other.js'//其他页的入口文件
    },
    output:{
        filename:'[name].js' //这里的name就是变量 他是你上面两个入口文件的名字 会自动执行
        path:path.resolve(__dirname,'dist')
    },
    //因为是两个页面 所以就需要new两次 
    plugin:[
        new HtmlWebpackPlugin({
            template:'./index.html'
            filename:'home.html'
            //引入哪个js文件
            chunks:['home']
        })
        new HtmlWebpackPlugin({
            template:'./index.html'
            filename:'other.html'
             //如果需要引入多个js文件
            chunks:['other','home']
        })
    ]
}
```

## 13.配置source-map

```JS
1.首先我们在打包的时候 代码都会被压缩 不方面我们查看错误
2.所以就需要把我们不打包的源码映射到内存中
3.devtool:'source-map'

let path = require('path')
let HtmlWebpackPlugin = require('HtmlWebpackPlugin')
module.exports = {
    mode:'development',
    entry:{
        home:'./src/index.js' //主页的入口js文件
    },
    output:{
        filename:'[name].js' //这里的name就是变量 他是你上面两个入口文件的名字 会自动执行
        path:path.resolve(__dirname,'dist')
    },
    //因为是两个页面 所以就需要new两次 
    plugin:[
        new HtmlWebpackPlugin({
            template:'./index.html'
            filename:'home.html'
            //引入哪个js文件
            chunks:['home']
        })
    ],
     devtool:'source-map' //把不打包的源码映射到内存中 方便查看错误 会单独生成一个很大的文件
	 devtool:'eval-source-map' //也会映射 但是不会单独生成一个打文件
}
```

## 14.webpack跨域

```js
1.我们前端的服务器代码 和我们后端的服务器代码肯定不一样 所以就会存在跨域
2.所以我们需要配置代理 在devServer 也就是我们的webpack-devserver的插件中去配置
let path = require('path')
let HtmlWebpackPlugin = require('HtmlWebpackPlugin')
module.exports = {
    mode:'development',
    devServer:{
        //这个意思就是凡是你以api(前提是后端给你写的接口是以api开头)开头的请求路径 就去这个端口请求
        proxy:{
            '/api':'http://localhost:3000'
        }
    }
    entry:{
        home:'./src/index.js' //主页的入口js文件
    },
    output:{
        filename:'[name].js' //这里的name就是变量 他是你上面两个入口文件的名字 会自动执行
        path:path.resolve(__dirname,'dist')
    },
    //因为是两个页面 所以就需要new两次 
    plugin:[
        new HtmlWebpackPlugin({
            template:'./index.html'
            filename:'home.html'
            //引入哪个js文件
            chunks:['home']
        })
    ],
	devtool:'eval-source-map' //也会映射 但是不会单独生成一个打文件
}
```

```JS
1.但是我们后端的接口可能是好几个人写的 所以不可能都给你写成api开头 这个时候就需要我们前端自己写 
2.前端在请求接口的时候都写成以api开头 然后再配置的时候 再把api去掉
let path = require('path')
let HtmlWebpackPlugin = require('HtmlWebpackPlugin')
module.exports = {
    mode:'development',
    devServer:{
        //这个意思就是凡是你以api(前提是后端给你写的接口是以api开头)开头的请求路径 就去这个端口请求
        proxy:{
            //就是你前端请求的是以api开头的路径  然后去目标路径那去找 然后再把api去掉
            '/api':{
                target:'http://localhost:3000' //最后请求的路径
            	pathRewrite:{'/api':''}
            }
        }
    }
    entry:{
        home:'./src/index.js' //主页的入口js文件
    },
    output:{
        filename:'[name].js' //这里的name就是变量 他是你上面两个入口文件的名字 会自动执行
        path:path.resolve(__dirname,'dist')
    },
    //因为是两个页面 所以就需要new两次 
    plugin:[
        new HtmlWebpackPlugin({
            template:'./index.html'
            filename:'home.html'
            //引入哪个js文件
            chunks:['home']
        })
    ],
	devtool:'eval-source-map' //也会映射 但是不会单独生成一个打文件
}
```

## 15.resolve属性的配置

```JS
1.我们的的模块 是common.js规范的 再查找第三方包的时候 都去node_modules的文件去找json文件 然后找到main属性 如果当前路径下没有 就接着向上查找
2.这里就可以通过配置 resolve属性 去改变他的查找的机制

resolve:{
    //这里配置的意思就是指再当前目录下的node_modules里去找
    modules:[path.resolve('node_modules')]
    //这个选项就是我们再import 的时候 可以不用写文件的后缀
    extensions: ['.js', '.vue', '.json'],
}
```

## 16.定义环境变量

```JS
1.有的时候我们需要执行不同的webpack配置文件 比如你开发环境的时候访问后台的接口是一个地址 打包之后访问的接口又是另外一个地址
2.webpack自带的插件 叫webpack.DefindePlugin() 可以给我们的去插入一个全局的变量 我们就可以通过判断这个变量的值 来让他跑什么环境
plugin:[
    new webpack.DefindePlugin({
        //这里就是再全局插入了一个变量
        DEV:'JSON.stringify('dev')'
        BASE_URL:'http://localhost:3000'
    })
]
//通过判断这个变量来改变请求的路径
let url = ''
if(DEV === dev){
    url = 'http://localhost:3000'
} else {
    url = 'www.houtai.8080'
}
```

## 17.区分不同环境

```JS
1.通过以上我们就可以区分不同的环境从而做到不同的配置 但是我们首先要配置一个公共的配置文件 不管是何种环境下 这些公共配置文件都要用到的
2.通过webpack-merge 模块来合并公共的配置文件

1-开发配置文件 webpack.dev.js
let {smart} = require('webpack-merge') //加载
//引入公共配置文件
let base = require('./webpack.base.js')
//配置一下开发环境的配置 比如可以devtool 调试之类的选项 显示合并公共文件的配置 然后再配置自己的
modele.exports = smart(base,{
    mode:'deveploment',
    devtool:'srouse-map',
    plugin:[
        new webpack.DefindePlugin({
        DEV:'JSON.stringify('dev')'
        BASE_URL:'http://localhost:3000'
    }) 
    ]
})
2-生产配置文件 webpack.pro.js //同上面一样
3-这个时候我们就可以再json文件里写脚本了 
"scripts":{
    "build":"webpack --config webpack.pro.js"
    "dev":"webpack --config webpack.dev.js"
  },
```

## 18.优化

```JS
1.noParse 我们webpack再打包的时候 需要解析主包 和别的包之间的依赖关系 但是我们有的包只有自己并没有依赖别的包 比如jquery 这个时候就需要用到noParse
module:{
    noParse:/jquery/, //不去解析jquery的依赖关系
    rules:[
        {}
    ]
}
2.IgnorPlugin  是webpack的一个内置插件 比如我们再引入包的时候 包和包之间会有依赖关系 有的时候有些依赖的包关系我们是用不到的 所以不需要他去打包 这个时候就需要用到IgnorPlugin 比如moment包 她有多语言设置 但我是们再打包的时候 会把这些多语言都打包进去 这个时候我们就需要用中文 而其他的不用 所以这个时候就要去用IgnorPlugin
let webpack = require('webpack') //先引入webpack
plugins:[
    //第一个参数是你包和包依赖的路径 第二个参数 是你要解除的依赖的入口包是哪个 都是正则表达式
    new webpack.IgnorPlugin(/\.\locale/,/moment/)
]
```

## 19.dllPlugin 动态链接库

```JS
1.动态链接库的作用 比如我们的第三方模块 我们是不会更改的他原本的代码 比如我们的vue 但是我们每次打包的时候 这些都会再被重新打包 
2.这个时候我们就需要先将这些我们不更改的第三包先去打包 打包会生成一个动态链接库 然后我们在打包我们更改的代码的时候 如果涉及到我们这些已经打包完毕的第三方包 我们就去加载这个动态链接库
3.这个配置文件执行完毕之后会生成两个文件 一个是打包好的js文件 一个是manifest.json文件(动态链接清单)
我们最后在打包别的文件的时候引入的就是这个manifest.json文件(动态链接清单)
4.我们需要在html文件 script标签 先引入一下我们打包好的文件
```

```JS
//以下是我们需要先打包的第三方模块 或者 需要生成动态链接清单的第三方包
const webpack = require("webpack");
const path = require('path');
const {CleanWebpackPlugin} = require("clean-webpack-plugin");
const dllPath = path.resolve(__dirname, "../static/cdn/dll"); // dll文件存放的目录

module.exports = {
  entry: {
    // 把 vue 相关模块的放到一个单独的动态链接库
    vendor: ["babel-polyfill", "vue", "vue-router", "vuex", "axios", "element-ui", "@smallwei/avue"]
  },
  output: {
    // 文件名称
    filename: '[name].dll.js',
    path: dllPath,
    /*
     存放相关的dll文件的全局变量名称，比如对于jquery来说的话就是 _dll_jquery, 在前面加 _dll
     是为了防止全局变量冲突。
    */
    library: '[name]_library',
    libraryTarget: 'umd'
  },
  plugins: [
    new CleanWebpackPlugin(),
    new webpack.DllPlugin({
      name: '[name]_library',
      // manifest.json 描述动态链接库包含了哪些内容
      path: path.join(__dirname, "./", "[name].dll.manifest.json")
    }),
  ],
};
```

```JS
//上面的代码已经生成了动态链接清单 下一步我们在打包我们的别的代码的时候就需要引入这个manifest.json文件(动态链接清单) 下面是配置
let webpack = require('webpack')
plugins:[
    new webpack.DllRefencePlugin({
        manifest:path.resolve(__dirname,'dist','manifest.json')
    })
]
```

## 20.多线程打包 happypack

```JS
1.主要应用 happypack
2.比如我们以前在打包js文件的时候用的babel相关的loader 这个时候我们要把打包JS的loader换成happypack 让happypack去多线程执行打包js的一些laoder 比如babel
3.happypack的use 必须是个数组
4.如果处理别的loader也需要多线程 比如处理css的loader 那么就要重新new Happypack一下 写法跟下面一样
```

```JS
let path = require('path')
//因为这里大写了 所以这是个构造函数也是es里的类 所以我们需要new出来
let HtmlWebpackPlugin = require('html-webpack-plugin')
let HappyPack = require('HappyPack')
module.exports = {
    mode:'development'
    extry:'./src/index.js'
    output:{
    filename:'bundle.js'
    path:path.resolve(__dirname,'build') 
	}
    module:{
        rules:[
            {
             test:'/\.js$/' 
             //改为用happypack去执行babel的loader 要给一个ID 
             use:'Happypack/laoder?id=js'
            },
            {
               test:'/\.css$/',
               use:'Happypack/laoder?id=css'
            }
        ]
    },
    plguins:[
        new Happypack({
            id:'css',
            use:[
                loader:['style-loader','css-loader']
            ]
        })
        //这里加载happypack 去执行你上面写好ID的那个loader
        new Happypack({
            id:'js',
            use:[
             	loader:'babel-loader',
             	options:{
             		presets:['@babel/preset-env'],
            		plugins:['@babel/plugin-proposal-class-properties','@balbel/plugin-transform-runtime'
            }
            }
            }, 
        ]
            ]
        })
        new HtmlWebpackPlugin({
            template:'./src/index.html'//你模板的路径
            filename:'index.html'//你希望再内存路径中访问的文件地址
            minify:{
            	removeAttributeQuotes:true,
         		collapseWhitespace:true
        }
        })
    ]
}
```

## 21.webpack自带优化

```JS
1.比如我们在一个js文件里写了若干个方法 然后再另外一个js文件里用import语法引用    而且只使用了其中的一个方法 而其他的方法没有用到 这个时候你再打包的时候 webpack会自动指帮你打包你使用的方法而没用的代码自动删除 叫tree-shaking(树摇摆) 注意 必须是import语法 (再生产环境下才会生效)
2.我们再使用require导入模块的时候 会自动把我们导出的方法挂在到default上面
```

## 22.抽取公共代码

```JS
1.针对于多页面打包
2.比如我们有两个入口文件需要打包 但是这两个文件都引入了相同的模块 这个时候我们就需要把相同的公共模块抽离出来打包
```

```JS
module.exports = {
    optimization:{
        //分割代码块配置
        splitChunks:{
            //缓存组
            cacheGroups:{
                common:{
                    chunks:'initial',
                    //超过多少大小的需要缓存
                    minSize:0,
                    //被使用了多少次的需要抽离
                    minChunks:2
                }
            }
        }
    }
}
```

## 23.懒加载

```JS
1.当我们点击某个按钮或者开关的时候才去加载一段js代码
2.原理 就是通过import 语法 回调是一个promise
3.需要安装对应的plugin才可以实现
import('./a.js').then(data => {
    console.log(data.default)
})
```



