<properties
	pageTitle="Improve Business Continuity with Azure Regional Pairs"
	description="Use Regional pairs to keep applications resilient during data center failures."
	services="multiple"
	documentationCenter=""
	authors="rboucher"
	manager="jwhit"
	editor="tysonn"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/07/2015"
    ms.author="robb"/>

# 提高可用性使用 Azure 区域对

## 解释的蔚蓝配对的区域

Azure 经营在世界各地的多个地域。蔚蓝的地理学是世界包含至少一个 Azure 区域定义的区域。蔚蓝的区域是包含一个或多个数据中心的地理区域。

每个蔚蓝的区域在一起搭配另一个区域内 (不包括巴西南部，搭配，它的地理位置以外的地区)，同一地理区域配对。


![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

图 1 — — 天蓝色区域双图



|地理 | 配对地区 |                 |
|:-------------|:-------------   |:-------------   |
|北美 |美国中北部 |美国中南部 |
|北美 |东美国 |西美 |
|北美 |美国东部 2 |美国中央 |
|欧洲 |北欧洲 |西欧 |
|亚洲 |东南亚地区 |东亚地区 |
|中国 |华东 |华北地区 |
|日本 |日本东 |西日本 |
|巴西 |巴西南 (1) |美国中南部 |
|澳大利亚 |澳大利亚东部 |澳大利亚 Southeast|
|美国政府 |美国 Gov 爱荷华州 |美国 Gov 弗吉尼亚州 |

表 1-映射的蔚蓝区域对

> (1) 巴西南非是独特的因为它搭配，在它自己的地理区域。请注意，巴西南非二级区域是美国中南部但美国中南部的次区域不是巴西南部。

我们建议你要从 Azure 的隔离和可用性政策中受益的区域对跨复制工作负载。例如，按顺序部署计划的 Azure 系统更新 (不在同一时间) 跨配对区域。这意味着，即使是在一个错误的更新罕见的事件，这两个区域将不受同时。此外，在广泛的停机，万一，每对至少一个区域复苏是优先考虑。

## 区域对示例
下面的图 2 显示一个假想的应用程序的区域对用于灾难恢复。绿色的数字突出显示三个 Azure 服务 (Azure 计算、 存储和数据库) 和如何将其配置为复制跨区域跨区域的活动。用橙色数字突出显示部署跨配对区域的独特优势。


![成对的区域优势概述](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

图 2 — — 假设蔚蓝区域双

## 跨区域活动
如图 2 所述。

![1Green](./media/best-practices-availability-paired-regions/1Green.png) **Azure 计算 (PaaS)** — — 你必须提供额外的计算资源提前以确保资源可用在另一个区域在一场灾难。有关更多信息，请参见 [蔚蓝的业务连续性技术指导](https://msdn.microsoft.com/library/azure/hh873027.aspx)

![2Green](./media/best-practices-availability-paired-regions/2Green.png) **Azure 存储空间** -默认情况下，创建一个 Azure 存储帐户时，将配置地理冗余存储 (GRS)。与地球观测卫星，您的数据自动复制三次主要区域内，和三次配对的地区。有关更多信息，请参见 [Azure 存储冗余选项](../storage/storage-redundancy.md).


![3Green](./media/best-practices-availability-paired-regions/3Green.png) **SQL azure 数据库** — — 与蔚蓝 SQL 标准 Geo-复制，您可以配置到配对的地区事务异步复制。与保费 Geo-复制，您可以配置复制到任何区域在世界;然而，我们建议您部署这些地区资源的配对为大多数灾难恢复方案。有关更多信息，请参见  [土力工程处复制 SQL Azure 数据库中](https://msdn.microsoft.com/library/azure/dn783447.aspx)

![4Green](./media/best-practices-availability-paired-regions/4Green.png) **蔚蓝的资源管理器 (ARM)** 臂本质提供了跨区域的服务管理组件的逻辑隔离。这意味着在一个区域中的逻辑故障不太可能影响另一个。

## 成对的区域的好处
如图 2 所述。  

![5Orange](./media/best-practices-availability-paired-regions/5Orange.png)
**物理隔离** — — 如果可能的话，Azure 更喜欢至少 300 公里的区域一在数据中心之间的分离，虽然这不是实际或可能在所有地理区域。物理数据中心分离可以减少自然灾害、 内乱、 电力中断或一次影响这两个区域的物理网络中断的可能性。隔离是约束内地理 (地理大小、 电源/网络基础架构的可用性、 法规等)。  

![6Orange](./media/best-practices-availability-paired-regions/6Orange.png)
**平台提供的复制** -某些服务，如地理冗余存储提供自动复制到该配对的区域。

![7Orange](./media/best-practices-availability-paired-regions/7Orange.png)
**地区恢复秩序** — — 的情况下广泛的中断，恢复一个区域是每对优先考虑。跨配对区域部署的应用程序保证有恢复具有优先级的地区之一。如果没有配对的地区部署应用程序，可能延迟复苏 — — 区域选择可能是最后的两个要恢复的最糟糕的情况。

![8Orange](./media/best-practices-availability-paired-regions/8Orange.png)
**连续更新** — — 计划 Azure 系统更新的推出到配对区域按顺序 (不在同一时间) 尽量减少停机时间、 bug 和中罕见的坏的更新逻辑故障的影响。


![9Orange](./media/best-practices-availability-paired-regions/9Orange.png)
**数据居住** — — 区域驻留在同一地理位置作为其双 (不包括巴西南) 以满足对税收和法律执法管辖权目的数据居住要求。
