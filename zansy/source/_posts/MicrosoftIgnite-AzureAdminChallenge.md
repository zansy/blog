title: Microsoft Ignite-Azure Admin Challenge(2022/02/05 updated)
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

## Create customer managed keys
The Azure Key Vault can manage your encryption keys. You can create your own encryption keys and store them in a key vault, or you can use Azure Key Vault's APIs to generate encryption keys.

Customer-managed keys give you more flexibility and control. You can create, disable, audit, rotate, and define access controls.

# Configure Azure files and Azure File Sync
## Compare files to blobs
File storage offers shared storage for applications using the industry standard SMB protocol. Microsoft Azure virtual machines and cloud services can share file data across application components via mounted shares, and on-premises applications can also access file data in the share.

Applications running in Azure virtual machines or cloud services can mount a file storage share to access file data. This process is similar to how a desktop application would mount a typical SMB share. Any number of Azure virtual machines or roles can mount and access the File storage share simultaneously.

| Feature     | Description                                                                                                                               | When to use                                                                                                                                                                                                                                                                     |   |
|-------------|-------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
| Azure Files | Provides an SMB interface, client libraries, and a REST interface that allows access from anywhere to stored files.                       | You want to "lift and shift" an application to the cloud that already **uses the native file system APIs to share data between it and other applications running in Azure**. You want to store development and debugging tools that need to be accessed from many virtual machines. |   |
| Azure Blobs | Provides client libraries and a REST interface that allows unstructured data to be stored and accessed at a massive scale in block blobs. | You want your application to **support streaming and random-access scenarios**. You want to be able to access application data from anywhere.                                                                                                                                       |   |

Other distinguishing features, when selecting Azure files.

- Azure files are true directory objects. Azure blobs are a flat namespace.
- Azure files are accessed through file shares. Azure blobs are accessed through a container.
- Azure files provide shared access across multiple virtual machines. Azure disks are exclusive to a single virtual machine.

## Manage file shares
To access your files, you will need a storage account. After the storage account is created, provide the file share **Name** and the **Quota**. Quota refers to total size of files on the share.
- **Mapping file shares (Windows)**. You can connect to your Azure file share with Windows or Windows Server. Just select Connect from your file share page.
- **Mounting file shares (Linux)**. Azure file shares can be mounted in Linux distributions using the CIFS kernel client. File mounting can be done on-demand with the mount command or on-boot (persistent) by creating an entry in /etc/fstab.

## Create file share snapshots
Azure Files provides the capability to take share snapshots of file shares. Share snapshots capture a point-in-time, read-only copy of your data.

**Share snapshots are incremental in nature**. Only the data that has changed after your most recent share snapshot is saved. Incremental snapshots minimizes the time required to create the share snapshot and saves on storage costs. Even though share snapshots are saved incrementally, you need to retain only the most recent share snapshot in order to restore the share.

## Implement file sync
Use Azure File Sync to centralize your organization's file shares in Azure Files, while keeping the flexibility, performance, and compatibility of an on-premises file server. Azure File Sync transforms Windows Server into a quick cache of your Azure file share. You can use any protocol that's available on Windows Server to access your data locally, including SMB, NFS, and FTPS. You can have as many caches as you need across the world.

