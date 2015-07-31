<properties
   pageTitle="Availability checklist | Microsoft Azure"
   description="Checklist that provides guidance for availability concerns during design."
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

# 可用性检查表

![](media/best-practices-availability-checklist/pnp-logo.png)

## 应用程序设计

- **避免任何单点故障。** 所有的组件、 服务、 资源和计算实例应作为多个实例，以避免单点故障影响可用性部署。这包括身份验证机制。将应用程序配置使用多个实例，和要自动检测故障，请求重定向到非失败的情况下在哪里平台不做这自动设计。
- **分解每个不同的 SLA 工作量。** 如果服务组成的关键和不太关键的工作量，以不同的方式对其进行管理，并指定的服务功能和数量的实例来满足他们的可用性要求。
- **最小化和理解服务依存关系。** 使用在可能的情况下，不同服务的数量降到最低，确保你了解所有的功能和服务的依赖项在系统中存在。这包括这些依赖关系的性质和故障的影响，或在每一个在整个应用程序性能降低。微软至少保证 99.9%的可用性对于大多数服务，但这意味着，每个额外的服务，应用程序可能会依赖减少了二语习得的您的系统的整体可用性百分之零点一。
- **设计任务和消息，以便在可能的情况是等幂的** 这样重复的请求不会导致问题。例如，一个服务可以作为处理由作为生产者的其他部分系统作为请求发送的消息的消费者。如果消费者失败之后处理该消息，但在承认，它已经被处理之前，生产者可能提交的消费者的另一个实例可以处理重发请求。为此，消费者和他们所进行的操作应该是幂等以便重复先前执行的操作并不会使结果无效。这可能意味着检测重复的邮件，或通过使用乐观的方法来处理冲突确保一致性。
- **使用实现高可用性的关键交易消息代理。** 用于启动任务或访问远程服务的许多方案使用消息传递应用程序和目标服务之间传递指令。为了获得最佳性能，应用程序应该能够发送消息，然后返回来处理更多请求，而无需等待答复。为了保证消息的传递，消息传递系统应提供高可用性。服务总线消息队列实现 _至少一次_ 语义学，藉以每条发送到队列的消息将不会丢失，虽然可能在某些情况下提供重复拷贝。如果消息处理是幂等 (见前一项) 然后重复传递应该不成问题。
- **设计应用程序以妥善降级** when reaching resource limits, and take appropriate action to minimize the impact for the user. In some cases, the load on the application may exceed the capacity of one or more parts, causing reduced availability and failed connections. Scaling can help to alleviate this, but it may reach a limit imposed by factors such as resource availability or cost. Design the application so that, in this situation, it can automatically degrade gracefully. For example, in an ecommerce system, if the order-processing subsystem is under strain (or has even failed completely), that part of the system can be temporarily disabled while allowing other functionality (such as browsing the product catalog) to continue. It might be appropriate to postpone requests to a failing subsystem, for example still enabling customers to submit orders but saving them to a queue or other safe storage mechanism for later processing when the orders subsystem is available again.
- **优雅地处理快速的突发事件。** 大多数应用程序需要处理不同的工作负载随着时间推移，如山峰在早上在业务应用程序中或在一个电子商务网站发布新的产品的时候第一件事。自动缩放可以帮助处理的负载，但它可能需要一些附加的实例，来在线处理请求的时间。防止突发和意外爆发活动压倒性通过设计将队列请求到其使用的服务的应用程序和降解优雅地当队列是接近满负荷。确保有足够的性能和容量可用条件非突发排水队列和处理未完成的请求。有关更多信息，请参见 [基于队列的载荷平衡模式](https://msdn.microsoft.com/library/dn589783.aspx).

## 部署和维护

- **部署多个实例的每个服务的角色。** 微软做可用性保证服务的创建和部署，但这些保证只是有效的如果您部署的每个角色服务中至少两个实例。这使一个角色要不可用，而其他仍处于活动状态。这是特别重要，如果你需要将更新部署到一个活的系统，而无需中断客户的活动;实例可以记录在案，分别升级而别人继续在线。
- **宿主应用程序中多个数据中心。** 虽然极不可能的它是可能的整个数据中心以脱机通过事件如自然灾害或树干互联网失败。重要的业务应用程序应该将承载在一个以上的数据中心，以提供最大的可用性。这有一个额外的好处，它可以减少延迟对于本地用户，并提供灵活的额外机会更新应用程序时。
- **自动化和测试部署和维护任务。** 分布式应用程序由必须工作在一起的多个部分组成。部署因此应该可以自动使用测试并行之有效的机制，如脚本和部署应用程序更新和验证配置，并自动执行部署过程。自动化的技术也应该用于执行的全部或部分应用程序更新。要测试所有这些进程充分确保错误不会导致额外的停机时间是至关重要的。所有部署工具必须都有适当的安全限制，以保护已部署的应用程序;定义并仔细落实部署策略，尽量减少人为干预的必要性。
- **请考虑使用临时和生产平台的功能** 这些在哪里可用。例如，使用 Azure 云服务临时和生产环境允许应用程序从一个到另一个立即通过虚拟 IP 地址交换 (VIP 交换) 切换。然而，如果你更喜欢舞台上处所，或同时部署应用程序的不同版本和逐步迁移的用户，您可能无法使用 VIP 交换操作。
- **应用配置更改而无需回收** 实例在可能的情况。在许多情况下，可以更改 Azure 应用程序或服务的配置设置，而无需重新启动的作用。角色公开可以处理以检测配置更改并将它们应用到应用程序中的组件的事件。然而，一些对核心平台设置的更改将需要重新启动的作用。在构建时组件和服务，最大化可用性，减少宕机时间设计他们接受对配置设置的更改而不需要整个应用程序重新启动。
- **在更新期间用于零停机时间升级域。** Azure 计算单位，如 web 和工作者角色分配来升级域。在一起升级域组角色实例，以便滚动更新发生时，每个角色在升级域停止、 更新，并重启反过来以尽量减少对应用程序的可用性的影响。您可以指定在部署服务时，应为服务创建多少升级域。

	> [AZURE。注意] 角色也遍布在整个故障域，每个是合理地独立于其他故障域的服务器机架、 电源和冷却的规定，以尽量减少影响所有角色实例失败的机会。这种分布自动发生，你无法控制它。

- **配置蔚蓝的虚拟机的可用性设置。** Placing two or more virtual machines in the same availability set guarantees that these virtual machines will not be deployed to the same fault domain. To maximize availability, you should create multiple instances of each critical virtual machine used by your system and place these instances in the same availability set. If you are running multiple virtual machines that serve different purposes, create an availability set for each virtual machine and add instances of each virtual machine to each availability set. For example, if you have created separate virtual machines to act as a web server and a reporting server, create an availability set for the web server and another availability set for the reporting server. Add instances of the web server virtual machine to the web server availability set, and add instances of the reporting server virtual machine to the reporting server availability set.

## 数据管理

- **利用数据复制** 通过本地和地理冗余。在 Azure 存储中的数据自动复制，以保护基础设施出现故障时丢失，可以配置此复制的一些因素。例如，可能在多个地理区域 (称为读访问全球冗余存储或 RA GRS) 复制数据的只读副本。注意，使用 RA GRS 招致额外的费用，请参阅 [Azure 存储定价](http://azure.microsoft.com/pricing/details/storage/) 微软网站的详细信息页面。
- **使用开放式并发和最终一致性** 在可能的情况。交易可导致性能降低和大大减少可用性块访问资源通过锁定 (悲观并发性)。这些问题可以成为分布式系统中尤为严重。在许多情况下，精心设计和技术，如分区可以尽量减少冲突更新发生的机会。凡数据复制，或只将最终一致的数据，但通常远远大于弊对可用性的使用事务以确保即时一致性的影响从单独更新存储中读取。
- **使用定期备份和时间点还原**, and ensure it meets the Recovery Point Objective (RPO). Regularly and automatically back up data that is not preserved elsewhere, and verify you can reliably restore both the data and the application itself should a failure occur. Data replication is not a backup feature because errors and inconsistencies introduced through failure, error, or malicious operations will be replicated across all stores. The backup process must be secure to protect the data in transit and in storage. Databases or parts of a data store can usually be recovered to a previous point in time by using transaction logs. Microsoft Azure provides a backup facility for data stored in Microsoft Azure SQL Database. The data is exported to a backup package on Microsoft Azure blob storage, and can be downloaded to a secure on-premises location for storage.
- **启用高可用性选项可保持一个穿红衣的缓存的辅助拷贝。** 使用时穿红衣的缓存，选择标准选项保持辅助副本的内容。有关详细信息，请参见页 [在 Azure 穿红衣的缓存中创建一个缓存](https://msdn.microsoft.com/library/dn690516.aspx) 在微软的网站。

## 错误和失败

- **介绍了概念的超时。** 服务和资源可能会变得不可用，导致请求失败。确保您应用的超时值适用于每个服务或资源以及它们进行访问的客户端 (在某些情况下，它可能是适当的特定实例的客户端，具体取决于上下文和客户端执行其他操作允许更长的超时)。很短的超时可能会导致过度重试操作对于服务和资源，有相当大的延迟，但是很长超时可以导致阻塞如果大量的请求进行排队等待服务或资源作出回应。
- **重试失败的操作引起的瞬时性故障。** 设计访问所有的服务和资源在哪里他们本身不支持自动连接重试重试策略。使用策略，包括增加延迟作为数目的增加，防止超载的资源，允许它优雅地恢复并处理排队的请求而失败重试间隔。不断重试很短滞有可能使问题恶化。
- **停止发送请求，以避免级联故障** when remote services are unavailable. There may be situations where transient or other faults, ranging in severity from a partial loss of connectivity to the complete failure of a service, take much longer than expected to return to normal. Additionally, if a service is very busy, failure in one part of the system may lead to cascading failures, and result in many operations becoming blocked while holding onto critical system resources such as memory, threads, and database connections. Instead of continually retrying an operation that is unlikely to succeed, the application should quickly accept that the operation has failed, and gracefully handle this failure. You can use the Circuit Breaker pattern to reject requests for specific operations for defined periods. The [电路断路器模式](https://msdn.microsoft.com/library/dn589784.aspx)微软网站的网页上提供更多的细节。
- **撰写或回退到多个组件** 以减轻脱机或不可用的特定服务的影响。设计应用程序以利用没有影响操作和现有连接的多个实例，在可能的情况。使用多个实例和分发请求他们之间，和检测，避免将请求发送到失败的实例，以最大化可用性。
- **回退到不同的服务或工作流** 在可能的情况。例如，如果 SQL 数据库写入失败，在 blob 存储中临时存储数据和提供一个设施，重播在 blob 存储到 SQL 数据库中写入的当该服务可用。在某些情况下，操作失败可能有替代的行动，使应用程序继续工作甚至当一个组件或服务失败。如果可能，检测故障，并请求重定向到其他服务，可以提供合适的替代功能，或以备份或减少可以维持核心业务的主要服务处于脱机状态时的功能情况。

## 监测和灾难恢复

- **为可能的失败和失败事件提供丰富的仪表** 要向操作人员情况的报告。为失败，有可能但尚未发生，提供足够的数据，使操作人员确定的原因，减轻这种情况，并确保系统仍然可用。已经发生的故障，应用程序应该向用户返回相应的错误信息，但尝试继续运行，尽管与缩减功能。在所有情况下，监测系统应捕获全面的细节，使操作人员有效的快速的恢复，并在必要时为设计师和开发人员修改系统以防止情况再发生。
- **通过实施检查功能来监视系统健康。** 随着时间的推移，不明显，直到它失败可能会降低应用程序的性能与健康。防止这样的一种方法是实现探头或检查定期从外部应用程序执行的功能。这些检查可以是一样简单象测量响应时间整个应用程序、 应用程序的各个部分、 各个服务的应用程序使用，或单个组件。检查功能可以执行流程来确保他们产生有效的结果，测量滞后时间和房间或床位，并从系统中提取信息。
- **定期测试所有故障切换和备用系统** 为确保他们可用并正常运行。对系统和操作的更改可能会影响故障转移和回退功能，但直到主系统发生故障或超载的影响可能不会检测。它是很好的做法需要弥补生活的问题，在运行时之前对其进行测试。
- **测试的监测系统。** 自动故障切换和备用系统的手动可视化的系统健康状况和性能，通过使用仪表板都取决于监测和仪器仪表运行正常。如果这些元素失败，遗漏重要信息，或者报告不准确的数据，然后操作员可能没有意识到该系统是不健康或不及格。
- **长时间运行的工作流的进度跟踪** 和失败时重试。长时间运行的工作流通常由多个步骤组成。在设计这些类型的工作流时确保每一步是独立和可以重试，以减低整个工作流需要回滚的机会或多个补偿事务需要执行。监视和管理长时间运行工作流的进度实施模式如调度代理主管。有关更多信息，请参见 [调度程序代理主管模式](https://msdn.microsoft.com/library/dn589780.aspx) 在 Microsoft 网站上的页面。
- **制定灾难恢复计划。** 确保有一个记录，商定，和充分的测试从任何类型的故障，可能使部分或全部主系统复苏计划不可用。定期测试程序并确保所有的操作人员熟悉的过程。
