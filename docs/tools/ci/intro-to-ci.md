---
title: Introduction à l’intégration continue avec Xamarin
description: Ce document décrit l’intégration continue avec Xamarin. Il aborde les différents environnements d’intégration continue et de contrôle de version.
ms.prod: xamarin
ms.assetid: C034200E-2947-4309-9DDD-80DAC505C43F
author: lobrien
ms.author: laobri
ms.date: 07/19/2017
ms.openlocfilehash: 48dd2e1f2f5dd9ffdad0b726c5066a9a968d2396
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650536"
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introduction à l’intégration continue avec Xamarin

_L’intégration continue est une pratique d’ingénierie logicielle dans laquelle une build automatisée compile et teste éventuellement une application lorsque le code est ajouté ou modifié par les développeurs dans le référentiel de contrôle de version du projet. Cet article explique les concepts généraux de l’intégration continue et certaines des options disponibles pour l’intégration continue avec les projets Xamarin._

Il est courant sur des projets de logiciels pour les développeurs de travailler en parallèle. À un moment donné, il est nécessaire d’intégrer tous ces flux parallèles d’un travail en une seule codebase constituant le produit final. Aux débuts du développement de logiciels, cette intégration a été effectuée à la fin d’un projet, ce qui était un processus difficile et risqué.

Intégration continue (CI) éviter ces complexités en fusionnant les modifications de chaque développeur dans la base de code commune en continu, généralement chaque fois que les développeurs vérifie les modifications dans le projet partagé référentiel de code. Chaque archivage déclenche une build automatisée et exécute des tests automatisés pour vérifier que le code qui vient d’être introduit n’a pas interrompre tout code existant.  De cette façon, CI met en évidence les erreurs et problèmes immédiatement et garantit que tous les membres de l’équipe restent à jour avec l’autre travail. Cela entraîne un code base stable et cohésif.

Systèmes d’intégration continue ont deux parties principales :

- **Contrôle de version** – Version contrôle (CV), également appelé contrôle de code source ou de la gestion du code source, consolide tous du code d’un projet dans un référentiel partagé unique et conserve un historique complet de chaque modification à chaque fichier. Ce dépôt, souvent appelé le *mainline* ou *master* créer une branche, contient le code source qui est finalement utilisé pour générer la production ou de mise en production de la version de l’application. Il existe de nombreux produits commerciaux pour cette tâche, qui permettent généralement plusieurs personnes pour dupliquer une copie du code dans des branches secondaire où ils peuvent apporter des modifications importantes ou effectuer des expériences sans risque pour la branche principale ou équipes et open source. Une fois que les modifications dans une branche secondaire sont validées, elles peuvent ensuite être tous ensemble à nouveau fusionnées avec la branche principale.
- **Serveur d’intégration continue** – le serveur d’intégration continue est responsable de la collecte de tous les artefacts d’un projet (code source, images, vidéos, bases de données, des tests automatisés, etc.), compilation de l’application et exécution de tests automatisés. Là encore, il existe plusieurs outils de serveur CI commerciales et open source.

Les développeurs ont généralement une copie de travail d’une ou plusieurs branches sur leurs stations de travail, où le travail est effectué au départ. Une fois qu’un ensemble approprié de travail est terminé, les modifications sont « cochées dans » ou « validées » à la branche appropriée, ce qui les propage sur les copies de travail des autres développeurs. Voici comment une équipe garantit qu’ils travaillent bien sur le même code.

Là encore, avec l’intégration continue, le fait de la validation des modifications, le serveur de CI générer le projet et exécuter des tests automatisés pour vérifier l’exactitude du code source. S’il existe des erreurs de build ou d’échecs de test, un serveur CI notifie le développeur responsable (par courrier électronique, messagerie instantanée, Twitter, Growl, etc.) afin qu’il ou elle peut corriger le problème. (Serveurs CI peuvent refuser même de la validation de cas de défaillance, ce qui est appelé un « archivage contrôlé ».)

