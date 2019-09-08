---
title: Microservices conteneurisés
description: Ce chapitre explique comment utiliser les microservices et les conteneurs pour créer des applications Cloud modernes, évolutives et fiables.
ms.prod: xamarin
ms.assetid: 5872ad92-04e0-4f1a-9691-79d5602f5683
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2017
ms.openlocfilehash: dc71da512519cdd7fcc56df1ff987ffbc1354663
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760391"
---
# <a name="containerized-microservices"></a>Microservices conteneurisés

Le développement d’applications client-serveur a donné lieu à la création d’applications à plusieurs niveaux qui utilisent des technologies spécifiques dans chaque niveau. Ces applications sont souvent appelées des applications *monolithiques* et sont empaquetées sur le matériel avant les pics de charge. Le principal inconvénient de cette approche de développement est le couplage étroit entre les composants au sein de chaque niveau, que les composants individuels ne peuvent pas être facilement mis à l’échelle et le coût des tests. Une mise à jour simple peut avoir des effets imprévus sur le reste du niveau, et par conséquent, une modification apportée à un composant d’application nécessite de retester et de redéployer son niveau entier.

En particulier en ce qui concerne l’âge du Cloud, les composants individuels ne peuvent pas être facilement mis à l’échelle. Une application monolithique contient des fonctionnalités spécifiques à un domaine et est généralement divisée par des couches fonctionnelles telles que le serveur frontal, la logique métier et le stockage de données. Une application monolithique est mise à l’échelle en clonant l’ensemble de l’application sur plusieurs ordinateurs, comme illustré à la figure 8-1.

![](containerized-microservices-images/monolithicapp.png "Approche de mise à l’échelle des applications monolithiques")

**Figure 8-1**: Approche de mise à l’échelle des applications monolithiques

## <a name="microservices"></a>Microservices

Les microservices offrent une approche différente du développement et du déploiement d’applications, une approche adaptée aux exigences en matière d’agilité, de mise à l’échelle et de fiabilité des applications Cloud modernes. Une application de microservices est décomposée en composants indépendants qui fonctionnent ensemble pour fournir les fonctionnalités globales de l’application. Le terme microservice insiste sur le fait que les applications doivent être composées de services suffisamment petits pour refléter des préoccupations indépendantes, afin que chaque microservice implémente une fonction unique. En outre, chaque microservice a des contrats bien définis afin que d’autres microservices puissent communiquer et partager des données avec lui. Les microservices sont des exemples typiques de microservices : les paniers, le traitement des stocks, les sous-systèmes d’achat et le traitement des paiements.

Les microservices peuvent être mis à l’échelle indépendamment, par rapport aux applications monolithiques géantes qui sont mises à l’échelle. Cela signifie qu’une zone fonctionnelle spécifique nécessitant plus de puissance de traitement ou de bande passante réseau pour prendre en charge la demande peut être mise à l’échelle, plutôt que de faire évoluer inutilement d’autres zones de l’application. La figure 8-2 illustre cette approche, où les microservices sont déployés et mis à l’échelle indépendamment, créant des instances de services sur plusieurs ordinateurs.

![](containerized-microservices-images/microservicesapp.png "Approche de mise à l’échelle des applications de microservices")

**Figure 8-2**: Approche de mise à l’échelle des applications de microservices

La montée en charge des microservices peut être quasiment instantanée, ce qui permet à une application de s’adapter à l’évolution des charges. Par exemple, un microservice unique dans la fonctionnalité Web d’une application peut être le seul microservice de l’application qui doit être mis à l’échelle pour gérer le trafic entrant supplémentaire.

Le modèle classique pour l’évolutivité des applications consiste à disposer d’un niveau à charge équilibrée et sans état avec un magasin de données externe partagé pour stocker les données persistantes. Les microservices avec état gèrent leurs propres données persistantes, généralement en les stockant localement sur les serveurs sur lesquels ils sont placés, afin d’éviter la surcharge liée à l’accès réseau et à la complexité des opérations entre les services. Cela permet le traitement le plus rapide possible des données et peut éliminer la nécessité de mettre en cache les systèmes. En outre, les microservices avec état évolutif partitionnent généralement les données entre leurs instances, afin de gérer la taille des données et le débit de transfert au-delà duquel un serveur unique peut prendre en charge.

