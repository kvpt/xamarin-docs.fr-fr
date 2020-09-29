---
title: Icônes d’application dans Xamarin. iOS
description: 'Ce document explique comment utiliser diverses icônes d’application dans Xamarin. iOS : l’icône d’application elle-même, les icônes de Spotlight, les icônes de paramètres et l’illustration iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/22/2017
ms.openlocfilehash: 8bf0df03419d3705d72b679cfbfca427b0ac1a93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435647"
---
# <a name="application-icons-in-xamarinios"></a>Icônes d’application dans Xamarin. iOS

Les rubriques suivantes sont traitées en détail :

- [Icônes application, Spotlight et Settings](#icon-types) : les différents types d’icônes requis pour une application iOS.
- [Gestion des icônes avec les catalogues de composants](#managing) : gestion des icônes d’application à l’aide de catalogues de ressources.
- des [illustrations iTunes](#itunes) , en fournissant les illustrations iTunes nécessaires pour la méthode ad hoc de livraison de votre application.

<a name="icon-types"></a>

## <a name="application-spotlight-and-settings-icons"></a>Icônes d’application, de Spotlight et de paramètres

De la même façon qu’une application Xamarin. iOS peut utiliser des ressources d’image pour les contrôles d’interface utilisateur et comme icônes de document, les ressources d’image peuvent être utilisées pour fournir des icônes d’application. Les captures d’écran suivantes d’un iPad illustrent les trois utilisations des icônes dans iOS :

- **Icône d’application** : chaque application iOS doit définir une icône d’application. Il s’agit de l’icône sur laquelle l’utilisateur appuie sur l’écran d’accueil iOS pour lancer l’application. En outre, cette icône est utilisée par Game Center, le cas échéant. Exemple :

    [![Icône d’application](app-icons-images/000.png)](app-icons-images/000-full.png#lightbox)
- **Icône Spotlight** : chaque fois que l’utilisateur entre le nom d’une application dans une recherche Spotlight, cette icône s’affiche. Exemple :

    [![Icône de Spotlight](app-icons-images/000a.png)](app-icons-images/000a-full.png#lightbox)
- **Icône des paramètres** : si l’utilisateur entre l’application **paramètres** sur son appareil iOS, cette icône s’affiche à la fin de la liste des **paramètres** de l’application. Exemple :

    [![Icône Paramètres](app-icons-images/000b.png)](app-icons-images/000b-full.png#lightbox)

Les tailles et résolutions de ressource d’image suivantes seront nécessaires pour prendre en charge tous les types d’icônes requis par une application Xamarin. iOS ciblant iOS 5 via iOS 9 (ou version ultérieure) :

### <a name="iphone-icon-sizes"></a>Tailles des icônes iPhone

- **iPhone : iOS 9 & 10 (iPhone 6 & 7 plus)**

    |Icône|3x|
    |---|---|
    |Icône d’application|180x180|
    |À la une|120x120|
    |Paramètres|87x87|

- **iPhone : iOS 7 & 8**

    |Icône|1x|x 2|
    |---|---|---|
    |Icône d’application|60 x 60<sup>1</sup>|120x120|
    |À la une|40 x 40<sup>2</sup>|80 x 80|
    |Paramètres|-|-|

- **iPhone : iOS 5 & 6**

    |Icône|1x|x 2|
    |---|---|---|
    |Icône d’application|57 x 57|114x114|
    |À la une|29 x 29|58 x 58|
    |Paramètres|29x29<sup>3, 4</sup>|58 x 58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>Tailles d’icône iPad

- **iPad : iOS 9 & 10**

    |Icône|2x (iPad Pro)|
    |---|---|
    |Icône d’application|167x167<sup>6</sup>|
    |À la une|120 x 120<sup>6</sup>|
    |Paramètres|58 x 58<sup>5</sup>|

- **iPad : iOS 7 & 8**

    |Icône|1x|x 2|
    |---|---|---|
    |Icône d’application|76 x 76|152 x 152|
    |À la une|40 x 40|80 x 80|
    |Paramètres|-|-|

- **iPad : iOS 5 & 6**

    |Icône|1x|x 2|
    |---|---|---|
    |Icône d’application|72 x 72|144x144|
    |À la une|50 x 50|100x100|
    |Paramètres|29x29<sup>3, 5</sup>|58 x 58<sup>3, 5</sup>|

 1. Visual Studio pour Mac et Xcode ne prennent plus en charge le paramétrage de l’image 1x pour iOS 7.
 2. La définition d’une image 1x pour iOS 7 n’est pas prise en charge lors de l’utilisation des catalogues de ressources.
 3. iOS 7 & 8 utilisent les mêmes tailles d’image que iOS 5 & 6.
 4. Utilise les mêmes images et tailles que l’icône Spotlight.
 5. Utilise les mêmes icônes de taille que l’iPhone.
 6. Pris en charge uniquement avec les jeux d’images du catalogue d’actifs.

 Pour plus d’informations sur les icônes, consultez la documentation sur [les tailles d’image et d’icône](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) d’Apple.

<a name="managing"></a>

## <a name="managing-icons-with-asset-catalogs"></a>Gestion des icônes avec les catalogues de ressources

Pour les icônes, un `AppIcon` jeu d’images spécial peut être ajouté au `Assets.xcassets` fichier dans le projet de l’application. Toutes les versions de l’image nécessaires à la prise en charge de toutes les résolutions sont incluses dans le _xcasset_ et regroupées. Un éditeur spécial dans Visual Studio pour Mac permet au développeur d’inclure et de configurer ces images graphiquement.

Pour utiliser un catalogue de composants, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Info.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Faites défiler jusqu’à la section **icônes iPhone** .
3. Cliquez sur le bouton **migrer vers le catalogue d’actifs** :

    ![Vérifier que l’option AppIcon est sélectionnée](app-icons-images/migrate01.png)

4. À partir de la **Explorateur de solutions**, double-cliquez sur le fichier pour l' `Assets.xcassets` ouvrir et le modifier :

    ![Fichier Assets. xcassets dans le Explorateur de solutions](app-icons-images/asset01.png)

5. Sélectionnez `AppIcon` dans la liste des ressources pour afficher `Icon Editor` :

    ![Éditeur AppIcon](app-icons-images/asset02.png)

6. Cliquez sur le type d’icône donné, puis sélectionnez un fichier image pour le type/la taille requis ou faites-le glisser dans une image à partir d’un dossier et déposez-le à la taille souhaitée.
7. Cliquez sur le bouton **ouvrir** pour inclure l’image dans le projet et la définir dans le xcasset.
8. Répétez cette opération pour toutes les images requises.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur * * info.  * * fichier dans le **Explorateur de solutions**:

    ![Sélectionnez info. plist](app-icons-images/icon01w.png)

2. Cliquez sur l’onglet **ressources visuelles** , puis cliquez sur le bouton **utiliser le catalogue de ressources** sous icônes d' **application**:

    ![Sélectionnez l’onglet ressources visuelles](app-icons-images/icon02w.png)

    S’il n’y a aucun bouton, mais plutôt une liste déroulante, un catalogue de ressources a déjà été ajouté à ce projet.

3. À partir de la **Explorateur de solutions**, développez le dossier du **catalogue de composants** :

    ![Développer le dossier du catalogue de ressources](app-icons-images/image009.png)

4. Double-cliquez sur le fichier **multimédia** pour l’ouvrir dans l’éditeur :

    ![Ouvrir le fichier multimédia dans l’éditeur](app-icons-images/image010.png)

5. Dans l' **Explorateur de propriétés** , le développeur peut sélectionner les différents types et tailles d’icônes requis.
6. Cliquez sur le type d’icône donné et sélectionnez un fichier image pour le type/la taille requis.
7. Cliquez sur le bouton **ouvrir** pour inclure l’image dans le projet et la définir dans le xcasset.
8. Répétez cette opération pour toutes les images requises.

-----

Il s’agit de la méthode recommandée pour inclure et gérer des ressources d’images qui seront utilisées pour fournir des icônes d’application, de Spotlight et de paramètres pour une application.

<a name="itunes"></a>

## <a name="itunes-artwork"></a>Conception graphique iTunes

Si vous utilisez la méthode ad hoc de diffusion de l’application (pour les utilisateurs d’entreprise ou pour des tests bêta sur des appareils réels), le développeur doit également inclure une image 512 x 512 et 1024x1024 qui sera utilisée pour représenter l’application dans iTunes.

Pour spécifier les illustrations iTunes, effectuez les tâches suivantes :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Info.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Faites défiler jusqu’à la section **illustrations d’iTunes** de l’éditeur :

    ![Faire défiler jusqu’à la section illustrations d’iTunes de l’éditeur](app-icons-images/itunes01.png)
3. Pour toute image manquante, cliquez sur la miniature dans l’éditeur, sélectionnez le fichier image correspondant à l’illustration iTunes souhaitée dans la boîte de dialogue Ouvrir un fichier, puis cliquez sur le bouton **OK** .
4. Répétez cette étape jusqu’à ce que toutes les images nécessaires aient été spécifiées pour l’application.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Info.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.

2. Cliquez sur l’onglet **ressources visuelles** et développez l' **illustration iTunes**:

    ![Modification d’une illustration iTunes dans Visual Studio](app-icons-images/itunes01w.png)
3. Pour toute image manquante, cliquez sur la miniature dans l’éditeur, sélectionnez le fichier image correspondant à l’illustration iTunes souhaitée dans la boîte de dialogue Ouvrir un fichier, puis cliquez sur le bouton **ouvrir** .
4. Répétez cette étape jusqu’à ce que toutes les images nécessaires aient été spécifiées pour l’application.

-----

## <a name="related-links"></a>Liens associés

- [Utilisation des images (exemple)](/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Instructions personnalisées de création d’image et d’icône](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))