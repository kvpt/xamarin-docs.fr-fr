---
title: watchOS contrôles Image dans Xamarin
description: Ce document décrit comment utiliser des contrôles d’image dans une application watchOS avec Xamarin. Il aborde le contrôle WKInterfaceImage, la méthode SetImage, ajout d’images à une extension watch, animations et bien plus encore.
ms.prod: xamarin
ms.assetid: B741C207-3427-46F3-9C90-A52BF8933FA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 30bb8f096384dd9f76e208fbd3dbef73cf53bb33
ms.sourcegitcommit: 8ecfa339d0f3e7687977bfe4fc96448942690183
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67558692"
---
# <a name="watchos-image-controls-in-xamarin"></a>watchOS contrôles Image dans Xamarin

watchOS fournit un [`WKInterfaceImage`](xref:WatchKit.WKInterfaceImage) contrôle pour afficher des images et animations simples. Certains contrôles peuvent également avoir une image d’arrière-plan (par exemple, les boutons, les groupes et les contrôleurs d’interface).

![](image-images/image-walkway.png "Affichage d’une image de l’Apple Watch") ![](image-images/image-animation.png "Apple Watch avec animation simple")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

Images du catalogue asset permet d’ajouter des images pour les applications espion Kit.
Uniquement **@2x** versions n’est obligatoire, dans la mesure où tous les regarder les appareils ont Retina.

![](image-images/asset-universal-sml.png "Seulement 2 x sont requis, dans la mesure où tous les regarder les appareils ont Retina")

Il est conseillé de vérifier que les images proprement dites sont la taille correcte pour l’affichage d’espion. *Éviter* à l’aide d’images correctement dimensionnés (ceux qui sont particulièrement volumineux) et de mise à l’échelle pour les afficher sur la surveillance.

Vous pouvez utiliser les tailles d’espion Kit (38mm et 42mm) dans une image de catalogue asset pour spécifier des images différentes pour chaque taille d’affichage.

![](image-images/asset-watch-sml.png "Vous pouvez utiliser les tailles de Kit de Watch 38mm et 42mm dans une image de catalogue asset pour spécifier des images différentes pour chaque taille d’affichage")


## <a name="images-on-the-watch"></a>Images sur la surveillance

Le moyen le plus efficace d’afficher des images consiste à *les inclure dans le projet d’application watch* et les afficher à l’aide de la `SetImage(string imageName)` (méthode).

Par exemple, le [WatchKitCatalog](https://developer.xamarin.com/samples/WatchKitCatalog/) exemple comporte un nombre d’images ajoutées à un catalogue de ressources dans le projet d’application espion :

![](image-images/asset-whale-sml.png "L’exemple WatchKitCatalog a un nombre d’images ajoutées à un catalogue de ressources dans le projet d’application espion")

Ceux-ci peuvent être chargés et affichés sur la surveillance à l’aide d’efficacement `SetImage` avec le paramètre de nom de chaîne :

```csharp
myImageControl.SetImage("Whale");
myOtherImageControl.SetImage("Worry");
```

### <a name="background-images"></a>Images d'arrière-plan

La même logique s’applique pour le `SetBackgroundImage (string imageName)` sur le `Button`, `Group`, et `InterfaceController` classes. Meilleures performances sont obtenues en stockant les images dans l’application watch elle-même.


## <a name="images-in-the-watch-extension"></a>Images dans l’Extension Watch

Outre le chargement des images qui sont stockées dans l’application watch elle-même, vous pouvez envoyer des images à partir de l’offre d’extension à l’application watch pour l’affichage (ou vous pouvez télécharger des images à partir d’un emplacement distant et afficher ceux).

Pour charger des images à partir de l’extension watch, créez `UIImage` instances, puis appelez `SetImage` avec la `UIImage` objet.

Par exemple, le [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) exemple comporte une image nommée **Bumblebee** dans le projet d’extension espion :

![](image-images/asset-bumblebee-sml.png "L’exemple de WatchKitCatalog comporte une image nommée Bumblebee dans le projet d’extension watch")

Le code suivant entraîne :

- l’image en cours de chargement en mémoire, et
- affichée sur la surveillance.

```csharp
using (var image = UIImage.FromBundle ("Bumblebee")) {
    myImageControl.SetImage (image);
}
```


## <a name="animations"></a>Animations

Pour animer un ensemble d’images, elles doivent toutes commencent par le même préfixe et ont un suffixe numérique.

Le [WatchKitCatalog](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/) exemple comporte une série d’images numérotées dans le projet d’application watch avec le **Bus** préfixe :

![](image-images/asset-bus-animation-sml.png "L’exemple de WatchKitCatalog comporte une série d’images numérotées dans le projet d’application watch avec le préfixe de Bus")

Pour afficher ces images comme une animation, d’abord charger l’image en utilisant `SetImage` avec le nom de préfixe et appelez ensuite `StartAnimating`:

```csharp
animatedImage.SetImage ("Bus");
animatedImage.StartAnimating ();
```

Appeler `StopAnimating` sur le contrôle d’image pour arrêter l’animation en boucle :

```csharp
animatedImage.StopAnimating ();
```


<a name="cache" />

## <a name="appendix-caching-images-watchos-1"></a>Annexe : La mise en cache d’Images (watchOS 1)

> [!IMPORTANT]
> watchOS 3 applications s’exécutent entièrement sur l’appareil. Les informations suivantes sont pour les applications watchOS 1 uniquement.

Si l’application utilise à plusieurs reprises une image qui est stockée dans l’extension (ou a été téléchargée), il est possible de mettre en cache l’image dans le stockage de la surveillance, pour augmenter les performances pour les écrans suivants.

Utiliser le `WKInterfaceDevice`s `AddCachedImage` méthode pour transférer l’image à la surveillance, puis utilisez `SetImage` avec le paramètre de nom d’image sous forme de chaîne pour l’afficher :

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

Vous pouvez interroger le contenu du cache d’image à l’aide de code `WKInterfaceDevice.CurrentDevice.WeakCachedImages`.


### <a name="managing-the-cache"></a>Gestion du Cache

La taille d’environ 20 Mo de cache. Il est conservé entre les redémarrages de l’application, et lorsqu’elle est remplie il vous incombe d’effacer les fichiers à l’aide de `RemoveCachedImage` ou `RemoveAllCachedImages` méthodes sur le `WKInterfaceDevice.CurrentDevice` objet.



## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Doc d’Image d’Apple](https://developer.apple.com/documentation/watchkit/wkinterfaceimage)
