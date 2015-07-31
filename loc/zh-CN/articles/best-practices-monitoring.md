<properties
   pageTitle="Monitoring and diagnostics guidance | Microsoft Azure"
   description="Best practices for monitoring distributed applications in the cloud."
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

# 监测和诊断指导

![](media/best-practices-monitoring/pnp-logo.png)

## 概述
分布式应用程序和服务在云中运行是软件的由其本身的性质，包含许多移动部件中复杂部分。在生产环境中，它是系统的重要的是系统的能够跟踪用户利用你的系统，跟踪资源利用率的方式和一般监测的健康和你性能。此信息可以用作辅助诊断的手段来检测和纠正问题，而且还要帮助发现潜在问题和防止其发生。

## 监测和诊断方案
监视使您能够深入了解如何井系统正在运作，和是在维护服务质量目标的关键。监测数据收集一些常见的情况包括:

- 确保该系统仍然健康。
- 跟踪系统和其组成元素的可用性。
- 维护性能，以确保系统的吞吐量作为工作增加的容量不意外降低。
- 确保系统满足任何 Sla 同意客户。
- 保护隐私和安全的系统、 用户和他们的数据。
- 跟踪审计执行的操作或管理目的。
- 监测系统和帮助的现货趋势可能导致的问题，如果他们得不到解决的日常使用。
- 跟踪问题的发生，从初次报告通过分析可能的原因、 整改，由此软件更新和部署。
- 跟踪的操作和调试软件版本。

> [AZURE。注意] 此列表不是很全面。本文档集中在这些场景作为最常见的情况进行监测，但是很可能有其他较不常见或特定于您自己的环境。

以下各节描述这些场景中更多的细节。按以下格式，讨论了每个场景的信息:

- 该方案的简要概述。
- 对这种情况下的典型要求。
- 支持的方案中和可能的这一信息来源所需原料检测数据。
- 如何这份原始数据可以分析和组合以生成有意义的诊断信息。

## 健康监测
一个系统是健康的如果它正在运行，并且能够处理请求。健康监测的目的是健康的生成快照的当前系统状况，使您可以验证该系统的所有组件都按照预期的方式都运行。

### 健康监测要求
如果系统的任何部分被认为是不健康，操作员应发出警报很快 (在几秒钟内)。操作人员应该能够确定哪些部分系统运作正常，和哪些部分遇到的问题。可以通过使用一个交通灯系统; 强调系统健康红色的是不健康 (系统已停止)，黄色的部分健康 (系统以缩减功能运行)，和绿色的完全健康。

一个全面的健康监测系统使得操作员能够通过系统查看子系统和组件的健康状况向下钻取。例如，如果整个系统被描绘成部分健康，经营者应该能够放大并确定哪些功能是当前不可用。

### 数据源、 仪器仪表、 数据采集要求
支持健康监测所需的原始数据可以生成的结果:

- 跟踪的用户请求的执行。可以使用此信息来确定哪些请求已成功、 有失败的和每个请求需要多长时间。
- 综合用户监测。这一过程模拟由用户执行的步骤，并遵循一系列预定义的步骤。每个步骤的结果，应捕获。
- 日志记录异常、 错误和警告。由于跟踪语句嵌入到应用程序代码，以及从任何服务引用的系统事件日志中检索信息，可以捕获此信息。
- 监测系统使用的任何第三方服务的健康。这可能需要检索和分析提供的这些服务的健康数据，此信息可以有多种格式。
- 终结点的监测。这种机制所述详细监测断面的可用性。
- 收集环境性能的信息，如 CPU 利用率背景或 (包括网络) 的 I/O 活动。

### 健康数据分析
健康监测的主要重点是迅速指示系统是否正在运行。如果检测到的一个重要组成部分，是不健康的 (它未能回应一系列连续的 ping 命令，例如)，热分析中的立即数据可以触发警报。经营者然后可以采取适当的纠正措施。

A more advanced system might include a predictive element that performs a cold analysis over recent and current workloads to spot trends and determine whether the system is likely to remain healthy or whether additional resources are going to be required. This predictive element should be based on critical performance metrics, such as the rate of requests directed at each service or subsystem, the response times of these requests, and the volume of data flowing into and out of each service. If the value of any metric exceeds a defined threshold the system can raise an alert to enable an operator or auto-scaling (if available) to take the preventative actions necessary to maintain system health. These actions might involve adding resources, restarting one or more services that are failing, or applying throttling to lower-priority requests.

## 可用性监视
一个真正健康的系统需要的组件和子系统组成的系统可用。可用性监视密切相关的健康监测，但健康监测提供即时查看的当前系统的健康状况，而可用性监视与跟踪系统及其组件生成统计信息有关的系统运行时间的可用性有关。

在许多系统中，某些组件 (如数据库) 都配置了内置冗余严重故障或连接丢失的情况下允许快速故障切换。理想情况下，用户不应意识到发生了此类故障，但从可用性监测的角度讲它是有必要收集尽可能多的信息尽可能尝试和确定原因并采取纠正措施以防止再次发生此类故障。

要跟踪可用性所需的数据可能是依赖于大量的低水平的因素，其中很多可能是特定于应用程序、 系统和环境。一个有效的监测系统捕获的可用性数据对应于这些低级的因素，然后聚合他们给系统整体图片。例如，在电子商务系统中，业务功能，可以使客户下订单可能取决于在哪个订单详细信息的存储和处理这些订单支付货币交易的支付系统的存储库。顺序放置部分的系统可用性因此是可用性的存储库和支付子系统功能。

### 可用性监测要求
运营商也应该能够查看的每个系统和子系统，历史的可用性和使用此信息来找出任何趋势，可能会导致一个或多个子系统，以定期失败 (做服务启动失败的对应处理高峰的一天某一时刻吗?)

以及提供可用性立即和历史视图或否则为的每个子系统，一个监控解决方案还应该能够的快速警报操作员时一个或更多服务失败或用户是无法连接到服务。这不是简单的监测每个服务，但亦正研究如果这些行动失败尝试与服务通信时由每个用户正在执行的操作。在某种程度上，一定程度的连接失败是正常的可能是由于瞬态错误，但是它可能有用，使系统能够连接到指定的子系统的故障出现在一个特定时期内的数目会生成警报。

### 数据源、 仪器仪表、 数据采集要求
与健康监测、 支持可用性监视所需的原始数据可以产生合成用户监测和记录任何例外、 断层和可能发生的警告。此外，可以从执行终结点监测获得可用性数据。应用程序可以公开一个或多个健康终结点，每个测试访问到系统内的功能领域。监控系统可以 ping 每个终结点后定义的计划，并收集结果 (成功或失败)。

必须记录所有超时和网络连接故障和连接重试次数。所有的数据应该是时间戳。

<a name="analyzing-availablity-data"></a>
### 分析可用性数据
检测数据必须聚合和相关支持以下类型的分析:

- 立即提供的系统和子系统。
- 可用性故障率的系统和子系统。理想情况下，操作员应能够关联失败与具体活动;当系统发生故障时，发生了什么?
- 历史观的系统或跨任何任何子系统的故障率指定的时间段和加载系统 (例如用户请求数) 时出现故障。
- 系统或任何子系统不可用的原因。例如，服务未运行，丢失的连接，连接超时，而且已连接，但返回的错误。

您可以通过使用下面的公式在一段时间计算的一种服务的可用性百分比:

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

