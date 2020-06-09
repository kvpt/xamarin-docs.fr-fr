---
title: Utilisation de l’application parente Watchos dans Xamarin
description: Ce document décrit comment utiliser une application parente Watchos dans Xamarin. Il aborde les extensions d’application Watchos, les applications iOS, le stockage partagé, et bien plus encore.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 49f2bdf63c286464073308cd1f17239692aa2395
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567331"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Utilisation de l’application parente Watchos dans Xamarin

Il existe différentes façons de communiquer entre l’application espion et l’application iOS avec laquelle elle est regroupée :

- Les applications de visionneuse peuvent [exécuter du code](#run-code) sur l’application parente sur l’iPhone.

- Les extensions Watch peuvent [partager un emplacement de stockage](#shared-storage) avec l’application iPhone parente.

- Utilisez la remise pour transmettre des données d’une notification à l’application Watch, en envoyant l’utilisateur à un contrôleur d’interface spécifique dans l’application.

L’application parente est également parfois appelée « application de conteneur ».

## <a name="run-code"></a>Exécuter le code

Ces deux exemples montrent comment utiliser `WCSession` pour exécuter du code et envoyer des messages entre une application espion et l’iPhone couplé :

- [Surveiller la connectivité](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchconnectivity/)
- [SimpleWatchConnectivity](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-simplewatchconnectivity/) 

## <a name="shared-storage"></a>Stockage partagé

Si vous configurez un [groupe d’applications](~/ios/watchos/app-fundamentals/app-groups.md) , les extensions iOS 8 (y compris les extensions espion) peuvent partager des données avec l’application parente.

### <a name="nsuserdefaults"></a>NSUserDefaults

Le code suivant peut être écrit à la fois dans l’extension de l’application espion et dans l’application iPhone parente afin de pouvoir faire référence à un ensemble commun de `NSUserDefaults` :

```csharp
NSUserDefaults shared = new NSUserDefaults(
        "group.com.your-company.watchstuff",
        NSUserDefaultsType.SuiteName);

// set values
shared.SetInt (2, "count");
shared.Synchronize ();

// get values
shared.Synchronize ();
var count = shared.IntForKey ("count");
```

<a name="files"></a>

### <a name="files"></a>Fichiers

L’extension d’application et de visionneuse iOS peut également partager des fichiers à l’aide d’un chemin d’accès de fichier commun.

```csharp
var FileManager = new NSFileManager ();
var appGroupContainer =
            FileManager.GetContainerUrl ("group.com.your-company.watchstuff");
var appGroupContainerPath = appGroupContainer.Path;
Console.WriteLine ("agcpath: " + appGroupContainerPath);
// use the path to create and update files
```

Remarque : si le chemin d’accès est `null` ensuite, vérifiez la [configuration du groupe d’applications](~/ios/watchos/app-fundamentals/app-groups.md) pour vous assurer que les profils d’approvisionnement ont été correctement configurés et ont été téléchargés/installés sur l’ordinateur de développement.

Pour plus d’informations, consultez la documentation sur les [fonctionnalités du groupe d’applications](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="related-links"></a>Liens connexes

- [Référence WKInterfaceController d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Partager des données avec votre application conteneur](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
