---
title: Icône d’application pour les applications Xamarin.Mac
description: Cet article traite de la création des images nécessaires à l’icône d’une application Xamarin.Mac, de leur regroupement dans un fichier .icns et de l’ajout de l’icône dans le projet Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 675b9405-d9a7-49f0-94ad-417f10a71d11
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: fdc132143905db6cc32ebd9aeb07268e69bf2070
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434682"
---
# <a name="application-icon-for-xamarinmac-apps"></a>Icône d’application pour les applications Xamarin.Mac

_Cet article traite de la création des images nécessaires à l’icône d’une application Xamarin.Mac, de leur regroupement dans un fichier .icns et de l’ajout de l’icône dans le projet Xamarin.Mac._

## <a name="overview"></a>Vue d’ensemble

Quand un développeur utilise C# et .NET dans une application Xamarin.Mac, il a accès aux mêmes outils d’image et d’icône qu’un développeur travaillant en *Objective-C* et en *Xcode*.

Une icône de qualité doit véhiculer l’objectif principal d’une application Xamarin.Mac et suggérer l’expérience que l’utilisateur doit attendre lors de cette dernière. Cet article couvre toutes les étapes nécessaires pour créer les ressources d’image nécessaires pour une icône, en empaquetant ces ressources dans un fichier `AppIcon.appiconset` et en utilisant ce fichier dans une application Xamarin.Mac.

![L’éditeur AppIcon. appiconset](app-icon-images/intro01.png "L’éditeur AppIcon. appiconset")

## <a name="application-icon"></a>Icône d'application

Une icône de qualité doit véhiculer l’objectif principal d’une application Xamarin.Mac et suggérer l’expérience que l’utilisateur doit attendre lors de cette dernière. Chaque application macOS doit inclure son icône dans différentes tailles pour son affichage dans Finder, la station d’accueil, Launchpad et d’autres emplacements sur l’ensemble de l’ordinateur.

## <a name="designing-the-icon"></a>Conception de l’icône

Apple suggère les conseils suivants lors de la conception de l’icône d’une application :

- Donnez à l’icône une forme réaliste et unique.
- Si l’application macOS possède un équivalent iOS, ne réutilisez pas l’icône de l’application iOS.
- Utilisez des images universelles que les utilisateurs peuvent reconnaître facilement.
- Recherchez la simplicité.
- Utilisez les couleurs et les ombres avec modération pour permettre à l’icône de présenter l’application.
- Évitez de combiner du texte réel avec du texte ou des lignes _en latin_ qui suggèrent du texte.
- Créez une version idéale de l’objet de l’icône au lieu d’utiliser une photo réelle.
- Évitez d’utiliser des éléments d’interface utilisateur macOS dans les icônes.
- N’utilisez pas des répliques des icônes d’Apple dans les icônes.

Lisez les sections [Galerie d’icônes d’application](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Gallery.html#//apple_ref/doc/uid/20000957-CH88-SW1) et [Conception d’icônes d’application](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/Designing.html#//apple_ref/doc/uid/20000957-CH87-SW1) des [Human Interface Guidelines pour OS X](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/) d’Apple avant de concevoir l’icône d’une application Xamarin.Mac.

## <a name="required-image-sizes-and-filenames"></a>Tailles et noms de fichiers d’image obligatoires

Comme toute autre ressource d’image que le développeur va utiliser dans une application Xamarin.Mac, l’icône de l’application doit être fournie à la fois dans une version de résolution standard et de résolution Retina. Là encore, comme toute autre image, utilisez un format `@2x` quand vous nommez les fichiers d’icône :

- **Résolution standard**   -  _ImageName_**.** _NomFichier-extension_ (exemple : **icon_512x512.png**)
- **Haute résolution**   -  _ImageName_** @2x .** _NomFichier-extension_ (exemple : **icon_512x512@2x.png** )

Par exemple, pour fournir la version 512 x 512 de l’icône de l’application, le fichier serait nommé **icon_512x512.png** et **icon_512x512@2x.png**.

Pour vérifier que l’icône s’affiche parfaitement dans tous les emplacements où les utilisateurs la voient, fournissez les ressources dans les tailles répertoriées ci-dessous :

|Nom de fichier|Taille (en pixels)|
|---|---|
|icon_512x512@2x.png|1024 x 1024|
|icon_512x512.png|512 x 512|
|icon_256x256@2x.png|512 x 512|
|icon_256x256.png|256 x 256|
|icon_128x128@2x.png|256 x 256|
|icon_128x128.png|128 x 128|
|icon_32x32@2x.png|64 x 64|
|icon_32x32.png|32 x 32|
|icon_16x16@2x.png|32 x 32|
|icon_16x16.png|16 x 16|

Pour plus d’informations, consultez la documentation [Fournir les versions haute résolution de toutes les ressources graphiques des applications](https://developer.apple.com/library/mac/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Optimizing/Optimizing.html#//apple_ref/doc/uid/TP40012302-CH7-SW3) d’Apple.

## <a name="packaging-the-icon-resources"></a>Empaquetage des ressources d’icônes

Quand l’icône est conçue et enregistrée avec les tailles de fichier et noms obligatoires, Visual Studio pour Mac permet de l’affecter facilement aux ressources d’image pour qu’elle soit utilisée dans Xamarin.Mac.

Effectuez les actions suivantes :

1. Dans le **Panneau Solutions**, ouvrez **Assets.xcassets** > **AppIcons.appiconset** : 

    ![Modification de AppIcon. appiconset](app-icon-images/intro01.png "Modification de AppIcon. appiconset")
2. Pour chaque taille d’icône exigée, cliquez sur l’icône, puis sélectionnez le fichier image correspondant créé ci-dessus : 

    [![Sélection d’une image d’icône](app-icon-images/intro02.png "Sélection d’une image d’icône")](app-icon-images/intro02-large.png#lightbox)
3. Enregistrez vos modifications.

## <a name="using-the-icon"></a>Utilisation de l’icône

Une fois le fichier `AppIcon.appiconset` généré, il doit être affecté au projet Xamarin.Mac dans Visual Studio pour Mac.

Effectuez les actions suivantes :

1. Double-cliquez sur le fichier **Info.plist** dans le **Panneau Solutions** pour ouvrir les **Options du projet**.
2. Dans la section **Cible d’application Mac OS X**, cliquez sur **Icônes d’application** pour sélectionner le fichier `AppIcon.appiconset` : 

    [![Définition du jeu d’icônes](app-icon-images/icon01.png "Définition du jeu d’icônes")](app-icon-images/icon01-large.png#lightbox)
3. Enregistrez les modifications.

Quand l’application est exécutée, la nouvelle icône s’affiche dans la station d’accueil :

![Exemple d’icône d’application dans la station d’accueil macOS](app-icon-images/icon04.png "Exemple d’icône d’application dans la station d’accueil macOS")

## <a name="summary"></a>Résumé

Cet article a examiné en détail l’utilisation des images nécessaires pour créer une icône d’application macOS, l’empaquetage d’une icône et l’inclusion d’une icône dans un projet Xamarin.Mac.

## <a name="related-links"></a>Liens associés

- [MacImages (exemple)](/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation d’images](~/mac/app-fundamentals/image.md)
- [Human Interface Guidelines pour macOS - Icons and Images](https://developer.apple.com/macos/human-interface-guidelines/icons-and-images/image-size-and-resolution/)
- [À propos de la haute résolution pour OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
- [Générateur d’icônes](https://itunes.apple.com/us/app/icns-builder/id554660130?mt=12)