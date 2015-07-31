<properties
   pageTitle="Content Delivery Network (CDN) guidance | Microsoft Azure"
   description="Guidance on Content Delivery Network (CDN) to deliver high bandwidth content hosted in Azure."
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

# 内容交付网络 (CDN) 指导

![](media/best-practices-cdn/pnp-logo.png)

## 概述

微软 Azure 内容交付网络 (CDN) 为开发人员提供了用于传送承载在 Azure 中的高带宽内容的全球解决方案。CDN 缓存公开可用的对象，从蔚蓝的 blob 存储加载或在应用程序文件夹策略性地点提供最大带宽为交付内容给用户。它通常用于提供静态内容，如图像、 样式表、 文档、 文件、 客户端脚本和 HTML 页面。

使用 CDN 的主要优点是处理的更低的延迟和内容更快交付给用户，无论他们在何地承载了应用程序，应用程序本身的负荷减少因为它可以缓解需要访问和提供内容的数据中心的地理位置。这种减少负载可以帮助提高性能和可扩展性的应用程序，以及通过减少实现特定级别的性能和可用性所需的处理资源托管成本最小化。

您可能能够使用其他内容交付网络实现的系统是不由 Azure 在您的应用程序如果 Azure CDN 不能满足您的需求。或者，您可能能够使用 Azure CDN 承载与其他供应商的暴露在 Azure 存储中或在 Azure 计算实例的静态内容应用程序。  
![](media/best-practices-cdn/CDN.png)

## 如何以及为什么使用 CDN

CDN 的典型用途包括:  

- 提供客户端应用程序，往往是从一个网站的静态资源。这些可以是图像、 样式表、 文档、 文件、 客户端脚本、 HTML 页、 HTML 片段或服务器不需要修改为每个请求的任何其他内容。应用程序可以在运行时创建项目并向他们提供 CDN (例如，通过创建当前新闻标题的列表)，但它没有为每个请求这样做。

- 向设备如移动电话和平板电脑应用程序本身在哪里提供 web 服务的 API 向客户端提供公共的静态和共享内容。除了其他服务器不需要修改为每个请求的内容，CDN 可以提供静态数据集的客户端使用-也许要生成客户端 UI。例如，它可以用于传递 JSON 或 XML 文档。

- 服务整个网站包含唯一的公共静态内容给客户端，而无需任何专用计算资源。

- 流媒体视频文件到客户端上的需求。视频得益于低延迟和可靠的连接，从遍布全球的数据中心，提供 CDN 连接可用。

- 一般提高用户，特别是那些位于应用程序的数据中心位置，否则将遭受更高延迟的体验。大部分的 web 应用程序中的内容的总大小通常是静态的并使用 CDN 可以有助于保持性能和整体的用户体验，同时消除应用程序部署到多个数据中心的要求。

- 应对应用程序的日益负载是物联网 (物联网) 的一部分，服务移动和固定设备。大量的这类设备和电器能够轻而易举地战胜应用程序是否需要处理广播的消息和直接管理固件更新分发。

- 应对高峰飙升需求而无需应用程序进行扩展，避免随之增加，运行成本。例如，当更新发布到操作系统，硬件设备如特定型号的路由器，或消费者设备如智能电视，会有巨大的峰值需求，它由数以百万计的用户和设备下载在较短时间。  

