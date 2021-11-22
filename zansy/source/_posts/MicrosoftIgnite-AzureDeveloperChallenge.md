title: Microsoft Ignite: Azure Developer Challenge(2021/11/22 updated)
author: zansy
toc: true
tags:
  - Azure
categories:
  - 唯有爱与工作不可辜负
date: 2021-11-22 17:04:00
---
[This challenge](https://docs.microsoft.com/zh-cn/learn/challenges?id=af95eabc-aafa-4ba1-8ec7-1c6444102f70) is for developers interested in designing, building, testing, and maintaining cloud applications and services on Microsoft Azure.
<!--more-->

#  Explore Azure Functions
## Discover Azure Functions
**Azure Functions** are a great solution for processing data, integrating systems, working with the internet-of-things (IoT), and building simple APIs and microservices. Consider Functions for tasks like image or order processing, file maintenance, or for any tasks that you want to run on a schedule.

### Compare Azure Functions and Azure Logic Apps
Both Functions and Logic Apps enable serverless workloads. Azure Functions is a serverless compute service, whereas Azure Logic Apps provides serverless workflows. Both can create complex orchestrations.

For Azure Functions, you develop orchestrations by writing code and using the Durable Functions extension. For Logic Apps, you create orchestrations by using a GUI or editing configuration files.

You can mix and match services when you build an orchestration, calling functions from logic apps and calling logic apps from functions. 

|                   | Durable Functions                                                     | Logic Apps                                                                                             |
|-------------------|-----------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| Development       | Code-first (imperative)                                               | Designer-first (declarative)                                                                           |
| Connectivity      | About a dozen built-in binding types, write code for custom bindings  | Large collection of connectors, Enterprise Integration Pack for B2B scenarios, build custom connectors |
| Actions           | Each activity is an Azure function; write code for activity functions | Large collection of ready-made actions                                                                 |
| Monitoring        | Azure Application Insights                                            | Azure portal, Azure Monitor logs                                                                       |
| Management        | REST API, Visual Studio                                               | Azure portal, REST API, PowerShell, Visual Studio                                                      |
| Execution context | Can run locally or in the cloud                                       | Runs only in the cloud                                                                                 |

### Compare Functions and WebJobs
Like Azure Functions, Azure App Service WebJobs with the WebJobs SDK is a code-first integration service that is designed for developers. Both are built on Azure App Service and support features such as source control integration, authentication, and monitoring with Application Insights integration.

Azure Functions is built on the WebJobs SDK, so it shares many of the same event triggers and connections to other Azure services.

|                                             | Functions                                                                                                                                              | WebJobs with WebJobs SDK                                                                                              |
|---------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------|
| Serverless app model with automatic scaling | Yes                                                                                                                                                    | No                                                                                                                    |
| Develop and test in browser                 | Yes                                                                                                                                                    | No                                                                                                                    |
| Pay-per-use pricing                         | Yes                                                                                                                                                    | No                                                                                                                    |
| Integration with Logic Apps                 | Yes                                                                                                                                                    | No                                                                                                                    |
| Trigger events                              | Timer Azure Storage queues and blobs Azure Service Bus queues and topics Azure Cosmos DB Azure Event Hubs HTTP/WebHook (GitHub Slack) Azure Event Grid | Timer Azure Storage queues and blobs Azure Service Bus queues and topics Azure Cosmos DB Azure Event Hubs File system | 

## Compare Azure Functions hosting options
There are three basic hosting plans available for Azure Functions: *Consumption plan, Functions Premium plan, and App service (Dedicated) plan.*

The following is a summary of the benefits of the three main hosting plans for Functions:

| Plan                   | Benefits                                                                                                                                                                                                                                    |
|------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Consumption plan       | This is the default hosting plan. It scales automatically and you only pay for compute resources when your functions are running. Instances of the Functions host are dynamically added and removed based on the number of incoming events. |
| Functions Premium plan | Automatically scales based on demand using pre-warmed workers which run applications with no delay after being idle, runs on more powerful instances, and connects to virtual networks.                                                     |
| App service plan       | Run your functions within an App Service plan at regular App Service plan rates. Best for long-running scenarios where Durable Functions can't be used.                                                                                     |

### Always on
If you run on an App Service plan, you should enable the Always on setting so that your function app runs correctly. On an App Service plan, the functions runtime goes idle after a few minutes of inactivity, so only HTTP triggers will "wake up" your functions. Always on is available only on an App Service plan. 

### Storage account requirements
On any plan, a function app requires a general Azure Storage account, which supports Azure Blob, Queue, Files, and Table storage. This is because Functions relies on Azure Storage for operations such as managing triggers and logging function executions, but some storage accounts do not support queues and tables.

## Scale Azure Functions
In the Consumption and Premium plans, Azure Functions scales CPU and memory resources by adding additional instances of the Functions host. The number of instances is determined on the number of events that trigger a function.

### Runtime scaling

Azure Functions uses a component called *the scale controller* to monitor the rate of events and determine whether to scale out or scale in. The scale controller uses heuristics for each trigger type. For example, when you're using an Azure Queue storage trigger, it scales based on the queue length and the age of the oldest queue message.

The unit of scale for Azure Functions is the function app. When the function app is scaled out, additional resources are allocated to run multiple instances of the Azure Functions host. Conversely, as compute demand is reduced, the scale controller removes function host instances. The number of instances is eventually "scaled in" to zero when no functions are running within a function app.

### Scaling behaviors
Scaling can vary on a number of factors, and scale differently based on the trigger and language selected. There are a few intricacies of scaling behaviors to be aware of:
- **Maximum instances:** A single function app only scales out to a maximum of 200 instances. A single instance may process more than one message or request at a time though, so there isn't a set limit on number of concurrent executions.

- **New instance rate:** For HTTP triggers, new instances are allocated, at most, once per second. For non-HTTP triggers, new instances are allocated, at most, once every 30 seconds. Scaling is faster when running in a Premium plan.

### Limit scale out

You may wish to restrict the maximum number of instances an app used to scale out. This is most common for cases where a downstream component like a database has limited throughput. By default, Consumption plan functions scale out to as many as 200 instances, and Premium plan functions will scale out to as many as 100 instances. You can specify a lower maximum for a specific app by modifying the value. The can be set to or for unrestricted, or a valid value between and the app maximum.

## Knowledge check
>Which of the following Azure Functions hosting plans is best when predictive scaling and costs are required?

App service plan

>An organization wants to implement a serverless workflow to solve a business problem. One of the requirements is the solution needs to a designer-first (declarative) development model. Which of the choices below meets the requirements?

Azure Logic Apps
