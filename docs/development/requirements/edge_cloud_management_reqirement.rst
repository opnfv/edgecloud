.. This work is licensed under a Creative Commons Attribution 4.0 International License.
   .. SPDX-License-Identifier: CC-BY-4.0
      .. (c) Qihui Zhao China Mobile and other contributors

========================================
Edge Cloud Management Requirements
========================================

Edge services and deployment scenario
=========================================

+-------------------------+--------------------------+
| Edge Service            | Deployment Scenario      |
+-------------------------+--------------------------+
| Industrial application  | On-prem                  | 
+-------------------------+--------------------------+
| V2X                     | Network edge/ on-prem    | 
+-------------------------+--------------------------+
| AR/VR                   | Network edge/ on-prem    | 
+-------------------------+--------------------------+
| IoT analysis            | on-prem                  | 
+-------------------------+--------------------------+
| Cloud gaming            | Network edge             | 
+-------------------------+--------------------------+
| CDN                     | Network edge             | 
+-------------------------+--------------------------+
| IoT control             | On-prem/ network edge    | 
+-------------------------+--------------------------+

Note: 

"On-prem" refers to deployment scenarios in which service being deployed in places like industrial park; “Network edge” refers to  deployment scenarios in which service could be deployed in operators’ edge cloud.


Problems of providing edge cloud services
===========================================

Telco operators, also as edge cloud service providers, are facing the following problems when providing edge cloud services:

**Multiple types of cloud & remote deployment**

a) Edge resource type: VMs, containers, bare metals;

b) Widespread edge sites, hard to do deployment and installation.

**Incompatible cloud solutions from multiple cloud vendors or open source community**

a) Tens of thousands of edge sites could use IaaS from multiple vendors;

b) Different cloud solutions are incompatible to each other to some degree;

c) Inconvenient for edge cloud users if using different vendors’ edge IaaS directly.

**Lack of unified & regional-centralized operation and management**

a) Impossible to provide local O&M for each edge site (high cost, poor efficiency).

**Support both IT and CT applications**

a) Complicate for IT application to follow MANO processes;

b) Some IT applications have their own service management system;

c) Resource orchestration is enough for IT application or they don’t need to be orchestrated.


Requirements of providing edge cloud services
=================================================

**Unified management for multiple resource types**

a) Provide VM, container, bare metal resources;

b) Provide access management (connection management) and auto deployment for all edge resource pools.

**Standard resource & service interface**

a) Shield difference of multi-vendor products;

b) Provide standard and stable service, resource, APIs for edge cloud users (including end users & systems);

c) Simple deployment process for both IT & CT applications.

**Unified & Centralized O&M**

a) Centralized fcaps management;

b) Standard O&M APIs  for other systems to subscribe O&M service;

c) Unified & automatic O&M entrance to provide localized O&M.



