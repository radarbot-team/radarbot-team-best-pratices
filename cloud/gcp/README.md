# GCP Best Practices

![GCP ](static/gcp.png "GCP Best Practices")

## Index

* [Introduction](#introduction)
* [Services](#services)
    * [IAM](#iam)
    * [APIs](#apis)
    * [Compute](#compute)
        * [App Engine](#app-engine)
        * [Compute Engine](#compute-engine)
        * [Container Engine](#container-engine)
        * [Cloud Functions](#cloud-functions)
    * [Storage](#storage)
        * [Bigtable](#bigtable)
        * [Datastore](#datastore)
        * [Storage](#storage)
        * [SQL](#sql)
        * [Spanner](#spanner)
    * [Networking](#networking)
    * [Stackdriver](#stackdriver)
        * [Monitoring](#monitoring)
        * [Debug](#debug)
        * [Trace](#trace)
        * [Logging](#logging)
        * [Error Reporting](#error-reporting)
    * [Tools](#tools)
        * [Container Registry](#container-registry)
        * [Source Repositories](#source-repositories)
        * [Deployment Manager](#deployment-manager)
        * [Endpoints](#endpoints)
    * [Big Data](#big-data)
        * [BigQuery](#bigquery)
        * [Pub/Sub](#pub-sub)
        * [Dataproc](#dataproc)
        * [Dataflow](#dataflow)
        * [ML Engine](#ml-engine)
        * [Dataprep](#dataprep)
        * [Datalab](#datalab)
        * [Data Studio](#data-studio)
* [References](#references)

---

## Introduction

With Google Cloud Platform (GCP) you can build, test, and deploy applications
highly-scalable, fault-tolerant and reliable infrastructure for your
web, mobile, and backend solutions.

The platform covers services like IaaS (Infrastructure as a Service),
PaaS (Platform as a Service), SaaS (Software as a Service) and FaaS
(Functions as a Service), being examples of these, Compute Engine, App
Engine, Stackdriver and Cloud Functions.

It's divided in nine big areas, and covered the following topics,
IAM, APIs, Compute, Storage, Networking, Operations (Stackdriver), Tools and
Big Data.

The structure of GCP is composed by projects and resources (included in projects).

---

## Services

### IAM

TBD

---

### APIs

TBD

---

### Compute

TBD


#### App Engine

TBD


#### Compute Engine

TBD


#### Container Engine

TBD


#### Cloud Functions

TBD

---

### Storage

All applications need persistent and durable storage to accomplish their
purpose, also vary in their storage requirements.
So Google Cloud Platform offers different persistent storage services.

Every kind of storage provided different capacities and covered distinct use cases

|          | Cloud Storage | Cloud SQL | Cloud Spanner | Datastore | Bigtable  |
| -------- |:-------------:|:---------:|:-------------:|:---------:| ---------:|
| Capacity | Petabytes +   | Gigabytes | 1000s+ nodes  | Terabytes | Petabytes |
| Usage    | Store blobs   | No-ops SQL database on the cloud | Strong consistency. High Availability | Structured data from AppEngine apps | No-ops, high throughput, scalable, flattened data |

Each service provide ranges of scaling, performance, and data structure characteristics.
Variations within each service complicate things. For example, Cloud Storage stores large objects.
However, one type of Cloud Storage is good for streaming video,
while another type is intended to archive data that will be accessed no more than once a year.

#### Cloud Storage

Google Cloud Storage is the object storage service to files, from file service to analytics/ML and to backup information for near and longtime periods.

Includes features like lifecycle management, versioned, deletion policies, scalability, durability, availability and consistency, and a price that adapts to the needs of each client or business.

Each object is grouped in Buckets, and is important to apply an access policy for the different objects.

##### Storage class

|                 | Regional       | Multi-Regional | Nearline | Coldline |
| --------------- |----------------|----------------|----------| ---------|
| Design Patterns | **Data that is used in one region** or needs to remain in region | **Data that is used globally** and has no regional restrictions | **Backups** Data that is accessed no more than once a month | Archival or **Disaster Recovery (DR)** data that is accessed once a year or less often |
| Feature         | Regional       | Geo-redundant  | Backup   | Archived or DR |
| Availability    | 99.9%          | 99.95%         | 99.0%    | 99.0% |
| Durability      | 99.9999999999% | 99.9999999999% | 99.9999999999% | 99.9999999999% |
| Duration        | Hot data       | Hot data       | 30 day minimum | 90 day minimum |
| Retrieval cost  | none           | none           | $        | $$ |


#### Cloud SQL

TBD

#### Cloud Spanner

TBD

#### Datastore

TBD

#### Bigtable

TBD

The following link shows all different options to storage in more detail: https://cloud.google.com/storage-options/

### Networking

Each Cloud Platform project has 5 independent networks. The resources within a project can be organized using these networks, even if they are distributed in different regions, since the IPs they manage are global. If necessary, this network quota can be increased to 15 per project.

Incoming network traffic to the VM instances and the load balancers can be configured through firewall rules to, for example, manage the clusters autoscaling. Also, routes must be used to control the outgoing network traffic. As an example, you can use routes to derive all the traffic to a specific range of corporate IPs through a VPN. If a low level control is a must, you can configure the iptables in every single VM instance.

Each new VM instance acquires an internal IP address that it can use to communicate with other instances of the same network. An ephemeral or static public IP address may also be attached to the VM instance to enable access to external resources, including the Cloud Platform APIs; in this case, a best practice is to limit the incoming traffic in these instances by using firewall rules.

If the VM instances are required to be exclusively internal, you must configure a NAT Proxy so that they can access the internet. The access via SSH to these VM instances can not be done directly; a bastion VM instance properly configured must be used instead.

Subnets can also help us organize the project VM instances. Subnets are ranges of non-public IPs, so they don't depend on a main common network. Like networks, they can take advantage of traffic routing by using firewall rules and routes.

Some Cloud Platform resources can be created in specific regions and zones, such as VM instances, App Engine applications and BigQuery datasets. It's important to have this on mind, since regions and zones represent specific geographical locations, and a good design of the resources distribution can achieve an adequate service redundancy or minimize the latency in the accesses to them.

---

### Stackdriver

TBD

#### Monitoring

TBD

#### Debug

TBD

#### Trace

TBD

#### Logging

TBD

#### Error Reporting

TBD

---

### Tools

#### Container Registry

TBD

#### Source Repositories

TBD

#### Deployment Manager

TBD

#### Endpoints

TBD

---

### Big Data

#### BigQuery

TBD

#### Pub/Sub

TBD

#### Dataproc

TBD

#### Dataflow

TBD

#### ML Engine

#### Dataprep

TBD

#### Datalab

TBD

#### Data Studio

TBD

---

## References

* [Best Practices for Enterprise Organizations](https://cloud.google.com/docs/enterprise/best-practices-for-enterprise-organizations)

---

[BEEVA](https://www.beeva.com) | Technology and innovative solutions for companies
