---
title: Microsoft Azure et Xamarin
description: Ce document est lié à la documentation sur les services connectés en studio visuel pour Mac, Azure Mobile Apps, Active Directory Authentication et WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 2b6dfeb0de0fac59556280609dbf870c23a9298b
ms.sourcegitcommit: 2a8eb8bce427e72d4e7edd06ce432e19f17dcdd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388523"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure et Xamarin

[![](images/evolve-mikej-azure-sml.png "Azure App Services features are easy to add to Xamarin apps, including cloud data storage and cross-platform push notifications")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016 : Développer des applications connectées à l’aide d’Azure et de Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Services connectés dans Visual Studio pour Mac

La nouvelle fonctionnalité [services connectés](connected-services.md) de Visual Studio pour Mac aide les développeurs à ajouter rapidement et facilement les fonctionnalités Azure aux applications mobiles à partir de l’IDE. Actuellement disponible pour les tests dans le canal Alpha.

## <a name="azure-app-services"></a>Azure App Services

Il existe une collection de [documentation Azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) qui vous guide à travers le processus de mise en œuvre du client mobile [Azure](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin propose également un forfait Azure Messaging NuGet pour [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) et [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) pour aider à implémenter des notifications push sur toutes les plateformes.

Configurez vos applications sur le [portail Azure App Services](https://portal.azure.com/) pour accéder aux applications mobiles, aux API Web, au Stockage et bien plus encore. Découvrez [comment les services d’applications sont différents](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) et regardez dans [ces vidéos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Authentification Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) peut être utilisé pour connecter les utilisateurs aux applications Xamarin. Les applications peuvent ensuite accéder à des services supplémentaires comme Office 365.

## <a name="webapi"></a>WebAPI (en)

L’API Web de Microsoft expose une interface DE LA REST qui peut être facilement consommée par les applications Xamarin.
Vous pouvez facilement faire tourner un [site Web Azure](https://trywebsites.azurewebsites.net/) et créer une application WebAPI pour vous connecter aux applications Xamarin.

### <a name="introduction-to-web-services"></a>[Introduction aux services Web](~/cross-platform/data-cloud/web-services/index.md)

Ce tutoriel introduit comment intégrer les technologies de service Web REST, WCF et SOAP avec les applications mobiles Xamarin. Il examine diverses implémentations de services, évalue les outils et les bibliothèques disponibles pour les intégrer et fournit des exemples de données sur les services. Enfin, il fournit un aperçu de base de la création d’un service Web RESTful pour la consommation avec une application mobile Xamarin.

## <a name="samples"></a>Exemples

En plus des échantillons de [documentation](https://github.com/xamarin/mobile-samples/tree/master/Azure), les applications complètes suivantes démontrent diverses fonctionnalités Azure incorporées dans les applications Xamarin :

- [Sport](https://github.com/xamarin/Sport) - application amicale de suivi de ligue sportive qui utilise le stockage de données & les notifications push.
- [Moments](https://github.com/pierceboggan/Moments) - partage de photos instantanées qui utilise Azure Storage pour les images.
- [Xamarin CRM](https://github.com/xamarin/app-crm) - utilise l’API Web pour le back-end.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) - Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) - échantillon pour la [série Architecture](https://www.microsoft.com/net/learn/architecture) de livres électroniques.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) - Azure - IoT échantillon de Build 2016.

## <a name="related-links"></a>Liens connexes

- [Azure PCL Exemple @paulbatum(par ) (échantillon)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure portal](https://azure.microsoft.com/)
- [Client mobile pour Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
