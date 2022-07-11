---
typora-copy-images-to: pics
typora-root-url: pics
---



DICOM接口开发涉及到的内容：

1. word文档转图片——>图片转dcm图片

2. 把数据：DICOM将所有现实世界的数据——病人、检查、医疗设备等看作具有各自属性（properties or attributes1 ）的对象，以DICOM信息对象定义（DICOM InformationObject Definitions，IODs）为标准制定对象和属性。
   根据 27 个值表现（Value Representation，VR）类型进行对其格式化，获得病人信息姓名、电子病历编号（ID）、性别、年龄、体重、吸烟史等各类数据并保证属性命名和处理的一致性。

   写入图片信息中

3. 把图片信息上传到PACS系统中



其它问题：

1. 下载，预览

 ![1083581-20200914162115846-555554088](/1083581-20200914162115846-555554088.gif) 

2. 参数？都需要压入什么信息

![1655898787726](/1655898787726.png) 

3. 压缩格式也需要了解



————————————————
版权声明：本文为CSDN博主「一滩海豹」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/qq_35524189/article/details/117464167

### 一.什么是DICOM

DICOM 就是医学数字成像和传输协议（Digital Imaging and COmmunications in Medicine）的简称，多年来致力于创造最广泛和根本的数字医学成像标准。比如，DICOM 提供使医学成像数据能够在诊断中被精确地表达和处理的所有工具。此外，与普遍看法相反，DICOM不只是图像或者文件，他是数据传输、存储和显示协议的总和。

PACS 图像传输和归档系统（Picture Archiving and CommunicationSystems）。PACS 是一种医疗系统（由必要的软硬件组成），是被用来实现数字医学成像的。

PACS 是由数字影像获取设备（modality，比如 CT、核磁、DR、CR、超声等）、数字影像归档（archive，他来存档影像设备产生的图像），以及工作站（workstation，用来让影像科医师浏览影像）。

![在这里插入图片描述](https://img-blog.csdnimg.cn/2021060211400392.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTI0MTg5,size_16,color_FFFFFF,t_70) 

PACS和DICOM关系：
所有的PACS都要遵守DICOM协议标准。PACS 直接与 DICOM 相关。PACS 的功能是由 DICOM 驱动的，DICOM 保证 PACS 的交互。
因此 PACS 设备或软件具有他们自己的 DICOM 一致性声明（DICOM Conformance Statement）,他是解释设备支持 DICOM 程度的重要文档。可以说，PACS 给 DICOM 带来了生命

优点

1. 数字医疗的普遍标准。
2. 绝佳的图像品质。比如，对于显示黑白图像，DICOM 支持最高 65536（16 比特）个灰阶。将 DICOM 图像转换为 JPEG或 BMP 图像，则只能保证 256 个灰阶。
3. 对于许多成像参数和不同数据类型的支持。DICOM 不仅能用来存图像，还能保存众多其他影像相关参数，比如病人的三维位置，在图像中被检对象的实际大小，层厚，图像曝光参数等。
4. 医疗数据的完整编码。从病人姓名到图像颜色深度，再到当前病人的诊断信息，DICOM文件和消息使用超过 2000 个标准的属性来传送各类医疗数据覆盖当代医学影像领域的所有方面。

### 二、DICOM 是如何运作的

DICOM将所有现实世界的数据——病人、检查、医疗设备等看作具有各自属性（properties or attributes1 ）的对象，以DICOM信息对象定义（DICOM InformationObject Definitions，IODs）为标准制定对象和属性。
根据 27 个值表现（Value Representation，VR）类型进行对其格式化，获得病人信息姓名、电子病历编号（ID）、性别、年龄、体重、吸烟史等各类数据并保证属性命名和处理的一致性。![在这里插入图片描述](https://img-blog.csdnimg.cn/20210602114129831.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTI0MTg5,size_16,color_FFFFFF,t_70) 

当数据以 DICOM 数据属性的形式被采集后，这些数据就能够在各类 DICOM 设备和软件（应用实体 Application Entities，AEs）间进行传送和处理了。DICOM 是依靠服务提供模型来描绘事物处理过程的。也就是说，一个 DICOM 应用会为参与处理过程的双方提供服务。每个服务通常都要涉及一些数据交换（特别是通过电脑网络进行交换）。数据交换过程中，服务会带着需要处理的数据（IODs）与特定的服务类型建立联系。DICOM 称这种联系叫做服务对象配对（ServiceObject Pairs，OPs），并且将 SOP 组合起来，称为 SOP 类（SOP Class）。比如，从数字 CT 机要将 CT 图像存储到数字 PACS 中，就可以称作：CT 存储 SOP，如图 3。在此例中，CT 图像就是服务所要处理的数据，所以 CT 图像即代表 DICOM IOD（DICOM 数据对象![在这里插入图片描述](https://img-blog.csdnimg.cn/20210602114137616.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM1NTI0MTg5,size_16,color_FFFFFF,t_70) 



### 处理dicom图像与bmp图像的区别，难点在哪？

1)从文件格式上看，用于保存医学数据的DICOM包容的信息量大
（除图像信息外，还有很多附加信息，比如患者病例档案等），
格式复杂。所以编写读写文件函数时，处理DICOM比BMP要繁琐。
2)BMP只能保存一帧静止图像，DICOM的压缩算法种类很多，也可以保存
动画图像，这是它们的一个区别，也可以说是DICOM处理的一个难点。
3)DICOM的图像基本都是与医学相关的（MRI，CT等），医学图像的
特点是分辨率低，特征不清晰等等。对这类图像做分割和特征提取
难度较大。



