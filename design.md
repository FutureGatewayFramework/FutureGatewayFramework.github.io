---
layout: default
title: Design
permalink: /design/
---

# Design Principles
Many of the ideas behind the FGF have been extracted from the:

|**Definition** [[XEDE](https://www.xsede.org/ecosystem/science-gateways)]|
|---|
|*A **Science Gateway** is a community-developed set of tools, applications, and data that is integrated via a portal or a suite of applications, usually in a graphical user interface, that is further customized to meet the needs of a specific community.*|

In particular, the following **keywords** can be highlighted:

* **Serves Communities** - SGs are targeted to serve one or many communities.
* **Tools, applications and data** integration - SGs are made of different components operating at different levels, from the computing system back-end up to the user front-end.
* Provides **GUIs** (Web, desktop and mobile applications) - SGs can offer many kind of user level front-ends.
* **Customization** - Served communities require their own specific customisations that SGs should easily provide.

# Key features

**Ease the installation and the maintenance**
* It provides installation and maintenance scripts
* Open Source code available on GitHub in Apache licence
* Customizable for different community needs

**Flexible and structured access to distributed computing services through the use of pluggable components**
* Potentially the FGF can access any kind of DCI (G&CEngine (JSAGA), Tosca)
* Advanced users may integrate new DCIs without changing the SG front-end
* SG front-end may use many DCI in a transparent way
* SG user activity will be automatically tracked and usage resource accounted automatically

**Provide a set of restFull [APIs](/fgfapis/)**
* Back-end web portals independency
* Mobile and Desktop applications, Workflow Engines, IoT
* Accessible by any programming language