Le diagramme suivant illustre ce processus :

[![](intro-to-ci-images/intro01-small.png "Ce diagramme illustre ce processus")](intro-to-ci-images/intro01.png#lightbox)

Les applications mobiles présentent des défis uniques pour l’intégration continue. Les applications peuvent nécessiter des capteurs tels que le GPS ou d’un appareil photo qui sont uniquement disponibles sur les appareils physiques. En outre, simulateurs ou les émulateurs sont uniquement une approximation du matériel et peuvent masquer ou masquer des problèmes. Au final, il est nécessaire tester une application mobile sur du vrai matériel pour être certain qu’il s’agit véritablement prêtes à l’emploi.

Le [App Center Test](https://docs.microsoft.com/appcenter/test-cloud) résout ce problème particulier en testant les applications directement sur des centaines d’appareils physiques. Les développeurs écrivent des tests d’acceptation automatisés autoriser pour le test d’interface utilisateur puissantes. Une fois que ces tests sont chargés à App Center, le serveur CI permettre les exécuter automatiquement dans le cadre d’un processus CI comme indiqué dans le diagramme suivant :

[![](intro-to-ci-images/intro02-small.png "Une fois que ces tests sont chargés à App Center, le serveur CI permettre les exécuter automatiquement dans le cadre d’un processus CI comme indiqué dans ce diagramme")](intro-to-ci-images/intro02.png#lightbox)

## <a name="components-of-continuous-integration"></a>Composants d’intégration continue

Il existe un écosystème complet d’outils commerciaux et open source conçu pour prendre en charge CI. Cette section décrit quelques-unes des causes plus courantes.

### <a name="version-control"></a>Gestion de version

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps et Team Foundation Server

[Azure DevOps](https://azure.microsoft.com/services/devops/) et [Team Foundation Server](https://visualstudio.microsoft.com/tfs/) (TFS) se trouvent les outils de collaboration de Microsoft pour l’intégration continue des services, suivi des tâches, agile planification et contrôle de version et les outils de reporting. Avec le contrôle de version, Azure DevOps et TFS peut travailler avec son propre système (Team Foundation Version Control ou TFVC) ou avec les projets hébergés sur GitHub.

- Azure DevOps fournit des services via le cloud. Leur principal avantage est qu’il ne nécessite pas un matériel dédié ou une infrastructure et accessibles à partir de n’importe où via les navigateurs web et des outils de développement courants tels que Visual Studio, rendant l’attrait pour les équipes qui sont répartis géographiquement . Il est gratuit pour les équipes de cinq développeurs ou moins, après les licences supplémentaires peuvent être achetées pour prendre en charge d’une équipe se développe.
- TFS est conçu pour les serveurs Windows en local et accessibles via un réseau local ou une connexion VPN à ce réseau. Leur principal avantage est que vous avez entièrement contrôlez la configuration des serveurs de build et que vous pouvez installer les logiciels supplémentaires ou des services sont nécessaires. TFS a une édition Express gratuite d’entrée de gamme, pour les petites équipes.

TFS et Azure DevOps sont étroitement intégré à Visual Studio et permettant aux développeurs d’effectuer de nombreux contrôle de version et les tâches de CI à partir d’un IDE unique sans quitter. Le plug-in Team Explorer Everywhere pour Eclipse (voir ci-dessous) est également disponible. Visual Studio pour Mac a [un aperçu de TFVC disponible](/visualstudio/mac/tf-version-control/).

[Les Pipelines de DevOps Azure](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/) a prise en charge directe pour les projets Xamarin, dans lequel vous créez une définition de build pour chaque plateforme que vous souhaitez cible (Android, iOS et Windows). La licence Xamarin appropriée est nécessaire pour chaque définition de build. Il est également possible de se connecter à une variable locale, TFS prenant en charge de Xamarin build server pour Azure DevOps à cet effet. Avec cette configuration, les builds qui sont en attente pour Azure DevOps seront déléguées au serveur local. Pour plus d’informations, reportez-vous à [Build et agents de publication](https://docs.microsoft.com/azure/devops/pipelines/agents/agents). Alternativement, vous pouvez utiliser un autre outil de génération tels que Jenkins ou de la ville de l’équipe.

Un résumé complet de toutes les fonctionnalités de gestion de cycle de vie des applications (ALM) de Visual Studio, Azure DevOps et Team Foundation Server, consultez [DevOps avec les applications Xamarin](https://docs.microsoft.com/visualstudio/cross-platform/application-lifecycle-management-alm-with-xamarin-apps).

#### <a name="team-explorer-everywhere"></a>Team Explorer Everywhere

[Team Explorer Everywhere](https://docs.microsoft.com/azure/devops/java/download-eclipse-plug-in/) apporte la puissance de Team Foundation Server et Azure DevOps aux équipes développant en dehors de Visual Studio. Il permet aux développeurs de se connecter aux projets d’équipe sur site ou dans le cloud depuis Eclipse ou le client de ligne de commande multiplateforme pour OS X et Linux. Team Explorer Everywhere offre complète d’accès au contrôle de version (dont Git), éléments de travail et générer des fonctions pour les plateformes non Windows.

#### <a name="git"></a>Git

[GIT](http://git-scm.com) est une solution de contrôle de version open source populaire qui a été développée pour gérer le code source pour le noyau Linux. C’est un système très rapide et flexible qui est souvent utilisé avec des projets logiciels de toutes tailles. Il s’adapte facilement à partir de l’unique aux développeurs un accès Internet médiocre grandes équipes qui couvrent le monde entier. GIT facilite également la création de branches d’une opération très simple, qui à son tour peut encourager les flux parallèles d’un développement en limitant les risques.

GIT peut fonctionner entièrement via les navigateurs web, ou [clients GUI](http://git-scm.com/downloads/guis) qui s’exécutent sur Linux, Mac OSX et Windows. C’est gratuit pour les référentiels publics ; référentiels privés requièrent un [payante](https://github.com/pricing).

Versions actuelles de Visual Studio pour Windows et Mac fournissent une prise en charge native pour Git. Microsoft fournit un [extension téléchargeable pour Git](http://visualstudiogallery.msdn.microsoft.com/abafc7d6-dcaa-40f4-8a5e-d6724bdb980c) pour les versions antérieures de Visual Studio. Comme indiqué ci-dessus, Azure DevOps et TFS peut utiliser Git pour le contrôle de version au lieu de TFVC.

#### <a name="subversion"></a>Subversion

[Subversion](http://subversion.apache.org) (SVN) est un système de contrôle de version, open source populaire qui a été utilisé depuis 2000. SVN s’exécute sur toutes les versions récentes d’OS X, Windows, FreeBSD, Linux et Unix. Visual Studio pour Mac a une prise en charge native pour SVN. Il existe des extensions tierces qui apportent de prise en charge SVN pour Visual Studio.

### <a name="continuous-integration-environments"></a>Environnements d’intégration continue

Configuration d’un environnement d’intégration continue signifie combinant un système de contrôle de version avec un service de build.  Dans le second cas, les deux plus courantes sont :

- [Les Pipelines Azure](https://docs.microsoft.com/azure/devops/pipelines/) est le système de génération de Azure DevOps et de TFS. Il est étroitement intégré à Visual Studio, il est pratique pour les développeurs déclencher le génère, ce qui permet d’exécuter des tests automatiquement et voir les résultats.
- Jenkins est un serveur d’intégration continue open source avec un riche écosystème de plug-ins pour prendre en charge tous les types de développement de logiciels. Il s’exécute sur Windows et Mac OS X. Jenkins n’est pas intégré à n’importe quel IDE spécifique. Au lieu de cela, il est configuré et géré via une interface web. Jenkins CI est également facile à installer et configurer ce qui le rend idéal pour les petites équipes.

Vous pouvez utiliser TFS/Azure DevOps en lui-même, ou vous pouvez utiliser Jenkins en combinaison avec TFS/Azure DevOps ou Git, comme décrit dans les sections suivantes.

#### <a name="azure-devops-and-team-foundation-server"></a>Azure DevOps et Team Foundation Server

Comme indiqué, Azure DevOps et Team Foundation Server fournit les deux version contrôler et générer des services. Services de build nécessitent toujours une licence Xamarin Business ou Enterprise pour chaque plateforme cible.

Avec Azure DevOps, vous créez une définition de build distinctes pour chaque plateforme cible et entrez il la licence appropriée. Une fois configurées, Azure DevOps sera exécution génère et teste dans le cloud. Consultez [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/) pour plus d’informations.

Avec Team Foundation Server, vous configurez un ordinateur de build comme suit pour les plateformes cibles spécifiques :

- **Android et Windows :** Installez Visual Studio et les outils Xamarin (pour Android et Windows à la fois) et configurez avec vos licences Xamarin. Il est également nécessaire de déplacer le Kit Android SDK à un emplacement partagé sur le serveur où l’agent de build TFS peut le trouver. Pour plus d’informations, consultez [TFVC configuration](https://docs.microsoft.com/azure/devops/repos/tfvc/overview).
- **iOS et Xamarin :** Installer Visual Studio et les outils Xamarin sur le serveur Windows avec la licence appropriée. Ensuite, installez Visual Studio pour Mac sur un ordinateur Mac OS X accessible via le réseau, ce qui permettra de servir d’un hôte de build et de créer le package d’application finale (IPA pour une application pour OS X, iOS).

Le diagramme suivant illustre cette topographie :

[![](intro-to-ci-images/intro03-small.png "Ce diagramme illustre cette topographie")](intro-to-ci-images/intro03.png#lightbox)

Il est également possible de lier un serveur TFS local à un projet Azure DevOps afin que les builds Azure DevOps sont déléguées au serveur local. Pour plus d’informations, consultez [Build et agents de publication](https://docs.microsoft.com/azure/devops/pipelines/agents/agents/).

#### <a name="azure-devops-and-jenkins"></a>Jenkins et azure DevOps

Si vous utilisez Jenkins pour générer vos applications, vous pouvez stocker votre code dans Azure DevOps ou Team Foundation Server et continuer à utiliser Jenkins pour vos builds d’intégration continue. Vous pouvez déclencher une build Jenkins quand vous placez le code au référentiel Git de votre projet d’équipe ou lorsque vous archivez code dans TFVC. Pour plus d’informations, consultez [Jenkins avec Azure DevOps](https://docs.microsoft.com/azure/devops/service-hooks/services/jenkins).

[![](intro-to-ci-images/intro04-small.png "Si vous utilisez Jenkins pour générer vos applications, vous pouvez stocker votre code dans Azure DevOps ou Team Foundation Server et continuer à utiliser Jenkins pour vos builds CI")](intro-to-ci-images/intro04.png#lightbox)

#### <a name="git-and-jenkins"></a>GIT et Jenkins

Un autre environnement CI commun peut être entièrement OS X en fonction. Ce scénario implique l’utilisation de Git pour le contrôle de code source et Jenkins pour le serveur de builds. Ces deux éléments sont en cours d’exécution sur un seul ordinateur Mac OS X avec Visual Studio pour Mac sont installés. Cela est très similaire à l’Azure DevOps + d’un environnement Jenkins abordées dans la section précédente :

[![](intro-to-ci-images/intro05-small.png "Cela est très similaire à l’Azure DevOps + d’un environnement Jenkins abordées dans la section précédente")](intro-to-ci-images/intro05.png#lightbox)

> [!IMPORTANT]
> **Jenkins est [ne pas pris en charge par Microsoft](~/cross-platform/troubleshooting/questions/xamarin-jenkins.md).**
