---
title: Microsoft Azure Mobile Apps
description: Ce document contient des liens vers des guides qui décrivent comment créer une application Xamarin connectée à Azure. Il traite de l’utilisation du composant Azure Xamarin, des utilisateurs et des notifications push.
ms.prod: xamarin
ms.assetid: 7B9AA8D9-C181-4C33-8AB0-2F56E4DBFC03
author: conceptdev
ms.author: crdun
ms.date: 04/02/2017
ms.openlocfilehash: 7d39615f20cdd52bb862d9ee998e9a45bbb6834e
ms.sourcegitcommit: d116fdcad9f5f0587ce64749cd8d791eee87f8aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72246534"
---
# <a name="microsoft-azure-mobile-apps"></a>Microsoft Azure Mobile Apps

> [!NOTE]
> Visual Studio App Center prend en charge les services intégrés de bout en bout et centraux au développement d’applications mobiles. Les développeurs peuvent utiliser les services de **création**, de **test** et de **distribution** pour configurer un pipeline d’intégration et de livraison continus. Une fois l’application déployée, les développeurs peuvent surveiller l’État et l’utilisation de leur application à l’aide des services d' **analyse** et de **diagnostic** , et s’adresser aux utilisateurs à l’aide du service **Push** . Les développeurs peuvent également tirer parti de l' **authentification** pour authentifier leurs utilisateurs et le service de **données** afin de conserver et de synchroniser les données d’application dans le Cloud.
> Si vous souhaitez intégrer des services Cloud à votre application mobile, inscrivez-vous avec [App Center](https://appcenter.ms/signup?utm_source=XamarinDocs&utm_medium=Azure&utm_campaign=docs) aujourd’hui.

_Exemples et téléchargements de code pour la documentation Portail Azure._

<!--
NOTE TO AUTHORS: this page is referenced from
https://azure.microsoft.com/develop/mobile/xamarin/
as https://developer xamarin com/guides/cross-platform/data-cloud/mobile-services/
A redirect has been put in place to /mobile-apps/ HOWEVER the /Resources/ .ZIP files are still located in /mobile-services/ so that the following permalinks don't break

The ZIPs in /Resources/ are also referenced by inbound links
Getting Started http://go.microsoft.com/fwlink/p/?LinkId=331359
Get started with data http://go.microsoft.com/fwlink/p/?LinkId=331302
Get started with push http://go.microsoft.com/fwlink/p/?LinkId=331303
Get started with authentication http://go.microsoft.com/fwlink/p/?LinkId=331328
Get started with Notification Hubs http://go.microsoft.com/fwlink/p/?LinkId=331329
Validate and modify data  http://go.microsoft.com/fwlink/p/?LinkId=331330
-->

Ces liens concernent la documentation Xamarin disponible sur le site Web [Azure Mobile Apps](https://docs.microsoft.com/azure/app-service-mobile/) .
Ajout de fonctionnalités Azure à une application Xamarin en téléchargeant le [client Azure mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/).

## <a name="working-with-the-xamarin-azure-component"></a>Utilisation du composant Azure Xamarin

Documentation générale [utilisation de la bibliothèque cliente Xamarin (composant)](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library) pour accomplir diverses tâches avec Azure Mobile Apps. Cette page contient un grand nombre d’exemples d’extraits de code, sans les explications détaillées et les exemples disponibles dans chacun des Articles de procédure pas à pas ci-dessous.

## <a name="getting-started"></a>Prise en main

Cet article fournit des instructions pas à pas pour mettre en service votre première application Xamarin Azure.
Il aborde la création d’une nouvelle application mobile Azure dans le portail, puis le téléchargement et l’exécution de l’application préconfigurée.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started/)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started/)
- [Xamarin.Forms](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started)

<!--
## Validate, Modify and Augment Data in Scripts

Demonstrates how to add server-side scripts to Azure Mobile Services data tables to implement server-side validation and other functionality.

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#errors)
-->

<!--
## Add Paging to Data

A quick example of paging large sets of data using Skip() and Take().

- [iOS](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
- [Android](https://azure.microsoft.com/documentation/articles/mobile-services-dotnet-how-to-use-client-library/#paging)
-->

## <a name="get-started-with-users"></a>Prise en main des utilisateurs

Fournit des instructions complètes pour la configuration et le codage d’un écran de connexion à l’aide d’Azure Mobile Services. Les fournisseurs d’authentification pris en charge sont Microsoft, Google, Facebook et Twitter.

- [iOS](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-ios-get-started-users/)
- [Android](https://azure.microsoft.com/documentation/articles/app-service-mobile-xamarin-android-get-started-users/)

## <a name="authorize-users-in-scripts"></a>Autoriser des utilisateurs dans des scripts

Exemple de code pour les principaux JavaScript

- [Todo.js](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/personal-table/tables/TodoItem.js#L38)

## <a name="get-started-with-push"></a>Prise en main de push

Suivez les instructions pour configurer les notifications push sur les sites Web Apple et Google, puis envoyez une notification push à partir d’Azure Mobile Services à un appareil.

- [iOS](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-ios-get-started-push)
- [Android](https://docs.microsoft.com/azure/app-service-mobile/app-service-mobile-xamarin-android-get-started-push)

## <a name="get-started-with-notification-hubs"></a>Prise en main de Notification Hubs

Suivez les instructions pour configurer les notifications push sur les sites Web Apple et Google, configurer le concentrateur de notification Azure, puis générer des notifications push sur des appareils.

- [iOS](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-ios-push-notification-apns-get-started)
- [Android](https://docs.microsoft.com/azure/notification-hubs/xamarin-notification-hubs-push-notifications-android-gcm)

## <a name="related-links"></a>Liens associés

- [GettingStarted (exemple)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GettingStarted)
- [GetStartedWithData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithData)
- [GetStartedWithUsers (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithUsers)
- [GetStartedWithPush (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/GetStartedWithPush)
- [NotificationHubs (exemple)](https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs)
- [Client Azure mobile](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)
- [Parcours d’apprentissage Azure Mobile Apps](https://azure.microsoft.com/documentation/learning-paths/appservice-mobileapps/)

<!--
- [ValidateModifyData (sample)](https://github.com/xamarin/mobile-samples/tree/master/Azure/ValidateModifyData)
-->
