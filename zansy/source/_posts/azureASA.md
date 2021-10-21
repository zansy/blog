title: Azure Stream Analytics Notes(2021/10/13 updated)
author: zansy
toc: true
tags:
  - Azure
categories:
  - 唯有爱与工作不可辜负
date: 2021-10-09 18:32:00
---
Azure Stream Analytics is a real-time analytics and complex event-processing engine that is designed to analyze and process high volumes of fast streaming data from multiple sources simultaneously.
<!--more-->
# Overview
The following scenarios are examples of when you can use Azure Stream Analytics:

- Analyze real-time telemetry streams from IoT devices
- Web logs/clickstream analytics
- Geospatial analytics for fleet management and driverless vehicles
- Remote monitoring and predictive maintenance of high value assets
- Real-time analytics on Point of Sale data for inventory control and anomaly detection

![](/images/ASA/MicrosoftTeams-image.png)

## How does Stream Analytics work?
An Azure Stream Analytics job consists of an input, query, and an output. Stream Analytics ingests data from Azure Event Hubs (including Azure Event Hubs from Apache Kafka), Azure IoT Hub, or Azure Blob Storage. The query, which is based on SQL query language, can be used to easily filter, sort, aggregate, and join streaming data over a period of time.

The following image shows how data is sent to Stream Analytics, analyzed, and sent for other actions like storage or presentation:
![stream-analytics-e2e-pipeline](/images/ASA/stream-analytics-e2e-pipeline.png)

## Overview of Azure Stream Analytics Cluster
Stream Analytics clusters are powered by the same engine that powers Stream Analytics jobs running in a multi-tenant environment. The single tenant, dedicated cluster have the following features:

- Single tenant hosting with no noise from other tenants. Your resources are truly "isolated" and performs better when there are burst in traffic.
- Scale your cluster between 36 to 216 SUs as your streaming usage increases over time.
- VNet support that allows your Stream Analytics jobs to connect to other resources securely using private endpoints.
- Ability to author C# user-defined functions and custom deserializers in any region.
- Zero maintenance cost allowing you to focus your effort on building real-time analytics solutions.

# Quickstart
## Create a Stream Analytics job

[by using the Azure portal](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-portal)
[by using the Azure CLI](https://docs.microsoft.com/en-us/azure/stream-analytics/quick-create-azure-cli)
[by using an ARM template](https://docs.microsoft.com/en-us/azure/stream-analytics/quick-create-azure-resource-manager)
[by using Azure PowerShell](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-powershell)
[by using Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-quick-create-vs)
[by Visual Studio Code](https://docs.microsoft.com/en-us/azure/stream-analytics/quick-create-visual-studio-code)

## Create a dedicated Azure Stream Analytics cluster
[using Azure portal](https://docs.microsoft.com/en-us/azure/stream-analytics/create-cluster)

# Tutorial
## [Analyze fraudulent call data with Stream Analytics and visualize results in Power BI dashboard](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-real-time-fraud-detection)

- Create an Azure Event Hub
- Start the event generator application(sends data to the event hub)
- Create a Stream Analytics job
- Configure job input/output
- Create queries to transform real-time data
- Start the job and visualize output

## [Run Azure Functions from Azure Stream Analytics jobs](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-with-azure-functions)
Stream Analytics invokes Functions through HTTP triggers. The Functions output adapter allows users to connect Functions to Stream Analytics, such that the events can be triggered based on Stream Analytics queries.

## [Write a C# user-defined function for Azure Stream Analytics job (Preview)](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-edge-csharp-udf)

C# user-defined functions (UDFs) created in Visual Studio allow you to extend the Azure Stream Analytics query language with your own functions. You can reuse existing code (including DLLs) and use mathematical or complex logic with C#. There are three ways to implement UDFs: CodeBehind files in a Stream Analytics project, UDFs from a local C# project, or UDFs from an existing package from a storage account.

## [Custom .NET deserializers for Azure Stream Analytics](https://docs.microsoft.com/en-us/azure/stream-analytics/custom-deserializer)

## [Deploy Azure Stream Analytics as an IoT Edge module](https://docs.microsoft.com/en-us/azure/iot-edge/tutorial-deploy-stream-analytics)
Many IoT solutions use analytics services to gain insight about data as it arrives in the cloud from IoT devices. With Azure IoT Edge, you can take Azure Stream Analytics logic and move it onto the device itself. By processing telemetry streams at the edge, you can reduce the amount of uploaded data and reduce the time it takes to react to actionable insights.

Azure IoT Edge and Azure Stream Analytics are integrated to simplify your workload development. You can create an Azure Stream Analytics job in the Azure portal and then deploy it as an IoT Edge module with no additional code.
