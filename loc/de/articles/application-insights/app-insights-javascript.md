﻿<properties 
	pageTitle="Application Insights for JavaScript web apps" 
	description="Get page view and session counts, web client data, and track usage patterns. Detect exceptions and performance issues in JavaScript web pages." 
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
	ms.date="07/10/2015" 
	ms.author="awills"/>
 
# Application Insights for JavaScript web apps

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]

Find out about the performance and usage of your web page. Add Visual Studio Application Insights to your page, and you'll find out how many users you have, how often they come back, and which pages they use most. You'll also get reports of load times and any exceptions. Add a few [custom events and metrics][track], and you can analyse in detail the most popular features, the most common mistakes, and tune your page to success with your users.

![Choose New, Developer Services, Application Insights.](./media/app-insights-javascript/16-page-views.png)    

If you already set up server telemetry for your [ASP.NET][greenbrown] or [Java][java] web app, you'll get the picture from both client and server angles. The two streams will be integrated in the Application Insights portal.

## Create an Application Insights resource

The Application Insights resource is where data about your page's performance and usage is displayed. (If you already created a resource, maybe to collect data from your web server, skip this step.)

In the [Azure portal](http://portal.azure.com), create a new Application Insights resource:

![Choose New, Developer Services, Application Insights.](./media/app-insights-javascript/01-create.png)    

*Questions already?* [More about creating a resource][new].


## Add the SDK script to your app or web pages

In Quick Start, get the script for web pages:

![On your app overview blade, choose Quick Start, Get code to monitor my web pages. Copy the script.](./media/app-insights-javascript/02-monitor-web-page.png)

Insert the script just before the &lt;/head&gt; tag of every page you want to track. If your website has a master page, you can put the script there. For example:

* In an ASP.NET MVC project, you'd put it in View\Shared\\_Layout.cshtml
* In a SharePoint site, on the control panel, open [Site Settings / Master Page](app-insights-sharepoint.md).

The script contains the instrumentation key that directs the data to your Application Insights resource.

*(If you're using a well-known web page framework, look around for Application Insights adaptors. For example, there's [an AngularJS module](http://ngmodules.org/modules/angular-appinsights).)*

 
## <a name="run"></a>Run your app

Run your web app, use it a while to generate telemetry, and wait a few seconds. You can either run it with F5 on your development machine, or publish it and let users play with it.

If you want to check the telemetry that a web app is sending to Application Insights, use your browser's debugging tools (F12 on many browsers). Data is sent to dc.services.visualstudio.com.

## Explore your data

In the application overview blade, there's a chart near the top that shows average time to load pages into the browser:


![](./media/app-insights-javascript/05-browser-page-load.png)


*No data yet? Click **Refresh** at the top of the page. Still nothing? See [Troubleshooting][qna].*

Click on that chart, and you get a more detailed version:

![](./media/app-insights-javascript/07-client-perf.png)

This is a stacked chart which breaks the total page load time into the [standard timings defined by W3C](http://www.w3.org/TR/navigation-timing/#processing-model). 

![](./media/app-insights-javascript/08-client-split.png)

Note that the *network connect* time is usually lower than you might expect, because it's an average over all requests from the browser to the server. Many individual requests have a connect time of 0 because there is already an active connection to the server.


### Performance by page

Further down in the details blade, there's a grid segmented by page URL:


![](./media/app-insights-javascript/09-page-perf.png)

If you'd like to see the performance of the pages over time, double-click the grid and change its chart type:

![](./media/app-insights-javascript/10-page-perf-area.png)

## Client usage overview

Back on the overview blade, click Usage:

![](./media/app-insights-javascript/14-usage.png)

* **Users:** The count of distinct users over the time range of the chart. (Cookies are used to identify returning users.)
* **Sessions:** A session is counted when a user has not made any requests for 30 minutes.
* **Page views** Counts the number of calls to trackPageView(), typically called once in each web page.


### Click through to more detail

Click any of the charts to see more detail. Notice that you can change the time range of the charts.

![](./media/app-insights-javascript/appinsights-49usage.png)


Click a chart to see other metrics that you can display, or add a new chart and select the metrics it displays.

![](./media/app-insights-javascript/appinsights-63usermetrics.png)

> [AZURE.NOTE] Metrics can only be displayed in some combinations. When you select a metric, the incompatible ones are disabled.



## Custom page counts

By default, a page count occurs each time a new page loads into the client browser.  But you might want to count additional page views. For example, a page might display its content in tabs and you want to count a page when the user switches tabs. Or JavaScript code in the page might load new content without changing the browser's URL. 

Insert a JavaScript call like this at the appropriate point in your client code:

    appInsights.trackPageView(myPageName);

The page name can contain the same characters as a URL, but anything after "#" or "?" will be ignored.


## Inspect individual page view events

Usually page view telemetry is analysed by Application Insights and you see only cumulative reports, averaged over all your users. But for debugging purposes, you can also look at individual page view events.

In the Diagnostic Search blade, set Filters to Page View.

![](./media/app-insights-javascript/12-search-pages.png)

Select any event to see more detail. In the details page, click "..." to see even more detail.

> [AZURE.NOTE] If you use [Search][diagnostic], notice that you have to match whole words: "Abou" and "bout" do not match "About", but "Abou* " does. And you cannot begin a search term with a wildcard. For example, searching for "*bou" would not match "About". 

> [Learn more about diagnostic search][diagnostic]

### Page view properties

* **Page view duration** The time taken to load the page and start running scripts. Specifically, the interval between starting to load the page and execution of the trackPageView. If you moved trackPageView from its usual position after the initialization of the script, it will reflect a different value. 

## Custom usage tracking

Want to find out what your users do with your app? By inserting calls in your client and server code, you can send your own telemetry to Application Insights. For example, you could find out the numbers of users who create orders without completing them, or which validation errors are hit most often, or the average score in a game.

[Learn about the custom events and metrics API][track].

## Server telemetry

If you haven't done this yet, you can get insights from your server and display the data along with your client-side data, enabling you to assess performance at the server and diagnose any issues.

* [Add Application Insights to an ASP.NET app][greenbrown]
* [Add Application Insights to a Java web app][java]


## <a name="video"></a> Video: Tracking Usage

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Next steps

[Track usage with custom events and metrics][track]




<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[new]: app-insights-create-new-resource.md
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

 
