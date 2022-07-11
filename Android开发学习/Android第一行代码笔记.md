---
typora-copy-images-to: adroidPics
typora-root-url: adroidPics第一种
---

## 第1章 开始启程

## 第2章 探究活动Activity

### 2.4 活动的生命周期

#### 2.4.1 返回栈

使用任务（Task）来管理活动，Task是一组存放在栈（返回栈，Back Stack）里的活动的集合。

#### 2.4.2 活动状态

四种：

- 运行状态：活动位于栈顶
- 暂停状态：活动不位于栈顶，但可见
- 停止状态：活动不位于栈顶，且不可见
- 销毁状态：活动从返回栈移除后

#### 2.4.3 活动生存期

- 七个回调方法
  - **onCreate(),** 
  - **onStart(),  不可见->可见**
  - **onResume(),  运行状态，栈顶** 
  - **onPause(), 暂停状态，活动可见，但不可以交互** 。如果活动是Dialog，执行onPause()
  - **onStop(), 活动完全不可见**。如果活动是其它的，执行onStop()
  - **onDestroy(), 销毁活动**
  - onRestart()
- 三种生存期
  - 完整生存期：onCreate() --> onDestroy()
  - 可见生存期：onStart() --> onStop(), 保证处于停止状态的活动不会占用过多内存
  - 前台生存期：onResume() --> onPause()

#### 2.4.5 活动被回收前，用Bundle来存储临时数据

### 2.5 活动的启动模式

- standard
- singleTop: 活动已经在栈顶，再次启动时，直接使用它，不会新建活动
- singleTask: 让某个Activity在整个应用程序只有一个实例，如果有，此活动之上的活动统统出栈，如果没有，则新建一个活动
- singleInstance：启动一个新的返回栈来管理活动。我们的程序有一个活动允许其它程序调用，要两个程序共享我们这个程序的实例

在AndroidManifest.xml中通过给<activity>标签指定android:launchMode属性来启动模式，如：

```xml
<activity
	android:name=".FirstActivity"
	android:launchMode="singleTop"
	android:label="This is FirstActivity">
	<intent_filter>
		<action android:name="android.intent.action.MAIN" />
		<category android:name="android.intent.category.LAUNCHER" />
	</intent-filter>
</activity>
```

### 2.6 活动的实践

#### 2.6.1 知晓当前是在哪一个活动

建立BaseActivity，父类实现如下代码：

```
Log.d("BAseActivity", getClass().getSimpleName());
```

#### 2.6.2 随时随地退出程序

建立一个集合类ActivityCollector作为活动管理器

```
public class ActivityCollector{
	public static List<Activity> activities = new ArrayList<>();
	
	public static void addActivity(Activity activity){
		activities.add(activity);
	}
	
	removeActivity(Activity)...
	
	public static void finishAll(){
		for(Activity activity : activities){
			if(!activity.isFinishing()){
				activity.finish();
			}
		}
	}
}

// 也可以加上杀掉一个进程的代码
android.os.Process.killProcess(android.os.Process.myPid());
```

#### 2.6.3 启动活动的最佳写法

基本方法：构建Intent对象，然后调用startActivity()或startActivityForResult()。例如：

```
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
intent.putExtra("param1", "data1");
intent.putExtra("param2", "data2");
startActivity(intent);
```

原始接收代码：

```
public class SecondActivity extends BaseActivity{
	protected void onCreate(Bundle savedInstanceState){
		super.onCreate(savedInstanceState);
		setContentView(R.layout.second_layout);
		
		Intent intent = getIntent();
		String msg1 = intent.getStringExtra("param1");
		String msg2 = intent.getStringExtra("param2"); 
		...
	}
}
```



SeconodActivity代码接收，如下：

```
public class SecondActivity extends BaseActivity{
	public static void actionStart(Context context, String data1, String data2){
		Intent intent = new Intent(context, SecondActivity.class);
		intent.putExtra("param1", data1);
		intent.putExtra("param2", data2);
		context.startActivity(intent);
	}
}
```

