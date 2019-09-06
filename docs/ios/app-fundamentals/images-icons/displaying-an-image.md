---
title: Affichage d’une image dans Xamarin. iOS
description: Cet article aborde l’inclusion d’une ressource d’image dans une application Xamarin. iOS et l’affichage C# de cette image en utilisant du code ou en l’affectant à un contrôle dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/24/2018
ms.openlocfilehash: 8e06b03e0acb129f1eb0c3c793d0b4a05c11ce2b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70282376"
---
# <a name="displaying-an-image-in-xamarinios"></a>Affichage d’une image dans Xamarin. iOS

_Cet article aborde l’inclusion d’une ressource d’image dans une application Xamarin. iOS et l’affichage C# de cette image en utilisant du code ou en l’affectant à un contrôle dans le concepteur iOS._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Ajout et organisation d’images dans une application Xamarin. iOS

Quand vous ajoutez une image à utiliser dans une application Xamarin. iOS, le développeur utilise un _catalogue de ressources_ pour prendre en charge chaque appareil iOS et la résolution requises par une application.

Ajouté dans iOS 7, les **jeux d’images des catalogues de ressources** contiennent toutes les versions ou représentations d’une image qui sont nécessaires pour prendre en charge différents appareils et facteurs d’échelle pour une application. Au lieu de s’appuyer sur le nom de fichier des ressources d’image, les **jeux d’images** utilisent un fichier JSON pour spécifier l’image qui appartient à l’appareil et/ou à la résolution. Il s’agit de la méthode recommandée pour gérer et prendre en charge les images dans iOS (à partir d’iOS 9 ou version ultérieure).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Ajout d’images à un ensemble d’images du catalogue de composants

Comme indiqué ci-dessus, un **ensemble d’images de catalogues de ressources** contient toutes les versions ou représentations d’une image qui sont nécessaires pour prendre en charge différents appareils et facteurs d’échelle pour une application. Au lieu de s’appuyer sur le nom de fichier des ressources d’image, les **jeux d’images** utilisent un fichier JSON pour spécifier l’image qui appartient à l’appareil et/ou à la résolution.

Pour créer un ensemble d’images et y ajouter des images, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans la **Explorateur de solutions**, double-cliquez sur `Assets.xcassets` le fichier pour l’ouvrir et le modifier :

    ![](displaying-an-image-images/imageset01.png "Ressources. xcassets dans le Explorateur de solutions")
2. Cliquez avec le bouton droit sur la **liste ressources** et sélectionnez **nouvel ensemble d’images**:

    ![](displaying-an-image-images/imageset02.png "Ajout d’un nouveau jeu d’images")
3. Sélectionnez le nouvel ensemble d’images et l’éditeur s’affichera :

    ![](displaying-an-image-images/imageset03.png "Éditeur d’ensemble d’images")
4. À partir de là, faites glisser les images pour chacun des différents appareils et résolutions requis.
5. Double-cliquez sur le **nom** du nouvel ensemble d’images dans la **liste ressources** pour le modifier : ![](displaying-an-image-images/imageset04.png "Modification du nom du nouvel ensemble d’images")

Quand vous utilisez un **ensemble d’images** dans le concepteur iOS, il vous suffit de sélectionner le nom du jeu dans la liste déroulante de l’éditeur de propriétés :

![](displaying-an-image-images/imageset06.png "Sélectionner le nom d’un ensemble d’images dans la liste déroulante")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrez le catalogue de ressources à partir de la **Explorateur de solutions**et, dans le coin supérieur gauche, cliquez sur le bouton **plus** :

    ![](displaying-an-image-images/asset5.png "Cliquez sur le bouton plus")

2. Sélectionnez **Ajouter un ensemble d’images** et l’éditeur d’images s’affichera pour le nouvel ensemble d’images. À partir de là, faites glisser les images pour chacun des différents appareils et résolutions requis.

    ![](displaying-an-image-images/asset7.png "Éditeur d’ensemble d’images")

### <a name="renaming-an-image-set"></a>Attribution d’un nouveau nom à un ensemble d’images

Pour renommer un ensemble d’images, procédez comme suit :

