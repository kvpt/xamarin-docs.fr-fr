---
title: Microsoft Azure et Xamarin
description: Ce document contient des liens vers la documentation sur les Services connectés dans Visual Studio pour Mac, Azure Mobile Apps, l’authentification Active Directory et API Web.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: asb3993
ms.author: amburns
ms.date: 10/09/2017
ms.openlocfilehash: dd211fecad0bff58cb9ff6c6a99ae6a15c60eb7b
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67674988"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure et Xamarin

[![](images/evolve-mikej-azure-sml.png "Fonctionnalités des Services d’application Azure sont faciles à ajouter pour les applications Xamarin, y compris le stockage de données cloud et les notifications push multiplateforme")](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Evolve 2016 : Développement d’applications connectées à l’aide d’Azure et Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Services connectés dans Visual Studio pour Mac

La nouvelle [Services connectés](connected-services.md) fonctionnalité de Visual Studio pour Mac permet aux développeurs de rapidement et facilement ajouter des fonctionnalités Azure à des applications mobiles à partir de l’IDE. Actuellement disponible pour le test dans le canal Alpha.

## <a name="azure-app-services"></a>Azure App Services

Il existe une collection de [documentation Azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) qui vous guide dans le processus d’implémentation du [Azure Mobile Client](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin offre également un NuGet de messagerie Azure des packages pour [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) et [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) pour aider à implémenter des notifications push sur plusieurs plateformes.

Configurer vos applications sur le [portail des Services d’application Azure](https://portal.azure.com/) pour accéder aux applications mobiles, Web API, stockage et bien plus encore. En savoir plus sur [comment les services d’application sont différents](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) et regardez dans [ces vidéos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Authentification Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) peut être utilisé pour connecter les utilisateurs dans les applications Xamarin via la [Xamarin.Auth composant](https://www.nuget.org/packages/Xamarin.Auth/).
Les applications peuvent ensuite accéder des services supplémentaires tels qu’Office 365.

## <a name="webapi"></a>WebAPI

Microsoft Web API expose une interface REST qui peut être facilement utilisée par les applications Xamarin.
Vous pouvez facilement en place un [site Web Azure](https://trywebsites.azurewebsites.net/) et générer une application basée sur les API Web pour se connecter à des applications Xamarin.


###  <a name="introduction-to-web-servicescross-platformdata-cloudweb-servicesindexmd"></a>[Introduction aux Services Web](~/cross-platform/data-cloud/web-services/index.md)

Ce didacticiel explique comment intégrer REST, WCF et SOAP web des technologies de service avec les applications mobiles Xamarin. Il examine les différentes implémentations de service évalue les outils disponibles et les bibliothèques de les intégrer et fournit des exemples de modèles pour consommer des données de service. Enfin, il fournit une vue d’ensemble de la création d’un service web RESTful pour une utilisation avec une application mobile Xamarin.

## <a name="samples"></a>Exemples

Outre le [exemples de documentation](https://github.com/xamarin/mobile-samples/tree/master/Azure), les applications complètes suivantes illustrent diverses fonctionnalités de Azure incorporées dans des applications de Xamarin :

- [Sport](https://github.com/xamarin/Sport) – application de suivi des sports-league convivial qui utilise des notifications push et de stockage de données.
- [Instants](https://github.com/pierceboggan/Moments) – photo instantané de partage qui utilise le stockage Azure pour les images.
- [Xamarin CRM](https://github.com/xamarin/app-crm) – utilise les API Web pour le serveur principal.
- [MyShoppe](https://github.com/jamesmontemagno/MyShoppe) -Azure Mobile Apps.

- [eShop](https://github.com/dotnet-architecture/eShopOnContainers) – exemple pour le [Architecture série](https://www.microsoft.com/net/learn/architecture) des livres électroniques.
- [MyDriving](https://azure.microsoft.com/campaigns/mydriving/) – Azure + IoT échantillonnage à partir de la Build 2016.


## <a name="related-links"></a>Liens associés

- [Exemple de bibliothèque de classes portable Azure (par @paulbatum) (exemple)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Portail Azure](https://azure.microsoft.com/)
- [Client mobile pour Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
