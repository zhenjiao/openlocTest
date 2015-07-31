<properties
   pageTitle="Caching guidance | Microsoft Azure"
   description="Guidance on caching to improve performance and scalability."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# 缓存的指导

![](media/best-practices-caching/pnp-logo.png)

缓存是一种常用的技术，其目的是为了提高性能和
通过频繁地暂时复制系统的可扩展性访问数据
到位于应用程序的快速存储。如果这个快速的数据存储
是位于接近比原始的源，然后缓存的应用程序
可以显著提高客户端应用程序的响应时间服务
数据更快。缓存是最有效的当客户端实例反复
读取相同的数据，尤其是如果数据保持相对静止和
原始数据存储区是相对于缓存的速度慢是
主题到高水平的竞争，或者是很远的地方当网络延迟
可以导致 access 会很慢。

## 分布式应用程序中缓存

分布式应用程序通常执行或两个
当缓存数据的以下策略:

- 使用一个专用的高速缓存，数据被本地运行的应用程序或服务实例的计算机。
- 使用共享的缓存，作为共同的来源，这些可以由多个流程和/或机器访问服务。

在这两种情况下，缓存可以执行的客户端 (通过进程提供
用户界面系统，例如 web 浏览器或桌面应用程序)，
和/或服务器端 (通过提供业务服务的过程
远程运行)。

### 私人缓存

最基本的类型是缓存的内存中的存储，在地址举行
单个进程的空间和由运行的代码直接访问
在这一进程。这种类型的缓存是非常快速的访问，和它可以
提供用于存储少量的极为有效的策略
静态数据高速缓存的大小通常会受到
宿主进程的计算机上可用的内存量。如果你
需要缓存的更多是在内存中，物理上可能的信息
可以将缓存的数据写入本地文件系统中。这将
一定的访问比数据举行内存中，但是应该要慢
仍是更快、 更可靠比通过网络检索数据。

如果你有使用这种模式的应用程序的多个实例
同时运行，每个应用程序实例都将有它自己
独立缓存包含自己的数据副本。

你应该作为在一些原始数据的快照缓存
在过去点。如果此数据不是静态的它是可能的
不同的应用程序实例将容纳不同版本的
其缓存中的数据。因此，由这些执行的相同查询
实例可能会返回不同的结果，如图 1 所示。

![使用应用程序的不同实例在内存中缓存](media/best-practices-caching/Figure1.png)

_图 1: 使用应用程序的不同实例在内存中缓存_

### 共享缓存

使用共享的缓存可以帮助减轻数据可能的关注
相同在每个缓存中，就可以在内存中缓存。共享
缓存可确保不同的应用程序实例看到相同
通过在单独的位置，查找缓存中的缓存数据的视图
通常主办作为一个单独的服务，如图 2 所示。

![使用共享的缓存_](media/best-practices-caching/Figure2.png)

_图 2: 使用一个共享的缓存_

使用共享缓存方法的一个重要的好处是
它可以帮助提供的可伸缩性。许多共享的缓存服务
通过使用服务器群集中，实现和利用软件，
以透明的方式，将数据分布在群集。一个
应用程序实例只是向服务发送请求缓存，
底层的基础架构是负责确定
群集中的缓存数据的位置。你可以轻松地扩展
通过添加更多服务器的高速缓存。

缺点的共享缓存方法是缓存
较慢的访问，因为它不再持有本地对每个
应用程序实例，并要求执行一个单独的
缓存服务可能向解决方案中添加复杂性。

## 使用缓存的注意事项

以下各节描述更详细地考虑
设计和使用的缓存。

### 何时缓存数据?

缓存可以大大提高性能、 可扩展性和可用性。更多的数据
你有和需要访问此数据，更多的用户数量愈大
缓存成为通过降低延迟和争用与处理相关的好处
大量的原始数据存储区中的并发请求。例如，一个数据库
可能支持有限的数量的并发连接，但从共享其检索数据
允许客户端应用程序访问此数据缓存，而不是基础数据库
即使目前耗尽可用连接的数量。此外，如果
数据库变得不可用，客户端应用程序可以继续使用
数据保留在缓存中。

你应该考虑缓存读取频繁但就是不经常修改的数据
(数据有更高比例的比较执行写操作的读取操作)。然而，
你不应该使用缓存作为权威信息的存储量关键;你应该
确保您的应用程序不能失去的所有更改都保存到
持久性数据存储区。这种方式，如果缓存中不可用，您的应用程序就可以
仍继续使用的数据存储区进行操作，你不会失去重要
信息。

### 类型的数据和缓存的人口战略

有效地使用缓存的关键在于确定最适当的数据到
缓存和缓存它在适当的时候。数据可能在添加到缓存
它由应用程序，检索，以便在应用程序需要的需求第一次
只是一旦从数据存储区和后续访问可以满足中提取数据
通过使用缓存。

另外，缓存可能会部分或完全填充数据提前，
通常当应用程序启动 (称为播种的方法)。然而，它可能会
不是明智之举实施播种为大的缓存，这种方法可以施加
原始数据存储区的应用程序启动运行时突然、 高负荷。

经常使用模式分析可以帮助决定是否全部或部分
预填充高速缓存，并选择要缓存的数据。例如，它
可能会有用，种子与静态的用户配置文件数据缓存
客户使用该应用程序定期 (也许每一天)，但并不是因为
使用应用程序一周只有一次的客户。

缓存通常适用于数据这就是永恒不变或更改
很少。例子包括参考信息，如产品和定价
电子商务应用程序或昂贵的共享静态资源中的信息
要构建。一些或所有这些数据可以加载到应用程序的缓存中
启动时，尽量减少对资源的需求，提高性能。它也可能是
适当的有一个后台进程，定期更新参考数据
在缓存中以确保它是到目前为止，或刷新缓存时参考
数据更改。

缓存可能不那么有用的动态数据，虽然有一些例外情况
这种考虑 (见缓存高度动态数据后来在这一节
指南的详细信息)。当原始数据定期更改要么
缓存的信息可以非常迅速地变得陈旧或保持的开销
与原始数据存储区同步缓存减少的有效性
高速缓存。请注意，缓存必须不包括完整的数据
实体。例如，如果数据项目代表一个多值的对象，如一家银行
客户名称、 地址和帐户余额，其中一些元素可能
保持静态 (名称和地址)，而其他人 (如帐户余额)
可能会更有活力。在这些情况下，它可能是有用来缓存静态
数据部分和只检索 (或计算) 的剩余信息
和当需要时。

应进行性能测试和使用情况分析，以确定是否
预填充或按需加载缓存或两者兼而有之，是
适当。这项决定应基于组合的波动性和
使用模式的数据。缓存利用率和性能分析
在应用程序遇到重物和必须尤为重要
高度可扩展。例如，在高度可扩展的情况可能是有意义
种子的缓存来减少数据存储区的负载高峰的时候。