Les microservices prennent également en charge les mises à jour indépendantes. Ce couplage faible entre les microservices fournit une évolution rapide et fiable des applications. Leur nature indépendante et distribuée prend en charge les mises à jour propagées, où seul un sous-ensemble d’instances d’un même microservice sera mis à jour à un moment donné. Par conséquent, si un problème est détecté, une mise à jour de bogue peut être restaurée avant que toutes les instances soient mises à jour avec le code ou la configuration défectueux. De même, les microservices utilisent généralement le contrôle de version de schéma, de sorte que les clients voient une version cohérente lors de l’application des mises à jour, quelle que soit l’instance de microservice utilisée pour la communication.

Par conséquent, les applications de microservice présentent de nombreux avantages par rapport aux applications monolithiques :

- Chaque microservice est relativement petit, facile à gérer et évoluer.
- Chaque microservice peut être développé et déployé indépendamment des autres services.
- Chaque microservice peut être mis à l’échelle indépendamment. Par exemple, un service de catalogue ou un service de panier d’achat devra peut-être être mis à l’échelle plus qu’un service de tri. Par conséquent, l’infrastructure qui en résulte utilise plus efficacement des ressources lors de la montée en charge.
- Chaque microservice isole tous les problèmes. Par exemple, s’il existe un problème dans un service, il n’affecte que ce service. Les autres services peuvent continuer à gérer les demandes.
- Chaque microservice peut utiliser les dernières technologies. Étant donné que les microservices sont autonomes et exécutés côte à côte, les dernières technologies et infrastructures peuvent être utilisées, au lieu d’être forcées à utiliser une infrastructure plus ancienne qui peut être utilisée par une application monolithique.

Toutefois, une solution basée sur des microservices présente également des inconvénients potentiels :

- Le choix de la façon de partitionner une application en microservices peut être difficile, car chaque microservice doit être complètement autonome, de bout en bout, y compris la responsabilité de ses sources de données.
- Les développeurs doivent implémenter la communication entre les services, ce qui ajoute de la complexité et de la latence à l’application.
- Les transactions atomiques entre plusieurs microservices ne sont généralement pas possibles. Par conséquent, les besoins de l’entreprise doivent adopter une cohérence éventuelle entre les microservices.
- En production, il existe une complexité opérationnelle dans le déploiement et la gestion d’un système compromis de nombreux services indépendants.
- La communication directe entre les clients et les microservices peut compliquer la refactorisation des contrats des microservices. Par exemple, dans le temps, la façon dont le système est partitionné en services peut être modifiée. Un service unique peut être fractionné en deux ou plusieurs services, et deux services peuvent être fusionnés. Lorsque les clients communiquent directement avec les microservices, ce travail de refactorisation peut rompre la compatibilité avec les applications clientes.

## <a name="containerization"></a>Mise en conteneur

La mise en conteneur est une approche du développement de logiciels dans laquelle une application et son ensemble de dépendances, ainsi que sa configuration d’environnement abstraite en tant que fichiers manifestes de déploiement, sont regroupés comme une image conteneur, testée en tant qu’unité et déployé sur un système d’exploitation hôte.

Un conteneur est un environnement d’exploitation portable, contrôlé par les ressources et isolé, dans lequel une application peut s’exécuter sans toucher aux ressources d’autres conteneurs ou à l’hôte. Par conséquent, un conteneur se présente et agit comme un ordinateur physique nouvellement installé ou un ordinateur virtuel.

Il existe de nombreuses similitudes entre les conteneurs et les machines virtuelles, comme illustré dans la figure 8-3.

![](containerized-microservices-images/containersvsvirtualmachines.png "Approche de mise à l’échelle des applications de microservices")

**Figure 8-3**: Comparaison entre les machines virtuelles et les conteneurs

Un conteneur exécute un système d’exploitation, a un système de fichiers et est accessible sur un réseau comme s’il s’agissait d’une machine physique ou virtuelle. Toutefois, la technologie et les concepts utilisés par les conteneurs sont très différents de ceux des machines virtuelles. Les machines virtuelles incluent les applications, les dépendances requises et un système d’exploitation invité complet. Les conteneurs incluent l’application et ses dépendances, mais partagent le système d’exploitation avec d’autres conteneurs, s’exécutant en tant que processus isolés sur le système d’exploitation hôte (à part les conteneurs Hyper-V qui s’exécutent à l’intérieur d’une machine virtuelle spéciale par conteneur). Par conséquent, les conteneurs partagent des ressources et nécessitent généralement moins de ressources que les machines virtuelles.

L’avantage d’une approche de développement et de déploiement orientée conteneur est qu’elle élimine la plupart des problèmes qui résultent de la configuration d’environnements incohérents et des problèmes qui y sont liés. En outre, les conteneurs permettent une fonctionnalité de mise à l’échelle des applications rapide en instanciant de nouveaux conteneurs en fonction des besoins.

