---
title: Contrôles d’image Watchos dans Xamarin
description: Ce document explique comment utiliser les contrôles d’image dans une application Watchos générée avec Xamarin. Il traite du contrôle WKInterfaceImage, de la méthode SetImage, de l’ajout d’images à une extension Watch, d’animations et bien plus encore.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: f1c21d64c8e1e271043e7d0b918f6033e21daac7
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91432992"
---
# <a name="watchos-image-controls-in-xamarin"></a>Contrôles d’image Watchos dans Xamarin

Watchos fournit un [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) contrôle pour afficher des images et des animations simples. Certains contrôles peuvent également avoir une image d’arrière-plan (par exemple, des boutons, des groupes et des contrôleurs d’interface).

![Apple Watch présentant une image ](image-images/image-walkway.png) ![ Apple Watch avec une animation simple](image-images/image-animation.png)
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Utilisez les images du catalogue de ressources pour ajouter des images aux applications du kit de surveillance.
Seules **@2x** les versions sont requises, car tous les appareils de surveillance ont des affichages de la rétine.

![Seules les versions 2x sont requises, car tous les appareils de surveillance ont des affichages de RETINE](image-images/asset-universal-sml.png)

Il est conseillé de s’assurer que les images elles-mêmes sont de la taille correcte pour l’affichage de la montre. *Évitez* d’utiliser des images de taille incorrecte (surtout des images de grande taille) et de les mettre à l’échelle pour les afficher sur la montre.

Vous pouvez utiliser les tailles de kit de surveillance (38mm et Watch 42mm) dans une image du catalogue de ressources pour spécifier différentes images pour chaque taille d’affichage.

![Vous pouvez utiliser les tailles du kit Watch 38mm et Watch 42mm dans une image du catalogue de ressources pour spécifier différentes images pour chaque taille d’affichage.](image-images/asset-watch-sml.png)

## <a name="images-on-the-watch"></a>Images sur la montre

Le moyen le plus efficace d’afficher des images consiste à les *inclure dans le projet d’application Watch* et à les afficher à l’aide de la `SetImage(string imageName)` méthode.

Par exemple, l’exemple [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog/) contient un certain nombre d’images ajoutées à un catalogue de composants dans le projet d’application Watch :

![L’exemple WatchKitCatalog a un certain nombre d’images ajoutées à un catalogue de composants dans le projet d’application Watch](image-images/asset-whale-sml.png)

Celles-ci peuvent être chargées et affichées efficacement sur la montre à l’aide `SetImage` du paramètre de nom de chaîne :

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Images d'arrière-plan

La même logique s’applique à `SetBackgroundImage (string imageName)` sur les `Button` `Group` classes, et `InterfaceController` . Les meilleures performances sont obtenues en stockant les images dans l’application Watch elle-même.

## <a name="images-in-the-watch-extension"></a>Images dans l’extension Watch

En plus de charger les images stockées dans l’application Watch elle-même, vous pouvez envoyer des images du bundle d’extension à l’application Watch pour l’afficher (ou vous pouvez télécharger des images à partir d’un emplacement distant et les afficher).

Pour charger des images à partir de l’extension Watch, créez des `UIImage` instances, puis appelez `SetImage` avec l' `UIImage` objet.

Par exemple, l’exemple [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) contient une image nommée **Bumblebee** dans le projet d’extension Watch :

![L’exemple WatchKitCatalog a une image nommée Bumblebee dans le projet d’extension Watch](image-images/asset-bumblebee-sml.png)

Le code suivant se traduira par :

- l’image en cours de chargement en mémoire, et
- affichée sur la montre.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```

## <a name="animations"></a>Animations

Pour animer un ensemble d’images, elles doivent toutes commencer par le même préfixe et avoir un suffixe numérique.

L’exemple [WatchKitCatalog](/samples/xamarin/ios-samples/watchos-watchkitcatalog) contient une série d’images numérotées dans le projet d’application Watch avec le préfixe de **bus** :

![L’exemple WatchKitCatalog contient une série d’images numérotées dans le projet d’application Watch avec le préfixe de bus](image-images/asset-bus-animation-sml.png)

Pour afficher ces images sous forme d’animation, commencez par charger l’image à l’aide `SetImage` de avec le nom de préfixe, puis appelez `StartAnimating` :

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Appelez `StopAnimating` sur le contrôle image pour arrêter la boucle d’animation :

```csharp
animatedImage.StopAnimating ();
```

<a name="cache"></a>

## <a name="appendix-caching-images-watchos-1"></a>Annexe : mise en cache d’images (Watchos 1)

> [!IMPORTANT]
> les applications Watchos 3 s’exécutent entièrement sur l’appareil. Les informations suivantes sont destinées aux applications Watchos 1 uniquement.

Si l’application utilise à plusieurs reprises une image qui est stockée dans l’extension (ou qui a été téléchargée), il est possible de mettre en cache l’image dans le stockage de l’espion, afin d’améliorer les performances des affichages suivants.

Utilisez la `WKInterfaceDevice` `AddCachedImage` méthode s pour transférer l’image vers la montre, puis utilisez `SetImage` avec le paramètre de nom d’image comme chaîne pour l’afficher :

```csharp
var device = WKInterfaceDevice.CurrentDevice;
using (var image = UIImage.FromBundle ("Bumblebee")) {
    if (!device.AddCachedImage (image, "Bumblebee")) {
            Console.WriteLine ("Image cache full.");
        } else {
            cachedImage.SetImage ("Bumblebee");
        }
    }
}
```

Vous pouvez interroger le contenu du cache d’images dans le code à l’aide de `WKInterfaceDevice.CurrentDevice.WeakCachedImages` .

### <a name="managing-the-cache"></a>Gestion du cache

La taille du cache est d’environ 20 Mo. Il est conservé entre les redémarrages d’application et, lorsqu’il est rempli, il vous incombe de supprimer des fichiers à l’aide `RemoveCachedImage` `RemoveAllCachedImages` des méthodes ou sur l' `WKInterfaceDevice.CurrentDevice` objet.

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Document image d’Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)