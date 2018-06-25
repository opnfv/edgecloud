.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. http://creativecommons.org/licenses/by/4.0
.. (c) Open Platform for NFV Project, Inc. and its contributors

============
Introduction
============

This Edge Cloud Requirement Document is used for eliciting Edge Cloud
Requirements of OPNFV. This document will define high-level Edge Cloud goals,
including service reqirements, sites conditions, and translate them into
detailed requirements on NFV components under edge cloud environment. Moreover,
this document can be used as reference for edge cloud testing scenarios design.


===========================
Definitions & Terminologies
===========================

The following terminologies will be used in this document:

**Core site(s)**: Sites that are far away from end users/ base stations,
completely virtualized, and mainly host control domain services (such as telco
services: HSS, MME, IMS, EPC, etc).

**Edge site(s)**: Sites that are closer to end users/ base stations, and mainly
host control and compute services.

**E2E delay**: time of the transmission process between the user equipment and
site. It contains four parts: time of radio transmission, time of optical fiber
transmission, time of GW forwarding, and time of VM forwarding.


============================
Telco related Edge Use Cases
============================

**RAN Services:**  CRAN-CU

CRAN OPNFV project link: https://wiki.opnfv.org/pages/viewpage.action?pageId=20743420

CRAN stands for Cloud Radio Access Network or Centralized Radio Access Network.
The basic concept is to consolidate compute resources to run some radio access
functions virtually and remotely in a datacenter, rather than in a base station.
CU stands for Centralized Unit, which holds the non-real-time protocol processing
function of BBU and supports deployment of some functions of core network. 
Its generic requirements are listed below:

- CRAN-CU has strict timing and performance requirements for signal processing
  to be compliant with RAN standards

- The E2E delay should be less than 3ms, which means it should be deployed as
  close to the users as possible to satisfy the low-latency requirement

- Required bandwidth of CRAN-CU is around 50GB

- Acceleration strategy is recommended for CRAN-CU to implement fast computing
  and forwarding processes. But what kind of acceleration solution should be used is not clear

- No external storage is needed by CRAN-CU. Local storage is enough.

**Gateways:** UPF, SAE-GW, enterprise vCPE

Gateway services like UPE, SAE-GW and enterprise vCPE were used to be deployed
in core sites. They usually do not have strict latency requirements. Latency
requirements of UPF and SAE-GW are both 10ms. Minimum latency required by
enterprise vCPE is around 50ms when transmitting video and voice data. The number
is over a second if transmitting email. So latency requirements can always be
satisfied theoretically. However, as these gateways usually serve over millions
of users (every enterprise vCPE serves more than a thousand enterprise-level users),
they have high throughput: BW of UPF>300GB, BW of SAW-GW is around 50GB, BW of
enterprise vCPE>40GB. Deploying these services separately in edge sites will help to
achieve merging and forwarding data flows locally, offload traffic flow from core
network, and reduce footprints of transmission resources and core computing resources.

**Access technology independent service:** edge CDN

According to China Mobile’s data, every CDN unit can serve over 10 thousand
customers and it requires bandwidth over one hundred GB. In order to reduce
footprints of transmission resources and burden of the network, CDN is recommended
to be deployed in edge sites, but not too close to users if taking deployment cost
into consideration. It also requires fast compute ability to generate content
distribution strategy quickly.

One edge CDN unit does not need to communicate with other edge CDN units at the
same level, but need to be connected with CDN units at higher level for policy
distribution and resource downloading.

**MEC/Edge Computing Services**

Besides requiring low-latency, high-bandwidth, and high reliability, newly
generated edge computing services requires the local deployment environment
to be capable of doing fast compute to support some intelligence functions
like real-time data analysis and optimal path calculation of services like V2X.
The deployment of MEC and third-party applications running on it mainly depends
on latency requirements. uRLLC requires E2E delay less than 3ms, which means it
should be deployed at edge sites close to base station. eMBB requires E2E delay
less than 10ms, which leads to more flexible deployment location.

Unlike traditional telco services which have relatively stable on-line and update
cycle, MEC and the third-party applications have more flexible lifecycle, which
means a more flexible infrastructure should be used such as container.

The table below summarizes the latency and bandwidth requirements of several
typical telco related services:( x in table stands for 1~5)

+---------------+---------+-----------------+----------+----------+-------------------------+----------+
| Service       | CND     | enterprise vCPE | SAE-GW   | 5G-UPF   | MEP                     | CRAN-CU  |
+---------------+---------+-----------------+----------+----------+-------------------------+----------+
| E2E delay     |   10ms  | 50ms            | 10ms     | 10ms     | URLLC<3ms, eMBB<10ms    | 3ms      |
+---------------+---------+-----------------+----------+----------+-------------------------+----------+
| Bandwidth     | >100GB  | 10x GB          | 10x GB   | >300GB   | 10x GB                  | 10x GB   |
+---------------+---------+-----------------+----------+----------+-------------------------+----------+

================
Features of Edge
================


Lighter weight control
======================

As space and power resources are limited in edge sites and edge usually has
fewer number of server (the number varies from a few to several dozens), it is
unnecessary to deploy orchestrator or VNFM. VIM (OpenStack) and SDN would be
deployed in light weight manner to save resources for services. Detailed
functions of light weight VIM and SDN have not been discussed yet, but basic
functions such as VM lifecycle management and automatic network management
should be persisted.

Remote provisioning
===================

