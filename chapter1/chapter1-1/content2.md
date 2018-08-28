# 应用背景
&emsp;&emsp;为了能灵活嵌入以native view为主的APP应用，并能接入定制的APP接口，且业务代码能够较快速的转移保留功能的完整性。ionic框架结合原生接入要求做了相应调整，以便适应此开发场景，能够快速开发。  
&emsp;&emsp;在这里以一个已实施的ionic项目开始讲解 

### 开发准备 
#### 1. 工程文件整理
1. 在原有项目目录清理后保留如下：
```
+-- scss/                                   --- 样式原文件
+-- www/                                    --- 开发主目录
|   +-- css/                                --- 根据样式原文件生成的样式文件 
|   +-- dist/                               --- 打包发布的文件目录
|   +-- img/                                --- 开发资源图片目录
|   +-- js/                                 --- 开发js代码目录
|   +-- lib/                                --- 第三方依赖库
|   +-- template/                           --- 模版文件
|   --- index.html                          --- 主入口
+-- node_modules/                           --- npm下载文件目录，清空稍后会重新安装
--- .gitignore                              --- git文件过滤配置  
--- package.json                            --- 配置文件    
--- gulpfile.json                           --- 工程化配置   
--- ionic.project                           --- 工程化配置  
--- README.md                               --- 说明文档                                       
```

2. 设置git版本管理的过滤文件.gitignore  
```
# Specifies intentionally untracked files to ignore when using Git
# http://git-scm.com/docs/gitignore
node_modules/
hooks/
resources/
platforms/
plugins/
www/dist/
.idea/
.DS_store
config.xml
```

#### 2. 工程重构

1. package.json修改工具包依赖配置
```json
{
"name": "zxscf",
"version": "1.1.1",
"description": "An Ionic project",
"devDependencies": {
"gulp": "^3.5.6",
"gulp-angular-templatecache": "^2.2.1",
"gulp-clean": "^0.4.0",
"gulp-clean-css": "^3.7.0",
"gulp-concat": "^2.6.1",
"gulp-jshint": "^2.1.0",
"gulp-ng-annotate": "^2.1.0",
"gulp-notify": "^3.2.0",
"gulp-rename": "^1.4.0",
"gulp-sass": "^3.1.0",
"gulp-uglify": "^3.0.1",
"gulp-useref": "^3.1.5",
"jshint": "^2.9.6"
},
"cordovaPlugins": [],
"cordovaPlatforms": [],
"dependencies": {}
}
```
2. 工程化处理配置gulpfile.js  
```javascript  
var gulp = require('gulp');
var sass = require('gulp-sass');
var cleanCss = require('gulp-clean-css');
var rename = require('gulp-rename');          //文件重命名
var useref=require('gulp-useref');            //合并文件
var uglify=require('gulp-uglify');            //js压缩
var jshint=require('gulp-jshint');            //js检查
var clean =require('gulp-clean');             //文件清理
var notify=require('gulp-notify');            //提示
var templateCache = require('gulp-angular-templatecache');
var ngAnnotate = require('gulp-ng-annotate');
var paths = {
  sass: ['./scss/**/*.scss'],
  templatecache: ['./www/template/**/*.html'],
  ng_annotate: ['./www/js/**/*.js'],
  useref: ['./www/*.html']
};
gulp.task('sass', function(done) {
  gulp.src('./scss/ionic.app.scss')
    .pipe(sass())
    .on('error', sass.logError)
    .pipe(gulp.dest('./www/css/'))
    .pipe(cleanCss({
      keepSpecialComments: 0
    }))
    .pipe(rename({ extname: '.min.css' }))
    .pipe(gulp.dest('./www/css/'))
    .on('end', done);
});
gulp.task('jslint', function() {
  return gulp.src('./www/js/**/*.js')
    .pipe(jshint())
    .pipe(jshint.reporter('default'));
});
// 合并html到js
gulp.task('templatecache', function (done) {
  gulp.src('./www/template/**/*.html')
    .pipe(templateCache({standalone:true}))
    .pipe(gulp.dest('./www/js'))
    .on('end', done);
});
// 补足语法规则，文件重新定位文件
gulp.task('ng_annotate',['jslint'], function (done) {
  gulp.src('./www/js/**/*.js')
    .pipe(ngAnnotate({single_quotes: true}))
    .pipe(gulp.dest('./www/dist/dist_js/app'))
    .on('end', done);
});
// 合并文件到dist目录
gulp.task('useref',["ng_annotate"], function (done) {
  gulp.src('./www/*.html')
    .pipe(useref())
    .pipe(gulp.dest('./www/dist'))
    .on('end', done);
});
// JS处理
gulp.task('minifyjs',["useref"],function(){
  return gulp.src(['./www/dist/dist_js/app.js'])
    .pipe(uglify())                               // 压缩
    .pipe(gulp.dest('./www/dist/dist_js/'))       // 输出
    .pipe(notify({message:"Build OK"}));        // 提示
});
gulp.task('clean',["minifyjs"], function (done) {
  gulp.src(['./www/dist/dist_js/app'])
    .pipe(clean());
}); 
gulp.task('release',function() {  gulp.src(['./www/**/*.*','!./www/template/**','!./www/js/**','!./www/css/**','!./www/dist/**','!./www/index.html'])
    .pipe(gulp.dest('./www/dist/'));
});
gulp.task('watch',["sass","templatecache","ng_annotate"], function() {
  gulp.watch(paths.sass, ['sass']);
  gulp.watch(paths.templatecache, ['templatecache']);
  gulp.watch(paths.ng_annotate, ['ng_annotate']);
});
gulp.task('default', ["watch"]);
gulp.task('build', ['jslint','clean','release']);
```
3. www/index.html 文件设置
```html
<!-- 头部设置 -->
 <head>
    <meta charset="utf-8">
    <meta name="viewport" content="initial-scale=1, maximum-scale=1, user-scalable=no, width=device-width">
    <title>睿E链金融</title>
    <!-- build:css dist_css/styles.css -->
    <link href="css/ionic.app.min.css" rel="stylesheet">
    <!-- endbuild -->
    <script src="lib/ionic/js/ionic.bundle.js"></script>
    <script src="https://cdn.polyfill.io/v2/polyfill.min.js?features=Intl.~locale.en"></script>
    <!-- build:js dist_js/app.js -->
    <script src="dist/dist_js/app/app.js"></script>
    <script src="dist/dist_js/app/config.js"></script>
    <script src="dist/dist_js/app/widget.js"></script>
    <script src="dist/dist_js/app/controllers.js"></script>
    <script src="dist/dist_js/app/ngIOS9UIWebViewPatch.js"></script>
    <script src="dist/dist_js/app/controllers/homeCtrls.js"></script>
    <script src="dist/dist_js/app/controllers/draftDisctCtrls.js"></script>
    <script src="dist/dist_js/app/controllers/draftTxnCtrls.js"></script>
    <script src="dist/dist_js/app/controllers/mineCtrls.js"></script>
    <script src="dist/dist_js/app/controllers/homeCtrlBus.js"></script>
    <script src="dist/dist_js/app/services.js"></script>
    <script src="dist/dist_js/app/services/mineService.js"></script>
    <script src="dist/dist_js/app/templates.js"></script>
    <!-- endbuild -->
  </head>
```
4. 工程构建  
在工程根目录下重构工程  
```
npm instsll
```
设置ionic默认启动项ionic.project  
```json
  "gulpStartupTasks": [
       "default"
   ]
```
5. 命令  
开发
```
ionic serve
```
发布 放入到原生APP指定的访问目录
```
gulp build
```

