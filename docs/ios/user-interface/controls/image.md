---
title: Affichage des images avec Xamarin.iOS
description: Ce document décrit comment afficher des images dans Xamarin.iOS. Il couvre l’ajout d’images à une application par programmation ou via le concepteur iOS.
ms.prod: xamarin
ms.assetid: 67CA8DB6-769D-42BB-A137-3AF933789FE1
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/13/2018
ms.openlocfilehash: 25c5595fac2ea2aa7a87d6640cc6b5c399ab5e5e
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827806"
---
# <a name="displaying-images-with-xamarinios"></a>Affichage des images avec Xamarin.iOS

Ajout d’images à votre application requiert deux étapes : tout d’abord, ajoutez les images à votre projet ; Ensuite, ajoutez des contrôles et du code pour les afficher sur un écran. Reportez-vous à la [utilisation des images](~/ios/app-fundamentals/images-icons/index.md) article pour plus d’une couverture d’image gère dans Xamarin.iOS.

## <a name="adding-images-to-your-app"></a>Ajout d’images à votre application

Les images peuvent être ajoutés à n’importe quel dossier dans Visual Studio pour la solution de Mac et si le **Action de génération** a la valeur **contenu** ensuite le fichier sera inclus dans votre application et peuvent être affiché.

Visual Studio pour Mac prend également en charge un répertoire spécial appelé **ressources** qui peut également contenir des fichiers image. Les fichiers dans le dossier de ressources doivent avoir le **Action de génération** définie sur **BundleResource**.

Cette capture d’écran montre le **Action de génération** options qui s’affichent quand un fichier est clic :

 [![](image-images/image30a.png "Menu Action de génération")](image-images/image30a.png#lightbox)

Visual Studio pour Mac choisit généralement la bonne **Action de génération** automatiquement mais vous devez être conscient de ces paramètres, en particulier si vous déplacer des fichiers dans votre projet.

### <a name="adding-an-image-file"></a>Ajout d’un fichier image

Pour ajouter un fichier image à votre projet, tout d’abord cliquez sur le projet et choisissez **ajouter des fichiers...**

 [![](image-images/image31a.png "Ajouter des fichiers... menu")](image-images/image31a.png#lightbox)

Sélectionnez l’image (ou images) à inclure dans la boîte de dialogue de fichier standard. Action de génération de la valeur par défaut pour les images seront **BundleResource** – ne pas remplacer cette valeur, sauf si vous avez une raison spécifique.

 [![](image-images/image32a.png "Ajouter la boîte de dialogue de fichiers")](image-images/image32a.png#lightbox)

L’image sera ajoutée à votre projet et disponibles pour être chargés et affichés dans le code. Cette capture d’écran montre l’image ajoutée à un projet d’application iOS :

 [![](image-images/image33a.png "Image dans le projet")](image-images/image33a.png#lightbox)

### <a name="what-is-the-resources-directory"></a>Qu’est le répertoire des ressources ?

Les fichiers placés dans le **ressources** répertoire sont traitées différemment à partir de fichiers régulières : le contenu de la **ressources** dossier sont copiés à la racine de l’application et peuvent être référencées à partir de là dans votre code. Cela peut être utile pour de nombreuses raisons :

-  Stockage des images configurés dans les propriétés de l’application, telles que les images de démarrage par défaut et les icônes d’application.
-  Stockage autres images et des fichiers séparément à partir du code, par conséquent, elles sont plus faciles à gérer (les sous-répertoires sont conservées lorsque le contenu du répertoire ressources est copié).


Le **ressources** directory est particulièrement utile dans un projet de bibliothèque dans la mesure où le code peut supposer que ces images sont copiés dans la racine de l’application consommateur, ce qui facilite les bibliothèques de code partagé en écriture qui nécessitent vidéo, audio, image, XML ou d’autres fichiers.

Le **ressources** répertoire doit être nommé par conséquent, et tous les fichiers doivent avoir la valeur est l’action de génération **BundleResource**.

## <a name="displaying-the-image"></a>Affichage de l’image

Dans le concepteur iOS, utilisez un **affichage de l’Image** pour afficher une image ou une série animée d’images. Le **affichage de l’Image** icône à partir de la boîte à outils est indiqué ci-dessous :

 [![](image-images/image35a.png "ImageView dans la boîte à outils")](image-images/image35.png#lightbox)

Faites glisser le **affichage de l’Image** à partir de la **boîte à outils** sur le contrôleur d’affichage. Ensuite, sous **affichage de l’Image > Image** la liste déroulante fournira une liste de tous les fichiers d’image disponibles dans votre projet. Sélectionnez un d'entre eux pour l’ajouter à votre affichage de l’image.

 [![](image-images/image36a.png "ImageView dans la boîte à outils")](image-images/image36.png#lightbox)

### <a name="displaying-the-image-programmatically"></a>Affichage de l’image par programme

Étant donné que **SF Monkey.jpg** se trouve dans la racine de la **ressources** directory il sera disponible lors de l’exécution dans la racine de l’offre application. Pour afficher cette image dans un contrôle image, utilisez le code suivant :

```csharp
imageview1.Image = UIImage.FromBundle("SF Monkey.png");
```

Si nous avions placé l’image dans **Pics/ressources/SF Monkey.jpg**, puis le code inclut le **Pics** dossier dans le chemin d’accès :

```csharp
imageview1.Image = UIImage.FromBundle("Pics/SF Monkey.png");
```

Fichier de ressources fait référence à jamais nécessaire d’inclure le **ressources** dossier.

## <a name="related-links"></a>Liens connexes

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
