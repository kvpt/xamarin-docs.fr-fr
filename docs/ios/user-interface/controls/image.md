---
title: Affichage d’images avec Xamarin. iOS
description: Ce document explique comment afficher des images dans Xamarin. iOS. Il aborde l’ajout d’images à une application par programme ou par le biais du concepteur iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/13/2018
ms.openlocfilehash: 900a4bf3407b4a3d37f6102ff4b10292edf4bd25
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91434043"
---
# <a name="displaying-images-with-xamarinios"></a>Affichage d’images avec Xamarin. iOS

L’ajout d’images à votre application nécessite deux étapes : tout d’abord, ajoutez les images à votre projet. Ensuite, ajoutez des contrôles et du code pour les afficher sur un écran. Reportez-vous à l’article [utilisation d’images](~/ios/app-fundamentals/images-icons/index.md) pour obtenir une couverture plus détaillée de la gestion des images dans Xamarin. iOS.

## <a name="adding-images-to-your-app"></a>Ajout d’images à votre application

Les images peuvent être ajoutées à n’importe quel dossier de votre solution Visual Studio pour Mac, et si l' **action de génération** est définie sur **contenu** , le fichier est inclus dans votre application et peut être affiché.

Visual Studio pour Mac prend également en charge un répertoire spécial appelé **ressources** qui peut également contenir des fichiers image. L' **action de génération** des fichiers dans le dossier ressources doit être définie sur **BundleResource**.

Cette capture d’écran montre les options d' **action de génération** qui s’affichent lorsque vous cliquez avec le bouton droit sur un fichier :

 [![Menu action de génération](image-images/image30a.png)](image-images/image30a.png#lightbox)

En général, Visual Studio pour Mac choisit automatiquement l' **action de génération** correcte, mais vous devez être conscient de ces paramètres, en particulier si vous déplacez des fichiers dans votre projet.

### <a name="adding-an-image-file"></a>Ajout d’un fichier image

Pour ajouter un fichier image à votre projet, commencez par cliquer avec le bouton droit sur le projet et choisissez **Ajouter des fichiers...**

 [![Ajouter des fichiers... menus](image-images/image31a.png)](image-images/image31a.png#lightbox)

Sélectionnez l’image (ou les images) que vous souhaitez inclure dans la boîte de dialogue fichier standard. L’action de génération par défaut pour les images sera **BundleResource** -ne pas remplacer cette valeur à moins que vous n’ayez une raison spécifique.

 [![Boîte de dialogue Ajouter des fichiers](image-images/image32a.png)](image-images/image32a.png#lightbox)

L’image sera ajoutée à votre projet et disponible pour être chargée et affichée dans le code. Cette capture d’écran montre une image ajoutée à un projet d’application iOS :

 [![Image dans le projet](image-images/image33a.png)](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>Qu’est-ce que le répertoire des ressources ?

Les fichiers placés dans le répertoire **Resources** sont traités différemment des fichiers standard : le contenu du dossier **Resources** est copié à la racine de l’application et peut être référencé à partir de cet emplacement dans votre code. Cela peut être utile pour de nombreuses raisons :

- Stockage des images configurées dans les propriétés de l’application, telles que les images de démarrage par défaut et les icônes d’application.
- Le stockage d’autres images et fichiers séparément du code, afin qu’ils soient plus faciles à gérer (les sous-répertoires sont conservés lors de la copie du contenu du répertoire des ressources).

Le répertoire **Resources** est particulièrement utile dans un projet de bibliothèque, car le code peut supposer que ces images seront copiées dans la racine de l’application consommatrice, ce qui facilite l’écriture de bibliothèques de code partagé qui requièrent des fichiers image, audio, vidéo, XML ou d’autres fichiers.

Le répertoire **Resources** doit être nommé, et l’action de génération doit être définie sur **BundleResource**pour tous les fichiers.

## <a name="displaying-the-image"></a>Affichage de l’image

Dans le concepteur iOS, utilisez une **vue d’image** pour afficher une image ou une série animée d’images. L’icône d' **affichage d’image** de la boîte à outils est illustrée ci-dessous :

 [![ImageView dans la boîte à outils](image-images/image35a.png)](image-images/image35.png#lightbox)

Faites glisser la **vue image** de la **boîte à outils** vers le contrôleur d’affichage. Ensuite, sous **vue d’image > image** , la liste déroulante fournit la liste de tous les fichiers image disponibles dans votre projet. Sélectionnez l’un d’entre eux pour l’ajouter à votre vue d’image.

 [![ImageView dans la boîte à outils](image-images/image36a.png)](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Affichage de l’image par programmation

Étant donné que **df Monkey.jpg** se trouve à la racine du répertoire de **ressources** , il sera disponible au moment de l’exécution dans la racine de l’ensemble d’applications. Pour afficher cette image dans un contrôle d’affichage d’image, utilisez le code suivant :

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Si nous avions placé l’image dans **/Resources/Pics/SF Monkey.jpg**, le code inclurait le dossier **pics** dans le chemin d’accès :

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

Les références de fichier de ressources n’ont jamais besoin d’inclure le dossier **Resources** .

## <a name="related-links"></a>Liens connexes

- [Contrôles (exemple)](/samples/xamarin/ios-samples/controls)