# ionic案例背景
此案例基于已实施的ionic项目为起始讲解

### 开发应用目录
在原有项目目录清理后保留如下：
```
+-- www(开发主目录)/                      ---打包的文件目录
|   +-- css/                                --- 打包后的样式目录 
|   +-- dist/                               --- 打包的文件目录 发布目录
|   +-- img/                                --- 打包后的图片目录
|   +-- js/                                 --- 打包后的js代码目录
|   +-- lib/                                --- 第三方依赖库
|   +-- template/                           --- 模版文件
|   --- index.html                          --- 主入口
+-- node_modules/                           --- npm下载文件目录
--- .gitignore                              --- git文件过滤配置  
--- package.json                            --- 配置文件    
--- gulpfile.json                           --- 工程化配置    
--- README.md                               --- 说明文档                                       
```

设置git版本管理的过滤文件.gitignore
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

## 