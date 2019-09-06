---
title: Utilisation de watchOS disposition dans Xamarin
description: Ce document décrit comment créer une disposition de watchOS avec Xamarin. Il aborde les contrôleurs d’interface, les groupes, les séparateurs et les contrôles de contenu.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: 5272921cade2b9dadd24a4c917e6251ac591e178
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292320"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Utilisation de watchOS disposition dans Xamarin

La mise en page pour l’Apple Watch [tailles d’écran](~/ios/watchos/app-fundamentals/screen-sizes.md) propose des défis uniques.

## <a name="design-tips"></a>Conseils de conception

Le point essentiel est : rendre votre interface utilisateur lisible et utilisable sur un écran espion petit, avec un doigt volumineux. N’appartiennent à l’interruption de la conception de la **iOS Simulator** (qui apparaît très volumineux) et un **pointeur de la souris** (qui fonctionne avec des cibles de touch minuscule) !

- Utiliser un arrière-plan noir, il crée l’illusion d’un écran plus grand avec panneau noir de la montre.

- Ne pas remplir autour de la disposition de votre écran - cadre constitue un remplissage visual naturels.

- Se concentrer sur la lisibilité. Utiliser judicieusement les tailles de police et couleurs pour garantir le texte est lisible. Utiliser les styles de texte intégré pour obtenir la prise en charge automatique de Type dynamique.

![](layout-images/type.png "Exemple de prise en charge de Type dynamique")

- Concentrez-vous sur les tailles de cibles tactile. Boutons/tappable lignes de la table avec les étiquettes de texte doivent s’étendre à l’écran entier. Apple indique « ne placez jamais plus de trois éléments côte à côte », et si vous n’utilisez pas les étiquettes de texte et les icônes.

- Utilisez le [ `Menu` contrôle](~/ios/watchos/user-interface/menu.md) aux fonctionnalités exposent moins fréquemment utilisé pour conserver votre conception d’applications claire et concise.


## <a name="implementation"></a>Implémentation

Regardez le que kit inclut les contrôles suivants pour vous aider à créer des dispositions d’application attrayante espion :

### <a name="interface-controller"></a>Contrôleur d’interface

Le `WKInterfaceController` est la classe de base toutes vos séquences.

L’aire de conception pour le contrôleur d’interface se comporte comme un vertical **groupe**: vous pouvez faire glisser des autres contrôles sur le contrôleur d’interface et elles sera disposé automatiquement au-dessus de l’autre :

![](layout-images/controller-scene.png "Les contrôles sont un disposé automatiquement au-dessus de l’autre")

Vous pouvez définir le **Position** et **taille** propriétés sur chaque contrôle afin de contrôler leur apparence :

![](layout-images/positionsize-attributes.png "Définir les propriétés Position et la taille sur chaque contrôle")

Quand la taille est définie sur **relatif au conteneur** vous pouvez fournir une valeur proportionnelle et un réglage du décalage. Cette capture d’écran affiche un bouton qui a été défini pour utiliser 80 % de la largeur de l’écran de surveillance (**0.8**) :

![](layout-images/button-attributes.png "Fournir une valeur proportionnelle et un ajustement de décalage")


### <a name="group"></a>Regrouper

`WKInterfaceGroup` est un conteneur de disposition simple qui peut être configuré pour la pile contrôle verticalement ou horizontalement. Il inclut l’espacement entre chaque contrôle par défaut, mais vous pouvez modifier l’espacement (et encarts) dans le **attributs** inspecteur.

![](layout-images/group-attributes.png "Modifier l’espacement et les marges dans l’inspecteur d’attributs")

Groupes peuvent eux-mêmes être dimensionnés et positionnés par rapport à des contrôles autour d’elles, et les groupes peuvent être imbriqués pour créer des dispositions complexes.

![](layout-images/group-scene.png "Les groupes peuvent être imbriqués pour créer des dispositions complexes")


### <a name="separator"></a>Séparateur

Le contrôle de séparateur vise à aider à fournir une aide visuelle dans votre disposition. Utiliser des séparateurs (ou les couleurs d’arrière-plan ou des images) pour aider l’utilisateur à comprendre le contenu qui est lié à l’écran.

![](layout-images/separator-scene.png "Exemple d’utilisation de séparateur")

Notez les séparateurs bleues et vertes qui n’utilisent pas la largeur totale de l’écran ont été configurées avec soit **fixe** ou **relatif au conteneur** tailles.

### <a name="content-controls"></a>Contrôles de contenu

Aucune disposition ne serait complète sans le `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, et [autres contrôles](~/ios/watchos/user-interface/index.md).
Il peuvent être positionnés dans vos dispositions à l’aide de **groupes** ou les paramètres de position et la taille de chaque contrôle.



## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Référence de mise en page d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Apple couleur & Typographie de référence](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