As there is no professional maintenance staff except for hardware operators at
edge, remote provisioning should be provided so that virtual resources of
distributed edge sites can obtain unified orchestration and maintenance.
Orchestrator together with OSS/BSS, EMS and VNFM should be deployed remotely
in some central offices to reduce the difficulty and cost of management as well
as increase edge resource utilization ratio. Multi region OpenStack could be
considered as one of the VIM solution.

Resource diversity
==================

With various applications running on edge, heterogeneous resources, including
VM, container and bare-metal could co-exist and form heterogeneous resource pool.
These resources should be managed by edge management components as well as core
orchestration/management components.

Hardware/Software acceleration
==============================

Edge services usually requires strict low latency, high bandwidth, and fast
compute and processing ability. Acceleration technology should be used in
edge to maintain good service performance. OpenStack should fully expose these
acceleration capabilities to services. The usage of different acceleration
technologies (including DPDK, SR-IoV, GPU, Smart NIC, FPGA and etc.) varies
from service to service. 

Related project about acceleration: https://wiki.openstack.org/wiki/Cyborg 

===========================================
Edge Sites Conditions/ Deployment Scenarios
===========================================

Latency and distance to customer are taken as two main characters to separate
different sites. The following figure shows three different sites.

.. image:: ../requirement/images/SitesPlot.png
	:alt: Edge Sites Structure
	:figclass: align-center

Small Edge
==========

- Distance to base station: less than 10km, closest site to end users/ base station

- **E2E delay: around 2ms**

- Maximum bandwidth can provide: 50G

- Minimum server number: 1

- Maximum server number: 20

- Power for a site: < 10KW

- Services might be deployed here: MEC, or other services which have strict
  requirements on latency. Services deployed in this kind of sites have huge
  regional deference

- Physical access of maintainer: Rare, maintenance staff may only show up in
  this kind of site when machines initialize for the first time or a machine down

- Remote network connection reliability: Not 100% reliable or stable

- Orchestration: no orchestration component. MANO deployed in core site provide
  remote orchestration

- Degree of virtualization: it is possible that no virtualization technology would
  be used in small edge site if virtualization increases structure/network complexity,
  reduce service performance, or cost more resources. Bare-metal is common in small
  edge sites. Container would also be a future choice if virtualization was needed

- Storage: mainly consider local storage. Distributed storage would be used depending
  on services’ needs and site conditions.

Medium Edge
===========

- **E2E delay: less than 2.5ms**

- **Distance to base station: 10x km (1<x<8)**

- Maximum bandwidth can provide: 100G

- Server number: 20 ~ 100

- Power for a site: 10 KW<power<20 KW

- Services might be deployed here: MEC, RAN, CPE, etc.

- Physical access of maintainer: Rare

- Remote network connection reliability: Not 100% reliable or stable

- Orchestration: no orchestration component. MANO deployed in core site
  provide remote orchestration.

- Degree of virtualization: depends on site conditions and service requirements.
  VM, container may form hybrid virtualization layer. Bare-metal is possible in
  middle sites

- Storage: local storage and distributed storage, which depends on site conditions
  and services’ needs

Large Edge
==========

- E2E delay: around 4ms

- Distance to base station: 100x km (0.8<x<3)

- Maximum bandwidth can provide: 200G

- Server number: 100+

- Power for a site: 10x KW (2<x<9)

- Services might be deployed here: CDN, SAE-GW, UPF, CPE and etc., which have
  large bandwidth requirements and relatively small latency requirements

- Physical access of maintainer: professional maintainer will monitor the site

- Remote network connection reliability: reliable and stable

- Orchestration: no orchestration component. MANO deployed in core site provide
  remote orchestration

- Degree of virtualization: almost completely virtualized in the form of VMs
  (if take CDN into consideration, which may not be virtualized, the virtualization
  degree would decrease in sites with CDN deployment)

- Storage: distributed storage

==============
Edge Structure
==============

Based on requirements of telco related use cases and edge sites conditions,
the edge structure has been summarized as the figure below.

.. image:: ../requirement/images/EdgeStructure.png
	:alt: Edge Structure
	:figclass: align-center

=========================================
Requirements & Features on NFV Components
=========================================

Hardware
========

Customized server would be possible for edge because of limited space and power.

More derails: TBD

Acceleration
============
Hardware acceleration resources and acceleration software would be necessary for edge.

More details:TBD

OpenStack
=========
Edge OpenStack would be in hierachical structure. Remote provisioning like
multi-region OpenStack would exist in large edge sites with professional
maintenance staff and provide remote management on several middle/small
edge sites. Middle and small edge sites would not only have their own resource
management components to provide local resource and network management, but
also under the remote provisioning of OpenStack in large edge sites.

.. image:: ../requirement/images/Layer.png
	:alt: Hierachical OpenStack
	:figclass: align-center

For large edge sites, OpenStack would be fully deployed. Its Keystone and Horizon
would provide unified tenant and UI management for both itself and remote middle
and small edge sites. Middle edge sites would have OpenStack with neccessary
services like Nova, Neutron and Glance. Small edge site, which has server number
less than 20, would use light weight OpenStack.

More detalis: TBD

SDN
===
TBD

Orchestration & Management
==========================

Orchestration and VNF lifecycle management: NFVO, VNFM, EMS exist in core cloud
and provide remote lifecycle management.

More details: TBD

Container
=========
VM, container and bare-metal would exist as three different types of
infrastructure resources. Which type of resources to use depends on services’
requirements and sites conditions. The introduction of container would be a
future topic.