缓存可还用于避免重复计算，因为应用程序是
运行。如果操作将数据转换或执行复杂的计算，
它可以在缓存中保存操作的结果。如果同样的计算
后来都是需要，应用程序可以简单地检索结果
到缓存。

应用程序可以修改数据保留在缓存中，但您应该考虑
作为随时可能消失的瞬态数据存储的缓存。不要存储
有价值的数据，只有在缓存中，但一定要维护信息
在原始数据存储区以及。以这种方式，如果缓存应该成为
不可用，你尽量减少数据丢失的机会。

### 高度动态的数据缓存

存储持久性数据存储区中迅速变化可以施加的信息
在系统上的开销。例如，考虑一种设备，不断报告
状态或一些其他测量。如果应用程序选择不缓存这
数据缓存的信息几乎总是会过时，然后按
同样的考虑可能是真的时存储和检索此信息
从数据存储区;在保存和获取数据所需的时间，它可能有
改变了。在这种情况，考虑存储动态的好处
直接而不是持久性数据缓存中存储的信息。如果
非关键数据并不需要审核，那么也没关系
如果偶尔的更改将会丢失。

### 管理在缓存中的数据过期

在大多数情况下，保留在缓存中的数据是保存在原始数据中的数据的副本
存储区。原始数据存储区中的数据可能会更改后缓存，造成
缓存的数据变得陈旧。很多的缓存系统使您能够配置
缓存过期数据并缩短的数据可能已过期。