1. Dans la **Explorateur de solutions**, double-cliquez sur le fichier de **catalogue de ressources** pour l’ouvrir et le modifier :

    ![](displaying-an-image-images/rename01.png "Le catalogue de ressources dans le Explorateur de solutions")
2. Sélectionnez l' **ensemble d’images** à renommer :

    ![](displaying-an-image-images/rename02.png "Sélectionner l’ensemble d’images à renommer")
3. Dans l' **Explorateur de propriétés**, faites défiler vers le bas et sélectionnez **nom**(sous la section **divers** ) :

    ![](displaying-an-image-images/rename03.png "Sélectionnez nom sous la section divers")
4. Entrez un nouveau **nom** pour l' **ensemble d’images** et enregistrez les modifications.

-----

Quand vous utilisez un **ensemble d’images** dans le code, référencez-le `FromBundle` par nom en `UIImage` appelant la méthode de la classe. Par exemple :

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Si les images affectées à un ensemble d’images ne s’illustrent pas correctement, assurez-vous que le nom `FromBundle` de fichier correct est utilisé avec la méthode (l' **ensemble d’images** et non le nom du **catalogue de ressources** parent). Pour les images PNG, `.png` l’extension peut être omise. Pour les autres formats d’image, l’extension est requise (par exemple, `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>Utilisation d’images vectorielles dans des catalogues de ressources

À compter d’iOS 8, une classe **vectorielle** spéciale, ajoutée aux **jeux d’images** , permet au développeur d’inclure une image vectorielle au format **PDF** dans la cassette, en incluant des fichiers bitmap individuels aux différentes résolutions. À l’aide de cette méthode, fournissez un fichier `@1x` de vecteur unique pour la résolution (sous forme de fichier `@2x` PDF `@3x` vectoriel) et les versions et du fichier seront générées au moment de la compilation et incluses dans l’offre groupée de l’application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Images vectorielles dans l’éditeur de catalogues de composants")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Images vectorielles dans l’éditeur de catalogues de composants")

-----

Par exemple, si le développeur inclut un `MonkeyIcon.pdf` fichier comme vecteur d’un catalogue de composants avec une résolution de 150 px x 150 px, les ressources bitmap suivantes sont incluses dans l’ensemble d’applications final lors de la compilation :

- `MonkeyIcon@1x.png`-150 px x 150 px résolution.
- `MonkeyIcon@2x.png`-300 pixels x 300 pixels résolution.
- `MonkeyIcon@3x.png`-450px x 450px résolution.

Les éléments suivants doivent être pris en considération lors de l’utilisation d’images vectorielles PDF dans des catalogues de ressources :

- Il ne s’agit pas d’une prise en charge complète de Vector, car le fichier PDF sera pixellisé en bitmap au moment de la compilation et les bitmaps expédiés dans l’application finale.
- La taille de l’image ne peut pas être ajustée une fois qu’elle a été définie dans le catalogue de ressources. Si le développeur tente de redimensionner l’image (dans le code ou à l’aide des classes de disposition et de taille automatiques), l’image sera déformée comme n’importe quelle autre image bitmap.
- Les catalogues de ressources sont uniquement compatibles avec iOS 7 et versions ultérieures. Si une application doit prendre en charge iOS 6 ou une version antérieure, elle ne peut pas utiliser les catalogues de ressources.

## <a name="working-with-template-images"></a>Utilisation d’images de modèle

En fonction de la conception d’une application iOS, il peut arriver que le développeur doive personnaliser une icône ou une image à l’intérieur de l’interface utilisateur pour qu’elle corresponde à une modification du modèle de couleurs (par exemple, en fonction des préférences de l’utilisateur).

Pour obtenir facilement cet effet, basculez le _mode de rendu_ de la ressource d’image vers l’image de **modèle**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "Mode de rendu défini sur l’image de modèle")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "Mode de rendu défini sur modèle")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

