title: Mesosphere DCOS 常见问题
date: 2016-04-28 05:20:01
tags:
  - Mesos
  - cloud
  - DCOS
---
本文摘自Mesosphere 官网，可以从这些常见问题中明确Mesosphere DOCS的定位以及作用。并可一瞥其架构。
<!--more-->
## Mesosphere DCOS 和 Apache Mesos有何区别？
DCOS 是一个建立在开源Mesos之上的强大的有商业支持的软件产品。主要改进包括命令行和Web界面，很容易打包与安装，它还有不断壮大的技术合作伙伴生态系统。

## DCOS集群和私有云或虚拟化有何区别？
DCOS是下一代的私有云。它和虚拟机之类的软件不同，因为DCOS可以组织任何形式的服务器——物理机，虚拟机，云——作为单一的共享资源池。它的Mesos内核会动态分配必要的计算、存储、网络资源，而不是预先配置的机器镜像。

## DCOS与Kuneretes、Docker这类容器技术有何区别？
DCOS对Kubernetes和Docker都支持。因为DCOS在服务器级别管理基础设施，它是运行在Linux服务器之上的，它可以大规模地启动Docker容器。Mesosphere和Google合作把Kuneretes引入DCOS。Kuneretes直接和DCOS API交互，就像任何其他的DCOS服务一样。

## 我可以在DCOS里运行什么类型的工作负载？
DCOS里可以运行几乎任何类型的工作负载。它支持诸如Cassandra和MemSQL这类的数据库服务，Hadoop 和 Spark这类大数据系统，也支持Marathon 和 Kubernetes这种编排服务用于托管长期运行的云应用。

## 我可以编写自己的运行在DCOS上的分布式服务吗？
可以。Mesosphere DCOS目前支持所有Mesos框架，并且很快将会推出软件开发工具包（SDK）以便更容易得编写新服务或者集成现有服务，即利用DCOS固有的伸缩性、灵活性和高可用性。

## Mesos和DCOS有哪些常见使用案例？
尽管DCOS支持几乎所有类型的工作负载，但特别流行用它支持PaSS产品，大数据分析（例如Hadoop, Spark 和 Kafka），还有HDFS、Cassandra 和 MemSQL这类有状态服务。即将支持更多样化的服务，包括MySQL这类传统数据库服务。

## 我可以下载社区版的DCOS并在本地运行吗？
DCOS社区版目前还不支持下载，也不支持在本地服务器上运行。用户需要这种功能时应该联系Mesosphere了解DCOS企业版。

## 我没有数千台服务器时使用DOCS还有意义吗？
有。尽管像Twitter这种Mesos大用户受到了很多关注，但很多Mesos和DCOS早期用户在几百个或十几个节点上也能成功运行。DCOS为小型集群提供了便利，并且当你需要的时候还可以扩展至数万台节点。

## Mesosphere DCOS开源吗？
DCOS是不开源的，但它是建立在开源基础之上的，包括Apache Mesos, Marathon 和 Chronos。DCOS可以运行与集成许多流行的开源技术。

