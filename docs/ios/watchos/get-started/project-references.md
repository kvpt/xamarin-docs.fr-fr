---
title: watchOS références de projet dans Xamarin
description: Ce document décrit la relation entre une application iOS, une application watch et une extension d’application watch. Il aborde les références de projet et d’offre groupée identificateurs.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 60eaad98e2d5469e9c43e6b4ad889080e1aa63ba
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832066"
---
# <a name="watchos-project-references-in-xamarin"></a>watchOS références de projet dans Xamarin

_Explication de la relation entre l’application iOS, une application watch et une extension watch._

Les trois projets dans une solution watchOS sont *automatiquement configuré* à se référencent mutuellement de manière spécifique pour les applications watchOS 3 doivent être générés et regroupé correctement. Ces références de projet et les paramètres de l’identificateur bundle sont décrits ci-dessous pour référence.

## <a name="project-references"></a>Références de projets

Afficher les références en double-cliquant sur les nœuds de références pour chaque projet :

- **application iPhone** références **application Watch**

  ![](project-references-images/catalog-reference1.png "application iPhone fait référence à application Watch")

- **Regardez l’application** références **Extension d’application Watch**

  ![](project-references-images/catalog-reference2.png "application iPhone fait référence à application Watch")


- Le **Extension d’application Watch** ne fait pas référence à une des autres projets

  ![](project-references-images/catalog-reference3.png "Regardez le Qu'extension d’application ne fait pas référence à d’autres projets")



## <a name="bundle-identifiers"></a>Identificateurs de bundle

Vous devez également vous assurer votre **identificateurs de Bundle** sont corrects.
Les trois projets doivent avoir le *même* préfixe d’identificateur, avec les projets de deux espion fait de prédéfinir les extensions de `watchkitextension` et `watchkitapp`, comme suit (pour le **WatchKitCatalog** exemple) :

- Projet de Unified Xamarin.iOS- `com.xamarin.WatchKitCatalog`

- Projet de WatchKit Extension : `com.xamarin.WatchKitCatalog.watchkitextension`

- Projet d’application Watch- `com.xamarin.WatchKitCatalog.watchkitapp`

Assurez-vous également que ces **Info.plist** paramètres sont corrects :

- Le projet application Watch `WKCompanionAppBundleIdentifier` correspond à Bundle ID l’application conteneur/parent (ie. celui qui s’exécute sur l’iPhone) ;

- Le projet d’Extension de Kit Watch **ID d’ensemble WKApp** correspond aux ID d’ensemble. du projet d’application Watch

Vous pouvez modifier les identificateurs en double-cliquant sur le **Info.plist** fichier dans chaque projet.

Cette capture d’écran est la **d’Extension Watch** fichier Info.plist, montrant la **d’application Watch** identificateur ainsi :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "Cette capture d’écran est le fichier Info.plist de l’Extension surveillance")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "Cette capture d’écran est le fichier Info.plist de l’Extension surveillance")

-----

Cette capture d’écran est la **d’application Watch** fichier Info.plist.
Actuel **système d’exploitation Watch** version est 8.2, donc la **cible de déploiement** pour l’application Watch doit être **8.2**. Notez que si vous avez Xcode 6.3 est installé, cette valeur peut être définie sur 8.3 - vous devez le modifier 8.2.

![](project-references-images/infoplist-watchapp.png "La fichier Info.plist espion")

La cible de déploiement pour l’application Watch peut être différente de l’Extension Watch et une application iOS.

