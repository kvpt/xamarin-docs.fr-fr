---
title: Affichage d’une image dans Xamarin.iOS
description: Cet article aborde notamment une ressource image dans une application Xamarin.iOS et l’affichage de cette image à l’aide de code c# ou en l’assignant à un contrôle dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: 60288B12-49E3-4E87-8690-D04A5EC7A664
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/24/2018
ms.openlocfilehash: 6d584c45288d7e7341a4423c32a6966c6c7f21f3
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827901"
---
# <a name="displaying-an-image-in-xamarinios"></a>Affichage d’une image dans Xamarin.iOS

_Cet article aborde notamment une ressource image dans une application Xamarin.iOS et l’affichage de cette image à l’aide de code c# ou en l’assignant à un contrôle dans le concepteur iOS._

## <a name="adding-and-organizing-images-in-a-xamarinios-app"></a>Ajout et l’organisation des images dans une application Xamarin.iOS

Lorsque vous ajoutez une image pour une utilisation dans une application Xamarin.iOS, le développeur utilise une _catalogue_ pour prendre en charge de chaque appareil iOS et la résolution requise par une application.

Ajouté dans iOS 7, **ensembles d’images catalogues Asset** contiennent toutes les versions ou les représentations sous forme d’une image qui est nécessaires pour prendre en charge de divers périphériques et facteurs pour une application d’échelle. Au lieu d’utiliser le nom du fichier de ressources d’image, **ensembles d’images** utiliser un fichier Json pour spécifier quelle image appartient à quels appareils et/ou la résolution. Il s’agit de la meilleure façon de gérer et prendre en charge des images dans iOS (à partir d’iOS 9 ou version ultérieure).

## <a name="adding-images-to-an-asset-catalog-image-set"></a>Ajout d’images à une image de catalogue actif défini

Comme indiqué ci-dessus, un **ensembles d’images catalogues Asset** contiennent toutes les versions ou les représentations sous forme d’une image qui est nécessaires pour prendre en charge de divers périphériques et facteurs pour une application d’échelle. Au lieu d’utiliser le nom du fichier de ressources d’image, **ensembles d’images** utiliser un fichier Json pour spécifier quelle image appartient à quels appareils et/ou la résolution.

Pour créer un nouvel ensemble de l’image et ajouter des images, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le `Assets.xcassets` fichier à ouvrir pour modification :

    ![](displaying-an-image-images/imageset01.png "Le Assets.xcassets dans l’Explorateur de solutions")
2. Avec le bouton droit sur le **liste Assets** et sélectionnez **nouveau jeu d’Image**:

    ![](displaying-an-image-images/imageset02.png "Ajout d’un nouveau jeu d’images")
3. Sélectionnez le nouveau jeu d’images et l’éditeur s’affichera :

    ![](displaying-an-image-images/imageset03.png "L’éditeur d’ensemble de l’Image")
4. À ce stade, faites glisser dans des images pour chacun des différents appareils et résolutions nécessaires. 
5. Double-cliquez sur le nouvel ensemble image **nom** dans le **liste Assets** pour le modifier : ![](displaying-an-image-images/imageset04.png "Nom du nouveau jeu d’images de la modification")

Lorsque vous utilisez un **Image définie** dans le concepteur iOS, il suffit de sélectionner le nom dans la liste déroulante dans l’éditeur de propriétés :

![](displaying-an-image-images/imageset06.png "Sélectionnez le nom d’un ensemble d’image dans la liste déroulante")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrir le catalogue de composants à partir de la **l’Explorateur de solutions**, dans le coin supérieur gauche, cliquez sur le **Plus** bouton :

    ![](displaying-an-image-images/asset5.png "Cliquez sur le signe Plus de bouton")

2. Sélectionnez **ajouter un ensemble de Image** et l’éditeur d’ensemble de l’Image s’affichera pour le nouvel ensemble de l’image. À ce stade, faites glisser dans des images pour chacun des différents appareils et résolutions nécessaires. 

    ![](displaying-an-image-images/asset7.png "Éditeur d’ensembles de l’image")

### <a name="renaming-an-image-set"></a>Modification du nom d’un ensemble d’images

Pour renommer un groupe à haute Image, procédez comme suit :

1. Dans le **l’Explorateur de solutions**, double-cliquez sur le **catalogue** fichier à ouvrir pour modification :

    ![](displaying-an-image-images/rename01.png "Le catalogue de composants dans l’Explorateur de solutions")
