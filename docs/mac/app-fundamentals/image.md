---
title: Images dans Xamarin. Mac
description: Cet article traite de l’utilisation des images et des icônes dans une application Xamarin. Mac. Il décrit la création et la gestion des images nécessaires à la création de l’icône de votre application C# et l’utilisation d’images dans le code et le Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: C6B539C2-FC6A-4C38-B839-32BFFB9B16A7
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: 197600f713527ed79d9a52768d367589d9edf2fa
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646884"
---
# <a name="images-in-xamarinmac"></a>Images dans Xamarin. Mac

_Cet article traite de l’utilisation des images et des icônes dans une application Xamarin. Mac. Il décrit la création et la gestion des images nécessaires à la création de l’icône de votre application C# et l’utilisation d’images dans le code et le Interface Builder de Xcode._

## <a name="overview"></a>Vue d'ensemble

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes outils d’image et d’icône qu’un développeur travaillant dans *objective-C* et *Xcode* .

Il existe plusieurs façons d’utiliser des ressources d’image dans une application macOS (anciennement Mac OS X). De simplement afficher une image dans le cadre de l’interface utilisateur de votre application à, en l’assignant à un contrôle d’interface utilisateur tel qu’une barre d’outils ou un élément de liste source, en fournissant des icônes, Xamarin. Mac facilite l’ajout de superbes conceptions graphiques à vos applications macOS de l’une des manières suivantes: : 

- **Éléments d’interface utilisateur** : les images peuvent être affichées sous forme d’arrière-plan ou dans le cadre`NSImageView`de votre application dans une vue d’image ().
- **Button** : les images peuvent être affichées dans des`NSButton`boutons ().
- **Cellule d’image** : dans le cadre d’un contrôle basé`NSTableView` sur `NSOutlineView`une table (ou), les images peuvent être utilisées`NSImageCell`dans une cellule d’image ().
- **Élément de barre d’outils** -les images peuvent être ajoutées`NSToolbar`à une barre d’outils ()`NSToolbarItem`en tant qu’élément de barre d’outils d’image ().
- **Icône de liste source** : dans le cadre d’une liste de sources ( `NSOutlineView`une mise en forme spéciale).
- **Icône d’application** : une série d’images peut être regroupée dans `.icns` un ensemble et utilisée comme icône de votre application. Pour plus d’informations, consultez la documentation de notre [icône d’application](~/mac/deploy-test/app-icon.md) .

En outre, macOS fournit un ensemble d’images prédéfinies qui peuvent être utilisées dans l’ensemble de votre application.

