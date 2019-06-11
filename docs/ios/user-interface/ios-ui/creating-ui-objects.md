---
title: Création d’objets d’Interface utilisateur dans Xamarin.iOS
description: Ce document fournit une vue d’ensemble de la création d’une interface utilisateur dans Xamarin.iOS. Il aborde le concepteur iOS, Xcode Interface Builder, C#et les tables de montage séquentiel.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 65ebbddfa1a315a478ceb43162b3c80eba2d8cc1
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827801"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Création d’objets d’Interface utilisateur dans Xamarin.iOS

Apple regroupe des connexes des fonctionnalités dans « frameworks », qui sont équivalentes aux espaces de noms Xamarin.iOS. `UIKit` est l’espace de noms qui contient tous les contrôles d’interface utilisateur pour iOS.

Chaque fois que votre code doit faire référence à un contrôle d’interface utilisateur, tel qu’une étiquette ou un bouton, veillez à inclure les éléments suivants à l’aide d’instruction :

```csharp
using UIKit;
```

Tous les contrôles abordés dans cet article sont dans l’espace de noms UIKit, et chaque nom de classe de contrôle utilisateur possède le `UI` préfixe.

Vous pouvez modifier les contrôles d’interface utilisateur et les dispositions de trois façons :

-  **[Concepteur de Xamarin iOS](~/ios/user-interface/designer/index.md)**  : Concepteur de présentation intégrés de Xamarin d’utilisation à concevoir les écrans. Double-cliquez sur table de montage séquentiel ou les fichiers XIB à modifier avec le concepteur intégré.
-  **Xcode Interface Builder** : faites glisser des contrôles sur vos dispositions d’écran avec Interface Builder. Ouvrez la table de montage séquentiel ou d’un fichier XIB dans Xcode en double-cliquant sur le fichier dans le **panneau solutions** et en choisissant **ouvrir avec > Xcode Interface Builder**.
-  **À l’aide de C#**  – contrôles peuvent également être par programmation construits avec code et ajoutées à la hiérarchie d’affichage.

Nouveaux fichiers de table de montage séquentiel et XIB peuvent être ajoutés en cliquant sur un projet iOS et en choisissant **Ajouter > nouveau fichier...** .

Quelle que soit la méthode que vous utilisez, les propriétés de contrôle et les événements peuvent être manipulés avec C# dans votre logique d’application.

## <a name="using-xamarin-ios-designer"></a>À l’aide du Concepteur de Xamarin iOS