## 什么是DICOMweb？

————————————————
版权声明：本文为CSDN博主「ZStarWalker」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/zstarwalker/article/details/88367509

DICOMweb是DICOM标准的一部分，它采用当前互联网应用最广的RESTful服务方式，面向互联网，提供DICOM影像查询、调阅和存储等服务。可以说，DICOMweb将DICOM的应用范围从传统的医院内局域网，扩展到了互联网。

#### 为什么需要DICOMweb？

在当前互联网时代，用户希望能够通过互联网，随时随地，在不同类型的终端设备上方便的调阅医疗影像。说的更具体一些，医生或个人用户往往希望拿出手机或笔记本，打开一个网页，就能直接看到DICOM影像。医疗影像阅片已经从之前局限在放射科阅片室工作站，扩展到了更大的范围，包括整个医院临床科室，医联体，乃至整个互联网。最新的电子胶片服务模式更是加速推动了医疗影像走向互联网了这一大趋势。

而传统的DICOM标准中，用于点对点数据交换的DIMSE Services无法适用于互联网。相比于当前互联网普遍采用的RESTFul架构，DIMSE显得有些“笨重”，不够轻盈和灵活。同时，DIMSE从起源上，是面向局域网应用设计的，它往往要求通讯双方具有固定且已知的IP，并且在开始正式传输数据前，通讯双方要先完成一套稍显复杂的“握手互认”过程。此外，互联网通讯具有高延迟、高并发特点，这些也是DIMSE不能很好支持的。

新的DICOMweb标准基于RESTful进行设计，全面拥抱互联网主流通讯方式。这一点能够给软件厂商，乃至医院信息化投资带来实在且巨大的好处。