À partir du concepteur iOS, assignez la ressource d’image à un contrôle d’interface utilisateur, puis définissez la **teinte** pour colorier l’image :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Définir la teinte pour coloriser l’image")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Définir la teinte pour coloriser l’image")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Si vous le souhaitez, la ressource et la teinte de l’image peuvent être définies directement dans le code :

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Pour utiliser complètement une image de modèle à partir du code, procédez comme suit :

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Étant donné `RenderMode` que la propriété `UIImage` d’un est en lecture seule `ImageWithRenderingMode` , utilisez la méthode pour créer une nouvelle instance de l’image avec le paramètre de mode de rendu souhaité.

Il existe trois paramètres possibles pour `UIImage.RenderMode` via l' `UIImageRenderingMode` énumération :

- `AlwaysOriginal`: Force le rendu de l’image en tant que fichier image source d’origine sans aucune modification.
- `AlwaysTemplate`: Force le rendu de l’image en tant qu’image de modèle en coloriant les pixels avec `Tint` la couleur spécifiée.
- `Automatic`: Effectue le rendu de l’image en tant que modèle ou original en fonction de l’environnement dans lequel elle est utilisée. Par exemple, si l’image est utilisée dans un `UIToolBar` `UITabBar` , `UINavigationBar`ou `UISegmentControl` si elle est traitée comme un modèle.

## <a name="adding-new-assets-collections"></a>Ajout de nouveaux regroupements de ressources

Lorsque vous utilisez des images dans des catalogues de ressources, il peut arriver qu’une nouvelle collection soit requise, au lieu d’ajouter toutes les images de l’application `Assets.xcassets` à la collection. Par exemple, lors de la conception de ressources à la demande.

Pour ajouter un nouveau catalogue de ressources au projet :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Cliquez avec le bouton droit sur le **nom du projet** dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **un nouveau fichier...**
2. Sélectionnez**catalogue de ressources** **iOS** > , entrez un **nom** pour le regroupement, puis cliquez sur le bouton **nouveau** :

    ![](displaying-an-image-images/asset01.png "Création d’un catalogue de ressources")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans le Explorateur de solutions, cliquez avec le bouton droit sur le dossier **catalogues de ressources** , puis sélectionnez **Ajouter > nouveau catalogue de ressources**.
2. Donnez-lui un nom et cliquez sur **Ajouter**:

    ![](displaying-an-image-images/asset1.png "Création d’un catalogue de ressources")

-----

À partir de là, la collection peut être utilisée de la même façon que la `Assets.xcassets` collection par défaut incluse automatiquement dans le projet.

## <a name="using-images-with-controls"></a>Utilisation d’images avec des contrôles

En plus d’utiliser des images pour prendre en charge une application, iOS utilise également des images avec des types de contrôle d’application tels que des barres d’onglets, des barres d’outils, des barres de navigation, des tables et des boutons. Un moyen simple de faire apparaître une image sur un contrôle consiste à assigner une `UIImage` instance à la propriété du `Image` contrôle.

### <a name="frombundle"></a>FromBundle

L' `FromBundle` appel de méthode est un appel synchrone (bloquant) qui a un certain nombre de fonctionnalités de chargement et de gestion d’image intégrées, telles que la prise en charge de la mise en cache et la gestion automatique des fichiers image pour diverses résolutions.

L’exemple suivant montre comment définir l’image d’un `UITabBarItem` sur un : `UITabBar`

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

En supposant `MyImage` que est le nom d’une ressource d’image ajoutée à un catalogue de ressources ci-dessus. Lorsque vous travaillez sur des images du catalogue de ressources, spécifiez simplement le nom `FromBundle` de l’ensemble d’images dans la méthode pour les images au format **png** :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Pour tout autre format d’image, incluez l’extension portant le nom. Exemple :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Pour plus d’informations sur les icônes et les images, consultez la documentation Apple sur l' [icône personnalisée et les instructions de création d’image](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Affichage d’une image dans des storyboards

Une fois qu’une image a été ajoutée à un projet Xamarin. IOS à l’aide de catalogues de ressources, elle peut être facilement affichée sur `UIImageView` une table de montage séquentiel à l’aide d’un dans le concepteur iOS. Par exemple, si la ressource d’image suivante a été ajoutée :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](displaying-an-image-images/display01.png "Un exemple d’élément multimédia d’image a été ajouté")

