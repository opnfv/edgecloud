.. _opnfv-installation:

.. This work is licensed under a Creative Commons Attribution 4.0 International License.
.. SPDX-License-Identifier: CC-BY-4.0
.. (c) Qihui Zhao China Mobile and other contributors

====================================
**Edge Cloud Test Case Reference**
====================================


**Introduction**
=================

This is a document for limited edge cloud test cases. It can be used as reference for 
edge cloud testing.

Cases cover basic functions of virtualization layer, basic functions of OpenStack, 
reliability, management of multiple OpenStack, and performance.


**Basic Functions of Virtualization Layer**
============================================

**Test case #1: Resource isolation**

**Description**: Resources used by VMs running on the same server should be seperated 
and isolated.

**Preconditions**:none

**Steps**:

1. VM1 and VM2 running on one server

2. Monitering CPU and memory usage rate of these two VMs

3. Login to VM2, add pressure to VM2's memory

4. Check CPU and memory usage rate of VM1 and VM2

**Expected outcome**: Usage rate of VM2's memory increase repidly after adding pressure 
while that of VM1 stays steady

----------------------------------------

**Test case #2: Support control & compute mixed deployment on one server**

**Description**: Edge OpenStack supports control services and compute services to be 
deployed on the same server

**Preconditions**:
An OpenStack system with all-in-one node has been setup, where openstack control processes 
and compute processes running on the same node

**Steps**:

1. Check the processes on an all-in-one node

2. Create a VM on this node

**Expected outcome**:

1. control processes and compute processed such as nove-compute can be found on all-in-one 
node

2. successfully create VM

----------------------------------------

**Test case #3: Local LVM backend**

**Description**: Virtualization layer uses local LVM as the storage backend

**Preconditions**: An OpenStack system with LVM as storage backend has been setup

**Steps**:

1. Check the usable storage capacity

2. Create a volume of 10GB and attech to a VM 

3. Check the usable storage capacity

**Expected outcome**:
The difference of local avaliable storage between step 1 and step 3 is 10GB 


**Functions of OpenStack as VIM**
==================================

**Test case #4: Cold migration**

**Description**: OpenStack support cold migration of VM

**Preconditions**£ºAn OpenStack system with at least two physical server has been setup

**Steps**:

1. Start with a VM running on one node

2. Check the location of this VM, VM ID, IP, and internal data

3. Cold migrate this VM

4. Check the location of this VM, VM ID, IP, and internal data

**Expected outcome**:

1. Notifications like "Succeffuly migrated " can be found

2. After step 4, except for location of this VM, the rest information stays the same as 
those in step 2

----------------------------------------

**Test case #5: Check VIM resouce occupation**

**Description**: Check the resource occupation of hypervisor or OpenStack control services

**Preconditions**: An OpenStack system with control services and compute services deployed 
on the same server has been setup  (control services could be deployed in HA mode, only 
total usage of control services/hypervisor would be check in this case)

**Steps**:

1. Choose an environment using all-in-one deployment 

2. Check the total CPU number of the system

3. On webpage or using "top" command to check the CPU resources used by hypervisor or 
OpenStack control services

4. Create VMs using all the rest CPUs

**Expected outcome**:

1. VMs created successfully

2.  CPU number of HostOS&OVS/DPDK (which could be treated as hypervisor) + CPU number 
of control services + CPU number of VMs = total CPU number

**Reliability of System**
==========================

**Test case #6: Cluster reliability**

**Description**: Verify VIM reliability and reliability of VMs on an server in which 
the OpenStack control services and compute service has been deployed

**Preconditions**:

1. Edge OpenStack has been setup, there are at least two servers in this system

2. VIM is deployed with control services and compute services on the same node and at 
least 2 HA for control services (on different nodes)

3. System is working normally

4. At least one VM is running on each node in this system 

**Steps**:

1. check the primary VIM node (an all-in-one node) and processes running on it; make 
sure there is at least one VM running on it, and VMs running on other node

2. keep ping VMs on other nodes except VMs on primary VIM node

3. Power off this server of primary VIM node

4. Create VM on other node to check whether the function of VIM has been influenced 
by the stop of primary VIM node

5. Check whether the VM used to be on primary VIM node has been migrated to other nodes

6. Check the corresponding alarm within this system

**Expected outcome**:

1. After step 3, communication to VMs which are not on primary VIM node (in step 2) 
is not interrupt

2. VM can be created successfully in step 4

3. In step 5, VM used to be on primary VIM node has been migrated to other nodes

4. In step 6, alarms related to server down can be found within the system

----------------------------------------

**Test case #7: Test VM Operations on isolated edge cloud**

**Description**: If the network between center of edge and edge broke, the edge should 
be able to provide basic VM operation functions (Reliability of multi-cloud system 
under unreliable network)