这时，调用Activity只用一行代码传递变量和启动SecondActivity:

```
button1.setOnClickListener(new OnclickListener(){
	@Override
	public void onClick(View v){
		SecondActivity.actionStart(FirstActivity.this, "data1", "data2");
	}
})
```



## 第3章 UI开发

### 3.2 常用控件的使用

#### 3.2.1 TextView

#### 3.2.2 Button

#### 3.2.3 EditText

#### 3.2.4 ImageView

#### 3.2.5 ProgressBar

#### 3.2.6 AlertDialog

#### 3.2.7 ProgressDialog

### 3.3 四种布局

#### 3.3.1 线性布局 LinearLayout

#### 3.3.2 相对布局 RelativeLayout

#### 3.3.3 帧布局 FrameLayout

#### 3.3.4 百分比布局 PercentFrameLayout，PercentRelativeLayout

### 3.4 自定义控件

- 建立title.xml控件排列模板-
- 建立TitleLayout类，并继承自LinearLayout
- 在调用的xml模板中插入控件
- 控件的响应事件，在TitleLayout控件中处理

### 3.5 最常用最难用的控件——ListView

#### 3.5.1 ListView简单用法

String[]数组 --> ArrayAdapter<String>适配器 --> ListView

#### 3.5.2 定制ListView的界面

定制ListView界面



## 第6章 数据存储——持久化技术

### 6.1 持久化技术简介

Android主要包括3种：

- 文件存储
- SharedPreference存储
- 数据库存储

还有包括保存在SD卡中方式。

### 6.2 文件存储

#### 6.2.1 将数据存入文件

```
import android.content.Context;

FileOutputStream openFileOutput(String filename, MODE_PRIVATE|MODE_APPEND)
```

- 获得文件输出流**FileOutputStream**对象**out**：携带**文件名**和**写入模式** 
- 获得写缓冲区**ButtferedWriter**对象**write**：
  - 文件输出流-->写输出流-->写缓冲区:  `writer=new BufferedWriter(new OutputStreamWriter(out))`
- 利用缓冲区对象writer写字符串到文件 `writer.write(inputText)` 

#### 6.2.2 读取文件数据

- 获得文件输入流**FileInputStream**对象**in**：携带**文件名** 

- 获得读缓冲区**BufferedReader**对象**reader**：

  - 文件输出流-->读输入流-->读缓冲区: `reater=new BufferedReader(new InputStreamReader(in))`

- 建立字符串构建StringBuilder对象 content

- 利用缓冲区对象reader读取文件字符串 `reader.readLine()`，利用字符串构建对象content拼接文件内容：

  ```
  String line="";
  while((line=reader.readline())!=null){
  	content.append(line);
  }
  ```

### 6.3 SharedPreferences存储

- 用键值存储数据
- 支持不同类型的数据存储

#### 6.3.1 存储数据到SharedPreferences中

获得SharedPreferences对象的方法：

- Context类中的`getSharedPreferences(String filename, MODE_PRIVATE)`方法
- Activity类中的`getPreferecnes(MODE_PRIVATE)`方法，自动将类名作为文件名
- PreferenceManager类中的`getDefaultSharedPreferences(Context context)`方法，静态方法

向SharedPreferences文件中存储数据：

- 调用其对象的edit()方法获得**SharedPreferences.Editor**对象
- 向**SharedPreferences.Editor**对象中添加数据，如添加布尔型数据 putBoolean()，添加字符串putString()，等等。
- 调用 apply() 将添加的数据提交，完成存储。

### 6.4 SQLite数据库存储

特点：

- 运算速度快
- 占用资源少
- 支持标准SQL语法，遵循ACID事务
  - A：Atomicity：原子性，要么都发生，要么都不发生
  - C：Consisitency：一致性，数据的“完整性”必须保持一致，例如：完整性认为a+b=10，那么改变了a，b也要做响应的改变，来维持完整性。
  - I：Isolation：隔离性，多个并发的事务之间要进行隔离，互不干扰
  - D：Durability：持久性，一个事务一旦被提交，则数据库永久被改变，不会回滚。

