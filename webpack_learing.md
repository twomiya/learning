# webpack learning
## 1,安装webpack
### (安装前确保已经安装node)
##### 本地安装
`npm install --save-dev webpack`<br>
`npm install --save-dev webpack@<version>` 
##### 全局安装
`npm install --global webpack`
###### (不推荐全局安装 webpack。这会将你项目中的 webpack 锁定到指定版本，并且在使用不同的 webpack 版本的项目中，可能会导致构建失败。)
## 2,对项目目录进行安装
`npm init` <br>
`npm install --save-dev webpack`

#### 查看webpack版本
`webpack -v`

## 3,webpack 打包
`webpack src/entry.js dist/bundle.js`

## 4，配置文件
```
 const path = require('path');  
 module.exports={
    //入口文件的配置项
    entry:{
        entry:'./src/entry.js'
    },
    //出口文件的配置项
    output:{
        //输出的路径，用了Node语法
        path:path.resolve(__dirname,'dist'),
        //输出的文件名称
        filename:'bundle.js'
    },
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}
```
##### 多入口、多出口配置
```
const path = require('path');
module.exports={
    //入口文件的配置项
    entry:{
        entry:'./src/entry.js',
        //这里我们又引入了一个入口文件
        entry2:'./src/entry2.js'
    },
    //出口文件的配置项
    output:{
        //输出的路径，用了Node语法
        path:path.resolve(__dirname,'dist'),
        //输出的文件名称
        filename:'[name].js'
    },
    //模块：例如解读CSS,图片如何转换，压缩
    module:{},
    //插件，用于生产模版和各项功能
    plugins:[],
    //配置webpack开发服务功能
    devServer:{}
}
```
### 5,配置文件： 服务和热更新
##### 安装设置webpack-dev-server
`npm install webpack-dev-server --save-dev`
### 6,css打包
##### 安装style-loader
`npm install style-loader --save-dev`
##### 安装css-loader
`npm install --save-dev css-loader`
##### webpack.config.js
```
   module:{
        rules: [
            {
              test: /\.css$/,
              use: [ 'style-loader', 'css-loader' ]
            }
          ]
    },
```
### 7,插件配置：js压缩
##### 在webpack.config.js中引入uglifyjs-webpack-glugin插件
`const uglify = require('uglifyjs-webpack-plugin');`
##### 在plugins配置里new一个 uglify对象
``` 
plugins:[
        new uglify()
    ],
```
### 8,插件配置：HTML文件的发布
##### 配置webpack.config.js文件，先引入我们的html-webpack-plugin插件。
`const htmlPlugin= require('html-webpack-plugin');`
##### 在webpack.config.js里的plugins里进行插件配置
```
new htmlPlugin({
    minify:{
        removeAttributeQuotes:true
    },
    hash:true,
    template:'./src/index.html'
   
})
```
### 9,CSS中的图片处理
##### 安装file-loader和url-loader
`npm install --save-dev file-loader url-loader`
### 10,CSS分离与图片路径处理
##### CSS分离:extract-text-webpack-plugin
`npm install --save-dev extract-text-webpack-plugin`
##### 引入：安装完成后，需要先用require引入
`const extractTextPlugin = require("extract-text-webpack-plugin");`
##### 设置Plugins：引入成功后需要在plugins属性中进行配置
`new extractTextPlugin("/css/index.css")`
##### 分离会导致路径问题，publicPath：是在webpack.config.js文件的output选项中，主要作用就是处理静态文件路径的。
##### 在处理前，我们在webpack.config.js 上方声明一个对象，叫website。
```
var website ={
    publicPath:"http://192.168.1.108:1717/"
}
```
##### 然后在output选项中引用这个对象的publicPath属性
```
output:{
    publicPath:website.publicPath
},
```
### 11,处理HTML中的图片
##### 安装html-withimg-loader
`npm install html-withimg-loader --save`
##### 配置loader
```
{
    test: /\.(htm|html)$/i,
    use:[ 'html-withimg-loader'] 
}
```
### 12,Less文件的打包和分离
##### 安装Less
`npm install --save-dev less`
##### 安装Less-loader用来打包
`npm install --save-dev less-loader`
##### 在webpack.config.js里编写loader配置
```
{
    test: /\.less$/,
    use: [{
           loader: "style-loader" // creates style nodes from JS strings
        }, {
            loader: "css-loader" // translates CSS into CommonJS
        , {
            loader: "less-loader" // compiles Less to CSS
        }]
}
```
### 13,Sass文件的打包和分离
##### 安装sass
`npm install --save-dev node-sass`
##### 安装sass-loader
`npm install --save-dev sass-loader`
##### loader配置
```
{
    test: /\.scss$/,
    use: [{
        loader: "style-loader" // creates style nodes from JS strings
    }, {
        loader: "css-loader" // translates CSS into CommonJS
    }, {
        loader: "sass-loader" // compiles Sass to CSS
    }]
}
```
##### SASS文件分离
```
{
            test: /\.scss$/,
            use: extractTextPlugin.extract({
                use: [{
                    loader: "css-loader"
                }, {
                    loader: "sass-loader"
                }],
                // use style-loader in development
                fallback: "style-loader"
            })
 }
```
### 14,自动处理CSS3属性前缀postcss-loader
#####安装两个包postcss-loader 和autoprefixer（自动添加前缀的插件）
`npm install --save-dev postcss-loader autoprefixer`
##### postCSS推荐在项目根目录（和webpack.config.js同级），建立一个postcss.config.js文件。
```
module.exports = {
    plugins: [
        require('autoprefixer')
    ]
}
```
##### 对postcss.config.js配置完成后，我们还需要编写我们的loader配置。
```
{
      test: /\.css$/,
      use: [
            {
              loader: "style-loader"
            }, {
              loader: "css-loader",
              options: {
                 modules: true
              }
            }, {
              loader: "postcss-loader"
            }
      ]
}
```
##### 配置提取CSS的loader配置.
```
{
    test: /\.css$/,
    use: extractTextPlugin.extract({
        fallback: 'style-loader',
        use: [
            { loader: 'css-loader', options: { importLoaders: 1 } },
            'postcss-loader'
        ]
    })
    
}
```
### 15,消除未使用的CSSPurifyCSS
##### 安装PurifyCSS-webpack
`npm i -D purifycss-webpack purify-css`
##### 引入glob
`const glob = require('glob');`
##### 引入purifycss-webpack
`const PurifyCSSPlugin = require("purifycss-webpack");`
##### 在webpack.config.js里配置plugins
```
new PurifyCSSPlugin({
	paths: glob.sync(path.join(__dirname, 'src/*.html')),
})
```
###### 注意：使用这个插件必须配合extract-text-webpack-plugin这个插件
### 16,给webpack增加babel支持
##### 安装依赖包
`npm install --save-dev babel-core babel-loader babel-preset-es2015 babel-preset-react`
##### 配置Babel
```
{
    test:/\.(jsx|js)$/,
    use:{
        loader:'babel-loader',
        options:{
            presets:[
                "es2015","react"
            ]
        }
    },
    exclude:/node_modules/
}
```
### 17,优雅打包第三方类库（jq为例子）
##### 先安装
`npm install --save jquery`
##### 用plugin引入
`const webpack = require('webpack')`
##### 配置plugins模块
```
plugins:[
    new webpack.ProvidePlugin({
        $:"jquery"
    })
],
```
### 18,watch的正确使用方法
```
watchOptions:{
    //检测修改的时间，以毫秒为单位
    poll:1000, 
    //防止重复保存而发生重复编译错误。这里设置的500是半秒内重复保存，不进行打包操作
    aggregateTimeout:500, 
    //不监听的目录
    ignored:/node_modules/, 
}
```
##### BannerPlugin插件
`new webpack.BannerPlugin('zxf版权所有，欢迎收看')`
###### 需要注意的是在使用这个插件之前必须引入webpack。
`const webpack = require('webpack');`
### 19,webpack优化
##### 抽离jquery
##### 抽离的第一步是修改入口文件，把我们的JQuery也加入到入口文件中
```
entry:{
        entry:'./src/entry.js',
        jquery:'jquery'
 },
```
##### 引入插件
```
new webpack.optimize.CommonsChunkPlugin({
    //name对应入口文件中的名字，我们起的是jQuery
    name:'jquery',
    //把文件打包到哪里，是一个路径
    filename:"assets/js/jquery.min.js",
    //最小打包的文件模块数，这里直接写2就好
    minChunks:2
}),
```
##### 多个第三方类库抽离
##### 先用npm 进行安装
`npm install vue --save`
#####在入口配置中引入vue和jquery
```
entry:{
    entry:'./src/entry.js',
    jquery:'jquery',
    vue:'vue'
},
```
##### 修改CommonsChunkPlugin配置
```
new webpack.optimize.CommonsChunkPlugin({
    //name对应入口文件中的名字，我们起的是jQuery
    name:['jquery','vue'],
    //把文件打包到哪里，是一个路径
    filename:"assets/js/[name].js",
    //最小打包的文件模块数，这里直接写2就好
    minChunks:2
}),
```
### 20,静态资源集中输出
##### 使用copy-webpack-plugin
`npm install --save-dev copy-webpack-plugin`
##### 引入插件
`const copyWebpackPlugin= require("copy-webpack-plugin")`
##### 配置插件
```
new copyWebpackPlugin([{
    from:__dirname+'/src/public',
    to:'./public'
}])
```
### 21,Json配置文件使用
#### 读出Json内容
##### 第一步：现在我们的index.html模板中加入一个层，并给层一个Id，为了是在javascript代码中可以方便引用。
`<div id="json"></div>`
##### 第二步：到src文件夹下，找到入口文件entry.js文件。修改里边的代码
```
var json =require('../config.json');
document.getElementById("json").innerHTML= json.name;
```
##### 第三步：启用我们的npm run start 命令就可以在浏览器中看到结果了。

##### 热更新
##### 其实在webpack3中启用热加载相当的容易，只要加入HotModuleReplacementPlugin这个插件就可以了。
`new webpack.HotModuleReplacementPlugin()`
