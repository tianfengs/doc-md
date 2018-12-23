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

- 文件模块:自己写的

- 第三方模块：在网站上下载的

  ```
  var async = require('async');
  ```

### 包和npm

- 多个模块（多个.js文件）可以封装成一个***包***

- npm是node.js默认的模块管理器，用来安装和管理node模块 网址为

  > http://npmjs.org

- 可以以包的方式通过npm**安装、卸载、发布包**

### 如何初始化一个项目

- mkdir studynode		创建一个目录

- cd studynode                进入目录

- npm init                         初始化项目描述文件

  {

  "name":"包的名字，必须唯一，由小写英文字母、数字和下划线组成，不能有空格"

  "description"：包的简要说明

  "version"：符合语义化八本识别规范的版本字符串

  "keywords"：关键字数组，通常用于在npm网站上搜索

  "contributors"：贡献者数组，格式与maintainers相同，包的作者应该是贡献者数组第一个元素

  "bugs"：提交bug的地址，可以是网址或电子邮件地址

  "licenses"：许可证数组，每个元素要包含type（许可证名称）和url（链接到许可证文本的地址）字段

  "repositories"：仓库托管地址数组，每个元素要包含type（许可证名称）和url（链接到许可证文本的地址）字段

  "dependencies"：包的依赖，一个关联数组，由名称和版本组成

  }

### npm install（安装第三方模块）

- **全局安装** 直接下载到Node的安装目录中，**各个项目都可以调用**，适合**工具模块**，比如**gulp**

  ```
  npm install -global [package name]
  ```

- 本地安装 将一个模块下载到**当前目录的node_modules子目录**，只有在当前目录和子目录中，才能使用

  ```
  npm install [package name]
  ```

### 重要对象

- 全局对象global
  *global*在Node中，类似于浏览器中的*window*对象

- 控制台对象console

  - 向标准输出流打印字符并以换行符介绍

    ```
    console.log([data]\[, ...])
    ```

  - 返回信息性消息

    ```
    console.info([data]\[,...])
    ```

  - 输出红色错误消息

    ```
    console.error([data]\[,...])
    ```

  - 输出警告消息

    ```
    console.warn([data]\[,...])
    ```

  - 输出时间，表示计时开始结束

    ```
    console.time(label)
    console.timeEnd(label)
    ```

- 文件模块fs

  - readFileSync方法用于同步读取文件并返回一个字符串

    ```
    var text = fs.readFileSync(filename, "utf8");
    ```

  - readFile方法用于异步读取文件

    ```
    fs.readFile(fileName, "utf8", function(err,text){});
    ```

### HTTP

- 服务器：在特定IP，特定端口监听客户端的请求，并返回相应的结果

- 客户端

- 数据在Server和Client之间传递

- 交互：要指明一种协议（http或https）和一种 数据封装格式 （HTML/JSON）

  - 请求包括
    - 方法：get，post，patch等
    - URL
    - 协议和版本 HTTP/1.1
    - 请求首部字段
  - 相应包括
    - 协议版本
    - 状态码
    - 响应头、请求头
    - type
    - 响应体

- 一个普通网站访问的过程（WebStorm编辑的nodejs项目

  - 浏览器发出一个HTTP请求
  - 先把域名解析为IP地址（chrome缓存1分钟（chrome://net-internals/#dns) -> 搜索操作系统缓存->读取本地host文件->发起本地DNS系统调用->运营商DNS缓存->找根域->com域
  - 客户端通过随机端口向服务器发起TCP三次握手，建立TCP连接
  - 建立后浏览器就可以发送HTTP请求
  - 服务器接收到HTTP请求，解析请求的路径和参数，经过后台的一些处理之后 生成完整响应页面
  - 服务器将生成的页面作为HTTP响应体，根据不同的处理结果生成响应头，发回给客户端
  - 客户端（浏览器）接收到HTTP响应，从请求中得到的HTTP响应体力是HTML代码，**于是对HTML代码开始解析**
  - 解析过程中遇到**引用的服务器上的资源**（额外的CSS、JS代码，图片、音视频，附件等），再向服务器发送请求
  - 浏览器解析HTML包含的内容，用得到的**CSS代码**进行**外观**上的进一步**渲染**，**JS代码**也可能会对外观进行一定的**处理**
  - 当用户**与页面交互**（点击、悬停等）时，JS代码对此作出一定的反应，添加特效与动画
  - 交互的过程中可能需要向服务器索取或提交额外的数据（局部刷新），一般不是跳转，就是通过JS代码（响应某个动作或定时）向服务器发送**AJAX**请求
  - 服务器再把客户端需要的资源返回，客户端用得到的资源来实现动态效果，或**修改DOM结构**

- 请求

  - 方式

    - GET
    - POST

  - 请求URL

    **http://**user:pass@**zhufengpeixun.cn**:80/**node/index.htm**?type=1**#top**

    **协议**    登录信息      **服务器地址**             端口号      **文件路径**     查询字符 **片段标志符** 

  - 请求头

    - Content-Type 指定响应内容的 类型 和 编码

  - 状态码

    - 1xx 请求正在处理中：
      - 101 websocket
    - 2xx 正常处理完成
      - 200 OK 请求成功
    - 3xx 重定向
      - 301 Moved Permanently 永久重定向
      - 302 Found 临时重定向
    - 4xx 客户端错误
      - 400 Bad Request 语法错误
      - 401 Unauthorized 权限未认证
      - 403 Forbidden 禁止访问（认证过了，单无权限）
      - 404 Not Found 资源未找到
    - 5xx 服务器端错误
      - 500 Internalserver 

```
WebStrorm使用技巧，Ctrl+拖动一个文件到一个文件夹上，表示copy这个文件的内容到这个文件夹下并建立新文件
```

- global

  - 页面查询 ，用‘？’开始，用'&'分隔

    ```
    localhost:8080/?name=tianfeng&age=6
    ```

  - this 对象是导出对象

    ```
    var name = 'tianfeng';
    exports.name = name;
    console.log(this);
    
    输出为：
    { name: 'tianfeng' }
    ```

  - global内容

    ```
    console.log(global);
    ```

    - console.log()
      - 当前模块内部声明的变量
      - 外部导入的模块
      - 全局变量
      - 类全局变量，从外界传入的参数
        - function参数：
          - modeule，当前模块对象
          - exports，导出对象
          - require，加载模块的方法
          - __dirname ,  当前模块所在目录的绝对路径
          - __filename 当前模块的绝对路径
    - setInterval，setTimeout，setImmediate等
    - root，buffer
    - pid:9796 进程ID
    - process 当前的进程对象

  - nextstick 队列轮询，事件环，优先级（4.global/4.nextstick.js）

    - nodejs是单线程，顺序执行

    - 可以用setTimeout吧同步执行的步骤，放到最后执行，即放到**下一个队列的顶部**

    - nextstick，把步骤，放到**当前队列的底部**

    - 优先级顺序

      ```
      *  process.nextStick
      *  setTimeout
      *  setImmediate
      *  异步IO
      ```

- nodejs主线程是单线程，所以注意事项
  - 不能让主线程做更多的事情
  - 维护其正常运行
- util：nodejs的工具模块