---
title: Création d’objets d’interface utilisateur dans Xamarin. iOS
description: Ce document fournit une vue d’ensemble de la création d’une interface utilisateur dans Xamarin. iOS. Il aborde le concepteur iOS, les Interface Builder Xcode, C# et les storyboards.
ms.prod: xamarin
ms.assetid: 4D6B136C-744A-4936-8655-A77E62BA7A60
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: ef54cebebb3881f5bb08aa6edfdc344169f179b4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574091"
---
# <a name="creating-user-interface-objects-in-xamarinios"></a>Création d’objets d’interface utilisateur dans Xamarin. iOS

Apple regroupe des fonctionnalités connexes dans des « frameworks » qui correspondent à des espaces de noms Xamarin. iOS. `UIKit`est l’espace de noms qui contient tous les contrôles d’interface utilisateur pour iOS.

Chaque fois que votre code doit faire référence à un contrôle d’interface utilisateur, tel qu’une étiquette ou un bouton, n’oubliez pas d’inclure l’instruction using suivante :

```csharp
using UIKit;
```

Tous les contrôles présentés dans ce chapitre se trouvent dans l’espace de noms UIKit, et chaque nom de classe de contrôle utilisateur a le `UI` préfixe.

Vous pouvez modifier les mises en page et les contrôles de l’interface utilisateur de trois façons :

- **[Concepteur iOS Xamarin](~/ios/user-interface/designer/index.md)** : utilisez le concepteur de disposition intégré à Xamarin pour concevoir des écrans. Double-cliquez sur fichiers Storyboard ou XIB à modifier avec le concepteur intégré.
- **Xcode Interface Builder** : faites glisser les contrôles sur vos dispositions d’écran avec Interface Builder. Ouvrez le fichier Storyboard ou XIB dans Xcode en cliquant avec le bouton droit sur le fichier dans le **panneau solutions** et en choisissant **ouvrir avec > Interface Builder Xcode**.
- **L’utilisation de C#** – Controls peut également être construite par programmation avec du code et ajoutée à la hiérarchie de la vue.

Vous pouvez ajouter de nouveaux fichiers Storyboard et XIB en cliquant avec le bouton droit sur un projet iOS et en choisissant **ajouter > nouveau fichier...**.

Quelle que soit la méthode utilisée, les propriétés et les événements de contrôle peuvent toujours être manipulés avec C# dans votre logique d’application.

## <a name="using-xamarin-ios-designer"></a>Utilisation du concepteur iOS Xamarin

