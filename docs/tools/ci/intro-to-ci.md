---
title: Présentation de l’intégration continue avec Xamarin
description: Ce document décrit l’intégration continue avec Xamarin. Il décrit le contrôle de version et divers environnements d’intégration continue.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: conceptdev
ms.author: crdun
ms.date: 07/19/2017
ms.openlocfilehash: d335a107d1520db3c76ee602d38adcb129f122b0
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70293094"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Présentation de l’intégration continue avec Xamarin

_L’intégration continue est une pratique d’ingénierie logicielle dans laquelle une génération automatisée compile et, éventuellement, teste une application quand du code est ajouté ou modifié par les développeurs dans le référentiel de contrôle de version du projet. Cet article aborde les concepts généraux de l’intégration continue et certaines des options disponibles pour l’intégration continue avec les projets Xamarin._

Il est courant que les développeurs travaillent en parallèle sur des projets logiciels. À un moment donné, il est nécessaire d’intégrer tous ces flux de travail parallèles dans un code base qui constitue le produit final. Au début du développement de logiciels, cette intégration était effectuée à la fin d’un projet, ce qui était un processus difficile et risqué.

L’intégration continue (CI) évite de telles complexités en fusionnant les modifications de chaque développeur dans la base de code commune de manière continue, généralement chaque fois que les développeurs archivent des modifications dans le référentiel de code partagé du projet. Chaque archivage déclenche une génération automatisée et exécute des tests automatisés pour vérifier que le code nouvellement introduit n’a pas rompu de code existant.  De cette façon, l’élément de configuration met immédiatement en évidence les erreurs et les problèmes et s’assure que tous les membres de l’équipe restent à jour. Cela aboutit à une base de code cohérente et stable.

Les systèmes d’intégration continue ont deux parties principales :

- **Le** contrôle de version (VC), également appelé contrôle de code source ou gestion du code source, consolide tout le code d’un projet dans un référentiel partagé unique et conserve un historique complet de chaque modification apportée à chaque fichier. Ce référentiel, souvent appelé « succursale *principale* », *contient le code* source qui sera utilisé pour générer la version de production ou de publication de l’application. Il existe de nombreux produits commerciaux et open source pour cette tâche, qui permettent généralement aux équipes ou aux individus de répliquer une copie du code dans des branches secondaires où elles peuvent apporter de nombreuses modifications ou effectuer des expérimentations sans risque pour la branche principale. Une fois les modifications apportées dans une branche secondaire validées, elles peuvent être fusionnées de nouveau dans la branche principale.
- **Serveur d’intégration continue** : le serveur d’intégration continue est chargé de collecter tous les artefacts d’un projet (code source, images, vidéos, bases de données, tests automatisés, etc.), de compiler l’application et d’exécuter les tests automatisés. Là encore, il existe de nombreux outils de serveur d’intégration continue Open source et commerciaux.

Les développeurs disposent généralement d’une copie de travail d’une ou de plusieurs branches sur leurs postes de travail, où le travail est initialement effectué. Une fois qu’un ensemble de tâches approprié est terminé, les modifications sont « archivées dans » ou « validées » dans la branche appropriée, qui les propage aux copies de travail d’autres développeurs. C’est ainsi qu’une équipe s’assure qu’elle travaille sur le même code.

Là encore, avec l’intégration continue, l’acte de validation des modifications amène le serveur CI à générer le projet et à exécuter des tests automatisés pour vérifier l’exactitude du code source. S’il existe des erreurs de build ou des échecs de test, un serveur CI notifie le développeur responsable (par e-mail, IM, Twitter, Growl, etc.) pour qu’il puisse corriger le problème. (Les serveurs CI peuvent même refuser la validation en cas d’échec, ce qui s’appelle un « archivage contrôlé ».)

Le diagramme suivant illustre ce processus :