#### 6.4.1 创建数据库

用**SQLiteOpenHelper**类进行数据库的操作，它是抽象类。重要方法包括：

- onCreate()
- onUpgrade()
- getReadableDatabase()
- getWritableDatabase()
- 构造方法，参数
  - Context
  - 数据库名
  - 允许我们在查询数据的时候返回一个自定义的Cursor，一般为null
  - 当前数据库的版本号，可以用于对数据库进行升级。

#### 6.4.3 添加数据

SQLitedatabase对象（由getReadableDatabase()或getWritableDatabase()返回），提供 `insert()`方法，参数

- 表名
- 未指定添加数据的情况下可为空列赋值NULL，一般为null
- ContentValues对象，提供一系列put()方法的重载，用于向ContentValues中添加数据

#### 6.4.6 查询数据

query() 参数：

- 表名
- 指定查询哪几列，默认查询所有列
- 约束查询某一行或某几行，默认所有行
- 约束查询某一行或某几行，默认所有行
- 指定需要去group by的列，默认不对结果进行group by操作
- 对group by数据进行过滤，默认不过滤
- 查询结果排序方式，默认为默认排序

### 6.5 使用LitePal操作数据库 ORM框架



## 第7章 跨程序共享数据——内容提供器

### 7.1 Content Provider 内容提供器

数据跨应用程序数据共享。提供机制保证数据访问安全性。

### 7.2 运行时权限 6.0开始

#### 7.2.1 Android运行时权限机制

- 应用程序使用了哪些权限，提醒用户
- 用户随时查看任意程序的权限申请情况

但有些权限比如微信读取短信彩信权限，很霸道。因此引入**运行时权限**，即在运行时根据需要对某一项权限进行授权。危险权限列表，9组24个：

![image-20200510155326330](/../adroidPics/image-20200510155326330.png)

#### 7.2.2 在运行时申请权限

### 7.3 访问其它程序的数据

内容提供器：

- 使用**现有的内容提供器**来读取和操作相应程序中的数据
- 创建自己的内容提供器，给程序数据**提供外部接口** 

#### 7.3.1 ContentResolver的用法

- 访问数据，通过**ContentResolver类的实例**，通过Context的**getContentResolver()**方法获取。

- 之后，通过**CRUD**来进行数据处理。

- 定位通过**URI**参数，分为两部分**authority**和**path**：
  - **authority**区分不同的应用程序，一般采用程序包名方式。例如：包名是com.example.app，则其authoriy命名为”com.example.app.provider“
  - **path**用于对同一应用程序中不同的表做区分，通常添加到authority后面。比如：某应用程序数据库有两张表：table1和table2，path命名为/table1 和 /table2。
  - 组合后为：”com.example.app.provider/table1“和”com.example.app.provider/table2“
    - content://com.example.app.provider/table1
    - content://com.example.app.provider/table2

解析成URI；

```java
Uri uri = Uri.parse("content://com.example.app.provider/table1");
```

用URI对象来查询table1表中的数据：

```
Cursor cursor = getContentResolver().query(
	uri,						// from table_name
	projection,					// select column1,column2 	查询的列名
	selection,					// where column = value		指定where的约束
	selectionArgs,				// 为上一个参数里的占位符提供值  为where中的占位符指定具体的值
	orderBy);					// order by column1,column2 指定查询结果的排序方式
```

### 7.4 创建自己的内容提供器

#### 7.4.1 创建自己的内容提供器步骤

- 继承 ContenProvider

- 实现其6个抽象方法

  - **boolean** onCreate()：通常完成对数据库的创建和升级等操作。

  - **Cursor** query()：查询数据

  - **Uri** insert()：向内容提供器添加一条数据

  - **int** update()：更新提供器中已有的数据

  - **int** delete()：从内容提供器中删除数据

  - **String** getType(Uri uri)：返回MIME类型，Uri对应的MIME字符串由3部分组成

    - 以vnd开头
    - 如果内容URI以路径结尾，则后接 android.cursor.dir/，如果内容URI以id结尾，则后接android.cursor.item/。
    - 最后接上 vnd.\<authority>.\<path>

    例如：

    content://com.example.app.provider/table1

     -->

     vnd.android.cursor.**dir**/vnd.com.example.app.provider.table1

    而

    content://com.example.app.provider/table1/1

    -->

    vnd.android.cursor.**item**/vnd.com.example.app.provider.table1

