<properties 
	pageTitle="Set up billing alerts for your Microsoft Azure subscriptions" 
	description="Describes how you can set up alerts on your Azure bill so you can avoid billing surprises." 
	services="" 
	documentationCenter="" 
	authors="vikdesai" 
	manager="msmbaldwin" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="vikdesai"/>

# 设置您的微软 Azure 订阅计费通知

你是否担心你多少花每个月为你蔚蓝的订阅?如果你是蔚蓝的订阅的帐户管理员，您可以使用 Azure 计费警报服务来创建自定义计费的警报，帮助您监视和管理计费活动你蔚蓝的帐户。

这项服务是一项预览服务，所以你要做的第一件事是注册为它-访问 <a href="https://account.windowsazure.com/PreviewFeatures">预览功能页 </a> 在 Azure 帐户管理门户中做到这一点。

## 设置警报的阈值和电子邮件收件人

你收到账单服务打开的电子邮件确认您的订阅后，请访问 <a href="https://account.windowsazure.com/Subscriptions">订阅页</a> 在帐户门户。单击您想要监视，然后单击的订阅 **警报**.

![][Image1]

接下来，单击 **添加警报** 若要创建您的第一个-您可以设置共五个计费通知每个订阅，与不同的阈值和每个警报的两个电子邮件收件人。

![][Image2]

当你添加一个警报时，你给它一个独特的名称，选择消费的门槛，并选择将发送的通知的电子邮件地址。当设置阈值，您可以选择 **帐单总额** 或 **货币信贷** 从 **警报** 列表。计费总订阅支出超过阈值时，会发出一个警报。货币信贷，当货币信贷下降低于上限的时候，会发出一个警报。货币信贷通常适用于免费试用和与 MSDN 帐户关联的订阅。

![][Image3]

Azure 支持任何电子邮件地址，但不会验证电子邮件地址的工作，所以仔细检查输入错误。

## 检查您的通知

设置警报后，帐户中心列出他们并显示多少你可以设置更多。对于每个警报，你看到的日期和发送，是否它是一个警报，对于计费总或货币信贷的时间以及您设置的限制。日期和时间格式是 24 小时通用协调时间 (UTC) 和日期是 yyyy mm dd 格式。单击加号为警报列表中要编辑它，或单击垃圾桶来删除它。

[Image1]: ./media/azure-billing-set-up-alerts/billingalert1.png
[Image2]: ./media/azure-billing-set-up-alerts/billingalert2.png
[Image3]: ./media/azure-billing-set-up-alerts/billingalerts3.png
