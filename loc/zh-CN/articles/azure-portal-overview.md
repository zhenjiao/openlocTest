<properties
	pageTitle="Microsoft Azure preview portal overview"
	description="Learn how to use the Microsoft Azure preview portal."
	services=""
	documentationCenter=""
	authors="davidwrede"
	manager="dwrede"
	editor="jimbe"/>

<tags
	ms.service="na"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na" 
	ms.topic="hero-article"
	ms.date="04/28/2015"
	ms.author="dwrede"/>

# 微软 Azure 预览门户概览

微软 Azure 预览门户是一个中央的地方，在那里你可以调配和管理你蔚蓝的资源。 本教程将使您熟悉门户并向您展示如何使用一些这些关键功能:
- A **综合市场** 这允许您浏览通过成千上万个来自 Microsoft 和其他供应商可以购买和/或设置项。
- A **统一、 可扩展的浏览体验** 这样一来，我们就容易找到你关心和执行各种管理操作的资源。
- **一致的管理页面** (或刀片)，让您管理 Azure 的范围广泛的各种服务，通过一致的方式来暴露设置、 操作、 计费信息、 健康监测和使用数据，和很多更多。
- A **个人经验** 这允许您创建一个自定义的启动屏幕，显示的信息，你想要东西的时候，你登录。 您还可以自定义任何包含瓷砖管理刀片。

 ![蔚蓝的门户 UI 方向][UIOrientation]

## 在你开始之前

您将需要有效的蔚蓝订阅去通过本教程。 如果你没有一个，然后 [注册免费试用](http://azure.microsoft.com/pricing/free-trial/) 今天。 一旦你有订阅，您可以访问在门户 [https://portal.azure.com].

## 如何创建资源

Azure 有数以千计的项目，您可以创建从一个地方的市场。 假设您想要创建一个新的 Windows 服务器 2012 VM。 + 新集线器是您成热门类别从市场策划组的切入点。 每个类别有一小套的特色项目，并提供一个链接到显示所有类别和搜索的全部市场。若要创建这种新的 Windows 服务器 2012 VM，请执行以下操作:  

1.	Windows Server 2012 被精选的所以你可以从计算类别中选择它。  
2.	填写一些基本的输入窗体上。
3.	单击创建和你是 VM 将开始立即提供。

通知中心会提醒您，当您的资源已创建和管理刀片将打开 (你总是可以浏览到资源后)。

![门户网站类别][PortalCategories]


## 如何查找您的资源

你总是可以销频繁访问资源到你的 startboard，但您可能需要浏览到你不经常访问的东西。 如下图所示的浏览枢纽是你的方式去你所有的资源。 你可以通过订阅筛选，选择调整列，并导航到管理叶片通过点击单个项目。

![浏览集线器][BrowseHub]

## 如何管理和委派对资源的访问

从这把剑你可以连接到虚拟机使用远程桌面，监控关键绩效指标、 控制对此 VM 使用基于角色的访问 (RBAC) 的访问、 配置 VM，和执行其他重要的管理任务。 委派基于角色的访问权限是在规模管理的关键。 单击 [在这里](role-based-access-control-configure.md) 要了解关于它的更多信息。若要委派对资源的访问，请执行以下操作:

1.	浏览到您的资源。
2.	单击设置中的要点部分。
3.	在设置列表中单击用户。
4.	在命令栏中单击添加。
5.	选择用户和角色。

![管理资源][ManageResource]

## 如何自定义资源叶片

Azure 预先配置的叶片为您的资源，但这些刀片上的瓷砖是你的控制。 你可以去到自定义模式，以添加、 删除、 调整大小，或重新安排的瓷砖。要自定义刀片式服务器，请执行以下操作:

1.	浏览到您的资源。
2.	单击...你想要自定义的叶片顶部。
3.	单击添加部件。
4.	开始拖动和下降部分。  

![自定义叶片][CustomizeBlades]

## 如何获取帮助

如果你有一个问题，我们在这里为你。 门户网站有一个帮助和支持的页面，可以点你在正确的方向前进。 取决于你 [支持计划](http://azure.microsoft.com/support/plans/)您还可以直接在门户中创建的支持票。 创建一个支持票之后, 您可以管理的生命周期中从门户内的票价。你可以通过导航到浏览-帮助 + 支持 > 到达帮助和支持页。  

![帮助和支持][HelpSupport]

## 摘要

让我们复习您在本教程中所学的内容:
- 您学习了如何注册、 征订单，并浏览到门户
- 你得到了面向与门户 UI 并学会了如何创建和浏览资源
- 您学习了如何创建资源并浏览资源
- 您了解了结构或管理刀片以及您如何可以一贯地管理不同类型的资源
- 您学习了如何自定义门户网站将信息你关心的前面和中心
- 你学会了如何控制对资源使用基于角色的访问 (RBAC) 的访问
- 您学习了如何获得帮助和支持

微软 Azure 预览门户大大简化了构建和管理您的应用程序在云中。 看一看 [管理博客](http://azure.microsoft.com/blog/topics/management/) 要保持与时俱进，随着我们不断 [倾听的反馈](http://feedback.azure.com/forums/223579-azure-preview-portal) 并做出改进。  [ScottGu 的博客](http://weblogs.asp.net/scottgu) 是另一个伟大的地方寻找蔚蓝的所有更新。

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