Les concepts clés lors de la création et de l’utilisation des conteneurs sont les suivants :

- Hôte de conteneur : Ordinateur physique ou virtuel configuré pour héberger des conteneurs. L’hôte de conteneur exécute un ou plusieurs conteneurs.
- Image de conteneur : Une image se compose d’une Union de systèmes de fichiers en couches empilés les uns sur les autres et est la base d’un conteneur. Une image n’a pas d’État et ne change jamais lorsqu’elle est déployée dans différents environnements.
- Conteneur Un conteneur est une instance d’exécution d’une image.
- Image du système d’exploitation du conteneur : Les conteneurs sont déployés à partir d’images. L’image du système d’exploitation du conteneur est la première couche dans potentiellement des couches d’images qui constituent un conteneur. Un système d’exploitation de conteneur est immuable et ne peut pas être modifié.
- Référentiel de conteneurs : Chaque fois qu’une image de conteneur est créée, l’image et ses dépendances sont stockées dans un référentiel local. Ces images peuvent être réutilisées plusieurs fois sur l’hôte de conteneur. Les images de conteneur peuvent également être stockées dans un registre public ou privé, tel qu’un [hub d’ancrage](https://hub.docker.com/), afin qu’elles puissent être utilisées sur différents hôtes de conteneur.

Les entreprises adoptent de plus en plus de conteneurs lors de l’implémentation d’applications basées sur des microservices, et l’arrimeur est devenu l’implémentation de conteneur standard adoptée par la plupart des plateformes logicielles et des fournisseurs de Cloud.

L’application de référence eShopOnContainers utilise Dockr pour héberger quatre microservices back-end en conteneur, comme illustré dans la figure 8-4.

![](containerized-microservices-images/microservicesarchitecture.png "microservices principaux de l’application de référence eShopOnContainers")

**Figure 8-4**: microservices back-end de l’application de référence eShopOnContainers

L’architecture des services principaux dans l’application de référence est décomposée en plusieurs sous-systèmes autonomes sous la forme de microservices et de conteneurs de collaboration. Chaque microservice fournit une seule zone de fonctionnalités : un service d’identité, un service de catalogue, un service de tri et un service de panier.

Chaque microservice possède sa propre base de données, ce qui lui permet d’être entièrement découplé des autres microservices. Si nécessaire, la cohérence entre les bases de données de différents microservices est obtenue à l’aide d’événements au niveau de l’application. Pour plus d’informations, consultez [communication entre les microservices](#communication_between_microservices).

Pour plus d’informations sur l’application de référence [, consultez microservices .net : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

<a name="communication_between_client_and_microservices" />

## <a name="communication-between-client-and-microservices"></a>Communication entre le client et les microservices

L’application mobile eShopOnContainers communique avec les microservices back-end en conteneur à l’aide de la communication directe entre les clients et les *microservices* , illustrée dans la figure 8-5.

![](containerized-microservices-images/directclienttomicroservicecommunication.png "Approche de mise à l’échelle des applications de microservices")

**Figure 8-5**: Communication directe de client à microservice

Avec la communication directe entre les clients et les microservices, l’application mobile envoie des demandes à chaque microservice directement par le biais de son point de terminaison public, avec un port TCP différent pour chaque microservice. En production, le point de terminaison est généralement mappé à l’équilibreur de charge du microservice, qui distribue les demandes sur les instances disponibles.

> [!TIP]
> Envisagez d’utiliser la communication de la passerelle API. La communication directe entre les clients et les microservices peut présenter des inconvénients lors de la création d’une application basée sur des microservices complexes et de grande envergure, mais elle est plus que suffisante pour une petite application. Lors de la conception d’une application basée sur un microservice de grande taille avec des dizaines de microservices, envisagez d’utiliser la communication de la passerelle API. Pour plus d’informations, [consultez microservices .net : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

<a name="communication_between_microservices" />

## <a name="communication-between-microservices"></a>Communication entre microservices

Une application basée sur des microservices est un système distribué, qui peut s’exécuter sur plusieurs ordinateurs. Chaque instance de service est généralement un processus. Par conséquent, les services doivent interagir à l’aide d’un protocole de communication inter-processus, tel que les protocoles HTTP, TCP, Advance Message Queueing Protocol (AMQP) ou binaires, en fonction de la nature de chaque service.

Les deux approches courantes pour la communication entre les microservices et les microservices sont la communication REST basée sur HTTP lors de l’interrogation de données et la messagerie asynchrone légère lors de la communication des mises à jour sur plusieurs microservices.

Les communications asynchrones basées sur les événements sont critiques lors de la propagation des modifications sur plusieurs microservices. Avec cette approche, un microservice publie un événement quand un événement notable se produit, par exemple lorsqu’il met à jour une entité métier. D’autres microservices s’abonnent à ces événements. Ensuite, lorsqu’un microservice reçoit un événement, il met à jour ses propres entités métier, ce qui peut à son tour entraîner la publication d’un plus grand nombre d’événements. Cette fonctionnalité de publication-abonnement est généralement effectuée avec un bus d’événements.

Un bus d’événements permet la communication de publication-abonnement entre les microservices, sans exiger que les composants soient explicitement conscients l’un de l’autre, comme illustré dans la figure 8-6.

![](containerized-microservices-images/eventbus.png "Publier-s’abonner avec un bus d’événements")

**Figure 8-6 :** Publier-s’abonner avec un bus d’événements

Du point de vue de l’application, le bus d’événements est simplement un canal de publication-abonnement exposé via une interface. Toutefois, la façon dont le bus d’événements est implémenté peut varier. Par exemple, une implémentation du bus d’événements peut utiliser RabbitMQ, Azure Service Bus ou d’autres bus de service tels que NServiceBus et MassTransit. La figure 8-7 montre comment un bus d’événements est utilisé dans l’application de référence eShopOnContainers.

![](containerized-microservices-images/microservicesarchitecturewitheventbus.png "Communication asynchrone pilotée par les événements dans l’application de référence")

**Figure 8-7 :** Communication asynchrone pilotée par les événements dans l’application de référence

Le bus d’événements eShopOnContainers, implémenté à l’aide de RabbitMQ, offre une fonctionnalité de publication-abonnement asynchrone d’un à plusieurs. Cela signifie qu’après la publication d’un événement, plusieurs abonnés peuvent écouter le même événement. La figure 8-9 illustre cette relation.

![](containerized-microservices-images/eventdrivencommunication.png "Communication un-à-plusieurs")

**Figure 8-9**: Communication un-à-plusieurs

Cette approche de communication un-à-plusieurs utilise des événements pour mettre en œuvre des transactions commerciales qui s’étendent sur plusieurs services, garantissant une cohérence éventuelle entre les services. Une transaction cohérente est constituée d’une série d’étapes distribuées. Par conséquent, lorsque le microservice de profil utilisateur reçoit la commande UpdateUser, il met à jour les informations de l’utilisateur dans sa base de données et publie l’événement UserUpdated dans le bus d’événements. Le microservice de panier et le microservice de commande se sont abonnés pour recevoir cet événement et, en réponse, mettent à jour leurs informations d’acheteur dans leurs bases de données respectives.

> [!NOTE]
> Le bus d’événements eShopOnContainers, implémenté à l’aide de RabbitMQ, est destiné à être utilisé uniquement comme preuve de concept. Pour les systèmes de production, d’autres implémentations de bus d’événements doivent être prises en compte.

Pour plus d’informations sur l’implémentation du bus [d’événements, consultez microservices .net : Architecture pour les applications .NET en conteneurs](https://aka.ms/microservicesebook).

## <a name="summary"></a>Récapitulatif

Les microservices offrent une approche du développement et du déploiement d’applications adaptées aux exigences en termes d’agilité, de mise à l’échelle et de fiabilité des applications Cloud modernes. L’un des principaux avantages des microservices est qu’ils peuvent être mis à l’échelle de manière indépendante, ce qui signifie qu’il est possible de mettre à l’échelle une zone fonctionnelle spécifique nécessitant davantage de puissance de traitement ou de bande passante réseau pour prendre en charge la demande, sans mettre à l’échelle les zones de l’application qui n’a pas de demande accrue.

Un conteneur est un environnement d’exploitation portable, contrôlé par les ressources et isolé, dans lequel une application peut s’exécuter sans toucher aux ressources d’autres conteneurs ou à l’hôte. Les entreprises adoptent de plus en plus de conteneurs lors de l’implémentation d’applications basées sur des microservices, et l’arrimeur est devenu l’implémentation de conteneur standard adoptée par la plupart des plateformes logicielles et des fournisseurs de Cloud.

## <a name="related-links"></a>Liens associés

- [Télécharger le livre électronique (PDF de 2 Mo)](https://aka.ms/xamarinpatternsebook)
- [eShopOnContainers (GitHub) (exemple)](https://github.com/dotnet-architecture/eShopOnContainers)