这对于二语习得的目的 (很有用[SLA 监控](#SLA-monitoring) 详细介绍了更多稍后在本指南)。定义 _停机时间_ 依赖的服务。例如，视觉工作室在线将停机时间定义为期间其间客户试图连接到服务的时间超过 120 秒的所有基本的读和写操作失败后在该期间内建立连接。

## 性能监视
作为系统被安置在越来越多的压力之下作为用户增加的数量和大小的数据集，这些用户访问增长，可能失败的一个或多个组件成为可能。通常情况下，组件故障之前性能降低。如果你能检测你可以采取主动的措施来补救这种情况的减少。

系统性能是取决于若干因素。每个因素是通常用测定关键性能指标 (Kpi)，如数目数据库每秒事务数或在给定时间内成功地提供服务的网络请求的数量。一些这些 Kpi 可作为特定性能的措施，而其他人可能来自指标组合。

> [AZURE。注意] 确定性能很差或好需要你明白的该系统应能够运行的性能级别。这需要观测系统，虽然它在典型的负载下运作和在一段时间，对于每个 KPI 捕获数据。这可能需要在测试环境中运行模拟负载系统与系统在部署到生产环境之前收集适当的数据。

> 您还应该确保监测性能的目的并不能成为系统不必要的负担。您可能能够动态调整的有关性能监视过程收集的数据的详细程度。

### 性能监测要求
要检查系统性能，操作员通常需要看到信息包括:

- 用户请求的响应率。
- 并发用户请求的数量。
- 网络通讯量。
- 在商业交易正在完成率。
- 请求的平均处理时间。

它也可以帮助提供工具，使操作员能够帮助现货相关性，如:

- 与请求延迟时间的并发用户数目 (它多久开始处理请求之后用户已发送它,)。
- 平均响应时间与并发用户数目 (需要多长时间做它后，它已开始处理完成一个请求)。
- 与数的处理错误的请求的数量。

此高级功能的信息，以及运营商也应该能够获得系统中的每个组件的性能的详细的视图。此数据通常由使用低级别性能计数器如跟踪信息提供:

- 内存利用率
- 线程的数量，
- CPU 处理时间，
- 请求队列长度
- 磁盘或网络 I/O 速率和错误，
- 写或读取的字节数
- 中间件的指标，如队列长度。

所有的可视化效果应该允许运算符来指定一个时间段;显示的数据可能是当前形势的快照和/或性能历史视图。

运营商应该能够在任何指定的时间间隔期间基于任何性能度量对于任何给定的值会生成警报。

### 数据源、 仪器仪表、 数据采集要求
高级别性能数据 (吞吐量、 并发用户数、 数的商业交易、 错误率、 等等) 可以通过监测用户的请求的进展，他们到达以及通过系统收集。这涉及到将跟踪语句与计时信息的应用程序代码中的关键点。应具有足够的数据，以便能够引起他们的请求相关捕获所有故障、 异常和警告。IIS 日志是另一个有用的财源。

如果可能的话，你还应该捕获应用程序使用的任何外部系统的性能数据。这些外部的系统可能会为自己的性能计数器或其他功能请求的性能数据。如果这是不可能，您应该记录的开始时间和结束时间的每个请求向外部系统，以及操作的状态 (成功、 失败或警告) 的信息。例如，您可以使用秒表方法对时间要求;启动运行时请求启动一个定时器，然后停止计时器，当请求完成。

低级别性能数据在系统中的单个组件，可通过 Windows 性能计数器和 Azure 诊断等功能。

### 分析性能数据
很多的分析工作包括聚合性能数据由用户请求类型 (例如将项目添加到购物车，或在电子商务系统中执行结帐过程) 和/或子系统或每个请求发送到服务。

另一个常见的需求汇总中选定百分的性能数据。例如，确定请求的 99%、 95%的请求和 70%的请求的响应时间。可能有 SLA 目标或其他目标设置为每个百分位数。正在进行的结果应报告在两个附近的实时帮助检测迫在眉睫的问题，以及被聚合在很长的时间，用于统计目的。

在延迟问题会影响性能，操作员应很快就能够通过检查每个请求执行每个步骤的延迟确定瓶颈的原因。性能数据，因此必须提供相关的每一步，来配合他们的特定请求的性能度量方法的一种手段。

根据可视化的要求，它可能是有用来生成和存储多维数据集包含原始数据允许复杂特设查询的视图和分析性能信息。

## 安全监测
包括敏感数据的所有商业系统必须实现一个安全结构。安全机制的复杂性通常是敏感性的一个函数的数据。在系统中，要求用户进行身份验证，您应该记录所有的登录尝试，无论失败还是成功。此外，执行所有操作，应记录所有通过身份验证的用户访问的资源的详细信息。当用户终止他们的会话并注销时，应该也会记录此信息。

监测可能能够帮助检测系统上的攻击。例如，大量的失败的登录尝试可能表明蛮力攻击，或者请求的意外的激增可能 DDoS 攻击的结果。你必须准备监视这些请求; 源无关的所有资源的所有请求系统登录漏洞可能不小心暴露到外面的世界，而无需用户实际登录资源。

### 安全监测要求
最关键的安全监测应迅速启用操作员:

- 检测入侵企图通过未经身份验证的实体，
- 通过对数据，他们未被授予访问权限，执行操作的实体标识尝试
- 确定是否系统或一些系统的一部分，在从外部或内部的攻击 (例如，身份验证的恶意用户可能试图降低系统)。

为了支持这些要求，应通知操作员:

- 任何重复的失败的登录尝试在指定的时间段内由相同的帐户。
- 如果同一身份验证的帐户反复尝试访问一个禁止的资源在指定的时间段内。
- 如果在指定的时间段内发生大量的未经身份验证或未经授权的请求。

向运营商提供的资料应包括每个请求的源主机地址。如果违反安全定期出现从一个特定的地址范围，则可能阻止这些主机。

A key part in maintaining the security of a system is being able to quickly detect actions that deviate from the usual pattern. Information such as the number of failed and/or successful login requests can be displayed visually to help detect whether there is a spike in activity at an unusual time (such as users logging in at 3am and performing a large number of operations when their working day starts at 9am). This information can also be used to help configure time-based autoscaling, For example, if an operator observes that a large number of users regularly log in at a particular time of day, the operator can arrange to start additional authentication services to handle the volume of work, and then shut these additional services down when the peak has passed.

### 数据源、 仪器仪表、 数据采集要求
安全性是无所不包的最分布式系统方面和相关数据很可能会在整个系统的多个点生成。你应该考虑利用安全信息和事件管理 (SIEM) 的方法，收集由应用程序、 网络设备、 服务器、 防火墙、 防病毒软件和其他入侵预防元素引发的事件所引起的安全相关的信息。

安全监测可以合并数据的工具，不是您的应用程序，如确定端口扫描活动由外部机构或网络筛选器检测到试图未经授权访问您的应用程序和数据的实用程序的一部分。

在所有情况下收集到的数据必须使管理员能够确定任何攻击的性质，并采取相应的反措施。

### 分析安全数据
安全监测的一个特点是各种来源的数据产生。不同的格式和详细程度往往需要复杂分析捕获的数据，把它系在一起成连贯的线程的信息。除了最简单的情况下 (如检测大量的失败的登录或未经授权访问临界资源的反复的尝试)，它不可能来执行的安全数据，任何复杂的自动化的处理，相反，它可能更可取写这种数据，但在其原始的形式，到安全的存储库中，以便进行专家手动分析否则为加上时间戳。

<a name="SLA-monitoring"></a>

## SLA 监控
支持付费客户的许多商业系统形式的 Sla 保证对系统的性能。基本上，Sla 状态系统可以处理的工作在商定的时间内，不丢失关键信息定义的体积块。SLA 监测是确保该系统能满足可测量的 Sla。

> [AZURE。注意] SLA 监控密切相关的性能监测，而性能监测与确保系统功能有关，但 _以最佳方式_SLA 监控由定义什么合同义务 _以最佳方式_ 事实上的意思。

频繁地定义 Sla 的角度:

- 整个系统的可用性。例如，一个组织可能保证系统将供 99.9%的时间;这相当于不超过 9 小时的停机时间，每年或每周大约 10 分钟。
- 操作的吞吐量。这方面通常表示为一个或多个关键高-水痕，如保证系统将能够支持高达 100,000 的并发用户请求或处理 10,000 并发的商业交易。
- 操作的响应时间。该系统也可保障率的处理请求，如 99%的所有业务交易将在 2 秒内完成，没有单个事务将时间超过 10 秒。

> [AZURE。注意] 商业系统的一些合约可能还包含关于客户的支持，如帮助台的所有请求会在 5 分钟内，都得到的回应和 99%的所有问题应充分处理 1 个工作日内的 Sla。有效 [问题跟踪](#issue-tracking) (在本节的后面介绍) 是这些满足 Sla 的关键。

### SLA 监测要求
在最高一级，运营商应该能够一眼就确定系统是否开会商定的 Sla 与否，和如果不然后来向下钻取和检查基础的因素，以便确定为不合格的原因。

可以直观地描述的典型高级别指标包括:

- 服务的正常运行时间的百分比。
- 应用程序的吞吐量 (衡量成功的交易和/或每秒的操作)。
- 成功/失败的应用程序请求的数量。
- 应用程序和系统故障、 异常和警告的数目。

所有这些指标应该能够按指定的时间内被过滤掉。

云应用程序中可能会包含大量子系统和组件。运营商应该能够选择一个高级别指标，看看如何从健康的基本要素组成。例如，如果整个系统的正常运行时间低于可接受的值，运营商应该能够放大并确定哪一个元素导致此失败。

> [AZURE。注意] 系统正常运行时间需要仔细加以界定。在使用冗余，以确保最高可用性的系统中，各个实例的元素可能会失败，但是系统可以保持功能。提出的健康监测系统的正常运行时间应表明每个元素的聚合正常运行时间并不一定是否系统实际上已经停止。此外，故障可能是孤立的所以即使一个特定的系统不可系统其余部分可能仍然可用，虽然与减少功能 (在电子商务系统，在系统中的故障可能会阻止客户下订单但是客户仍然能够浏览产品目录)。

提醒的目的，该系统应该能够引发一个事件，如果任何高级别指标超过指定的阈值。较低级别的详细信息，包括高级别指标的各种因素应可用作上下文数据报警系统。

### 数据源、 仪器仪表、 数据采集要求
支持 SLA 监测所需的原始数据是类似于所需的性能监测与健康和可用性监测的某些方面 (见那些部分更多详细信息)。您可以捕获此数据:

- 执行终结点监测。
- 日志记录异常、 错误和警告。
- 跟踪的用户请求的执行。
- 监测系统使用的任何第三方服务的可用性。
- 使用性能度量标准和计数器。

所有数据必须定时和时间戳。

### 二语习得数据分析
检测数据必须进行聚合，生成的图片的系统的整体性能，支持向下钻取，使考试基础子系统的性能。例如，您应该能够:

- 在给定期间计算的用户请求总数，并确定这些请求的成功和失败率。
- 结合生成系统响应时间的总体视图的用户请求的响应时间。
- 到该请求中的单个工作项的响应时间的下来分析用户请求休息的进度给定请求的总体响应时间。  
- 确定任何特定一段时间的整体系统的可用性，作为百分比正常运行时间。
- 在系统分析每个单独的组件和服务时间的可用性百分比。这可能涉及解析日志生成由第三方服务。

许多商业系统需要一段特定的时间，通常一个月报告实际性能数字根据商定的 Sla。此信息可用于为客户计算学分或其他形式的还款，如果在此期间不满足 Sla。您可以通过使用一节中描述的技术计算服务的可用性 [分析可用性数据](#analyzing-availability-data).

用于内部目的，组织也可能会跟踪的数目和性质造成服务失败的事件。学习如何快速，解决这些问题，或他们完全消除，将有助于减少停机时间并满足 sla 的要求。

## 审计
根据应用程序的性质，可能指定审核用户和记录所有的数据访问所执行的操作要求的法定或其他法律规定。审计可以提供证据表明客户提出的具体请求;不可抵赖性是一个重要的因素，在许多的电子商务系统，以帮助保持信任是客户与负责应用程序或服务的组织之间。

### 审核要求
分析师必须能够跟踪业务正在执行的操作的用户，您可以重构用户的操作序列。这可能是必要的只是作为一项记录，或作为法医调查的一部分。

审计信息是高度敏感的因为它可能会包括用于标识系统和他们正在执行的任务的用户的数据。为此，它是审计信息将可视化的形式报告只提供给受信任的分析师，而不是通过使用交互式系统，支持向下钻取图形操作最有可能。一位分析师应该能够生成一系列报告，例如列出所有用户活动发生在指定的时间内，针对一个或多个资源执行详细的为单个用户，活动年表或清单的顺序操作。

### 数据源、 仪器仪表、 数据采集要求
审计信息的主要来源可包括:

- 管理用户身份验证的安全系统。
- 跟踪日志记录用户活动。
- 安全日志跟踪所有可识别和非可识别的网络请求。

审计数据和在其中存储的方式的格式可能驱使管理法规要求。例如，它不可能以任何方式 (它必须记录在其原始格式)，对数据进行清理和访问存储库，它举行必须受到保护，以防止篡改。

### 审计数据分析
分析师必须能够访问其整体在其原始形式的原始数据。除了要求产生的共同审计报告，用来分析这些数据的工具有可能专门和保持外部系统。

## 使用监视
使用监视跟踪如何使用的功能和应用程序的组件。可利用收集到的数据:

- 确定哪些功能大量使用和确定系统中的任何潜在的热点地区。高交通元素可以受益于功能分区或甚至复制更均匀地分配负载。此信息也可以用来确定哪些功能很少使用和为退休或更换系统的未来版本中是可能的候选人。
- 获取有关事件的业务系统正常使用情况下的信息。例如，在电子商务网站你可以记录有关的交易数量和负责他们的客户量的统计信息。此信息可用于容量规划随着客户数量的增长。
- (可能是间接的) 检测的性能或功能的系统的用户满意。例如，如果大量的电子商务系统中的客户经常放弃他们的购物车这可能是由于签出功能的问题。
- 生成的记帐信息。商业应用程序或多租户服务可能会收取客户对他们所使用的资源。
- 强制实施配额。如果用户在多租户系统超过他们支付的配额指定期间处理时间或资源使用情况的可以限制其访问或处理节流。

### 使用监测要求
要检查系统使用情况，操作员通常需要看到信息包括:

- 正在处理的每个子系统和向每个资源的请求的数量。
- 由每个用户正在进行的工作。
- 占领的每个用户的数据存储量。
- 每个用户正在访问的资源。

运营商也应该能够生成图，例如，显示最渴求资源的用户或最频繁访问的资源。

### 数据源、 仪器仪表、 数据采集要求
使用跟踪可以执行水平相对较高，注意到每个请求的开始和结束时间和请求 (读、 写和等等，根据资源问题) 的性质。您可以获取这一信息:

- 跟踪用户的活动。
- 捕获性能计数器测量每个资源的利用率。
- 监测行动 CPU 和 I/O 利用率由每个用户执行。

对于计量的目的，你还需要能够标识哪些用户是负责执行哪些操作，这些操作利用的资源。所收集的资料应该是不够详细，使计费准确。

<a name="issue-tracking"></a>
## 问题跟踪
如果在系统中发生的突发的事件或行为，客户和其他用户可能会报告问题。问题跟踪与管理这些问题，关联在一起努力，在系统中，解决任何潜在的问题，并向客户可能的分辨率有关。

### 问题跟踪所需经费
问题跟踪经常执行通过使用一个单独的系统，使得操作人员能够记录和报告由用户报告的问题的详细信息。这些详细信息可以包括信息，例如用户尝试执行，症状的问题、 序列的事件，和任何错误或发出的警告消息的任务。

### 数据源、 仪器仪表、 数据采集要求
问题跟踪数据的初始数据源是报道这一问题放在第一位用户。用户可能能够提供附加的数据如崩溃转储 (如果该应用程序包括在用户的桌面上运行的组件)，屏幕快照，以及日期和时间以及其他环境信息，比如他们的位置出现了错误。

此信息可用于饲料中的调试工作，并有助于构建软件的未来版本的积压。

### 分析问题跟踪数据
不同的用户可能会报告同样的问题，和问题跟踪系统应该在一起准常见报告。

应针对每个问题的报告，记录调试工作的进度和解决问题的时候可以解的告知客户。

如果用户报告一个公认的问题有已知的解决方案，在问题跟踪系统，操作人员应该能够立即通知解决方案的用户。

## 跟踪的操作和调试软件版本
当用户报告的问题时，用户往往只知道是经验的它对他或她的行动，和用户可以只将结果报告自己给操作员负责维护系统的直接影响。这些经验是通常的一个或多个基本问题的一种可见的症状。在许多情况下，分析师将需要挖通的底层操作以建立根源的问题 (这一过程称为年表 _根本原因分析_).

> [AZURE。注意] 根本原因分析，可能会发现在应用程序的设计效率低下的问题。在这些情况下，有可能要返工受影响的元素并将它们作为后续版本的一部分部署。这一过程需要小心的控制，和已更新的组件应密切监测。

### 跟踪和调试所需经费
追踪突发的事件和其他问题，至关重要的是监测数据提供足够的信息不仅仅是在高级别上发生的问题，但也包括足够细节，以使这些问题的起源追溯分析师和重构发生的事件的序列。此信息必须足以使分析师可以诊断任何问题的根本原因，以便开发人员可以进行必要的修改，以防止它们再次发生。

### 数据源、 仪器仪表、 数据采集要求
故障诊断可包括的跟踪作为一部分的操作来建立一棵树，描绘通过系统的逻辑流程，客户提出具体的请求时调用的所有方法 (和它们的参数)。例外和警告此流系统生成的需要可以捕获和记录。

若要支持调试，系统可以提供挂钩，使操作员能够捕获在关键点的状态信息在系统中，或提供详细的分步信息，作为所选的操作进度。捕获这一级别的细节数据可以施加额外系统上的负载应该是一个临时的过程，主要用于当一连串极不寻常的事件发生是很难复制，或时的一个新版本或更多的元素到系统需要仔细监测，以确保它们按预期运行。

## 监测和诊断管道
监控大型分布式的系统构成了重大的挑战，和每上一节中描述的场景应该并不是孤立。还有可能是重大的重叠，在每一种情况，所需的监测和诊断数据，虽然这个数据可能需要处理，提出了一种以不同的方式。出于这些原因，应采取监测和诊断的整体视图。

作为一种管道，包括图 1 中所示的阶段，可以预见整个监测和诊断过程。

![](media/best-practices-monitoring/Pipeline.png)

_图 1。
在监测和诊断管道阶段_

Figure 1 highlights how the data for monitoring and diagnostics can come from a variety of data sources. The Instrumentation/Collection stage is concerned with instrumentation; determining which data to capture, how to capture it, and how to format this data so that it can be easily examined. The Analysis/Diagnosis phase takes the raw data and uses it to generate meaningful information that can be used to determine the state of the system. This information can be used to make decisions about possible actions to take, and the results can be fed back into the Instrumentation/Collection phase. The Visualization/Alerting stage phase presents a consumable view of the system state; it could display information in near real-time by using a series of dashboards, and it could generates reports, graphs, and charts to provide a historical view of the data that can help identify long-term trends. If information indicates that a KPI is likely to exceed acceptable bounds, then this stage can also trigger an alert to an operator. In some cases, an alert can also be used to trigger an automated process that attempts to take corrective actions, such as auto-scaling.

请注意这些步骤构成并行在哪里发生阶段的连续流动过程。理想情况下，所有阶段都应动态地配置;在一些点，尤其是当系统已经新部署或者遇到了问题，它可能需要更频繁地收集扩展的数据。在其他时候，它应该有可能恢复到捕获基级关键信息，来验证系统正常运行。

此外，整个监测过程应视为一个活的、 持续的解决方案，是微调，根据反馈意见改进。例如，你可能开始与测量许多因素来确定系统的健康，但随着时间的推移分析可能导致一个细化，如同你抛弃措施是不相关的使你能更精确地专注于你所需要的数据同时尽量减少任何背景噪音。

## 监测和诊断数据的来源
The information used by the monitoring process can come from several sources, as illustrated in Figure 1. At the application level, information comes from trace logs incorporated into the code of the system. Developers should follow a standard approach for tracking the flow of control through their code (for example, on entry to a method emit a trace message that specifies the name of the method, the current time, the value of each parameter, and any other pertinent information. Recording the entry and exit times could also prove useful). You should log all exceptions and warnings, and ensure that you retain a full trace of any nested exceptions and warnings. Ideally, you should also capture information that identifies the user running the code together with activity correlation information (to track requests as they pass through the system), and log attempts to access all resources such as message queues, databases, files, and other dependent services; this information can be used for metering and auditing purposes.

许多应用程序使用的库和框架来执行常见任务，如访问数据存储区或通过网络进行通信。这些框架可能是可配置的输出自己的跟踪消息和原始的诊断信息，如事务率、 数据传输成功和失败，等等。

> [AZURE。注意] 许多现代框架自动发布性能和跟踪事件，并且捕获此信息只是提供一种手段来检索和存储它可以进行处理和分析。

应用程序正在其运行的操作系统可以的低级全系统的信息来源，如性能计数器指示 I/O 速率、 内存使用率和 CPU 使用率。也可能报告操作系统错误 (如未能正确打开文件)。

您还应考虑的基本的基础设施和对您的系统运行的组件。虚拟机、 虚拟网络和存储服务都可以来源的重要的基础设施水平性能计数器和其他诊断数据。

如果您的应用程序使用其他的外部服务，例如 web 服务器或数据库管理系统，这些服务可能会发布自己的跟踪信息、 日志和性能计数器。例子包括 SQL 服务器动态管理视图用于跟踪对 SQL Server 数据库，执行的操作和互联网信息服务器跟踪日志记录向 web 服务器发出的请求。

因为系统的组件部署的改性和新版本，它是重要的是能够对属性的问题、 事件和指标与每个版本。此信息应回到拴释放管道，这样可以快速跟踪和纠正问题与特定版本的组件。

在任何点可能会出现安全问题，在系统中。例如，用户可能尝试使用无效的用户 ID 或密码; 登录经过身份验证的用户可能尝试，并取得未经授权的访问的资源;或用户可能提供无效或过期的密钥，才能访问加密的信息。始终应记录与安全相关的信息，为成功和失败的请求。

部分 [检测应用程序](#instrumenting-an-application) 包含的信息你应该可以捕捉，但是有各种各样的策略，你可以用来收集此信息放在第一位的进一步指导:

- **应用程序/系统监控**.该策略使用应用程序，应用程序框架，操作系统和基础设施内的内部来源。应用程序代码本身可以生成自己的客户端请求的生命周期期间监测显著点的数据。应用程序可以包括跟踪语句，可以选择性地启用或禁用视情况。也可以通过使用诊断框架动态注入诊断。这些框架通常提供的插件，可以向您的代码中的各种仪器仪表点附加和捕获这些点的跟踪数据。

此外，您的代码和/或底层基础结构可能引发事件在临界点。监控代理配置为侦听这些事件可以记录的事件信息。

- **真实的用户监测**.这种方法记录用户和应用程序之间的交互，并观察到的每个请求和响应流。此信息可以有双重目的: 它可用于计量使用的每个用户，并可用于确定用户是否收到合适的服务质量的 (例如，快速的响应时间、 低延迟和最小错误发生)。捕获的数据可以用于标识的关切领域最频繁出现故障和元素在哪里系统变慢了，可能由于应用程序或某种其他形式的瓶颈中的热点。如果认真落实这一方针，有可能重建用户的流量通过调试和测试目的的申请。

	> [AZURE。重要] 通过监测实际用户捕获的数据应该被认为是高度敏感的因为它可能包含机密材料。如果捕获的数据保存必须安全地存储。如果数据被用于性能监视或调试目的，所有的个人可识别信息应去除了第一名。

- **综合用户监测**.在此方法中，你写你自己测试客户端模拟用户，并执行一系列可配置，但典型的操作。您可以跟踪性能的测试客户端，以帮助确定系统的状态。你也可以使用测试客户端的多个实例作为负载测试的操作的一部分建立系统如何响应在压力之下，并在这些条件下生成什么样的监测输出。

	> [AZURE。注意] 您可以实现真正的和合成的用户监测包括跟踪和次方法调用和其他的应用程序的关键部分的执行的代码。

- **性能分析**.这种方法主要针对监测和提高应用程序性能。而不是受雇于真正和合成用户监测的功能级别运行，它捕捉到较低级别信息在应用程序运行。分析可以通过定期采样的执行状态的应用程序 (确定应用程序运行在一个给定的点在时间中的代码片断)，或使用仪器探头插入到代码在重要时段 (例如开始和结束的方法调用) 并记录哪些方法被调用，在什么时候实施和每个调用的时间。然后可以分析此数据来确定应用程序的哪些部分可能会导致性能问题。

- **终结点监测**.这种技术使用一个或多个应用程序所公开的诊断终结点以便监测。终结点到应用程序代码提供一种途径，可以返回有关健康的信息系统。不同的端点可以集中各方面的功能。你可以写你自己将定期请求发送到这些终结点的诊断程序客户端和同化反应。这种方法通过更充分的说明 [健康终结点监测模式](https://msdn.microsoft.com/library/dn589789.aspx) 在微软的网站。

最大覆盖，你应该在相互组合使用这些技术。

<a name="instrumenting-an-application"></a>
## 检测应用程序
仪器仪表是关键部分的监测进程;您只可以有意义的决定，关于性能和系统健康状况如果你捕获的数据，使您能够做出这些决策放在第一位。你通过使用检测方法收集的信息应手动足以使您能够评估性能、 诊断问题并作出决定而迫使你必须登录到远程生产服务器执行跟踪 (和调试)。

检测数据通常将包括信息写入跟踪日志和度量标准:

- The contents of a trace log can be the result of textual data written by the application, binary data created as the result of a trace event (if the application is using Event Tracing for Windows – ETW), or they can be generated from system logs that record events arising from parts of the infrastructure, such as a web server. Textual log messages are often designed to be human-readable, but they should also be written in a format that enables them to be easily parsed by an automated system. You should also categorize logs; don't write all trace data to a single log but use separate logs to record the trace output from different operational aspects of the system. This enables you to quickly filter log messages by reading from the appropriate log rather than having to process a single lengthy file. Never write information that has different security requirements (such as audit information and debugging data) to the same log.

	> [AZURE。注意] 日志可实现为一个文件在文件系统上，或者它可以举行一些其他格式，如在 blob 存储 blob。日志信息也可能举行更结构化的存储，如表中的行。

- 度量标准一般只是将度量值或计数的某些方面或资源在系统中在特定的时间与一个或多个关联的标签或尺寸 (有时被称为 _示例_).公制的单个实例不是通常有用隔离;相反，度量必须随着时间的推移捕获。需要考虑的关键问题是哪些量度你应该记录和如何频繁地。为生成数据指标往往可以施加在系统上，一个重要的额外负荷而捕获度量很少可能会导致你错过导致重大事件的情况。考虑将不同度量的度量。例如，服务器上的 CPU 利用率可能会发生变化显著从第二到第二，但较高的利用率只有成为一个问题，如果它是长寿的分钟数。

<a name="information-for-correlating-data"></a>
### 相关数据信息
You can easily monitor individual system-level performance counters, capture metrics for resources, and obtain application trace information from various log files, but some forms of monitoring require the analysis and diagnostics stage in the monitoring pipeline to correlate the data retrieved from several sources. This data may take several forms in the raw data, and the analysis process must be provided with sufficient instrumentation data to be able to map these different forms. For example, at the application framework level, a task might be identified by a thread ID but within an application the same work might be associated with the user ID for the user performing that task. Furthermore, there is unlikely to be a 1:1 mapping between threads and user requests as asynchronous operations may reuse the same threads to perform operations on behalf of more than one user.  To complicate matters further, a single request may be handled by more than one thread as execution flows through the system. If possible, associate each request with a unique activity ID that is propagated through the system as part of the request context (the technique for generating and including activity IDs in trace information will be dependent on the technology being used to capture the trace data).

所有的监测数据应以相同的方式加上时间戳。为保持一致性，通过使用协调通用时间记录所有日期和时间。这将有助于使您能够更轻松地跟踪序列的事件。

> [AZURE。注意] 运行在不同的时区和网络中的计算机可能不同步，因此您不应依赖使用单独的时间戳为关联跨越多台机器的检测数据。

### 检测数据应该包括些什么信息?
决定您需要收集哪些仪器仪表数据时，请考虑以下几点:

- 通过跟踪事件捕获的信息应该是机器与人类可读。 你应该采用良好定义的模式，此信息便于自动的处理的日志数据跨多个系统，并提供对操作和工程人员读取日志的一致性。包括环境的信息，例如部署环境，运行的计算机上的进程是，流程和调用堆栈的细节。  
- 分析可以施加相当大的开销，在系统上和在必要时，才应启用。使用仪器分析记录一个事件 (如方法调用) 每次出现时，而只有采样记录选定的事件。所选内容可以是基于时间 — — 每 N 秒一次，或基于频率 — — 一旦每 N 个请求。如果事件非常频繁地发生，通过检测进行分析可能造成太大的负担和本身影响总体性能。在这种情况下，采样方法可能更可取。但是，如果事件的频率很低，然后取样可能会想念他们，在这种情况下检测可能是更好的方法。
- 提供足够的上下文，使开发人员或管理员联系，以确定每个请求的来源。这可能包括某种形式的活动 ID 标识特定实例的请求，并可以用于关联此活动与执行的计算工作和使用的资源的信息。请注意这项工作可能跨越进程和计算机边界。计量，范围还应包括 (直接或间接地通过其他相关信息) 引起的请求作出的客户参考。此上下文提供有关应用程序状态时监测数据被捕获的宝贵信息。
- 记录所有请求和地点或地区从中发出这些请求。此信息可帮助确定是否有任何特定位置的热点，并提供可以是有用的在决定是否对应用程序或数据，使用它进行重新分区的数据。
- 记录并仔细捕获的异常详细信息。经常批评调试信息就会丢失由于贫穷的异常处理。捕获应用程序，包括任何内部异常和其他上下文信息，如有可能包括调用堆栈由引发的异常的全部详情。
- Be consistent in the data that the different elements of your application capture as this can assist in analyzing events and correlating them with user requests. Consider utilizing a comprehensive and configurable logging package to gather information rather than depending on developers implementing different parts of the system all adopting the same approach. Gather data from key performance counters, such as the volume of I/O being performed, network utilization, number of requests, memory use, and CPU utilization. Some infrastructure services may provide their own specific performance counters, such as the number of connections to a database, the rate at which transactions are being performed, and the number of transactions that succeed or fail. Applications might also define their own specific performance counters.
- 登录到外部服务，如数据库系统、 web 服务或其他系统级别的服务，提供基础设施 (例如缓存的 Azure) 的一部分进行的所有调用。记录有关执行每个呼叫，所需时间的信息和成功或失败的调用。如果可能的话，捕获所有的重试次数和故障暂态发生任何错误的信息。

### 确保与遥测系统的兼容性
在许多情况下，通过仪器产生的信息是作为一系列的事件生成和传递给一个单独的遥测系统进行处理和分析。遥测系统通常独立于任何特定的应用程序或技术，但预计要遵循特定的格式通常由架构定义的信息。该架构有效地指定合同定义的数据字段和遥测系统可以摄取的类型。应广义架构以允许将数据从各种平台和设备抵达。

常见的架构应包括常见仪器仪表的所有事件，如事件名称、 事件时间、 发件人，并需要与其他事件 (如用户 ID、 设备 ID 和应用程序 ID) 相关的详细信息的 IP 地址的字段。请记住，可能会引发事件由任意数量的设备，所以架构不应该取决于设备类型。此外，同一应用程序的事件可能会引发由大量不同的设备;应用程序可能支持漫游或一些其他形式的跨设备分布。理想情况下的大部分这些领域应映射到输出用于捕获事件的日志库。

该架构还可以包括跨不同应用程序有关的特定场景中常见的域字段。这可能是有关异常，应用程序启动和结束事件和 web 服务 API 调用成功和/或失败的信息。使用相同的域字段集的所有应用程序应该发出相同的事件，使常见的报告和分析，建立了一整套集。

最后，架构可以包含自定义字段用于捕获应用程序特定事件的细节。

### 规范应用程序的最佳做法
以下列表总结了插装在云中运行分布式应用程序的最佳做法。

- 使日志容易阅读和容易解析。使用结构化测井在可能的情况。是简明和描述性的日志消息。
- 在所有日志中，标识源并提供上下文和定时信息写入每个日志记录。
- 用于所有时间戳相同的时区和格式。这将有助于相关跨硬件和服务在不同的地理区域中运行的操作的事件。
- 分类日志和消息写入相应的日志文件。
- 不会披露有关系统的敏感信息或用户的个人信息。擦洗此信息之前它被记录，但确保保留有关的细节。例如，从任何数据库连接字符串，删除 ID 和密码，但余下的信息写入日志，以便分析师可以确定系统正在访问正确的数据库。日志中所有关键的异常，但使管理员可以打开日志记录和关闭更低级别的异常和警告。此外，捕获和记录所有重试逻辑信息。该数据可以用于监视系统的瞬态健康。
- 进程外调用，如外部 web 服务或数据库请求的跟踪。
- 不要混合日志消息的不同安全需求，相同的日志文件中。例如，不写入调试和审核信息记录到同一日志。
- 除了审核事件，所有的日志记录调用应该是火和忘记的行动必须不会阻止业务行动的进度。审核事件是例外，因为它们对业务至关重要，可以列为业务操作的基本组成部分。
- 日志记录应该是可扩展的和具体的目标没有任何直接的依赖关系。举个例子，而不是通过使用编写信息 _System.Diagnostics.Trace_定义一个抽象接口 (如 _ILogger_) 其中暴露出的测井方法和可以通过使用任何适当的手段来实现。
- 所有日志记录必须万无一失，应该永远不会触发任何级联的错误。日志记录必须不引发任何异常。
- 治疗仪器仪表作为一个持续的迭代过程和日志定期检讨，不只是一个问题时。

## 收集和存储数据
The collection stage of the monitoring process is concerned with retrieving the information generated by instrumentation, formatting this data to make it easier to consume by the analysis/computation phase, and saving the transformed data in reliable and accessible storage. The instrumentation data that you gather from different parts of a distributed system could be held in a variety of locations and with varying formats. For example, your application code might generate trace log files, and generate application event log data, while performance counters that monitor key aspects of the infrastructure that your application uses could be captured by using other technologies.  Any third-party components and services that your application uses may provide instrumentation information in different formats, using separate trace files, blob storage, or even a custom data store.

通过实施服务，藉此可以从生成的检测数据的应用程序自动运行经常执行数据收集。图 2 说明了这种体系结构，突出显示仪表数据采集子系统的示例。

![](media/best-practices-monitoring/TelemetryService.png)

_图 2。
仪器仪表数据采集_

请注意这是一个简化的看法。收集服务未必不是一个单一的过程，并且可以包括许多组成部分在不同的机器上运行以下各节中所述。此外，如果一些遥测数据的分析需要快速执行 (热分析，如下节所述 [支持热，温暖和寒冷的分析](#supporting-hot-warm-and-cold-analysis) 稍后在此文档中)，本地组件外的收集服务进行操作可能会立即执行分析任务。图 2 描述了这种情况为选定的事件;在分析处理后结果可以被直接发送到可视化和报警子系统。虽然正在等待处理，在存储举行遭受热或冷的分析数据。

Azure 应用程序和服务，Azure 诊断 (一团) 提供一个可能的解决方案，用于捕获数据。一团收集的每个计算节点，以下来源的数据汇总在一起，然后将它上载到 Azure 存储空间:

- 蔚蓝的日志
- IIS 日志
- IIS 失败请求日志
- Windows 事件日志
- 性能计数器
- 故障转储
- 蔚蓝的诊断基础设施日志  
- 自定义错误日志

有关详细信息，请参阅文章 [Azure: 遥测基础知识和故障排除](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) 在微软的网站。

### 仪器仪表数据收集策略
你给出的弹性性质的云，并避免手动从每个节点系统中检索遥测数据的必要性，应安排到一个中央位置传输的数据和合并。在跨越多个数据中心的系统，它可能很有用，首先收集、 整合和将数据存储在区域的基础上，然后的区域的数据聚合到一个单一的中央系统。

若要优化带宽的使用，您可以选择传输不太紧急数据块，作为批处理中。然而，数据必须不拖延下去，尤其是如果它包含时间敏感的信息。

#### _拉和推检测数据_
仪器仪表的数据采集子系统可以要么积极检测数据从检索各种日志和其他来源的应用程序 (每个实例 _拉模型_)，或者它可以作为一个被动的接收者，等待数据从构成的应用程序 (每个实例的组件发送 _推模型_).

执行拉模型的一种方法是使用本地运行应用程序的每个实例的监控代理。监视的 agent 是一个单独的过程，定期检索 (拉) 遥测数据收集在本地节点，并且直接将此信息写入应用程序的所有实例共享的集中式存储。这是由一团实施的机制。Azure 的 web 或工作者角色的每个实例可以配置为捕获诊断和其他跟踪信息，存储在本地。沿着每个副本指定的数据到 Azure 存储监控代理。页面 [配置诊断 Azure 的云服务和虚拟机](https://msdn.microsoft.com/library/azure/dn186185.aspx) 在 Microsoft 网站上提供更多的细节，在这一进程。某些元素，如 IIS 日志，崩溃转储，和自定义错误日志写入到 blob 存储，同时从 Windows 事件日志、 ETW 事件和性能计数器的数据记录在表存储。图 3 显示了这种机制:

![](media/best-practices-monitoring/PullModel.png)

_图 3。
使用监视 agent 中提取信息并写入共享存储_

> [AZURE。注意] 使用监视代理被适合应用于捕捉自然拉从一个数据源，例如 SQL Server 管理视图或 Azure 服务总线队列的长度的信息的检测数据。

有关配置和使用 Azure 诊断程序的信息，请访问 [使用 Azure 的诊断收集日志记录数据](https://msdn.microsoft.com/library/azure/gg433048.aspx) 在 Microsoft 网站上的页面。

一个小型的应用程序，在为数有限的节点上运行的遥测数据切实可以存储在单个位置使用刚刚描述的方法。然而，一个复杂的、 高度可伸缩的、 全球性的云应用程序可能会轻松地生成巨大的数据量从数以百计的 web 和工作者角色，数据库碎片和其他服务。这次洪水的数据能够轻而易举地战胜与一个单一的中央位置可用的 I/O 带宽。因此你遥测解决方案必须是冗余的可伸缩，防止它充当一个瓶颈，随着系统的扩展，和理想的情况是冗余的纳入一定程度，以减少失去重要监测信息 (如审计或计费数据)，如果系统的一部分失败的风险。

若要解决这些问题，你可以实现排队。图 4 显示了这种结构。在这种体系结构、 当地监控代理 (如果它可以适当配置) 或自定义数据收集服务 (如果没有) 职位数据到一个队列中，并以异步方式运行一个单独的进程 (存储写作服务在图 4 中) 在此队列中接收数据，并将它写入到共享存储。消息队列是适合这种情况下，因为它至少一次提供语义确保，一旦发布，排队的数据不会丢失。可以通过使用一个单独的工作角色实现存储写作服务。

![](media/best-practices-monitoring/BufferedQueue.png)

_图 4。
使用缓冲仪表数据队列_

本地数据收集服务可以将数据添加到队列立即收到。作为一个缓冲队列和写作服务的存储可以检索和写入的数据在其自己的速度。默认情况下，队列操作按先进先出，但你可以优先处理的邮件，加快他们通过队列，如果它们包含必须更快处理的数据。有关更多信息，请参见 [优先级队列](https://msdn.microsoft.com/library/dn589794.aspx) 模式。或者，您可以使用不同的渠道 (例如服务总线主题) 直接数据到不同的目的地，根据所需的分析处理的形式。

可伸缩性，您可以运行写作服务存储的多个的实例。如果有大量的事件，您可以使用一个事件中心派遣不同的计算资源，用于处理和存储数据。

<a name="consolidating-instrumentation-data"></a>
#### _巩固的检测数据_
The instrumentation data retrieved by the data collection service from a single instance of an application gives a localized view of the health and performance of that instance. To assess the overall health of the system, it is necessary to consolidate some aspects of the data in the local views together. This can be performed after the data has been stored, but in some cases it could also be achieved as the data is collected. Rather than being written directly to shared storage, the instrumentation data could pass through a separate data consolidation service which combines data and acts as a filter and cleanup process. For example, instrumentation data that includes the same correlation information such as an activity ID can be amalgamated (it is possible that a user starts performing a business operation on one node, and then gets transferred to another node in the event of node failure or depending on how load-balancing is configured). This process can also detect and remove any duplicated data (always a possibility if the telemetry service uses message queues to push instrumentation data out to storage). Figure 5 illustrates an example of this structure.

![](media/best-practices-monitoring/Consolidation.png)

_图 5。
使用一个单独的服务，巩固和清理检测数据_

### 存储检测数据
The previous discussions have depicted a rather simplistic view of the way in which instrumentation data is stored. In reality, it can make sense to store the different types of information by using technologies that are most appropriate to the way in which each type is likely to be used. For example, Azure blob and table storage have some similarities in the way in which they are accessed, but have limitations concerning the operations that you can perform using them and the granularity of the data that they hold is quite different. If you need to perform more analytical operations or require full-text search capabilities on the data, it may be more appropriate to use data storage that provides capabilities that are optimized for specific types of queries and data access. For example, performance counter data could be stored in a SQL database to enable ad-hoc analysis; trace logs might be better stored in Azure DocumentDB; security information could be written to HDFS; information requiring full-text search could be stored by using Elastic Search (which can also speed searches by using rich indexing.) You can implement an additional service that periodically retrieves the data from shared storage, partitions and filters the data according to its purpose, and then writes it to an appropriate set of data stores as shown in Figure 6. An alternative approach is to include this functionality in the consolidation and cleanup process and write the data directly to these stores as it is retrieved rather than saving it in an intermediate shared storage area. Each approach has its advantages and disadvantages. Implementing a separate partitioning service lessens the load on the consolidation and cleanup service, and enables at least some of the partitioned data to be regenerated if necessary (depending on how much data is retained in shared storage). However, it consumes additional resources, and there may be a delay between the instrumentation data being received from each application instance and this data being converted into actionable information.

![](media/best-practices-monitoring/DataStorage.png)

_图 6。
根据分析数据进行分区和存储需求_

相同的仪表数据可能需要为多个目的。例如，性能计数器可用于提供系统性能随着时间推移，从历史角度看，但这一信息也可能结合其他使用数据来生成客户帐单信息。在这些情况下，相同的数据可能会发送到多个目的地，就像一个文档数据库，它可以作为长期持有计费信息，存储和处理复杂的性能分析的多维存储。

您还应该考虑如何迫切需要的数据时。数据提供有关警报需要快速访问的信息，因此应在快速的数据存储和索引或结构来优化报警系统执行的查询。在某些情况下，可能需要为每个节点设置格式和本地保存数据，以便本地实例的警报系统可以迅速通知的任何问题收集的数据的遥测服务。相同的数据可以被调度到存储写作服务前面的图表所示，集中存储，如果还需要作其他用途。

用于更多的信息考虑分析报告，和历史趋势不那么紧迫，可以支持数据挖掘和特设查询的方式存储。有关详细信息，请参阅 [支持热，温暖和寒冷的分析](#supporting-hot-warm-and-cold-analysis) 后来在这份文件。

#### _日志旋转和数据保留_
Instrumentation can generate considerable volumes of data. This data can be held in several places, starting with the raw log files, trace files, and other information captured at each node to the consolidated, cleaned, and partitioned view of this data held in shared storage. In some cases, once the data has been processed and transferred the original raw source data can be removed from each node. In other cases, it may be necessary or simply useful to save the raw information. For example, data generated for debugging purposes may be best left available in its raw form but can then be discarded quite quickly once any bugs have been rectified. Performance data often has a longer life to enable it to be used to spot performance trends and for capacity planning. The consolidated view of this data is usually kept on-line for a finite period to enable fast access, after which it might be archived or discarded. Data gathered for metering and billing customers may need to be saved indefinitely. Additionally, regulatory requirements might dictate that information collected for auditing and security purposes will also need to be archived and saved. This data is also sensitive and may need to be encrypted or otherwise protected to prevent tampering. You should never record information such as users' passwords or other information that could be used to commit identity fraud; such details should be scrubbed from the data before it is stored.

#### _降采样_
经常是将存储历史数据，能够发现长期趋势非常有用。而不是保存在其全部的旧数据，则可能要降采样的数据来降低其分辨率和节约存储成本。作为一个例子，而不是保存每时每刻性能指标数据超过一个月大可以合并，形成由小时视图。

### 收集和存储日志信息的最佳做法
以下列表总结了用于捕获和存储日志信息的最佳做法。

- 监视代理或数据收集服务作为进程外服务应该运行，应该是简单的部署。
- 所有输出从监控代理或数据收集服务应该是一种不可知的格式，是独立于机器、 操作系统或网络协议。例如，发出如 JSON，MessagePack，或 Protobuf，而不是 ETL/ETW 的自我描述格式的信息。使用标准的格式使体系构建处理管道;读取、 转换和输出中所商定的格式的数据的组件可以很容易的集成。
- 监测和数据收集过程必须万无一失，必须不触发任何级联的错误情况。
- 瞬态故障监测代理或数据收集服务到数据接收器发送信息时，应准备对遥测数据重新排序，以便首先发送最新的信息 (监测代理/数据收集服务可以选择删除较旧的数据，或本地保存和传输它后来赶上来，自行)。

## 分析数据和诊断问题
监测和诊断过程的重要组成部分分析来获得系统总体福祉的图片收集到的数据。您应该定义你自己的 Kpi 和性能指标，也是很重要的要了解您可以如何构建已聚集了满足您的分析要求的数据。它也是重要的是了解如何在不同的度量中捕获的数据和日志文件关连的因为此信息可以被跟踪的事件序列的关键和帮助诊断出现的问题。

在部分所述 [巩固的检测数据](#consolidating-instrumentation-data), the data for each part of the system is typically captured locally, but generally needs to be combined with data generated at other sites that participate in the system. This information requires careful correlation to ensure that data is combined accurately. For example, the usage data for an operation may span a node hosting a web site to which a user connects, a node running a separate service accessed as part of this operation, and data storage held on a further node. This information needs to be tied together to provide an overall view of the resource and processing usage for the operation. Some pre-processing and filtering of data might occur on the node on which the data is captured, while aggregation and formatting is more likely to occur on a central node.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### 支持热，温暖和寒冷的分析
重新格式化数据的可视化和分析，报告和警报的目的可以是一个复杂的过程，消耗其自己的资源集。某些形式的监测时间紧迫，需要立即分析数据是有效。这被称为 _热分析_.例子包括警报和安全监测 (如检测攻击系统) 的某些方面所需的分析。为这些目的所需的数据必须快速可用且结构进行有效的处理;它某些情况下它可能需要移动到单个节点保留数据的处理分析。

其他形式的分析较少时间挑剔，一旦收到的原始数据，也可能需要一些计算和聚合。这被称为 _温暖的分析_.性能分析通常属于此类。在这种情况下，独立、 单独的性能事件是不会有统计学意义 (它可引起突然激增或故障)，而从一系列的事件数据应提供系统性能更可靠图片。温暖的分析也可以用于帮助诊断健康问题。健康事件的通常处理方式进行热分析，可以立即引发警报。操作人员应该能够钻入健康事件的原因，通过检查的数据从温暖的路径;这个数据应该包含事件导致导致健康事件的问题有关的信息。

某些类型的监测生成更多的长期数据，并且可以执行分析在稍后的日期，可能根据预定义的日程安排。在某些情况下，分析可能需要执行复杂的筛选的一段时间内捕获的数据量很大。这被称为 _冷的分析_.关键的要求是，一旦它被捕获安全地存储数据。例如使用监视和审计要求准确的定期点系统的状态，在时间，但此状态信息并没有可供加工立即已收集。 冷的分析也可以用于为预测健康分析提供数据。在指定的时间内收集的历史信息可以被一起用当前的健康数据 (从热路径检索) 现货趋势可能很快导致健康问题。在这些情况下，它可能需要发出警报，以便采取纠正行动。

### 相关数据
The data captured by instrumentation can provide a snapshot of the system state, but the purpose of analysis is to make this data actionable. For example, what has caused an intense I/O loading at the system level at a specific time? Is it the result of a large number of database operations? Is this reflected in the database response times, the number of transactions per second, and application response times at the same juncture? If so, then one remedial action that may reduce the load could be to shard the data over more servers. In addition, exceptions can arise as a result of a fault in any level of the system, and an exception in one level often triggers another fault in the level above. For these reasons you need to be able to correlate the different types of monitoring data at each level to produce an overall view of the state of the system and the applications that are executing on it. You can then use this information to make decisions about whether the system is functioning acceptably or not, and determine what can be done to improve the quality of the system.

在部分所述 [相关数据信息](#information-for-correlating-data)您必须确保原始检测数据包括足够的上下文和活动 ID 信息，以支持所需的聚合相关事件。此外，这个数据可能举行以不同的格式，和它可能需要解析此信息以将其转换为标准化的格式，以便分析的目的。

### 故障排除和诊断问题
诊断需要能够确定故障或意外的行为，包括执行根本原因分析的原因。通常所需的信息包括:

- 从事件日志和跟踪为整个系统或指定的子系统期间指定的时间窗口的详细的信息。
- 完整的堆栈跟踪异常和在指定的时间内发生无论是在系统或指定的子系统内的任何指定的级别故障造成的。
- 在指定的时间窗口在系统的任何地方或指定子系统崩溃转储为任何失败的过程。
- 活动日志记录执行操作的所有用户或所选用户在指定的时间段。

分析数据用于疑难解答经常需要深厚的技术了解的系统体系结构和各组成部分包含解决方案。因此，它经常需要很大程度的手动干预来解释数据，建立问题原因，并建议适当的策略，以纠正它们。它可适当简单地在其原始格式存储此信息的副本并使其可供冷分析专家。

## 数据可视化和提高警报
任何监测系统的一个重要方面是在这种运算符可以迅速发现任何趋势或问题，并迅速通知操作员如果重大事件的发生，需要注意展示数据的能力。

数据演示文稿可以采取多种形式，包括可视化通过使用仪表板、 警报和报告。

### 可视化与仪表板
The most common way to visualize data is to use dashboards that can display information as a series of charts, graphs, or some other pictorial manner. These items could be parameterized, and an analyst should be able to select the important parameters (such as the time period) for any specific situation. Dashboards can be organized hierarchically, with top-level dashboards giving an overall view of each aspect of the system but with the facility to enable an operator to drill down to the details. For example, a dashboard that depicts the overall disk I/O for the system should allow an analyst to dig into the data and view the I/O rates for each individual disk to ascertain whether one or more specific devices account for a disproportionate volume of traffic. Ideally the dashboard should also display related information, such as the source of each request (the user or activity) that is generating this I/O. This information could then be used to determine whether (and how) to spread the load more evenly across devices, and whether the system would perform better if more devices were added. A dashboard might also be able to use color-coding or some other visual cues to indicate values that appear anomalous or that are outside an expected range. Using the previous example, a disk with an I/O rate approaching its maximum capacity over an extended period (a hot disk) could be highlighted in red, a disk with an I/O rate that periodically runs at its maximum limit over short periods (a warm disk) could be highlighted in yellow, and a disk exhibiting normal usage could be displayed in green.

注意，对于仪表板系统有效地工作，它必须具有原始数据，才能工作。如果您在构建您自己的仪表板系统，或利用仪表板开发由另一个组织，你必须明白你需要收集，在什么级别的粒度，以及应该如何格式化为消费的仪表板的检测数据。

良好的仪表板不只显示信息，它提供了一种手段，让分析师提问特设的信息。一些系统提供的管理工具，操作员可以使用来执行这些任务和探索的基础数据。或者，根据存储库中用来保存这些信息有可能直接地质疑这种数据，或将其导入工具，如 Microsoft Excel 进行进一步分析和报告。

> [AZURE。注意] 您应当限制对仪表板经过授权的人员;此信息可能是敏感的商业。您也应该保护提出要防止用户更改它的仪表板的基础数据。

### 提高警报
警报是过程的监视和检测数据分析和生成通知，如果检测到的一个重大事件。

警报用于确保该系统仍然健康、 灵敏、 和安全。它是任何系统，给用户，使性能、 可用性和隐私保障的重要组成部分和数据可能需要立即采取行动。操作人员可能需要触发警报的事件通知。警报还被用于调用系统功能，如自动缩放。

警报通常取决于以下的检测数据:

- 安全事件。如果事件日志表明，重复身份验证和/或授权失败正在发生，系统可能受到攻击和应通知操作员。
- 性能度量标准。系统必须迅速作出反应，如果特定的性能度量指标超过指定的阈值。
- 可用性的信息。如果它可能需要快速重新启动一个或多个子系统或故障转移到备份资源，检测到故障。子系统中的重复的故障可能表明更多的担忧。

运营商可能会通过使用许多的交付渠道，例如电子邮件、 寻呼机设备或 SMS 短信收到警报信息。警报可能还包括相对值的是多么关键的情况出现。许多警报系统支持订阅服务器组和所有运算符的是同一组的成员可以都发送组相同的警报。

An alerting system should be customizable and the appropriate values from the underlying instrumentation data could be provided as parameters. This approach enables an operator to filter data and focus on those thresholds or combinations of values which are of interest. Note that in some cases, the raw instrumentation data could be provided to the alerting system, while in other situations it might be more appropriate to supply aggregated data (for example, an alert could be triggered if the CPU utilization for a node exceeded 90% over the last 10 minutes). The details provided to the alerting system should also include any appropriate summary and context information; this data can help to reduce the possibility of false-positive events tripping an alert.

### 报告
报告用来生成系统，总体视图和可能纳入历史数据，以及当前的信息。报告要求本身分为两大类: 业务报告和安全报告。

业务报告通常包括以下几个方面:

- 汇总统计数据使您能够了解资源利用的整体系统或指定的子系统在指定的时间窗口。
- 在给定的时间内确定的系统整体或指定的子系统资源使用情况的趋势。
- 监测的例外情况发生在整个系统或在给定的时间段期间指定子系统。
- 确定在已部署的资源，应用程序的效率和了解是否的资源量 (和其相关的成本)，可以减少不必要地影响性能。

安全报告与跟踪系统的客户，使用有关，可以包括:

- 审核用户操作;这就要求记录由日期和时间以及每个用户执行的单个请求。数据的结构应以使管理员能够快速重建在指定的时间段内由特定用户执行的操作序列。
- 跟踪资源使用的用户;这就要求如何每个请求的用户访问组成系统的各种资源，如何长时间的记录。管理员必须能够使用此数据来生成利用报告由用户在指定的时间段，可能出于计费。

在许多情况下，可以通过批处理过程根据定义的计划生成报告 (延迟是不正常的问题)，但他们也应提供在特设的基础，如果需要上一代人。 作为一个例子，如果你将数据存储在关系数据库，如 SQL Azure 数据库，你可以使用 SQL 服务器报告服务之类的工具来提取和设置数据的格式并把它作为一套的报告。

## 相关的模式和指导
- 自动缩放指导介绍如何减少管理开销通过减少运营商不断地监视系统的性能和做出决定有关添加或删除资源的需要。
- 的 [健康终结点监测模式](https://msdn.microsoft.com/library/dn589789.aspx) 描述如何实现内外部工具可以在定期访问通过暴露的终结点的应用程序的功能检查。
- 的 [优先级队列](https://msdn.microsoft.com/library/dn589794.aspx) 模式显示如何划分优先级队列中的邮件，以便紧急请求被接受，并且可以在不太紧急的邮件之前处理。

## 更多的信息
- 这篇文章 [监测、 诊断和解决微软 Azure 存储](storage-monitoring-diagnosing-troubleshooting.md) 在微软的网站。
- 这篇文章 [Azure: 遥测基础知识和故障排除](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) 在微软的网站。
- 页面 [使用 Azure 的诊断收集日志记录数据](https://msdn.microsoft.com/library/azure/gg433048.aspx) 在微软的网站。
- 页面 [配置诊断 Azure 的云服务和虚拟机](https://msdn.microsoft.com/library/azure/dn186185.aspx) 在微软的网站。
- 的 [天青穿红衣的缓存](http://azure.microsoft.com/services/cache/), [蔚蓝 DocumentDB](http://azure.microsoft.com/services/documentdb/)和 [HDInsight](http://azure.microsoft.com/services/hdinsight/) 在 Microsoft 网站上的页面。
- 页面 [如何使用服务总线队列](http://azure.microsoft.com/) 在微软的网站。
- 这篇文章 [SQL 服务器商业智能在蔚蓝的虚拟机](https://msdn.microsoft.com/library/azure/jj992719.aspx) 在微软的网站。
- 页面 [监视警报和通知在 Azure 中的理解](https://msdn.microsoft.com/library/azure/dn306639.aspx) 在微软的网站。
- 的 [应用程序的见解](app-insights-get-started/) 在 Microsoft 网站上的页面。
