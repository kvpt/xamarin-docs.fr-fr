---
title: Icônes d’application dans Xamarin.iOS
description: 'Ce document décrit comment utiliser des icônes d’application différents dans Xamarin.iOS : l’icône de l’application, les icônes de Spotlight, les icônes de paramètres et les illustrations iTunes.'
ms.prod: xamarin
ms.assetid: B7791574-4A0F-4CB6-8C18-36D40B5C91EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/22/2017
ms.openlocfilehash: cf2da9f7fe8d90c52517118cc3b4aa0e046d1f69
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864550"
---
# <a name="application-icons-in-xamarinios"></a>Icônes d’application dans Xamarin.iOS

Les rubriques suivantes sont traitées en détail :

* [Application, Spotlight et icônes de paramètres](#icon-types) -les différents types d’icônes requis pour une application iOS.
* [La gestion des icônes avec les catalogues de composants](#managing) : la gestion des icônes d’application à l’aide des catalogues de composants.
* [conception graphique iTunes](#itunes) -fournissant les illustrations iTunes requis pour la méthode Ad-Hoc de livraison de votre application.

<a name="icon-types" />

## <a name="application-spotlight-and-settings-icons"></a>Application, Spotlight et icônes de paramètres

Dans la même façon qu’une application Xamarin.iOS pouvez utiliser des ressources d’image pour les contrôles d’interface utilisateur et sous forme d’icônes de document, ressources d’image peuvent être utilisés pour fournir des icônes d’Application. Les captures d’écran suivantes à partir d’un iPad illustre les trois utilisations des icônes dans iOS :

- **Icône d’application** -toutes les applications iOS doivent définir une icône d’application. Voici l’icône de l’utilisateur appuie à partir de l’écran d’accueil pour lancer l’application iOS. En outre, cette icône est utilisée par Game Center, le cas échéant. Exemple : 

    [![](app-icons-images/000.png "Icône d’application")](app-icons-images/000-full.png#lightbox)
- **Icône de Spotlight** : chaque fois que l’utilisateur entre le nom d’une application dans la recherche Spotlight cette icône s’affiche. Exemple : 

    [![](app-icons-images/000a.png "Icône de Spotlight")](app-icons-images/000a-full.png#lightbox)
- **Icône des paramètres** : si l’utilisateur entre le **paramètres** application sur leur appareil iOS, cette icône s’affichera à la fin de la **paramètres** liste pour l’application. Exemple : 

    [![](app-icons-images/000b.png "Icône des paramètres")](app-icons-images/000b-full.png#lightbox)

Les tailles de ressource d’image et les résolutions suivantes seront nécessaires pour prendre en charge tous les types d’icône requis par une application Xamarin.iOS ciblant iOS 5 via iOS 9 (ou version ultérieure) :

### <a name="iphone-icon-sizes"></a>iPhone tailles d’icônes

- **iPhone : iOS 9 et 10 (iPhone 6 & 7 Plus)**

    ||3 x|
    |---|---|
    |Icône d’application|180x180|
    |Spotlight|120x120|
    |Paramètres|87 x 87|

- **iPhone : iOS 7 et 8**

    ||1 x|x 2|
    |---|---|---|
    |Icône d’application|60x60<sup>1</sup>|120x120|
    |Spotlight|40x40<sup>2</sup>|80 x 80|
    |Paramètres|-|-|

- **iPhone : iOS 5 et 6**

    ||1 x|x 2|
    |---|---|---|
    |Icône d’application|57 x 57|114x114|
    |Spotlight|29 x 29|58x58|
    |Paramètres|29x29<sup>3, 4</sup>|58x58<sup>3, 4</sup>|

### <a name="ipad-icon-sizes"></a>iPad tailles d’icônes

- **iPad : iOS 9 et 10**

    ||2x (iPad Pro)|
    |---|---|
    |Icône d’application|167x167<sup>6</sup>|
    |Spotlight|120x120<sup>6</sup>|
    |Paramètres|58x58<sup>5</sup>|

- **iPad : iOS 7 et 8**

    ||1 x|x 2|
    |---|---|---|
    |Icône d’application|76 x 76|152x152|
    |Spotlight|40 x 40|80 x 80|
    |Paramètres|-|-|

- **iPad : iOS 5 et 6**

    ||1 x|x 2|
    |---|---|---|
    |Icône d’application|72 x 72|144x144|
    |Spotlight|50 x 50|100x100|
    |Paramètres|29x29<sup>3, 5</sup>|58x58<sup>3, 5</sup>|

 1. Visual Studio pour Mac et Xcode ne plus en charge la définition de 1 image x pour iOS 7.
 2. Définition d’une image de 1 x pour iOS 7 n’est pas pris en charge lors de l’utilisation des catalogues de composants.
 3. iOS 7 et 8 utilisent la même taille de l’image en tant qu’iOS 5 et 6.
 4. Utilise les images et les tailles de même que l’icône de Spotlight.
 5. Utilise les mêmes icônes de taille que l’iPhone.
 6. Prise en charge uniquement avec des ensembles d’images catalogue actif.
 
 Pour plus d’informations sur les icônes, consultez d’Apple [icône et des tailles d’Image](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/IconMatrix.html#//apple_ref/doc/uid/TP40006556-CH27-SW1) documentation.

<a name="managing" />

## <a name="managing-icons-with-asset-catalogs"></a>La gestion des icônes avec les catalogues de composants

Pour les icônes, une spéciale `AppIcon` ensemble d’images peut être ajouté à la `Assets.xcassets` fichier projet de l’application. Toutes les versions de l’image doit prendre en charge toutes les résolutions sont inclus dans le _xcasset_ et regroupées. Un éditeur spécial dans Visual Studio pour Mac permet au développeur d’inclure et configurer ces images sous forme graphique.

Pour utiliser un catalogue de ressources, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Faites défiler jusqu'à la **icônes d’application** section.
3. À partir de la **Source** liste déroulante liste, vérifiez que **AppIcon** est sélectionné : 

    ![](app-icons-images/migrate01.png "Vérifiez Qu'appicon est sélectionné.")
4. À partir de la **l’Explorateur de solutions**, double-cliquez sur le `Assets.xcassets` fichier à ouvrir pour modification : 

    ![](app-icons-images/asset01.png "Le fichier Assets.xcassets dans l’Explorateur de solutions")
5. Sélectionnez `AppIcon` dans la liste des ressources pour afficher le `Icon Editor`:

    ![](app-icons-images/asset02.png "L’éditeur AppIcon")
6. Cliquez sur icône type donné et sélectionnez un fichier image pour le type/la taille requise ou faites glisser dans une image à partir d’un dossier et déposez-la sur la taille souhaitée.
7. Cliquez sur le **Open** bouton pour inclure l’image dans le projet et définissez-le dans le xcasset.
8. Répétez cette opération pour toutes les images nécessaires.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le **Info.plist** de fichiers dans le **l’Explorateur de solutions**:

    ![](app-icons-images/icon01w.png "Sélectionnez le fichier Info.plist")
2. Cliquez sur le **ressources visuelles** onglet, puis cliquez sur le **utiliser le catalogue Asset** bouton sous **icônes d’application**: 

    ![](app-icons-images/icon02w.png "Sélectionnez l’onglet ressources visuelles")
3. À partir de la **l’Explorateur de solutions**, développez le **catalogue** dossier : 

    ![](app-icons-images/image009.png "Développez le dossier de catalogue de composants")
4. Double-cliquez sur le **Media** fichier pour l’ouvrir dans l’éditeur : 

    ![](app-icons-images/image010.png "Ouvrez le fichier multimédia dans l’éditeur")
5. Sous le **l’Explorateur de propriétés** le développeur peut sélectionner les différents types et les tailles des icônes requis.
6. Cliquez sur l’icône type donné, puis sélectionnez un fichier image pour le type/la taille requise.
7. Cliquez sur le **Open** bouton pour inclure l’image dans le projet et définissez-le dans le xcasset.
8. Répétez cette opération pour toutes les images nécessaires.

-----

Il s’agit de la méthode préférée de, y compris et la gestion des ressources d’image qui seront utilisés pour fournir des icônes d’Application, Spotlight et réglages pour une application.

### <a name="migrating-from-infoplist-to-asset-catalogs"></a>Migration de Info.plist vers des catalogues de composants

Pour une application Xamarin.iOS existant en utilisant le `Info.plist` de fichiers pour gérer ses icônes, il est fortement recommandé que le développeur le basculer en utiliser le `AppIcons` composant de l’Image à l’intérieur de la `Assets.xcassets`.

Effectuez ce qui suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Faites défiler jusqu'à la **icônes d’application** section.
3. À partir de la **Source** liste déroulante, sélectionnez **migrer vers des catalogues de composants**: 

    ![](app-icons-images/migrate02.png "Sélectionnez migrer vers des catalogues de composants")
4. Toutes les icônes définis dans le `Info.plist` fichier est migré vers un `AppIcons` Image définie ajouté à `Assets.xcassets`: 

     ![](app-icons-images/migrate03.png "L’Image de AppIcons définie dans le Assets.xcassets")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Cliquez sur l’iPhone section icônes : 

    ![](app-icons-images/image007.png "Éditeur d’icônes Rhe iPhone")
3. Faites défiler jusqu'à la **icônes** section.
4. À partir de la **catalogue** liste déroulante, sélectionnez **catalogues de composants utilisation**.
5. Toutes les icônes définis dans le `Info.plist` fichier est migré vers un `Images` ensemble ajouté à `Assets.xcassets`.
6. Enregistrez les changements dans le fichier `Info.plist`.

-----

<a name="itunes" />

## <a name="itunes-artwork"></a>Conception graphique iTunes

Si vous utilisez la méthode Ad-Hoc de la fourniture de l’application (soit pour les utilisateurs d’entreprise ou de test bêta sur des appareils réels), le développeur doit également inclure un 512 x 512 et une image de 1 024 x 1 024 qui sera utilisée pour représenter l’application dans iTunes.

Pour spécifier les illustrations iTunes, effectuez les tâches suivantes :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Faites défiler vers le **Itunesartwork** section de l’éditeur : 

    ![](app-icons-images/itunes01.png "Faites défiler jusqu'à la section d’une illustration de l’éditeur d’iTunes")
3. Pour toute image manquante, cliquez sur la miniature dans l’éditeur, sélectionnez le fichier image pour l’illustration iTunes souhaitée à partir de la boîte de dialogue Ouvrir un fichier et cliquez sur le **OK** bouton.
4. Répétez cette étape jusqu'à ce que tous nécessitent images ont été spécifiées pour l’application.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Info.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.

2. Cliquez sur le **ressources visuelles** onglet et développez le **Itunesartwork**: 

    ![](app-icons-images/itunes01w.png "Modification Itunesartwork dans Visual Studio")
3. Pour toute image manquante, cliquez sur la miniature dans l’éditeur, sélectionnez le fichier image pour l’illustration iTunes souhaitée à partir de la boîte de dialogue Ouvrir un fichier et cliquez sur le **Open** bouton.
4. Répétez cette étape jusqu'à ce que tous nécessitent images ont été spécifiées pour l’application.

-----

## <a name="related-links"></a>Liens associés

- [Utilisation d’Images (exemple)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Icône personnalisée et les instructions de création d’Image](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html))
