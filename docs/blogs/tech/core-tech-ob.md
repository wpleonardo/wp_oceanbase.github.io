﻿---
slug: core-tech-ob
title: 'Decoding the Core Technologies of OceanBase Community Edition 4.x'
---

In the digital age, data is growing exponentially across industries. In the midst of increasing computational complexity, unlocking the value of massive data is a driving force for innovation. Against this backdrop, databases, the foundation of data processing, are confronting shifts in the market. On one hand, conventional solutions such as centralized databases and sharding struggle to rise to the challenges of massive data, with issues such as performance bottlenecks, insufficient analytical capabilities, and high costs becoming increasingly evident. On the other hand, distributed databases, with their inherent advantages in automatically distributing data across multiple nodes and allowing read/write operations on the cluster data from any node, along with features such as maintaining strong transactional consistency, are becoming the next-generation data management solutions.

This article summarizes a presentation from the database forum of the Global Internet Architecture Conference (GIAC), delivered by Zheng Xiaofeng, a technical expert who leads the open source initiatives of OceanBase Database in South China. Focusing on OceanBase Database, a native distributed database, this article discusses a data management solution from its architecture to its features, community editions, ecosystem tools, future versions, and roadmap.

![1691646145](/img/blogs/tech/core-tech-ob/image/1691646145286.png)

When it comes to distributed databases, we naturally think of large-scale application scenarios. OceanBase Database was born in such a scenario. In 2010, when conventional relational databases struggled to support Taobao Favorites, OceanBase Database V0.1 was rolled out as a distributed storage solution. At the time, NoSQL was popular. We tried separating the storage layer but found that relational databases with a loosely coupled design incurred significant efficiency overheads and thus were unsuitable for transaction processing (TP) scenarios where low latency was required.

To resolve this issue, we implemented an integrated architecture in OceanBase Database V1.0, which has also been used in versions 2.0, 3.0, and 4.0. The architecture was simple, with the storage, SQL, and transaction engines integrated into a single OBServer node. As OceanBase Database gained more external users, we found that they usually used more than one database. For example, a user might use MySQL and Oracle at the same time. Therefore, we added compatibility with Oracle on top of multitenancy during the commercialization of OceanBase Database V2.0. In OceanBase Database V3.0, we further enhanced its compatibility and performance.

In June 2021, we open-sourced OceanBase Database V3.1 when the latest enterprise edition was OceanBase Database V3.2. For OceanBase Database V3.x, community editions differ from enterprise editions in performance. For OceanBase Database V4.0, which has an integrated architecture for standalone and distributed modes, the kernel features of the community edition in MySQL mode are the same as those of the enterprise edition in MySQL mode.

![1691646186](/img/blogs/tech/core-tech-ob/image/1691646186227.png)  

![1691646209](/img/blogs/tech/core-tech-ob/image/1691646209339.png)

The name of the integrated architecture for standalone and distributed modes in OceanBase Database V4.x has two meanings. On one hand, it indicates that the architecture supports both standalone and distributed deployments. On the other hand, it signifies that a tenant using standalone resources is in standalone mode even if the tenant is in a distributed OceanBase cluster. During this process, OceanBase Database can seamlessly switch between standalone and distributed modes.

In addition to the standalone and distributed modes, OceanBase Database can also be deployed in primary/standby mode. In OceanBase Database V4.1, the primary and standby OceanBase clusters, which transmit archive logs through Alibaba Cloud Object Storage Service (OSS) or Network File System (NFS) for synchronization, are similar to primary and standby MySQL databases. Compared to the distributed mode, the primary/standby mode has the risk of data loss because it does not guarantee a recovery point objective (RPO) of zero. In this case, a three-replica deployment can meet your need for high availability and scalability. Multitenancy in OceanBase Database facilitates cluster management in large-scale business scenarios. For example, if a company has tens of thousands of servers but a handful of database administrators (DBAs), each DBA has to manage thousands of database instances. With multitenancy, many MySQL instances can be integrated into an OceanBase cluster, greatly reducing the number of instances that need to be managed.

Throughout the process, the deployment modes of OceanBase Database are tailored to business needs so that companies only need to pay for the features they use.

![1691646239](/img/blogs/tech/core-tech-ob/image/1691646238911.png)

  
![1691646264](/img/blogs/tech/core-tech-ob/image/1691646264872.png)

With its integrated architecture for standalone and distributed modes, OceanBase Database is compatible with the classic mode and supports both TP and analytical processing (AP). Additionally, OceanBase Database achieves strong consistency, zero data loss, high availability, and seamless scalability through full data verification.

