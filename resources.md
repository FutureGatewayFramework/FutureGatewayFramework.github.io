---
layout: default
title: Resources
permalink: /resources/
order: 5
---

# Tutorials

The best way to get familiar with the FutureGateway Framework is to make practice with their components and this chapter provide links to several useful tutorials.

|                                                                        |                                                          |
| ---------------------------------------------------------------------- | -------------------------------------------------------- |
| [FutureGateway APIs]({{ site.baseurl }}{% link tutorials/fgapis.md %}) | FutureGateway installation and basic API usage examples. |
| [Liferay GUI]({{ site.baseurl }}{% link tutorials/gui_liferay7.md %})  | How to prepare a GUI interface intacting with FG.        |

[infn]: https://www.infn.it
[infnct]: https://www.ct.infn.it
[indigo-dc]: https://www.indigo-datacloud.eu
[eosc-hub]: https://www.eosc-hub.eu
[fgf]: https://github.com/FutureGatewayFramework
[fg]: https://github.com/indigo-dc/fgDocumentation
[inveniordmpm]: https://indico.cern.ch/event/854421/page/18559-general-information
[infnoar]: https://www.openaccessrepository.it

# Souce code projects

All components of the [FutureGateway Framework][fgf] are stored under a common area in [GitHub][github] repository.
This area consists of different software projects briefly described below.

| Project                                                                                                        | Description                                                                                                                                                                                                                                                                                                                                                                                                                |
| -------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [fgDocumentation](https://github.com/FutureGatewayFramework/fgDocumentation)                                   | This repository contains installation and usage instructions of the FutureGateway framework. The doumentation is written in Markdown and integrated with [Readthedocs](https://readthedocs.org/projects/futuregateway-framework/)                                                                                                                                                                                          |
| [fgAPIServer](https://github.com/FutureGatewayFramework/fgAPIServer)                                           | This is a core component of the FutureGateway since it is the repository holding the implementation of the [API specifications](https://fgapis.docs.apiary.io). It also contain the necessary scripts to implement the DB schema.                                                                                                                                                                                          |
| [fgAPIServerDaemon](https://github.com/FutureGatewayFramework/APIServerDaemon)                                 | This repository contains the API Server implemented with JAVA which having two Executor Interfaces available: the 'Grid and Cloud Engine' and the 'TOSCA-idc'. The first EI is capable to interface with any infrastructure that [JSAGA library](http://software.in2p3.fr/jsaga/latest-release/) can interacth with.                                                                                                       |
| [fgSetup](https://github.com/FutureGatewayFramework/fgSetup)                                                   | This repository contains the scripts to install FutureGateway components. Installation scripts are implemented in bash and they are structured to support any hosting operating system using separate modules for each OS. Tested operating systems are currently: Ubuntu >16.04 and MacOS X > 10.10. Under `docker` directory it is possible to find scripts to isntall FutureGateway components using Docker containers. |
| [fgAPIServerDaemon](https://github.com/FutureGatewayFramework/fgAPIServerDaemon)                               | A python based implementation of the APIServerDaemon; it is under development and does not provide yet any Executor Interface; it just provides the daemon execution loop.                                                                                                                                                                                                                                                 |
| [FutureGatewayFramework.github.io](https://github.com/FutureGatewayFramework/FutureGatewayFramework.github.io) | This web site content                                                                                                                                                                                                                                                                                                                                                                                                      |
| [fgAPIServerGUI](https://github.com/FutureGatewayFramework/fgAPIServerGUI)                                     | A simple GUI front-end to manage APIs (IAT, UGR and AAA). Although the GUI already contains the necessary core element, this project is on hold status.                                                                                                                                                                                                                                                                    |
| [fgToolkit](https://github.com/FutureGatewayFramework/fgToolkit)                                               | This repository contains progamming APIs, script tools to ease the development of GUIs or to manage the components of the framework                                                                                                                                                                                                                                                                                        |
| [FutureGateway-APIs](https://github.com/FutureGatewayFramework/FutureGateway-APIs)                             | This repository contains the specifications of the FutureGateway APIs as they are explained in [APIary.io](https://fgapis.docs.apiary.io) web site. It only contains specifications for IAT part of the APIs yet.                                                                                                                                                                                                          |
| [ExecutorInterfaces](https://github.com/FutureGatewayFramework/ExecutorInterfaces)                             | This repository contains a study for python language to instantiate EI classes only using their name. This study will be part of the ExecutorInterface engine for [fgAPIServerDaemon](https://github.com/FutureGatewayFramework/fgAPIServerDaemon)                                                                                                                                                                         |
| [bare_executor](https://github.com/FutureGatewayFramework/bare_executor)                                       | This work should implement an ExecutorInterface operating at API Server host level and fully implemented in Bash. This work should be inerited in order to implement more complicated EIs. Developments on this repository are on hold                                                                                                                                                                                     |

[fgf]: https://github.com/FutureGatewayFramework
[github]: https://github.com
