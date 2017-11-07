# GCP Best Practices

![GCP ](static/gcp.png "GCP Best Practices")

## Index

* [Introduction](#introduction)
* [Services](#services)
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
        * [Genomics](#genomics)
        * [Dataprep](#dataprep)
* [References](#references)

---

## Introduction

TBD

---

## Services

TBD

---

### Compute

TBD

---

#### App Engine

TBD

---

#### Compute Engine

TBD

---

#### Container Engine

TBD

---

#### Cloud Functions

TBD

---

### Storage

TBD

---

#### Bigtable

TBD

---

#### Datastore

TBD

---

#### Storage

TBD

---

#### SQL

TBD

---

#### Spanner

TBD

---

### Networking

Cada proyecto Cloud Platform dispone de serie de 5 redes independientes. Los recursos de un proyecto pueden organizarse usando estas redes, incluso si están distribuidos en distintas regiones, ya que las IPs que manejan son globales. En caso de ser necesario, la cuota de redes puede aumentarse hasta un número de 15 por proyecto.

El tráfico de red de entrada a las instancias de VMs y balanceadores de carga pueden configurarse a través de reglas de firewall para, por ejemplo, gestionar el escalado de los clústeres. Por otro lado, para controlar el tráfico de red de salida deben usarse rutas. A modo de ejemplo, con las rutas puede derivarse todo el tráfico a un rango de IPs corporativo a través de una VPN. Si se necesita un grano de control que llegue a nivel de puerto, deben configurarse las iptables a nivel de instancia de VM.

Cada nueva instancia de VM adquiere una dirección IP interna que puede usar para comunicarse con otras instancias de la misma red. Se puede adjuntar, además, una dirección IP pública efímera o estática para habilitar en la instancia el acceso a recursos externos a la red, incluidas las API de Cloud Platform; en este caso, lo ideal es limitar el tráfico entrante en estas instancias haciendo uso de reglas de firewall.

Si se requiere que las instancias sean exclusivamente internas, es necesario configurar un Proxy NAT para que puedan acceder a internet. El acceso vía SSH a estas instancias de VM no puede hacerse de manera directa; se hará a través de una instancia de VM bastionada propiamente configurada.

Las subredes también pueden ayudarnos a organizar las instancias de VMs de un proyecto. Las subredes son rangos de IPs no públicos, por lo que no dependen de una red principal común. Al igual que las redes, pueden aprovecharse del enrutado del tráfico mediante el uso de reglas de firewall y rutas.

Ciertos recursos de Cloud Platform pueden crearse en regiones y zonas específicas, como pueden ser instancias de VMs de Compute Engine, aplicaciones de App Engine o datasets de BigQuery. Es importante tener esto en cuenta, ya que las regiones y zonas representan ubicaciones geográficas concretas, y un buen diseño de distribución de recursos puede lograr una redundancia de servicio adecuada o minimizar la latencia en los accesos a éstos.

---

### Stackdriver

TBD

---

#### Monitoring

TBD

---

#### Debug

TBD

---

#### Trace

TBD

---

#### Logging

TBD

---

#### Error Reporting

TBD

---

### Tools

TBD

---

#### Container Registry

TBD

---

#### Source Repositories

TBD

---

#### Deployment Manager

TBD

---

#### Endpoints

TBD

---

### Big Data

TBD

---

#### BigQuery

TBD

---

#### Pub/Sub

TBD

---

#### Dataproc

TBD

---

#### Dataflow

TBD

---

#### ML Engine

TBD

---

#### Genomics

TBD

---

#### Dataprep

TBD

---

## References

* [Best Practices for Enterprise Organizations](https://cloud.google.com/docs/enterprise/best-practices-for-enterprise-organizations)

---

[BEEVA](https://www.beeva.com) | Technology and innovative solutions for companies