[![](intro-to-ci-images/intro01-small.png "Ce diagramme illustre ce processus")](intro-to-ci-images/intro01.png#lightbox)

Mobile Apps présente des défis uniques pour l’intégration continue. Les applications peuvent nécessiter des capteurs tels que le GPS ou l’appareil photo qui sont uniquement disponibles sur des appareils physiques. En outre, les simulateurs ou les émulateurs sont uniquement une approximation du matériel et peuvent masquer ou masquer les problèmes. En fin de compte, il est nécessaire de tester une application mobile sur du matériel réel pour être certain qu’elle est véritablement prête pour les clients.

Le [test App Center](https://docs.microsoft.com/appcenter/test-cloud) résout ce problème en testant des applications directement sur des centaines d’appareils physiques. Les développeurs écrivent des tests d’acceptation automatisés, qui permettent un test puissant de l’interface utilisateur. Une fois ces tests téléchargés vers App Center, le serveur CI peut les exécuter automatiquement dans le cadre d’un processus CI, comme indiqué dans le diagramme suivant :

[![](intro-to-ci-images/intro02-small.png "Une fois ces tests téléchargés vers App Center, le serveur CI peut les exécuter automatiquement dans le cadre d’un processus CI, comme indiqué dans ce diagramme.")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>Composants de l’intégration continue

Il existe un vaste écosystème d’outils commerciaux et open source conçus pour prendre en charge l’intégration continue. Cette section décrit quelques-uns des plus courants.

### <a name="version-control"></a>Gestion de version

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps et Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) et [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) sont des outils collaboratifs de Microsoft pour les services de build d’intégration continue, le suivi des tâches, les outils de planification et de création de rapports agile et la gestion de version. Avec le contrôle de version, Azure DevOps et TFS peuvent fonctionner avec son propre système (Team Foundation Version Control ou TFVC) ou avec des projets hébergés sur GitHub.

- Azure DevOps fournit des services via le Cloud. Son principal avantage est qu’il ne nécessite pas de matériel ou d’infrastructure dédié et qu’il est accessible depuis n’importe où via des navigateurs Web et par le biais d’outils de développement populaires tels que Visual Studio, ce qui est attrayant pour les équipes distribuées géographiquement. . Il est gratuit pour les équipes de cinq développeurs ou moins, après quoi des licences supplémentaires peuvent être achetées pour s’adapter à une équipe en pleine croissance.
- TFS est conçu pour les serveurs Windows locaux et est accessible via un réseau local ou une connexion VPN à ce réseau. Son principal avantage est que vous contrôlez entièrement la configuration des serveurs de build et que vous pouvez installer tous les logiciels ou services supplémentaires nécessaires. TFS offre une édition Express gratuite pour les petites équipes.

TFS et Azure DevOps sont étroitement intégrés à Visual Studio et permettent aux développeurs d’effectuer de nombreuses tâches de contrôle de version et d’élément de configuration à partir d’un seul IDE. Le plug-in Team Explorer Everywhere pour Eclipse (voir ci-dessous) est également disponible. Visual Studio pour Mac propose [un aperçu des TFVC disponibles](/visualstudio/mac/tf-version-control/).

Les [pipelines Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) prennent en charge directement les projets Xamarin, dans lesquels vous créez une définition de build pour chaque plateforme que vous souhaitez cibler (Android, iOS et Windows). La licence Xamarin appropriée est nécessaire pour chaque définition de Build. Il est également possible de connecter un serveur de build TFS Xamarin local à Azure DevOps à cet effet. Avec cette configuration, les builds mises en file d’attente vers Azure DevOps sont déléguées au serveur local. Pour plus d’informations, consultez [agents de build et de mise en version](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). Vous pouvez également utiliser un autre outil de génération comme Jenkins ou City Team.

Un résumé complet de toutes les fonctionnalités de Application Lifecycle Management (ALM) de Visual Studio, d’Azure DevOps et de Team Foundation Server, consultez [DevOps with Xamarin Apps](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) apporte la puissance de Team Foundation Server et d’Azure DevOps aux équipes développant en dehors de Visual Studio. Il permet aux développeurs de se connecter à des projets d’équipe locaux ou dans le Cloud à partir d’Eclipse ou du client de ligne de commande multiplateforme pour OS X et Linux. Team Explorer Everywhere fournit un accès complet au contrôle de version (y compris git), des éléments de travail et des fonctionnalités de génération pour les plateformes non-Windows.

#### <a name="git"></a>Git

[Git](http://git-scm.com) est une solution de gestion de version open source populaire qui a été développée à l’origine pour gérer le code source pour le noyau Linux. Il s’agit d’un système très rapide et flexible qui est populaire avec des projets logiciels de toutes tailles. Il s’adapte facilement à des développeurs simples disposant d’un accès Internet médiocre aux grandes équipes qui couvrent le monde entier. Git facilite également la mise en branche, qui peut, à son tour, encourager des flux de développement parallèles avec un risque minime.

Git peut fonctionner entièrement via des navigateurs Web ou des [clients d’interface utilisateur graphique](http://git-scm.com/downloads/guis) qui s’exécutent sur Linux, Mac OSX et Windows. Il est gratuit pour les dépôts publics ; les dépôts privés nécessitent un [plan payant](https://github.com/pricing).

Les versions actuelles de Visual Studio pour Windows et Mac offrent une prise en charge native de git. Microsoft fournit une [extension téléchargeable pour git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) pour les versions antérieures de Visual Studio. Comme indiqué ci-dessus, Azure DevOps et TFS peuvent utiliser Git pour la gestion de version au lieu de TFVC.

#### <a name="subversion"></a>Subversion

Sous- [version](http://subversion.apache.org) (SVN) est un système de contrôle de version open source populaire qui a été utilisé depuis 2000. SVN s’exécute sur toutes les versions modernes de OS X, Windows, FreeBSD, Linux et UNIX. Visual Studio pour Mac dispose d’une prise en charge native de SVN. Il existe des extensions tierces qui offrent la prise en charge de SVN à Visual Studio.

### <a name="continuous-integration-environments"></a>Environnements d’intégration continue

La configuration d’un environnement d’intégration continue implique la combinaison d’un système de contrôle de version avec un service de Build.  Pour ce dernier, les deux types les plus courants sont les suivants :

- [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/) est le système de génération d’Azure DEVOPS et TFS. Il est étroitement intégré à Visual Studio, ce qui permet aux développeurs de déclencher des builds, d’exécuter automatiquement des tests et de voir les résultats.
- Jenkins est un serveur d’intégration open source avec un écosystème complet de plug-ins pour prendre en charge tous les types de développements logiciels. Il s’exécute sur Windows et Mac OS X. Jenkins n’est pas intégré à un IDE spécifique. Au lieu de cela, elle est configurée et gérée par le biais d’une interface Web. Jenkins CI est également facile à installer et à configurer, ce qui le rend attrayant pour les petites équipes.

Vous pouvez utiliser TFS/Azure DevOps par lui-même, ou vous pouvez utiliser Jenkins en association avec TFS/Azure DevOps ou git, comme décrit dans les sections suivantes.

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps et Team Foundation Server

Comme nous l’avons vu, Azure DevOps et Team Foundation Server fournissent à la fois le contrôle de version et les services de Build. Les services de build requièrent toujours une licence Xamarin Business ou Enterprise pour chaque plateforme cible.

Avec Azure DevOps, vous créez une définition de build distincte pour chaque plateforme cible et entrez la licence appropriée. Une fois configuré, Azure DevOps exécute des builds et des tests dans le Cloud. Pour plus d’informations, consultez [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/) .

Avec Team Foundation Server, vous configurez un ordinateur de build comme suit pour des plateformes cibles spécifiques :

- **Android et Windows :** Installez Visual Studio et les outils Xamarin (pour Android et Windows) et configurez avec vos licences Xamarin. Il est également nécessaire de déplacer le Android SDK vers un emplacement partagé sur le serveur où l’agent de build TFS peut le trouver. Pour plus d’informations, consultez [configuration de TFVC](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS et Xamarin :** Installez Visual Studio et les outils Xamarin sur le serveur Windows avec la licence appropriée. Installez ensuite Visual Studio pour Mac sur un ordinateur Mac OS X accessible sur le réseau, qui servira d’hôte de build et créera le package d’application final (loi pour iOS, APP pour OS X).

Le diagramme suivant illustre cette topographie :

[![](intro-to-ci-images/intro03-small.png "Ce diagramme illustre cette topographie")](intro-to-ci-images/intro03.png#lightbox)

Il est également possible de lier un serveur TFS local à un projet Azure DevOps afin que les builds Azure DevOps soient déléguées au serveur local. Pour plus d’informations, consultez [agents de build et de mise en version](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

#### <a name="azure-devops-and-jenkins"></a>Azure DevOps et Jenkins

Si vous utilisez Jenkins pour générer vos applications, vous pouvez stocker votre code dans Azure DevOps ou Team Foundation Server et continuer à utiliser Jenkins pour vos builds d’intégration continue. Vous pouvez déclencher une build Jenkins quand vous envoyez du code au référentiel git de votre projet d’équipe ou lorsque vous archivez le code dans TFVC. Pour plus d’informations, consultez [Jenkins avec Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Si vous utilisez Jenkins pour générer vos applications, vous pouvez stocker votre code dans Azure DevOps ou Team Foundation Server et continuer à utiliser Jenkins pour vos builds d’intégration continue")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>Git et Jenkins

Un autre environnement CI commun peut être entièrement basé sur OS X. Ce scénario implique l’utilisation de Git pour le contrôle de code source et de Jenkins pour le serveur de builds. Les deux s’exécutent sur un seul Mac OS X ordinateur sur lequel Visual Studio pour Mac installé. Cela est très similaire à l’environnement Azure DevOps + Jenkins abordé dans la section précédente :

[![](intro-to-ci-images/intro05-small.png "Cela est très similaire à l’environnement Azure DevOps + Jenkins abordé dans la section précédente.")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins n’est [pas pris en charge par Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**
