# xwkd

### 最下方增加版本更新日志
> 创建于8.10号
> 路由及项目结构配置8.12号

### 依赖模块
<span style="color: rgb(184,49,47); padding-left:40px;">项目是用webpack自定义创建的</span>
#### 项目需要安装node.js(https://nodejs.org/en/download/)
#### 具体依赖如下模块
- [vue@2.5.17](https://cn.vuejs.org/)
- [vue-router@3.0.1](https://router.vuejs.org/)
- [element-ui@2.4.5](http://element-cn.eleme.io/#/zh-CN)
- [mint-ui@2.2.13](http://mint-ui.github.io/#!/zh-cn)

### 调试方式
* 1、打开chrome，地址输入chrome://inspect/#devices
* 2、链接安卓手机，打开开发者模式，允许运行手机调试，打开开发环境地址
* 3、打开页面下方chrome调试页面，在调试页面按ctrl -+ 进行正确页面比例缩放
* 4、浏览断点输出，http请求状态

- 首页

### 代码目录
```js
+-- dist(根据项目名称具体配置)/             ---打包的文件目录
|   +-- css/                                ---打包后的样式目录 
|   +-- images/                             ---打包后的图片目录
|   +-- js/                                 ---打包后的js代码目录
|   --- index.html                          ---打包后的文件主入口
+-- config/                                 ---npm run 的配置文件目录
+-- node_modules/                           ---npm下载文件目录
+-- src/                                    ---核心代码目录
|   +-- assets/                             ---样式表,静态文件目录
|   +-- constants/                          ---常量代码目录
|   +-- component/                          ---组件代码目录
|   +-- utils/                              ---工具类代码目录		
|   +-- images/                             ---图片文件目录
|   +-- router/                             ---路由代码目录
|   +-- views/                              ---vue分页面代码目录
|   +-- services/                           ---http请求代码目录
|   --- App.vue      	                    ---vue主入口代码
|   --- index.html                          ---页面入口
|   --- main.js                             ---主页面引用的js文件
+-- static/                                 ---静态文件目录
--- .babelrc                                ---es6语法配置
--- pottcss.config.js                       ---css3属性自动添加配置 
--- package.json                            ---配置文件    
--- README.md                               ---说明文档                                       
```
### 安装运行
##### 1.下载或克隆项目源码,全局安装npm install webpack webpack-cli webpack-dev-server -g
##### 2.npm安装相关包文件
```js
npm i (等于npm install)
```
##### 3.启动项目
```js
npm start
```
##### 4.打包项目
```js
npm run build
```
### 功能模块
<!--more-->
> components下各类型组件，例头部header，下一步按钮nextButton
> constants下各页面常量
> router下单页面路由模块配置
> utils下各类方法汇总,例reg下各正则匹配方法

### webpack项目构建方法
* 1、命令行或终端进入项目文件夹,输入npm init 填写关于package.json等一些相关信息，也可以enter跳过
* 2、npm install --save-dev 或 npm install --save 安装项目所需的webpack等依赖
* 3、mkdir config文件夹,创建webpack.dev.js  webpack文件配置
* 4、配置webpack具体的文件依赖，例如引入html-webpack-plugin(html打包)、uglifyjs-webpack-plugin(js压缩)、extract-text-webpack-plugin(css分离)，babel(es6代码格式转换)配置出口，入口文件，服务器配置等。
* 5、mkdir src 创建生产代码结构。

### 开始
* 1、在开发环境目录src下创建index.html，开发模式指定这个文件路径。
* 2、创建main.js文件，在webpack中配置main.js的引入。通过import引入项目依赖，例如vue、vueRouter、elementUI。引入主模块App.vue,因为配置的路由router模块。
* 3、创建App.vue主路由，写主路由router-view入口。
* 4、创建router下的index.js路由配置代码，引入vue、vueRouter、App.vue及各子模块。创建vueRouter对象，书写各模块路由。
* 5、mkdir views书写各分路由模块。

### 更新日志

