---
title: Utilisation de la disposition Watchos dans Xamarin
description: Ce document décrit comment créer une disposition Watchos à l’aide de Xamarin. Il aborde les contrôleurs d’interface, les groupes, les séparateurs et les contrôles de contenu.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 345c05a439423474644ac64ef86f9adc580ab0b1
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937720"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Utilisation de la disposition Watchos dans Xamarin

La conception de dispositions pour les [tailles d’écran](~/ios/watchos/app-fundamentals/screen-sizes.md) Apple Watch présente des défis uniques.

## <a name="design-tips"></a>Conseils de conception

Le point clé est : rendre votre interface utilisateur lisible et utilisable sur un petit écran de montre, avec un doigt de grande taille. Ne rentrez pas dans l’interruption de la conception pour le **simulateur iOS** (qui paraît très grand) et un **pointeur de souris** (qui fonctionne avec les petites cibles tactiles) !

- Utilisez un arrière-plan noir : il crée l’illusion d’un écran plus grand avec le cadre noir de la montre.

- Ne pas entourer la disposition de l’écran : le cadre forme un remplissage visuel naturel.

- Concentrez-vous sur la lisibilité. Utilisez les couleurs et les tailles de police judicieusement pour vous assurer que le texte est lisible. Utilisez les styles de texte intégrés pour bénéficier d’une prise en charge automatique des types dynamiques.

![Exemple de prise en charge de type dynamique](layout-images/type.png)

- Concentrez-vous sur les tailles de cibles tactiles. Les lignes de table Buttons/tappable avec des étiquettes de texte doivent s’étendre sur la totalité de l’écran. Apple indique « ne jamais placer plus de trois éléments côte à côte » et si vous utilisez des icônes et non des étiquettes de texte.

- Utilisez le [ `Menu` contrôle](~/ios/watchos/user-interface/menu.md) pour exposer des fonctionnalités moins fréquemment utilisées pour que votre conception d’application reste claire et concise.

## <a name="implementation"></a>Implémentation

Le kit Watch comprend les contrôles suivants pour vous aider à créer des dispositions attrayantes pour les applications Watch :

### <a name="interface-controller"></a>Contrôleur d’interface

`WKInterfaceController`Est la classe de base de toutes vos scènes.

L’aire de conception du contrôleur d’interface se comporte comme un **groupe**vertical : vous pouvez faire glisser d’autres contrôles sur le contrôleur d’interface et ils seront automatiquement disposés l’un au-dessus de l’autre :

![Les contrôles sont automatiquement disposés l’un au-dessus de l’autre](layout-images/controller-scene.png)

Vous pouvez définir les propriétés de **position** et de **taille** sur chaque contrôle pour contrôler leur apparence :

![Définir les propriétés de position et de taille sur chaque contrôle](layout-images/positionsize-attributes.png)

Lorsque la taille est définie sur **relative au conteneur** , vous pouvez fournir une valeur proportionnelle et un ajustement de décalage. Cette capture d’écran montre un bouton qui a été défini pour utiliser 80% de la largeur de l’écran de surveillance (**0,8**) :

![Fournir une valeur proportionnelle et un ajustement de décalage](layout-images/button-attributes.png)

### <a name="group"></a>Groupe

`WKInterfaceGroup`est un conteneur de disposition simple qui peut être configuré pour empiler des contrôles verticalement ou horizontalement. Il comprend l’espacement entre chaque contrôle par défaut, mais vous pouvez modifier l’espacement (et les indéfinis) dans l’inspecteur d' **attributs** .

![Modifier l’espacement et les indéfinis dans l’inspecteur d’attributs](layout-images/group-attributes.png)

Les groupes peuvent eux-mêmes être dimensionnés et positionnés par rapport aux contrôles qui les entourent, et les groupes peuvent être imbriqués pour créer des dispositions complexes.

![Les groupes peuvent être imbriqués pour créer des dispositions complexes](layout-images/group-scene.png)

### <a name="separator"></a>Séparateur

Le contrôle separator est destiné à fournir une aide visuelle à votre disposition. Utilisez des séparateurs (ou des couleurs ou des images d’arrière-plan) pour aider l’utilisateur à comprendre quel contenu est lié à votre écran.

![Exemple d’utilisation d’un séparateur](layout-images/separator-scene.png)

Notez que les séparateurs bleus et verts qui n’utilisent pas la largeur complète de l’écran ont été configurés avec des tailles **fixes** ou **relatives aux** tailles de conteneur.

### <a name="content-controls"></a>Contrôles de contenu

Aucune disposition n’est complète sans les `Label` `Image` contrôles,, `Button` ,,, `Switch` `Slider` `Map` et [other controls](~/ios/watchos/user-interface/index.md).
Celles-ci peuvent être positionnées dans vos dispositions à l’aide de **groupes** ou de paramètres de position et de taille sur chaque contrôle.

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Informations de référence sur la disposition d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Informations de référence sur la typographie de Color & d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
