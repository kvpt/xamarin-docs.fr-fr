---
title: Utilisation des icônes Watchos dans Xamarin
description: Ce document décrit les différentes icônes nécessaires pour une application Watchos et explique comment configurer une solution pour inclure ces icônes.
ms.prod: xamarin
ms.assetid: EE3D45BD-8091-4C04-BA83-371371D8BEB9
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/26/2018
ms.openlocfilehash: b3968f211ff5445e8b704bbd7897f4c46ecc2be9
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292593"
---
# <a name="working-with-watchos-icons-in-xamarin"></a>Utilisation des icônes Watchos dans Xamarin

Apple Watch solutions requièrent deux ensembles d’icônes :

- Icônes de l’application iOS qui s’affichent sur l’iPhone.
- Apple Watch icônes qui seront affichées dans un cercle dans le menu espion et dans les écrans de notification. L’icône d’application espion apparaît également dans l’application [Apple Watch](~/ios/watchos/app-fundamentals/settings.md) iOS.

## <a name="apple-watch-icons"></a>Icônes de Apple Watch

| | | |
|-|-|-|
|Icône d’application iOS|Apparaît sur l’iPhone et démarre l’application parente|![icône d’application iOS](icons-images/icon-ios.png)|
|Icône regarder l’application|Apparaît sur la Apple Watch écran d’accueil|![icône d’application Watchos](icons-images/icon-home.png)|
||Apparaît sur les notifications de surveillance|![icône de notification espionneos](icons-images/notification-icon.png)|
||Apparaît dans l' [application Apple Watch iOS](~/ios/watchos/app-fundamentals/settings.md)|![icône d’application espion iOS](icons-images/watch-app-sml.png)|

## <a name="configuring-your-solution"></a>Configuration de votre solution

Pour vous assurer que votre application iOS et l’application Watch affichent le nom et l’icône corrects, suivez ces instructions pour chaque projet :

### <a name="ios-app"></a>Application iOS

Reportez-vous au Guide des icônes de l' [application iOS](~/ios/app-fundamentals/images-icons/app-icons.md) pour vérifier que les icônes de votre application IOS sont correctement configurées.

#### <a name="infoplist"></a>Info.plist

La chaîne qui apparaît en regard de votre application Watch dans l' [application paramètres de Apple Watch](~/ios/watchos/app-fundamentals/settings.md) est configurée dans le fichier **info. plist de l’application iOS**.

Vérifiez que votre fichier **info. plist** contient une clé et une `CFBundleName` valeur (Remarque : Ceci est différent du `CFBundleDisplayName`, vous pouvez avoir les deux) :

```xml
<key>CFBundleName</key>
<string>Your App Name</string>
```

### <a name="apple-watch-app"></a>Application Apple Watch

Une fois que vous avez configuré les icônes de votre [application parente](~/ios/watchos/app-fundamentals/parent-app.md) , vous devez ajouter une icône d’application catalogue de ressources à l’application espion.

1. Cliquez avec le bouton droit sur le projet d’application Watch et sélectionnez **fichier > ajouter > nouveau fichier... > iOS > Catalogue de ressources** pour ajouter un catalogue de composants au projet.

    ![](icons-images/newasset.png "Ajouter un catalogue de composants au projet")

2. Double-cliquez sur le fichier **AppIcon. appiconset/contents. JSON**

    ![](icons-images/xcassets-iconset-sml.png "Contenu de l’AppIcon")

3. Ajoutez toutes les images Watchos, comme illustré dans cette capture d’écran :

    [![](icons-images/appicons-sml.png "Ajoutez toutes les images Watchos, comme illustré dans cette capture d’écran.")](icons-images/appicons.png#lightbox)

    Reportez-vous aux [instructions relatives aux icônes Apple](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/menu-icons/) pour les tailles requises (les dimensions s’affichent également à l’écran). N’oubliez pas que ces icônes seront automatiquement découpées pour être rendues dans un cercle.

    La liste des icônes doit ressembler à ceci :

    ![](icons-images/xcassets-complete-sml.png "Liste des icônes dans le Explorateur de solutions")

4. Pour vous assurer que le catalogue de ressources est inclus dans l’application, ajoutez la clé et la valeur suivantes au fichier **info. plist de l’application Watch**:

    ```xml
    <key>XSAppIconAssets</key>
    <string>Images.xcassets/AppIcon.appiconset</string>
    ```

Vous pouvez vérifier que les icônes sont configurées correctement en vérifiant l' [application paramètres Apple Watch](~/ios/watchos/app-fundamentals/settings.md) dans le simulateur iPhone, ou en générant une [notification](~/ios/watchos/platform/notifications.md) et en confirmant que l’icône s’affiche sur l’écran de notification.

> [!NOTE]
> Les icônes ne peuvent pas avoir de canal alpha (l’application est rejetée lors de l’envoi de l’App Store si un canal alpha est présent). Vous pouvez vérifier s’il existe un canal alpha et [le supprimer à l’aide de l’application Preview sur Mac OS X](~/ios/watchos/troubleshooting.md#noalpha).


## <a name="related-links"></a>Liens associés

- [Icône regarderos d’Apple & Guide des images](https://developer.apple.com/design/human-interface-guidelines/watchos/icons-and-images/)
