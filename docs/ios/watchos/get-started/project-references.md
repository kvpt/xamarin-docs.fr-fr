---
title: Références de projet Watchos dans Xamarin
description: Ce document décrit la relation entre une application iOS, une application Watch et une extension d’application Watch. Il aborde les références de projet et les identificateurs de bundle.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: a7b0f0af887232da52cdca6f6506124fa5e0eef8
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930089"
---
# <a name="watchos-project-references-in-xamarin"></a>Références de projet Watchos dans Xamarin

_Explication de la relation entre l’application iOS, l’application Watch et l’extension Watch._

Les trois projets d’une solution espionneos sont *automatiquement configurés* pour se référencer mutuellement de manière spécifique pour que les applications Watchos 3 soient créées et regroupées correctement. Ces références de projet et paramètres d’identificateur de Bundle sont décrits ci-dessous pour référence.

## <a name="project-references"></a>Références de projets

Pour afficher les références, double-cliquez sur les nœuds références de chaque projet :

- l’application **iPhone** fait référence à l' **application Watch**

  ![l’application iPhone fait référence à l’application Watch](project-references-images/catalog-reference1.png)

- Référence de l' **application Watch** -extension de l' **application Watch**

  ![l’application iPhone fait référence à l’application Watch](project-references-images/catalog-reference2.png)

- L' **extension de l’application Watch** ne fait pas référence à l’un des autres projets

  ![L’extension de l’application Watch ne fait pas référence aux autres projets](project-references-images/catalog-reference3.png)

## <a name="bundle-identifiers"></a>Identificateurs de Bundle

Vous devez également vous assurer que les **identificateurs de Bundle** sont corrects.
Les trois projets doivent avoir le *même* préfixe d’identificateur, les deux projets Watch ayant des extensions prédéfinies de `watchkitextension` et `watchkitapp` , comme suit (pour l’exemple **WatchKitCatalog** ) :

- Projet unifié Xamarin. iOS-`com.xamarin.WatchKitCatalog`

- Projet d’extension WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`

- Projet d’application Watch-`com.xamarin.WatchKitCatalog.watchkitapp`

Assurez-vous également que ces paramètres **info. plist** sont corrects :

- Le projet d’application Watch `WKCompanionAppBundleIdentifier` correspond à l’ID d’offre groupée de l’application parent/conteneur (c’est-à-dire, celui qui s’exécute sur l’iPhone).

- L' **ID de lot WKApp** du projet d’extension du kit de surveillance correspond à l’ID de lot du projet d’application Watch.

Vous pouvez modifier les identificateurs en double-cliquant sur le fichier **info. plist** dans chaque projet.

Cette capture d’écran est le fichier info. plist **de l’extension Watch** , qui présente également l’identificateur **de l’application Watch** :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![](project-references-images/infoplist-extension.png "This screenshot is the Watch Extension's Info.plist file")

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![](project-references-images/infoplist-extension-vs.png "This screenshot is the Watch Extension's Info.plist file")

-----

Cette capture d’écran est le fichier info. plist **de l’application Watch** .
La version actuelle du **système d’exploitation** est 8,2. la **cible de déploiement** de l’application watch doit donc être **8,2**. Notez que si Xcode 6,3 est installé, cette valeur peut être définie sur 8,3-vous devez la modifier 8,2.

![Fichier info. plist Watch](project-references-images/infoplist-watchapp.png)

La cible de déploiement de l’application Watch peut être différente de l’extension Watch et de l’application iOS.
