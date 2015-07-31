<properties
   pageTitle="Data partitioning guidance | Microsoft Azure"
   description="Guidance upon how to separate partitions to be managed and accessed separately."
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

# 数据分区指导

![](media/best-practices-data-partitioning/pnp-logo.png)

## 概述

在很多大型解决方案中，数据分为单独的分区，可以管理和单独访问。要慎重选择分区策略，同时尽量减少不利影响效益最大化。分区可以有助于提高可伸缩性，减少争用，并优化性能。分区的好处是使用的，也可以提供一种机制的数据除以模式;你可能年长、 更多非活动 (冷) 将数据存档到更便宜的数据存储。

## 为什么分区数据吗?

大多数的云应用程序和服务存储和检索数据作为其行动的一部分。应用程序使用的数据存储的设计可以有重大的影响，在性能、 吞吐量和可伸缩性的系统上。一个大型的系统中应用最广泛的技术是将数据分成单独的分区。

> 期限 _分区_ 在此指导是指物理上将数据划分到单独的数据存储区的过程。这不是相同的 SQL Server 表分区，这是不同的概念。

数据分区可以提供很多好处。例如，它可以为应用:

- **提高可伸缩性**.加大单个数据库系统将最终会到达一个物理硬件限制。将数据划分跨多个分区，每个宿主在单独的服务器上，允许系统几乎无限放大。
- **提高性能**.每个分区上的数据访问操作发生在较小的数据量。提供数据分区时以适当的方式，这是效率更高。影响多个分区的操作可以并行执行。每个分区可以位于附近的应用程序使用它来最小化网络延迟。
- **提高可用性**.跨多个服务器分离数据避免单点故障。如果一个服务器出现故障，或正在计划的维护，只有数据在这分区不可用。在其他分区上的操作可以继续。增加分区的数量，可以减少单个服务器故障产生的相对影响减少的百分比将不可用的数据。复制每个分区可以进一步减少影响操作的单个分区失败的机会。它还使必须不断的关键数据的分离和高度可用从低值数据 (如日志文件)，有更低的可用性要求。
- **提高安全性**.根据数据和它如何分区的性质，它可能分成不同的分区，并因此不同的服务器或数据存储的敏感和非敏感数据。然后，可以专门优化安全，为敏感数据。
- **提供业务灵活性**.划分为提供了许多机会微调操作、 行政效率最大化和成本最小化。一些例子定义不同的战略管理、 监测、 备份和恢复，并基于每个分区中的数据的重要性的其他管理任务。
- **匹配的模式使用的数据存储区**.分区允许每个分区部署在不同类型的数据存储区，基于成本和数据存储提供的内置功能。例如，大型二进制数据可以存储在 blob 数据存储中，而更结构化的数据可以保存在数据库中文档。有关详细信息请参阅 [构建一个通晓多国语言解决方案](https://msdn.microsoft.com/library/dn313279.aspx) 在模式 & 实践指南 [数据访问为高度可伸缩的解决方案: 使用 SQL，NoSQL 和通晓多国语言的持久性](https://msdn.microsoft.com/library/dn271399.aspx) 在微软的网站。

某些系统不执行分区因为它被认为是开销，而不是一种优势。这种理由的常见原因包括:

- 很多数据存储系统不支持跨分区，联接，就很难保持参照完整性在分区的系统。它是经常需要执行联接和完整性检查的应用程序代码 (分区图层)，这会导致额外的 I/O 和应用程序的复杂性。
- 维护分区并不总是是微不足道的任务。在系统中的数据是不稳定，您可能需要重新平衡分区定期以减少争用和热点。
- 一些常用的工具不能自然地与分区数据。

## 设计分区

可在不同的方式对数据进行分区: 水平、 垂直方向或功能。您选择的策略取决于分区的数据和应用程序和服务将使用的数据的要求的理由。

> [AZURE。注意] 本指南中所述的分区方案是独立于底层的数据存储技术的方式加以说明。他们可以适用于许多类型的数据存储，包括关系和 NoSQL 数据库。

### 分区策略

为数据分区的三个典型策略是:

- **水平分区** (通常称为 _切分_).在此策略中的每个分区是数据存储在其自己的权利，但所有分区都具有相同的架构。每个分区被称为 _碎片_ 并持有特定子集的数据，例如一组特定的电子商务应用程序中的客户的所有订单。
- **垂直分区**.在此策略中每个分区在数据存储区中包含项字段的子集。字段是根据他们的使用方法，例如将频繁访问的字段放在一个垂直分区并不经常访问在另一个领域的模式来划分。
- **功能分区**.在此策略中的数据进行聚合根据如何在系统中使用的每个有界的上下文。例如，电子商务系统实现分离为开具发票的业务功能和产品库存管理可能将发票数据存储在另一个分区和产品库存数据。

它是重要的是注意此处所述的三个策略可以结合起来。 他们并不互相排斥你设计分区方案时，应考虑他们所有。例如，您可能会将数据分成碎片，然后使用垂直分区进一步细分每个切分中的数据。同样，功能分区中的数据可能会分裂成碎片 (其中也可能垂直分区)。

然而，每个策略的不同要求可以提高一些你必须评估的冲突问题和平衡时设计满足您的系统的总体数据处理性能目标的分区方案。以下各节探讨每个策略的更多细节。

### 水平分区 (分片)

图 1 显示了水平分区或分片的概述。在此示例中，产品库存数据被分成碎片的基础的产品密钥。每个分片持有一个连续范围的碎片键 (A G 和 H Z)，按英文字母排序的数据。

![](media/best-practices-data-partitioning/DataPartitioning01.png)

_图 1。-水平分区 (分片) 数据基于分区键_

分片使您可以将负载分散更多的计算机;减少争用，并提高性能。你可以通过进一步添加额外的服务器上运行的碎片扩展出系统。

The most important factor when implementing this partitioning strategy is the choice of sharding key. It can be difficult to change the key after the system is in operation. The key must ensure that data is partitioned so that the workload is as even as possible across the shards. Note that different shards do not have to contain similar volumes of data, rather the important consideration is to balance the number of requests; some shards may be very large but each item is the subject of a low number of access operations, while other shards may be smaller but each item is accessed much more frequently. It is also important to ensure that a single shard does not exceed the scale limits (in terms of capacity and processing resources) of the data store being used to host that shard.

分片计划也应避免创建热点 (或热分区)，可能会影响性能和可用性。例如，使用哈希值的客户标识符而不客户名称的第一个字母将防止会导致从常见和不常见的首写字母的分布不均衡。这是一种典型的技术，有助于更均匀分散的数据分区。

The sharding key you choose should minimize any future requirements to split large shards into smaller pieces, coalesce small shards into larger partitions, or change the schema that describes the data stored in a set of partitions. These operations can be very time consuming, and may require taking one or more shards offline while they are performed. If shards are replicated, it may be possible to keep some of the replicas online while others are split, merged, or reconfigured, but the system may need to limit the operations that can be performed on the data in these shards while the reconfiguration is taking place. For example, the data in the replicas could be marked as read-only to limit the scope of any inconsistences that could otherwise occur while shards are being restructured.

> 更详细的信息和有关的这些考虑，很多的指导和良好做法技术设计数据存储的实现水平分区，请参阅 [分片模式](http://aka.ms/Sharding-Pattern)

### 垂直分区

垂直分区的最常见用途是减少 I/O 和最频繁访问与取出项目相关联的性能开销。图 2 显示示例的垂直分区，每个数据项的不同属性关押在不同的分区; 的概述比在股票或上次订购的日期卷更加频繁地访问名称、 说明和产品的价格信息。

![](media/best-practices-data-partitioning/DataPartitioning02.png)

_图 2。-通过使用其模式垂直分区数据_

在此示例中，应用程序定期查询产品名称、 描述和价格一起向客户显示产品的详细信息时。由于这两个项目都常用的在一起在一个单独的分区举行的库存水平和当从制造商中最后订购产品时的日期。这种分区方案的优点是添加相对缓慢移动数据 (产品名称、 描述和价格) 分开的更动态的数据 (库存水平和上次订购的日期)。应用程序可能发现它有益缓存内存中缓慢移动的数据，如果频繁地访问它。

此分区的策略的另一个典型方案是最大化敏感数据的安全性。例如，通过在单独的分区中存储信用卡号码和相应的信用卡安全验证码。

垂直分区还可以减少所需的数据的并发访问量。

> 垂直分区内数据存储区，部分正火的实体，把它分解从实体一级操作 _宽_ 对一组项 _缩小_ 项目。它非常适合 HBase 和卡桑德拉等面向列的数据存储。如果列集合中的数据是不可能改变的你也可以考虑使用列存储在 SQL Server 中。

### 功能分区

哪里可以确定为每个不同的业务领域或服务应用程序中的有界的方面的系统，功能分区为提高隔离和数据访问的性能提供一种技术。功能分区的另一个常见用途是用于报告目的的只读数据分开读写数据。图 3 显示功能分区的概述库存数据从客户数据的分离在哪里。

![](media/best-practices-data-partitioning/DataPartitioning03.png)

_图 3。-数据由有界的上下文或子域功能分区_

此分区的策略可以帮助降低整个系统的不同部分的数据访问争用。

## 设计分区的可扩展性

至关重要的是要考虑大小和每个分区的工作量及平衡他们，数据分布来实现最大的可伸缩性。然而，你也必须分区数据，以便它不超过单个分区存储的比例限制。

在设计可伸缩性的分区时，请执行以下步骤:

1. 分析应用程序理解数据访问模式，如每个查询所返回的结果集的大小、 频率的访问、 固有的延迟和服务器端计算处理的要求。在许多情况下，几个主要实体将要求绝大多数的处理资源。
2. 分析的基础，确定当前和未来的可扩展性目标，例如数据大小和工作量，和分发数据跨分区以满足可伸缩性的目标。在水平的分区策略中，选择适当的分片键是确保分布甚至是重要的。详细信息请参阅 [分片模式](http://aka.ms/Sharding-Pattern).
3. Make sure that the resources available to each partition are sufficient to handle the scalability requirements in terms of data size and throughput. For example, the node hosting a partition might impose a hard limit on the amount of storage space, processing power, or network bandwidth that it provides. If the data storage and processing requirements are likely to exceed these limits it may be necessary to refine your partitioning strategy or split data out further. For example, one scalability approach might be to separate logging data from the core application features by using separate data stores to prevent the total data storage requirements exceeding the scaling limit of the node. If the total number of data stores exceeds the node limit, it may be necessary to use separate storage nodes.
4. 监控下用来验证数据分布按预期系统和分区可以处理的负载强加给他们。有可能使用不符预期有可能要重新调整分区的分析。如果不这样做，可能有必要重新设计系统以获得一种平衡所需的部分。

请注意，有些云环境中分配资源，包括基础设施的界限，你应该确保你所选边界的限制提供足够空间的数据，在数据存储、 处理能力和带宽量任何预期的增长。例如，如果你使用 Azure 表存储，忙的碎片可能需要更多的资源比单个分区来处理请求 (在给定的时间内可以由单个分区处理的请求的数量限制 — — 请参见页 [Azure 存储可扩展性和性能指标](https://msdn.microsoft.com/library/azure/dn249410.aspx) 微软的网站上有关更多详细信息)。在这种情况下，碎片可能需要重新分区，以分散负载。如果总大小或这些表吞吐量超过容量的存储帐户，它可能需要创建额外的存储帐户和表散布这些帐户。如果存储帐户数目超过可用的订阅的帐户数目，它可能需要使用多个订阅。

## 设计分区的查询性能

只有使用较小的数据集和执行并行查询，往往推动查询性能。每个分区应包含整个数据集，一小部分，这种减少量可以提高查询的性能。然而，分区不是设计和适当地配置数据库供选择。例如，请确保你有必要指标到位，如果你使用的关系数据库。

设计查询性能的分区时，请执行以下步骤:

1. 检查应用程序的要求和性能:
	- 使用业务需求来确定关键总是必须快速执行的查询。
	- 监视系统以识别任何执行缓慢的查询。
	- 建立最频繁执行的查询。每个查询的单个实例可能有最小的成本，但累计消耗量的资源可能具有重大意义。它可能有利于分离到不同的分区或甚至缓存这些查询所检索的数据。
2. 分区导致缓慢的性能数据。确保您:
	- 限制每个分区的大小，这样查询的响应时间是在目标范围内。
	- 设计的方式，应用程序可以很容易找到的分区，如果你要实现水平分区分片键。这可以防止查询需要通过每个分区扫描。
	- 考虑一个分区的查询的性能上的位置。如果可能的话，尽量保持在地理上靠近的应用程序和用户访问它的分区中的数据。
3. 如果一个实体具有吞吐量和查询性能要求，使用基于该实体的功能分区。如果这是仍然不能满足要求，申请以及水平分区。在大多数情况下单个分区策略就够了，但在某些情况下它是更有效地结合这两种策略。
4. 请考虑使用异步运行的查询，在并行跨分区来提高性能。

## 设计分区的可用性

数据分区，可以提高应用程序的可用性确保整个数据集并不构成一个单点故障，可以独立管理单个数据集的子集。复制包含关键数据的分区也可以提高可用性。

在设计和实施分区，考虑影响可用性的下列因素:

- 如何数据是对关键的业务运营。一些数据可能包括关键业务信息，如发票详细信息或银行交易记录。其他的数据可能只是不太重要的业务数据，如日志文件、 性能跟踪等等。后查明每种类型的数据，请考虑:
	- 将关键数据存储在高度可用的分区与适当的后备计划。
	- 建立单独的管理和监测机制或程序为每个数据集的不同行为的转变。具有相同级别的临界性相同的分区，这样它就可以的地方数据一起备份在一个合适的频率。例如，分区举行的银行交易记录的数据可能需要比分区举行日志记录或跟踪信息更加频繁地备份。
- 如何可以管理单个分区。设计分区以支持独立的管理和维护提供了若干好处。例如:
	- 如果一个分区失败，它可以恢复独立而不会影响在其他分区中的数据访问应用程序的实例。
	- 按地理区域的数据分区可能允许定期的维护任务发生在非高峰时间为每个位置。确保分区不是太大了，从被完成在此期间防止任何计划中的维护。
- 是否要在各个分区复制关键数据。这种策略可以提高可用性和性能，虽然它也可以介绍的一致性问题。对分区中的数据与每个副本同步所做更改的时间，在此期间不同的分区将包含不同的数据值。

## 问题和注意事项

使用分区添加系统的开发与设计的复杂性。它是重要的是考虑分区作为系统设计的一个基本部分，即使该系统最初只包含单个分区。解决分区作为事后在系统启动遭受性能和可伸缩性问题时只会增加复杂性作为你可能现在有一个活的系统维护。更新系统纳入分区在这种环境，就必须不仅修改数据访问逻辑，它还涉及到迁移大量现有的数据分发跨分区，经常当用户希望能够继续使用该系统。

在某些情况下，分区认为并不重要因为初始数据集是小和可以由单个服务器很容易处理。这可能是真的在一个系统，预计不会扩展到其初始的大小，但许多商业系统需要能够扩大以用户数量的增长。这种扩张是通常伴随着增长的数据量。您还应该了解，分区并不总是大型数据存储库的函数。例如，可能几百个并发客户大量访问小数据存储区。在这种情况的数据分区可以帮助减少争用和提高吞吐量。

当你设计一个数据分区方案时，应考虑以下几点:

- 在可能的情况下，将最常见的数据库操作的数据放在一起，尽量减少跨分区数据访问操作的每个分区中。查询跨分区可以更耗时比查询只能在单个分区，但优化分区为一组查询，可能会影响其他查询集。尽量的查询时间跨分区在哪里不能避免，在分区执行并行查询和聚合应用程序内的结果。然而，这种方法不可能在某些情况下，例如当有需要从一个查询获得的结果和在下一个查询中使用此。
- 如果查询使用的相对静态的参考数据，如邮政编码表或产品列表，请考虑复制这所有分区，减少需求的单独查找操作在不同的分区中的数据。这种方法还可以减少的可能性越来越"热"的数据集将会从交通拥挤在整个系统中，尽管有与同步此引用的数据可能发生的任何更改相关联的额外费用的参考数据。
- 在可能的情况下，尽量减少跨垂直和功能分区的参照完整性的要求。在这些方案中，应用程序本身负责跨分区保持参照完整性，当数据被更新和消费。必须加入跨多个分区的数据的查询运行速度比加入只能在同一分区内的数据，因为应用程序通常需要连续根据执行查询在一个键，然后在一个外键的查询更慢。相反，考虑复制或取消正火的相关数据。为了尽量减少跨分区联接必要的查询时间，通过分区执行并行查询和加入应用程序中的数据。
- 考虑分区方案可能跨分区对数据一致性的影响。你应评估是否强一致性是实际上要求。相反，在云计算中常用的方法是执行最终一致性。分开，更新每个分区中的数据和应用程序逻辑可以负责确保所有成功完成的更新 — — 以及处理可以从查询数据，最终一致的操作时出现的不一致。有关实现最终一致性的详细信息，请参阅一致性指导。(#insertlink) #
- 考虑如何查询会找到正确的分区。如果查询必须扫描以查找所需的数据的所有分区都会显著影响性能，即使当使用多个并行查询。查询用于垂直和功能分区策略自然可以指定分区。然而，当使用水平分区 (分片)，定位项目可以难，因为每个碎片具有相同的架构。典型的解决方案为分片是保持可以用于查找特定数据项的碎片位置的地图。这张地图可能实施分片逻辑的应用程序，或由数据存储区，如果它支持透明的切分。
- 当使用水平分区策略，考虑定期重新平衡的碎片以均匀分布的数据，按大小和工作量，尽量减少热点，最大化查询性能，并解决物理存储限制。然而，这是一项复杂的任务，通常需要使用自定义工具或过程。
- 复制每个分区提供额外的保护，防止失败。如果单个副本失败，查询可以针对工作副本。
- 如果你达到生理极限的分区策略，您可能需要扩展到不同的水平可伸缩性。例如，如果分区是在数据库级别可能意味着定位或复制多个数据库分区。如果分区已经是在数据库级别，和物理限制是一个问题，这可能意味着定位或复制多个托管帐户中的分区。
- 避免访问多个分区中的数据的交易。一些数据存储实现事务的一致性和完整性的操作，修改数据，但只有当它坐落在单个分区。如果你需要事务支持跨多个分区，您可能需要执行这作为你的应用程序逻辑的一部分，因为最分区系统不提供本机支持。

所有的数据存储需要一些业务的管理和监测活动。任务的范围可以从加载数据、 备份和恢复数据、 重新组织数据，确保系统执行准确、 高效。

考虑以下的因素影响业务管理:

- 考虑你将如何落实相应的管理和操作任务时对数据分区，如备份和恢复、 存档数据，监测系统和其他管理任务。例如，在备份和还原操作期间保持逻辑上的一致性可以是一个挑战。
- 如何的数据可以是加载到多个分区，和如何新可能添加来自其他来源的数据。一些工具和实用程序可能不支持数据加载到正确的分区，分片数据运算，所以这可能需要创建或获取新的工具和实用程序。
- 如何将存档的数据，并定期 (也许是每个月)，防止过快增长的分区删除。它可能需要转换数据以匹配不同的归档模式。
- 考虑执行定期过程找到任何数据完整性问题，如引用另一个信息的一个分区中的数据，但此信息是失踪。这一进程可以尝试自动修复这些问题或引发警报给操作员手动纠正存在的问题。例如，在电子商务应用程序中，订单信息可能会举行一个分区但构成每个订单行项目可能在另一个举行。下订单的过程需要添加数据来打扰分区。如果那里此过程失败，可以行项存储为，没有相应的订单。

不同的数据存储技术通常提供其自身的特点，支持分区。以下各节总结由常用的 Azure 应用程序的数据存储区执行的选项和描述设计可以充分利用这些功能的应用程序的注意事项。

## SQL Azure 数据库的分区策略

SQL azure 数据库是关系型数据库-作为-一-服务，在云中运行。它基于 Microsoft SQL Server。关系数据库将信息分为表，和每个表包含关于实体作为一系列行的信息。每一行都包含保存为每个字段的实体数据的列。的 [SQL azure 数据库](https://msdn.microsoft.com/library/azure/ee336279.aspx) 微软网站的网页上提供了有关创建和使用 SQL 数据库的详细的文档。

## 弹性的规模与水平分区

单个的 SQL 数据库的卷的数据，它所包含的而吞吐量受建筑因素和它支持的并发连接数限制。SQL azure 数据库提供弹性扩展规模以支持水平扩展的 SQL 数据库。使用弹性规模，您可以将数据分割成碎片分散在多个 SQL 数据库，和您可以添加或删除碎片，随着你需要处理的数据量的增长和收缩。使用弹性规模也有助于减少争用跨数据库分配负载。

> [AZURE。注意] 到 2015 年 1 月预览当前弹性规模。它是一个替换为将退休的 Azure SQL 数据库联盟。通过使用就可以将现有的 SQL Azure 数据库联邦安装迁移到弹性规模 [联盟的迁移工具](https://code.msdn.microsoft.com/vstudio/Federations-Migration-ce61e9c1).或者，您可以实现自己的分片机制，如果您的应用场景不会不借本身自然给提供弹性规模的功能。

每个切分是作为 SQL 数据库实现的。一个碎片可以容纳多个数据集 (称为 _shardlet_)，和每个数据库维护描述 shardlets 它所包含的元数据。Shardlet 可以是单个数据项，或它可以是一组共享相同的 shardlet 键的项。例如，如果您是多租户应用程序中的分片数据，shardlet 键可以是租户 ID 和给定的租客的所有数据将都举行作为相同的 shardlet 的一部分。其他租户的数据会在不同的 shardlets 举行。

它是程序员的责任，要将数据集与 shardlet 键关联。一个单独的 SQL 数据库充当全球碎片映射管理器包含的数据库 (碎片)，包含了整个系统以及关于每个数据库中的 shardlets 信息列表。客户端应用程序访问的数据第一次连接到全球的碎片映射经理数据库以获取碎片-映射 (上市碎片和 shardlets)，它在本地缓存的副本。然后，应用程序使用该信息对数据请求路由到适当的碎片。此功能是隐藏在一系列的 Api 包含的 Azure SQL 数据库弹性规模客户端库中，可作为 NuGet 包。页面 [SQL azure 数据库弹性规模概述](sql-database-elastic-scale-introduction.md) 在 Microsoft 网站提供弹性规模更全面介绍。

> [AZURE。注意] 你可以复制全球碎片映射管理器数据库来降低延迟并提高可用性。如果您执行数据库通过使用一个溢价定价层，您可以配置活动的土力工程处复制不断将数据复制到不同区域中的数据库。在用户根据，并配置您的应用程序连接到此副本获取碎片映射，每个区域中创建数据库的一个副本。

> 另一种方法是使用 SQL Azure 数据同步或 Azure 数据工厂管道跨地区复制碎片映射管理器数据库。这种形式的复制定期运行，且如果碎片映射不经常更改的更适合。此外，碎片映射管理器数据库不必通过使用保费定价层创建。

弹性的尺度能提供将数据映射到 shardlets，并将其存储在碎片中的两种方案:

- 列表中碎片映射描述单个密钥和 shardlet 之间的关联。例如，在多租户系统中，每个租户的数据可能与关联一个独一无二的密钥，并存储在其自己的 shardlet。为了保证隐私和隔离 (以防止耗尽的数据存储资源提供给他人一个租户)，可以在其自己的碎片内举行每个 shardlet。

![](media/best-practices-data-partitioning/PointShardlet.png)

_图 4。-使用列表碎片映射将租客数据存储在单独的碎片_

- 范围碎片映射描述了一组连续的关键值和 shardlet 之间的关联。在多租户前面描述的示例，作为替代执行专用的 shardlets，你可以为一套的住户 (每个使用自己的密钥) 相同的 shardlet 内数据进行分组。这项计划是比第一次便宜 (租户共享数据存储资源)，但是冒着降低数据隐私和隔离。

![](media/best-practices-data-partitioning/RangeShardlet.png)

_图 5。-使用范围碎片映射将租户范围的数据存储在一个碎片_

请注意，一个单一的碎片可以包含几个 shardlets 的数据。例如，您可以使用列表 shardlets 来将数据存储为不同的非连续租户，在相同的分片。你还可以混合使用范围 shardlets 和列表 shardlets 在相同的分片，虽然他们将通过不同的映射 (全球碎片映射管理器数据库可以包含多个碎片映射) 全球碎片映射管理器数据库中解决。图 6 描述了这种方法。

![](media/best-practices-data-partitioning/MultipleShardMaps.png)

_图 6。-执行多个碎片映射_

你执行分区方案可以对您系统的性能有重大影响和也影响自杀率的碎片来添加或移除或跨碎片重新分区的数据。使用弹性比例对数据进行分区时，应考虑以下几点:

- Group data that is used together into the same shard and avoid operations that need to access data held in multiple shards. Bear in mind that with Elastic Scale a shard is a SQL database in its own right, and Azure SQL Database does not support cross-database joins; these operations have to be performed on the client-side. Also remember that with Azure SQL Database referential integrity constraints, triggers, and stored procedures in one database cannot reference objects in another, so don't design a system that has dependencies between shards. However, a SQL database can contain tables holding copies of reference data frequently used by queries and other operations, and these tables do not have to belong to any specific shardlet. Replicating this data across shards can help to remove the need to join data that spans databases. Ideally, such data should be static or slow-moving to minimize the replication effort and reduce the chances of it becoming stale.

	> [AZURE。注意] 虽然 SQL Azure 数据库不支持跨数据库联接，弹性规模 API 使您可以执行可以透明地遍历数据碎片映射所引用的所有 shardlets 都举行的跨切分查询。休息跨切分弹性规模 API 查询下来成一系列的个人查询 (一个用于每个数据库)，然后将结果合并在一起。有关更多信息，请参见 [多碎片查询](sql-database-elastic-scale-multishard-querying.md) 在 Microsoft 网站上的页面。

- Shardlets 属于相同的碎片映射中存储的数据应具有相同的架构。例如，不创建指向包含租户数据一些 shardlets 和其他 shardlets 包含产品信息列表碎片映射。这条规则不通过弹性的规模，但数据管理执行和查询变得非常复杂，如果每个 shardlet 具有不同架构。在刚才举的例子，您应该创建两个列表碎片映射;一个引用租户数据和产品信息的其他点。请记住，属于不同的 shardlets 的数据可以存储在相同的分片。

	> [AZURE。注意] 弹性的规模 API 的跨切分查询功能取决于每个 shardlet 在碎片映射包含相同的架构。
- 只支持事务性操作，举行内的相同的碎片，并不是碎片的数据。交易可以跨越 shardlets，只要它们是相同的分片的一部分。因此，如果您的业务逻辑需要执行交易，将受影响的数据存储在相同的分片或执行最终一致性。更多的信息，请参阅数据一致性指导。
- 地方附近的用户访问这些碎片中的数据碎片 (geo-查找碎片)。这一战略将有助于减少延迟。
- 避免的混合物高度活跃 (热点) 和相对不活跃的碎片。试着和负载均匀地遍布碎片。这可能需要进行哈希处理的 shardlet 键。
- 如果你是地理定位的碎片，请确保散列的键映射到 shardlets 举行的碎片存储接近访问该数据的用户。
- 目前，只有有限的一组 SQL 支持的数据类型是作为 shardlet 键; _int，bigint，低级的分组级别_ 和 _唯一标识符_.SQL _int_ 和 _bigint_ 类型对应于 _int_ 和 _长_ 数据类型在 C# 中，和有相同的范围。SQL _varbinary_ 可以使用处理类型 _字节_ 在 C# 中，和 SQL 中的数组 _uniqueidentier_ 类型对应于 _Guid_ 在.NET Framework 中的类。

顾名思义，弹性规模使系统能够添加和删除碎片，随着数据量的缩小和放大。在 Azure SQL 数据库弹性规模客户端库 Api 使应用程序能够创建和删除碎片动态 (和透明地更新碎片映射经理)，但删除碎片是破坏性的操作，也需要删除那碎片中的所有数据。如果应用程序需要将碎片拆分为两个单独的碎片或碎片结合在一起，弹性尺度能够提供一个单独的分割/合并服务。此服务运行在云托管的服务 (开发人员已创建此云托管服务)，和照顾的安全迁移数据碎片之间。有关详细信息，请参阅主题 [拆分和合并与弹性规模](sql-database-elastic-scale-overview-split-and-merge.md) 在微软的网站。

## Azure 存储分区策略

Azure 存储空间提供用于管理数据的三个抽象概念:

- 表存储，实现可伸缩结构存储。表中包含的实体，每个可以包含一组属性和值的集合。
- Blob 存储用品大对象和文件存储。
- 存储队列，支持可靠的异步消息传递应用程序之间。

表存储和 Blob 存储是本质上是键 / 值存储优化分别持有结构化和非结构化数据。存储队列提供一种机制用于构建松散耦合的、 可扩展的应用程序。在 Azure 存储帐户的上下文中创建了表存储、 Blob 存储和存储队列。Azure 存储帐户支持冗余的三种形式:

- 本地冗余存储，维护三个副本，在单个数据中心内的数据。这种形式的冗余保护针对硬件故障，但不是针对包括整个数据中心的灾难。
- 带冗余存储设有三个数据拷贝传播跨越同一区域内不同数据中心 (或两个地理位置相近的区域)。这种形式的冗余可以防止灾害发生在单个数据中心，但不能防止大规模网络断开连接，会影响整个区域。请注意，带冗余存储只有目前只供块 blob。
- 土力工程处冗余的存储，维护六个副本的数据;在一个区域 (你当地的区域)，三份副本和另一个三个副本中的一个偏远地区。这种形式的冗余提供灾难保护的最高水平。

Microsoft 已经发布 Azure 存储帐户; 可伸缩性目标请参见页 [Azure 存储可扩展性和性能指标](https://msdn.microsoft.com/library/azure/dn249410.aspx) 在微软的网站。目前，总存储帐户容量 (举行在表存储 blob 存储和未完成的消息存储队列中的数据的大小) 不能超过 500 TB。(假设 1 KB 实体、 blob 或邮件大小) 的最大请求速率是每秒 20 K。如果您的系统有可能超出这些限制，然后考虑分区负载跨多个存储帐户;单个蔚蓝订阅可以创建最多 100 个存储帐户。然而，请注意，这些限制可能会随着时间的推移改变。

## 分区的 Azure 表存储

Azure 表存储是键值存储围绕分区设计。所有实体都存储在一个分区，和分区由蔚蓝的表存储在内部进行管理。存储在表中每个实体必须提供两个部分主要包括:

- 分区键。这是确定哪个分区 Azure 表存储会将该实体的字符串值。具有相同分区键的所有实体将都存储在同一个分区中。
- 行键。这是另一个字符串值，用于标识在分区内的实体。在分区内的所有实体都通过这个键词法上，都按升序排序。分区键/行键组合必须是唯一的每个实体和长度不能超过 1 KB。

其余的实体的数据由应用程序定义的字段组成。没有特定的架构强制的和每一行可以包含一组不同的应用程序定义的字段。唯一的限制是实体 (包括分区和行的键) 的最大大小目前是 1 MB。表的最大大小为 200 TB，虽然这些数字可能会改变，未来 (检查页 [Azure 存储可扩展性和性能指标](https://msdn.microsoft.com/library/azure/dn249410.aspx) 在微软网站有关这些限制的最新信息。如果您试图存储超过这种能力的实体，则可以考虑将它们拆分成多个表;使用垂直分区和字段划分到最有可能在一起访问的组。

图 7 显示了一个示例存储帐户 (Contoso 数据) 的逻辑结构为一个虚构的电子商务应用程序。存储帐户包含三个表 (客户信息、 产品信息和订单信息)，以及每个表有多个分区。在客户信息表中的数据分区根据客户所在，其中城市和行键包含客户 id。在产品信息表中按产品类别划分为产品和行键包含的产品编号。在订单信息表中按日期，他们被置于和行键指定的命令被收到的时间划分为订单。请注意所有的数据由每个分区中的行键排序。

![](media/best-practices-data-partitioning/TableStorage.png)

_图 7。-表和分区的示例存储帐户_

> [AZURE。注意] Azure 表存储还向每个实体添加一个时间戳字段。时间戳字段由表存储维护并更新每次的实体是修改和写回分区。表存储服务使用该字段来实现乐观并发 (每次应用程序写入实体回表存储表存储服务将表存储中保存的值的实体正在编写中的时间戳值进行比较，如果二者不同实体另一个应用程序必须已修改，因为它检索和写入操作失败)。您不应修改此字段在您自己的代码，和你也不应该指定此字段的值，当您创建一个新的实体。

Azure 表存储使用分区键来确定如何存储数据。如果与以前未使用的分区键表中添加一个实体，Azure 表存储将创建一个新的分区，此实体。其他具有相同分区键的实体将存储在同一个分区中。这种机制可以有效地实现自动的扩展策略。每个分区将被存储在单个服务器在 Azure 数据中心 (以帮助确保从单个分区中检索数据的查询运行速度快)，但不同的分区可以分布在多个服务器。此外，单个服务器可以承载多个分区，如果这些分区的大小有限。

当您设计您的 Azure 表存储的实体时，应考虑以下几点:

- 分区键和行关键值的选择应在访问数据时顺便驱动。你应该选择支持您查询绝大多数的分区键/行键组合。最高效的查询将检索数据通过指定分区键和行键。指定分区键和一系列行键的查询可以满足的扫描单个分区;这是相对较快，因为数据在行键顺序举行。至少不要指定分区键的查询可能需要 Azure 表存储扫描每个分区为您的数据。

	> [AZURE。小贴士] 如果一个实体有一个自然键，然后使用它作为分区键和指定空字符串作为行键。如果一个实体有两个属性组成的复合键，分区键以及其他作为行键选择的慢变化的属性。如果一个实体有两个以上的关键属性，使用串联组成的属性提供的分区和行的键。

- 如果你定期执行查找数据使用的分区和行的键字段的查询，请考虑实施 [索引表模式](https://msdn.microsoft.com/library/dn589791.aspx).
- If you generate partition keys using a monotonic increasing or decreasing sequence (such as "0001", "0002", "0003", …) and each partition only contains a limited amount of data, then Azure table storage may physically group these partitions together on the same server. This mechanism assumes that the application is most likely to perform queries across a contiguous range of partitions (range queries) and is optimized for this case. However, this approach can lead to hotspots focused on a single server as all inserts of new entities will likely be concentrated at one or other end of the contiguous ranges. It can also reduce scalability. To spread the load more evenly across servers, consider hashing the partition key to make the sequence more random.
- Azure 表存储支持事务性操作属于同一分区的实体。这意味着，应用程序可以执行多个插入、 更新、 删除、 替换或合并操作作为一个原子单元 (需要交易不包括超过 100 个实体和有效载荷的大小不超过 4 MB 的请求)。跨多个分区的操作并不是事务性的可能需要重新执行最终一致性，数据一致性指导所述。有关表存储和交易记录的详细信息，请访问 [执行实体组交易](https://msdn.microsoft.com/library/azure/dd894038.aspx) 在 Microsoft 网站上的页面。
- 给予认真的关注，对分区键的粒度:
	- 为每个实体都使用相同的分区键将导致表存储服务来创建一个单一的大分区举行防止它从外扩和相反侧重单个服务器的负载的一台服务器上。结果，这种方法仅适用于系统管理实体的小数目。然而，这种做法并确保所有实体可以都参与实体组交易记录。
	- 使用独特的分区键，每个实体都将导致表存储服务来创建一个单独的分区，每个实体，可能会导致大量的小分区 (取决于实体的大小)。这种方法是可扩展性更强，比使用单一分区键，但实体组交易可能并不取多个实体的查询可能涉及读从多个服务器。然而，如果在应用程序执行范围查询，然后使用一个单调序列生成的分区键可能有助于优化这些查询。
	- 在一个子集的实体之间共享分区键使您可以组相关实体在同一分区。涉及相关的实体的操作可以由使用实体组交易记录，并由访问一台服务器可以满足获取一组相关实体的查询。

在 Azure 表存储分区数据的其他信息，请参阅文章 [Azure 表存储设计可扩展的分区策略](https://msdn.microsoft.com/library/azure/hh508997.aspx) 在微软的网站。

## 分区的蔚蓝 blob 存储

蔚蓝的 Blob 存储使您能够为页面 blob 举行大型二进制对象，目前达 200 GB 大小的块斑点或 1 TB (最新的信息，请访问 [Azure 存储可扩展性和性能指标](https://msdn.microsoft.com/library/azure/dn249410.aspx) 在 Microsoft 网站上的页面)。用于块 blob 在方案如流，你需要上传或下载大容量数据快速。需要随机而不是串行访问到的数据部分的应用程序使用页面 blob。

每个 blob (块或页) 是举行在 Azure 存储帐户的容器。您可以使用容器进行分组在一起，具有相同安全要求的相关的 blob，虽然这种分组是逻辑的而不是物理。容器内每个 blob 具有唯一的名称。

Blob 存储自动分区基于 blob 名称。每个 blob 举行在它自己的分区，并且与同一容器中的 blob 不共享一个分区。此体系结构使蔚蓝 blob 存储以平衡负载服务器之间透明地作为同一容器中的不同 blob 可能分布在不同的服务器。

写单个块 (块 blob) 或页面 (页面 blob) 的操作是原子的但跨块、 页或 blob 的操作不是。如果您需要确保一致性，跨块、 页和 blob 执行写操作时，您将需要拿出一个写锁定使用 blob 租赁。

蔚蓝的 blob 存储支持 60 MB 每第二次或 500 的请求，每秒的传输速率为每个 blob。如果您预计超过这些限制，并且是相对静态的 blob 数据，然后考虑通过使用 Azure 内容交付网络 (CDN) 复制 blob。有关详细信息，请参见页 [使用 CDN 的 Azure](cdn-how-to-use.md) 在微软的网站。更多的指导和注意事项，请参阅文章内容交付网络 (CDN)。

## 分区的 Azure 存储队列

Azure 存储队列使您能够实现进程之间的异步消息传递。Azure 存储帐户可以包含任意数量的队列，以及每个队列可以包含任意数量的消息。唯一的限制是中存储帐户的可用空间。单个邮件的最大大小为 64 KB。如果你需要比这更糟糕的消息，那么请考虑改用 Azure 服务总线队列。

Each storage queue has a unique name within the storage account in which it is contained. Azure partitions queues based on the name, and all messages for the same queue are stored in the same partition, controlled by a single server. Different queues can be managed by different servers to help balance the load. The allocation of queues to servers is transparent to applications and users. In a large scale application, don't use the same storage queue for all instances of the application as this approach may cause the server hosting the queue to become a hotspot; use different queues for different functional areas of the application. Azure storage queues do not support transactions, so directing messages to different queues should have little impact on messaging consistency.

Azure 存储队列可以承受高达每秒 2000年封邮件。 如果您需要处理速度比这更大的消息然后考虑创建多个队列。例如，在全球化应用程序，创建单独的存储队列在单独的存储帐户来处理在每个区域中运行的应用程序实例。

## Azure 服务总线的分片策略

Azure 服务总线使用消息代理来处理消息发送到服务总线队列或主题。默认情况下，所有消息发送到队列或主题由相同的消息代理进程都处理。这种体系结构可以将限制放在消息队列的总体吞吐量。然而，你可以还分区队列或主题时它所创造的设置 _EnablePartitioning_ 财产的队列或主题描述 _真正的_. A partitioned queue or topic is divided up into multiple fragments, each of which is backed by a separate message store and message broker. Service Bus takes responsibility for creating and managing these fragments. When an application posts a message to a partitioned queue or topic, Service Bus assigns the message to a fragment for that queue or topic. When an application receives a message from a queue or subscription, Service Bus checks each fragment for the next available message and then passes it to the application for processing. This structure helps to distribute the load across message brokers and message stores, increasing scalability and improving availability; if the message broker or message store for one fragment is temporarily unavailable, Service Bus can retrieve messages from one of the remaining available fragments.

服务总线为邮件分配到某个片段，如下所示:

- 如果消息属于一个会话，所有消息具有相同的值 _ SessionId_  属性被发送到相同的片段。
- 如果该消息不属于一个会话，但发件人已指定了值 _PartitionKey_ 属性，然后使用相同的所有邮件 _PartitionKey_ 值是发送到相同的片段。

	> [AZURE。注意] 如果 _SessionId_ 和 _PartitionKey_ 均已指定属性，则必须将其设置为相同的值否则为邮件将被拒绝。
- 如果 _SessionId_ 和 _PartitionKey_ 未指定邮件的属性，但启用了重复检测， _消息 Id_ 将使用属性。具有相同的所有邮件 _消息 Id_ 将定向到相同的片段。
- 如果邮件不包含 _SessionId，PartitionKey，_ 或 _消息 Id_ 属性，然后服务总线将消息分配给轮循机制方式中的片段。如果一个片段是不可用的服务总线将继续前进到下一个。这种方式，在消息传递基础设施的临时性故障不会导致邮件发送操作失败。

您应该考虑以下各点，决定何时以及如何，或是否为分区服务总线消息队列或主题:

- 服务总线命名空间范围内创建服务总线队列和主题。服务总线目前允许达 100 分区的队列或主题每个命名空间。
- 每个服务总线命名空间实施配额限制的资源可用，例如每个主题，并发发送数目的订阅数和接收请求每秒，可以建立的并发连接的最大数目。这些配额被记录在 Microsoft 网站上的页 [服务总线配额](https://msdn.microsoft.com/library/azure/ee732538.aspx).如果你想要超过这些值，然后创建其他命名空间具有自己的队列和主题，并跨越这些命名空间的工作。例如，在全球化应用程序，在每个区域中创建单独的命名空间和配置应用程序实例，在最近的命名空间中使用的队列和主题。
- 作为事务的一部分发送的消息必须指定分区键。这可以是 _SessionId，PartitionKey，_ 或 _消息 Id_.作为同一事务的一部分发送的所有邮件必须都指定相同的分区键，因为他们必须由相同的消息代理进程。无法将消息发送到不同的队列或主题在相同的事务。
- 您不能配置分区的队列或主题，当它变为空闲状态时，自动删除。
- 如果你正在构建跨平台或混合解决方案，你不能目前使用分区的队列和主题与高级消息队列协议 (AMQP)。

## Azure DocumentDB 分区策略

蔚蓝的 DocumentDB 是一个 NoSQL 数据库，可存储文件。DocumentDB 中的文档是一个 JSON 序列化的对象或其他数据片段中的表示。没有固定的架构是强制执行，只是每个文档必须包含一个唯一的 id。

文件被组织成集合。一个集合使您可以组合在一起的相关的文件。例如，在系统中维护的博客帖子，你可以作为文档集合中存储的每个博客帖子内容和创建为每个主题类型的集合。或者，如一个系统，使不同的作者多租户应用程序中来控制和管理自己的博客帖子，你可以分区博客作者和创建一个单独的集合，每个作者。存储空间分配给集合是弹性和可以缩小或根据需要增加。

文档集合提供对单个数据库中数据进行分区的自然机制。在内部，DocumentDB 数据库可以跨多个服务器和 DocumentDB 可能会尝试在服务器之间分配集合分散负载。实施分片的最简单方法是为每个碎片创建一个集合。

> [AZURE。注意] 每个 DocumentDB 分配资源的角度 _性能级别_.性能级别是与关联 _请求单元_ (RU) 速率极限。汝速率限制指定将保留为该集合，可供该集合由独占使用的资源的量。集合的成本取决于为该集合; 选定的性能级别更高的性能级别 (和儒率限度) 越高收费。您可以通过使用 Azure 管理门户调整集合的性能级别。有关详细信息，请参见页 [在 DocumentDB 的性能级别](documentdb-performance-levels.md) 在微软的网站。

在 DocumentDB 帐户的上下文中创建的所有数据库。一个单一的 DocumentDB 帐户可以包含多个数据库，并指定在哪个区域中创建的数据库。每个 DocumentDB 帐户还强制执行其自己的访问控制。你可以使用 DocumentDB 帐户到地球同步轨道-查找碎片 (在数据库内集合) 附近的用户需要访问它们，并强制限制，所以，只有那些用户可以连接到它们。

DocumentDB 的每个帐户都有配额限制的数据库和集合，它可以包含数量和可用的文档存储量。这些限制都可能改变，但在页面描述 [DocumentDB 限制和配额](documentdb-limits.md) 在微软的网站。它是理论上可能，如果您实施的系统所有碎片都属于同一个数据库的地方你可能达到存储容量限制的帐户。在这种情况下，您可能需要创建额外的 DocumentDB 帐户和数据库，并在这些数据库中分发的碎片。然而，即使你不太可能达到数据库的存储容量，使用多个数据库的好理由是每个数据库有其自己的用户和权限集。这种机制可用于隔离对集合基于每个数据库的访问。

图 8 说明了 DocumentDB 体系结构的高级结构。

![](media/best-practices-data-partitioning/DocumentDBStructure.png)

_图 8。-结构的 DocumentDB_

它是客户端应用程序将请求定向到适当的碎片，通常通过实施基于某些属性定义的分片键数据自身映射机制的责任。图 9 显示两个 DocumentDB 数据库，每个包含两个集合作为碎片。数据是由租户 ID 切分和住户的具体包含的数据。在单独的 DocumenDB 帐户位于同一区域作为租户它们包含的数据创建的数据库。客户端应用程序中的路由逻辑使用租户 ID 作为分片键。

![](media/best-practices-data-partitioning/DocumentDBPartitions.png)

_图 9。-实施分片使用 Azure DocumentDB_

决定如何与 DocumentDB 数据进行分区时，应考虑以下几点:

- DocumentDB 数据库的可用资源受配额限制的 DocumentDB 帐户。每个数据库可以包含大量的收藏品 (再一次，还有的限制) 和每个集合是与治理汝速率限制 (保留吞吐量) 为该集合的性能级别相关联。有关详细信息，请访问 [DocumentDB 限制和配额](documentdb-limits.md) 在 Microsoft 网站上的页面。
- 每个文档必须有一个属性，可以用于唯一标识该文件内的集合，它举行。这是不同于分片键定义的集合中保存该文档。集合可以包含大量的文件，在理论上只限于由文档 ID 的最大长度文档 ID 可达 255 个字符。
- 对文档的所有操作都执行的范围是在其中包含文档的集合的事务上下文内。如果操作失败，则回滚它已执行的工作。 接受操作文档时，所做的任何更改受到快照隔离级别。这种机制确保了如果，例如，创建一个新文档失败，另一个用户同时查询数据库的请求不会看到然后删除部分文档。
- DocumentDB 查询也是集合级别的范围。单个查询只能从一个集合中检索数据。如果您需要从多个集合中检索的数据必须查询每个集合分别和合并应用程序代码的结果。
- DocumentDB supports programmable items that can all be stored in a collection alongside documents: stored procedures, user-defined functions, and triggers (written in JavaScript). These items can access any document within the same collection. Furthermore, these items execute either inside the scope of the ambient transaction (in the case of a trigger that fires as the result of a create, delete, or replace operation performed against a document), or by starting a new transaction (in the case of a stored procedure that is executed as the result of an explicit client request). If the code in a programmable item throws an exception, the transaction is rolled back. You can use stored procedures and triggers to maintain integrity and consistency between documents, but these documents must all be part of the same collection.
- 你应该确保你打算举行一个 DocumentDB 帐户在数据库中的集合是不太可能超过定义的集合的性能级别的吞吐量限制。这些限制说明 [管理 DocumentDB 容量需求](documentdb-manage.md) 在 Microsoft 网站上的页面。如果您预计达到这些极限，考虑跨数据库中不同的 DocumentDB 帐户，以减少每个集合负载拆分集合。

## 蔚蓝搜索分区策略

要搜索的数据的能力往往是导航和勘探提供的许多 web 应用程序，使用户能够快速查找基于组合搜索条件的资源 (例如，在电子商务应用程序中的产品) 的主要方法。Azure 搜索服务 web 内容提供全文搜索功能，包括基于接近的比赛和分面导航的类型前面，建议查询等功能。这些功能的完整说明是上可用 [蔚蓝搜索概述](https://msdn.microsoft.com/library/azure/dn798933.aspx) 在 Microsoft 网站上的页面。

搜索服务将可搜索内容存储为数据库中的 JSON 文档。你定义索引，这些文档中指定的可搜索字段，并提供这些定义的搜索服务。当用户提交搜索请求时，搜索服务将使用适当的索引来查找匹配项。

为了减少争用，使用搜索服务的存储空间可以被划分成了成 1，2，3，4，6，或 12 个分区分区，每个分区可以复制和 6 倍。分区的副本数量乘以数目的产品叫做 _搜索单位_ (苏)。搜索服务的单个实例可以包含最多 36 SUs (具有 12 个分区分区的数据库只支持最多 3 副本)。你是和支付货款分配每个 SU 到您的服务。随着体积的可搜索的内容增加或的搜索请求的速率增长，你可以将 SUs 添加到现有实例的搜索服务来处理额外负载。搜索服务本身负责文件会平均分布到这两个分区，并且没有手动分区策略目前支持。

每个分区可以包含最多 1500 万文件或占据 300 GB 的存储空间 (两者以较低，取决于您的文件和索引的大小)。您可以创建达 50 索引。服务的性能将随文件，可用的索引和网络延迟影响的复杂性。平均来看，单个副本 (1SU) 应该能够处理 15 查询每秒 (检疫)，虽然您应该执行基准与您自己的数据，以获得更精确的测量的吞吐量。有关更多信息，请参见 [限制和约束 (蔚蓝搜索 API)]( https://msdn.microsoft.com/library/azure/dn798934.aspx) 在 Microsoft 网站上的页面。

> [AZURE。注意] 你可以将一组有限的数据类型存储在可搜索的文件;字符串、 布尔值、 数字数据、 日期时间数据和一些地理数据。更多详细信息，请参见 [支持的数据类型 (蔚蓝搜索)]( https://msdn.microsoft.com/library/azure/dn798938.aspx) 在 Microsoft 网站上的页面。

控制力如何 Azure 搜索服务分区，每个服务实例的数据是很有限的。然而，在全球环境中，您可能能够提高性能并降低延迟和争用进一步通过分区服务本身使用以下策略之一:

- 在每个地理区域，创建的搜索服务实例，并确保客户端应用程序针对最近的可用实例。这种战略需要跨服务的所有实例，及时地复制到可搜索内容的任何更新。
- 创建两层的搜索服务;在每个区域，其中包含由用户在该区域中最频繁访问的数据，全球服务包含的所有数据的本地服务。用户可以直接向当地服务 (用于快速但有限的结果)，或 (对于速度较慢但更完整的结果) 的全球服务的请求。这种方法是最适合要搜索的数据显著区域变化时。

## Azure 穿红衣的缓存的分区策略

蔚蓝的穿红衣的缓存提供了在云计算基于穿红衣的键/值数据存储的共享缓存服务。正如其名称所示，Azure 穿红衣的缓存作为缓存的解决方案和所以应只用于举行瞬态数据，而不是作为永久数据存储区;利用 Azure 穿红衣的缓存的应用程序应该能够继续运作如果缓存中不可用。蔚蓝的穿红衣的缓存支持小学和中学复制，以提供高可用性，但目前限制到 53 GB 的最大高速缓存大小。如果你需要比这更多的空间，则必须创建附加的高速缓存。更多信息请访问 [微软 Azure 缓存](http://azure.microsoft.com/services/cache/) 在 Microsoft 网站上的页面。

Partitioning a Redis data store involves splitting the data across instances of the Redis service. Each instance constitutes a single partition. Azure Redis Cache abstracts the Redis services behind a façade and does not expose them directly. The simplest way to implement partitioning is to create multiple Azure Redis caches and spread the data across them. You can associate each data item with an identifier (a partition key) that specifies in which cache it should be stored. Your client application logic can use this identifier to route requests to the appropriate partition. This scheme is very simple, but if the partitioning scheme changes (if additional Azure Redis Caches are created, for example), client applications may need to be reconfigured.

Native Redis (not Azure Redis Cache) supports server-side partitioning based on Redis clustering. In this approach, the data is divided evenly across servers by using a hashing mechanism. Each Redis server stores metadata that describes the range of hash keys that the partition holds, and also contains information about which hash keys are located in the partitions on other servers. Client applications simply send requests to any of the participating Redis servers (probably the closest server).The Redis server examines the client request and if it can be resolved locally it will perform the requested operation, otherwise it will forward the request on to the appropriate server. This model is implemented by using Redis clustering, and is described in more detail on the [穿红衣的群集教程](http://redis.io/topics/cluster-tutorial) 穿红衣的网站页面。穿红衣的聚类是透明的客户端应用程序，和其他穿红衣的服务器可以无需您重新配置客户端添加到群集 (和重新分区的数据)。

> [AZURE。重要] 蔚蓝的穿红衣的缓存当前不支持穿红衣的聚类。如果你想要实现这种方法与 Azure 然后您必须执行穿红衣的服务器通过在一套天蓝色的虚拟机上安装穿红衣和手动对其进行配置。页面 [在 CentOS Linux VM 在 Azure 上运行穿红衣](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) 微软网站走过了一个示例，演示如何生成和配置作为 Azure VM 运行一个穿红衣的节点。

页面 [分区: 如何拆分多个穿红衣的实例中的数据](http://redis.io/topics/partitioning) 在穿红衣的网站提供有关实现分区与穿红衣的进一步信息。本节的其余部分假定您正在执行客户端或代理辅助分区。

决定如何与 Azure 穿红衣的缓存中的数据进行分区时，应考虑以下几点:

- 蔚蓝的穿红衣的缓存不打算作为永久数据存储区，所以无论你执行的分区方案应用程序代码应该准备接受不在缓存中发现了数据和已从其他地方检索。
- 将经常访问的数据放在同一分区。穿红衣的是强大的键值存储，提供了几个高度优化机制结构化数据，从简单的字符串 (实际上，二进制数据的长度为 512 MB) 到聚合类型列表 (即可以作为队列和堆栈)、 集 (序和无序) 和哈希值 (可以相关的字段分组在一起，如代表对象中的字段的项目) 等。聚合类型使您能够将许多相关的值关联使用同一密钥;一个穿红衣的键标识列表、 集，或哈希值，而不是它包含的数据项目。这些类型都可用，Azure 穿红衣的高速缓存和由描述 [数据类型](http://redis.io/topics/data-types) 穿红衣的网站页面。例如，部分的电子商务系统，可以追踪客户的订单，每个客户的详细信息可以存储在通过使用客户 ID 关联的穿红衣的散列每个哈希能为客户的订单 Id 的集合。单独的穿红衣的一组控制不住的订单，再结构作为哈希表，键控利用订单 id。 图 10 显示了这种结构。请注意，穿红衣的未实现任何形式的引用完整性，所以它是开发人员的责任，以维护客户和订单之间的关系。

![](media/best-practices-data-partitioning/RedisCustomersandOrders.png)

_图 10。-建议结构中穿红衣的存储记录客户订单和他们的详细资料_

> [AZURE。注意] 在穿红衣的所有二进制数据值 (比如穿红衣的字符串) 和密钥可以包含达 512 MB 的数据，因此在理论一把钥匙可以包含几乎任何信息。然而，你应该采取的钥匙，是描述性的数据的类型和标识的实体，但这不是过长一致的命名约定。常用的方法是使用窗体"的实体键_类型: ID"，如"客户: 99"以指示与 ID 99 客户的关键。

- 您可以实现垂直分区通过将相关的信息存储在不同的聚合在同一数据库中。例如，在电子商务中一个穿红衣的散列中的产品信息和更少的常用的详细的信息，在另一个访问应用程序通常可以存储。这两个哈希值也会作为一部分的关键，例如可以使用相同的产品 ID"产品:_nn_"在哪里 _nn_ 是产品信息和"产品的产品 ID_详细信息: _nn_"对于详细的数据。这种策略可以帮助减少的大多数查询都可能要检索的数据量。
- Repartitioning a Redis data store is a complex and time-consuming task. Redis clustering can repartition data automatically, but this facility is not available with Azure Redis Cache. Therefore, when you design your partitioning scheme, you should endeavor to leave sufficient free space in each partition to allow for expected data growth over time. However, remember that Azure Redis Cache is intended to cache data temporarily, and that data held in the cache can have a limited lifetime specified as a time-to-live (TTL) value. For relatively volatile data the TTL should be short, but for static data the TTL can be a lot longer. You should avoid storing large amounts of long-lived data in the cache if the volume of this data is likely to fill the cache. You can specify an eviction policy that causes Azure Redis Cache to remove data if space is at a premium.

	> [AZURE。注意] 蔚蓝的穿红衣的缓存使您能够指定 (从 250 MB 到 53 GB) 缓存的最大大小通过选择适当的定价层。然而，一旦创建了 Azure 穿红衣的高速缓存，您不能增加 (或减少) 其大小。

- 穿红衣的批次和交易不能跨越多个连接，所以应该在同一个数据库 (碎片) 举行受批处理或事务的所有数据。

	> [AZURE。注意] 穿红衣的事务中的操作序列是不一定的原子。验证所包含的事务的命令并执行之前, 排队，如果错误发生在这一阶段整个队列将被丢弃。然而，一旦交易已成功提交，将依次执行队列中的命令。如果任何命令失败该命令只被中止;在队列中的所有以前和后续命令执行。如果您需要执行原子操作。有关详细信息，请访问 [交易](http://redis.io/topics/transactions) 穿红衣的网站页面。

- 穿红衣的支持有限的数量的原子操作，并支持多个键和值的类型的唯一操作是 MGET (它返回集合的键指定列表的值) 和尺度 (这可以存储为键指定列表的值的集合)。如果你需要使用这些操作，必须在同一数据库内存储的尺度和 MGET 命令所引用的键/值对。

## 再平衡分区

由于系统成熟和句型的用法变得更好的理解，有可能它可能需要调整的分区方案。这可能是交通的由于个别分区吸引大量不相称越来越热，导致过度的争用。此外，你可能会低估了一些分区中的数据量导致你要接近这些分区中存储容量的极限。不管原因是什么，它是有时需要平衡分区以更均匀地分配负载。

在某些情况下，不公开的方式，在其中的数据分配到服务器的数据存储系统可以自动平衡分区可用资源的限度内。在其他情况下，再平衡是分为两个阶段的管理任务:

1. 确定新的分区策略，并确定哪些分区可能需要被拆分 (或可能组合)，以及如何通过设计新的分区键分配给这些新的分区的数据。
2. 受影响的数据从旧分区计划迁移到新的分区集。

> [AZURE。注意] 到服务器的 DocumentDB 集合的映射是透明的但你仍然可能达到 DocumentDB 帐户的存储容量和吞吐量限制，在这种情况下您可能需要重新设计你的分区方案和迁移数据。

根据数据存储技术和数据存储系统的设计，你可能能够将数据分区时他们正在使用 (在线迁移) 之间迁移。如果这是不可能，您可能需要使受影响的分区暂时不可用，而数据是搬迁 (脱机迁移)。

## 离线迁移

离线迁移可以说是最简单的方法，因为它减少了发生; 争用的机会正在迁移的数据不应更改，而不是移动和重组。

从概念上讲，这一过程包括以下步骤:

1. 马克的碎片脱机，
2. 拆分/合并和数据移动到新的碎片，
3. 验证数据，
4. 带来的新的碎片在线，
5. 删除旧的碎片。

若要保留一些可用性，它可能标记为只读的步骤 1，而不是使其不可用的原始碎片。这将允许应用程序读取数据，而它被移动，但不是更改它。

## 在线迁移

在线迁移是执行更复杂，而是对用户的中断，在整个过程中，数据仍然可用较小。过程类似于使用的离线迁移，只是原始的碎片未标记为脱机 (步骤 1)。根据粒度的迁移过程 (按项目或碎片的碎片)，客户端应用程序中的数据访问代码可能需要处理读和写数据在两个地点 (原碎片和新碎片) 举行

一种解决方案，支持在线迁移的示例，请参见 [弹性比例的拆分/合并服务](sql-database-elastic-scale-overview-split-and-merge.md)在 Microsoft 网站上在线记录。

## 相关的模式和指导

考虑实施数据一致性的策略时，也可能与您的应用场景相关以下模式:

- 数据一致性指南页，可在 Microsoft 网站上，描述用于在分布式环境中如云的一致性维护策略。
- 的 [数据分区指导](https://msdn.microsoft.com/library/dn589795.aspx) 微软网站的网页上提供设计分区以符合各项标准的分布式解决方案的总体概况。
- 的 [分片模式](https://msdn.microsoft.com/library/dn589797.aspx)描述在 Microsoft 网站上，总结了一些分片数据的常用策略。
- 的 [索引表模式](https://msdn.microsoft.com/library/dn589791.aspx) 所述上微软网站的说明如何创建辅助索引数据。这种方法使应用程序能够快速通过使用未引用的主键的一个集合的查询检索数据。
- 的 [实例化的视图模式](https://msdn.microsoft.com/library/dn589782.aspx) 讨论了在微软网站描述如何生成汇总数据，支持快速查询操作的预填充的视图。此方法可以在分区的数据存储区中，很有用，如果分区包含正在汇总的数据分布在多个站点。
- 这篇文章的内容交付网络 (CDN) 上配置和使用 Azure 的 CDN 提供其他指导。

## 更多的信息

- 的 [SQL azure 数据库](https://msdn.microsoft.com/library/azure/ee336279.aspx) 微软网站的网页上提供描述如何创建和使用 SQL 数据库的详细的文档。
- 页面 [SQL azure 数据库弹性规模概述](sql-database-elastic-scale-introduction.md) 在 Microsoft 网站提供弹性的规模全面介绍。
- 主题 [拆分和合并与弹性规模](sql-database-elastic-scale-overview-split-and-merge.md) 微软网站包含有关使用拆分/合并服务管理弹性规模碎片信息。
- 页面 [Azure 存储可扩展性和性能指标](https://msdn.microsoft.com/library/azure/dn249410.aspx) 微软网站文件 Azure 存储空间的电流大小和吞吐量限制。
- 的 [执行实体组交易](https://msdn.microsoft.com/library/azure/dd894038.aspx) 微软网站的网页上提供有关在 Azure 表存储中存储的实体执行事务性操作的详细的信息。
- 这篇文章 [Azure 表存储设计可扩展的分区策略](https://msdn.microsoft.com/library/azure/hh508997.aspx) 微软网站包含有关在 Azure 表存储数据分区的详细的信息。
- 页面 [使用 CDN 的 Azure](cdn-how-to-use.md) 微软网站描述如何复制数据在 Azure Blob 存储通过使用 Azure 内容交付网络 (CDN) 举行。
- 页面 [预览版本 DocumentDB 限值](documentdb-limits.md) 微软网站描述当前的限制和配额微软 DocumentDB。
- 页面 [DocumentDB 容量和性能管理](documentdb-manage.md) 微软网站包含有关如何 Azure DocumentDB 分配给数据库的资源信息。
- 的 [蔚蓝搜索概述](https://msdn.microsoft.com/library/azure/dn798933.aspx) 微软网站的网页上提供充分说明可用与 Azure 搜索服务的功能。
- 的 [限制和约束 (蔚蓝搜索 API)](https://msdn.microsoft.com/library/azure/dn798934.aspx) 在 Microsoft 网站上的页面包含对每个实例的 Azure 搜索服务能力的信息。
- 的 [支持的数据类型 (蔚蓝搜索)](https://msdn.microsoft.com/library/azure/dn798938.aspx) 在 Microsoft 网站上的页总结了您可以使用可搜索文件和索引中的数据类型。
- 的 [微软 Azure 缓存](http://azure.microsoft.com/services/cache.md) 微软网站的网页上提供导论 Azure 穿红衣的缓存。
- 页面 [分区: 如何拆分多个穿红衣的实例中的数据](http://redis.io/topics/partitioning) 上穿红衣的网站提供有关执行与穿红衣的分区信息。
- 页面 [在 CentOS Linux VM 在 Azure 上运行穿红衣](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) 微软网站走过了一个示例，演示如何生成和配置作为 Azure VM 运行一个穿红衣的节点。
- 的 [数据类型](http://redis.io/topics/data-types) 穿红衣的网站网页上描述了可用的穿红衣和 Azure 穿红衣的缓存的数据类型。
