## 全栈工程师

- 前端开发
  - 从HTML CSS JavaScript jQuery 特效 前端优化 自动化工具和框架
- 后端开发
  - linux，nodejs，后端构建restful，api服务，常用的框架和库异步服务的构建
- 移动端开发
  - 前端开发：html5，css3，bootstrap，**angular**，**promise**，**websocket**，require.js，sea.js
  - 后端开发：node.js，**express，socket.io，restful，http**
  - 数据库：***mongode***，*redis，mysql*（三大数据库），orm，waterline
  - 移动客户端：ionic，**react**，react-native，gdp，定位
  - 微信开发：oauth，api设计，支付，摇一摇等
  - 运维部署：linux，naqios，**nqinx**

JavaScript已经由一门浏览器端脚本语言成为了 **全栈** 语言

## 了解Node

- 什么是node.js
  - 是JavaScript语言的服务器运行环境
  - Node提供大量的工具库，使得JavaScript可以调用操作系统级别的API，调用**底层命令**
  - 内部采用Google公司的V8引擎，作为JavaScript语言解释器，速度非常快
    - chrome按F12，调出Console界面，敲入>console.log('hello');解析
  - Node.js是一个基于 **事件驱动** 和 **异步I/O**（不等待） 的服务端JavaScript环境
- 如何学习node.js
  - npm官网：https://www.npmjs.com
    - 项目管理：**npm，grunt,gulp,bower,yeoman**
    - 桌面应用：**node-wbkit**
    - Web开发：**express,ejs**,hexo,**socket.io**,restify,**nodeppt**,stylus,**browserify,cheerio**
    - 数据库：**mysql,mongoose,redis**,memcached
    - 工具包：underscore,**moment,connect**,later,**log4js**,passport(oAth),requ
    - 异步：**async**,wind,eventProxy,**bluebird**
    - 部署：forever，**pm2，nodemon**(监控node应用)
    - 测试：**jasmine，karma,protractor**
    - 跨平台：rio,tty
    - 内核：**cluster**
    - 模板：**jade,ejs**
    - 博客：ghost,hexo
    - 微信：weui
    - 硬件控制：NoduinoWeb
    - 操作系统：
  - node官网：https://nodejs.org/en/
  - github
  - stackoverflow：https://stackoverflow.com/
  - 开发IDE:WebStrom
- node.js快速上手
  - 第一个node程序
    - 编写一个文件：console.log(‘Hello，Nodejs’)；
    - 将文件保存为hello.js
    - 命令行下执行：node hello.js
  - REPL(Read-eval-print loop "读取-求值-输出" 循环)：跟Chrome浏览器F12的Console控制台相似
    - node + 回车
    - eval（1+2）
    - var name='zfpx';
    - age=10;
    - global,window(浏览器中)全局变量
    - _+10 下划线代表最后一个变量
    - 设置函数
      -  var p={name:'sdf',age:6}
      -  p.getname = function(){return p.name;}
      - 调用函数
        - p.getname()
    - 命令
      - .break
      - .clear
      - .editor
      - .exit
      - .load
      - .save
      - .help
  - 异步、事件驱动模型、非I/O阻塞等特性
  - 模块和包
  - 全局对象global
  - fs文件模块

# http

### 模块

- 一个.js文件就是一个模块

- File-Settings-Node.js and NPM中配置一下代码提示

  - Node.js Core library is enabled.
  - usage scope 配置文件夹的Library里的**Node.js Core**、HTML、HTML5

- 向其它.js文件暴露本模块的变量

  ```
  var name='zfpx';
  var age=40;
  
  exports.name=name;
  ```

- 其它.js文件通过requier引用这个模块，并赋值给一个变量，通过这个变量就可以访问被暴露的变量

  ```
  var person=require('./person');
  console.log(person.name);
  ```

四步：

- 创建模块

  ```
  math.js
  ```

- 导出模块(暴露模块内容)

  ```
  exports.add=function(a,b){return a+b;}
  ```

- 加载模块（在另一个模块中）

  ```
  var math=require('./math');
  ```

- 调用模块（在另一个模块中）

  ```
  var sum = math.add(1,2);
  ```

### 模块的分类

- 核心模块

  ```
  http,fs,path
  ```

  文件模块:自己写的

- 第三方模块：在网站上下载的

  ```
  var async = require('async');
  ```

### 包和npm

- 多个模块（多个.js文件）可以封装成一个***包***

- npm是node.js默认的模块管理器，用来安装和管理node模块 网址为

  > http://npmjs.org

- 可以以包的方式通过npm安装、卸载、发布包

### 如何初始化一个项目

