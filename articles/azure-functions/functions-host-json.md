---
title: host.json reference for Azure Functions 2.x
description: Reference documentation for the Azure Functions host.json file with the v2 runtime.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: glenga
---

# host.json reference for Azure Functions 2.x  

> [!div class="op_single_selector" title1="Select the version of the Azure Functions runtime you are using: "]
> * [Version 1](functions-host-json-v1.md)
> * [Version 2](functions-host-json.md)

The *host.json* metadata file contains global configuration options that affect all functions for a function app. This article lists the settings that are available for the v2 runtime.  

> [!NOTE]
> This article is for Azure Functions 2.x.  For a reference of host.json in Functions 1.x, see [host.json reference for Azure Functions 1.x](functions-host-json-v1.md).

Other function app configuration options are managed in your [app settings](functions-app-settings.md).

Some host.json settings are only used when running locally in the [local.settings.json](functions-run-local.md#local-settings-file) file.

## Sample host.json file

The following sample *host.json* files have all possible options specified.

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
            "Function.MyFunction": "Information",
            "default": "None"
        },
        "applicationInsights": {
            "enableLiveMetrics": true,
            "enableDependencyTracing": true,
            "enablePerformanceCountersCollection": true,
            "samplingExcludedTypes": "Dependency;Event",
            "samplingIncludedTypes": "Exception;PageView;Request;Trace",        
            "samplingSettings": {
                "isEnabled": true,
                "maxTelemetryItemsPerSecond": 20,
                "evaluationInterval": "00:10:00",
                "initialSamplingPercentage": 1.0,
                "maxSamplingPercentage": 1.0,
                "minSamplingPercentage": 0.1,
                "movingAverageRatio": 1.0,
                "samplingPercentageDecreaseTimeout": "00:02:00",
                "samplingPercentageIncreaseTimeout": "00:15:00"
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
        "lockPeriod": "00:00:15",
        "listenerLockPeriod": "00:01:00",
        "listenerLockRecoveryPollingInterval": "00:01:00",
        "lockAcquisitionTimeout": "00:01:00",
        "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

The following sections of this article explain each top-level property. All are optional unless otherwise indicated.

## aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## applicationInsights

This setting is a child of [logging](#logging).

Controls the [sampling feature in Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "enableLiveMetrics": true,
        "enableDependencyTracing": true,
        "enablePerformanceCountersCollection": true,
        "samplingExcludedTypes": "Dependency;Event",
        "samplingIncludedTypes": "Exception;PageView;Request;Trace",
        "samplingSettings": {
            "isEnabled": true,
            "maxTelemetryItemsPerSecond" : 20
            "evaluationInterval": "00:10:00",
            "initialSamplingPercentage": 1.0,
            "maxSamplingPercentage": 1.0,
            "minSamplingPercentage": 0.1,
            "movingAverageRatio": 1.0,
            "samplingPercentageDecreaseTimeout": "00:02:00",
            "samplingPercentageIncreaseTimeout": "00:15:00"
        }
    }
}
```

> [!NOTE]
> Log sampling may cause some executions to not show up in the Application Insights monitor blade. To avoid log sampling, add `"Request"` to the value for `"samplingExcludedTypes"` to the `applicationInsights` value.

| Property | Default | Description |
| --------- | --------- | --------- | 
| enableLiveMetrics | true | Enables live metrics collection. |
| enableDependencyTracking | true | Enables dependency tracking. |
| enablePerformanceCountersCollection | true | Enables Kudu performance counters collection. |
| samplingExcludedTypes | "Dependency;Event" | A semi-colon delimited list of types that you don't want to be sampled. Recognized types are: Dependency, Event, Exception, PageView, Request, Trace. All instances of the specified types are transmitted; the types that are not specified are sampled. |
| samplingIncludedTypes | "PageView;Trace" | A semi-colon delimited list of types that you want to be sampled; an empty list implies all types. Type listed in `samplingExcludedTypes` override types listed here. Recognized types are: Dependency, Event, Exception, PageView, Request, Trace. All instances of the specified types are transmitted; the types that are not specified are sampled. |
| samplingSettings.isEnabled | true | Enables or disables sampling. | 
| samplingSettings.maxTelemetryItemsPerSecond | 20 | The target number of telemetry items logged per second on each server host. If your app runs on many hosts, reduce this value to remain within your overall target rate of traffic. | 
| samplingSettings.evaluationInterval | 01:00:00 | The interval at which the current rate of telemetry is reevaluated. Evaluation is performed as a moving average. You might want to shorten this interval if your telemetry is liable to sudden bursts. |
| samplingSettings.initialSamplingPercentage| 1.0 | The initial sampling percentage applied at the start of the sampling process to dynamically vary the percentage. Don't reduce value while you're debugging. |
| samplingSettings.minSamplingPercentage | 0.1 | As sampling percentage varies, this property determines the minimum allowed sampling percentage. |
| samplingSettings.maxSamplingPercentage | 1.0 | As sampling percentage varies, this property determines the maximum allowed sampling percentage. |
| samplingSettings.movingAverageRatio | 1.0 | In the calculation of the moving average, the weight assigned to the most recent value. Use a value equal to or less than 1. Smaller values make the algorithm less reactive to sudden changes. |
| samplingSettings.samplingPercentageIncreaseTimeout | 00:00:01 | When the sampling percentage value changes, this property determines how soon afterwards Application Insights is allowed to raise sampling percentage again to capture more data. |
| samplingSettings.samplingPercentageDecreaseTimeout | 00:00:01 | When the sampling percentage value changes, this property determines how soon afterwards Application Insights is allowed to lower sampling percentage again to capture less data. |

## cosmosDb

Configuration setting can be found in [Cosmos DB triggers and bindings](functions-bindings-cosmosdb-v2.md#host-json).

## durableTask

Configuration setting can be found in [bindings for Durable Functions](durable/durable-functions-bindings.md#host-json).

## eventHub

Configuration settings can be found in [Event Hub triggers and bindings](functions-bindings-event-hubs.md#host-json). 

## extensions

Property that returns an object that contains all of the binding-specific settings, such as [http](#http) and [eventHub](#eventhub).

## functions

A list of functions that the job host runs. An empty array means run all functions. Intended for use only when [running locally](functions-run-local.md). In function apps in Azure, you should instead follow the steps in [How to disable functions in Azure Functions](disable-function.md) to disable specific functions rather than using this setting.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## functionTimeout

Indicates the timeout duration for all functions. It follows the timespan string format. In a serverless Consumption plan, the valid range is from 1 second to 10 minutes, and the default value is 5 minutes.  
In a Dedicated (App Service) plan, there is no overall limit, and the default value is 30 minutes. A value of `-1` indicates unbounded execution.

```json
{
    "functionTimeout": "00:05:00"
}
```

## healthMonitor

Configuration settings for [Host health monitor](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Property  |Default | Description |
|---------|---------|---------| 
|enabled|true|Specifies whether the feature is enabled. | 
|healthCheckInterval|10 seconds|The time interval between the periodic background health checks. | 
|healthCheckWindow|2 minutes|A sliding time window used in conjunction with the `healthCheckThreshold` setting.| 
|healthCheckThreshold|6|Maximum number of times the health check can fail before a host recycle is initiated.| 
|counterThreshold|0.80|The threshold at which a performance counter will be considered unhealthy.| 

## http

Configuration settings can be found in [http triggers and bindings](functions-bindings-http-webhook.md#hostjson-settings).

## logging

Controls the logging behaviors of the function app, including Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Property  |Default | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Defines what level of file logging is enabled.  Options are `never`, `always`, `debugOnly`. |
|logLevel|n/a|Object that defines the log category filtering for functions in the app. Version 2.x follows the ASP.NET Core layout for log category filtering. This lets you filter logging for specific functions. For more information, see [Log filtering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) in the ASP.NET Core documentation. |
|console|n/a| The [console](#console) logging setting. |
|applicationInsights|n/a| The [applicationInsights](#applicationinsights) setting. |

## console

This setting is a child of [logging](#logging). It controls the console logging when not in debugging mode.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Property  |Default | Description |
|---------|---------|---------| 
|isEnabled|false|Enables or disables console logging.| 

## managedDependency

Managed dependency is a feature that is currently only supported with PowerShell based functions. It enables dependencies to be automatically managed by the service. When the `enabled` property is set to `true`, the `requirements.psd1` file is processed. Dependencies are updated when any minor versions are released. For more information, see [Managed dependency](functions-reference-powershell.md#dependency-management) in the PowerShell article.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## queues

Configuration settings can be found in [Storage queue triggers and bindings](functions-bindings-storage-queue.md#host-json).  

## sendGrid

Configuration setting can be found in [SendGrid triggers and bindings](functions-bindings-sendgrid.md#host-json).

## serviceBus

Configuration setting can be found in [Service Bus triggers and bindings](functions-bindings-service-bus.md#host-json).

## singleton

Configuration settings for Singleton lock behavior. For more information, see [GitHub issue about singleton support](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Property  |Default | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|The period that function level locks are taken for. The locks auto-renew.| 
|listenerLockPeriod|00:01:00|The period that listener locks are taken for.| 
|listenerLockRecoveryPollingInterval|00:01:00|The time interval used for listener lock recovery if a listener lock couldn't be acquired on startup.| 
|lockAcquisitionTimeout|00:01:00|The maximum amount of time the runtime will try to acquire a lock.| 
|lockAcquisitionPollingInterval|n/a|The interval between lock acquisition attempts.| 

## version

The version string `"version": "2.0"` is required for a function app that targets the v2 runtime.

## watchDirectories

A set of [shared code directories](functions-reference-csharp.md#watched-directories) that should be monitored for changes.  Ensures that when code in these directories is changed, the changes are picked up by your functions.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## Next steps

> [!div class="nextstepaction"]
> [Learn how to update the host.json file](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [See global settings in environment variables](functions-app-settings.md)