- URI表示法

  - 标准的（表中全部内容）：content://com.example.app.provider/table1
  - 可以加id：content://com.example.app.provider/table1/1
  - 可以用通配符\*（任意长度任意字符），#（任意长度的数字）：
    - 匹配任意表的内容：content://com.example.app.provider/\* 
    - 匹配table1中任意行数据：content://com.example.app.provider/table1/#

- 接着用UriMatcher类匹配内容URI

## 第8章 运用手机多媒体

### 8.1 程序运行到手机上

### 8.2 使用通知

Notification 通知会在程序不在前台的时候，在状态栏显示一个通知的图标，下拉状态栏会有一个详细内容。

#### 8.2.1 通知的基本用法

可以在**Activity**里创建，可以在**广播接收器**里创建，也可以在下一章的**服务**里创建。

- NotificationManager <-- Context 的 getSystemService()

  ```java
  NotificationManager manager = getSystemService(Context.NOTIFICATION_SERVICE);
  ```

- 用Builder构造器创建Notification对象，但由于代码升级，必须使用中间库进行兼容，这个中间库就是support-v4库提供的NotificationCompat类

  ```java
  Notification notification = new NotificationCompat.Builder(context)
  	...
  	.setContentTitle("消息标题")
      .setContentText("消息正文")
      .setWhen(System.currentTimeMillis())
      .setSmallIcon(R.drawable.small_icon)
      .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.lare_icon))
      ...
  	.build();
  ```

  调用：

  ```
  manager.notify(1, notification);
  ```

  在高版本上没有消息，是因为没有添加通知渠道，低版本是自动添加的

  ```java
  //开通通知渠道 NotificationChannel
  if(Build.VERSION.SDK_INT>=Build.VERSION_CODES.O){
  	NotificationChannel notificationChannel = new NotificationChannel("1", "name", NotificationManager.IMPORTANCE_HIGH);
  	manager.createNotificationChannel(notificationChannel);
  }
  ```

- 实现通知点击效果 PendingIntent

  延迟执行的Internt，在某一个时机，去执行某个动作：启动活动、启动服务和发送广播等

### 8.3 调用摄像头和相册

#### 8.3.1 调用摄像头拍照

- 创建File对象，存放图片，把图片存放于SD卡的`应用关联缓存目录`下<--getExternalCacheDir()方法。具体路径是：(此目录不需要权限)

  ```
  /sdcard/Android/data/<package name>/cache
  ```

  Android 7.0以下，可以调用Uri的fromFile()方法将File对象转换成Uri对象，即output_image.jpg这张图片的路径。

  Android 7.0以上，调用FileProvider的getUriForFile()方法将File对象转换成一个封装过的Uri对象，参数：

  - Context对象
  - authority 唯一字符串，**android:authorities**，在AndroidManifest里注册provider时使用
  - 刚创建的File对象

- 构建Intent对象，action指定为`android.media.action.IMAGE_CAPTURE`，调用Intent的putExtra()指定图片的输出地址——Uri。隐式Intent。

- 用startActivityForResult()启动活动。因此拍完照返回结果到 onActivityResult()方法中。

- 拍照成功：调用BitmapFactory的decodeStream()方法将 .jpg照片解析成Bitmap对象，在ImageView中显示。

