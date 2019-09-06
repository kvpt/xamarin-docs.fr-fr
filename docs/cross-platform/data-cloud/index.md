---
title: Microsoft Azure et Xamarin
description: Ce document fournit des liens vers la documentation relative à Services connectés dans Visual Studio pour Mac, Azure Mobile Apps, Active Directory Authentication et WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: conceptdev
ms.author: crdun
ms.date: 10/09/2017
ms.openlocfilehash: 0979a0b65cc3d5b4944dadaf67aaa14cf1b3cf73
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70287600"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure et Xamarin

[![](images/evolve-mikej-azure-sml.png "Les fonctionnalités Azure App services sont faciles à ajouter aux applications Xamarin, y compris le stockage de données Cloud et les notifications push multiplateforme.")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016 : Développement d’applications connectées à l’aide d’Azure et de Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Services connectés dans Visual Studio pour Mac

La nouvelle fonctionnalité de [services connectés](connected-services.md) de Visual Studio pour Mac aide les développeurs à ajouter rapidement et facilement des fonctionnalités Azure aux applications mobiles à partir de l’IDE. Actuellement disponible pour le test dans le canal alpha.

## <a name="azure-app-services"></a>Services Azure App

Il existe une collection de [documentation azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) qui vous guide tout au long du processus d’implémentation du [client Azure mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin offre également des packages NuGet de messagerie Azure pour [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) et [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) afin de faciliter l’implémentation des notifications push sur les plateformes.

Configurez vos applications sur le [portail des Services Azure App](https://portal.azure.com/) pour accéder aux Mobile Apps, API Web, stockage et bien plus encore. Découvrez [Comment les services d’application sont différents](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) et regardez dans [ces vidéos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Authentification Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) peut être utilisé pour connecter des utilisateurs dans des applications Xamarin via le [composant Xamarin. auth](https://www.nuget.org/packages/Xamarin.Auth/).
Les applications peuvent ensuite accéder à des services supplémentaires, comme Office 365.

## <a name="webapi"></a>WebAPI

L’API Web de Microsoft expose une interface de type REST qui peut facilement être utilisée par les applications Xamarin.
Vous pouvez facilement créer un [site Web Azure](https://trywebsites.azurewebsites.net/) et créer une application basée sur WebAPI pour vous connecter aux applications Xamarin.


### <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Présentation des services Web](~/cross-platform/data-cloud/web-services/index.md)

Ce didacticiel explique comment intégrer les technologies de service Web REST, WCF et SOAP avec les applications mobiles Xamarin. Il examine diverses implémentations de service, évalue les outils et bibliothèques disponibles pour les intégrer et fournit des exemples de modèles pour l’utilisation des données de service. Enfin, il fournit une vue d’ensemble de base de la création d’un service Web RESTful pour une utilisation avec une application mobile Xamarin.

## <a name="samples"></a>Exemples

Outre les exemples de [documentation](https://github.com/xamarin/mobile-samples/tree/master/Azure), les applications complètes suivantes illustrent les différentes fonctionnalités Azure intégrées aux applications Xamarin :

- [Sport](https://github.com/xamarin/Sport) : application de suivi Sports-Ligue conviviale qui utilise le stockage de données & les notifications push.
- [Instants](https://github.com/pierceboggan/Moments) : le partage de photos instantanée qui utilise le stockage Azure pour les images.
- [XAMARIN CRM](https://github.com/xamarin/app-crm) – utilise l’API Web pour le serveur principal.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) -Sample pour la [série](https://www.microsoft.com/net/learn/architecture) d’ouvrages sur l’architecture.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – exemple Azure + IOT à partir de la build 2016.


## <a name="related-links"></a>Liens associés

- [Exemple de PCL Azure ( @paulbatumpar) (exemple)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portail Azure](https://azure.microsoft.com/)
- [Client mobile pour Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
