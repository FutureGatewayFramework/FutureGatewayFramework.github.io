---
layout: default
title: ""
order: 0
---

# ![FGF](/images/fglogo_64.png) [FutureGateway Framework][fgf]

Science always requires an increasing amount of computational resources and cyber insfrastructures in the form of Distributed Computing Infrasrtuctures (**DCI**) become the most significant solution to satisfy this continue demand. However the usability of the DCIs is not comfortable for the final users and in general, the bigger the resources are and the more complex is the usage of the related infrastructure. For this reason the **Science Gateways** (SG)s have to be considered one of the most satisfying answers to cope this problem.
One of the first implementation of Science Gateways have been provided by the [XEDE](https://www.xsede.org) initiative, who also provides the following:

| **Definition** [[XEDE](https://www.xsede.org/ecosystem/science-gateways)]                                                                                                                                                                                      |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| _A **Science Gateway** is a community-developed set of tools, applications, and data that is integrated via a portal or a suite of applications, usually in a graphical user interface, that is further customized to meet the needs of a specific community._ |

Although SGs are a powerful solution to easily access DCIs, their adoption introduces a further level of complexity placed between the DCIs exploitation and the pure scientific work, made of non trivial steps like: identify the resources, develop the back-end, develop user interfaces, plan the authentication and authorization and provide at least a minimal accounting solution. For this reason it has been developed the:

|---|
|**FutureGateway Framework** (FGF) a **software initiative** aiming to build **quickly** and **securely** well structured Science Gateways.|

The framework is made of two components:

- The **FutureGateway** (FG), that consists of the core part of the Framework
- A collections of **Software APIs**, **Installation scripts** and **Tools**, to install, maintain and develop both back-end and GUIs.

The FGF is totally [open source][osi], licenced under the [Apache2][apache2] terms, the source code of all its components is fully available on [GitHub][github] and the final users contribution is welcome and even encouraged.

If you are interested in this work and you want to get more information, plase visit the [design](/design/) page. For GUI developers it could be interesting to get overview of the [futuregateway APIs](/fgfapis/), or to get full information about the FGF, please visit its [documentation](https://futuregateway-framework.readthedocs.io/en/latest/?badge=latest).
If you would like to stay updated with FGF developments you may visit our [news](/news/) area.
Under [resources](/resources/) it is possible to find **tutorials** very useful to make practice with the framework or get information about any specific source projects available under [FutureGateway Framework][fgf] inside [GitHub][github] repository].

Subscribe the FutureGatewayFramework <a href="https://mailman.ct.infn.it/mailman/listinfo/futuregatewayframework">mailing list</a>

[fgf]: https://github.com/FutureGatewayFramework
[osi]: https://opensource.org/osd
[github]: https://github.com
[apache2]: https://www.apache.org/licenses/LICENSE-2.0