## Identify file sync components
![](https://docs.microsoft.com/en-us/learn/wwl-azure/configure-azure-files-file-sync/media/file-sync-components-c6561274.png)

## Deploy Azure File Sync
![](https://docs.microsoft.com/en-us/learn/wwl-azure/configure-azure-files-file-sync/media/file-sync-steps-b6fa9fd9.png)

# Configure storage with tools
## Use Azure Storage Explorer
Azure Storage Explorer is a standalone app that makes it easy to work with Azure Storage data on Windows, macOS, and Linux. With Storage Explorer, you can access multiple accounts and subscriptions and manage all your storage content.
![](https://docs.microsoft.com/en-us/learn/wwl-azure/configure-storage-tools/media/storage-explorer-304e94f3.png)

## Use the import and export service
Azure Import/Export service is used to securely import large amounts of data to Azure Blob storage and Azure Files by shipping disk drives to an Azure datacenter. This service can also be used to transfer data from Azure Blob storage to disk drives and ship to your on-premises sites. Data from one or more disk drives can be imported either to Azure Blob storage or Azure Files. With the Azure Import/Export service, you supply your own disk drives and transfer data yourself.

![](https://docs.microsoft.com/en-us/learn/wwl-azure/configure-storage-tools/media/import-jobs-3dd387ae.png)
![](https://docs.microsoft.com/en-us/learn/wwl-azure/configure-storage-tools/media/export-jobs-850746e1.png)

**Import/Export Tool (WAImportExport)**

## Use AzCopy
An alternative method for transferring data is AzCopy. AzCopy v10 is the next-generation command-line utility for **copying data to/from Microsoft Azure Blob and File storage, which offers a redesigned command-line interface and new architecture for high-performance reliable data transfers**. Using AzCopy, you can copy data between a file system and a storage account, or between storage accounts.

# Create an Azure Storage account
## Decide how many storage accounts you need

Azure groups four of these data services together under the name Azure Storage. The four services are **Azure Blobs, Azure Files, Azure Queues, and Azure Tables**. The following illustration shows the elements of **Azure Storage**.
![](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-azure-storage.png)

A **storage account** is a container that groups a set of Azure Storage services together. Only data services from Azure Storage can be included in a storage account (Azure Blobs, Azure Files, Azure Queues, and Azure Tables).

A storage account represents a collection of settings like location, replication strategy, and subscription owner. You need one storage account for each group of settings that you want to apply to your data. The following illustration shows two storage accounts that differ in one setting; that one difference is enough to require separate storage accounts.
![](https://docs.microsoft.com/en-us/learn/modules/create-azure-storage-account/media/2-multiple-storage-accounts.png)

## Choose your account settings
The storage account settings we've already covered apply to the data services in the account. Here, we will discuss the three settings that apply to the account itself, rather than to the data stored in the account:

- Name
- Deployment model: Microsoft recommends that you use **Resource Manager** for all new resources.
- Account kind: Microsoft recommends that you use the **General-purpose v2** option for new storage accounts.

These settings impact how you manage your account and the cost of the services within it.

# Control access to Azure Storage with shared access signatures

## Authorization options for Azure Storage
Files stored in Azure Storage are accessed by clients over HTTP/HTTPS. Azure checks each client request for authorization to access stored data. Four options are available for blob storage:
- Public access
- Azure Active Directory (Azure AD)
- Shared key
- Shared access signature (SAS)

### Shared access signature
A SAS lets you grant granular access to files in Azure Storage, such as **read-only or read-write access**, **expiration time**, after which the SAS no longer enables the client to access the chosen resources. A shared access signature is a key that grants permission to a storage resource, and should be protected in the same manner as an account key.

Azure Storage supports three types of shared access signatures:

- **User delegation SAS**: Can only be used for Blob storage and is secured with Azure AD credentials.
- **Service SAS**: A service SAS is secured using a storage account key. A service SAS delegates access to a resource in any one of four Azure Storage services: Blob, Queue, Table, or File.
- **Account SAS**: An account SAS is secured with a storage account key. An account SAS has the same controls as a service SAS, but can also control access to service-level operations, such as Get Service Stats.


You can create a SAS ad-hoc by specifying all the options you need to control, including **start time, expiration time**, and permissions.

If you plan to create a service SAS, there is also an option to associate it with a **stored access policy**. A stored access policy can be associated with up to five active SASs. You can control access and expiration at the stored access policy level. This is a good approach if you need to have granular control to change the expiration, or to revoke a SAS. The only way to revoke or change an ad-hoc SAS is to change the storage account keys.

## Use shared access signatures to delegate access to Azure Storage
[More details](https://docs.microsoft.com/en-us/learn/modules/control-access-to-azure-storage-with-sas/3-use-shared-access-signatures)

## Exercise - Use shared access signatures to delegate access to Azure Storage
RECOMMAND
[More details](https://docs.microsoft.com/en-us/learn/modules/control-access-to-azure-storage-with-sas/4-exercise-use-shared-access-signatures)

## Use stored access policies to delegate access to Azure Storage
A shared access signature (SAS) is a secure way to give access to clients without having to share your Azure credentials. This ease of use comes with a downside. Anyone with the correct SAS can access the file while it's still valid. **The only way you can revoke access to the storage is to regenerate access keys**. Regeneration requires you to update all apps that are using the old shared key to use the new one. **Another option is to associate the SASs with a stored access policy**.

![](https://docs.microsoft.com/en-us/learn/modules/control-access-to-azure-storage-with-sas/media/5-shared-acces-policy.png)

### Create stored access policies
You can create a stored access policy with C# code by using the Azure portal or Azure CLI commands.

#### With C# .NET code
```C#
BlobSignedIdentifier identifier = new BlobSignedIdentifier
{
    Id = "stored access policy identifier",
    AccessPolicy = new BlobAccessPolicy
    {
        ExpiresOn = DateTimeOffset.UtcNow.AddHours(1),
        Permissions = "rw"
    }
};

blobContainer.SetAccessPolicy(permissions: new BlobSignedIdentifier[] { identifier });
```

#### With the portal
![](https://docs.microsoft.com/en-us/learn/modules/control-access-to-azure-storage-with-sas/media/5-add-a-policy.png)

#### With Azure CLI commands
```Azure CLI
az storage container policy create \
    --name <stored access policy identifier> \
    --container-name <container name> \
    --start <start time UTC datetime> \
    --expiry <expiry time UTC datetime> \
    --permissions <(a)dd, (c)reate, (d)elete, (l)ist, (r)ead, or (w)rite> \
    --account-key <storage account key> \
    --account-name <storage account name> \
```

## Exercise - Use stored access policies to delegate access to Azure Storage
[More details](https://docs.microsoft.com/en-us/learn/modules/control-access-to-azure-storage-with-sas/6-exercise-use-stored-access-policies)

# Upload, download, and manage data with Azure Storage Explorer
In this module, you'll learn about the features of Azure Storage Explorer, how to install it, and what it can connect to. Finally, you'll use Storage Explorer to connect to Azure Cosmos DB and Data Lake, to create a database and upload data.

By the end of this module, you'll know how to use Storage Explorer to manipulate data in multiple Azure services.

## Connect Azure Storage Explorer to a storage account
### What is Storage Explorer?
