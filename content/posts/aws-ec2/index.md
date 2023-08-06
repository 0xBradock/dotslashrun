---
title: "Aws Ec2"
slug: "Aws Ec2"
description: "Aws Ec2"
date: 2023-01-15T16:22:13+01:00
draft: true
editURL: https://github.com/Kmelow/dotslashrun/issues
tags: ["AWS", "EC2"]
---

> Amazon Elastic Compute (EC2) is a compute platform - 🌱 Local Service

Configuration parameters:

- OS
- Memory (RAM)
- Space (Network EBS &EFS or Hardware EC2)
- Network
- Firewall
- Bootstrap script (configure at launch): Automate boot tasks (install updates, sw, dw common files) and runs with the root user

## Categories

| Category                  | Initials         | Accronym |
|---------------------------|------------------|----------|
| **General Purpose**       | `A`, `T` and `M` | gepatm   |
| **Compute Optimized**     | `C`              | coc      |
| **Memory Optimized**      | `R`, `X` and `z` | morxz    |
| **Storage Purpose**       | `H`, `I` and `D` | stohid   |
| **Accelerated Computing** | `P`, `G` and `F` | accopfg  |

[Types](https://aws.amazon.com/ec2/instance-types/) of instances, `m5.2xlarge`:

- `m`: Instance class
- `5`: Generation
- `2xlarge`: Size of the instance class (memory)

- Compute Optimized `C*`: High performance processors, batch workloads, transcoding, modeling, machine learning, etc.
- Memory Optimized `R*`: Fast data processing, SQL, NoSQL, In-memory dbs, real-time
- Storage Optimized ``: Storage intensive with high read and write operations, OLTP, Cache, Warehouse, distributed systems

### Security Groups

> Act as a firewall on EC2 that regulate inbound and outbound trafic

It sits in front of a service (i.e. EC2) and it can be attached to multiple instances and can also reference other Security Groups

They regulate:

- Access to ports
- Authorized ranges
- Inbound (by default blocks all)
- Outbound (by default allows all)

## Pricing

{{< alert >}}
Absolute and percentages only give an idea, they vary with time and region
{{< /alert >}}

Different tiers of price and availability:

| Tier                 | Description                                 | Price/h                                 | Recommended for                                    |
|----------------------|---------------------------------------------|-----------------------------------------|----------------------------------------------------|
| On-Demand            | Pay by second                               | $ 0,10                                  | short term, spiky, unpredictable                   |
| Reserved             | Commit to use (1/3 years)                   | Up to 72% discount (pay upfront or not) | Steady usage applications                          |
| Savings Plans        | Commit to a type of usage (usage hr / year) | Up to 72% discount                      | Long workloads                                     |
| Dedicated Host       | Book entire physical server                 | Most expensive (demand / reserved)      | Compliance, server-bound licenses                  |
| Dedicated Instance   | No sharing hardware                         |                                         |                                                    |
| Capacity Reservation | Reserve instance                            |                                         | Uninterrupted workloads, need to be in specific AZ |
| *Spot                | Suggest a price and lose if price goes up   | Up to 90% discount                      | Batch jobs, data analysis, img processing          |

## Placement Group

- Cluster: Single AZ in same hardware, allows low latency and high bandwidth (high risk)
- Spread: Spread accross AZs (7 max per AZ) in different hardware, maximize availability and minize risk and isolate failure
- Partition: Spread accross different partitions (Hadoop, Cassandra, etc.)

## Storage Volumes

### EBS

> Elastic Block Store (EBS) is a **network drive** attached to an instance (can be multi-attached) bounded to an AZ

Allows to persist data even after termination.
It can be snapshotted, attached, detached and created independently from instances.

Features:

- Archive: Send to an archive, which is cheaper to store, but it takes 1-3 days to restore
- Recycle Bin: Possibility to restore even if deleted (accidentally)
- Fast Snaphot Restore (FSR): Force no latency on creation ($$$)

Types according to size, throughput and IOPS:

- *gp2 / gp3 (SSD): General purpose SSD cost effective and low-latency. Used for system boot, virtual desktops and dev environment
  - gp3: Is a newer generation that have independent IOPS and throughput
- *io1 / io2 (SSD): Highest performance SSD mission-critical low-latency. The olny storage that can be accessed concurrently by more than one instance.
- st1 (HDD): Low cost HDD volume for less frequent access.
- sc1 (HDD): Lowest cost HDD for low workloads. C stands for 'cold', meaning that the storage is intended to use with very low frequency

*Can be used for boot

### AMI

> Is an instance image pre-created

There are community, private and comercial AMIs.
Instances can be launched using an AMI.


### EC2 Instance Store

> Effemeral hardware storage physically attached to the instance

Great for disk performance.
Good for cache, buffer and temporary content

### EFS

> Elastic File System is a managed NFS (network file system) that can be mounten on many EC2 in different AZ at the same time

Use cases are content management, web server, data sharing, wordpress.

Some of the features:

- It is highly available (3x gp2) with pay-per-use subscription
- It uses NFSv4.1 protocol
- Compatible with Linux with a POSIX file system with standard API
- Encryption at rest with KMS
- File system scales automatically, no capacity planning

Performance & Storage Classes:

- 1000s of concurrent NCF clients, up to 10GB/s throughput
- Grow to PetaByte network file system automatically
- Performance modes: General purpose (default) and Max I/O
- Throughput modes: Bursting and provisioned

Storage Classes: standard (frequent access) and infrequent access (EFS-IA) is cost effective.
Concerning availability the options are: standard (multi AZ) great for production and One Zone (EFS OZ AZ-IA) that is great for development.
The later provides up to 90% of discount.