Pour commencer à créer votre interface utilisateur dans le concepteur iOS, double-cliquez sur un fichier de table de montage séquentiel. Vous pouvez faire glisser les contrôles sur l’aire de conception à partir de la **boîte à outils** , comme illustré ci-dessous :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

 [![](creating-ui-objects-images/image2b.png "Toolbox Pad")](creating-ui-objects-images/image2b.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

 [![](creating-ui-objects-images/image2b-vs.png "Toolbox Pad - Visual Stuio")](creating-ui-objects-images/image2b.png#lightbox)

-----

Lorsqu’un contrôle est sélectionné sur l’aire de conception, le **panneau Propriétés** affiche les attributs de ce contrôle. Le **Widget >** le champ nom de l’identité >, qui est renseigné dans la capture d’écran ci-dessous, est utilisé comme nom de *sortie* . Voici comment vous pouvez référencer le contrôle en C# :

 [![](creating-ui-objects-images/image3b.png "Properties Widget Pad")](creating-ui-objects-images/image3b.png#lightbox)

Pour approfondir votre connaissance de l’utilisation du concepteur iOS, reportez-vous au guide [d’introduction au concepteur iOS](~/ios/user-interface/designer/introduction.md) .

## <a name="using-xcode-interface-builder"></a>Utilisation de Xcode Interface Builder

Si vous n’êtes pas familiarisé avec l’utilisation de Interface Builder, reportez-vous aux documents d’Apple [Interface Builder](https://developer.apple.com/xcode/interface-builder/) .

Pour ouvrir une table de montage séquentiel dans Xcode, cliquez avec le bouton droit pour accéder au menu contextuel du fichier de Storyboard et choisissez de l’ouvrir avec l' **Interface Builder Xcode**:

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

 [![](creating-ui-objects-images/imagexcode.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![](creating-ui-objects-images/imagexcode-vs.png "Storyboard context menu - Xcode")](creating-ui-objects-images/imagexcode-vs.png#lightbox)

-----

Vous pouvez faire glisser les contrôles sur le Aire de conception à partir de la **bibliothèque d’objets** illustrée ci-dessous :

 [![](creating-ui-objects-images/image5a.png "Xcode Object Library")](creating-ui-objects-images/image5a.png#lightbox)

Lorsque vous concevez votre interface utilisateur avec Interface Builder vous devez créer une **sortie** pour chaque contrôle que vous souhaitez référencer en C#. Pour ce faire, vous activez l' **éditeur** de l’Assistant à l’aide du **bouton centrer** de la barre d’outils Xcode :

 [![](creating-ui-objects-images/image6a.png "Assistant Editor button")](creating-ui-objects-images/image6a.png#lightbox)

Cliquez sur un objet d’interface utilisateur. Ensuite, le **contrôle fait glisser** dans le fichier. h. Pour **contrôler le glissement**, maintenez la touche CTRL enfoncée, puis cliquez et maintenez le pointeur sur l’objet d’interface utilisateur pour lequel vous créez la sortie (ou action). Maintenez la touche CTRL enfoncée pendant que vous faites glisser le fichier d’en-tête. Terminez le glissement sous la `@interface` définition. Une ligne bleue doit apparaître avec une légende insérer une sortie ou une collection de sortie, comme illustré dans la capture d’écran ci-dessous.

Quand vous relâchez le clic, vous êtes invité à fournir un nom pour la prise, qui sera utilisé pour créer une propriété C# qui peut être référencée dans le code :

 [![](creating-ui-objects-images/image8a.png "Creating an outlet")](creating-ui-objects-images/image8a.png#lightbox)

Pour plus d’informations sur l’intégration de l’Interface Builder de Xcode à Visual Studio pour Mac, consultez le document de [génération de code XIB](~/ios/internals/xib-code-generation.md#generated) .

## <a name="using-c"></a>Utilisation de C\#

Si vous décidez de créer par programmation un objet d’interface utilisateur à l’aide de C# (dans un contrôleur de vue ou de vue, par exemple), procédez comme suit :

- Déclarez un champ au niveau de la classe pour l’objet d’interface utilisateur. Créez le contrôle lui-même une seule fois, `ViewDidLoad` par exemple. L’objet peut ensuite être référencé dans toutes les méthodes de cycle de vie du contrôleur d’affichage (par exemple,
`ViewWillAppear`).
- Créez un `CGRect` qui définit le frame du contrôle (ses coordonnées X et Y sur l’écran, ainsi que sa largeur et sa hauteur). Vous devez vous assurer que vous disposez d’une `using CoreGraphics` directive pour ce.
- Appelez le constructeur pour créer et assigner le contrôle.
- Définissez des propriétés ou des gestionnaires d’événements.
- Appelez `Add()` pour ajouter le contrôle à la hiérarchie d’affichage.

Voici un exemple simple de création d’un `UILabel` dans un contrôleur d’affichage à l’aide de C# :

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

<a name="partial_classes"></a>

## <a name="using-c-and-storyboards"></a>Utilisation de C# et des storyboards

Lorsque les contrôleurs d’affichage sont ajoutés au Aire de conception, deux fichiers C# correspondants sont créés dans le projet. Dans cet exemple, `ControlsViewController.cs` et `ControlsViewController.designer.cs` ont été créés automatiquement :

 [![](creating-ui-objects-images/image9b.png "ViewController partial class")](creating-ui-objects-images/image9b.png#lightbox)

Le `ControlsViewController.cs` fichier est destiné à *votre code*. C’est là que les `View` méthodes de cycle de vie telles que `ViewDidLoad` et `ViewWillAppear` sont implémentées, et où vous pouvez ajouter vos propres propriétés, champs et méthodes.

Le `ControlsViewController.designer.cs` code généré contient une classe partielle. Lorsque vous nommez un contrôle sur l’aire de conception dans Visual Studio pour Mac, ou créez une sortie ou une action dans Xcode, une propriété correspondante, ou une méthode partielle, est ajoutée au fichier du concepteur (designer.cs). Le code ci-dessous montre un exemple de code généré pour deux boutons et un affichage de texte, où l’un des boutons a également un `TouchUpInside` événement.

Ces éléments de la classe partielle permettent à votre code de référencer les contrôles et de répondre aux actions déclarées sur l’aire de conception :

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

Le `designer.cs` fichier ne doit pas être modifié manuellement : l’IDE (Visual Studio pour Mac ou Visual Studio) est responsable de sa synchronisation avec le Storyboard.

Lorsque des objets d’interface utilisateur sont ajoutés par programmation à un `View` ou à `ViewController` , vous instanciez et gérez les références de l’objet vous-même, et aucun fichier de concepteur n’est donc requis.

## <a name="related-links"></a>Liens connexes

- [Contrôles (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/controls)