Procédez comme suit pour l’afficher sur une table de montage séquentiel :

1. Double-cliquez sur `Main.storyboard` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier dans le concepteur iOS.
2. Sélectionnez une **vue d’image** à partir de la **boîte à outils**:

     ![](displaying-an-image-images/display02.png "Sélectionner une vue d’image à partir de la boîte à outils")
3. Faites glisser la vue image sur l’aire de conception et positionnez-la et redimensionnez-la en fonction des besoins :

    ![](displaying-an-image-images/display03.png "Nouvelle vue d’image sur le Aire de conception")
4. Dans la section **widget** de l' **Explorateur de propriétés** , sélectionnez la ressource d' **image** que vous souhaitez afficher :

    ![](displaying-an-image-images/display04.png "Sélectionnez la ressource d’image souhaitée à afficher")
5. Dans la section **vue** , utilisez le **mode** pour contrôler la façon dont l’image est redimensionnée lorsque la **vue d’image** est redimensionnée.
6. Une fois la **vue d’image** sélectionnée, cliquez à nouveau dessus pour ajouter des **contraintes**:

    ![](displaying-an-image-images/display05.png "Ajout de contraintes")
7. Faites glisser la poignée de mise en forme « T » sur chaque bord de la **vue d’image** vers le côté correspondant de l’écran pour « épingler » l’image sur les côtés. De cette façon, la **vue d’image** sera réduite et croître à mesure que l’écran sera redimensionné.
8. Enregistrez les modifications apportées à la table de montage séquentiel.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "Un exemple d’élément multimédia d’image a été ajouté")

Procédez comme suit pour l’afficher sur une table de montage séquentiel :

1. Double-cliquez sur `Main.storyboard` le fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier dans le concepteur iOS.
2. Sélectionnez une **vue d’image** à partir de la **boîte à outils**:

     ![](displaying-an-image-images/display02vs.png "Sélectionner une vue d’image à partir de la boîte à outils")
3. Faites glisser la vue image sur l’aire de conception et positionnez-la et redimensionnez-la en fonction des besoins :

    ![](displaying-an-image-images/display03vs.png "Nouvelle vue d’image sur le Aire de conception")
4. Dans la section **widget** de l' **Explorateur de propriétés** , sélectionnez la ressource d' **image** que vous souhaitez afficher :

    ![](displaying-an-image-images/display04vs.png "Sélectionnez la ressource d’image souhaitée à afficher")
5. Dans la section **vue** , utilisez le **mode** pour contrôler la façon dont l’image est redimensionnée lorsque la **vue d’image** est redimensionnée.
6. Une fois la **vue d’image** sélectionnée, cliquez à nouveau dessus pour ajouter des **contraintes**:

    ![](displaying-an-image-images/display05vs.png "Ajout de contraintes")
7. Faites glisser la poignée de mise en forme « T » sur chaque bord de la **vue d’image** vers le côté correspondant de l’écran pour « épingler » l’image sur les côtés. De cette façon, la **vue d’image** sera réduite et croître à mesure que l’écran sera redimensionné.
8. Enregistrez les modifications apportées à la table de montage séquentiel.

-----

## <a name="displaying-an-image-in-code"></a>Affichage d’une image dans le code

Tout comme l’affichage d’une image dans un Storyboard, une fois qu’une image a été ajoutée à un projet Xamarin. iOS à l’aide d’un catalogue de ressources C# , elle peut être facilement affichée à l’aide de code.

Prenons l’exemple suivant :

```csharp
// Create an image view that will fill the
// parent image view and set the image from
// an Image Asset

var imageView = new UIImageView (View.Frame);
imageView.Image = UIImage.FromBundle ("Kemah");

// Add the Image View to the parent view
View.AddSubview (imageView);
```

Ce code crée un nouveau `UIImageView` et lui attribue une taille et une position initiales. Ensuite, il charge l’image à partir d’une ressource d’image ajoutée au projet `UIImageView` et ajoute le `UIView` au parent pour l’afficher.

## <a name="related-links"></a>Liens connexes

- [Utilisation des images (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/workingwithimages)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Taille et résolution de l’image (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