Pour commencer à créer votre interface utilisateur dans le concepteur iOS, double-cliquez sur un fichier de storyboard. Les contrôles peuvent être déplacés sur l’aire de conception à partir de la **boîte à outils** comme illustré ci-dessous :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Panneau boîte à outils")](creating-ui-objects-images/image2b.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Panneau boîte à outils - Visual Studio")](creating-ui-objects-images/image2b.png#lightbox)
 
-----

Quand un contrôle est sélectionné sur l’aire de conception le **panneau Propriétés** affichent les attributs pour ce contrôle. Le **Widget > identité > nom** champ, qui est remplie dans la capture d’écran ci-dessous, est utilisé comme le *Outlet* nom. Voici comment vous pouvez référencer le contrôle dans C#:

 [![](creating-ui-objects-images/image3b.png "Panneau de Widget de propriétés")](creating-ui-objects-images/image3b.png#lightbox)

Pour approfondir vos connaissances à l’aide du concepteur iOS, reportez-vous à la [présentation du concepteur iOS](~/ios/user-interface/designer/introduction.md) guide.

## <a name="using-xcode-interface-builder"></a>À l’aide de Xcode Interface Builder

Si vous n’êtes pas familiarisé avec l’utilisation du Générateur d’Interface, consultez d’Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/) documents.

Pour ouvrir une table de montage séquentiel dans Xcode, cliquez sur pour accéder au menu contextuel pour le fichier de storyboard et choisissez Ouvrir avec le **Xcode Interface Builder**:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Menu contextuel de table de montage séquentiel, Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)
 
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Menu contextuel de table de montage séquentiel, Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Les contrôles peuvent être déplacés sur l’aire de conception à partir de la **bibliothèque d’objets** illustré ci-dessous :

 [![](creating-ui-objects-images/image5a.png "Bibliothèque d’objets de Xcode")](creating-ui-objects-images/image5a.png#lightbox)

Lorsque vous concevez votre interface utilisateur avec Interface Builder, vous devez créer un **Outlet** pour chaque contrôle que vous voulez référencer dans C#. Cela en activant le **éditeur de l’Assistant** à l’aide du centre de **éditeur** bouton sur le bouton de barre d’outils Xcode :

 [![](creating-ui-objects-images/image6a.png "Bouton de l’éditeur de l’Assistant")](creating-ui-objects-images/image6a.png#lightbox)

Cliquez sur un objet d’interface utilisateur ; puis **faites glisser le contrôle** dans le fichier .h. Pour **faites glisser le contrôle**, maintenez enfoncée la touche CTRL, puis cliquez sur et sur l’objet d’interface utilisateur que vous créez l’outlet (ou action) pour. Maintenez la touche CTRL pendant que vous faites glisser dans le fichier d’en-tête. Terminer en faisant glisser sous le `@interface` définition. Une ligne bleue doit apparaître avec une légende Outlet insérer ou une Collection de sortie, comme illustré dans la capture d’écran ci-dessous.

Lorsque vous relâchez le clic, vous êtes invité à fournir un nom pour la sortie, ce qui permet de créer un C# propriété qui peut être référencée dans le code :

 [![](creating-ui-objects-images/image8a.png "Création d’un outlet")](creating-ui-objects-images/image8a.png#lightbox)

Pour plus d’informations sur la façon dont les Interface Builder de Xcode s’intègre à Visual Studio pour Mac, reportez-vous à la [génération de Code Xib](~/ios/internals/xib-code-generation.md#generated) document.

##  <a name="using-c"></a>À l’aide deC#

Si vous décidez de créer par programme un objet d’interface utilisateur à l’aide C# (dans une vue ou un contrôleur d’affichage, par exemple), procédez comme suit :

-  Déclarer un champ de niveau classe pour l’objet d’interface utilisateur. Créer le contrôle proprement dit dans une seule fois, `ViewDidLoad` par exemple. L’objet peut ensuite être référencée dans les méthodes de cycle de vie du contrôleur d’affichage (par exemple).
`ViewWillAppear`).
-  Créer un `CGRect` qui définit le frame du contrôle (ses coordonnées X et Y sur l’écran, ainsi que sa largeur et sa hauteur). Vous devez vous assurer que vous avez un `using CoreGraphics` directive pour cela.
-  Appelez le constructeur pour créer et assigner le contrôle.
-  Définir des propriétés ou des gestionnaires d’événements.
-  Appelez `Add()` pour ajouter le contrôle à la hiérarchie d’affichage.

Voici un exemple simple de création d’un `UILabel` dans un contrôleur d’affichage à l’aide C#:

```csharp
UILabel label1;
public override void ViewDidLoad () {
    base.ViewDidLoad ();
    var frame = new CGRect(10, 10, 300, 30);
    label1 = new UILabel(frame);
    label1.Text = "New Label";
    View.Add (label1);
}
```

<a name="partial_classes" />

## <a name="using-c-and-storyboards"></a>À l’aide de C# et Storyboards

Lorsque les contrôleurs d’affichage sont ajoutés à l’aire de conception, deux correspondant C# fichiers sont créés dans le projet. Dans cet exemple, `ControlsViewController.cs` et `ControlsViewController.designer.cs` ont été créés automatiquement :

 [![](creating-ui-objects-images/image9b.png "Classe partielle ViewController")](creating-ui-objects-images/image9b.png#lightbox)

Le `MainViewController.cs` fichier est destiné à *votre code*. C’est là le `View` du cycle de vie des méthodes telles que `ViewDidLoad` et `ViewWillAppear` sont implémentées et où vous pouvez ajouter vos propres propriétés, les champs et les méthodes.

Le `ControlsViewController.designer.cs` est le code généré qui contient une classe partielle. Lorsque vous nommez un contrôle sur la conception de surface dans Visual Studio pour Mac ou créez une sortie ou une action dans Xcode, une propriété ou d’une méthode partielle, est ajouté au fichier de concepteur (designer.cs). Le code ci-dessous montre un exemple de code généré pour les deux boutons et un affichage de texte, où un des boutons a également un `TouchUpInside` événement.

Ces éléments de la classe partielle permettent à votre code référencer les contrôles et de répondre aux actions qui sont déclarées sur l’aire de conception :

```csharp
[Register ("ControlsViewController")]
    partial class ControlsViewController
    {
        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button1 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UIButton Button2 { get; set; }

        [Outlet]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        UIKit.UITextField textfield1 { get; set; }

        [Action ("button2_TouchUpInside:")]
        [GeneratedCodeAttribute ("iOS Designer", "1.0")]
        partial void button2_TouchUpInside (UIButton sender);

        void ReleaseDesignerOutlets ()
        {
            if (Button1 != null) {
                Button1.Dispose ();
                Button1 = null;
            }
            if (Button2 != null) {
                Button2.Dispose ();
                Button2 = null;
            }
            if (textfield1 != null) {
                textfield1.Dispose ();
                textfield1 = null;
            }
        }
    }
}
```

Le `designer.cs` fichier ne doit pas être modifié manuellement : l’IDE (Visual Studio pour Mac ou Visual Studio) est chargé de conserver synchronisés avec la table de montage séquentiel.

Lorsque des objets d’interface utilisateur sont ajoutés par programme à un `View` ou `ViewController`, instancier et de gérer les références d’objet vous-même et par conséquent, aucun fichier de concepteur n’est requise.



## <a name="related-links"></a>Liens associés

- [Contrôles (exemple)](https://developer.xamarin.com/samples/monotouch/Controls/)
