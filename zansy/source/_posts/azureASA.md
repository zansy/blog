title: Azure Stream Analytics Notes(2021/10/09 updated)
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

