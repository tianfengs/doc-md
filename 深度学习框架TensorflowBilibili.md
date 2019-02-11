TensorFlow 深度学习框架 Bilibili课程

---- 炼数成金 DATAGURU

Ben



第一课

介绍：支持python、c++语言

2015年11月开源了TensorFlow

可参考**黄校长**的深度学习课程

本课程讲解TS的运作流程、使用方法，各种优化器的使用方法，cnn等网络

TensorFlow的工具：***TensorBoard***，运行于浏览器



##### Anaconda安装

用管理员安装，同时安装python3.7

python编辑编译器推荐：Jupyter Notebook

1. cmd中输入下面命令在shi下建立Jupyter_notebook_config.py

   > jupyter notebook --generate-config

2. 修改Jupyter_notebook_config.py文件中的目录地址

   > The directory to use for notebooks and kernels.
   >
   > #c.NotebookApp.notebook_dir = ''
   > c.NotebookApp.notebook_dir = u'D:\maidan\Tensorflow'

##### Tesorflow安装(管理员)

CPU：pip install tensorflow

GPU: pip install tensorflow-gpu

此处安装失败，因为python版本过高，可以通过以下命令降低python版本：（http://docs.anaconda.com/anaconda/user-guide/faq/#how-do-i-get-the-latest-anaconda-with-python-3-5）

> conda install python=3.5

##### 实际解决方案：

1. 建立新的environments，并安装指定的python版本

   1. 检测可安装的python版本，不能高于3.5

      conda search --full-name python

   2. 安装3.5.6版本的python环境

      conda create -n py35 python=3.5.6 anaconda

   3. 启动py35环境(前提：要启动Anaconda Prompt)

      activate py35

   4. 在py35中安装tensorflow

      pip install tensorflow

   5. （可选）升级pip为最新版本

      python -m pip install --upgrade pip

   6. 列一下环境列表

      conda info --envs

   7. 由于引用tensorflow不成功，要重新安装下面两个

      conda install ipython

      conda install jupyter

   8. 启动jupyter notebook并检测（在Anaconda Prompt中）

      jupyter notebook

   9. 在jupyter中建立文件，输入

      import tensorflow as tf

      按shift+Enter，如果没有报错，说明安装成功

本解决方案参考链接：

- https://conda.io/docs/user-guide/tasks/manage-python.html 建立新的环境方法
- https://conda.io/docs/user-guide/tasks/manage-environments.html#activate-env 进入和解除新环境
- https://blog.csdn.net/qq_18941425/article/details/80399500 第7条的报错安装

##### Tensorflow基本概念

Tensorflow是一个编程系统

- 使用图（graphs）来表示计算任务

- 图中的节点称为op（operation）

- 一个op获得0个或多个Tensor（张量：常量、变量、占位符、稀疏张量。。。），执行计算，产生0或多个Tensor

- Tensor看做是一个n维的数组或列表，用来表示数据

- 图必须在会话（Session）的上下文（Context）里被启动执行

- 通过变量（Variable）维护状态

- 使用feed和fetch可以为任意的操作赋值或从中获取数据

  - fetch：同时运行多个op
  - feed：在运行的时候传入数据，以字典的方式传入

  ![](D:\maidan\doc笔记md\pics\Tensorflow结构.png)

##### 2-4Tensorflow简单示例

用线性模型接近给出样本点

import tensorflow as tf
import numpy as np

1. 建立100个随机样本点

   ```
   x_data = np.random.rand(100)
   y_data = x_data*0.1 + 0.2
   ```

2. 构造一个线性模型

   ```
   b = tf.Variable(0.)
   k = tf.Variable(0.)
   y = k*x_data + b
   ```

3. 定义一个**二次代价函数**

   ```
   loss = tf.reduce_mean(tf.square(y_data-y))
   ```

   **误差的平方求平均**

4. 定义一个梯度下降法，进行训练的优化器

   ```
   optimizer = tf.train.GradientDescentOptimizer(0.2)
   ```

5. 最小化代价函数

   ```
   train = optimizer.minimize(loss)
   ```

6. 初始化变量，定义会话并打印输出

   ```
   init = tf.global_variables_initializer()
   
   with tf.Session() as sess:
       sess.run(init)
       for step in range(201):
           sess.run(train)
           if step%20==0:
               print(step,sess.run([k,b]))
   ```

7. 运行结果

   ```
   0 [0.052117176, 0.09957275]
   20 [0.102184065, 0.1988538]
   40 [0.101263136, 0.19933717]
   60 [0.100730516, 0.19961666]
   80 [0.100422494, 0.19977829]
   100 [0.10024436, 0.19987178]
   120 [0.10014132, 0.19992584]
   140 [0.10008172, 0.19995712]
   160 [0.100047275, 0.19997519]
   180 [0.10002734, 0.19998565]
   200 [0.10001583, 0.19999169]
   ```