**Preconditions**:

1. A multi-cloud system has been setup and operate normally

2. the network between the "center of edge" and edge is controllable

**Steps**:

1. Before edge cloud isolated: Launch VM, Vm Launch success.

2. VM image is cached, hit shows 0: glance-cache-manage -H ${subcloud_floating_ip} list-cached.

3. Cut off network connection between center of edge and edge cloud 

4. Launch VM with cached image, VM launch success.

5. VM rebuild

6. VM live migration

7. VM cold migration

8. VM evacuation

9. Vm delete

**Expected outcome**: All Test Steps are passed.


**Management of Multiple OpenStack**
=============================================

**Test case #8: "Single Sign on" in multiple cloud environment**

**Description**:Maintainers/Users can login to central cloud/large edge cloud, and 
can jump to edge cloud without entering user name and password again

**Preconditions**:

1. A multi-cloud system has been established, with a cloud play as "central" or the 
multi-cloud management platform play as the "central"

2. The network between the "central" and edge are available

**Steps**:

1. Login to central cloud/large edge cloud with user name and password

2. Jump to related medium/small edge cloud withoud reentering username and password

**Expected outcome**: Jump from central/large edge to medium/small edge succefully

----------------------------------------

**Test case #9: Manage a newly established edge cloud**

**Description**: The central cloud or large edge cloud or multi-cloud management 
platform should supports the management of newly created edge sites (the edge site 
has already exist in this case)

**Preconditions**:

1. A multi-cloud system has been established, with a cloud play as "central" or 
the multi-cloud management platform play as the "central"

2. And edge cloud has already exist but not managed by central edge cloud

3. The network between the "central" and edge are available

**Steps**:

1. Log into "central" cloud or multi-cloud platform using admin account and switch 
to "add cloud environment" related page (using CLI is also ok)

2. Filling the accessing interface/URL information of a newly created edge VIM

**Expected outcome**:

1. The accessing interface/URL can be added to the "central" cloud or multi-cloud 
management platform; 

2. Using the interface/URL can jump to the newly created edge VIM correctly

3. We can list the managed edge VIMs

----------------------------------------

**Test case #10: Removing a managed edge cloud**

**Description**: The central cloud or large edge cloud or multi-cloud management 
platform should supports to remove an edge VIM from "managed" list

**Preconditions**:

1. A multi-cloud system has been established, with a cloud play as "central" or the 
multi-cloud management platform play as the "central"

2. And edge cloud has already exist and has managed by central edge cloud

3. The network between the "central" and edge are available

**Steps**:

1. Log into "central" cloud or multi-cloud platform using admin account and switch 
to "managed cloud list" related page (using CLI is also ok)

2. remove the edge cloud from the center of edge

**Expected outcome**: The edge cloud has been remove from the managed cloud list, 
and cannot reached from the center of edge

----------------------------------------

**Test case #11: Provision a new edge hardware from the central cloud**

**Description**:There is remote hardware that is ready to host edge software. That 
hardware must be provisioned from the central edge in an automated way

**Preconditions**:

1. A multi-cloud system has been established, with a cloud play as "central" or the 
multi-cloud management platform play as the "central"

2. The central node has a DHCP and HTTP server which is capable of booting thorugh PXE

3. An edge node exists

4. The network between the "central" and edge are available (L2? L3?) --> Not sure 
how this would work if the new node and the server are not in the same L2 network 
(which is the general case)

**Steps**:

1. Power on the node remotely (e.g. through BMC) and configures PXE booting in BIOS

2. Verify that the DHCP server detects the message and the PXE boot starts

3. Check after a reasonable time that the node is booted

**Expected outcome**:

1. Node powers on and starts PXE boot

2. PXE boot packages reach the central cloud

3. Node downloads the image and dumps it into its hard drive

4. We can access the node (e.g. ssh)

----------------------------------------

**Test case #12: Display multi-VIM virtualization resource**

**Description**: As there is no cloud  O&M stuff at most of the edge cloud, the 
virtualization resources of different edge should be displayed and updated somewhere 
at the central cloud or large edge cloud or multi-cloud management platform

**Preconditions**:

1. A multi-cloud system has been setup and operate normally (at least two cloud, one 
plays as the center of edge and the rest plays as the edge)

2. the edge cloud has been managed by the center of edge (could be cantral cloud or 
large edge cloud or multi-cloud management platform)

**Steps**:

1. Log into "center of edge" using admin account, and check the resource information 
of managed edge VIM including host, VM, CPU, resource usage rate and etc.

2. delete a VM in the edge cloud and recheck the resource information of this edge 
cloud in the "center of edge"

**Expected outcome**:

1. The general resource information of edge cloud could be found in the "center of 
edge" and it's correct