2. Sélectionnez le **Image définie** à renommer :

    ![](displaying-an-image-images/rename02.png "Sélectionnez l’ensemble de l’Image à renommer")
3. Dans le **l’Explorateur de propriétés**, faites défiler vers le bas et sélectionnez **nom**(sous le **divers** section) :

    ![](displaying-an-image-images/rename03.png "Sélectionnez le nom sous la section Misc")
4. Entrez un nouveau **nom** pour le **Image définie** et enregistrez les modifications.

-----

Lorsque vous utilisez un **Image définie** dans le code, référencer par nom en appelant le `FromBundle` méthode de la `UIImage` classe. Par exemple :

```csharp
MonkeyImage.Image = UIImage.FromBundle ("PurpleMonkey");
```

> [!IMPORTANT]
> Si les images assignées à un ensemble d’images sont affichent pas correctement, vérifiez que le nom de fichier correct est utilisé avec le `FromBundle` (méthode) (le **Image définie** et non le parent **catalogue** nom). Pour les images PNG, le `.png` extension peut être omise. Pour les autres formats d’image, l’extension est nécessaire (par exemple). `PurpleMonkey.jpg`).

### <a name="using-vector-images-in-asset-catalogs"></a>À l’aide d’images vectorielles dans les catalogues de composants

À compter d’iOS 8, spéciale **vecteur** classe qu’ajoutée au **ensembles d’images** qui permet au développeur d’inclure un **PDF** mis en forme d’image vectorielle dans la cassette au lieu de cela, y compris fichiers bitmap individuels avec les différentes résolutions. À l’aide de cette méthode, fournissez un fichier de vecteur unique pour le `@1x` résolution (mis en forme en tant que fichier PDF vecteur) et le `@2x` et `@3x` versions du fichier seront générées au moment de la compilation et incluses dans le bundle de l’application.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](displaying-an-image-images/imageset05.png "Images de vecteur dans l’éditeur de catalogues de composants")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/asset8.png "Images de vecteur dans l’éditeur de catalogues de composants")

-----

Par exemple, si le développeur inclut un `MonkeyIcon.pdf` fichier en tant que le vecteur d’un catalogue de ressources avec une résolution de 150 px x 150px, la bitmap suivante actifs seraient inclus dans le bundle d’applications finale lorsqu’il a été compilé :

- `MonkeyIcon@1x.png` -150 px x 150px résolution.
- `MonkeyIcon@2x.png` -résolution x 300px de 300 pixels.
- `MonkeyIcon@3x.png` -résolution de x 450px à 450 px.

Les éléments suivants doivent être prises en considération lors de l’utilisation d’images vectorielles PDF dans les catalogues de composants :

- N’est pas prise en charge complète de vecteur et le fichier PDF seront rastérisé vers une bitmap au moment de la compilation et les bitmaps fournies dans l’application finale.
- La taille de l’image n’est pas modifiable une fois qu’il a été défini dans le catalogue de ressources. Si le développeur tente de redimensionner l’image (soit dans le code ou à l’aide de la disposition automatique et les Classes de taille) l’image est déformée comme toute autre image bitmap.
- Catalogues de composants sont uniquement compatibles avec iOS 7 et supérieure, si une application doivent prendre en charge d’iOS 6 ou inférieure, elle ne peut pas utiliser les catalogues de composants.

## <a name="working-with-template-images"></a>Utilisation des images de modèle

En fonction de la conception d’une application iOS, il peut arriver lorsque le développeur a besoin pour personnaliser une icône ou une image à l’intérieur de l’Interface utilisateur afin de refléter une modification dans le jeu de couleurs (par exemple, en fonction des préférences de l’utilisateur).

