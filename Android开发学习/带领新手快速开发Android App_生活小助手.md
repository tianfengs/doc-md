---
typora-root-url: adroidPics
typora-copy-images-to: adroidPics
---

## 项目：SmartBulter 智能管家

### 包括：

- 闪屏页，判断是否是第一次运行

  ViewPage+小圆点+跳过

- 用户登录

  第三方Bmob的用户系统

  忘记密码：修改密码，忘记密码（邮箱找回）

  工具类封装

  注册页。

- 登录后，进入Dialog页

  - 科大讯飞TTS引擎，聊天页
  - 微信精选，Picasso加载图片，listview
  - 美女图片：picasso加载，PhotoView
  - 个人中心
    - 快递查询：聚合数据+时间轴
    - 归属地查询：聚合数据+键盘逻辑
    - 添加照片
    - 设置
      - 短信提醒：短信服务，事件分发绑定返回键和主页键
      - 检测版本
      - Tomcat演示
      - 扫一扫
      - 我的位置，地图
      - 二维码分享：生成二维码

- 开发逻辑+一整套项目经验

### 项目概括

项目功能：

- Baidu Map
- Picasso
- 自定义dialog
- 自定义ViewPage
- Tablayout
- 腾讯Bugly，检查catch
- 自定义Log
- 二维码
- 科大讯飞TTS
- 语音机器人引擎
- Version Update升级
- RxVolley
- Bmob用户系统
- WebView加载
- ListView高级用法

课程收获

- 工具类的封装
  - 封装Log
  - 封装SharedPreference
  - 封装Picasso
- Android Studio使用
- 实际开发的技巧和源码的理论知识
  - model和库，jar包的冲突
- 个人经验
  - 布局，思考，打框架



### 项目架构部署

从哪些方面下手

- Package：包，归类
- Application:统一定义，统一初始化
- Activity：基类
- Drawable：
- Values
- UtilTools：写一个目录，统一的方法，封装
- StaticClass：

### TabLayout

14年google推出的设计语言——**Material Design**。并推出一系列实现MD效果的**控件库**——**Support Library**。其中**TabLayout**就是之一。

TabLayout就是一个选项卡的控件。

```
compile 'com.android.support:design:24.2.0'
```

- 样式
  - xmlns:app="http://schemas.android.com/apk/res-auto"
  - 标题颜色
  - 下划线颜色
  - 默认字体颜色
  - 选中字体颜色
  - 。。。
- TabLayout绑定ViewPage
  - ButlerFragment(管家服务)
  - WeChatFragment（微信精选）
  - GirlFragment（美女社区）
  - UserFragment（个人中心）

## 封装工具

### Log日志

##### 五个等级

- static Level DEBUG:细粒度信息事件对调试有帮助
- INFO：消息在粗粒度级别上突出强调应用程序的运行过程
- WARN：会出现潜在错误的清醒。会导致应用程序退出。
- ERROR：发生了错误事件，不影响系统的运行
- FATAL：严重的错误事件将会导致应用程序的退出。

##### 封装方法

- 定义“开关”，boolean值
- 定义一个TAG
- 定义等级：D，I，W，E，F
- 使用：Log.I(TAG, "text")

### 封装SharedPreferences

四种存储方式：

- SharedPreferences：xml方式存储在内存
- SQLite
- ContentProvider
- File

SharedPreferences轻型数据存储，基于xml文件存储key-value键值对，存储一些简单配置信息。

SharedPreferences对象本身只能获取数据而不支持存储和修改，存储修改时通过Editor对象实现。

实现步骤：

- 根据Context获取SharedPreferences对象
- 利用edit()方法获取Editor对象
- 通过Editor对象存储key-value键值对数据
- 通过commit()方法提交数据

封装方法：

- 定义存储方式：get/put
- 明确数据类型 Int/String/Boolean
- 定义删除功能 单个/全部



## 首页逻辑小技巧