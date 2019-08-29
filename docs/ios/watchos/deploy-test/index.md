---
title: Déploiement et test d’applications Watchos avec Xamarin
description: Ce document décrit comment déployer et tester des applications Watchos générées avec Xamarin. Il fournit une liste de vérification du déploiement, traite des ID d’application explicites et génériques, et examine les groupes d’applications.
ms.prod: xamarin
ms.assetid: 98257399-E9B3-4BAB-9204-0E89117DEA6D
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: d950ceb18bd13378ced06ec7257300fc5bf4504b
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120176"
---
# <a name="deploying-and-testing-watchos-apps-with-xamarin"></a>Déploiement et test d’applications Watchos avec Xamarin

## <a name="deployment-checklist"></a>Liste de vérification du déploiement

Que vous déployiez sur un espion de test ou que vous téléchargeiez sur l’App Store, vous devez effectuer les étapes de cette page:

- Dans le **Centre de développement iOS**:
  - Les [ID d’application](#App_IDs) ont été créés.
  - [Groupes d’applications](#App_Groups) configurés (si nécessaire).
  - Profil (s) de provisionnement de distribution créé (s)

- Dans votre solution:

  - Vérifiez que les [ID de Bundle et les références de projet](~/ios/watchos/get-started/installation.md) sont définis.
  - Vérifiez que vos icônes sont [correctement configurées](~/ios/watchos/app-fundamentals/icons.md).
  - Vérifiez que les numéros de version de Bundle correspondent dans tous les projets.
  - Configurez les groupes de **droits. plist** pour les groupes d’applications (si nécessaire).

- Suivez ensuite les instructions pour:
  - [Déployez sur un Apple Watch à des fins de test](~/ios/watchos/deploy-test/device.md), ou
  - [Téléchargez vers l’App Store](~/ios/watchos/deploy-test/appstore.md).

<a name="App_IDs"/>

## <a name="app-ids"></a>ID d’application

Comme indiqué dans les [instructions d’installation](~/ios/watchos/get-started/installation.md), les trois projets d’une application Watch comportent des ID groupés associés, tels que:

- Projet unifié Xamarin. iOS-`com.xamarin.WatchKitCatalog`
- Projet d’extension WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`
- Projet d’application Watch-`com.xamarin.WatchKitCatalog.watchkitapp`

Les trois projets nécessitent un profil de provisionnement de distribution correspondant, soit en utilisant des ID d’application explicites pour chacun, soit un ID d’application générique.

### <a name="explicit-app-ids"></a>ID d’application explicites

Créez un **ID d’application** pour chaque ID de Bundle de projet (qui ressemble à ceci dans le centre de développement iOS):

![ID d’offre groupée dans le centre de développement iOS](images/appids-specific-sml.png)

Lorsque vous créez ou configurez des ID d’application, n’oubliez pas d’activer les fonctionnalités spécifiques requises par votre application. Cela peut inclure des notifications push et des groupes d’applications.

Vous devrez créer un profil de provisionnement de distribution pour chaque ID d’application.

### <a name="wildcard-app-id"></a>ID d’application générique

Vous pouvez également créer un **ID d’application** générique qui correspond aux trois projets, tels que `com.xamarin.*`.

Notez que certaines fonctionnalités ne peuvent pas être utilisées avec un ID d’application générique (par exemple, les notifications push). Si votre application nécessite ces fonctionnalités, vous devez créer des ID d’application explicites.

Pour la distribution, il vous suffit de créer un profil de provisionnement de distribution pour l’ID d’application générique.

<a name="App_Groups" />

## <a name="app-groups"></a>App Groups

Vous pouvez utiliser un groupe d’applications pour partager des données entre votre application iOS et l’extension Watch. Vous devez vous assurer que votre solution dispose des éléments suivants:

- Configurez le **groupe d’applications** dans la section certificats du portail des développeurs Apple **, identificateurs & profils** .

- Les **groupes d’applications** activés (et fournissaient l' **ID du groupe d’applications**) à la *fois* dans l’application iOS et dans l' **ID d’application** de l’extension Watch et dans conversions **. plist**.

### <a name="certificates-identifiers--profiles"></a>Certificats, identificateurs & profils

Pour utiliser un groupe d’applications, créez une entrée dans l’écran **groupes d’applications** . Dans l’exemple ci-dessous, le nom du groupe est identique à celui de DNS inversé couramment utilisé pour les ID d’application, `group.` mais avec le préfixe (qui est requis):

![Identificateur](images/appgroups-new-sml.png)

Le groupe d’applications s’affiche alors dans la liste:

![Liste d’identificateurs](images/appgroups-setup-sml.png)

Une fois le groupe créé, il peut être référencé dans la configuration de votre **ID d’application** . N’oubliez pas de l’inclure à la fois sur l’application iOS et l' **ID d’application**de l’extension Watch.

![Configurations disponibles](images/appgroups-sml.png)

N’activez **pas** les groupes d’applications dans l’ID d’application Apple Watch. Il n’est pas nécessaire d’activer l’option sur la montre.

### <a name="entitlementsplist"></a>Entitlements.plist

Certaines fonctionnalités de l’application (par exemple, Les groupes d’applications) vous obligent à définir vos droits.
Double-cliquez pour modifier le fichier **habilitations. plist** dans ces projets:

- projet d’application iOS
- Projet d’extension Watch

.![Éditeur de droits. plist](images/entitlements-plist-sml.png)

N’activez **pas** les droits dans le projet d’application Watch. Il n’est pas nécessaire d’activer l’option sur la montre.

## <a name="related-links"></a>Liens associés

- [Guide d’envoi Apple WatchKit](https://developer.apple.com/app-store/watch/)
