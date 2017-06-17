title: 什么是云计算
date: 2016-08-30 10:36:29
tags:
 - 云计算 
---
美国国家标准技术研究所（NIST）对云计算给的基本定义如下。
### 定义
云计算是一个实现了无处不在的、方便的、通过网络访问按需取用可配置的计算资源共享池（例如：网络、服务器、存储、应用程序和服务）的模型，用户能以最小的管理成本或与提供商沟通的代价即可快速配置和发布所需的计算资源。
<!--more-->
### 基本特征
* **按需自助服务**。客户能单方面地规定他所需的计算能力，比如服务时间和网络存储，便可自动地获得所需要的资源而无需人工参与。

* **宽带网接入**。服务能力可以基于宽带网通过标准机制访问，而屏蔽异构的瘦客户端或胖客户端平台（例如手机、平板电脑、笔记本电脑和工作站）。

* **资源池化**。提供商的计算资源被集中起来为多个客户提供多租户模式的服务，不同的物理或虚拟资源按客户需求动态地分配和再分配。还有一种位置独立感，客户一般不能控制也无需知晓被提供的资源的确切位置，但可以让其指定位置更高级别的抽象（如国家、州或数据中心）。资源一般包括存储、计算、内存、网络带宽。

* **快速伸缩**。服务能力可以在某些情况下自动地弹性调配与发布，规模化地向外扩展或向内收缩以适应需求。对客户而言，可以用于调配的资源能力几乎是无限制的，可以在任意时间任意取用。

* **服务可计量**。云计算系统通过利用各类服务（如，存储、计算、带宽、活跃用户）在某种级别上相应的抽象的计量能力以自动控制和优化资源使用。资源使用情况可以被监视、控制和报告，对利用服务的提供商和客户两者来说都是透明的。

### 服务模型
* **软件即服务（SaaS）** 客户能使用提供商的应用程序以运行在云计算基础设施之上。这类应用程序可以从各种客户端访问，从瘦客户端到胖客户端，比如通用的Web浏览器（例如基于web的email服务）或者是专用的程序界面。客户不用管理和控制底层的云计算基础设施，包括网络、服务器、操作系统、存储甚至是个别应用程序的功能等，可能例外是需要客户指定应用程序的部分配置信息。

* **平台即服务（PaaS）** 客户可以在云计算基础设施上部署客户自己创建的或者购置提供商支持的编程语言、库、服务及工具创建的应用程序。客户不管理或控制云计算底层基础设施，包括网络、服务器、操作系统或存储，但对其部署的应用程序有控制权并可能对应用运行环境的相关配置进行设定。

* **基础设施即服务（IaaS）** 客户可以规定满足其运行任何软件（包括操作系统和应用程序）所需的计算、存储、网络和其他基本的计算资源。客户不管理或控制云计算底层基础设施，但能控制操作系统、存储和部署的应用；可能对选择网络组件（如防火墙）等具有有限的控制权。

### 部署模式
* **私有云** 云计算基础设施提供给包含多个消费者（例如业务单元）组成的单一的独占使用的机构。它可能由这个机构拥有、管理或运营，也可以是第三方或者其他合作方式。这些基础设施可以存放于或不存放于该机构本地。

* **社区云** 云计算基础设施提供给来自组织中具有共同利益（例如任务、安全要求、政策和法规遵从性等方面考虑）的消费者组成的特定社区独占使用。基础设施被社区中的一个或多个组织拥有、管理及运营，可以存放或不存放于它们的处所。

* **公有云** 云基础设施提供给一般公众开放使用。它可能被一个企业、学院、政府机构或其他合作形式的组织拥有、管理及运营。它存放于云计算提供商那里。

* **混合云** 这种是私有云、社区云、公有云的混合组成形式，各部分是独立的实体，但被标准化或专利技术绑定在一起，实现数据和应用程序的可移植性（例如在突发情况下在不同的云之间做负载均衡）。

![cloudstack](http://7xn3rj.com1.z0.glb.clouddn.com/static/images/cloudstack.png)