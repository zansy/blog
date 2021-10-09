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