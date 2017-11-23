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

Google Cloud Functions is a **serverless compute service** that allows to write business logic for building and connecting cloud services. This solution grants Functions as a Service (FaaS) what abstracts developers completely from managing any server or runtime environment and being free of any system maintenance. This benefit is translated into elastic scalability without worrying about provisioning any infrastructure.

In its beta release, the only supported language for writing code is JavaScript and executes in a standard Node.js runtime environment. The powers of npm and package.json can be used to resolve dependencies.

**Connect and extend cloud services**. It has the feature of being able to integrate with the rest of Google services through a connective layer of logic allowing to develop use cases with event-drive code. 

**Service execution in response to events**. Configure the functions as a response of different cloud events with a trigger. Nowadays, Cloud Functions supports events form HTTP, Cloud Storage, Cloud Pub/Sub, Firebase (DB, Storage, Analytics, Auth) and Stackdriver Logging.

**Microservices over monoliths**. As much as you want to speed up your development, this service helps to compile systems composed of small and independent units of code whose functionality focuses on doing a single task efficiently. Instead of having to deploy applications as monoliths, you can create services in a single function to offer a new solution in form of microservices.

**Serverless economics**. Cloud Functions are invoiced on demand based on the execution time (down to 100 milliseconds), therefore you will pay as long as the function is active.

Due to the beta release, there are some restrictions to take into account:
- The API might be changed in backward-incompatible ways and is not subject to any SLA or deprecation policy.
- There is only one option of region to choose: us-central1.
- Google Cloud Functions offers the feature of a Cloud Functions Local Emulator that allows to deploy, run and debug the cloud functions on your local machine. 
    - Google do not recommend the use of this feature for production.
    - Those functions deployed in the Local Emulator whose triggers are non-HTTP (like Pub/sub or Storage) will not be invoked by the services. As long as the purpose of the Emulator is as a development environment, if you want to invoke these functions, you have to call them manually.  

Best practices:
- Avoiding the continuous running of a function and the possibility to be forcibly terminated by the system. There are two approaches depending on the kind of the function. For HTTP functions, the solution is calling a termination method like send(), json() or end(), once the function has been completed. On the other hand, for background functions, you could either invoke the callback argument or return a Promise when the function has finished. 
   
- In order to resist the temporary errors of the code, there is an option of "Retry on failure" included in the cloud functions. By default it is disabled and if the function fails there is no attempt to execute it again. Enabling this option, if for any reason, including code errors, the function fails, it will try again for a maximum of seven days. 

By last, there are several warnings that are good to know:
- Those functions written by the Firebase SDK can not be deployed using the gcloud command-line tools and vice versa.
- If the project ID contain a colon, functions with HTTP(S) triggers are not supported.
- When a project is deleted, the whole work of this project will too and the project ID can not ever be used again.

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

#### Storage

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
