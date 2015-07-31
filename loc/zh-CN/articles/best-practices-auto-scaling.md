<properties
   pageTitle="Autoscaling guidance | Microsoft Azure"
   description="Guidance upon how to autoscale to dynamically allocate resources required by an application."
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

# 自动缩放指导

![](media/best-practices-auto-scaling/pnp-logo.png)

## 概述
自动缩放是动态分配的应用程序匹配性能要求并满足服务级别协议 (Sla) 同时运行时成本最小化所需的资源的过程。随着工作量的增加，应用程序可能需要额外的资源，使其能够及时地执行其任务。随着需求放缓，资源可以是解除分配，尽量减少成本，同时仍保持足够的性能和满足 sla 要求。
自动缩放宽松管理开销通过减少需操作人员不断地监视系统的性能和决策有关添加或删除资源时的弹性云托管环境的利用。
> 自动缩放适用于所有的应用程序，而不仅仅是计算资源使用的资源。例如，如果您的系统使用消息队列来发送和接收信息，它可以创建附加队列，当它的规模。

## 类型的缩放
缩放比例通常采用两种形式之一 — — 垂直和水平缩放:

- **垂直缩放** (常简称为 _向上和向下缩放_) requires that you modify the hardware (expand or reduce its capacity and performance), or redeploy the solution using alternative hardware that has the appropriate capacity and performance. In a cloud environment, the hardware platform is typically a virtualized environment. Unless the original hardware was substantially overprovisioned, with the consequent upfront capital expense, vertically scaling up in this environment involves provisioning more powerful resources, and then moving the system onto these new resources. Vertical scaling is often a disruptive process that requires making the system temporarily unavailable while it is being redeployed. It may be possible to keep the original system running while the new hardware is provisioned and brought online, but there will likely be some interruption while the processing transitions from the old environment to the new one. It is uncommon to use autoscaling to implement a vertical scaling strategy.
- **水平方向上缩放** (常简称为 _缩放和在_) 要求部署上额外的解决方案或更少的资源，通常是商品资源，而不是高性能系统。虽然这些资源调配解决方案可以继续运行而不会中断。资源调配过程完成后，元素包含解决方案的副本可以部署在这些额外的资源和提供。如果需求下降，更多的资源可以回收后使用他们的元素已被干净地关闭。许多基于云的系统，包括微软 Azure 支持自动化的这种形式的缩放比例。

## 自动缩放战略实施
实现自动缩放策略通常涉及以下组件和流程:

- 仪器仪表和监测系统、 应用程序、 服务和基础设施的水平是捕捉的关键指标，比如响应时间、 队列长度、 CPU 利用率和内存使用情况。
- 决策的逻辑，可以评估监测缩放比例因子对预定义的系统阈值或时间表并就是否缩放或不做出决定。
- 负责开展与缩放系统等配置或解除配置资源关联的任务的组件。
- 测试、 监控和调优的自动缩放战略，以确保它按预期的运行。

大多数基于云计算的环境中，例如微软 Azure 提供内置的自动缩放机制的地址的常见情况。如果环境或你使用的服务不提供必要的自动缩放功能，或如果您有超出其能力的极端自动缩放要求，一个自定义的实现可能需要收集业务和系统度量，分析它们找出有关数据，并相应然后缩放资源。

## 实现自动缩放的注意事项
自动缩放不是即时的解决方案。只需将资源添加到系统或运行多个实例的过程并不能保证系统的性能将会提高。 设计自动缩放策略时，请考虑以下几点:

- The system must be designed to be horizontally scalable. Avoid making assumptions about instance affinity; do not design solutions that require that the code is always running in a specific instance of a process. When scaling a cloud service or web site horizontally, do not assume that a series of requests from the same source will always be routed to the same instance. For the same reason, design services to be stateless to avoid requiring a series of requests from an application to always be routed to the same instance of a service. When designing a service that reads messages from a queue and processes them, do not make any assumptions about which instance of the service handles a specific message because autoscaling could start additional instances of a service as the queue length grows. The [竞争消费者模式](http://msdn.microsoft.com/library/dn568101.aspx) 描述如何处理这种情况。
- 如果该解决方案实现了一个长时间运行的任务，设计此任务以支持外扩和结垢。没有适当照顾，这样一项任务可以防止一个流程实例关闭干净时系统扩展的或它可能会丢失数据，如果强行终止该进程。理想情况下，重构一个长时间运行的任务和分解成较小的离散块执行处理。的 [管道和过滤器模式](http://msdn.microsoft.com/library/dn568100.aspx) 提供一个示例的如何你可以做到这一点。或者，你可以实现一个检查点机制记录状态信息的任务按固定时间间隔，并保存此状态中可以访问的任何实例运行任务的进程的持久存储。这种方式，如果这一进程是关机，它正在从事的工作可以恢复从最后一个检查点通过使用另一个实例。
- 当后台任务运行在单独计算实例，如辅助角色的云服务中承载应用程序时，您可能需要扩展使用不同的缩放策略的应用程序的不同部分。例如，您可能需要部署额外的 UI 计算实例而不增加背景计算实例的数量或相反的这。如果你提供不同级别的服务 (如基本和特优服务包)，你可能需要比那些基本服务包更加积极放大优质服务封装的计算资源，满足 sla 的要求。
- 请考虑使用的用户界面和后台计算实例交流作为您的自动缩放策略的驱动程序的队列的长度。这是最好的指标的不平衡或当前负载和后台任务的处理能力之间的区别。
- 如果您将您的自动缩放策略基于衡量业务流程，如每小时或复杂的交易，平均执行时间的订单数目的计数器确保您完全理解这些类型的计数器的结果与实际计算容量需求之间的关系。它可能需要扩展多个组件或计算单元在响应变化业务进程计数器。  
- 若要防止系统尝试过，向外扩展，避免与运行数以千计的实例相关的成本，考虑限制可以自动添加的实例的最大数目。大多数自动缩放机制允许您指定一个规则的实例的最小值和最大数目。此外，优雅地考虑有辱人格系统提供如果已部署实例的最大数目的功能和系统仍然超载。
- 保持在心灵那自动缩放可能不是最适当的机制，以处理工作量的突然爆发。它需要时间来提供并启动新实例的服务或将资源添加到系统，和峰值可能通过这些额外的资源已可用的时间。在这种情况下，可能更好，节气门的服务。有关更多信息，请参见 [节流模式](http://msdn.microsoft.com/library/dn589798.aspx).
- 相反，如果你需要有能力处理所有请求量波动迅速，成本不是一个主要因素的时候，请考虑使用积极的自动缩放策略启动其他实例速度更快，或通过使用启动的实例来满足最大值足够数量的计划的策略加载前负荷预计。
- The autoscaling mechanism should monitor the autoscaling process and log the details of each autoscaling event (what triggered it, what resources were added or removed, and when). If you create a custom autoscaling mechanism, ensure that it incorporates this capability. The information can be analyzed to help measure the effectiveness of the autoscaling strategy, and tune it if necessary—both in the short term as the usage patterns become more obvious, and over the long term as the business expands or the requirements of the application evolve. If an application reaches the upper limit defined for autoscaling, the mechanism might also alert an operator who could manually start additional resources if the situation warrants this. Note that, under these circumstances, the operator may also be responsible for manually removing these resources after the workload eases.

## 在蔚蓝的解决方案自动缩放
有几个选项用于配置自动缩放为蔚蓝的解决方案:

- **蔚蓝的自动缩放**.此功能支持最常见的结垢情况根据日程安排，并 (可选) 触发缩放操作基于运行时的标准 (如处理器利用率，队列长度，或建在和自定义计数器)。你可以简单的自动缩放为配置策略的解决方案迅速和容易地通过使用 Azure 管理门户，和你可以使用 Azure 监测服务管理库配置自动缩放的规则与精细的控制。有关详细信息，请参阅 [Azure 监测服务管理库](#the-azure-monitoring-services-management-library).
- **自定义解决方案** 基于诊断、 监测、 和 Azure 服务管理功能。例如，您可以使用 Azure 的诊断，自定义代码，或 [Azure 系统中心管理包](http://www.microsoft.com/download/details.aspx?id=38414) 持续地监视性能的应用程序;和 [Azure 服务管理其他 API](http://msdn.microsoft.com/library/azure/ee460799.aspx)， [微软 Azure 管理库](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Libraries)或 [自动缩放应用程序块](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx) 要缩放出和中。触发一个缩放操作的度量值可以是任何内置或自定义的计数器或你在应用程序内执行其他仪器。然而，一个自定义的解决方案不是简单的执行，并应考虑只是否没有以前的办法可以满足你的要求。请注意，自动缩放应用程序块是一个开放的采购的框架，不直接受 Microsoft 支持。
- **第三方服务** 如 [Paraleap AzureWatch](http://www.paraleap.com/AzureWatch) 这使您能够扩展基于时间表、 服务负载和系统性能指标、 自定义规则和不同类型的规则组合的解决方案。

选择哪种自动缩放解决方案采取时, 请考虑以下几点:

- 使用内置的自动缩放功能的平台，如果他们可以满足您的要求。如果不是，仔细考虑是否你真的需要更复杂的缩放功能。额外要求以外的内置的自动缩放功能提供一些例子可能包括更多的粒度控制，不同的方法来检测缩放比例、 缩放整个订阅、 缩放其他类型的资源，和更多的触发事件。
- Consider if you can predict the load on the application with sufficient accuracy to depend only on scheduled autoscaling (adding and removing instances to meet anticipated peaks in demand). Where this is not possible, use reactive autoscaling based on metrics collected at runtime to allow the application to handle unpredictable changes in demand. However, it is typically appropriate to combine these approaches. For example, create a strategy that adds resources such as compute, storage, and queues based on a schedule of the times when you know the application is most busy. This helps to ensure that capacity is available when required without the delay encountered when starting new instances. In addition, for each scheduled rule, define metrics that allow reactive autoscaling during that period to ensure that the application can handle sustained but unpredictable peaks in demand.
- 通常难以理解度量和容量要求，特别是当最初部署应用程序之间的关系。愿意提供一点额外的容量，在开始的时候，然后监视和优化自动缩放规则将能力更接近实际的负载。

### 使用 Azure 自动缩放
蔚蓝的自动缩放使您能够配置扩展和扩展解决方案的选项。蔚蓝的自动缩放可以自动添加和删除的 Azure 云服务网站和工作者角色，Azure 移动服务和 Azure 网站应用程序实例。它也可以启用自动缩放的启动和停止实例的 Azure 虚拟机。蔚蓝的自动缩放战略包括两部分的因素:

- 基于时间表的自动缩放，可以确保附加实例可用于用途与预期的峰值相吻合，可以扩展在一旦高峰时间过去。这使您能够确保你有足够的实例已在运行而无需等待系统对负载作出反应。
- 在最后一小时或积压的解决方案处理 Azure 存储空间或服务总线队列中的消息的平均 CPU 利用率等因素对反应的基于度量的缩放。这允许应用程序分别从定时自动缩放规则，以适应需求计划外或不可预见的变化作出反应。

使用 Azure 自动缩放时，请考虑以下几点:

- 你自动缩放策略结合计划和基于度量的缩放。你可以指定两种类型的服务规则，以便应用程序扩展如期和响应负载的变化。
- 你应该配置 Azure 自动缩放规则，然后监视应用程序随着时间的推移的性能。使用监测的结果来调整系统级表如果必要的方式。但是，请牢记那自动缩放不是瞬时的过程 — — 它需要时间来度量，如平均 CPU 利用率超过 (或低于) 反应指定的阈值。
- Autoscaling rules that use a detection mechanism based on a measured trigger attribute (such as CPU usage or queue length) use an aggregated value over time, rather than the instantaneous values, to trigger an autoscaling action. By default, the aggregate is an average of the values. This prevents the system from reacting too quickly, or causing rapid oscillation. It also allows time for new instances that are auto-started to settle into running mode, preventing additional autoscaling actions from occurring while the new instances are starting up. For Cloud Services and Virtual Machines, the default period for the aggregation is 45 minutes, so it can take up to this period of time for the metric to trigger autoscaling in response to spikes in demand. You can change the aggregation period by using the SDK, but be aware that periods of less than 25 minutes may cause unpredictable results (see [自动缩放 CPU 百分比与监测服务管理图书馆的 Azure 云服务](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/) 更多的信息)。为 Azure 网站，平均的周期短很多，允许新的实例，可在约五分钟后改平均触发措施中。
- 如果您配置自动缩放使用 SDK，而不是门户网站，您可以指定更详细的时间表，在这期间的规则是积极。你也可以创建您自己的度量标准，并使用它们，有或没有任何现有的自动缩放规则。例如，你可能希望使用替代的计数器，如每秒或平均内存可用性，请求的数量或使用测量特定业务流程的自定义计数器。有关详细信息，请参阅 [Azure 监测服务管理库](#the-azure-monitoring-services-management-library).
- 时自动缩放 Azure 虚拟机，您必须部署数目等于最大数量的虚拟机的情况下你将允许自动缩放以开始。这些实例必须是相同的可用性设置的一部分。虚拟机自动缩放机制不会创建或删除实例的虚拟机;相反，您配置的自动缩放规则将启动和停止适当数量的这些实例。有关更多信息，请参见 [自动缩放运行 Web 角色、 工作者角色或虚拟机的应用程序](cloud-services-how-to-scale.md#autoscale).
- 如果不能启动新实例，也许因为订阅的最大值已达到 (如内核使用虚拟机服务时的最大数目)，或者一个错误发生在启动期间，门户网站可能会显示自动缩放操作成功。然而，随后 **ChangeDeploymentConfiguration** 事件显示在门户中将只显示请求服务启动，并将没有事件，以指示它已成功完成。
- 在 Azure 自动缩放，你可以使用 web 门户 UI 将 SQL 数据库实例和队列的资源链接到计算服务实例。这允许您更轻松地访问单独的手动和自动缩放配置选项为每个链接的资源。有关更多信息，请参见 [如何: 将资源链接到云服务](cloud-services-how-to-manage.md#linkresources) 在页面中如何管理云服务和页 [如何扩展应用程序](cloud-services-how-to-scale.md).
- 当您配置多项政策和规则时，还有他们可以互相冲突的可能性。蔚蓝的自动缩放使用以下的冲突解决规则来确保始终有足够数量的情况下运行:
  - 操作规模始终优先于在业务规模。
  - 当放大操作冲突，启动的实例数的最大升幅的规则优先。
  - 当缩放操作冲突中，启动的实例数的最小跌幅的规则优先。

<a name="the-azure-monitoring-services-management-library"></a>

### Azure 监测服务管理库
用来配置 Azure 自动缩放精细的控制和访问不是通过门户网站提供的功能，你可以使用服务管理 API。此 API 被访问作为一个 REST 的 Web API，直接或通过 Azure 监测服务管理库。

蔚蓝的自动缩放是由指定为云服务角色，虚拟机的可用性集，Azure 网站 (作为服务器农场在网络空间) 或 Azure 移动服务自动缩放配置文件配置的。每个配置文件，其中一个目标可以有达 20，指示:

- 当它是用于 (使用复发或某一固定的日期间隔)，
- 允许的数量的实例 (最小值、 最大值和默认数量)
- 哪些自动缩放规则实际上处于

门户网站允许配置的一组固定的型材，本质上区分白天/夜晚和平日和周末型材，用一双的规模规则基于 CPU 利用率或队列长度。通过改用服务管理 API 你可以配置更细粒度的适用性日期对于配置文件，并且指定达十规则与触发器基于任何度量可用到 Azure 监测服务。

自动缩放规则由指示时应用的规则，和规模行动，指示要在目标的配置上执行的变更触发器组成。在写作的时候，唯一受支持的操作是增加或减少的实例计数。

自动缩放规则触发器都基于可用的度量。配置度量值是从适当的来源，从周期性地采样，自动缩放配置中定义。从活动的配置文件的每个规则计算时，在触发器中指定的度量值聚合在时间和跨实例 (如果适用)，和此聚合比较阈值，以指示该规则是否适用。随着时间的推移有效聚合是平均 (默认值)，最小值、 最大值，最后，总，和计数。有效聚合了实例是平均 (默认值)，最大值和最小。

可用触发器指标是 Azure 存储和服务总线队列长度，标准性能计数器由 Azure 诊断，出版和发表每个角色或虚拟机的任何自定义性能计数器。在云服务解决方案中，默认情况下处理除了那些可用的性能计数器时，您必须更改服务在 UI 中的"最低限度"到"详细"的监控级别设置。

有关更多信息，请参见:

- 监测的 SDK [类库](http://msdn.microsoft.com/library/azure/dn510414.aspx)
- [如何配置性能计数器](http://msdn.microsoft.com/library/azure/dn535595.aspx)
- [自动缩放操作](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [添加自动缩放设置](http://msdn.microsoft.com/library/azure/dn510372.aspx)
- [自动缩放 CPU 百分比与监测服务管理图书馆的 Azure 云服务](http://rickrainey.com/2013/12/15/auto-scaling-cloud-services-on-cpu-percentage-with-the-windows-azure-monitoring-services-management-library/)
- [如何使用 Azure 监测服务管理库来创建自动缩放比例规则](http://blogs.msdn.com/b/cie/archive/2014/02/20/how-to-use-windows-azure-monitoring-services-management-library-to-create-an-autoscale-rule.aspx)

## 相关的模式和指导
下面的模式和指导也可能与有关您的应用场景时执行自动缩放:

- [节流模式](http://msdn.microsoft.com/library/dn589798.aspx).此模式描述了如何应用程序可以继续运作，满足服务级别协议，当需求增加置于极端负载资源。节流可以用自动缩放防止被淹没系统，虽然系统扩展的出。
- [竞争消费者模式](http://msdn.microsoft.com/library/dn568101.aspx).此模式描述了如何实现一个可以处理来自任何应用程序实例消息的服务实例池。自动缩放可以用于启动和停止服务实例来匹配预期的工作量。这种方法使系统能够处理多个消息同时优化吞吐量，提高了可扩展性和可用性，并平衡负载。
- [仪器仪表和遥测指导](http://msdn.microsoft.com/library/dn589775.aspx).仪器仪表及遥测对收集的信息，可以驱动自动缩放过程至关重要。

## 更多的信息
- [如何扩展应用程序](cloud-services-how-to-scale.md)
- [自动缩放运行 Web 角色、 工作者角色或虚拟机的应用程序](cloud-services-how-to-manage.md#linkresources)
- [如何: 将资源链接到云服务](cloud-services-how-to-manage.md#linkresources)
- [扩展链接的资源](cloud-services-how-to-scale.md#scalelink)
- [Azure 监测服务管理库](http://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Monitoring)
- [Azure 服务管理其他 API](http://msdn.microsoft.com/library/azure/ee460799.aspx)
- [自动缩放操作](http://msdn.microsoft.com/library/azure/dn510374.aspx)
- [Microsoft.WindowsAzure.Management.Monitoring.Autoscale Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.management.monitoring.autoscale.aspx)
- 的 [自动缩放应用程序块](http://msdn.microsoft.com/library/hh680892%28v=pandp.50%29.aspx) 在 MSDN 上的文件和关键场景。
