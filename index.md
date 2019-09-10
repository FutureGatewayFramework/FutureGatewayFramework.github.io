
#The FutureGateway Framework

Science always requires an increasing amount of computational resources and cyber insfrastructures in the form of Distributed Computing Infrasrtuctures (DCI) become the most significant solution to reach this continue demand. However the use of DCIs is not always comfortable for the final users and  in general, the bigger are the resources and the more complex is the usage of the related infrastructure. For this reason the Science Gateways have to be considered one of the most satisfying answers to cope this problem.
One of the first implementation of Science Gateways have been provided by the [XEDE](https://www.xsede.org/ecosystem/science-gateways) initiative, who also provided an official definition for them:

```definition
"A Science Gateway is a community-developed set of tools, applications, and data that is integrated via a portal or a suite of applications, usually in a graphical user interface, that is further customized to meet the needs of a specific community."
```

The **FutureGateway Framework**  (FGF) is a **software initiative** aiming to build **fastly** and **securely** well structured Science Gateways or aid existing Science Gateways to easily **integrate** DCIs.

# Design Principles
Many of the ideas behind the FGF have been extracted from the SG definition and in particular:

* **Serves Communities** - SGs are targeted to serve one or many communities.
* **Tools, applications and data** integration - SGs are made of different components operating at different levels, from the computing system back-end up to the user front-end.
* Provides **GUIs** (Web, desktop and mobile applications) - SGs can offer many kind of user level front-ends
* **Customization** - Served communities require their own specific customisations that SGs should easily support them.

Moreover, the following can be considered as the most relevan key points in adopting the FGF:

**Ease the installation and the maintenance**
* It provides installation and maintenance scripts
* Open Source code available on GitHub in Apache licence
* Customizable for different community needs

**Flexible and structured access to distributed computing services through the use of pluggable components**
* Potentially the FGF can access any kind of DCI (G&CEngine (JSAGA), Tosca)
* Advanced users may integrate new DCIs without changing the SG front-end
* SG front-end may use many DCI in a transparent way
* SG user activity will be automatically tracked and usage resource accounted automatically

**Provide a set of restFull APIs**
* Back-end web portals independency
* Mobile and Desktop applications, Workflow Engines, IoT
* Accessible by any programming language

# FutureGateway REST APIs
One of the most important aspects of the FGF consists of its set of [RESTful APIs](http://docs.fgapis.apiary.io/#)
The whole set of APIs have been splitted in the following families:

* **IAT** - Infrastructure Applications and Tasks
* **UGR** - Users Groups and Roles
* **AAA** - Authorizing, Auditing and Accounting (*under desing*)

## IAT
This set of API manages the core part of the FGG, in particuar:

**Infrastructures**
* Describe the DCI configuration necessary to run an ‘application’. 
* Form of a set of pairs: <Key name, Key value>

** Applications**
* It describes the activity to perform on the DCI.
* What to execute, involved files, error and output streams, etc.
* Each application may execute on one or more infrastructures (random choice).

**Tasks**
Applications executed or executing on a DCI are tasks. The term ‘task’ may include many operations ranging from simple batch executions, up to more sophisticated actions like a PaaS creation , start/stop services, etc.

## UGR
This set of API is used to map SG membership allowing or denying authorization to the DCI resorces.

## AAA
Auditing and Accounting provides the infrormation of who did what and when in the DCI form tre SG.