2. the information can be updated in the "center of edge" within 5 minutes automatically 
or be manually uppdated immediately

----------------------------------------

**Test case #13: Support remote upgrading of edge VIM from "center of edge"**

**Description**: As there might be no cloud O&M stuff at most of the dege cloud, the 
edge system should support remote upgrading of edge VIM from "centrer of edge"

**Preconditions**:

1. A multi-cloud system are running normally

2. At least one edge cloud has been managed by the "center of edge" or the "center 
of edge" can access the edge cloud and manage its resources remotely

3. At least one VM running on the edge cloud

4. Patch is ready

**Steps**:

1. Record the software version of edge cloud

2. Log into "center of edge" using admin account and go to the "software management" 
or "Patch management" page (CLI is acceptable)

3. Upload the patch and apply it to the edge cloud

4. check the software version of edge cloud aftter upgrading

5. Roll back the patch

6. Check the software version after rolling back

**Expected outcome**:

1. Patch can be applied/removed to/from the edge cloud successfully and the version 
number is correct after upgrading/rolling back

2. VM running normally during upgrading/rolling back process

----------------------------------------

**Test case #14: Alarm/warning from edge is displayed at the "center of edge"**

**Description**: Mutli-cloud system should support centralized alarm/warning display

**Preconditions**:

1. A mutli-cloud system are running normally

2. At least one edge cloud has been managed by the "center of edge" or from the "center 
of edge" can access the edge cloud

**Steps**:

1. Create an alarm at edge cloud by killing an important openstack process such as 
mysql, nova-api, nova-compute and etc.

2. check corresponding alarm displayed on alarm related page at "center of edge"

**Expected outcome**: Alarm from edge cloud can be displayed at "center of edge"

----------------------------------------

**Test case #15: Management database & configuration data backup**

**Description**: Multi-cloud system should support that the management database and 
configuration data of edge can be backed up to "center of edge" or to local edge. 
And those data can be recovered

**Preconditions**:

1. A multi-cloud system are running normally

2. At least one edge cloud has been managed by the "center of edge"

**Steps**:

1. Log in to "center of edge" using admin acount, and go to "data backup" related page

2. choose an edge cloud and choose a file or a server as the beckup destination (the 
destination could be backend storage or an outside ftp server at "center of edge", 
or it could be a local file directory)

3. Fully backup the management database and configuration data of the chosen edge 
cloud to the chosen storage destination

4. Create a VM in the edge cloud

5. Recover the backup data to the edge and check whether the VM exist

    (If using GUI, the original operation should start with log into "center of edge" not the edge cloud; CLI is also acceptable)

**Expected outcome**:

1. After step 3, the data can be backed up to s pointde place

2. After step 5, the newly created VM does not exist

**Performance of Multi-Cloud System Under Unreliable Network**
================================================================

**Test case #16: Influence of delay between "center of edge" and edge**

**Description**: none

**Preconditions**:

1. A multi-cloud system has been setup and operate normally

2. the network between the "center of edge" and edge is controllable

3. An important command/operation  which is triggered at the "center of edge" 
and will be finally executed at the edge has been chosen
	
    (the command/operation might be different in different system)

**Steps**:

1. Set the network delay between "center of edge" and edge to be 5 ms

2. do the command/operation in precondition step 3, observe whether it is successful 
(if doing batch operation, observe the success rate), and how long it cost

3. Increase the delay to 10 ms, 15 ms, 20 ms, 25 ms, 30 ms and repeat step 2

**Expected outcome**:

1. record the success condition/rate and time of the operation under different delay

2. Check atomicity. If communication provokes the command to fail, everything needs 
to continue working

----------------------------------------

**Test case #17: Influence of traffic between "center of edge" and edge**

**Description**: /

**Preconditions**:

**Steps**:

1. Set the network traffic between "center of edge" and edge to a certain number

2. do the command/operation in precondition step 3, observe whether it is successful 
(if doing batch operation, observe the success rate), and how long it cost

3. Increase the traffic  and repeat step 2

**Expected outcome**:

1. record the success condition/rate and time of the operation under different traffic

----------------------------------------

**Test case #18: Influence of packet loss between "center of edge" and edge**

**Description**: /

**Preconditions**:

1. A multi-cloud system has been setup and operate normally

2. the network between the "center of edge" and edge is controllable

3. An important batch operation  which is triggered at the "center of edge" and will 
be finally executed at the edge has been chosen

    (the command/operation might be different in different system)

**Steps**:

1. set the network packet loss between "center of edge" and edge to be 1%

2. do the batch operation and observe the seccess rate

3. increase the packet loss to 3%, 5%, 8%, 10%, and repeat step 2

**Expected outcome**:

1. record the success rate of batch operation under different packet loss