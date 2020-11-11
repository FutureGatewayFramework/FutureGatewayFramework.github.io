---
layout: default
title: APIs
permalink: /fgfapis/
order: 4
---

# FutureGateway APIs
One of the most important aspects of the FGF consists of its set of RESTful APIs.
The whole set of APIs have been splitted in the following families:

* **IAT** - Infrastructure Applications and Tasks ([Specifications](http://docs.fgapis.apiary.io/#))
* **UGR** - Users Groups and Roles (_Specification not yet available_)
* **AAA** - Authorizing, Auditing and Accounting (*under desing*)

## [IAT](https://futuregateway-framework.readthedocs.io/en/latest/usage/#user-guide)
This set of API that manages the core part of the [FGF], in particuar:

|**[Infrastructures](https://futuregateway-framework.readthedocs.io/en/latest/usage/#infrastructures)**|
|---|
|Describes the necessary configuration required to execute an ‘application’ into an existing DCI. Infrastructure configuration is specified through a set of pairs: <Key name, Key value>. These values will be associated to an infrastructure that will be refernced by one or more applications using that infrastructure to run.|

|**[Applications](https://futuregateway-framework.readthedocs.io/en/latest/usage/#applications)**|
|---|
|Describes the activity to perform on the DCI in terms of: what to execute, involved files, error and output streams, etc. Each application will then reference a target infrastructure and it is also possible to specify more than one infrastructures, in such a case the infrastructure will be randomly selected during execution request.|

|**[Tasks](https://futuregateway-framework.readthedocs.io/en/latest/usage/#tasks)**|
|---|
|Applications executed or executing on a DCI are tasks. The term ‘task’ may include many operations ranging from simple batch executions, up to more sophisticated actions like a PaaS creation , start/stop services, etc. The activity to perform on top of the DCI is a combination of settings between _Infrastructures_ and _Applications_.

## [UGR](https://futuregateway-framework.readthedocs.io/en/latest/usage/#users-groups-and-roles)
This set of API is used to map SG membership allowing or denying authorization to the DCI resorces.

|**Users**|
|---|
|Using this API set, user membership can be registered, modified and deleted. It is also possible to associate data at user level in the form of pairs:  <Key name, Key value>. Users can belong to one or more Groups in order to obtain a different set of authorisation rights.|

|**Groups**|
|---|
|Using this set of APIs it is possible to create, modify and delete user groups. One or more registered users can belong to a Group and in the same way one or more registered application can be associated to a group. It is the Group membership who grant roles to the users, since one or more roles can be associated to a group as well.|

|**Roles**|
|---|
|FutureGateway foresees a specific set of roles that defines the access rights associated to a given a user|


## AAA
Auditing and Accounting provides the infrormation of who did what and when on top of a given DCI form the SGs. This API set is not yet implemented, however Auditing and Accounting can be performed properly querying the FG database. This kind of interaction has been used for UGR, before this its set of APIs was available. To learn more about querying the database, pleas refer to the [DB Schema](https://futuregateway-framework.readthedocs.io/en/latest/usage/#db-schema) explanation.


[FGF]: https://github.com/FutureGatewayFramework
