﻿<properties 
	pageTitle="Walkthrough: export telemetry to SQL Database from Application Insights" 
	description="Code your own analysis of telemetry in Application Insights by using the continuous export feature." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/13/2015" 
	ms.author="awills"/>
 
# Walkthrough: Export to SQL from Application Insights using Stream Analytics

This article shows how to move your telemetry data from [Visual Studio Application Insights][start] into an Azure SQL database by using [Continuous Export][export] and [Azure Stream Analytics](http://azure.microsoft.com/services/stream-analytics/). 

Continuous export moves your telemetry data into Azure Storage in JSON format. We'll parse the JSON objects using Azure Stream Analytics and create rows in a database table.

(More generally, Continuous Export is the way to do your own analysis of the telemetry your apps send to Application Insights. You could adapt this code sample to do other things with the exported telemetry, such as aggregation of data and publishing the data to the PowerBI visualization suite.)

We'll start with the assumption that you already have the app you want to monitor.


In this example, we will be using the page view data, but the same pattern can easily be extended to other data types such as custom events and exceptions. 


## Add Application Insights SDK

To monitor your application, you [add an Application Insights SDK][start] to your application. There are different SDKs and helper tools for different platforms, IDEs and languages. You can monitor web pages, Java or ASP.NET web servers, and mobile devices of several kinds. All the SDKs send telemetry to the [Application Insights portal][portal], where you can use our powerful analysis and diagnostic tools, and export the data to storage.

To get started:

1. Get an [account in Microsoft Azure](http://azure.microsoft.com/pricing/).
2. In the [Azure portal][portal], add a new Application Insights resource for your app:

    ![Choose New, Developer Services, Application Insights, and choose the type of application](./media/app-insights-code-sample-export-sql-stream-analytics/010-new-asp.png)


    (Your app type and subscription might be different.)
3. Open Quick Start to find how to set up the SDK for your app type.

    ![Choose Quick Start and follow instructions](./media/app-insights-code-sample-export-sql-stream-analytics/020-quick.png)

    If your app type isn't listed, take a look at the [Getting Started][start] page.

4. In this example, we're monitoring a web app, so we can use the Azure tools in Visual Studio to install the SDK. Tell it the name of your Application Insights resource:

    ![In Visual Studio Solution Explorer, right-click your project and choose Add Application Insights. At Send telemetry to, choose to create a new resource, or use an existing one.](./media/app-insights-code-sample-export-sql-stream-analytics/appinsights-d012-addbrown.png)

5. Publish your app, and watch telemetry data appearing in your Application Insights resource.


## Create storage in Azure

Continuous export always outputs data to an Azure Storage account, so you need to create the storage first.

1. Create a storage account in your subscription in the [Azure portal][portal].

    ![In Azure portal, choose New, Data, Storage](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)

2. Create a container

    ![In the new storage, select Containers and then Add](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)

3. Copy the storage access key

    You'll need it soon to set up the input to the stream analytics service.

    ![In the storage, open Settings, Keys, and take a copy of the Primary Access Key](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## Start continuous export to Azure storage

1. In the Azure portal, browse to the Application Insights resource you created for your application.

    ![Choose Browse, Application Insights, your application](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)

2. Create a continuous export.

    ![Choose Settings, Continuous Export, Add](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)


    Select the storage account you created earlier:

    ![Set the export destination](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)
    
    Set the event types you want to see:

    ![Choose event types](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)

Now sit back and let people use your application for a while. Telemetry will come in and you'll see statistical charts in [metric explorer][metrics] and individual events in [diagnostic search][diagnostic]. 

And also, the data will export to your storage, where you can inspect the content. For example, there's a storage browser in Visual Studio:


![In Visual Studio, open Server Browser, Azure, Storage](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)

The events are written to blob files in JSON format. Each file may contain one or more events. So we'd like to read the event data and filter out the fields we want. There are all kinds of things we could do with the data, but our plan today is to use Stream Analytics to move the data to a SQL database. That will make it easy to run lots of interesting queries.

## Create an Azure SQL Database

Once again starting from your subscription in [Azure portal][portal], create the database (and a new server, unless you've already got one) to which you'll write the data.

![New, Data, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)


Make sure that the database server allows access to Azure services:


![Browse, Servers, your server, Settings, Firewall, Allow Access to Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## Create a table in Azure SQL DB

Connect to the database created in the previous section with your preferred management tool. In this walkthrough, we will be using [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Create a new query, and execute the following T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
	[pageName] [nvarchar](max) NOT NULL,
	[viewCount] [int] NOT NULL,
	[url] [nvarchar](max) NULL,
	[urlDataPort] [int] NULL,
	[urlDataprotocol] [nvarchar](50) NULL,
	[urlDataHost] [nvarchar](50) NULL,
	[urlDataBase] [nvarchar](50) NULL,
	[urlDataHashTag] [nvarchar](max) NULL,
	[eventTime] [datetime] NOT NULL,
	[isSynthetic] [nvarchar](50) NULL,
	[deviceId] [nvarchar](50) NULL,
	[deviceType] [nvarchar](50) NULL,
	[os] [nvarchar](50) NULL,
	[osVersion] [nvarchar](50) NULL,
	[locale] [nvarchar](50) NULL,
	[userAgent] [nvarchar](max) NULL,
	[browser] [nvarchar](50) NULL,
	[browserVersion] [nvarchar](50) NULL,
	[screenResolution] [nvarchar](50) NULL,
	[sessionId] [nvarchar](max) NULL,
	[sessionIsFirst] [nvarchar](50) NULL,
	[clientIp] [nvarchar](50) NULL,
	[continent] [nvarchar](50) NULL,
	[country] [nvarchar](50) NULL,
	[province] [nvarchar](50) NULL,
	[city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
	[eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

## Create an Azure Stream Analytics instance

From the [Classic Azure Portal](https://manage.windowsazure.com/), select the Azure Stream Analytics service, and create a new Stream Analytics job:


![](./media/app-insights-code-sample-export-sql-stream-analytics/37-create-stream-analytics.png)



![](./media/app-insights-code-sample-export-sql-stream-analytics/38-create-stream-analytics-form.png)

When the new job is created, expand its details:

![](./media/app-insights-code-sample-export-sql-stream-analytics/41-sa-job.png)

#### Set blob location

Set it to take input from your Continuous Export blob:

![](./media/app-insights-code-sample-export-sql-stream-analytics/42-sa-wizard1.png)

Now you'll need the Primary Access Key from your Storage Account, which you noted earlier. Set this as the Storage Account Key.

![](./media/app-insights-code-sample-export-sql-stream-analytics/46-sa-wizard2.png)

#### Set path prefix pattern 

![](./media/app-insights-code-sample-export-sql-stream-analytics/47-sa-wizard3.png)

Be sure to set the Date Format to YYYY-MM-DD (with dashes).

The Path Prefix Pattern specifies how Stream Analytics finds the input files in the storage. You need to set it up to correspond to how Continuous Export stores the data. Set it like this:

    webapplication27_100000000-0000-0000-0000-000000000000/PageViews/{date}/{time}

In this example:

* `webapplication27` is the name of the Application Insights resource. 
* `1000...` is the instrumentation key of the Application Insights resource. 
* `PageViews` is the type of data we want to analyze. The available types depend on the filter you set in Continuous Export. Examine the exported data to see the other available types.
* `/{date}/{time}` is a pattern written literally.

To get the name and iKey of your Application Insights resource, open Essentials on its overview page, or open Settings.

#### Finish initial setup

Confirm the serialization format:

![Confirm and close wizard](./media/app-insights-code-sample-export-sql-stream-analytics/48-sa-wizard4.png)

Close the wizard and wait for the setup to complete.

## Set query

Open the query section:

![In stream analytics, select Query](./media/app-insights-code-sample-export-sql-stream-analytics/51-query.png)

Replace the default query with:

```SQL

    SELECT flat.ArrayValue.name as pageName
	, flat.ArrayValue.count as viewCount
	, flat.ArrayValue.url as url
	, flat.ArrayValue.urlData.port as urlDataPort
	, flat.ArrayValue.urlData.protocol as urlDataprotocol
	, flat.ArrayValue.urlData.host as urlDataHost
	, flat.ArrayValue.urlData.base as urlDataBase
	, flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOuput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Notice that the first few properties are specific to page view data. Exports of other telemetry types will have different properties.

## Set up output to database

Select SQL as the output.

![In stream analytics, select Outputs](./media/app-insights-code-sample-export-sql-stream-analytics/53-store.png)

Specify the SQL database.


![Fill in the details of your database](./media/app-insights-code-sample-export-sql-stream-analytics/55-output.png)

Close the wizard and wait for a notification that the output has been set up.

## Start processing

Start the job from the action bar:

![In stream analytics, click  Start](./media/app-insights-code-sample-export-sql-stream-analytics/61-start.png)

You can choose whether to start processing the data starting from now, or to start with earlier data. The latter is useful if you have had Continuous Export already running for a while.


![In stream analytics, click  Start](./media/app-insights-code-sample-export-sql-stream-analytics/63-start.png)

After a few minutes, go back to SQL Server Management Tools and watch the data flowing in. For example, use a query like this:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## Related articles

* [Export to SQL using a worker role](app-insights-code-sample-export-telemetry-sql-database.md)
* [Continuous Export in Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-get-started.md

 