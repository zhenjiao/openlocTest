<properties
   pageTitle="Background jobs guidance | Microsoft Azure"
   description="Guidance on background tasks that run independently of the user interface."
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

# 后台作业指导

![](media/best-practices-background-jobs/pnp-logo.png)


## 概述

许多类型的应用程序需要运行独立的用户界面 (UI) 的后台任务。例子包括批处理作业，密集处理任务，和长时间运行的工作流的过程。后台作业可以执行而无需用户交互;应用程序可以开始这项工作，然后继续处理来自用户的交互请求。这可以帮助减少应用程序 UI 可以提高可用性，降低交互式响应时间上的负载。

例如，如果应用程序由用户上传的图像的缩略图生成所需的它可以做这作为后台作业并没有用户需要等待进程完成将缩略图保存到存储完成后。同样的方式，在下订单的用户可以启动背景工作流处理的顺序，而用户界面允许用户继续浏览本网站。后台作业完成时，它可以更新存储的订单数据，并发送一封电子邮件给用户确认订单。

当考虑是否要执行一项任务作为后台作业，主要的标准是否任务可以运行没有用户交互和用户界面需要等待要完成的工作。需要用户或 UI 等待它们完成的任务可能不适宜作为后台作业。

## 类型的后台作业

后台作业通常有一个或多个以下特性:

- CPU 密集型岗位如数学计算、 结构模型的分析，以及更多。
- 如执行一系列存储交易或索引文件 I/O 密集型的就业。
- 如夜间数据更新或计划的处理的批处理作业。
- 长时间运行的订单执行的工作流或资源调配服务和系统。
- 敏感的数据处理任务关闭交给更安全的地点进行处理。例如，你可能不想处理 web 角色内的敏感数据，如改用一种模式 [网关守卫](http://msdn.microsoft.com/library/dn589793.aspx) 要将数据传输到孤立的背景角色有权访问受保护的存储。

## 触发器

后台作业可以在多种不同的方式启动。有效，他们都属于以下类别之一:

- [**事件驱动的触发器**](#event-driven-triggers).在响应的事件，通常由用户或工作流中的步骤而采取的行动是在任务的开始。
- [**附表驱动触发器**](#schedule-driven-triggers).这项任务被调用基于计时器的计划。这可能是反复出现的调度或指定在稍后时间一次性调用

### 事件驱动的触发器

事件驱动的调用使用触发器来启动后台任务。使用事件驱动的触发器的例子包括:

- 用户界面或另一份工作在队列中放置消息。该消息包含有关采取行动，有的地方，如用户订货数据。后台任务侦听此队列和检测新消息到达。它读取该消息，并使用数据在它作为后台作业的输入。
- 用户界面或另一份工作保存或更新中存储的值。后台任务监视存储并检测到更改。它读取数据并使用它作为后台作业的输入。
- 用户界面或另一份工作对端点，例如 HTTPS URI 发出请求或作为 web 服务公开的 API。它将传递作为请求的一部分完成后台任务所需的数据。终结点或 web 服务调用的后台任务，使用的数据作为其输入。

适合于事件驱动调用任务的典型例子包括图像处理、 工作流，将信息发送到远程服务，发送电子邮件，资源调配多租户应用程序，和更多的新用户。

### 附表驱动触发器

附表驱动调用使用计时器来启动后台任务。使用附表驱动触发器的示例包括:

- 本地运行应用程序内部或应用程序的操作系统的一部分作为一个定时器调用定期后台任务。
- 运行在不同的应用程序或计时器服务如 Azure 调度器，计时器将请求发送到定期 API 或 web 服务。该 API 或 web 服务调用的后台任务。
- 一个单独的进程或应用程序启动一个计时器，导致后台任务要调用一次指定的时间延迟后，或在特定的时间。

适合于附表驱动调用任务的典型例子包括批量处理例程，如更新相关的产品列表基于他们最近的行为，常规的数据处理任务，如更新索引或生成为用户积累数据的每日报告、 数据保留清理、 数据一致性检查，分析的结果。

如果你使用驱动必须作为单个实例运行的任务的日程安排，请注意以下内容:

- 如果缩放运行调度 (如使用 Windows 任务计划程序的虚拟机) 的计算实例，您将运行调度程序的多个实例和这些可以启动任务的多个实例。
- 如果任务运行时间超过期间调度事件之间，调度程序可能开始的任务的另一个实例前, 一个仍在运行。

## 返回结果

后台作业异步执行一个单独的进程或甚至是一个单独的位置，从 UI 或调用后台任务的过程中。理想情况下，后台任务是"fire and forget"的操作，而且其执行进度没有影响 UI 或调用进程。这意味着调用进程不会等待完成的任务，因此无法自动检测到该任务结束时。
如果你需要一个后台任务，沟通要显示进度或完成的调用任务，您必须为此实现机制。是一些示例:

- 状态指标值写入访问的 UI 或调用方的任务，可以监视或检查此值时所需的存储。后台任务必须返回到调用方的其他数据可以放入同一存储。
- 建立 UI 或调用方侦听应答队列。后台任务可以向指示状态和完成队列发送消息。后台任务必须返回到调用方的数据可以放入邮件。如果您正在使用 Azure 服务总线，则可以使用 **回复** 和 **找到** 要实现此功能的属性。有关更多信息，请参见 [在服务总线相关斡旋消息](http://www.cloudcasts.net/devguide/Default.aspx?id=13029).
- 公开的 API 或终结点从 UI 或调用方可以访问以获取状态信息的后台任务。后台任务必须返回到调用方的数据可以包含在响应。
- 有回叫用户界面或通过 API 调用方以指示状态在预定义点或完成后的后台任务。这可能是通过本地，引发的事件或通过发布和订阅机制。后台任务必须返回到调用方的数据可以包含在请求或事件的有效载荷。

## 宿主环境

你可以承载后台任务使用一系列不同 Azure 平台服务:

- [**蔚蓝的 Web 站点**](#azure-web-sites-and-webjobs).你可以使用 WebJobs 来执行基于一系列不同类型的脚本或可执行程序的网站范围内的自定义作业。
- [**Azure 云服务 web 和工作者角色**](#azure-cloud-services-web-and-worker-roles).您可以编写代码内作为后台任务执行的作用。
- [**蔚蓝的虚拟机**](#azure-virtual-machines).如果你有一个 Windows 服务或您想要使用 Windows 任务计划程序，它是共同主办您专用的虚拟机中的后台任务。

以下各节描述了每个这些选项的详细信息，和包括考虑以帮助您选择适当的选项。

### 蔚蓝的 Web 站点和 WebJobs

你可以使用 Azure WebJobs 执行后台任务在 Azure 网站托管应用程序中的自定义工作。WebJobs 可以运行脚本或可执行程序在您的网站范围内作为一个连续的过程，或者在从 Azure 调度程序或更改外部因素触发事件响应存储 blob 和消息队列。作业可以开始和停止对需求，正常关闭。如果连续运行 WebJob 失败，它会自动重新启动。重试和错误的行动都是可配置的。

当配置 WebJob:

- 如果你想要这份工作，来响应一个事件驱动的触发器，它应配置为 **连续运行**.该脚本或程序存储在名为网站/wwwroot/应用程序的文件夹中_数据/工作/连续。
- 如果你想要回应驱动触发一个时间表的作业，应将其配置为 **按计划运行**.该脚本或程序存储在名为网站/wwwroot/应用程序的文件夹中_数据/工作/触发。
- 如果您选择 **按需运行** 选择当您配置一份工作，它将执行相同的代码 **按计划运行** 当你启动它的选项。

蔚蓝的 WebJobs 网站，这意味着他们可以访问的环境变量，并与网站共享信息，如连接字符串的沙箱内运行。这项工作已进入机器运行作业的唯一标识符。名为的连接字符串 **AzureJobsStorage** 消息传递和通信提供 Azure 存储队列，blob 和表对应用程序数据和服务总线访问。名为的连接字符串 **AzureJobsDashboard** 提供对作业操作日志文件的访问。

天青 WebJobs 具有以下特点:

- **安全**: WebJobs 受该网站的部署凭据。
- **支持的文件类型**: WebJobs 可以使用命令脚本 (.cmd)，批处理文件 (.bat)，PowerShell 脚本 (.ps1) 定义，bash shell 脚本 (.sh)、 PHP 脚本 (.php)，Python 脚本 (.py)、 JavaScript 代码 (.js) 和可执行程序 (.exe、.jar，等等)。
- **部署**: 脚本和可执行文件可以使用 Azure 门户，创建和使用的部署部署 [WebJobsVs](https://visualstudiogallery.msdn.microsoft.com/f4824551-2660-4afa-aba1-1fcc1673c3d0) 在添加 Visual studio 或 [视觉工作室 2013年更新 4](http://www.visualstudio.com/news/vs2013-update4-rc-vs)通过使用 [天青 WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md)或通过复制他们直接到以下位置:
  - 为触发执行: 网站/wwwroot/应用程序_数据工作触发 / {职位名称}
  - 连续执行: 网站/wwwroot/应用程序_数据/工作连续 / {职位名称}
- **日志记录**: Console.Out 作为信息和 Console.Error 作为错误处理 (标记)。可以使用 Azure 门户访问监视和诊断信息和日志文件可以直接从网站上下载。它们保存在以下位置:
  - 为触发执行: Vfs/数据/工作/连续/jobName
  - 连续执行: Vfs/数据/工作/触发/jobName
- **配置**: 可以使用门户、 REST API 和 PowerShell 配置 WebJobs。配置文件命名为 settings.job 在同一根目录中作业脚本可以被用来提供一份工作的配置信息。例如:
  - {"停止_等待_时间": 60}
  - {"是_单身": 真实}

### 注意事项

- 默认情况下，与该网站 WebJobs 规模。然而，作业可以配置为在单个实例上运行通过设置 **是_单身人士** 配置属性设置为 true。单个实例 WebJobs 可用于你不想要扩展或作为同步多路运行的任务实例，如重新索引、 数据分析和相似的任务。
- 为了尽量减少对性能的网站工作的影响，请考虑创建空 Azure 网站实例在新应用程序服务，计划到主机可能是长时间运行的 WebJobs 或资源密集。

### 更多的信息

- [天青 WebJobs 推荐资源](websites-webjobs-resources/) WebJobs 列出了很多有用的资源、 下载和样品。

## Azure 云服务 web 和工作者角色

在 web 角色范围内或在一个单独的工作角色，可以执行后台任务。决定是否要使用一个工人应该思考的可伸缩性和弹性要求，任务的一生中，基于角色释放节奏、 安全、 容错、 争用、 复杂性和逻辑体系结构。有关更多信息，请参见 [计算资源整合模式](http://msdn.microsoft.com/library/dn589778.aspx).

有几种方法来执行后台任务在云服务角色范围内:

- 创建一个实现 **RoleEntryPoint** 类中的作用和使用它的方法来执行后台任务。任务 WaIISHost.exe 的上下文中运行，可以使用 **通过** 方法 **CloudConfigurationManager** 要加载的配置设置的类。有关更多信息，请参见 [生命周期 (云服务)](#lifecycle-cloud-services-).
- 使用启动任务要在应用程序启动时执行后台任务。强迫的任务继续运行在后台设定 **taskType** 属性设置为 **背景** (如果你不这样做，应用程序启动进程将停止，等待要完成的任务)。有关更多信息，请参见 [在 Azure 运行启动任务](http://msdn.microsoft.com/library/azure/hh180155.aspx).
- 使用 WebJobs SDK 来实现作为启动任务启动的 WebJobs 作为后台任务。有关更多信息，请参见 [入门蔚蓝 WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md).
- 使用启动任务可以安装一个 Windows 服务，执行一个或多个后台任务。您必须设置 **taskType** 属性设置为 **背景** 因此，该服务在后台执行。有关更多信息，请参见 [在 Azure 运行启动任务](http://msdn.microsoft.com/library/azure/hh180155.aspx).

### 在 web 角色中运行后台任务

在 web 角色中运行后台任务的主要优点是在托管成本，因为没有要求部署额外的角色，所以节省。

### 在工作者角色的运行后台任务。

在辅助角色中运行后台任务有几个优点:

- 它允许您管理分别缩放为每种类型的角色。例如，您可能需要多个实例的 web 角色，以支持当前的负载，但较少的执行后台任务的辅助角色实例。缩放背景任务计算实例分别从 UI 角色可以降低托管成本，同时维持可接受的性能。
- 它卸载 web 角色从后台任务的处理开销。提供用户界面的 web 角色可以保持响应，而这可能意味着支持给定的体积的来自用户的请求所需的较少的情况。
- 它允许您实现关注点分离。每个角色类型可以实现一组特定的明确定义和相关的任务。这使得设计和维护代码更容易，因为有较少的代码和功能之间的每个角色相互依存的关系。
- 它可以帮助隔离敏感流程和数据。例如，实现用户界面的 web 角色不需要访问数据的管理与控制的辅助角色。这可以有助于加强安全，尤其是当使用模式如 [看门人模式](http://msdn.microsoft.com/library/dn589793.aspx).  

### 注意事项

选择如何以及在何处时考虑以下各点时使用云服务 web 和工作者角色部署后台任务:

- 主机后台任务在现有的 web 作用，可以节约成本的运行这些任务，只是单独的工作角色，但它是可能会影响性能和可用性的应用程序，如果有是处理和其他资源的争夺。使用一个单独的工作角色保护 web 角色从长时间运行或资源的集约化背景任务的影响。
- 如果你的主机使用的后台任务 **RoleEntryPoint** 类，您可以轻松移动这给另一个角色。例如，如果您创建类 web 角色，后来又决定你需要运行的任务的辅助角色，您可以移动 **RoleEntryPoint** 类执行到辅助角色。
- 启动任务旨在执行程序或脚本。部署的可执行程序作为后台作业可能更加困难，尤其是如果它也需要依赖程序集的部署。它可能是易于部署和使用一个脚本来定义后台作业时使用启动任务。
- 导致背景任务失败的异常都有不同的影响，取决于他们托管的方式:
  - 如果您使用 **RoleEntryPoint** 类方法，失败的任务将导致角色重新启动，该任务会自动重新启动。这可能会影响应用程序的可用性。若要防止这种情况，确保你包括健壮的异常处理中 **RoleEntryPoint** 类和所有后台任务。使用代码来重新启动任务失败在哪里，这是适当的并引发异常，以便重新启动作用，只是如果你不能优雅地从故障中恢复您的代码中。
  - 如果您使用启动任务，你是负责管理任务的执行和检查如果它失败了。
- 管理和监视启动任务是比使用更难 **RoleEntryPoint** 类方法。然而，Azure WebJobs SDK 包括一个仪表板，使它更易于管理您启动通过启动任务的 WebJobs。

### 更多的信息

- [计算资源整合模式](http://msdn.microsoft.com/library/dn589778.aspx)
- [入门蔚蓝 WebJobs SDK](websites-dotnet-webjobs-sdk-get-started/)

## 蔚蓝的虚拟机

这可能不方便或后台任务可能实现防止被部署到 Azure 网站或云服务的方式。典型的例子是 Windows 服务和第三方实用程序和可执行程序。它还可能包括编写的执行环境不同于承载该应用程序; 程序例如，它可能是一个 Unix 或 Linux 的程序，你想要从 Windows 或.NET 的应用程序执行。你可以选择从广泛的操作系统 Azure 的虚拟机，并在该虚拟机上运行您的服务或可执行文件。

若要帮助您选择何时使用虚拟机，请参阅 [蔚蓝的网站、 云服务和虚拟机比较](choose-web-site-cloud-service-vm.md).有关选项的信息 [虚拟机，请参阅虚拟机和 azure 云服务大小](http://msdn.microsoft.com/library/azure/dn197896.aspx).为虚拟机的操作系统和可用的预构建的图像的更多信息，请参见 [蔚蓝的虚拟机画廊](http://azure.microsoft.com/gallery/virtual-machines/).

要启动后台任务在一个单独的虚拟机，您有一系列选项:

- 你可以通过执行任务需求直接从您的应用程序发送一个请求到任务公开，在这项任务需要的任何数据将传递一个端点。此端点调用任务。
- 您可以配置要使用调度程序或在您选择的操作系统可用的计时器如期运行的任务。例如，在 Windows 上您可以使用 Windows 任务计划程序来执行脚本和任务，或者，如果您已经在虚拟机上安装的 SQL 服务器，您可以使用 SQL Server 代理执行脚本和任务。
- 您可以使用 Azure 计划程序通过将消息添加到任务，监听队列或者向任务公开 API 发送一个请求启动任务。

请参阅前面一节 [触发器](#triggers) 了解更多的信息，您可以启动后台任务。  

### 注意事项

在决定是否部署 Azure 的虚拟机中的后台任务时，请考虑以下几点:

- 主办后台任务在一个单独的蔚蓝虚拟机提供了灵活性，并允许精确地控制启动、 执行、 调度和资源分配。然而，它会增加运行成本，如果必须部署虚拟机，只是为了运行后台任务。
- 有是没有监测中蔚蓝的门户和失败的任务，没有自动重启能力的任务，虽然你可以监视虚拟机的基本状态和管理使用的设施 [Azure 服务管理 Cmdlet](http://msdn.microsoft.com/library/azure/dn495240.aspx).然而，没有设施，以控制进程和线程的计算节点。通常，使用虚拟机将需要额外的工作来实现从在任务中，仪器仪表和操作系统在虚拟机中收集数据的机制。可能是适当的一个解决方案是使用 [Azure 系统中心管理包](http://technet.microsoft.com/library/gg276383.aspx).
- 您可以考虑创建通过 HTTP 端点公开的监控探头。这些探测器的代码可以执行健康检查、 收集业务信息和统计数据，或整理错误的信息，和返回到一个管理应用程序。有关更多信息，请参见 [健康终结点监测模式](http://msdn.microsoft.com/library/dn589789.aspx).

### 更多的信息

- [虚拟机](http://azure.microsoft.com/services/virtual-machines/) 在蔚蓝的网站上
- [蔚蓝的虚拟机常见问题](http://msdn.microsoft.com/library/azure/dn683781.aspx)

## 设计考虑事项

有几个基本因素设计后台任务时要考虑。以下各节讨论分区、 冲突和协调。

## 分区

如果您决定包括后台任务 (如网站、 web 角色，现有工人角色或虚拟机) 的现有计算实例中，您必须考虑这将如何影响的计算实例和后台任务本身的质量属性。这些因素将帮助您决定是否要共同查找的任务与现有的计算实例，或将它们分离出去到一个单独的计算实例:

- **可用性**: 背景任务可能不需要有相同级别的可用性作为其他应用的部分，特别是在 UI 和其他部件直接参与用户交互的。后台任务可能的延迟，再审的连接失败，多些宽容，因为可以排队操作的其它因素，影响可用性。然而，必须有足够的能力来防止备份可以阻止队列而影响整个应用程序的请求。
- **可扩展性**: 背景任务很可能有一个不同的可伸缩性要求对用户界面和应用程序的交互部分。缩放用户界面可能需要满足高峰需求，而优秀的后台任务可以在完成少忙时期少数量计算实例。
- **可恢复性**: 失败只是主机后台任务在如果为这些任务请求可以排队或推迟到任务不可能致命影响整个应用程序的计算实例将再次可用。如果计算实例和/或任务可以在适当的时间间隔内重新启动，可能不会影响应用程序的用户。
- **安全**: 背景任务可能具有不同的安全要求或限制比 UI 或应用程序的其他部分。通过使用一个单独的计算实例，您可以指定一个不同的安全环境的任务。也可以使用模式，如网关守卫来隔离从 UI 背景计算实例以最大限度的安全和分离。
- **性能**: 您可以选择后台任务具体匹配任务的性能要求的计算实例的类型。这可能意味着如果任务都不需要用户界面或一个较大的实例相同的处理功能，如果他们需要更多的能力和资源使用成本较低的计算选项。
- **可管理性**: 背景任务可能具有不同的开发和部署节奏从主应用程序代码或用户界面。将它们部署到一个单独的计算实例可以简化更新和版本控制。
- **成本**: 添加计算实例执行后台任务托管成本的增加。您应该仔细考虑额外的容量与这些额外的成本之间的权衡。

有关更多信息，请参见 [领导人选举模式](http://msdn.microsoft.com/library/dn568104.aspx) 和 [竞争消费者模式](http://msdn.microsoft.com/library/dn568101.aspx).

## 冲突

如果您有多个实例的后台作业，很可能他们会争夺资源和服务，如数据库和存储的访问。这种并发访问可能会导致资源争用，可能会导致冲突，在提供的服务和存储中的数据的完整性。可以通过使用悲观锁定方法来防止解决资源争用竞争实例同时访问的服务，或破坏数据的任务。

另一种方法来解决冲突是定义后台任务作为一个单一实例，这样就永远都只有一个实例运行。然而，这消除了多实例配置可以提供，可靠性和性能的好处，尤其是如果 UI 可以提供足够的工作，以保持多个后台任务忙。至关重要的是确保，后台任务可以自动重新启动，和它有足够的能力应付高峰需求。这可以通过分配足够的资源，计算实例，通过实施可以存储供以后执行需求减少时，请求排队机制或通过这些技术的组合。

## 协调

后台任务可能非常复杂，同时需要多个单个任务执行产生的结果或未能满足所有的要求。它是共同的在这些情况下，将任务划分为较小的谨慎步骤或可以由多个使用者执行的子任务。多步工作可能会更有效率、 更灵活，因为个别步骤可能会在多个工作岗位中可以重用。它也很容易添加、 删除或修改步骤的顺序。

协调多个任务和步骤可以具有挑战性，但有三种常见的模式，你可以使用来指导您的解决方案的实现:

- **将任务分解为多个可重复使用的步骤**.应用程序可能需要对 it 流程的信息执行各种任务的复杂程度不同。一个简单但不够灵活的方法，实施此应用程序可以执行此处理作为整体模块。然而，这种做法很可能减少重构代码，优化它，或重用它，如果同一加工零件所需的应用程序内的其他地方的机会。有关更多信息，请参见 [管道和过滤器模式](http://msdn.microsoft.com/library/dn568100.aspx).
- **管理执行的任务的步骤**.应用程序可能会执行包括若干步骤，其中一些可能会调用远程服务或访问远程资源的任务。各个步骤可能独立于彼此，但他们都精心策划的实现任务的应用程序逻辑。有关更多信息，请参见 [调度程序代理主管模式](http://msdn.microsoft.com/library/dn589780.aspx).
- **管理的任务失败的步骤恢复**.应用程序可能需要撤消执行一系列步骤，它们一起定义最终一致的操作，如果一个或多个步骤失败的工作。有关更多信息，请参见 [补偿的交易模式](http://msdn.microsoft.com/library/dn589804.aspx).

## 生命周期 (云服务)

 如果您决定实现云服务的应用程序通过使用 web 和工作者角色的后台作业 **RoleEntryPoint** 类，它是重要的是了解此类的生命周期，以便正确使用。

Web 和工人的角色去通过一组不同的阶段，他们启动、 运行和停止。的 **RoleEntryPoint** 类公开一系列事件，表明这些阶段发生的时候。你使用这些初始化，运行，并停止您自定义的后台任务。完整的周期是:

- Azure 角色集加载和从派生的类中搜索 **RoleEntryPoint**.
- 如果它找到此类，它调用 **RoleEntryPoint.OnStart()**.你重写此方法来初始化您的后台任务。
- 后 **OnStart** 方法完成后，Azure 调用 **应用程序_Start)** 在应用程序的全局文件如果这存在 (例如，在运行 ASP.NET web 角色 Global.asax)。
- 蔚蓝的电话 **RoleEntryPoint.Run()** 在新的前台线程与并行执行 **OnStart()**.你重写此方法来启动你的后台任务。
- Run 方法结束时，Azure 首先调用 **应用程序_End)** 在应用程序的全局文件如果这是礼物，然后调用 **RoleEntryPoint.OnStop()**.你重写 **OnStop** 方法停止你的后台任务，清理资源，处置的对象，并关闭任务可能已经在使用的连接。
- 天青工作者角色主机进程已停止。在这一点上的作用将被回收，并将重新启动。

有关更多详细信息和使用的方法的示例 **RoleEntryPoint** 同学们，请参见 [计算资源整合模式](http://msdn.microsoft.com/library/dn589778.aspx).

## 注意事项

规划你将如何运行后台任务在 web 或工人的角色时，请考虑以下几点:

- 默认值 **运行** 中的方法实现 **RoleEntryPoint** 类包含调用 **Thread.Sleep(Timeout.Infinite)** 这使得作用无限期地活着。如果您重写 **运行** 方法 (这是通常需要执行后台任务) 你不能允许您的代码以退出该方法，除非你想要回收的角色实例。
- 一个典型的实现 **运行** 方法包括启动每个后台任务和一种循环构造，定期检查所有后台任务的状态代码。它可以重新启动任何失败或监视取消标记，表明工作已经完成。
- 如果后台任务引发未处理的异常，同时允许任何其他后台任务中的作用继续运行应回收这项任务。然而，如果异常造成腐败的对象以外的任务，如共享存储，处理该异常应该由你 **RoleEntryPoint** 类，所有任务都应该取消，和 **运行** 方法允许结束。Azure 将重新启动作用。
- 使用 **OnStop** 要暂停或杀死后台任务和清理资源的方法。这可能涉及停止长时间运行或多步的任务，并要考虑如何做这以避免数据不一致的关键。如果角色实例停止用户启动关机，运行的代码以外的任何原因 **OnStop** 方法必须在被强行终止前的五分钟内完成。确保您的代码可以完成在那个时候，或可以容忍不运行完成。  
- 蔚蓝的负载平衡器开始指挥交通的作用实例时 **RoleEntryPoint.OnStart** 方法返回 true。因此，考虑将您的所有初始化代码都放 **OnStart** 方法以便不成功地初始化意志的角色实例不接收任何通信。
- 您可以使用启动任务的方法 **RoleEntryPoint** 类。你应该使用启动任务来初始化您需要更改在 Azure 负载平衡器，因为这些任务的执行角色收到的任何请求之前的任何设置。有关更多信息，请参见 [在 Azure 运行启动任务](http://msdn.microsoft.com/library/azure/hh180155.aspx).
- 如果在启动任务中有错误，它可能会迫使要不断重启的角色。这可以阻止你执行 VIP 换回以前上演的版本，因为交换需要独占访问的作用，并且作用重新启动时，这就不能获得。要解决此问题:
	-  将以下代码添加到的开头 **OnStart** 和 **运行** 在你的角色的方法:

	```C#
	var freeze = CloudConfigurationManager.GetSetting("Freeze");
	if (freeze != null)
	{
		if (Boolean.Parse(freeze))
	  	{
		    Thread.Sleep(System.Threading.Timeout.Infinite);
		}
	}
	```

   - 添加的定义 **冻结** 作为一个布尔值，设置为 ServiceDefinition.csdef 和服务配置。*.cscfg 文件的作用并将它设置为 **假**.如果角色进入反复重新启动模式后，您可以将设置更改为 **真正的** 要冻结作用执行并允许它被换用以前的版本。

## 可恢复性方面的考虑

后台任务必须弹性提供可靠的服务，给应用程序。规划设计后台任务时，请考虑以下几点:

- 后台任务必须能够优雅地处理角色或服务重新启动，而不会损坏数据或应用程序中引入不一致。对于长时间运行或多步的任务，请考虑使用 _检查指_ 通过保存的工作状态在持久性存储，或作为队列中的消息，如果这是适当的。例如，你可以坚持在队列中的邮件的状态信息和任务进度与增量更新此状态信息，这样就可以从上一个已知良好检查点不用重启电脑从一开始处理任务。当使用 Azure 服务总线队列时，可以使用消息会话，使同样的场景。会议允许您保存和检索应用程序处理状态通过使用 [SetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.setstate.aspx) 和 [GetState](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.messagesession.getstate.aspx) 方法。关于设计可靠的多步流程和工作流的详细信息，请参阅 [调度程序代理主管模式](http://msdn.microsoft.com/library/dn589780.aspx).
- 当使用 web 或工人角色来承载多个后台任务，设计的重写 **运行** 方法以监视失败或停滞的任务，并重新启动它们。这是不实际的和你使用的辅助角色，迫使工作者角色重新启动退出 **运行** 方法。
- 当使用队列来沟通与后台任务，队列可以充当一个缓冲区来存放发送到任务，同时应用程序能在高于通常负载下的请求。这允许任务少非繁忙时段赶上来的 UI。这也意味着循环作用，不会阻止用户界面。有关更多信息，请参见 [基于队列的载荷平衡模式](http://msdn.microsoft.com/library/dn589783.aspx).如果某些任务比其他人更重要，请考虑实施 [优先队列模式](http://msdn.microsoft.com/library/dn589794.aspx) 为确保这些任务运行之前不那么重要的。
- 发起的或以其他方式处理消息的后台任务必须设计为处理不一致等消息到达顺序，一再导致错误 (通常称为的消息 _带毒邮件_)，并不止一次发送的邮件。考虑下列因素:
  - Messages that must be processed in a specific order, such as those that change data based on its existing value (for example, adding a value to an existing value), may not arrive in the original order they were sent. Alternatively, they may be handled by different instances of a background task in a different order due to varying loads on each instance. Messages that must be processed in a specific order should include a sequence number, key, or some other indicator that background tasks can use to ensure they are processed in the correct order. If you are using Azure Service Bus, you can use message sessions to guarantee the order of delivery. However, it is usually more efficient where possible to design the process so that the message order is not important.
  - Typically, a background task will peek messages in the queue, which temporarily hides them from other message consumers, and then delete the messages after they have been successfully processed. If a background task fails when processing a message, that message will reappear on the queue after the peek timeout expires, and will be processed by another instance of the task or during the next processing cycle of this instance. If the message consistently causes an error in the consumer, it will block the task, the queue, and eventually the application itself when the queue becomes full. Therefore, it is vital to detect and remove poison messages from the queue. If you are using Azure Service Bus, messages that cause an error can be moved automatically or manually to an associated dead letter queue.
  - 在保证队列 _至少每一次_ delivery mechanisms, but they may deliver the same message more than once. In addition, if a background task fails after processing a message but before deleting it from the queue, the message will become available for processing again. Background tasks should be idempotent, which means that processing the same message more than once does not cause an error or inconsistency in the application’s data. Some operations are naturally idempotent, such as setting a stored value to a specific new value. However, operations such as adding a value to an existing stored value without checking that the stored value is still the same as when the message was originally sent will cause inconsistencies.  Azure Service Bus queues can be configured to automatically remove duplicated messages.
  - 某些消息传递系统中的，如 Azure 存储队列和 Azure 服务总线队列，支持 de-queue count 属性，指示消息已经从队列中读取的次数。这可以在处理重复和毒药的消息。有关更多信息，请参见 [异步消息传递的底漆](http://msdn.microsoft.com/library/dn589781.aspx) 和 [幂等性模式](http://blog.jonathanoliver.com/2010/04/idempotency-patterns/).

## 缩放和性能方面的考虑

后台任务必须提供足够的性能，以确保他们不会阻止应用程序，或导致延迟手术的不一致，当系统的负载。通常情况下，通过缩放主机后台任务的计算实例提高性能。规划设计后台任务时，考虑以下各点周围可伸缩性和性能:

- Azure 支持自动缩放 (外扩和相应缩减) 基于当前的需求和负载，或在一个预定义的时间表，对于网站来说，云服务网站和工作者角色和虚拟机托管部署。使用此功能来确保应用程序作为整个运行时成本最小化的同时有足够的性能。
- 后台任务有不同的性能，其他各地的云服务应用程序 (例如，用户界面或组件，例如数据访问层)，主办后台任务一起在一个单独的工作角色允许的 UI 和背景要独立扩展来管理负载的任务角色。如果多个后台任务已经从彼此明显不同的表现能力，可以考虑将它们划分为单独的工作角色和缩放每个角色类型独立，但请注意，这可能会增加运行时成本相比，将所有的任务组合成较少的角色。
- 只缩放的角色不可能足以防止在荷载作用下的性能损失。您可能还需要扩展存储队列和其他资源，以防止单点的整体加工链将成为一个瓶颈。另外，还要考虑其他的限制，例如存储的最大吞吐量和其他服务及应用程序依赖于后台任务。
- 后台任务必须用于缩放。例如，他们必须能够动态地检测中使用以侦听或将消息发送到相应的队列的存储队列的数目。
- 默认情况下，WebJobs 规模与它们相关联的 Azure 网站实例。然而，如果你想要的 WebJob 作为只能运行一个实例运行，您可以创建一个包含 JSON 数据的 Settings.job 文件 **{"是_单身": 真实}**.这就迫使 Azure，只能运行一个实例的 WebJob，即使有关联的网站，可以是一个有用的技术，对于计划作业，必须作为只能运行一个实例运行的多个实例。

## 相关的模式

- [异步消息传递的底漆](http://msdn.microsoft.com/library/dn589781.aspx)
- [自动缩放指导](http://msdn.microsoft.com/library/dn589774.aspx)
- [补偿的交易模式](http://msdn.microsoft.com/library/dn589804.aspx)
- [竞争消费者模式](http://msdn.microsoft.com/library/dn568101.aspx)
- [计算分区指导](http://msdn.microsoft.com/library/dn589773.aspx)
- [计算资源整合模式](http://msdn.microsoft.com/library/dn589778.aspx)
- [看门人模式](http://msdn.microsoft.com/library/dn589793.aspx)
- [领导人选举模式](http://msdn.microsoft.com/library/dn568104.aspx)
- [管道和过滤器模式](http://msdn.microsoft.com/library/dn568100.aspx)
- [优先队列模式](http://msdn.microsoft.com/library/dn589794.aspx)
- [基于队列的载荷平衡模式](http://msdn.microsoft.com/library/dn589783.aspx)
- [调度程序代理主管模式](http://msdn.microsoft.com/library/dn589780.aspx)

## 更多的信息

- [缩放与工作者角色的 Azure 应用程序](http://msdn.microsoft.com/library/hh534484.aspx#sec8)
- [执行后台任务](http://msdn.microsoft.com/library/ff803365.aspx)
- [Azure 角色启动生命周期](http://blog.syntaxc4.net/post/2011/04/13/windows-azure-role-startup-life-cycle.aspx) (博文)
- [Azure 的云服务角色生命周期](http://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Windows-Azure-Cloud-Services-Role-Lifecycle) (视频)
- [入门蔚蓝 WebJobs SDK](websites-dotnet-webjobs-sdk-get-started/)
- [蔚蓝的队列和服务总线队列-比较和对比](http://msdn.microsoft.com/library/hh767287.aspx)
- [如何启用诊断在云服务中](http://msdn.microsoft.com/library/dn482131.aspx)