In terms of the architecture, instead of using Storage Area Network (SAN) storage devices that require a dedicated network, OceanBase Database can be deployed in a shared-nothing cluster based on general servers and IDC networks. The cluster automatically manages the allocation and dynamic balancing of computing and storage resources, and supports auto scaling, which allows the linear increase of the read and write performance. SQL execution and data storage are supported on all service nodes, and partition replicas are autonomously managed by each node. The cluster runs only one type of database service processes, without depending on external services, which facilitates O&M management. OceanBase Database provides unified database services to applications, supports global indexes, and ensures the atomicity, consistency, isolation, and durability (ACID) properties of transactions. This means it performs as a standalone system in the development of applications. OceanBase Database adapts to various infrastructures, supporting flexible deployment modes such as three IDCs in the same city, three IDCs across two regions, and five IDCs across three regions.

In terms of features such as native distributed capabilities, scalability, single-node performance, hybrid transaction/analytical processing (HTAP), low costs, and multitenancy, their technical principles are as follows:

*   **Native distributed capabilities**: As we all know, a distributed database features high scalability and availability. In terms of high scalability, OceanBase Database leverages the Paxos consensus protocol and native capabilities for seamless horizontal and vertical scaling, improving resource utilization and reducing costs. In terms of high availability, in addition to strong consistency among multiple replicas, OceanBase Database also verifies consistency between replicas, consistency between transaction commits, and disk data to ensure high data reliability.
*   **Seamless scaling**: After you add OBServer nodes, the cluster automatically migrates data from old nodes to new ones. The entire process is transparent to applications. In Ant Group, the largest archive database stores petabytes of data. With multi-node copying at 500 MB/s, TB-level migration takes just a few hours. Take the Double 11 shopping festival as an example. Each year, Ant Group applies for a set of cloud servers half a month before the Double 11 shopping festival to distribute user data to IDCs in more zones to handle the sharp traffic spike. During the migration, read-only replicas are copied first, followed by a leader switch in seconds. After the Double 11 peak, resources are reclaimed. This is how OceanBase Database ensures highly flexible scalability.
*   **Single-node performance comparable to the performance of a standalone database**: Distributed databases typically trade single-node performance for horizontal scalability. However, in online transaction processing (OLTP) business, increased latency in processing individual transactions is often unacceptable. In many scenarios, replacing a standalone database with a distributed one requires multiple distributed nodes to maintain the same business scale even without increasing performance, ultimately driving up costs instead of reducing them.

OceanBase Database adopts an integrated architecture for standalone and distributed modes. When deployed in standalone mode, it is comparable to a standalone database and even outperforms some popular open source ones.

*   When deployed with three replicas in three IDCs, OceanBase Database provides higher availability than a conventional database that delivers the same performance in primary/standby mode.
*   OceanBase Database supports linear vertical scaling by allowing you to upgrade the node specifications.
*   OceanBase Database supports linear horizontal scaling by allowing you to deploy more nodes in each zone.

In the following three cases, OceanBase Database incurs no multi-node access overhead for queries and transactions:

*   When an SQL statement involves only partitions on a single node, no network is required for data reads and writes.
*   When a transaction involves only partitions on a single node, no distributed protocol overhead is incurred by transaction commits.
*   When a transaction involves only partitions on a single node, no remote access to Global Timestamp Service (GTS) is required for reading consistent snapshots based on multiversion concurrency control (MVCC).

Such an integrated architecture allows OceanBase Database to grow with the business and be adaptable to the needs of different customers, including small personal sites, core banking systems, and giant e-commerce platforms.

For a distributed database in OLTP scenarios, 80% of reads and writes are single-node while 20% of reads and writes are cross-node. Our goal with the integrated architecture is to ensure strong performance for the 80% single-node transactions while improving efficiency for the remaining 20% cross-node transactions. OceanBase Database V3.x pre-partitions data, with each partition having its own log stream. The larger the number of log streams, the higher the CPU and memory consumption. In a distributed scenario that involves the two-phase commit (2PC) protocol or the Paxos consensus protocol for distributed transactions, transaction atomicity and durability rely on multiple log streams. This incurs more system overhead than a single log stream in standalone mode.

In OceanBase Database V4.x, multiple log streams are merged into one, which greatly reduces the system load. This does not prevent a cluster from migrating data to another log stream during scaling for even data distribution. The migration process is fully automated and transparent to applications.

In addition to merging log streams, we have also made other optimizations, such as reducing the overhead of the sys tenant, offering parallel capabilities on a single node, and enabling on-demand metadata loading in memory, to boost the single-node performance of OceanBase Database. In our comparison of OceanBase Database and MySQL on a 32-core server, OceanBase Database significantly outperforms MySQL.

![1691646364](/img/blogs/tech/core-tech-ob/image/1691646364695.png)

Another test demonstrates the strong vertical scalability of OceanBase Database. In a sysbench stress test where CPU resources were doubled, the single-node performance of OceanBase Database in queries per second (QPS) increased proportionally, fully utilizing the added hardware.