Pour obtenir facilement cet effet, basculez le _Mode de rendu_ de la ressource Image à **Image de modèle**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](displaying-an-image-images/templateimage01.png "Le Mode de rendu défini sur Image de modèle")](displaying-an-image-images/templateimage01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage01vs.png "Le Mode de rendu défini sur modèle")](displaying-an-image-images/templateimage01vs.png#lightbox)

-----

À partir du concepteur iOS, affecter la ressource Image à un contrôle d’interface utilisateur, puis définissez le **teinte** à mettre en couleur de l’image :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

[![](displaying-an-image-images/templateimage03.png "Définir la teinte à colorer l’image")](displaying-an-image-images/templateimage03.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](displaying-an-image-images/templateimage03vs.png "Définir la teinte à colorer l’image")](displaying-an-image-images/templateimage03vs.png#lightbox)

-----

Si vous le souhaitez, vous peuvent définir le composant de l’Image et la teinte directement dans le code :

```csharp
MyIcon.Image = UIImage.FromBundle ("MessageIcon");
MyIcon.TintColor = UIColor.Red;
```

Pour utiliser une Image de modèle intégralement à partir de code, procédez comme suit :

```csharp
if (MyIcon.Image != null) {
    var mutableImage = MyIcon.Image.ImageWithRenderingMode (UIImageRenderingMode.AlwaysTemplate);
    MyIcon.Image = mutableImage;
    MyIcon.TintColor = UIColor.Red;
}
```

Étant donné que le `RenderMode` propriété d’un `UIImage` est en lecture seule, utilisez la `ImageWithRenderingMode` méthode pour créer une nouvelle instance de l’image avec le paramètre de Mode de rendu souhaité.

Il existe trois éventuellement les paramètres pour `UIImage.RenderMode` via la `UIImageRenderingMode` enum :

* `AlwaysOriginal` -Force l’image à afficher en tant que fichier image source d’origine sans aucune modification.
* `AlwaysTemplate` -Force l’image se présentent sous la forme d’une Image de modèle par Coloriser les pixels avec la valeur `Tint` couleur.
* `Automatic` Que ce soit restitue l’image comme un modèle ou un Original selon l’environnement que celui dans. Par exemple, si l’image est utilisée dans un `UIToolBar`, `UINavigationBar`, `UITabBar` ou `UISegmentControl` il sera traité comme un modèle.

## <a name="adding-new-assets-collections"></a>Ajout de nouvelles collections de ressources

Lorsque vous travaillez avec des images dans les catalogues de ressources il peut arriver lorsqu’une nouvelle collection sera nécessaire, au lieu d’ajouter toutes les images de l’application à la `Assets.xcassets` collection. Par exemple, lors de la conception des ressources de la demande.

Pour ajouter un nouveau catalogue de ressources au projet :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Avec le bouton droit sur le **nom_projet** dans le **l’Explorateur de solutions** et sélectionnez **ajouter** > **nouveau fichier...**
2. Sélectionnez **iOS** > **catalogue**, entrez un **nom** pour la collection et cliquez sur le **New** bouton :

    ![](displaying-an-image-images/asset01.png "Création d’un nouveau catalogue d’actifs")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans l’Explorateur de solutions, cliquez sur **catalogues de composants** dossier, puis sélectionnez **Ajouter > Nouveau catalogue d’actifs**.
2. Donnez-lui un nom et cliquez sur **ajouter**:

    ![](displaying-an-image-images/asset1.png "Création d’un nouveau catalogue d’actifs")

-----

À ce stade, la collection peut être manipulée dans la même façon que la valeur par défaut `Assets.xcassets` collection automatiquement incluse dans le projet.

## <a name="using-images-with-controls"></a>Utilisation des images avec des contrôles

Outre l’utilisation d’images pour prendre en charge une application iOS utilise également des images avec les types de contrôle d’application telles que les barres d’onglets, les barres d’outils, les barres de navigation, les tables et les boutons. Un moyen simple d’afficher une image sur un contrôle consiste à attribuer un `UIImage` instance du contrôle `Image` propriété.

### <a name="frombundle"></a>FromBundle

Le `FromBundle` appel de méthode est un appel de (blocage) synchrone qui a un nombre d’image chargement et la gestion fonctionnalités intégrées, telles que la mise en cache à la prise en charge et la gestion automatique des fichiers image pour différentes résolutions.  

L’exemple suivant montre comment définir l’image d’un `UITabBarItem` sur un `UITabBar`:

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

En supposant que `MyImage` est le nom d’une ressource Image ajoutée à un catalogue de composants ci-dessus. Lorsque vous utilisez des images du catalogue d’actifs, spécifiez simplement le nom de l’ensemble de l’Image dans le `FromBundle` méthode pour **PNG** mis en forme d’images :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage");
```

Pour tout autre format d’image, incluez l’extension portant le nom. Exemple :

```csharp
TabBarItem.Image = UIImage.FromBundle ("MyImage.jpg");
```

Pour plus d’informations sur les icônes et des images, consultez la documentation Apple sur [personnaliser les icônes et les instructions de création d’Image](https://developer.apple.com/library/ios/#documentation/UserExperience/Conceptual/MobileHIG/IconsImages/IconsImages.html).

## <a name="displaying-an-image-in-a-storyboards"></a>Affichage d’une image dans un storyboards

Une fois qu’une image a été ajoutée à un projet Xamarin.iOS à l’aide d’un élément multimédia de catalogues, il peut facilement être affiché sur une table de montage séquentiel à l’aide un `UIImageView` dans le concepteur iOS. Par exemple, si le composant de l’Image suivante a été ajoutée :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![](displaying-an-image-images/display01.png "Un exemple de que composant de l’Image a été ajoutée.")

Procédez comme suit pour l’afficher dans une table de montage séquentiel :

1. Double-cliquez sur le `Main.storyboard` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification dans le concepteur iOS.
2. Sélectionnez un **Image vue** à partir de la **boîte à outils**:

     ![](displaying-an-image-images/display02.png "Sélectionnez un affichage de l’Image à partir de la boîte à outils")
3. Faites glisser de la vue de l’Image sur l’aire de conception et la position et la taille en fonction des besoins :

    ![](displaying-an-image-images/display03.png "Une nouvelle vue de l’Image sur l’aire de conception")
4. Dans le **Widget** section de la **Explorateur de propriétés** sélectionnez souhaité **Image** actif à afficher :

    ![](displaying-an-image-images/display04.png "Sélectionnez le composant souhaité de l’Image à afficher")
5. Dans le **vue** section, utilisez la **Mode** pour contrôler la façon dont l’image sera redimensionné quand la **affichage de l’Image** est redimensionné.
6. Avec le **affichage de l’Image** sélectionné, cliquez à nouveau pour ajouter **contraintes**:

    ![](displaying-an-image-images/display05.png "Ajout de contraintes")
7. Faites glisser la poignée de « T » en forme sur chaque bord de la **affichage de l’Image** vers le côté correspondant de l’écran pour l’image sur les côtés « épingler ». De cette façon, le **affichage de l’Image** sera variations de l’écran est redimensionné.
8. Enregistrez les modifications dans la table de montage séquentiel.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](displaying-an-image-images/display01vs.png "Un exemple de que composant de l’Image a été ajoutée.")

Procédez comme suit pour l’afficher dans une table de montage séquentiel :

1. Double-cliquez sur le `Main.storyboard` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification dans le concepteur iOS.
2. Sélectionnez un **Image vue** à partir de la **boîte à outils**:

     ![](displaying-an-image-images/display02vs.png "Sélectionnez un affichage de l’Image à partir de la boîte à outils")
3. Faites glisser de la vue de l’Image sur l’aire de conception et la position et la taille en fonction des besoins :

    ![](displaying-an-image-images/display03vs.png "Une nouvelle vue de l’Image sur l’aire de conception")
4. Dans le **Widget** section de la **Explorateur de propriétés** sélectionnez souhaité **Image** actif à afficher :

    ![](displaying-an-image-images/display04vs.png "Sélectionnez le composant souhaité de l’Image à afficher")
5. Dans le **vue** section, utilisez la **Mode** pour contrôler la façon dont l’image sera redimensionné quand la **affichage de l’Image** est redimensionné.
6. Avec le **affichage de l’Image** sélectionné, cliquez à nouveau pour ajouter **contraintes**:

    ![](displaying-an-image-images/display05vs.png "Ajout de contraintes")
7. Faites glisser la poignée de « T » en forme sur chaque bord de la **affichage de l’Image** vers le côté correspondant de l’écran pour l’image sur les côtés « épingler ». De cette façon, le **affichage de l’Image** sera variations de l’écran est redimensionné.
8. Enregistrez les modifications dans la table de montage séquentiel.

-----

## <a name="displaying-an-image-in-code"></a>Affichage d’une image dans le code

Tout comme l’affichage d’une image dans une table de montage séquentiel, une fois qu’une image a été ajoutée à un projet Xamarin.iOS à l’aide d’un catalogues de composants, il peut être facilement affiché à l’aide de code c#.

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

Ce code crée un nouveau `UIImageView` et lui donne une taille initiale et une position. Ensuite, il charge l’image à partir d’un composant de l’Image ajoutée au projet et ajoute le `UIImageView` au parent `UIView` pour l’afficher.

## <a name="related-links"></a>Liens connexes

- [Utilisation d’Images (exemple)](https://developer.xamarin.com/samples/monotouch/WorkingWithImages/)
- [Hello, iPhone](~/ios/get-started/hello-ios/index.md)
- [Taille de l’image et de résolution (Apple)](https://developer.apple.com/design/human-interface-guidelines/ios/icons-and-images/image-size-and-resolution/)
