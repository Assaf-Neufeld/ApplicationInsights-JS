<properties 
	pageTitle="Application Insights SDK JavaScript API" 
	description="Reference doc" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# Application Insights SDK JavaScript API

The JavaScript SDK is loaded into your web page when you set up [web page tracking](https://azure.microsoft.com/documentation/articles/app-insights-javascript/) in [Application Insights](https://azure.microsoft.com/services/application-insights/).
You add a short snippet of code to the page, which pulls in the rest of the SDK.

* [How to set up web page tracking](https://azure.microsoft.com/documentation/articles/app-insights-javascript/)
* [Overview of the API and examples](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/)

## class AppInsights

The shop front for the SDK, that sends telemetry to Application Insights.

In a web page where you have [set up web page tracking](https://azure.microsoft.com/documentation/articles/app-insights-javascript/), you can use the instance `appInsights`. For example:
    
    appInsights.trackPageView("page1");


### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Logs that a page or similar container was displayed to the user. 


 | |
---|---|---
`name` | The name used to identify the page in the portal. Defaults to the document title.
`url` |  A relative or absolute URL that identifies the page or similar item. Defaults to the window location.
`properties` | Map of string to string: Additional data used to [filter pages](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) in the portal. Defaults to empty.
`measurements` | Map of string to number: Metrics associated with this page, displayed in Metrics Explorer on the portal. Defaults to empty.

The standard snippet that you get from the portal includes a call to trackPageView. If you insert your own calls, consider
removing this default. An example where you might write your own calls is where your app is a single HTML page that has multiple
tabs, and you want to log a page view when each tab opens.

### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:number})

Log a user action or other occurrence.

 | | 
---|---|---
 `name` | Identifies the event. Events with the same name are counted and can be charted in [Metric Explorer](https://azure.microsoft.com/documentation/articles/app-insights-metrics-explorer/).
`properties` | Map of string to string: Additional data used to [filter events](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) in the portal. Defaults to empty.
`measurements` | Map of string to number: Metrics associated with this page, displayed in Metrics Explorer on the portal. Defaults to empty.

In the portal, you can select events by name, and [display charts that count them or display associated measurements](https://azure.microsoft.com/documentation/articles/app-insights-metrics-explorer/).

You can also search and [display individual events](https://azure.microsoft.com/documentation/articles/app-insights-diagnostic-search/).


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)

Log a positive numeric value that is not associated with a specific event. Typically used to send regular reports of performance indicators. 

 | | 
---|---|---
`name` | A string that identifies the metric. In the portal, you can select metrics for display by name.
`average` | Either a single measurement, or the average of several measurements. Should be >=0 to be correctly displayed.
`sampleCount` | Count of measurements represented by the average. Defaults to 1. Should be >=1.
`min` | The smallest measurement in the sample. Defaults to the average. Should be >= 0.
`max` | The largest measurement in the sample. Defaults to the average. Should be >= 0.

In the portal, you can select metrics by name to [chart their values over time](https://azure.microsoft.com/documentation/articles/app-insights-metrics-explorer/). You can't search or view individual trackMetric calls.

To send a single measurement, use just the first two parameters. If you take measurements very frequently, you can reduce the telemetry bandwidth by aggregating multiple measurements and sending the resulting average at intervals.


### trackException

    trackException(exception: Error, handledAt?: string, properties?: {[string]:string}, measurements?: {[string]:number})

Log an exception you have caught. (Exceptions caught by the browser are also logged.)

 | | 
---|---|---
`exception` | An Error from a catch clause.  
`handledAt` | Defaults to "unhandled".
`properties` | Map of string to string: Additional data used to [filter exceptions](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) in the portal. Defaults to empty.
`measurements` | Map of string to number: Metrics associated with this page, displayed in Metrics Explorer on the portal. Defaults to empty.

In the portal, you can [search on exception type and view](https://azure.microsoft.com/documentation/articles/app-insights-diagnostic-search/) the type, message, and stack trace of individual instances. 


### trackTrace

    trackTrace(message: string, properties?: {[string]:string}, measurements?: {[string]:number})

Log a diagnostic event such as entering or leaving a method.

 | | 
---|---|---
`message` | Diagnostic data. Can be much longer than a name.
`properties` | Map of string to string: Additional data used to [filter exceptions](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) in the portal. Defaults to empty.
`measurements` | Map of string to number: Metrics associated with this page, displayed in Metrics Explorer on the portal. Defaults to empty.


In the portal, you can search on message content and [display individual trackTrace events](https://azure.microsoft.com/documentation/articles/app-insights-diagnostic-search/).
(Unlike `trackEvent`, you can't filter on the message content in the portal.)


### flush

    flush()

Immediately send all queued telemetry. Synchronous.

You don't usually have to use this, as it happens automatically on window closing.


### config

    config: IConfig

Values that control how the telemetry data is sent.

    interface IConfig {
        // The key of your Application Insights resource in Azure
        instrumentationKey: string;
        
        // The Application Insights server
        endpointUrl: string;
        
        accountId: string;
        appUserId: string;
        
        // A session is logged if the user is inactive for this time in milliseconds. Default 30 mins.
        sessionRenewalMs: number; 
        
        // A session is logged if it has continued for this time in milliseconds. Default 24h.
        sessionExpirationMs: number;
        
        // Default 200k
        maxPayloadSizeInBytes: number; 
        
        //Default 100k
        maxBatchSizeInBytes: number;
        
        // Default 15s
        maxBatchInterval: number;
        
        // If true, data is sent immediately and not batched.
        enableDebug: boolean;
        
        // Report errors caught by the browser. Default true.
        autoCollectErrors: boolean;
        
        // If true, telemetry data is not collected or sent. Default false.
        disableTelemetry: boolean; 
        
        verboseLogging: boolean;
        
        // Default 10s:
        diagnosticLogInterval: number;
    }

Set these values in [the snippet](https://azure.microsoft.com/documentation/articles/app-insights-javascript-api/) that you insert in your web pages.
Look for this line, and add more items:

    })({
      instrumentationKey: "000...000"
    });

You can also read or write them dynamically:

   appInsights.config.disableTelemetry = true;

### context

    context: TelemetryContext

Information that the SDK attempts to extract from the environment about the device, location, and user. 




## class TelemetryContext


### context.application

    application: Context.Application

Details of the app you're monitoring.

     context.application.ver: string
     context.application.build : string

        
### context.device

    device : Context.Device
    
 The device the app is running on.
 
    |   |
---|---|---
    device.type  | Type of device
    device.id	| unique ID
    device.oemName |
    device.model |
    device.network | number  - IANA interface type
    device.resolution  | screen res
    device.locale | display language of the OS
    device.ip |
    device.language |
    device.os |  OS running on the device
    device.osversion | 

### context.user

    user: Context.User

Data about the current user. Users are identified by cookie, so one person can look like 
more than one user if they use different machines or browsers, or delete cookies.

   |   |
---|---|---
`user.id` | Unique, cookie-based user id, automatically assigned.
`user.accountId` | 
`user.accountAcquisitionDate` |
`user.agent` | 
`user.storeRegion` | 


### context.session

    session: Context.Session
    
The user session. A session represents a series of user actions. A session starts with a user action.
It ends at the last user activity when there is no more activity for sessionRenewalMs, or if it lasts longer than sessionExpirationMs.

   |   | 
---|---|---
`session.id` | Automatically assigned id
`session.isFirst` | Boolean. True if this is the first session for this user.
`session.acquisitionDate` | Number. The dateTime when this session was created.
`session.renewalDate` | Number. DateTime when telemetry was last sent with this session.


### context.location

   location: Context.Location

Data from which the geographical location of the user's device can be guessed.

   |   | 
---|---|---
`location.ip` | IP address

### context.operation

        operation: Context.Operation;
        
Represents the user request. Operation id is used to tie together related events in diagnostic search.

   |   | 
---|---|---
`id` | Unique id
`name` | 
`parentId` |
`rootId` |
`syntheticSource` | String identifying the bot or test agent.


### track

        public track(envelope: Telemetry.Common.Envelope) ;

Sends telemetry to the endpoint.

## Links

* Read or contribute to the [code for the SDK](https://github.com/Microsoft/ApplicationInsights-js).
* [Overview of the API and examples](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/)