[![Exemple d’exécution de l’application](image-images/intro01.png "Exemple d’exécution de l’application")](image-images/intro01-large.png#lightbox)

Dans cet article, nous allons aborder les bases de l’utilisation des images et des icônes dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. Cet article.


## <a name="adding-images-to-a-xamarinmac-project"></a>Ajout d’images à un projet Xamarin. Mac

Lorsque vous ajoutez une image à utiliser dans une application Xamarin. Mac, il existe plusieurs emplacements et manières pour le développeur de pouvoir inclure un fichier image dans la source du projet:

- **Arborescence de projet principale [déconseillé]** -les images peuvent être ajoutées directement à l’arborescence des projets. Lors de l’appel d’images stockées dans l’arborescence de projet principale à partir du code, aucun emplacement de dossier n’est spécifié. Par exemple : `NSImage image = NSImage.ImageNamed("tags.png");`. 
- **Dossier des ressources [déconseillé]** -le dossier **ressources** spéciales concerne tous les fichiers qui feront partie de l’offre groupée de l’application, tels que l’icône, l’écran de lancement ou les images générales (ou toute autre image ou fichier que le développeur souhaite ajouter). Lors de l’appel d’images stockées dans le dossier resources à partir du code, tout comme les images stockées dans l’arborescence de projet principale, aucun emplacement de dossier n’est spécifié. Par exemple : `NSImage.ImageNamed("tags.png")`.
- **Dossier ou sous-dossier personnalisé [déconseillé]** -le développeur peut ajouter un dossier personnalisé à l’arborescence source des projets et y stocker les images. L’emplacement où le fichier est ajouté peut être imbriqué dans un sous-dossier pour faciliter l’organisation du projet. Par exemple, si le développeur a ajouté `Card` un dossier au projet et un sous-dossier `Hearts` de à ce dossier, puis stockez une image **Jack. png** dans `Hearts` le `NSImage.ImageNamed("Card/Hearts/Jack.png")` dossier, chargez l’image au moment de l’exécution.
- **Jeux d’images du catalogue des actifs [preferred]** -ajouté dans OS X El Capitan, les **jeux d’images des catalogues de ressources** contiennent toutes les versions ou représentations d’une image qui sont nécessaires pour prendre en charge différents appareils et facteurs d’échelle pour votre application. Au lieu de s’appuyer sur le nom de fichier **@1x** des **@2x** ressources d’image (,).

<a name="asset-catalogs" />

### <a name="adding-images-to-an-asset-catalog-image-set"></a>Ajout d’images à un ensemble d’images du catalogue de composants

Comme indiqué ci-dessus, un **ensemble d’images de catalogues de ressources** contient toutes les versions ou représentations d’une image qui sont nécessaires pour prendre en charge différents appareils et facteurs d’échelle pour votre application. Au lieu de s’appuyer sur le nom de fichier des ressources d’image (voir la nomenclature indépendante images et image ci-dessus), les **jeux d’images** utilisent l’éditeur de ressources pour spécifier l’image qui appartient à l’appareil et/ou à la résolution.

1. Dans la **panneau solutions**, double-cliquez sur le fichier **Assets. xcassets** pour l’ouvrir et le modifier: 

    ![Sélection de Assets. xcassets](image-images/imageset01.png "Sélection de Assets. xcassets")
2. Cliquez avec le bouton droit sur la **liste ressources** et sélectionnez **nouvel ensemble d’images**: 

    [![Ajout d’un nouveau jeu d’images](image-images/imageset02.png "Ajout d’un nouveau jeu d’images")](image-images/imageset02-large.png#lightbox)
3. Sélectionnez le nouvel ensemble d’images et l’éditeur s’affichera: 

    [![Sélection du nouvel ensemble d’images](image-images/imageset03.png "Sélection du nouvel ensemble d’images")](image-images/imageset03-large.png#lightbox)
4. À partir de là, nous pouvons faire glisser les images pour chacun des différents appareils et résolutions requis. 
5. Double-cliquez sur le **nom** du nouvel ensemble d’images dans la **liste ressources** pour le modifier: 

    [![Modification du nom de l’ensemble d’images](image-images/imageset04.png "Modification du nom de l’ensemble d’images")](image-images/imageset04-large.png#lightbox)
    
Classe **Vector** spéciale ajoutée aux **jeux d’images** qui nous permet d’inclure une image vectorielle au format _PDF_ dans le casset à la place, y compris des fichiers bitmap individuels à des résolutions différentes. À l’aide de cette méthode, vous fournissez un fichier **@1x** de vecteur unique pour la résolution (sous forme de fichier **@2x** PDF **@3x** vectoriel) et les versions et du fichier seront générées au moment de la compilation et incluses dans l’offre groupée de l’application.

[![Interface de l’éditeur d’ensemble d’images](image-images/imageset05.png "Interface de l’éditeur d’ensemble d’images")](image-images/imageset05-large.png#lightbox)

Par exemple, si vous incluez `MonkeyIcon.pdf` un fichier comme vecteur d’un catalogue de composants avec une résolution de 150 px x 150 px, les ressources bitmap suivantes seraient incluses dans l’ensemble d’applications final lors de sa compilation:

1. **MonkeyIcon@1x.png** -150 px x 150 px résolution.
2. **MonkeyIcon@2x.png** -300 pixels x 300 pixels résolution.
3. **MonkeyIcon@3x.png** -450px x 450px résolution.

Les éléments suivants doivent être pris en considération lors de l’utilisation d’images vectorielles PDF dans des catalogues de ressources:

- Il ne s’agit pas d’une prise en charge complète de Vector, car le fichier PDF sera pixellisé en bitmap au moment de la compilation et les bitmaps expédiés dans l’application finale.
- Vous ne pouvez pas ajuster la taille de l’image une fois qu’elle a été définie dans le catalogue de ressources. Si vous tentez de redimensionner l’image (dans le code ou à l’aide des classes de disposition et de taille automatiques), l’image sera déformée comme n’importe quelle autre image bitmap.

Lorsque vous utilisez un **ensemble d’images** dans le Interface Builder de Xcode, vous pouvez simplement sélectionner le nom du jeu dans la liste déroulante de l’inspecteur d' **attribut**:

![Sélection d’un ensemble d’images dans le Interface Builder de Xcode](image-images/imageset06.png "Sélection d’un ensemble d’images dans le Interface Builder de Xcode")

<a name="Adding-new-Assets-Collections"/>

### <a name="adding-new-assets-collections"></a>Ajout de nouveaux regroupements de ressources

Lorsque vous utilisez des images dans des catalogues de ressources, il peut arriver que vous souhaitiez créer une nouvelle collection au lieu d’ajouter toutes vos images à la collection **Assets. xcassets** . Par exemple, lors de la conception de ressources à la demande.

Pour ajouter un nouveau catalogue de ressources à votre projet:

1. Cliquez avec le bouton droit sur le projet dans le **panneau solutions** puis sélectionnez **Ajouter** > **un nouveau fichier...**
2. Sélectionnez**catalogue de ressources** **Mac** > , entrez un **nom** pour le regroupement, puis cliquez sur le bouton **nouveau** : 

    ![Ajout d’un nouveau catalogue de ressources](image-images/asset01.png "Ajout d’un nouveau catalogue de ressources")

À partir de là, vous pouvez travailler avec la collection de la même façon que la collection **Assets. xcassets** par défaut, incluse automatiquement dans le projet.


### <a name="adding-images-to-resources"></a>Ajout d’images aux ressources

> [!IMPORTANT]
> Cette méthode d’utilisation des images dans une application macOS a été dépréciée par Apple. Vous devez utiliser les [ensembles d’images du catalogue de ressources](#asset-catalogs) pour Manager les images de votre application à la place.

Avant de pouvoir utiliser un fichier image dans votre application Xamarin. Mac (dans C# le code ou à partir de Interface Builder), il doit être inclus dans le dossier Resources du projet en tant que **ressource Bundle**. Pour ajouter un fichier à un projet, procédez comme suit:

1. Cliquez avec le bouton droit sur le dossier Resources de votre projet dans le **panneau solutions** puis sélectionnez **Ajouter** > **Ajouter des fichiers...** : 

    ![Ajout d’un fichier](image-images/add01.png "Ajout d’un fichier")
2. Dans la boîte de dialogue **Ajouter des fichiers** , sélectionnez les fichiers images à ajouter au projet, `BundleResource` sélectionnez pour l' **action remplacer la build** , puis cliquez sur le bouton **ouvrir** :

    [![Sélection des fichiers à ajouter](image-images/add02.png "Sélection des fichiers à ajouter")](image-images/add02-large.png#lightbox)
3. Si les fichiers ne se trouvent pas déjà dans le dossier **ressources** , vous êtes invité à **copier**, **déplacer** ou **lier** les fichiers. Choisissez ce qui convient le mieux à vos besoins, généralement à **copier**:

    ![Sélection de l’action Ajouter](image-images/add04.png "Sélection de l’action Ajouter")
4. Les nouveaux fichiers seront inclus dans le projet et lus en vue de leur utilisation: 

    ![Les nouveaux fichiers image ajoutés au panneau solutions](image-images/add03.png "Les nouveaux fichiers image ajoutés au panneau solutions")
5. Répétez le processus pour tous les fichiers image requis.

Vous pouvez utiliser n’importe quel fichier PNG, jpg ou PDF comme image source dans votre application Xamarin. Mac. Dans la section suivante, nous allons nous intéresser à l’ajout de versions haute résolution de nos images et icônes pour la prise en charge de Mac à base de retine.

> [!IMPORTANT]
> Si vous ajoutez des images au dossier **ressources** , vous pouvez conserver la valeur **par défaut**de l' **action remplacer la génération** . L’action de génération par défaut de ce `BundleResource`dossier est.

## <a name="provide-high-resolution-versions-of-all-app-graphics-resources"></a>Fournir des versions haute résolution de toutes les ressources graphiques de l’application

Les ressources graphiques que vous ajoutez à une application Xamarin. Mac (icônes, contrôles personnalisés, curseurs personnalisés, illustrations personnalisées, etc.) doivent avoir des versions haute résolution en plus de leurs versions de résolution standard. Cette opération est nécessaire pour que votre application s’affiche mieux lorsqu’elle est exécutée sur un ordinateur Mac équipé d’un écran de retine.


### <a name="adopt-the-2x-naming-convention"></a>Adopter la @2x Convention d’affectation de noms

> [!IMPORTANT]
> Cette méthode d’utilisation des images dans une application macOS a été dépréciée par Apple. Vous devez utiliser les [ensembles d’images du catalogue de ressources](#asset-catalogs) pour Manager les images de votre application à la place.

Lorsque vous créez les versions standard et haute résolution d’une image, suivez cette Convention d’affectation de noms pour la paire d’images lorsque vous les incluez dans votre projet Xamarin. Mac:

- Résolution  - standard**ImageName. FileName-extension** (exemple: **Tags. png**)
- **Haute résolution**   -  **tags@2x.png** (**exemple:)ImageName@2x.filename-extension**

Lorsqu’ils sont ajoutés à un projet, ils apparaissent comme suit:

![Fichiers image dans le panneau solutions](image-images/add03.png "Fichiers image dans le panneau solutions")

Quand une image est assignée à un élément d’interface utilisateur dans Interface Builder vous allez simplement sélectionner le fichier dans l' _ImageName_ **.** format _de nom de fichier-extension_ (exemple: **Tags. png**). Comme pour l’utilisation d’une image C# dans le code, vous allez choisir le fichier dans l' _ImageName_ **.** format _de nom de fichier_ .

Quand vous exécutez l’application Xamarin. Mac sur un Mac, l' _ImageName_ **.** _NomFichier-_ l’image de format d’extension sera utilisée sur les affichages **ImageName@2x.filename-extension** de résolution standard, l’image sera automatiquement choisie sur la retine des ordinateurs Mac.


## <a name="using-images-in-interface-builder"></a>Utilisation d’images dans Interface Builder

Toute ressource d’image que vous avez ajoutée au dossier Resources dans votre projet Xamarin. Mac et que vous avez défini l’action de génération sur **BundleResource** s’affiche automatiquement dans Interface Builder et peut être sélectionnée dans le cadre d’un élément d’interface utilisateur (si elle gère images).

Pour utiliser une image dans Interface Builder, procédez comme suit:

1. Ajoutez une image au dossier Resources avec une action de `BundleResource` **génération** : 

     ![Une ressource d’image dans le panneau solutions](image-images/ib00.png "Une ressource d’image dans le panneau solutions")
2. Double-cliquez sur le fichier **main. Storyboard** pour l’ouvrir en vue de le modifier dans Interface Builder: 

     [![Modification du storyboard principal](image-images/ib01.png "Modification du storyboard principal")](image-images/ib01-large.png#lightbox)
3. Faites glisser un élément d’interface utilisateur qui prend des images sur l’aire de conception (par exemple, un **élément de barre d’outils image**): 

     ![Modification d’un élément de barre d’outils](image-images/ib02.png "Modification d’un élément de barre d’outils")
4. Sélectionnez l’image que vous avez ajoutée au dossier **ressources** dans la liste déroulante nom de l' **image** : 

     [![Sélection d’une image pour un élément de barre d’outils](image-images/ib03.png "Sélection d’une image pour un élément de barre d’outils")](image-images/ib03-large.png#lightbox)
5. L’image sélectionnée sera affichée dans l’aire de conception: 

     ![Image affichée dans l’éditeur de barres d’outils](image-images/ib04.png "Image affichée dans l’éditeur de barres d’outils")
6. Enregistrez vos modifications et revenez à Visual Studio pour Mac pour effectuer une synchronisation avec Xcode.

Les étapes ci-dessus fonctionnent pour tout élément d’interface utilisateur qui permet de définir sa propriété image dans l' **inspecteur d’attribut**. Là encore, si vous avez inclus **@2x** une version de votre fichier image, celle-ci sera automatiquement utilisée sur les Mac à base d’affichage de la rétine.

> [!IMPORTANT]
> Si l’image n’est pas disponible dans la liste déroulante nom de l' **image** , fermez votre projet. Storyboard dans Xcode et rouvrez-le à partir de Visual Studio pour Mac. Si l’image n’est toujours pas disponible, assurez-vous `BundleResource` que son **action de génération** est et que l’image a été ajoutée au dossier **ressources** .

## <a name="using-images-in-c-code"></a>Utilisation d’images C# dans le code

Lors du chargement d’une image dans C# la mémoire à l’aide du code de votre application Xamarin. Mac, l' `NSImage` image est stockée dans un objet. Si le fichier image a été inclus dans le bundle d’applications Xamarin. Mac (inclus dans les ressources), utilisez le code suivant pour charger l’image:

```csharp
NSImage image = NSImage.ImageNamed("tags.png");
```

Le code ci-dessus utilise `ImageNamed("...")` la méthode statique `NSImage` de la classe pour charger l’image donnée dans la mémoire à partir du dossier Resources, si `null` l’image est introuvable, est retourné. À l’instar des images affectées dans Interface Builder, **@2x** si vous avez inclus une version de votre fichier image, elle est automatiquement utilisée sur les Mac à base d’affichage de la rétine.

Pour charger des images en dehors de l’offre groupée de l’application (à partir du système de fichiers Mac), utilisez le code suivant:

```csharp
NSImage image = new NSImage("/Users/KMullins/Documents/photo.jpg")
```

<a name="Working-with-Template-Images"/>

## <a name="working-with-template-images"></a>Utilisation d’images de modèle

En fonction de la conception de votre application macOS, il peut arriver que vous deviez personnaliser une icône ou une image à l’intérieur de l’interface utilisateur pour qu’elle corresponde à une modification du modèle de couleurs (par exemple, en fonction des préférences de l’utilisateur).

Pour obtenir cet effet, basculez le _mode de rendu_ de votre ressource d’image vers l’image de **modèle**:

[![Définition d’une image de modèle](image-images/templateimage01.png "Définition d’une image de modèle")](image-images/templateimage01-large.png#lightbox)

À partir de l’Interface Builder de Xcode, assignez la ressource d’image à un contrôle d’interface utilisateur:

![Sélection d’une image dans le Interface Builder de Xcode](image-images/templateimage02.png "Sélection d’une image dans le Interface Builder de Xcode")

Vous pouvez également définir la source de l’image dans le code:

```csharp
MyIcon.Image = NSImage.ImageNamed ("MessageIcon");
```

Ajoutez la fonction publique suivante à votre contrôleur d’affichage:

```csharp
public NSImage ImageTintedWithColor(NSImage sourceImage, NSColor tintColor)
    => NSImage.ImageWithSize(sourceImage.Size, false, rect => {
        // Draw the original source image
        sourceImage.DrawInRect(rect, CGRect.Empty, NSCompositingOperation.SourceOver, 1f);

        // Apply tint
        tintColor.Set();
        NSGraphics.RectFill(rect, NSCompositingOperation.SourceAtop);

        return true;
    });
```

> [!IMPORTANT]
> En particulier avec l’avènement du mode Dark dans MacOS Mojave, il est important d’éviter `LockFocus` l’API lorsque des objets de `NSImage` rendu personnalisé atouts. Ces images deviennent statiques et ne sont pas automatiquement mises à jour pour prendre en compte l’apparence ou les modifications de densité d’affichage.
>
> En utilisant le mécanisme basé sur le gestionnaire ci-dessus, le ré-rendu des conditions dynamiques se produit `NSImage` automatiquement lorsque le est hébergé, par exemple `NSImageView`dans un.

Enfin, pour teinter une image de modèle, appelez cette fonction sur l’image à coloriser:

```csharp
MyIcon.Image = ImageTintedWithColor (MyIcon.Image, NSColor.Red);
```

<a name="Using_Images_with_Table_Views" />

## <a name="using-images-with-table-views"></a>Utilisation d’images avec des vues de table

Pour inclure une image en tant que partie de la cellule `NSTableView`dans un, vous devez modifier la façon dont les données sont retournées par `NSTableViewDelegate's` la méthode de `GetViewForItem` la `NSTableCellView` vue table pour utiliser `NSTextField`un au lieu du type standard. Exemple :

```csharp
public override NSView GetViewForItem (NSTableView tableView, NSTableColumn tableColumn, nint row)
{

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)tableView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = true;

        view.TextField.EditingEnded += (sender, e) => {

            // Take action based on type
            switch(view.Identifier) {
            case "Product":
                DataSource.Products [(int)view.TextField.Tag].Title = view.TextField.StringValue;
                break;
            case "Details":
                DataSource.Products [(int)view.TextField.Tag].Description = view.TextField.StringValue;
                break; 
            }
        };
    }

    // Tag view
    view.TextField.Tag = row;

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed ("tags.png");
        view.TextField.StringValue = DataSource.Products [(int)row].Title;
        break;
    case "Details":
        view.TextField.StringValue = DataSource.Products [(int)row].Description;
        break;
    }

    return view;
}
```

Voici quelques lignes d’intérêt ici. Tout d’abord, pour les colonnes pour lesquelles nous souhaitons inclure une image, nous `NSImageView` créons un nouveau de la taille et de l’emplacement requis, `NSTextField` nous créons également une nouvelle position et placent sa position par défaut selon que nous utilisons une image ou non:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Deuxièmement, nous devons inclure la nouvelle vue d’image et le nouveau champ de texte `NSTableCellView`dans le parent:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Enfin, nous devons indiquer au champ de texte qu’il peut être réduit et croître avec la cellule d’affichage de tableau:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Exemple de sortie :

[![Exemple d’affichage d’une image dans une application](image-images/tables01.png "Exemple d’affichage d’une image dans une application")](image-images/tables01-large.png#lightbox)

Pour plus d’informations sur l’utilisation des vues de table, consultez notre documentation sur les [vues de tableau](~/mac/user-interface/table-view.md) .

<a name="Using_Images_with_Outline_Views" />

## <a name="using-images-with-outline-views"></a>Utilisation d’images avec des vues en mode plan

Pour inclure une image en tant que partie de la cellule `NSOutlineView`dans un, vous devez modifier la façon dont les données sont retournées par `NSTableViewDelegate's` la méthode du `GetView` mode `NSTableCellView` plan pour utiliser un `NSTextField`au lieu du type standard. Exemple :

```csharp
public override NSView GetView (NSOutlineView outlineView, NSTableColumn tableColumn, NSObject item) {
    // Cast item
    var product = item as Product;

    // This pattern allows you reuse existing views when they are no-longer in use.
    // If the returned view is null, you instance up a new view
    // If a non-null view is returned, you modify it enough to reflect the new data
    NSTableCellView view = (NSTableCellView)outlineView.MakeView (tableColumn.Title, this);
    if (view == null) {
        view = new NSTableCellView ();
        if (tableColumn.Title == "Product") {
            view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
            view.AddSubview (view.ImageView);
            view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
        } else {
            view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
        }
        view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
        view.AddSubview (view.TextField);
        view.Identifier = tableColumn.Title;
        view.TextField.BackgroundColor = NSColor.Clear;
        view.TextField.Bordered = false;
        view.TextField.Selectable = false;
        view.TextField.Editable = !product.IsProductGroup;
    }

    // Tag view
    view.TextField.Tag = outlineView.RowForItem (item);

    // Allow for edit
    view.TextField.EditingEnded += (sender, e) => {

        // Grab product
        var prod = outlineView.ItemAtRow(view.Tag) as Product;

        // Take action based on type
        switch(view.Identifier) {
        case "Product":
            prod.Title = view.TextField.StringValue;
            break;
        case "Details":
            prod.Description = view.TextField.StringValue;
            break; 
        }
    };

    // Setup view based on the column selected
    switch (tableColumn.Title) {
    case "Product":
        view.ImageView.Image = NSImage.ImageNamed (product.IsProductGroup ? "tags.png" : "tag.png");
        view.TextField.StringValue = product.Title;
        break;
    case "Details":
        view.TextField.StringValue = product.Description;
        break;
    }

    return view;
}
```

Voici quelques lignes d’intérêt ici. Tout d’abord, pour les colonnes pour lesquelles nous souhaitons inclure une image, nous `NSImageView` créons un nouveau de la taille et de l’emplacement requis, `NSTextField` nous créons également une nouvelle position et placent sa position par défaut selon que nous utilisons une image ou non:

```csharp
if (tableColumn.Title == "Product") {
    view.ImageView = new NSImageView (new CGRect (0, 0, 16, 16));
    view.AddSubview (view.ImageView);
    view.TextField = new NSTextField (new CGRect (20, 0, 400, 16));
} else {
    view.TextField = new NSTextField (new CGRect (0, 0, 400, 16));
}
```

Deuxièmement, nous devons inclure la nouvelle vue d’image et le nouveau champ de texte `NSTableCellView`dans le parent:

```csharp
view.AddSubview (view.ImageView);
...

view.AddSubview (view.TextField);
...

```

Enfin, nous devons indiquer au champ de texte qu’il peut être réduit et croître avec la cellule d’affichage de tableau:

```csharp
view.TextField.AutoresizingMask = NSViewResizingMask.WidthSizable;
```

Exemple de sortie :

[![Exemple d’une image affichée en mode plan](image-images/outline01.png "Exemple d’une image affichée en mode plan")](image-images/outline01-large.png#lightbox)

Pour plus d’informations sur l’utilisation des vues en mode plan, consultez notre documentation sur les [modes plan](~/mac/user-interface/outline-view.md) .


## <a name="summary"></a>Récapitulatif

Cet article a décrit en détail l’utilisation des images et des icônes dans une application Xamarin. Mac. Nous avons vu les différents types et utilisations d’images, comment utiliser des images et des icônes dans le Interface Builder de Xcode et comment utiliser des images et C# des icônes dans le code.



## <a name="related-links"></a>Liens associés

- [MacImages (exemple)](https://docs.microsoft.com/samples/xamarin/mac-samples/macimages)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Vues de table](~/mac/user-interface/table-view.md)
- [Modes plan](~/mac/user-interface/outline-view.md)
- [Guide de l’interface utilisateur macOS X](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
- [À propos de la haute résolution pour OS X](https://developer.apple.com/library/content/documentation/GraphicsAnimation/Conceptual/HighResolutionOSX/Introduction/Introduction.html)
