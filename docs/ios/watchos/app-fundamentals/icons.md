---
title: Utilisation de watchOS icônes dans Xamarin
description: Ce document décrit les différentes icônes nécessaires pour une application watchOS et comment configurer une solution à inclure ces icônes.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/26/2018
ms.openlocfilehash: 75b5d1f941921a84d96579a4b0d0666ae0c2522d
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864980"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Utilisation de watchOS icônes dans Xamarin

Solutions d’Apple Watch nécessitent deux jeux d’icônes :

* Les icônes d’application iOS qui seront affiche sur l’iPhone.
* Icônes d’Apple Watch seront affichera dans un cercle sur le menu espion et dans les écrans de notification. L’icône d’application watch apparaît également dans le [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) application iOS.

## <a name="apple-watch-icons"></a>Icônes d’Apple Watch

| | | |
|-|-|-|
|Icône d’application iOS|S’affiche sur l’iPhone et démarre l’application Parent|![icône de l’application iOS](icons-images/icon-ios.png)|
|Regardez l’icône de l’application|S’affiche sur l’écran d’accueil de l’Apple Watch|![icône de l’application watchOS](icons-images/icon-home.png)|
||S’affiche sur les notifications de surveillance|![icône de notification watchOS](icons-images/notification-icon.png)|
||S’affiche dans le [Apple Watch application iOS](~/ios/watchos/app-fundamentals/settings.md)|![icône d’application Watch iOS](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configuration de votre Solution

Pour garantir que votre application iOS et application watch affichent l’icône et le nom correct, suivez ces instructions pour chaque projet :

### <a name="ios-app"></a>Application iOS

Reportez-vous à la [guide des icônes d’Application iOS](~/ios/app-fundamentals/images-icons/app-icons.md) afin de garantir les icônes de votre application iOS sont correctement configurés.

#### <a name="infoplist"></a>Info.plist

La chaîne qui apparaît en regard de votre application watch dans le [Apple Watch paramètres application](~/ios/watchos/app-fundamentals/settings.md) est configuré dans le **Info.plist de l’application iOS**.

Vérifiez que votre **Info.plist** a un `CFBundleName` clé et la valeur (Remarque : cela est différent pour le `CFBundleDisplayName`, vous pouvez avoir les deux) :

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Application d’Apple Watch

Une fois votre [application parente](~/ios/watchos/app-fundamentals/parent-app.md) ses icônes a configuré, vous devez ajouter un catalogue de ressources d’icône application à l’application watch.

1. Avec le bouton droit sur le projet d’application Watch et sélectionnez **fichier > Ajouter > nouveau fichier... > iOS > catalogue de composants** pour ajouter un catalogue de ressources au projet.

    ![](icons-images/newasset.png "Ajouter un catalogue de ressources au projet")

2. Double-cliquez sur le **AppIcon.appiconset/Contents.json** fichier

    ![](icons-images/xcassets-iconset-sml.png "Le contenu AppIcon")

3. Ajoutez toutes les images watchOS, comme indiqué dans cette capture d’écran :

    [![](icons-images/appicons-sml.png "Ajoutez toutes les images watchOS, comme indiqué dans cette capture d’écran")](icons-images/appicons.png#lightbox)

    Reportez-vous à [instructions d’Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) pour les tailles requises (les dimensions sont également indiquées sur l’écran). N’oubliez pas que ces icônes seront automatiquement découpés à restituer dans un cercle.

    Votre liste d’icône doit ressembler à ceci :

    ![](icons-images/xcassets-complete-sml.png "La liste d’icône dans l’Explorateur de solutions")

4. Pour garantir le catalogue de composants est inclus dans l’application, ajoutez la clé suivante et que la valeur pour le **Info.plist de l’application Watch**:

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

Vous pouvez vérifier les icônes sont configurés correct en vérifiant la [Apple Watch paramètres application](~/ios/watchos/app-fundamentals/settings.md) dans le simulateur, iPhone ou en générant un [notification](~/ios/watchos/platform/notifications.md) et confirmation de l’icône s’affiche sur la notification écran.

> [!NOTE]
> Icônes ne peut pas avoir un canal alpha (l’application est rejetée lors de l’envoi de l’App Store si un canal alpha est présent). Vous pouvez vérifier si un canal alpha existe et supprimez-le [à l’aide de l’application de la version préliminaire sur Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Liens associés

- [Guide d’icône & images watchOS d’Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