- 下表显示的中位数时间的例子到第一个字节，从不同的地理位置。目标 web 角色部署到 Azure 在美国西部。有很强的相关性，进一步提高由于 CDN 和 CDN 节点的接近度之间。Azure CDN 节点位置的列表是可用在 [蔚蓝的内容传送网络 (CDN) 节点位置](http://msdn.microsoft.com/library/azure/gg680302.aspx).  

<table xmlns:xlink="http://www.w3.org/1999/xlink"><tr><th><a name="_MailEndCompose" href="#"><span /></a><br /></th><th><p>Time to First Byte (Origin)</p></th><th><p>Time to First Byte (CDN)</p></th><th><p>% faster for CDN</p></th></tr><tr><td><p>* San Jose, CA</p></td><td><p>47.5</p></td><td><p>46.5</p></td><td><p>2 %</p></td></tr><tr><td><p>** Dulles, VA</p></td><td><p>109</p></td><td><p>40.5</p></td><td><p>169 %</p></td></tr><tr><td><p>Buenos Aires, AR</p></td><td><p>210</p></td><td><p>151</p></td><td><p>39 %</p></td></tr><tr><td><p>* London, UK</p></td><td><p>195</p></td><td><p>44</p></td><td><p>343 %</p></td></tr><tr><td><p>Shanghai, CN</p></td><td><p>242</p></td><td><p>206</p></td><td><p>17 %</p></td></tr><tr><td><p>* Singapore</p></td><td><p>214</p></td><td><p>74</p></td><td><p>189%</p></td></tr><tr><td><p>* Tokyo, JP</p></td><td><p>163</p></td><td><p>48</p></td><td><p>240 %</p></td></tr><tr><td><p>Seoul, KR</p></td><td><p>190</p></td><td><p>190</p></td><td><p>0 %</p></td></tr></table>* Has an Azure CDN node in the same city.  
* Has an Azure CDN node in a neighboring city.  


## 挑战  

还有几个挑战在计划使用 CDN 时加以考虑:  

- **部署** 您必须决定在何处加载内容为 CDN 来源 (CDN 将从中获取内容)，以及是否需要部署多个存储系统中的内容 (如基于 CDN 和在另一个位置)。

- 您的应用程序部署机制必须考虑到帐户部署静态内容和资源，以及部署应用程序文件如 ASPX 页面。例如，它可能需要一个单独的步骤，以将内容加载到 Azure blob 存储。

- **版本控制和缓存控制** 你必须考虑如何将更新静态内容和部署新版本。CDN 当前尚未提供冲厕内容，因此，新版本可供选择的机制。这是一个类似挑战管理客户端缓存，例如 web 浏览器。

- **测试** 它可以很难执行本地测试您的 CDN 设置当开发和测试应用程序，本地或分期。

- **SEO** 当你使用他们 CDN，会有此内容对 SEO 的影响从不同的域服务内容，如图像和文档。

- **安全** 许多的 CDN 服务，例如 Azure CDN 目前不提供任何类型的内容的访问控制。

- **可恢复性** CDN 是潜在的单点故障的应用程序。它具有更低的可用性 SLA 比 blob 存储 (这可以用来直接交付内容) 所以你可能需要考虑执行的关键内容的回退机制。

- 客户端可能会从一种环境，不允许对资源的访问基于 CDN 连接。这可能是一个安全约束的环境，限制访问只有一组已知的来源，或防止从页面来源以外的任何资源的加载。因此，回退的实现将需要。

- 你应该实现监控您通过 CDN 的内容可用性的机制。

场景，CDN 可能用处不包括:  

- 当内容具有低的命中率，所以可能访问的几次，或只是一次，期间的生存时间的有效期。第一次下载的项目你招来两个交易收费 (从原点到 CDN，然后从 CDN 给客户)。

- 当数据是私有的如为大型企业或供应链生态系统。


## 一般准则和良好做法

使用 CDN 是好的方法，以尽量减少对您的应用程序，负载和最大化可用性和性能。你应该考虑这的所有适当的内容和你的应用程序使用的资源。设计您使用 CDN 的策略时，请考虑以下几点:  

- **起源 ** 部署通过 CDN 的内容简单地要求您指定 HTTP (端口 80) 端点 CDN 服务将用来访问和缓存的内容。+ 终结点可以指定蔚蓝 blob 存储容器包含您想要通过 CDN 提供的静态内容。容器必须标记为公共。仅有公共的读访问权限的 blob 公共容器中将通过 CDN 提供。

- 终结点可以指定一个名为文件夹 **cdn** 在一个应用程序的根计算层 (如 web 角色或虚拟机)。从对资源，包括如 ASPX 页面的动态资源的请求结果将被缓存在 CDN。最小的可缓存性周期为 300 秒。任何较短期间将阻止内容部署到 CDN (请参阅"<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">高速缓存控制</a>"为更多的信息)。

- 如果你使用 Azure 的 Web 站点，该终结点通过选择站点创建 CDN 实例时设置为的站点的根文件夹。所有站点的内容将通过 CDN 提供。

- 在大多数情况下，指向您在文件夹在您的应用程序的计算层之一的 CDN 端点将提供更多的灵活性和控制。例如，它使它易于管理当前和未来的路由需求和动态生成静态的内容，如图像的缩略图。

- 可以使用查询字符串来区分缓存中的对象，当内容交付等 ASPX 页面动态来源。然而，当你指定的 CDN 终结点可以由在管理门户中设置来禁用此行为。当从 blob 存储提供内容，查询字符串被视为字符串所以有相同的名称但不同的查询字符串的两个项目将存储作为单独的项目，对 CDN。

- 你可以利用的资源，如脚本和其他内容，以避免将您的文件移动到 CDN 起源文件夹 URL 重写。

- 使用 Azure 存储 blob 的 CDN 举行内容，blob 中的资源的 URL 时，容器和 blob 名称区分大小写。

- 使用 Azure 的 Web 站点，可以在链接到资源中指定了 CDN 实例的路径。例如，以下指定的图像文件 **图像** 将通过 CDN 提供的站点文件夹:

  ```
  <img src="http://[your-cdn-instance].vo.msecnd.net/Images/image.jpg" />
  ```

- **部署** 静态内容可能需要配置和独立部署从应用程序如果不包括它在应用程序部署包或过程。考虑这将如何影响版本控制用于管理应用程序组件和静态资源内容的方法。

- 考虑如何将处理捆绑 (将几个文件合并为一个文件) 和缩小 (删除不必要的字符，如空格、 新行字符，注释和其他字符) 的脚本和 CSS 文件。这些通常采用的技术可以减少加载时间对于客户端，并且与交付内容通过 CDN 兼容。有关更多信息，请参见 [捆绑和缩小](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification).

- 如果您需要将内容部署到其他位置，这将是一个额外的步骤，在部署过程中。如果应用程序更新为 CDN 的内容，也许在固定的时间间隔或在响应事件，它必须存储更新的内容在任何其他地点，以及该终结点的 CDN。

- 您不能设置为应用程序部署在 Azure 分期，或在 Visual Studio 中的本地蔚蓝模拟器的 CDN 端点。这会影响单元测试、 功能测试和最终的部署前测试。你必须允许为此通过实施一种替代机制。例如，你可以预先部署到使用临时的自定义应用程序或实用工具，CDN 内容和执行测试期间它缓存。或者，使用编译指令或全局常量来控制在应用程序加载的资源。例如，当在调试模式下运行它可以加载资源，如客户端脚本包和其他内容从本地文件夹，并使用 CDN 在发布模式下运行时。

- CDN 不支持任何本机压缩功能。然而，它将提供内容已压缩，如 zip 和 gzip 文件。当作为 CDN 终结点使用的应用程序文件夹，服务器可能压缩一些内容默认方式相同时它将直接输送给 web 浏览器或其他类型的客户端。这依赖于对 **接受编码** 从客户端发送的值。在 Azure 中的默认设置是自动压缩内容，当 CPU 利用率低于 50%。更改的设置，可能需要启动任务，如果你使用云服务来承载该应用程序打开压缩的动态输出在 IIS 中使用。请参见 [启用 gzip 压缩与 Azure CDN 通过 Web 角色](http://blogs.msdn.com/b/avkashchauhan/archive/2012/03/05/enableing-gzip-compression-with-windows-azure-cdn-through-web-role.aspx) 更多的信息。

- **路由和版本控制** 您可能需要使用不同的 CDN 实例;例如，当您部署新版本的应用程序，您可能想要使用一个不同的 CDN。如果你使用 Azure blob 存储作为内容的来源，你可以简单地创建一个单独的存储帐户或一个单独的容器和 CDN 终结点指向它。如果您使用 **cdn** 在应用中你可以使用 URL 重写技术直接的 CDN 终结点的根文件夹请到不同的文件夹。

- 不要使用查询字符串来表示不同版本的应用程序中链接到 CDN 上的资源，因为，从蔚蓝的 blob 存储内容时，查询字符串是资源名称 (blob) 的一部分。它也可以影响如何客户端缓存的资源。

- 如果以前的资源都被缓存在 CDN 部署新版本的静态内容，当你更新的应用程序可以是一个挑战。有关详细信息，请参阅"<a href="#cachecontrol" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">高速缓存控制</a>".

<a name="cachecontrol" href="#" xmlns:xlink="http://www.w3.org/1999/xlink"><span /></a>**高速缓存控制**+ 考虑要如何管理缓存，和在什么层面上的应用程序。例如，使用一个文件夹作为 CDN 起源时你可以指定在特定的文件夹中生成的内容和所有的资源的内容到期的页的可缓存性。CDN，并使用标准的 HTTP 标头的客户机，您还可以指定缓存属性。虽然你已经应该管理服务器和客户端上缓存，将有助于使你更加意识到如何您的内容进行缓存，并在使用 CDN。

- 若要防止对象被可用基于 CDN 可以删除它们从原点 (blob 容器或应用程序 **cdn** 根文件夹)，移除或删除 CDN 端点，或者 — — 在 blob 存储的情况下 — — 使容器或 blob 私人。然而，项目将删除从 CDN 只当他们生存时间 (TTL) 过期。

- 如果没有缓存的到期时间指定 (例如，当从 blob 存储加载内容)，它将被缓存在 CDN 长达 72 小时。

- 在 web 应用程序中，您可以设置缓存和到期时间，所有的内容通过使用 **clientCache** 中的元素 **system.webServer/staticContent** web.config 文件的节。你可以将 web.config 文件放在任何文件夹中，以便它影响到该文件夹中的文件和所有子文件夹中的文件。

- 如果你使用 ASP.NET 动态技术为 CDN 创建内容，请确保您指定 **Cache.SetExpires** 每一页上的属性。CDN 将不缓存使用公共的默认缓存设置的页面的输出。 设置为合适的值，以确保内容不被丢弃，并不会从应用程序在很短的时间间隔重新加载缓存的到期时间。  

- **安全** CDN 可以提供内容通过 HTTPS (SSL) 使用证书提供的 CDN，但它也可通过 HTTP 以及。你不能阻止 HTTP 访问 cdn 网络中的项目。您可能需要使用 HTTPS 请求通过 HTTPS 加载 (如购物车) 来避免浏览器警告关于混合内容的页中显示的静态内容。

- 许多的 CDN 服务，例如 Azure CDN，目前不提供任何设施的访问控制，以确保对内容的访问。您不能使用 CDN 共享访问签名 (SAS)。

- 如果您交付使用 CDN 的客户端脚本，你可能会遇到问题，如果这些脚本使用 **客户端代码** 打电话去 HTTP 请求的其他资源，如数据、 图像或字体在不同的域中。许多 web 浏览器防止跨源资源共享 (CORS)，除非 web 服务器配置为设置适当的响应标头。有关 CORS 的详细信息，请参阅"威胁缓解"在指南 》 <span class="highlight" xmlns:dt="uuid:C2F41010-65B3-11d1-A29F-00AA00C14882" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:MSHelp="http://msdn.microsoft.com/mshelp">API 的安全注意事项</span>.您可以配置 CDN 支持 CORS: + 如果从中你现在的发言内容的起源是蔚蓝 blob 存储，您可以添加 **CorsRule** 服务属性。规则可以指定允许的起源 CORS 请求、 获取，如允许的方法和最大年龄为以秒规则 (期内，客户端必须在加载原始内容之后请求链接的资源)。有关更多信息，请参见 [跨源资源共享 Azure 存储服务 (CORS) 支持](http://msdn.microsoft.com/library/azure/dn535601.aspx).

- 如果从你现在的发言内容的起源是一个文件夹在应用程序中，如 **cdn** 根文件夹，您可以在应用程序配置文件以设置配置出站规则 **访问控制允许起源** 对所有响应标头。有关使用重写规则的详细信息，请参阅 [URL 重写模块](http://www.iis.net/learn/extensions/url-rewrite-module).请注意，这种技术是不可能的当使用 Azure 的 Web 站点。

- **自定义的域**+ 大多数 Cdn，包括 Azure CDN，允许你指定一个自定义的域的名称，并使用它来访问资源通过 CDN。您还可以设置自定义的子域名称使用 **CNAME** 在您的 DNS 记录。使用这种方法可以提供额外的抽象和控制层。

- 如果您使用 **CNAME**你不能 (在撰写本指南时) 也使用了 SSL，因为 CDN 使用自己单一的 SSL 证书，并且这将与您自定义的域子域名称不匹配。

- **CDN 回退** 你应考虑您的应用程序将如何应付失败或暂时不可用的 CDN。客户端应用程序可以使用的资源已在以前的请求，本地 (客户端上) 缓存的副本，或者他们可以使用代码检测故障，而是请求资源来源 (应用程序文件夹或包含资源的蔚蓝 blob 容器) 如果 CDN 不可用。

- **SEO** 如果 SEO 是一个重要的考虑，在您的应用程序，请确保你: + 包括 **Rel** 在每个页面或资源的规范标题。

- 使用 **CNAME** 子域记录和访问使用该名称的资源。

- 考虑到影响 CDN 的 IP 地址可能是一个国家或地区不同于应用程序本身的事实。

- 当使用 Azure blob 存储作为原点，保持相同的文件结构上在应用程序文件夹 CDN 资源。


- **监测 & 日志记录** 作为监控策略来检测和测量失败的应用程序的一部分包括 CDN 或扩展延迟发生。

- 对 CDN 启用日志记录并将它作为您的日常操作的一部分。

- **成本的含义** CDN 加载数据从您的应用程序时，你被指控为这两个从 CDN 的出站数据传输和存储交易记录。你应该设置现实缓存到期期间为内容，保证新鲜，但不是引起反复重装来自应用程序的内容太短或 blob 存储到 CDN。但是，很长的过期时间使更难以从 CDN 中删除项目，因为你必须等待他们到期。

- 很少下载的项目会招致两的交易费用，无需提供任何显著减少服务器的负载。  



## 示例代码
本节包含一些代码和使用 CDN 技术的例子。  


## URL 重写
从一个托管的云服务应用程序的根目录中的 Web.config 文件除了以下演示如何执行 URL 重写，当使用 CDN。从它将缓存的内容 CDN 的请求重定向到特定文件夹内基于类型的资源 (如脚本和图像) 的应用程序根。  

```XML
<system.webServer>
  ...
  <rewrite>
    <rules>
      <rule name="VersionedResource" stopProcessing="false">
        <match url="(.*)_v(.*)\.(.*)" ignoreCase="true" />
        <action type="Rewrite" url="{R:1}.{R:3}" appendQueryString="true" />
      </rule>
      <rule name="CdnImages" stopProcessing="true">
        <match url="cdn/Images/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Images/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnContent" stopProcessing="true">
        <match url="cdn/Content/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Content/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScript" stopProcessing="true">
        <match url="cdn/Scripts/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/Scripts/{R:1}" appendQueryString="true" />
      </rule>
      <rule name="CdnScriptBundles" stopProcessing="true">
        <match url="cdn/bundles/(.*)" ignoreCase="true" />
        <action type="Rewrite" url="/bundles/{R:1}" appendQueryString="true" />
      </rule>
    </rules>
  </rewrite>
  ...
</system.webServer>
```

重写规则添加执行以下重定向:  

- 第一条规则允许您版本嵌入的资源，然后将忽略文件的文件名中。例如， **文件名_v123.jpg **被重写为 **Filename.jpg**.

- 接下来的四规则显示如何将请求重定向，如果你不想要在一个名为文件夹中存储的资源 **cdn** 在 web 角色的根。规则映射 **cdn/图像**, **cdn/内容**, **cdn/脚本**和 **cdn/束** 向 web 角色在他们各自的根文件夹的 Url。
使用 URL 重写，需要对各种资源的整合进行一些更改。  


## 捆绑和缩小 ##

捆绑和缩小可以由 ASP.NET 处理。在 MVC 项目中，您定义您的包中 **BundleConfig.cs**.通过调用创建一个引用到缩小的脚本包 **Script.Render** 方法，通常在视图类中的代码。本参考包含一个查询字符串，包括哈希，基于包的内容。如果包内容发生更改，也将更改生成的哈希。  
默认情况下，Azure CDN 的实例都可以 **查询字符串状态** 设置已禁用。在为更新的脚本包通过 CDN 妥善处理订单，您必须启用 **查询字符串状态** CDN 实例的设置。注意，它可能是一个小时或更多 CDN 在创建之前，更改的设置生效。  


## 更多的信息
+ [蔚蓝的 CDN](http://azure.microsoft.com/services/cdn/)
+ [蔚蓝的内容交付网络 (CDN) 的概述](http://msdn.microsoft.com/library/azure/ff919703.aspx)
+ [从 Web 应用程序中的蔚蓝 CDN 服务内容](cdn-serve-content-from-cdn-in-your-web-application.md)
+ [云服务结合 Azure CDN](cdn-cloud-service-with-cdn.md)
+ [蔚蓝的内容交付网络的最佳做法](http://azure.microsoft.com/blog/2011/03/18/best-practices-for-the-windows-azure-content-delivery-network/)
