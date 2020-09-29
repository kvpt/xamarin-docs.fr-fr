---
title: Microsoft Azure et Xamarin
description: Ce document fournit des liens vers la documentation relative à Services connectés dans Visual Studio pour Mac, Azure Mobile Apps, Active Directory Authentication et WebAPI.
ms.prod: xamarin
ms.assetid: 7b9aa8d9-c181-4c33-8ab0-2f56e4dbfc04
author: davidortinau
ms.author: daortin
ms.date: 10/09/2017
ms.openlocfilehash: 9439b33c0e2c8458fd1308003aa9cd93148b6eac
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457313"
---
# <a name="microsoft-azure-and-xamarin"></a>Microsoft Azure et Xamarin

[![Les fonctionnalités Azure App services sont faciles à ajouter aux applications Xamarin, y compris le stockage de données Cloud et les notifications push multiplateforme.](images/evolve-mikej-azure-sml.png)](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

[Évolution de 2016 : développement d’applications connectées à l’aide d’Azure et de Xamarin](https://evolve.xamarin.com/session/56ec886fde91c6253c277bc6)

## <a name="connected-services-in-visual-studio-for-mac"></a>Services connectés dans Visual Studio pour Mac

La nouvelle fonctionnalité de [services connectés](/visualstudio/mac/connected-services) de Visual Studio pour Mac aide les développeurs à ajouter rapidement et facilement des fonctionnalités Azure aux applications mobiles à partir de l’IDE. Actuellement disponible pour le test dans le canal alpha.

## <a name="azure-app-services"></a>Azure App Services

Il existe une collection de [documentation azure Mobile Apps](~/cross-platform/data-cloud/mobile-apps.md) qui vous guide tout au long du processus d’implémentation du [client Azure mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).
Xamarin offre également des packages NuGet de messagerie Azure pour [iOS](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.iOS/) et [Android](https://www.nuget.org/packages/Xamarin.Azure.NotificationHubs.Android/) afin de faciliter l’implémentation des notifications push sur les plateformes.

Configurez vos applications sur le [portail des Services Azure App](https://portal.azure.com/) pour accéder aux Mobile Apps, API Web, stockage et bien plus encore. Découvrez [Comment les services d’application sont différents](https://azure.microsoft.com/updates/whats-new-with-azure-app-service/) et regardez dans [ces vidéos de Microsoft](https://azure.microsoft.com/campaigns/azure-march-announcement/).

## <a name="active-directory-authentication"></a>Authentification Active Directory

[Azure Active Directory](~/cross-platform/data-cloud/active-directory/index.md) peut être utilisé pour connecter des utilisateurs à des applications Xamarin. Les applications peuvent ensuite accéder à des services supplémentaires, comme Office 365.

## <a name="webapi"></a>WebAPI

L’API Web de Microsoft expose une interface de type REST qui peut facilement être utilisée par les applications Xamarin.
Vous pouvez facilement créer un [site Web Azure](https://trywebsites.azurewebsites.net/) et créer une application basée sur WebAPI pour vous connecter aux applications Xamarin.

### <a name="introduction-to-web-services"></a>[Présentation des services Web](~/cross-platform/data-cloud/web-services/index.md)

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

- [Exemple de PCL Azure (par @paulbatum ) (exemple)](https://github.com/paulbatum/mobile-services-xamarin-pcl)
- [Azure portal](https://azure.microsoft.com/)
- [Client mobile pour Xamarin (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)