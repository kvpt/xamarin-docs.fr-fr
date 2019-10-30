---
title: Utilisation de l’application parente Watchos dans Xamarin
description: Ce document décrit comment utiliser une application parente Watchos dans Xamarin. Il aborde les extensions d’application WatchKit, les applications iOS, le stockage partagé, et bien plus encore.
ms.prod: xamarin
ms.assetid: 9AD29833-E9CC-41A3-95D2-8A655FF0B511
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 08637fe13b28565e7c6ab1c89b291c6db3b81025
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001478"
---
# <a name="working-with-the-watchos-parent-application-in-xamarin"></a>Utilisation de l’application parente Watchos dans Xamarin

> [!IMPORTANT]
> L’accès à l’application parente à l’aide des exemples ci-dessous fonctionne uniquement sur les applications Watchos 1 Watch.

Il existe différentes façons de communiquer entre l’application espion et l’application iOS avec laquelle elle est regroupée :

- Les extensions Watch peuvent [appeler une méthode](#code) sur l’application parente qui s’exécute en arrière-plan sur l’iPhone.

- Les extensions Watch peuvent [partager un emplacement de stockage](#storage) avec l’application iPhone parente.

- Utilisation de la remise pour transmettre des données d’un coup d’œil ou d’une notification à l’application Watch, en envoyant l’utilisateur à un contrôleur d’interface spécifique dans l’application.

L’application parente est également parfois appelée « application de conteneur ».

<a name="code" />

## <a name="run-code"></a>Exécuter le code

La communication entre une extension Watch et l’application iPhone parente est illustrée dans l' [exemple GpsWatch](https://docs.microsoft.com/samples/xamarin/ios-samples/watchkit-gpswatch).
Votre extension Watch peut demander à l’application iOS parente d’effectuer un traitement en son nom à l’aide de la méthode `OpenParentApplication`.

Ceci est particulièrement utile pour les tâches longues (y compris les demandes réseau) : seule l’application iOS parente peut tirer parti du traitement en arrière-plan pour effectuer ces tâches et enregistrer les données récupérées dans un emplacement accessible à l’extension Watch.

### <a name="watch-kit-app-extension"></a>Extension d’application Watch Kit

Appelez la `WKInterfaceController.OpenParentApplication` dans l’extension de votre application Watch. Elle retourne une `bool` qui indique si la demande de méthode a été correctement envoyée ou non. Vérifiez le paramètre `error` dans le `Action` de réponse pour déterminer s’il s’est produit pendant l’exécution de la méthode dans l’application iPhone.

```csharp
WKInterfaceController.OpenParentApplication (new NSDictionary (), (replyInfo, error) => {
    if(error != null) {
        Console.WriteLine (error);
        return;
    }
    Console.WriteLine ("parent app responded");
    // do something with replyInfo[] dictionary
});
```

### <a name="ios-app"></a>Application iOS

Tous les appels à partir d’une extension d’application espion sont acheminés via la méthode `HandleWatchKitExtensionRequest` de l’application iPhone.
Si vous effectuez des demandes différentes dans l’application Watch, cette méthode devra interroger le dictionnaire `userInfo` pour déterminer comment traiter la demande.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate : UIApplicationDelegate
{
    // ... other AppDelegate methods
    public override void HandleWatchKitExtensionRequest
        (UIApplication application, NSDictionary userInfo, Action<NSDictionary> reply)
    {
        var status = 2;
        // do something in the background, and respond
        reply (new NSDictionary (
            "count", NSNumber.FromInt32 ((int)status),
            "value2", new NSString("some-info")
            ));
    }
}
```

<a name="storage" />

## <a name="shared-storage"></a>Stockage partagé

Si vous configurez un [groupe d’applications](~/ios/watchos/app-fundamentals/app-groups.md) , les extensions iOS 8 (y compris les extensions espion) peuvent partager des données avec l’application parente.

<a name="nsuserdefaults" />

### <a name="nsuserdefaults"></a>NSUserDefaults

Le code suivant peut être écrit à la fois dans l’extension de l’application espion et dans l’application iPhone parente afin qu’il puisse référencer un ensemble commun de `NSUserDefaults`:

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

<a name="files" />

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

Remarque : si le chemin d’accès est `null` Vérifiez la [configuration du groupe d’applications](~/ios/watchos/app-fundamentals/app-groups.md) pour vous assurer que les profils de configuration ont été correctement configurés et ont été téléchargés/installés sur l’ordinateur de développement.

Pour plus d’informations, consultez la documentation sur les [fonctionnalités du groupe d’applications](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

## <a name="wormholesharp"></a>WormHoleSharp

Un mécanisme Open source populaire pour Watchos 1 (basé sur [MMWormHole](https://github.com/mutualmobile/MMWormhole)) pour passer des données ou des commandes entre l’application parente et l’application Watch.

Vous pouvez configurer le ver géant à l’aide d’un groupe d’applications comme celui-ci dans votre application iOS et l’extension Watch :

```csharp
// AppDelegate (iOS) or InterfaceController (watch extension)
Wormhole wormHole;
// ...
wormHole = new Wormhole ("group.com.your-company.watchstuff", "messageDir");
```

Téléchargez la C# version [WormHoleSharp](https://github.com/Clancey/WormHoleSharp).

## <a name="related-links"></a>Liens associés

- [GpsWatch (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [WormHoleSharp (exemple)](https://github.com/Clancey/WormHoleSharp)
- [Référence WKInterfaceController d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/clm/WKInterfaceController/openParentApplication:reply:)
- [Partager des données avec votre application conteneur](https://developer.apple.com/library/ios/documentation/General/Conceptual/ExtensibilityPG/ExtensionScenarios.html)