```
NumPy：NumPy(Numerical Python) 是 Python 语言的一个扩展程序库，支持大量的维度数组与矩阵运算，此外也针对数组运算提供大量的数学函数库。
NumPy 是一个运行速度非常快的数学库，主要用于数组计算，包含：
- 一个强大的N维数组对象 ndarray
- 广播功能函数
- 整合 C/C++/Fortran 代码的工具
- 线性代数、傅里叶变换、随机数生成等功能
NumPy 官网 http://www.numpy.org/
NumPy 源代码：https://github.com/numpy/numpy
SciPy 官网：https://www.scipy.org/
SciPy 源代码：https://github.com/scipy/scipy
Matplotlib 官网：https://matplotlib.org/
Matplotlib 源代码：https://github.com/matplotlib/matplotlib

```



##### 3-1非线性回归（要在网易云课堂上听一下吴恩达的深度学习课程）

用回归模型绘制给出随机点的，用曲线模拟，并绘制出图形

```
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt #绘图的库
```

1. 准备数据

   ```
   #使用numpy生成200个随机点,[:,np.newaxis]增加一个维度
   x_data = np.linspace(-0.5,0.5,200)[:,np.newaxis]
   noise = np.random.normal(0,0.02,x_data.shape)
   y_data = np.square(x_data) + noise
   
   #定义两个placeholder，预测值
   x= tf.placeholder(tf.float32,[None,1])
   y= tf.placeholder(tf.float32,[None,1])
   ```

2. 构建一个神经网络：输入一个x点，经过一个神经网络计算，得到一个y值，预测值

   输入层：一个点，一个神经元

   中间层：10个神经元

   输出层：一个点，一个神经元

   ```
   #定义神经网络的中间层
   Weights_L1 = tf.Variable(tf.random_normal([1,10])) #权值，是个变量，连接输入层和中间层
   biases_L1 = tf.Variable(tf.zeros([1,10]))          #偏制值，初始化为0
   Wx_plus_b_L1 = tf.matmul(x,Weights_L1) + biases_L1 #计算信号的总和，x是矩阵，是输入
   L1 = tf.nn.tanh(Wx_plus_b_L1)                      #中间层的输出，双曲正切函数作为激活函数
   ```

   ```
   #定义神经网络输出层
   Weights_L2 = tf.Variable(tf.random_normal([10,1])) #权值，连接中间层和输出层
   biases_L2 = tf.Variable(tf.zeros([1,1]))           #偏置值，
   Wx_plus_b_L2 = tf.matmul(L1,Weights_L2) + biases_L2#信号的总和，L1是中间层的输出值
   prediction = tf.nn.tanh(Wx_plus_b_L2)              #输出值，激活函数
   ```

3. 建立代价函数和训练方法

   ```
   #二次代价函数
   loss = tf.reduce_mean(tf.square(y-prediction))
   #使用梯度下降法
   train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)
   ```

4. 会话和输出

   ```
   with tf.Session() as sess:
       sess.run(tf.global_variables_initializer())	#变量初始化
       for _ in range(2000):
           sess.run(train_step,feed_dict={x:x_data,y:y_data})
       # 获得预测值
   	prediction_value = sess.run(prediction,feed_dict={x:x_data})
   	#绘图
   	plt.figure()
   	plt.scatter(x_data,y_data)
   	plt.plot(x_data,prediction_value,'r-',lw=3)
   ```

第三课（需要学习**吴恩达**的课程）

3-2 MNIST数据集：手写数字数字集

- MNIST Database官网：http://yann.lecun.com/exdb/mnist/

- 数据集分为两部分：6万行训练数据集(minst.train)和1万行测试数据集(mnist.test)

- 每张图片包含28x28个像素，展开成向量，长度28*28=784.因此训练数据集mnist.train.images是一个形状为[60000,784]的张量或矩阵。图片里的某个像素的强度值介于0-1之间。

  ![](D:\maidan\doc笔记md\pics\www.bilibili.com_video_av20542427__p=9.png)

- 数据集的标签是介于0-9的数字，要把标签转化为"one-hot vectors"。一个one-hot向量除了某一位数字是1以外，其余维度数字都是0。标签0表示为([1,0,0,0,0,0,0,0,0,0])，标签3表示为([0,0,0,1,0,0,0,0,0,0]).
- 因此，mnist.train.labels是一个[60000,10]的数字矩阵
- 神经网络构建 784-->10(感知器，因为没有中间层，直接输入->输出)

**Softmax函数**

​	用概率来确定是某一个数字的概率，取概率最高的数字为确认数字。

