title: Microsoft Ignite-Azure Admin Challenge(2022/01/10 updated)
author: zansy
toc: true
tags:
  - Azure
categories:
  - 唯有爱与工作不可辜负
date: 2022-01-10 17:04:00
---
[This challenge](https://docs.microsoft.com/en-us/users/cloudskillschallenge/collections/8pm3fxd3kyjo?WT.mc_id=cloudskillschallenge_10d2f838-16b2-4c16-ba6d-bc85422e43c7) is for developers to help implement, manage, and monitor an organization’s Microsoft Azure environment.
<!--more-->

# Configure storage accounts

## Implement Azure storage
Can generally think of Azure storage in three categories.

- **Storage for Virtual Machines.** Virtual machine storage includes disks and files. Disks are persistent block storage for Azure IaaS virtual machines. Files are fully managed file shares in the cloud.
- **Unstructured Data.** Unstructured data includes **Blobs** and **Data Lake Store**. Blobs are highly scalable, <u>REST-based</u> cloud object store. Data Lake Store is <u>Hadoop Distributed File System (HDFS)</u> as a service.
- **Structured Data.** Structured data includes **Tables, Cosmos DB, and Azure SQL DB**. Tables are a key/value, autoscaling NoSQL store. Cosmos DB is a <u>globally distributed database service</u>. Azure SQL DB is a fully managed database-as-a-service built on SQL.

## Explore Azure storage services
Azure Storage includes these data services, each of which is accessed through a storage account.

- **Azure Containers (Blobs)**: A massively scalable object store for text and binary data.
- **Azure Files**: Managed file shares for cloud or on-premises deployments.
- **Azure Queues**: A messaging store for reliable messaging between application components.
- **Azure Tables**: A NoSQL store for schemaless storage of structured data.

## Determine replication strategies
The data in your Azure storage account is always replicated to ensure durability and high availability. Azure Storage replication copies your data so that it is protected from planned and unplanned events ranging from transient hardware failures, network or power outages, massive natural disasters, and so on. 

- **Locally redundant storage**. LRS is the lowest-cost replication option and offers the least durability compared to other options. If a datacenter-level disaster (for example, fire or flooding) occurs, all replicas may be lost or unrecoverable.
- **Zone redundant storage**. Zone Redundant Storage (ZRS) <u>synchronously replicates your data across three (3) storage clusters in a single region</u>. Each storage cluster is physically separated from the others and resides in its own availability zone. Each availability zone, and the ZRS cluster within it, is autonomous, with separate utilities and networking capabilities. Storing your data in a ZRS account ensures that you will be able access and manage your data if a zone becomes unavailable. ZRS provides excellent performance and low latency.
- **Geo-redundant storage**. Geo-redundant storage (GRS) <u>replicates your data to a secondary region (hundreds of miles away from the primary location of the source data)</u>. GRS provides a higher level of durability even if there is a regional outage. GRS is designed to provide at least 99.99999999999999% (16 9's) durability. When your storage account has GRS enabled, then your data is durable even when there is a complete regional outage or a disaster where the primary region isn't recoverable.
- **Geo-zone redundant storage**. Geo-zone-redundant storage (GZRS) <u>combines the high availability of zone-redundant storage with protection from regional outages as provided by geo-redundant storage</u>. Data in a GZRS storage account is replicated across three Azure availability zones in the primary region and also replicated to a secondary geographic region for protection from regional disasters. Each Azure region is paired with another region within the same geography, together making a regional pair.

# Configure blob storage

## Implement blob storage
Azure Blob storage is a service that stores unstructured data in the cloud as objects/blobs. Blob storage can store any type of text or binary data, such as a document, media file, or application installer. Blob storage is also referred to as object storage.

Common uses of Blob storage include:

- Serving images or documents directly to a browser.
- Storing files for distributed access, such as installation.
- Streaming video and audio.
- Storing data for backup and restore, disaster recovery, and archiving.
- Storing data for analysis by an on-premises or Azure-hosted service.

Blob storage offers three types of resources:

- The storage account
- Containers in the storage account
- Blobs in a container

![](https://docs.microsoft.com/en-us/learn/wwl-azure/configure-blob-storage/media/blob-storage-94fb52b8.png)

## Create blob containers
**Public access level**: Specifies whether data in the container may be accessed publicly. By default, container data is private to the account owner.

- Use **Private** to ensure there is no anonymous access to the container and blobs.
- Use **Blob** to allow anonymous public read access for blobs only.
- Use **Container** to allow anonymous public read and list access to the entire container, including the blobs.

## Create blob access tiers
- **Hot**. The Hot tier is optimized for <u>frequent access</u> of objects in the storage account. New storage accounts are created in the Hot tier by default.
- **Cool**. The Cool tier is optimized for <u>storing large amounts of data that is infrequently accessed and stored for at least 30 days</u>. Storing data in the Cool tier is more cost-effective, but accessing that data may be more expensive than accessing data in the Hot tier.
- **Archive**. The Archive tier is optimized for <u>data that can tolerate several hours of retrieval latency and will remain in the Archive tier for at least 180 days</u>. The Archive tier is the most cost-effective option for storing data, but accessing that data is more expensive than accessing data in the Hot or Cool tiers.

## Add blob lifecycle management rules
The lifecycle management policy lets you:

- Transition blobs to a cooler storage tier (hot to cool, hot to archive, or cool to archive) to optimize for performance and cost.
- Delete blobs at the end of their lifecycles.
- Define rules to be run once per day at the storage account level.
- Apply rules to containers or a subset of blobs.

## Determine blob object replication
Object replication asynchronously copies block blobs in a container according to rules that you configure. The contents of the blob, any versions associated with the blob, and the blob's metadata and properties are all copied from the source container to the destination container.

### Considerations
- Object replication requires that blob versioning is enabled on both the source and destination accounts.
- Object replication doesn't support blob snapshots. Any snapshots on a blob in the source account are not replicated to the destination account.
- Object replication is supported when the source and destination accounts are in the hot or cool tier. The source and destination accounts may be in different tiers.
- When you configure object replication, you create a replication policy that specifies the source storage account and the destination account. A replication policy includes one or more rules that specify a source container and a destination container and indicate which block blobs in the source container will be replicated.

## Upload blobs
A blob can be any type and size file. Azure Storage offers three types of blobs: **block blobs, page blobs**, and **append blobs**. You specify the blob type and access tier when you create the blob.

- **Block blobs (default)** consist of blocks of data assembled to make a blob. Most scenarios using Blob storage employ block blobs. Block blobs are ideal for storing text and binary data in the cloud, like files, images, and videos.
- **Append blobs** are like block blobs in that they are made up of blocks, but they are optimized for append operations, so they are useful for logging scenarios.
- **Page blobs** can be up to 8 TB in size and are more efficient for frequent read/write operations. Azure virtual machines use page blobs as OS and data disks.

# Configure storage security
## Review storage security strategies
Azure Storage provides a comprehensive set of security capabilities that together enable developers to build secure applications.

- **Encryption**. All data written to Azure Storage is automatically encrypted using Storage Service Encryption (SSE).

- **Authentication**. Azure Active Directory (Azure AD) and Role-Based Access Control (RBAC) are supported for Azure Storage for both resource management operations and data operations, as follows:
  - You can assign RBAC roles scoped to the storage account to security principals and use Azure AD to authorize resource management operations such as key management.
  - Azure AD integration is supported for data operations on the Blob and Queue services.
  
- **Data in transit**. Data can be secured in transit between an application and Azure by using Client-Side Encryption, HTTPS, or SMB 3.0.

- **Disk encryption**. OS and data disks used by Azure virtual machines can be encrypted using Azure Disk Encryption.

- **Shared Access Signatures**. Delegated access to the data objects in Azure Storage can be granted using Shared Access Signatures.

## Create shared access signatures
A **shared access signature (SAS)** is a <u>URI that grants restricted access rights to Azure Storage resources</u>. You can provide a SAS to clients who shouldn't have access to your storage account key. By distributing a SAS URI to these clients, <u>you grant them access to a resource for a specified period of time</u>. SAS is a secure way to share your storage resources without compromising your account keys.

## Identify URI and SAS parameters
When you create your SAS, a URI is created using parameters and tokens. The URI consists of your Storage Resource URI and the SAS token.

## Determine storage service encryption
Azure **Storage Service Encryption (SSE)** for data at rest protects your data by ensuring your organizational security and compliance commitments are met.

SSE automatically encrypts your data before persisting it to Azure-managed Disks, Azure Blob, Queue, Table storage, or Azure Files, and decrypts the data before retrieval.

SSE encryption, encryption at rest, decryption, and key management are transparent to users. All data written to the Azure storage platform is encrypted through 256-bit AES encryption, one of the strongest block ciphers available.