![1691646408](/img/blogs/tech/core-tech-ob/image/1691646408458.png)

*   **HTAP**: Business scenarios of enterprise applications can be roughly classified into two types: OLTP and online analytical processing (OLAP). Large enterprises tend to deploy multiple database products to support OLTP and OLAP scenarios separately. This solution requires data to flow between different systems, causing latency and the risk of data inconsistency during data synchronization and leading to data redundancy among various systems. This inevitably drives up costs and hinders fast business adjustments in a competitive market.

Therefore, we want OceanBase Database to support both TP and AP workloads in lightweight real-time analysis scenarios. An OceanBase cluster typically has three replicas. By default, the leader processes strong-consistency reads and writes, ensuring that TP and AP tasks are performed on the same set of data. The integrated architecture for standalone and distributed modes also allows you to configure flexible settings such as read/write splitting to suit different business needs. After migrating from the sharding solution of MySQL to an OceanBase cluster, one of our customers has reduced their total cost of ownership (TCO) by 35% and increased their AP capability by 30%. This fully demonstrates the excellence of OceanBase Database in HTAP.  

*   **Low costs and high compression ratio**: Data compression is key to the reduction of storage space for massive data. OceanBase Database implements a distributed storage engine featuring a high compression ratio. Thanks to the adaptive compression technologies, this LSM-tree-based storage engine balances the system performance and compression ratio in a creative manner, which is impossible in a conventional database that stores data in fixed-size chunks. Moreover, data and logs are separately stored in the system to further reduce the storage costs. The LSM-tree-based storage engine greatly reduces the storage costs by using encoding and compression technologies.

How does OceanBase Database help reduce costs? It starts with saving server and storage resources. Based on our experience, in large-scale scenarios, migrating from MySQL to OceanBase Database reduces the number of required servers with the same specifications. As the storage engine of OceanBase Database organizes data in hybrid row-column storage, OceanBase Database V4.x stores approximately five times the total data volume of MySQL per replica, although the exact ratio may vary depending on user data characteristics.

*   **Multitenancy and resource isolation**: Pools are a key approach to fine-grained database resource management in the cloud era. OceanBase Database adopts a native multitenant architecture, which supports multiple tenants in one cluster. Data and resources of a tenant are isolated from those of other tenants, and tenant resources are uniformly scheduled within the cluster. Users can create MySQL or Oracle tenants and configure the number, type, and storage location of data replicas as well as computing resources for each tenant. Database cluster management features are integrated on a platform, application databases are integrated into multiple tenants, and online scaling and configuration adjustment are supported to facilitate automatic O&M.

OceanBase Database V4.1, released two months ago, introduces the following new features:

*   **Direct load is supported to speed up batch imports of massive data**, OBKV and multi-model data types such as GIS are available, and compatibility with MySQL 8.0 is enhanced.
*   **Primary/standby clusters are supported for stability**. Primary/standby clusters in OceanBase Database V4.1 are based on Alibaba Cloud OSS and NFS. We plan to implement primary/standby clusters based on network transmission in the future.
*   **A GUI installation tool is integrated for ease of use**. This allows you to deploy lightweight OCP Express and install an OceanBase cluster with a few clicks.
*   **Performance is improved**. Compared to OceanBase Database V4.0, OceanBase Database V4.1 improves TP performance by 40% and AP performance by 15% in scenarios where 4-core servers are used.

![1691646610](/img/blogs/tech/core-tech-ob/image/1691646610454.png)

![1691646625](/img/blogs/tech/core-tech-ob/image/1691646625403.png)

Additionally, the OceanBase team will collaborate with more ecosystem partners to offer comprehensive and seamless services before, during, and after database migration. We have provided several tools in the OceanBase ecosystem, and the most popular ones are OceanBase Developer Center (ODC), OceanBase Migration Service (OMS), and OceanBase Cloud Platform (OCP). We have also released and open-sourced lightweight OCP Express Community Edition with open APIs early this year. After deploying the service, you can access existing data management platforms through the open APIs to improve O&M efficiency and support business growth.

In MySQL mode, OceanBase Database Community Edition 4.x offers the same features and capabilities as OceanBase Database Enterprise Edition 4.x. Even though OceanBase Database V4.x shows a clear performance boost over OceanBase Database V3.x, we're still focused on optimizing the kernel. In TP scenarios, we are improving the performance of OceanBase Database on small-specification servers, in an attempt to enable a single-node OceanBase Database instance to run faster than a single-node MySQL instance, so that OceanBase Database can meet user demands regardless of whether a small-specification server or a large cluster is used. In AP scenarios, we strive to implement more features, such as separation of hot and cold data and support for read-only external tables, to suit diverse business needs.

We hope that OceanBase Database can help more enterprises overcome their business bottlenecks and that more users can build the OceanBase community with us.