- 在AndroidManifest.xml中对内容提供器进行注册。

  ``` xml
  <provider
  	android:name="androidx.core.content.FileProvider" 固定的
  	android:authorities="com.example.cameraalbumtest.fileprovider" 和getUriForFile一致
  	android:exported="false"						
  	android:grantUriPermissions="true"
  	<meta-data												指定Uri共享路径
  		android:name="android.support.FILE_PROVIDER_PATHS"		
  		android:resource="@xml/file_path" />				引用了一个资源
  </provider>
  
  file_path.xml文件：
  <?xml version="1.0" encoding="utf-8" ?>
  <paths xmlns:android="http://schemas.android.com/apk/res/android">
  	<external-paht 										指定Uri共享
      	name="my_images"								name随便填
          path="." />										共享的具体路径
  </paths>
  ```

  

- 创建file_paths.xml资源文件

- 为兼容4.4以前系统需要声明关联目录权限

#### 8.3.2 从相册中选择照片

- 申请SD卡的读写权限：WRITE_EXTERNAL_STORAGE
- 构建Intent，action指定为android.intent.action.GET_CONTENT
- 调用startActivityForResult()打开相册完成选择
- 4.4以上版本对Uri进行了封装，需要进行判断
  - 如果时Document类型，就用documentid进行处理，否则就普通方法处理
  - 如果Uri的authority时media格式，doucumentid还要再解析一次，取后半段字符串获得真正id
- 获得图片的真实路径
- 显示图片

### 8.4 播放多媒体文件

#### 8.4.1 播放音频

Mediaplay类

需要申请SD卡的读写权限：WRITE_EXTERNAL_STORAGE

需要在AndroidManifest.xml中声明

#### 8.4.2 播放视频

VideoView类

需要在AndroidManifest.xml中声明SD卡的读写权限：WRITE_EXTERNAL_STORAGE：

```
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```



## 第9章 使用网络技术

### 9.1 WebView内嵌Web浏览器

需要在AndroidManifest.xml中声明权限

```
<uses-permission android:name="android.permission.INTERNET" />
```

### 9.2 使用HTTP协议访问网络

#### 9.2.1 HttpURLConnection类

和其它一样，设置网址，设置HTTP模式，如GET，端口，超时时间等，然后开启连接，用Stream处理接受和发送数据。

声明android.permission.INTERNET权限

#### 9.2.2 OkHttp 第三方Square的网路通信库

可以和以下库一起使用:

Moshi：供android和java使用的 Json 库

Okio：供java使用的 I/O库

Picasso：供Android使用的 图片下载和缓存库

### 9.3 解析XML（都比较复杂）

Pull解析

sax解析

DOM解析

### 9.4 解析Json

#### 9.4.1 JsonObject解析（复杂）

#### 9.4.2 GSON(google提供)

直接把json映射为对象

```java
Gson gson = new Gson();
Person person = gson.fromJson(jsonData, Person.class);

List<Person> people = gson.fromJson(jsonData, new TypeToken<List<Person>>(){}.getType());
```

### 9.5 网络编程的最佳实践

## 10 服务

### 10.1 服务概念

后台运行方案，需要长期执行，又不需要和用户交互的任务。

### 10.2 Android多线程编程

#### 10.2.1 线程基本用法

```java
Class MyThread extends Thread{
	@Override
	public void run(){
		//...
	}
}
//使用
new MyThread().start();
//%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
//或者
Class MyThread extends Runnable{
	@Override
	public void run(){
		//...
	}
}
//使用
MyThread myThread = new MyThread();
new Thread(myThread).start();
//%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%
// 匿名
new Thread(new Runnable(){
	@Override
	public void run(){
		//...
	}
}).start();
```

#### 10.2.2 在子线程中更新UI

Message，Handler

#### 10.2.3 异步消息处理机制

异步消息处理有4个部分：

- Message：在不同线程之间传递消息
- Handler：Handler的sendMessage(Message)发送消息，传递到它的handleMessage(Message)进行处理
- MessageQueue：存放Handler发送的消息，每个线程有一个
- Looper：循环查询MessageQueue里的数据，发现有，就立刻进行传递。

#### 10.2.4 使用AsyncTask抽象类（封装上一节的异步消息处理机制）

3个参数：

- Params：**传入参数**，用于在后台任务中使用，Void时，不需要传入参数
- Progress：后台任务的进度，使用这里指定的**泛型**作为**进度单位**，如：Integer
- Result：这里指定的**泛型**，作为**返回值类型**，如 Boolean

