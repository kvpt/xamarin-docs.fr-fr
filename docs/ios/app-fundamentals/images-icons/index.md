---
title: Images et icônes dans Xamarin. iOS
description: Cette section inclut un grand nombre d’articles qui traitent de l’utilisation d’images dans une application Xamarin. iOS, telles que leur utilisation en tant qu’icônes, les écrans de lancement ou leur inclusion dans des contrôles et la fourniture d’icônes pour les types de documents personnalisés.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/18/2017
ms.openlocfilehash: af7dbad29c156964f154053dac00255b1b32e4ff
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289092"
---
# <a name="images-and-icons-in-xamarinios"></a>Images et icônes dans Xamarin. iOS

_Cette section inclut un grand nombre d’articles qui traitent de l’utilisation d’images dans une application Xamarin. iOS, telles que leur utilisation en tant qu’icônes, les écrans de lancement ou leur inclusion dans des contrôles et la fourniture d’icônes pour les types de documents personnalisés._

Il existe plusieurs façons d’utiliser des ressources d’image dans une application iOS. De simplement afficher une image dans le cadre de l’interface utilisateur d’une application à, en l’assignant à un `UIButton` contrôle `UIImageView`d’interface utilisateur tel qu’un ou, pour fournir des icônes et des écrans de lancement, Xamarin. iOS permet d’ajouter facilement de superbes illustrations à une application iOS de l’une des manières suivantes : 

- **Images indépendantes** de la résolution : utilisez la prise en charge intégrée d’IOS pour travailler avec des images sur différents types et résolutions d’appareils (iPhone, iPad, etc.).
- **Jeux d’images du catalogue de composants** : utilisez les **ensembles d’images du catalogue de composants** pour gérer et regrouper toute la version d’une ressource d’image donnée requise par une application.
- **Images dans le concepteur iOS** : utilisez le concepteur IOS pour définir des images pour les contrôles.
- **Images dans le code** : utilisez `UIImage` les méthodes de la classe pour charger et utiliser des ressources d’image et les assigner aux contrôles d’interface utilisateur dans C# le code.
- **Icône d’application** : définissez l’icône d’application requise par chaque application iOS. Il s’agit de l’icône sur laquelle l’utilisateur appuie sur l’écran d’accueil iOS pour lancer l’application. En outre, cette icône est utilisée par Game Center, le cas échéant.
- **Icône Spotlight** : définissez l’icône Spotlight de l’application. Chaque fois que l’utilisateur entre le nom d’une application dans une recherche Spotlight, cette icône s’affiche.
- **Icône Paramètres** : définissez l’icône **paramètres** de l’application. Si l’utilisateur entre l’application **paramètres** sur son appareil iOS, cette icône s’affiche à la fin de la liste des paramètres de l’application. 
- **Écrans de lancement** : définissez l’écran de lancement de l’application. Une fois que l’utilisateur a appuyé sur l’icône de l’application et avant que le premier affichage n’apparaisse, un écran vide s’affiche. Heureusement, iOS prend en charge l’affichage d’une image à la place de l’écran vide à l’aide d’un Storyboard. 
- **icône iTunes** : indiquez une icône de iTunes. Si vous utilisez la méthode ad hoc pour fournir une application (soit pour les utilisateurs d’entreprise, soit pour des tests bêta sur des appareils réels), le développeur doit également inclure une image 512 x 512 et 1024x1024 qui sera utilisée pour représenter l’application dans iTunes.
- **Icônes de document** : utilisez une image comme icône pour tout type de document spécifique qu’une application Xamarin. IOS prend en charge ou crée.

Il convient de tenir compte de plusieurs considérations lors de la création de ressources d’image pour une application iOS, ainsi que de plusieurs emplacements où ces ressources seront utilisées. Chacun de ces éléments a un impact sur le nombre de ressources d’image nécessaires, mais sur la façon dont ces ressources sont créées. Les rubriques suivantes traitent des types de ressources d’images qui seront nécessaires, de la façon dont ces ressources sont incluses dans l’offre groupée de l’application et de la façon dont les ressources d’image sont consommées pour fournir les fonctionnalités requises :


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Affichage d'une image](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Cet article aborde l’inclusion d’une ressource d’image dans une application Xamarin. iOS et l’affichage C# de cette image en utilisant du code ou en l’affectant à un contrôle dans le concepteur iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Icônes d'application](~/ios/app-fundamentals/images-icons/app-icons.md)

Cet article traite de l’ajout et de la gestion d’une ressource d’image dans une application Xamarin. iOS à utiliser comme icône d’application.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Autres icônes d’applications](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple a ajouté plusieurs améliorations à iOS 10,3 qui permettent à une application de gérer son icône :

- `ApplicationIconBadgeNumber`-Obtient ou définit le badge de l’icône d’application dans le Springboard.
- `SupportsAlternateIcons`-Si `true` l’application possède un autre ensemble d’icônes.
- `AlternateIconName`-Retourne le nom de l’autre icône actuellement sélectionnée ou `null` si vous utilisez l’icône principale.
- `SetAlternameIconName`-Utilisez cette méthode pour faire passer l’icône de l’application à l’autre icône donnée.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Écrans de lancement](~/ios/app-fundamentals/images-icons/launch-screens.md)

Cet article décrit l’utilisation d’un type spécial de table de montage séquentiel pour fournir un écran de lancement universel pour chaque taille et résolution de l’appareil iOS.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Types de document personnalisé](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Cet article traite de l’ajout et de la gestion d’une ressource d’image dans une application Xamarin. iOS à utiliser comme icône de type de document personnalisé.



## <a name="related-links"></a>Liens associés

- [Utilisation des images (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instructions personnalisées de création d’images et d’icônes](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