当缓存的数据过期时它会从缓存中删除，应用程序必须
从 (它可以把新获取的原始数据存储区中检索数据
信息反馈到缓存)。您可以设置默认过期策略时你
配置缓存。在很多缓存服务你也可以规定到期
当您将它们存储以编程方式在缓存中的单个对象的时期
(一些缓存使您能够指定过期期限作为绝对的价值，或
作为一个滑动的值导致的项从缓存中移除，如果它不是
在指定的时间内访问。此设置将覆盖任何缓存全
过期策略，但只为指定的对象。

> [AZURE。注意] 考虑的有效期为缓存和它仔细地包含的对象。如果你修得太短，对象将过期得太快，你将减少使用缓存的好处。如果你使周期太长，你的风险数据变得陈旧。

它也是可能缓存可能会填满，如果数据允许留
长时间的居民。在这种情况下，任何要求添加新项
高速缓存可能会导致一些项目要强行移除，称为过程
驱逐。缓存服务通常驱逐至少最近使用 (LRU) 上的数据
基础，但你可以通常覆盖这项政策，并防止物品
驱逐。然而，如果你采用这种方法你的风险你缓存超过
内存，它具有可用，和应用程序，要努力尝试添加的项目
向缓存中将失败并引发异常。

某些缓存实现可能会提供额外的驱逐政策。这些
通常包括最近使用的政策 (在期望，
数据将不会需要再一次)，先进先出政策 (最早的数据是
驱逐第一)，或显式删除基于触发的事件 (如
数据被修改)。

### 无效客户端缓存中的数据

保留在客户端缓存中的数据通常被认为是外面的
服务提供数据给客户端; 主持服务
不能直接强制客户端添加或删除信息从
客户端缓存。这意味着它是可能使用的客户端
(例如，过期的政策并非配置不当的缓存
正确地实现) 继续使用过时的信息缓存
本地原始数据源中的信息已经改变了。

如果您正在构建一个 web 应用程序通过 HTTP 服务数据
连接，您可以隐式强制 web 客户端 (如浏览器或
web 代理) 来获取最新信息，如果更新的资源
通过更改该资源的 URI。Web 客户端通常使用 URI
作为客户端的缓存中的关键资源，所以更改 URI
使 web 客户端忽略任何以前缓存的版本
资源和相反获取最新的版本。

## 管理中的缓存的并发

缓存被设计来由的多个实例共享
应用程序。每个应用程序实例可以读取和修改数据
到缓存。因此，同样的并发问题过程中出现的
任何共享的数据存储区，亦适用于高速缓存。在一种情况
在应用程序需要修改数据保留在缓存中，你可能
需要确保由一个实例的应用程序进行更新
不要盲目地覆盖另一个实例所做的更改。

取决于数据的性质和碰撞的可能性
你可以采用并发两种方法之一:

- __乐观。__ 应用程序将检查看看是否缓存中的数据已更改，因为它检索的方式，立即在更新它之前。如果数据仍然是相同的可以进行更改。否则，应用程序必须决定是否要更新它 (业务逻辑，用于驱动这一决定将特定于应用程序)。这种方法是适合的情况下，更新是罕见的或不可能发生碰撞的。
- __悲观。__ 应用程序锁定缓存中的数据，当它检索它以防止另一个实例更改的数据。此过程可确保碰撞不会发生，但可能会阻止其他需要处理相同的数据的实例。保守式并发可以影响解决方案的可扩展性，应该仅用于短期操作。这种方法可能适合碰撞更可能情形，特别是如果一个应用程序更新缓存中的多个项目，必须确保始终如一地应用这些更改。

### 实施高可用性和可伸缩性，并改善性能

高速缓存不应主存储库中的数据;这是的作用
原始数据存储区填充缓存时。的
原始数据存储区是负责确保的持久性
数据。

小心不要介绍关键依赖关系的可用性
共享的缓存服务可为您的解决方案。应用程序应
能继续运作如果提供共享的缓存的服务
不可用;应用程序不应挂或等待失败
为要恢复的缓存服务。因此，应用程序必须
准备检测缓存服务的可用性和回退
对原始数据存储，如果缓存中是无法访问。的
[电路断路器模式](http://msdn.microsoft.com/library/dn589784.aspx) 对于处理这种情况下很有用。的
服务提供可以恢复缓存中，并且一旦它变成
可用的缓存可以重新填充数据读取时窗体
原始数据存储区，如战略 [高速缓存留出模式](http://msdn.microsoft.com/library/dn589799.aspx).

然而，落回原始数据存储区如果缓存
暂时不可用可能会对可伸缩性影响系统;
数据存储区时被恢复原始数据存储区
可能会请求数据，从而导致超时被淹没和
失败的连接。你应该考虑的策略是
在每个实例的应用程序中实现本地、 私有缓存
与共享缓存，应用程序的所有实例
访问。当应用程序检索的项目时，它可以首先检查
在其本地缓存中，然后共享的缓存，和最后原
数据存储区。可以使用中的数据填充本地缓存
共享的缓存或数据库如果共享缓存中不可用。
这种方法需要仔细的配置，以防止当地
关于共享的缓存，但它太过时的缓存
作为一个缓冲，如果共享的缓存是遥不可及。图 3
表明这种结构。

![本地、 私有缓存中使用共享的缓存_](media/best-practices-caching/Caching3.png)
_图 3: 使用本地、 私有缓存共享缓存_

支持较大的缓存，一些持有较长寿命的数据，
缓存服务提供了实现高可用性选项
自动故障转移，如果缓存中变得不可用。这种方法
通常涉及复制缓存的数据存储在主
二级高速缓存服务器，并切换到缓存服务器
辅助服务器，如果主服务器出现故障或连接是
失去了。减少与写到多个相关联的延迟
目的地，当数据被写入到缓存中，在主服务器上
服务器，复制到辅助服务器可能会发生
以异步方式。 这种方法，一些导致的可能性
缓存的信息可能会丢失发生故障，但
这一数据的比例应该是小相比整体
缓存的大小。

如果共享的缓存较大，可能有益于分区
缓存数据跨节点来减少争用的机会和
提高可伸缩性。许多共享的缓存支持的能力
动态添加 (和删除) 节点和平衡跨数据
分区。这种方法可能涉及藉以聚类
节点的集合提交作为客户端应用程序
无缝单缓存，但内部的数据被分散。
以下的一些节点之间预定义分配策略
其中均匀地平衡负载。的 [数据分区指导文件](http://msdn.microsoft.com/library/dn589795.aspx)
在 Microsoft 网站提供有关可能的详细信息
分区策略。

聚类分析还可以添加进一步可用性的缓存;如果
节点发生故障，仍可以访问缓存中的其余部分。
聚类是经常与复制结合使用
和故障转移;每个节点可以复制和复制副本
很快，如果联机的节点失败。

很多人读和写操作可能会涉及到单个数据
值或对象。然而，有时可能之时
有必要存储或快速检索数据量很大。
例如，播种缓存可能涉及写几百或
数以千计的项目到缓存或应用程序可能需要
从缓存检索大量相关项目
同一请求的一部分。许多大型缓存提供批处理
达到这些目的，启用客户端应用程序的的操作
打包成一个单一的请求项目大体积和
减少与执行大量相关的开销
小的请求。

## 缓存和最终的一致性

高速缓存一旁模式取决于应用程序的实例
填充有访问到最新的缓存和
一致的数据版本。在实现系统
这可能是最终一致性 (例如复制的数据存储区)
不是这样。可以修改应用程序的一个实例
数据项目，使该项目的缓存的版本无效。另一个
应用程序的实例可能会尝试读取此项目从
这将导致缓存未命中，因此它从读取数据的缓存
数据存储，并将其添加到缓存中。然而，如果数据存储
尚未与其他副本进行完全同步
应用程序实例可以阅读和填充高速缓存
旧值。

有关处理数据一致性的详细信息，请参阅
在 Microsoft 网站上的数据一致性指导页。

### 保护缓存的数据

缓存服务无论您使用，您应该考虑
如何保护从未经授权的高速缓存中的数据
访问。有两个主要问题:

- 在缓存中数据的保密性。
- 高速缓存之间流动的数据作为它的隐私和
  使用高速缓存的应用程序。

为了保护缓存中的数据，缓存服务可能实施
要求的身份验证机制的应用程序
自我辨别和授权方案是
指定哪些标识可以访问缓存中的数据和
这些恒等式的操作 (读取和写入)
允许执行。为了减少系统开销
读取和写入数据，一旦授予某个标识
写和/或读取访问权限的缓存，可以使用身份
缓存中的任何数据。如果您需要限制访问
缓存的数据的子集，您可以:

- 缓存分为分区 (通过使用不同的缓存
  服务器) 并仅授予访问权限的身份
  他们应该被允许使用，分区或
- 通过使用不同的密钥加密的数据在每个子集
  与只提供身份的加密密钥，
  应该对每个子集的访问。客户端应用程序
  可能仍然能够检索所有缓存中的数据
  但它只能够解密，它的数据
  有钥匙。

要保护的数据作为它流入/流出缓存你
依赖于网络所提供的安全功能
客户端应用程序使用连接到的基础设施
高速缓存。如果使用现场的服务器实现的高速缓存
在同一组织内承载客户端应用程序，
然后你可能不需要网络本身的隔离
采取任何额外的步骤。如果缓存位于远程和
通过公用网络 (如需要 TCP 或 HTTP 连接
作为互联网)，您应该考虑执行 SSL。

## 执行缓存与微软 Azure 的注意事项

Azure 提供 Azure 穿红衣的缓存。这是一个实现
开放的源代码作为服务运行的穿红衣的缓存
Azure 数据中心。它提供了可以缓存服务
是否从任何 Azure 应用程序访问应用程序
作为一个云服务、 网站或内部实现
蔚蓝的虚拟机。可以由客户端共享缓存
应用程序具有适当的访问键。

穿红衣的是一个高性能缓存解决方案，提供
可用性、 可伸缩性和安全性。它通常运行
作为一种服务分布在一个或多个专用机器和
试图存储尽可能多的信息，它可以在内存中
确保快速访问。这种体系结构旨在提供
低延迟和高吞吐量降低到需要
执行缓慢的 I/O 操作。

穿红衣的 Azure 缓存是兼容的许多的各种
由客户端应用程序使用的 Api。如果您有现有的
已经使用穿红衣的房地上，运行的应用程序
蔚蓝穿红衣的缓存提供缓存的快速迁移路径
在云中。

> [AZURE。注意] Azure 还提供托管缓存服务。这
  服务基于 Microsoft AppFabric 缓存引擎。它
  使您能够创建可以共享一个分布式的缓存
  由松散耦合的应用程序。缓存被驻留在上
  在 Azure 数据中心运行的高性能服务器。
  然而，此选项不再推荐使用，只是
  提供以支持现有的应用程序建立了
  要使用它。对于所有的新发展，使用 Azure 穿红衣
  相反缓存。
>
> 此外，Azure 支持在角色缓存。此功能
  使您能够创建一个缓存特定于云计算服务。
  缓存由 web 或工人的角色，实例和
  只可以访问由角色作为一部分相同的操作
  云服务部署单元 (部署单元是集
  对角色实例部署为向特定的云服务
  区域)。缓存一个集群，和的所有实例
  同一单元内各部署托管缓存中的作用
  成为同一缓存群集的一部分。现有的应用程序
  那使用在角色缓存可以继续这样做，但
  迁移到 Azure 穿红衣的缓存可能会带来更多好处。
  有关是否使用 Azure 穿红衣的缓存详细信息
  或角色缓存，访问页面
  [Azure 缓存提供最适合我?](http://msdn.microsoft.com/library/azure/dn766201.aspx) 在微软的网站。


### 穿红衣的特点

穿红衣的是更多比一个简单的缓存服务器;它提供了在分布式的内存
数据库与广泛的命令集，支持许多常见的场景，
节中所描述的用例为穿红衣的晚些时候在此缓存
文档。本节总结了一些关键特性，穿红衣
提供。

### 穿红衣的作为内存中的数据库

穿红衣的支持读取和写入操作。不像很多缓存 (因而应被视为暂时的数据存储区)，写操作可以防止系统故障或者被存储在定期在本地快照文件或仅限附加的日志文件中。所有写操作都是异步的不会阻止客户端读取和写入数据。何时穿红衣的开始运行，它从快照或日志文件中读取数据并使用它来构建在内存缓存。有关更多信息，请参见 [穿红衣的持久性](http://redis.io/topics/persistence) 穿红衣的网站。

> [AZURE。注意] 穿红衣的并不能保证所有的写操作将事件中得救
  灾难性的失败，但最坏的一面你应该只失去几秒
  价值的数据。记住，缓存不是作为
  权威数据源，并可以是应用程序的责任
  使用高速缓存以确保关键数据保存一次成功
  适当的数据存储区。更多的信息，请参阅缓存留出模式。

#### 穿红衣的数据类型

穿红衣的是键 / 值存储，哪里值可以包含简单类型或复杂的数据结构，如哈希表，列表，和设置。它支持一组原子操作这些数据类型。键可以永久或有限的时间住在哪点的关键和其对应的值自动从缓存中删除标签。关于穿红衣的键和值的详细信息，请访问页面 [穿红衣的数据类型和抽象简介](http://redis.io/topics/data-types-intro) 穿红衣的网站。

#### 穿红衣的复制和群集

穿红衣的支持主/从复制，以帮助确保可用性和维护吞吐量;一个穿红衣的主节点写入操作将被复制到一个或多个从属节点和读取的操作可以送达由船长或任何下属。如果一个网络分区，下属可以继续服务数据，然后透明地重新同步与大师时重新建立连接。有关进一步的详细信息，请访问 [复制](http://redis.io/topics/replication) 穿红衣的网站页面。

穿红衣的还提供聚类，使您能够以透明方式跨服务器将数据分成碎片和分散负载。此功能提高了可扩展性，可以添加新的穿红衣的服务器以及作为缓存的大小重新分区的数据会增加。此外，在群集中的每个服务器可以通过使用主/从复制以确保可用性群集中的每个节点之间复制。关于聚类和分片的详细信息，请访问 [穿红衣的群集教程页](http://redis.io/topics/cluster-tutorial) 穿红衣的网站。

> [AZURE。注意] 蔚蓝的穿红衣的缓存当前不支持群集。如果您想要创建一个穿红衣的群集，您可以构建您自己的自定义穿红衣的服务器。更多的信息，请参阅构建自定义穿红衣的缓存在本文档后面的部分。

### 穿红衣的内存使用

一个穿红衣的缓存主机计算机上已根据可用资源有限的大小。穿红衣的服务器配置时，您可以指定最大它可以使用的内存量。穿红衣的高速缓存中的键可以配置一个过期时间之后，它自动从缓存中移除。此功能可以帮助防止在内存缓存中充满了旧的或过时的数据。

随着内存的占用，穿红衣的可以自动退出键和它们的值由以下一系列政策。默认值是 LRU (最近最少使用)，但您也可以选择其他政策，如随意，驱逐键或关闭驱逐一共 (在这种情况下，将项目添加到缓存中会失败如果已满)。页面 [作为一个 LRU 缓存使用穿红衣](http://redis.io/topics/lru-cache) 提供更多的信息。

### 穿红衣的交易和批处理

Redis enables a client application to submit a series of operations that read and write data in the cache as an atomic transaction. All of the commands in the transaction are guaranteed to be executed sequentially and no commands issued by other concurrent clients will be interwoven between them. However, these are not true transactions as a relational database would perform them. Transaction processing consists of two stages; command queuing and command execution. During the command queuing stage, the commands that comprise the transaction are submitted by the client. If some sort of error occurs at this point (such as a syntax error, or the wrong number of parameters) then Redis will refuse to process the entire transaction and discard it. During the execution phase, Redis performs each queued command in sequence. If a command fails during this phase Redis will continue with the next queued command and it does not roll back the effects of any commands that have already been executed. This simplified form of transaction helps to maintain performance and avoid performance problems caused by contention. Redis does implement a form of optimistic locking to assist in maintaining consistency. For detailed information about transactions and locking with Redis, visit the [交易记录页](http://redis.io/topics/transactions) 穿红衣的网站。

穿红衣的也支持非事务性配料的请求。穿红衣的协议客户端用来发送命令到穿红衣的服务器使客户端作为同一请求的一部分发送一系列操作。这可以帮助减少网络上的数据包碎片。当批处理时，执行每个命令。不同的交易，如果有这些命令的格式不正确他们将被拒绝，但会执行其余的命令。也是不能保证在将在其中处理批处理中的命令的顺序。

### 穿红衣的安全

穿红衣的专注于纯粹提供快速访问数据，和设计一个受信任的环境中运行，并只能由受信任客户端访问。穿红衣的只支持有限的安全模型基于密码的身份验证 (有可能完全，删除身份验证，但不建议这样做)。所有经过身份验证的客户端共享相同的全局密码，并具有相同的资源访问。如果你需要更全面的登录安全性，您必须实现自己安全层前穿红衣的服务器和所有客户端请求应通过此附加层;穿红衣的不应该直接暴露在不受信任或未经身份验证的客户端。

你可以通过禁用它们，或重命名他们 (和特权的客户只提供新的名称) 来限制对命令的访问。

穿红衣的不直接支持任何形式的数据加密，因此所有编码必须由客户端应用程序执行。此外，穿红衣的不提供任何形式的运输安全，所以如果你需要保护的数据，因为它流经网络则应实现 SSL 代理。

有关详细信息，请访问 [穿红衣的安全](http://redis.io/topics/security) 穿红衣的网站页面。

> [AZURE。注意] 蔚蓝的穿红衣的缓存提供了它自己的安全层，通过该客户端连接;底层的穿红衣
  服务器不暴露到公共网络。

### 使用 Azure 穿红衣的缓存

Azure 穿红衣的缓存提供在 Azure 数据中心; 在承载的服务器上运行的穿红衣的服务器访问它充当门面，提供访问控制和安全。您可以通过使用 Azure 管理门户提供缓存。门户网站提供了大量预定义的配置，从 53 GB 的缓存作为专用服务支持运行 SSL 通信 (为隐私) 和主/从复制与二语习得的 99.9%的可用性，到 250 MB 缓存没有复制 (没有可用性保证) 共享的硬件上运行。

使用 Azure 管理门户也可以配置缓存，驱逐政策和控制对缓存的访问通过将用户添加到角色提供;所有者，参与者，读者。这些角色定义的成员可以执行的操作。例如，所有者角色的成员具有完全控制高速缓存 (包括安全) 和它的内容、 参与者角色的成员可以读取和写入信息在缓存中，和读者角色的成员只能从缓存中检索数据。

大多数管理任务都通过 Azure 管理门户，执行，为此许多穿红衣的标准版本中可用的行政命令是没有可供选择，包括能够以编程方式修改配置关闭穿红衣的服务器，配置额外的奴隶，或强行将数据保存到磁盘。

The Azure management portal includes a convenient graphical display that enables you to monitor the performance of the cache. For example, you can view the number of connections being made, the number of requests performed, the volume of reads and writes, and the number of cache hits versus cache misses. Using this information you can determine the effectiveness of the cache and if necessary switch to a different configuration or change the eviction policy. Additionally, you can create alerts that send email messages to an administrator if one or more critical metrics fall outside of an expected range. For example, if the number of cache misses exceeds a specified value in the last hour, an administrator could be alerted as the cache may be too small or data may be being evicted too quickly.

您还可以监视 CPU、 内存和缓存的网络使用情况。

> [AZURE。注意] 蔚蓝的穿红衣的缓存被为了纯粹作为缓存，而不是数据库。因此，它目前未实现穿红衣的持久性。

有关进一步信息和示例展示了如何创建和配置一个 Azure 穿红衣的缓存，请访问页面 [天青穿红衣的缓存圈](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) 在蔚蓝的博客。

## 缓存的会话状态和 HTML 输出

如果你构建 ASP.NET web 应用程序，使用 Azure web 角色运行，您可以保存会话状态信息和 HTML 输出在 Azure 穿红衣的缓存中。Azure 穿红衣的缓存的会话状态提供程序使您能够会话之间共享信息的不同实例的 ASP.NET web 应用程序，并在 web 农场的情况，而客户端-服务器关联性不是可用缓存会话数据在内存并不适宜将非常有用。

使用会话状态提供程序，利用 Azure 穿红衣的缓存提供多种好处，包括:

- 它可以共享之间大量的实例提供改进的可扩展性，ASP.NET web 应用程序的会话状态
- 它支持多个读取器和单个编写器，控制、 并发访问同一会话状态数据和
- 它可以使用压缩来节省内存和提高网络性能。

更多信息请访问 [ASP.NET 会话状态提供程序 Azure 穿红衣的缓存](http://msdn.microsoft.com/library/azure/dn690522.aspx) 在 Microsoft 网站上的页面。

> [AZURE。注意] 不要使用会话状态提供程序为 Azure 穿红衣的缓存对于在蔚蓝环境外部运行的 ASP.NET 应用程序。访问在 Azure 之外从缓存的延迟可以消除缓存数据的性能好处。

同样，为 Azure 穿红衣的缓存的输出缓存提供程序使您能够保存生成 ASP.NET web 应用程序的 HTTP 响应。输出缓存提供程序中使用 Azure 穿红衣的缓存可以提高呈现复杂的 HTML 输出; 应用程序的响应时间应用程序实例产生类似的反应可以使用的共享的输出片段在缓存中，而不是生成此 HTML 输出重新。 更多信息请访问 [ASP.NET 输出缓存提供程序为 Azure 穿红衣的缓存](http://msdn.microsoft.com/library/azure/dn798898.aspx) 在 Microsoft 网站上的页面。

## 构建自定义的穿红衣的缓存

The Azure Redis cache acts as a façade to the underlying Redis servers. Currently it supports a fixed set of configurations but does not provide for Redis clustering. If you require an advanced configuration that is not covered by the Azure Redis cache (such as a cache bigger than 53GB) you can build and host your own Redis servers by using Azure virtual machines. This is a potentially complex process as you may need to create several VMs to act as master and subordinate nodes if you want to implement replication. Furthermore, if you wish to create a cluster, then you will need multiple masters and subordinate servers; a minimal clustered, replication topology that provides a high degree of availability and scalability comprises at least 6 VMs organized as 3 pairs of master/subordinate servers (a cluster must contain at least 3 master nodes). Each master/subordinate pair should be located close together to minimize latency, but each set of pairs can be running in different Azure datacenters located in different regions if you wish to locate cached data close to the applications that are most likely to use it. The page [在 CentOS Linux VM 在 Azure 上运行穿红衣](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) 微软网站走过了一个示例，演示如何生成和配置作为 Azure VM 运行一个穿红衣的节点。

请注意，如果您以这种方式实现自己穿红衣的缓存，则负责监视、 管理和保护服务。

## 一个穿红衣的缓存分区

分区缓存中涉及分裂跨多台计算机的缓存。这种结构给你几个优点在使用单一缓存服务器，包括:

- 创建一个缓存，是比可以存储在单个服务器上的要大。
- 在提高可用性的服务器之间分发数据。如果一个服务器失败或变得不可访问，只有它保存的数据将不可用;还可以访问在剩余的服务器数据。高速缓存，这是不作为缓存数据关键是只有暂态数据的副本保存在数据库中，并变得不可访问的服务器上的缓存的数据可以相反缓存在不同的服务器上。
- 从而使负荷分散服务器，从而提高性能和可扩展性。
- Geolocating 数据接近用户访问它，从而减少延迟。

为缓存，最常见的分区形式是分片。在此策略中每个分区 (或碎片) 是穿红衣的缓存在其自己的权利。数据通过使用切分逻辑，可以使用各种方法来分发数据定向到特定的分区。的 [分片模式](http://msdn.microsoft.com/library/dn589797.aspx) 关于实施分片提供更多的信息。

若要实现在一个穿红衣的缓存分区，可以采用下列方法之一:

- _服务器端查询路由。_ 在这种技术，客户端应用程序发送一个请求到任何
  穿红衣的包括缓存 (可能是最接近的服务器) 的服务器。每个穿红衣的服务器存储
  描述自己持有，，它还包含有关的信息的分区的元数据
  分区位于其他服务器上。穿红衣的服务器对客户端请求进行考查，如果它
  可以在本地解析它将执行所请求的操作，否则为它会将转发
  要求到相应的服务器。这种模式，实现了穿红衣的聚类，并是
  关于所述更多详细信息 [穿红衣的群集教程](http://redis.io/topics/cluster-tutorial) 穿红衣的网站页面。穿红衣的聚类分析
  对客户端应用程序和其他穿红衣的可以向群集添加服务器是透明的
  (和重新分区的数据) 而无需您重新配置客户端。

  > [AZURE。重要] 蔚蓝的穿红衣的缓存当前不支持穿红衣的聚类。如果你希望
  实现这种方法，那么你应该生成自定义的穿红衣的缓存，如前面所述。

- _客户端的分区。_ 在此模型中，客户端应用程序中包含逻辑 (可能在
  图书馆的形态) 路线请到适当的穿红衣的服务器。这种方法
  可以用 Azure 穿红衣的缓存;创建多个 Azure 穿红衣的缓存 (每个数据之一
  分区) 和执行将请求路由到正确的客户端的逻辑
  高速缓存。如果分区方案发生更改 (如果创建了附加的 Azure 穿红衣的高速缓存，，
  例如)，客户端应用程序可能需要重新配置。

- _代理协助分区。_ 在此方案中，客户端应用程序向其发送请求
  中介代理服务，了解数据如何分区，然后路线
  对适当的穿红衣的服务器的请求。这种方法也可以用 Azure
  穿红衣的缓存;代理服务就可以实现为 Azure 的云服务。这
  方法需要额外的复杂性，实现了服务级别和
  请求可能需要更长的时间要比使用客户端的分区。

页面 [分区: 如何拆分多个穿红衣的实例中的数据](http://redis.io/topics/partitioning)
在穿红衣的网站提供有关实现分区与穿红衣的进一步信息。

### 执行穿红衣的缓存客户端应用程序

穿红衣的支持多种编程语言编写的客户端应用程序。如果您通过使用.NET 框架构建新的应用程序，推荐的方法是使用 StackExchange.Redis 客户端库。此库提供了.NET 框架对象模型，抽象出连接到一个穿红衣的服务器、 发送命令，并接收响应的详细信息。NuGet 包是在 Visual Studio 中可用。可以使用此同一库连接到一个穿红衣的 Azure 的缓存或在 VM 上承载自定义穿红衣的缓存。

要连接到一个穿红衣的服务器，您使用静态 `Connect` 方法 `ConnectionMultiplexer` 类。此方法创建的连接可用于整个生命周期中的客户端应用程序，和相同的连接可以使用多个并发线程;不要重新连接和断开连接每次你执行一个穿红衣的操作，因为这样做会降低性能。您可以指定连接参数，例如穿红衣的主机地址和密码。如果你使用的穿红衣的 Azure 缓存，这要么是密码的小学或中学的关键为生成 Azure 穿红衣的缓存通过使用 Azure 管理门户。

您已连接到穿红衣的服务器后，您可以获得作为缓存的穿红衣的数据库上的句柄。穿红衣的连接提供 `GetDatabase` 要做到这一点的方法。然后可以从缓存中检索项目并通过使用存储在缓存中的数据 `StringGet` 和 `StringSet` 方法。这些方法期望密钥作为参数，和在缓存中有一个匹配的值 (或返回的项目`StringGet`) 或将该项目添加到与此关键 (缓存`StringSet`).

根据穿红衣的服务器的位置，许多操作可能招致一些延迟，而请求传输到服务器并返回到客户端的响应。课件图书馆提供许多它公开来帮助客户端应用程序保持响应的方法的异步的版本。这些方法支持 [基于任务的异步模式](http://msdn.microsoft.com/library/hh873175.aspx) 在.NET 框架中。

下面的代码片段显示一个名为方法 `RetrieveItem` 这说明基于穿红衣和课件图书馆的缓存一旁模式实现的一个示例。该方法将一个字符串键值，并尝试从穿红衣的缓存中检索相应的项目，通过调用 `StringGetAsync` (异步版本的方法 `StringGet`).如果找不到该项目，它从基础数据源使用取自 `GetItemFromDataSourceAsync` (这是一个本地方法和不课件库的一部分) 的方法，然后添加到缓存中的使用 `StringSetAsync` 方法，以便它可以更快地检索下一次。

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

的 `StringGet` 和 `StringSet` 方法并不限于检索或存储字符串值;他们可以序列化为字节数组的任何项目。如果你需要保存你可以作为字节流序列化，并使用 StringSet 方法将其写入到缓存中的.NET 对象。同样，你可以从缓存中读取的对象，通过使用 StringGet 方法，进行反序列化作为.NET 对象。下面的代码演示了一套 IDatabase 接口的扩展方法 (穿红衣的连接的 GetDatabase 方法返回 `IDatabase` 对象)，并使用这些方法来读取和写入缓存的博客对象一些示例代码:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

下面的代码演示一个名为方法 `RetrieveBlogPost` 使用这些扩展方法来读取和写入序列化 `BlogPost` 对象缓存后缓存一旁模式:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

穿红衣的支持命令流水如果客户端应用程序发送多个异步请求。穿红衣的可以多重请求使用相同的连接，而不是接收和响应命令在严格的顺序。这种做法有助于减少延迟，更有效地利用网络。下面的代码段演示一个同时检索两个客户的详细信息。代码提交两个请求，然后执行一些其他处理 (未显示) 之前等待接收结果。缓存对象的等待方法是类似于.NET 框架 Task.Wait 的方法:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

页面 [发展为天青穿红衣的缓存](http://msdn.microsoft.com/library/azure/dn690520.aspx) 在 Microsoft 网站提供了有关如何编写客户端应用程序可以使用 Azure 穿红衣的缓存的详细信息。附加信息是可用的 [基本用法页](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) 在 StackExchange.Redis 网站和页面 [管道和多路复用器](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) 在同一网站上提供了有关异步操作和课件图书馆与穿红衣的流水线操作的详细信息。 用例节穿红衣的缓存稍后在本指南中提供的一些更先进的技术，您可以将应用于举行穿红衣的高速缓存中的数据的示例。

## 穿红衣的缓存的使用情形

The simplest use of Redis for caching concerns storing key/value pairs where the value is an uninterpreted string of arbitrary length that can contain any binary data (it is essentially  an array of bytes that can be treated as a string). This scenario was illustrated in the section Implementing Redis Cache Client Applications earlier in this guidance. You should note that keys also contain uninterpreted data, so you can use any binary information as the key, although the longer the key is the more space it will take to store, and the longer it will take to perform lookup operations. For usability and ease of maintenance design your keyspace carefully and use meaningful (but not verbose) keys. For example, use structured keys such as "customer:100" to represent the key for the customer with ID 100 rather than simply "100". This scheme enables you to easily distinguish between values that store different data types. For example, you could also use the key "orders:100" to represent the key for the order with ID 100.

除了一维的二进制字符串，穿红衣的键/值对中的值也可以容纳更结构化的信息，包括列表设置 (排序和未排序的)，和进行哈希处理。穿红衣的提供一个全面的命令集，可以操纵这些类型，以及其中的许多命令可用.NET 框架通过向应用程序客户端库等课件。页面 [穿红衣的数据类型和抽象简介](http://redis.io/topics/data-types-intro) 在穿红衣的网站提供更详细的概览，这些类型和命令，您可以使用对其进行操作。

本节概述了一些常见的使用情况下对于这些数据类型和命令。

### 执行原子和批处理操作

穿红衣的支持一系列原子获取和设置操作的字符串值。这些操作删除使用单独时可能出现的可能竞赛危害 `GET` 和 `SET` 命令。可用的操作包括:

- `INCR`, `INCRBY`, `DECR`和 `DECRBY` 其中执行原子增量和减量操作上吗
  数值数据的整数值。课件图书馆提供的重载的版本
  `IDatabase.StringIncrementAsync` 和 `IDatabase.StringDecrementAsync` 执行方法
  这些操作和返回结果值存储在缓存中。下面的代码
  代码段阐释了如何使用这些方法:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET` 其中检索与某个键关联的值并将它更改为一个新值。的
  课件图书馆使此操作可通过 `IDatabase.StringGetSetAsync`
  方法。下面的代码片断显示了此方法的一个例子。这段代码返回当前
  值与"数据: 计数器"从前面的示例中的键关联和重置值
  为此密钥回零，所有作为相同操作的一部分:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` 和 `MSET`它可以返回或更改一组字符串值作为单一操作。的
  `IDatabase.StringGetAsync` 和 `IDatabase.StringSetAsync` 重载方法支持
  这一功能，如下面的示例所示:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key/value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key/value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

在本指南中穿红衣的交易和批处理的节中所述，还可以组合成一个穿红衣的事务的多个操作。课件图书馆为通过交易提供支持 `ITransaction` 界面。您可以通过使用 IDatabase.CreateTransaction 方法，创建一个 ITransaction 对象和调用命令到交易记录通过使用提供的方法 `ITransaction` 对象。的 `ITransaction` 接口提供对一组类似的方法作为访问 `IDatabase` 接口，除了所有的方法都是异步的;他们只是执行时 `ITransaction.Execute` 调用的方法。Execute 方法所返回的值指示是否已成功创建交易记录 (true) 或失败 (false)。

下面的代码片段显示了示例，递增和递减的两个计数器作为同一事务的一部分:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

记住，穿红衣的交易不同于关系数据库中的事务。Execute 方法简单队列包括执行，交易的所有命令，如果其中的任何格式不正确，然后中止该事务。如果已成功排队的所有命令，将以异步方式运行每个命令。如果任何命令失败，其他人仍会继续处理。如果您需要验证命令已成功完成必须通过使用相应的任务，结果属性获取该命令的结果，如上面的示例中所示。读属性会阻止，直到该任务已完成的结果。

有关更多信息，请参见 [在穿红衣的交易](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) 在 StackExchange.Redis 网站上的页面。

执行批处理操作，您可以使用课件图书馆的 IBatch 接口。此接口提供对一组类似的方法访问作为 IDatabase 接口只是所有的方法都是异步。你通过使用 IDatabase.CreateBatch 方法来创建一个 IBatch 对象，然后运行批处理通过使用 IBatch.Execute 方法，如下面的示例所示。这段代码只需设置一个字符串值，并递增和递减在前面的示例中使用的相同的计数器，并显示结果:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

它是重要的是理解，不同的交易，如果在批处理中的命令失败，因为它的格式不正确的其他命令可能仍然运行;IBatch.Execute 方法不返回任何成功或失败的征兆。

### 执行消防忘记缓存操作

穿红衣的支持火忘记操作通过使用命令标志。在这种情况，客户端只是启动操作但已不感兴趣的结果并不会等待命令完成。下面的示例演示如何执行增量命令作为一个火忘记的操作:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### 自动过期钥匙

当你在穿红衣的缓存中存储的项时，您可以指定的超时后，该项目将会自动从缓存删除。您也可以查询如何更多的时间关键已通过使用过期之前 `TTL` 命令;通过使用 IDatabase.KeyTimeToLive 方法，此命令才可用到课件的应用程序。

下面的代码段显示在关键点，设置过期时间为 20 秒和查询的键的剩余寿命的示例:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

通过使用过期的命令，可用在课件图书馆为 KeyExpireAsync 方法，也可以到一个特定的日期和时间设置过期时间:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _提示:_ 您可以手动删除项目从缓存中使用 DEL 命令，可通过课件图书馆为 IDatabase.KeyDeleteAsync 方法。

### 使用标记来交叉关联的缓存的条目

穿红衣的一组是共享的单个密钥的多个项的集合。通过使用萨德命令，可以创建一套。可以通过使用 SMEMBERS 命令来检索集合中的项。用 IDatabase.SetMembersAsync 方法，课件库实现了通过 IDatabase.SetAddAsync 方法，萨德指挥和 SMEMBERS 命令。你还可以结合现有的电视使用 SDIFF (差集)、 烧结 (交集) 和 SUNION (集) 命令来创建新集。课件图书馆结合这些操作在 IDatabase.SetCombineAsync 方法中;此方法的第一个参数指定要执行的设置的操作。

下面的代码片段展示了如何设置可用于快速存储和检索相关数据项的集合。此代码使用部分实施穿红衣的缓存客户端应用程序所述的博客类型。一个博客对象包含四个字段 — — ID、 标题、 等级得分和标签的集合。第一下面的代码片断显示了用于填充的博客对象列表 C# 示例数据:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

你可以作为一套穿红衣的缓存中存储每个博客对象标记并将每组与博客的 ID 相关联。这使应用程序能够快速查找属于特定博客张贴内容的所有标记。要启用在相反的方向搜索并找到所有共享特定标记的博客帖子，您可以创建拥有博客引用标记 ID 键中的另一套:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

这些结构使您能够执行许多常见的查询效率很低。例如，您可以查找并显示所有的博客文章 1 这样的标签:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

你可以找到所有标签所共有的博客都张贴 1 和博客都帖子 2 通过执行交集操作，如下:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

你可以找到所有包含特定标记的博客文章:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### 查找最近访问的项目

A common problem required by many applications is to find the most recently accessed items. For example, a blogging site might want to display information about the most recently read blog posts. You can implement this functionality by using a Redis list. A Redis list contains multiple items that share the same key, but the list acts as a double-ended queue. You can push items on to either end of the list by using the LPUSH (left push) and RPUSH (right push) commands. You can retrieve items from either end of the list by using the LPOP and RPOP commands.  You can also return a set of elements by using the LRANGE and RRANGE commands. The code snippets below show how you can perform these operations by using the StackExchange library. This code uses the BlogPost type from the previous examples. As a blog post is read by a user, the title of the blog post is pushed onto a list associated with the key "blog:recent_帖子"穿红衣的缓存中通过使用 IDatabase.ListLeftPushAsync 方法:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

当读取更多的博客文章时，他们的标题推到相同的列表。该列表排序的序列，他们已添加;最最近读博客张贴内容向左列表的末尾 (是否同一篇博文读过不止一次，它将在列表中有多个条目)。您可以通过使用 IDatabase.ListRange 方法来显示最最近读帖子的标题。此方法将包含列表、 起始点和结束点的关键。下面的代码检索标题 10 博客 (物品从 0 到 9) 在最左边的列表的末尾:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

请注意 ListRangeAsync 不会删除项目从列表中;为此可以使用的 IDatabase.ListLeftPopAsync 和 IDatabase.ListRightPopAsync 的方法。

以防止无限期地增长列表中，可以定期修剪列表中挑选项目。下面的代码片段中移除所有但 5 最左侧项从列表中:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### 执行领导委员会

默认情况下一组中的项不会被任何特定的顺序。可以通过使用 ZADD 命令 (课件图书馆中的 IDatabase.SortedSetAdd 方法) 来创建一组有序。项目是有序使用称作分数作为命令的参数提供一个数字值。下面的代码片段将一篇博文的标题添加到排序的列表。在示例中，每个博客帖子也有分数字段包含的博客帖子排名。

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

您可以检索的博客文章标题和在使用 IDatabase.SortedSetRangeByRankWithScores 方法按升序得分得分:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE。注意] 课件库还提供了 IDatabase.SortedSetRangeByRankAsync 方法得分顺序返回的数据，但不返回的分数。

你也可以检索项目以降序的分数，并限制返回的项目数通过提供额外的参数对 IDatabase.SortedSetRangeByRankWithScoresAsync 方法。下一个示例中显示的标题和分数最高的 10 排名的博客职位:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

下一个示例使用 IDatabase.SortedSetRangeByScoreWithScoresAsync 方法，您可以使用来限制物品归还给那些属于一个给定的分数范围:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### 通过使用渠道消息

除了作为一个数据缓存，穿红衣的服务器提供消息通过高性能发布服务器/订阅机制。客户端应用程序可以订阅的频道，和其他应用程序或服务可以将消息发布到通道。订阅应用程序然后将接收这些消息，并且可以处理它们。

若要订阅通道，穿红衣的提供订阅命令。这个命令需要的应用程序将接受消息的一个或多个电视频道的名称。该课件库包括 ISubscription 接口，可以将.NET Framework 应用程序订阅和发布渠道。你通过使用 GetSubscriber 方法连接到穿红衣的服务器上，创建一个 ISubscription 对象，然后侦听信道消息通过使用此对象的 SubscribeAsync 方法。下面的代码示例演示如何订阅频道命名"消息: 部落格文章":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

订阅方法的第一个参数是频道的名称。此名称遵循相同的约定所使用的键在缓存中，并且可以包含任何的二进制数据，虽然它是最好使用相对较短的、 有意义的字符串来帮助确保良好的性能和可维护性。您还应该注意渠道所使用的命名空间是分开使用的键，所以你可以有渠道和键具有相同的名称，尽管这可能使您的应用程序代码更难维护。

第二个参数是操作委托。此委托以异步方式运行每当一条新的消息出现在通道上。此示例只显示消息 (消息将包含一篇博客文章的标题) 的控制台上。

要发布到一个通道，应用程序可以使用穿红衣的发布命令。课件图书馆提供的 IServer.PublishAsync 方法来执行此操作。下一个代码片段显示如何将消息发布到"消息: 部落格文章"通道:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

有几个点，你应该了解的发布/订阅机制:

- 多个订阅服务器可以订阅到同一通道，和他们将会接收到该通道所发布的消息。
- 订阅服务器只接收消息后他们已订阅已发布。频道未进行缓冲处理，和消息公布后，穿红衣的基础设施将邮件推送到每个订阅服务器，然后再移除它。
- 默认情况下，由订阅服务器中，发送的顺序接收消息。具有大量高活性系统中
  消息和许多订阅者和出版商，保证的消息的顺序传递可以减缓系统性能。如果
  每个消息是独立和顺序是无关紧要你可以通过穿红衣的系统，可以帮助到启用并行处理
  提高响应能力。您可以通过设置连接所使用的 PreserveAsyncOrder 达到这个课件客户端中
  为 false 订阅服务器:
  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## 相关的模式和指导

实现您的应用程序中的缓存时，下面的模式也可能有关您的应用场景:

- [高速缓存留出模式](http://msdn.microsoft.com/library/dn589799.aspx): 此模式描述了如何加载到缓存从数据存储区的数据需求。这种模式也有助于维持保留在缓存中的数据与原始数据存储区中的数据之间的一致性。
- 的 [分片模式](http://msdn.microsoft.com/library/dn589797.aspx) 提供有关实现水平分区，以帮助提高可伸缩性时存储和访问大量的数据信息。

## 更多的信息

- 的 [MemoryCache 类](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) 在 Microsoft 网站上的页面。
- 的 [微软 Azure 缓存](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) 在 Microsoft 网站上的页面。
- 的 [Azure 缓存提供最适合我?](http://msdn.microsoft.com/library/azure/dn766201.aspx) 在 Microsoft 网站上的页面。
- 的 [配置模型](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) 在 Microsoft 网站上的页面。
- 的 [基于任务的异步模式](http://msdn.microsoft.com/library/hh873175.aspx) 在 Microsoft 网站上的页面。
- 的 [管道和多路复用器](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) 页面上 StackExchange.Redis GitHub 回购。
- 的 [穿红衣的持久性](http://redis.io/topics/persistence) 穿红衣的网站页面。
- 的 [复制页面](http://redis.io/topics/replication) 穿红衣的网站。
- 的 [穿红衣的群集教程](http://redis.io/topics/cluster-tutorial) 穿红衣的网站页面。
- 的 [分区: 如何拆分多个穿红衣的实例中的数据](http://redis.io/topics/partitioning) 穿红衣的网站页面。
- 页面 [作为一个 LRU 缓存使用穿红衣](http://redis.io/topics/lru-cache) 穿红衣的网站。
- 的 [交易记录页](http://redis.io/topics/transactions) 穿红衣的网站。
- 的 [穿红衣的安全](http://redis.io/topics/security) 穿红衣的网站页面。
- 页面 [天青穿红衣的缓存圈](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) 在蔚蓝的博客。
- 页面 [在 CentOS Linux VM 上运行穿红衣](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) 在 Microsoft 网站上的 Azure。
- 的 [ASP.NET 会话状态提供程序 Azure 穿红衣的缓存](http://msdn.microsoft.com/library/azure/dn690522.aspx) 在 Microsoft 网站上的页面。
- 的 [ASP.NET 输出缓存提供程序为 Azure 穿红衣的缓存](http://msdn.microsoft.com/library/azure/dn798898.aspx) 在 Microsoft 网站上的页面。
- 页面 [发展为天青穿红衣的缓存](http://msdn.microsoft.com/library/azure/dn690520.aspx) 在蔚蓝的网站。
- 页面 [穿红衣的数据类型和抽象简介](http://redis.io/topics/data-types-intro) 穿红衣的网站。
- 的 [基本用法](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) 在 StackExchange.Redis 网站上的页面。
- 的 [在穿红衣的交易](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) 页面上 StackExchange.Redis 回购。
- 的 [数据分区指南](http://msdn.microsoft.com/library/dn589795.aspx) 在微软的网站。
