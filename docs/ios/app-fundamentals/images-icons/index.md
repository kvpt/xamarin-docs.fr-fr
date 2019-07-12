---
title: Images et icônes dans Xamarin.iOS
description: Cette section inclut une variété d’articles abordons l’utilisation des images dans une application Xamarin.iOS, telles que leur utilisation sous forme d’icônes, lancez les écrans ou les inclure dans les contrôles et en fournissant des icônes pour les types de document personnalisées.
ms.prod: xamarin
ms.assetid: 0AB8CC07-11E4-0D75-4119-AED1A1252424
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 60b450cba73166462747de41176575da27190e0a
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832387"
---
# <a name="images-and-icons-in-xamarinios"></a>Images et icônes dans Xamarin.iOS

_Cette section inclut une variété d’articles abordons l’utilisation des images dans une application Xamarin.iOS, telles que leur utilisation sous forme d’icônes, lancez les écrans ou les inclure dans les contrôles et en fournissant des icônes pour les types de document personnalisées._

Il existe plusieurs façons de cette image actifs sont utilisés à l’intérieur d’une application iOS. À partir de simplement afficher une image dans le cadre de l’interface utilisateur d’une application, telles que l’affectation à un contrôle d’interface utilisateur une `UIButton` ou `UIImageView`, à fournissant des icônes et écrans de lancement, Xamarin.iOS permet de facilement ajouter une illustration excellente à une application iOS comme suit : 

- **Résolution des Images indépendantes** – utiliser la prise en charge intégrée d’iOS permet d’utiliser des images de résolutions de périphérique différent et types (iPhone, iPad, etc.).
- **Ensembles d’images catalogue Asset** -utilisez **ensembles d’images catalogue Asset** pour gérer et de regrouper toutes les versions d’une ressource image donnée requise par une application.
- **Les images dans le concepteur iOS** -le concepteur iOS permet de définir des images pour les contrôles.
- **Les images dans le Code** – utilisez le `UIImage` pour charger et utiliser des ressources d’image et affectez-les à des contrôles d’interface utilisateur dans les méthodes de la classe C# code.
- **Icône d’application** -définir l’icône d’application requis par chaque application iOS. Voici l’icône de l’utilisateur appuie à partir de l’écran d’accueil pour lancer l’application iOS. En outre, cette icône est utilisée par Game Center, le cas échéant.
- **Icône de Spotlight** -définir l’icône de Spotlight de l’application. Chaque fois que l’utilisateur entre le nom d’une application dans la recherche Spotlight, cette icône s’affiche.
- **Icône des paramètres** -définir l’application **paramètres** icône. Si l’utilisateur entre le **paramètres** application sur leur appareil iOS, cette icône s’affichera à la fin de la liste des paramètres de l’application. 
- **Écrans de lancement** -définir l’écran de démarrage de l’application. Une fois que l’utilisateur appuie sur l’icône d’application et avant la première vue s’affiche, un écran vide s’affichera. Heureusement, iOS prend en charge l’affichage d’une image à la place de l’écran vide à l’aide d’une table de montage séquentiel. 
- **Icône d’iTunes** -fournir une icône iTune. Si vous utilisez la méthode Ad-Hoc de la fourniture d’une application (soit pour les utilisateurs d’entreprise ou de test bêta sur des appareils réels), le développeur doit également inclure un 512 x 512 et une image de 1 024 x 1 024 qui sera utilisée pour représenter l’application dans iTunes.
- **Icônes de document** -utiliser une image en tant qu’icône pour n’importe quel type de document spécifique qui prend en charge ou en crée une application Xamarin.iOS.

Il existe plusieurs considérations qui doivent être prises en compte lors de la création des ressources d’image pour une application iOS, ainsi que plusieurs endroits où ces ressources seront utilisées. Chacune d'entre elles a une incidence sur la non seulement comment de nombreuses ressources d’image sera nécessaire, mais comment ces ressources sont créées. Les rubriques suivantes décrivent les types de ressources d’images qui seront nécessaires, comment ces ressources sont incluses dans l’offre groupée de l’application et comment les ressources d’image sont utilisés pour fournir la fonctionnalité requise :


## <a name="displaying-an-imageiosapp-fundamentalsimages-iconsdisplaying-an-imagemd"></a>[Affichage d'une image](~/ios/app-fundamentals/images-icons/displaying-an-image.md)

Cet article aborde notamment une ressource image dans une application Xamarin.iOS et l’affichage de cette image à l’aide de code c# ou en l’assignant à un contrôle dans le concepteur iOS.

## <a name="application-iconsiosapp-fundamentalsimages-iconsapp-iconsmd"></a>[Icônes d'application](~/ios/app-fundamentals/images-icons/app-icons.md)

Cette article explique comment y compris et la gestion d’une ressource image dans une application Xamarin.iOS à utiliser comme icône d’application.

## <a name="alternate-app-iconsiosapp-fundamentalsimages-iconsalternate-app-iconsmd"></a>[Autres icônes d’applications](~/ios/app-fundamentals/images-icons/alternate-app-icons.md)

Apple a ajouté plusieurs améliorations à iOS 10.3 qui permettent à une application gérer son icône :

- `ApplicationIconBadgeNumber` -Obtient ou définit le badge de l’icône d’application Springboard.
- `SupportsAlternateIcons` -If `true` l’application a un autre ensemble d’icônes.
- `AlternateIconName` -Retourne le nom de l’icône de remplacement actuellement sélectionné ou `null` si vous utilisez l’icône principale.
- `SetAlternameIconName` -Utilisez cette méthode pour basculer d’icône de l’application vers l’icône de remplacement donné.


## <a name="launch-screensiosapp-fundamentalsimages-iconslaunch-screensmd"></a>[Écrans de lancement](~/ios/app-fundamentals/images-icons/launch-screens.md)

Cet article couvre à l’aide d’un type spécial de plan conceptuel pour fournir un écran de lancement universelle pour chaque taille de périphérique iOS et la résolution.

## <a name="custom-document-typesiosapp-fundamentalsimages-iconscustom-document-typesmd"></a>[Types de document personnalisé](~/ios/app-fundamentals/images-icons/custom-document-types.md)

Cet article explique comment, y compris et la gestion d’une ressource image dans une application Xamarin.iOS à être utilisé comme une icône de Type de Document personnalisé.



## <a name="related-links"></a>Liens associés

- [Utilisation d’Images (exemple)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icône personnalisée et les instructions de création d’Image](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html)