```
Class DownloadTask extends AsyncTask<Void, Integer, Boolean>{
	...
}
```

4个重新的方法

- onPreExecute()：用于一些界面的初始化，如显示一个进度条对话框
- doInBackground(Params)：子线程代码，处理耗时任务，不允许进行UI操作
- onProgressUpdate(Progress...)：对UI进行操作，利用参数的数值对界面元素操作
- onPostExecute(Result)：后台任务return后，这个方法会被调用，，比如提醒任务结果，关闭进度条

启动这个异步任务：

```
new DownloadTask().execute();
```

publishProgress()方法

### 10.3 服务的基本用法

- 继承自Service

- 重写IBinder onBind(Intent intent)方法

- 重写onCreate()，onStartCommand()，onDestroy()方法，启动，执行命令，销毁服务

- 在AndroidManifest.xml中注册服务

  ```
  <service
  	android:name=".MyService"
  	android:enabled="true"
  	android:exported="true" />
  ```

- 启动和停止服务：
  - 在Activity里，用Context对象，startService(Intent),stopService(Intent)
  - 在服务里的任意地方调用 stopSelf()方法

#### 10.3.3 活动和服务进行通信

通过Service里的 onBind()方法进行通信。

- 在服务的派生类MyService类里返回Binder派生类的实例
  - 在MyService里创建一个`Binder`的派生子类
  - 在派生类里进行耗时的操作等，其实是在一个新线程里操作
  - 创建派生类的实例，如`mBinder`
  - 在onBinder(Intent)里return这个派生类的实例`mBinder`
- 在活动里调用服务
  - 创建一个ServiceConnection的匿名类，重新`onServiceConnected()`和`onServiceDisconnected()`方法，用于绑定和解绑服务
  - 建立Service派生类对象，并在`onServiceConnected()`中，把IBinder参数向下转型实例化此对象。
  - 在活动中根据需要，利用此对象调用服务中的任意public方法。
  - 绑定服务：用bindService(Intent, ServiceConnection, BIND_AUTO_CREATE)
  - 解绑服务：unbindService(ServiceConnection)

### 10.4 服务的生命周期

- 生命周期：startService() --> stopService()或stopSelp()（会调用onDestroy()方法，销毁服务）

- 服务是单例模式，无论调用多少次startService()都只启动一个实例，**注意**：但每次调用都会执行一次onStartCommand()

- 绑定服务，调用Context的bindService()获得持久连接，执行onCreate()，会回调onBind()方法。
- 解绑服务，直到调用unbindService()方法，执行服务中的onDestroy()方法。
- 如果一个服务被启动和绑定，要同时调用stopService()和unbindService()服务才被销毁，才会调用onDestroy()方法。

### 10.5 服务的更多技巧

#### 10.5.1 前台服务

防止系统不足导致被回收

它一直有一个正在运行的图标在系统的状态栏显示，下拉状态栏可以看到详细信息，类似于通知。

构建方法：

- 在MyService类的onCreate()方法中，建立通知Notification对象
- 用startForeground(Integer, Notification)启动通知对象，则让此服务变为前台服务了

#### 10.5.2 使用IntentService

服务的问题

- **服务**中的代码默认**运行在主线程**当中。如果耗时久，就会出现**ANR(Application Not Responding)**.

- 解决方法是，在每一个服务的方法里开启一个子线程，例如：

  ```java
  public class MyService extends Service{
  	...
  	@Override
  	public int onStartCommand(Intent intent, int flags, int startId){
  		new Thread(new Runnable(){
  			@Override
  			public void run(){
  				//.....耗时的逻辑。。。
  				
  				//处理完耗时的逻辑之后想要停止这个服务，则要
  				stopSelf();
  			}
  		}).start();
  		return super.onStartCommand(intent, flags, startId);
  	}
  	...	
  }
  ```

解决方案：**IntentService**

### 10.6 服务的最佳实践——完整版的下载示例









