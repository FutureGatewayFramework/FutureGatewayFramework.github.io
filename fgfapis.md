---
layout: default
title: FutureGateway APIs
permalink: /fgfapis/
---

# FutureGateway APIs
One of the most important aspects of the FGF consists of its set of RESTful APIs.
The whole set of APIs have been splitted in the following families:

* **IAT** - Infrastructure Applications and Tasks ([Specifications](http://docs.fgapis.apiary.io/#))
* **UGR** - Users Groups and Roles (_Specification not yet available_)
* **AAA** - Authorizing, Auditing and Accounting (*under desing*)

## IAT
This set of API manages the core part of the FGG, in particuar:

**Infrastructures**
* Describe the DCI configuration necessary to run an ‘application’. 
* Form of a set of pairs: <Key name, Key value>

**Applications**
* It describes the activity to perform on the DCI.
* What to execute, involved files, error and output streams, etc.
* Each application may execute on one or more infrastructures (random choice).

**Tasks**
* Applications executed or executing on a DCI are tasks.
* The term ‘task’ may include many operations ranging from simple batch executions, up to more sophisticated actions like a PaaS creation , start/stop services, etc.

## UGR
This set of API is used to map SG membership allowing or denying authorization to the DCI resorces.

## AAA
Auditing and Accounting provides the infrormation of who did what and when in the DCI form tre SG.