### 开发原文件功能介绍
```
+-- js/                                     --- 开发js代码目录
|   +-- controllers/                        --- 业务展示逻辑控制层目录
|   +-- services/                           --- 业务接口服务层目录
|   --- app.js                              --- 页面跳转路由配置
|   --- config.js                           --- 项目基础配置
|   --- controllers.js                      --- 全局控制初始化设置
|   --- services.js                         --- 全局服务包含统一请求处理和小工具
|   --- widget.js                           --- 自定义标签或者属性小工具
```

### WebViewJavascriptBridge开发
定义和初始化WebViewJavascriptBridge，在js/controllers.js的InitCtrl中设置
```javascript
  // 创建原生链接初始化方法
    window.setupWebViewJavascriptBridge = function(callback) {
      if (window.WebViewJavascriptBridge) { return callback(WebViewJavascriptBridge); }
      if (window.WVJBCallbacks) { return window.WVJBCallbacks.push(callback); }
      window.WVJBCallbacks = [callback];
      var WVJBIframe = document.createElement('iframe');
      WVJBIframe.style.display = 'none';
      WVJBIframe.src = 'https://__bridge_loaded__';
      document.documentElement.appendChild(WVJBIframe);
      setTimeout(function() { document.documentElement.removeChild(WVJBIframe); }, 0);
      //兼容Android端
      document.addEventListener(
        'WebViewJavascriptBridgeReady',
        function() {
            callback(WebViewJavascriptBridge);
        },
        false
      );
    };
    
       // 应用初始载入时调用一次
    $scope.$on('$ionicView.loaded',function(){
      // 初始化原生接入
      if(!window.bridge){
        window.setupWebViewJavascriptBridge(function(bridge) {
          window.bridge = bridge;
          //初始化,Android用到
          if(bridge.init && typeof(bridge.init)=="function") {
            console.log("call bridge.init");
            bridge.init(function(message, responseCallback) {
              console.log("call bridge.init finished");
              var data = {
                'Javascript Responds': 'Wee!'
              };
              responseCallback(data);
            });
          }
          //------注册供原生调用的方方--------
          //android返回处理,处理页面跳转或否则处理退出app
          bridge.registerHandler('androidBack', function(data, responseCallback) {
            if($location.path() == '/tab/home' || $location.path() == '/tab/draftDisctOn' || $location.path() == '/tab/draftTxnOn' || $location.path() == '/tab/mine'){
              responseCallback(true);
            }else{
              responseCallback(false);
              $ionicHistory.goBack(-1);
            }
          });
          bridge.registerHandler('',function(data,callback){
            console.log(data);
            callback('收到++');
          });

          // 设置需要的初始项
          initData();
        });
      }
    });
```

#### 1. 初始化WebViewJavascriptBridge
#### 2. 请求模块改造
#### 3. 业务对象字段调整

### 编写规范