互联网行业是当今发展最迅速的领域，新技术、新架构、各种开源免费组件缤纷夺目，日新月异。从下面的图片可以直观感受到Web开发在最近10年的飞跃发展。![img](https://img-blog.csdnimg.cn/20190309161029147.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzdGFyd2Fsa2Vy,size_16,color_FFFFFF,t_70) 

出现在右侧2019年下面框架和技术极大的提高了如今Web软件开发的效率。而且基于Javascript的开发，已经不再局限于浏览器，借助Electron和Node已经扩展覆盖了桌面应用和服务器后台程序开发。

还有一个很实在的好处，基于Javascript和上述进行软件开发，招人难度大大降低，各层次人才储备充足。

DICOMweb能做什么？
那么DICOMweb能做些什么呢？参考下图，可以用一句话来概括，DICOMweb定义了在互联网上基于RESTful接口，如何进行DICOM影像的查询、获取、存储和工作流程协同，以及如何查询一台服务对外提供哪些接口。![img](https://img-blog.csdnimg.cn/20190309161108211.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzdGFyd2Fsa2Vy,size_16,color_FFFFFF,t_70) 

这里简要介绍最常用的DICOM查询、获取和存储服务。

#### QIDO-RS，DICOM查询服务

#### QIDO-RS，英文全称为Query based on ID for DICOM Objects using RESTful Services，是通过RESTful接口查询服务器上是否有自己符合指定条件的影像。大家注意，名称缩写中的“RS”代表RESTful Services，这个后面还会遇到。

下图给出了很形象的示例。![img](https://img-blog.csdnimg.cn/20190309161153573.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzdGFyd2Fsa2Vy,size_16,color_FFFFFF,t_70) 

指定的条件可以是患者ID，也可以检查编号等。具体定义可以参见 PS3.18 6.7。这里给出一组比较典型的查询请求示例。：

http://myserver.com/studies?PatientID=11235813

http://myserver.com/studies?PatientID=11235813&StudyDate=20130509

http://myserver.com/studies?00100010=SMITH*&00101002.00100020=11235813&limit=25

http://myserver.com/studies?PatientID=11235813&StudyDate=20130509-20130510

注意，QIDO-RS请求必须使用RESTful中的GET操作。

#### **WADO-RS，DICOM获取服务**

#### WADO-RS，英文全称为Web Access to DICOM Persistent Objects using RESTful Services，是通过RESTful接口从服务器上获取得到指定的影像。

下图给出了形象示例。

![img](https://img-blog.csdnimg.cn/20190309161236534.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzdGFyd2Fsa2Vy,size_16,color_FFFFFF,t_70) 

可以指定获取某个Study，某个Series，某个Image，甚至是某一个Frame。此外，请求的发起方还可以指定自己能够或希望接受的图像类型。详细标准定义参见PS3.18 6.5。

https://myserver.com/studies/2.16.840.1.1.2.3/

https://myserver.com/studies/2.16.840.1.1.2.3/series/2.16.840.1.4.5.6/instances/2.16.840.1.7.8.9

注意，WADO-RS请求必须使用RESTful中的GET操作。

#### STOW-RS，DICOM存储服务

#### STOW-RS，英文全称Store over the Web using RESTful Services，是通过RESTful接口将指定影像上传到服务器上进行存储。

![img](https://img-blog.csdnimg.cn/20190309161335880.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzdGFyd2Fsa2Vy,size_16,color_FFFFFF,t_70) 

影像的上传存储除了影像数据本身，还需要提供与影像相关的其他信息，用于生成符合条件的DICOM头文件信息，所以相对有些复杂。具体可以参见PS3.18 6.6。

注意，WADO-RS请求必须使用RESTful中的POST操作。

#### UPS-RS，DICOM工作流程协同服务

UPS-RS，英文全称(Unified Procedure Step using RESTful Services，是通过RESTful接口来创建、声明和更新工作任务的状态（creating, claiming, and updating specific work item resources that store and communicate the state of a work item, its inputs and outputs）。![img](https://img-blog.csdnimg.cn/20190309161357578.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pzdGFyd2Fsa2Vy,size_16,color_FFFFFF,t_70) 

这部分有些抽象，也相对不常用。具体信息可以参考[PS3.18 6.9](http://dicom.nema.org/medical/dicom/current/output/chtml/part18/sect_6.9.html)。

#### **Capabilities， DICOMweb服务能力查询**

#### DICOMweb专门定义了一个接口，用于支持访问者去查询一台DICOMweb服务器都对外提供哪些服务。具体可以参见 [PS3.18 6.8](http://dicom.nema.org/medical/dicom/current/output/chtml/part18/sect_6.8.html)。

### 更多示例

<https://www.dicomstandard.org/dicomweb/dicomweb-examples/>

请自助选用:)

#### 如何实现DICOMweb？

了解了DICOMWeb的优势与功能之后，那么如何实现DICOMWeb呢？

DICOMWeb的实现可以分为客户端（请求发起方）和服务器端（请求响应方）两个方面。客户端和服务器由于只采用RESTful进行通讯，实现了最大程度的解耦，所以可以选择完全不同的实现方式，包括编程语言。只要保证都遵循DICOMweb规定的数据格式就可以了。

客户端相对简单，网上有直接可以拿来使用的开源代码， dicomweb-client.readthedocs.io。这个是Python版本的。但基于Python版本，也已经有了JavaScript版本。可以说，对于网页前端编程，有了JS版本，基本就无需他求了。

服务器端则要复杂一些。大家可以参考Othanc中的C++语言实现版本。不过，个人想法，使用Python的Django框架，或JavaScript的Node.js框架，来开发DICOMweb是最轻量级，成本最低的。网上发现一个Python实现版本，neurDICOM，但还没有研究过。https://github.com/reactmed/neurdicom

希望以后能抽出时间，专门学习这部分，然后再与大家分享。

#### 写在最后

个人想法，DICOMweb最大的作用，就是为采用JavaScripts语言开发DICOM影像软件打开了通道。这使得医疗影像软件的开发，不仅能够使用当下最丰富的开源软件库，而且能够依靠当下储备最丰富的软件开发人才。

医疗影像软件开发不再局限于C++、Java和C#，JavaScript的威力已经在当前医疗影像软件商业化市场开始显现，尤其是在最新的人工智能落地应用领域“功不可没”。这又是另外一个故事了，能看懂这句话的都是“业内资深”了:)
