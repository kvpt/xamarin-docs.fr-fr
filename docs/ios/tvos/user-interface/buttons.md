---
title: Utilisation des boutons tvOS dans Xamarin
description: Ce document décrit comment utiliser les boutons dans une application tvOS créée avec Xamarin. Il explique comment utiliser des boutons dans le code et dans les storyboards, et il examine comment styliser un bouton.
ms.prod: xamarin
ms.assetid: DA6EF400-A4E3-4245-A0D4-F2398CAE2C9B
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/07/2017
ms.openlocfilehash: aa1dd0b503dd59a89f526fef53d7d7756a3883de
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291107"
---
# <a name="working-with-tvos-buttons-in-xamarin"></a>Utilisation des boutons tvOS dans Xamarin

Utilisez une instance de la `UIButton` classe pour créer un bouton pouvant être activé et sélectionnable dans une fenêtre tvOS. Quand l’utilisateur sélectionne un bouton, il envoie un message d’action à l’objet cible, ce qui permet à votre application Xamarin. tvOS de répondre à l’entrée de l’utilisateur.

[![](buttons-images/buttons01.png "Exemples de boutons")](buttons-images/buttons01.png#lightbox)

Pour plus d’informations sur l’utilisation du focus et la navigation avec la Siri distante, consultez la documentation relative à la [navigation et au focus](~/ios/tvos/app-fundamentals/navigation-focus.md) et aux [Contrôleurs distants et des contrôleurs Bluetooth Siri](~/ios/tvos/platform/remote-bluetooth.md) .

<a name="About-Buttons" />

## <a name="about-buttons"></a>À propos des boutons

Dans tvOS, les boutons sont utilisés pour les actions spécifiques à l’application et peuvent contenir un titre, une icône ou les deux. Lorsque l’utilisateur navigue dans l’interface utilisateur de l’application à l’aide de la [Siri distante](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), le focus se déplace sur le bouton donné, ce qui permet de modifier le texte et les couleurs d’arrière-plan. Une ombre est également appliquée au bouton qui ajoute un effet 3D, ce qui a pour effet d’augmenter au-dessus du reste de l’interface utilisateur.

[![](buttons-images/buttons01.png "Exemples de boutons")](buttons-images/buttons01.png#lightbox)

Apple propose les suggestions suivantes pour l’utilisation des boutons :

- **Utilisez un titre ou une icône** , alors qu’une icône et un titre peuvent être inclus dans un bouton. l’espace est limité. n’essayez pas de combiner les deux.
- **Marquer clairement les boutons destructifs** : si le bouton exécute une action destructrice (telle que la suppression d’un fichier), marquez-le clairement comme tel à l’aide du texte et/ou de l’icône. Les actions destructrices doivent toujours présenter une [alerte](~/ios/tvos/user-interface/alerts.md) demandant à l’utilisateur de limiter l’action.
- **N’utilisez pas de boutons précédent** : le bouton de menu de la Siri distante est utilisé pour revenir à l’écran précédent. La seule exception à cette règle concerne les achats dans l’application ou les actions destructrices où un bouton **Annuler** doit être affiché.

Pour plus d’informations sur l’utilisation du focus et de la navigation, consultez notre page [utilisation de la navigation et](~/ios/tvos/app-fundamentals/navigation-focus.md) de la documentation.

<a name="Button-Icons" />

### <a name="button-icons"></a>Icônes de bouton

Apple suggère que vous utilisiez des images simples et facilement reconnaissables pour vos icônes de bouton. Les icônes trop complexes sont difficiles à reconnaître sur un écran de télévision à travers la salle d’une canapé, donc essayez d’utiliser la représentation la plus simple possible pour y parvenir. Dans la mesure du possible, utilisez des images standard et connues pour les icônes (par exemple, une loupe pour la recherche).

<a name="Button-Titles" />

### <a name="button-titles"></a>Titres de bouton

Apple présente les suggestions suivantes lors de la création des titres pour vos boutons :

- **Afficher le texte descriptif sous les boutons d’icônes** : dans la mesure du possible, placez le texte descriptif en dessous de l’icône pour obtenir plus d’informations sur l’objectif du bouton.
- **Utilisez des verbes ou des expressions de verbe pour le titre** : indiquez clairement l’action qui se produit lorsque l’utilisateur clique sur le bouton.
- Utilisez la mise en **majuscules du style titre** -à l’exception des articles, des conjonctions ou des prépositions (quatre lettres au maximum), chaque mot du titre du bouton doit être en majuscules.
- **Utilisez un petit titre à POINTER** -utilisez le type de description le plus bref possible pour décrire l’action du bouton.

<a name="Buttons-and-Storyboards" />

## <a name="buttons-and-storyboards"></a>Boutons et storyboards

Le moyen le plus simple d’utiliser des boutons dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide de l’Xamarin Designer pour iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser un **bouton** à partir de la **bibliothèque** et déposez-le sur la vue : 

    [![](buttons-images/storyboard01.png "Un bouton")](buttons-images/storyboard01.png#lightbox)
1. Dans l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés du bouton, telles que le **titre** et la **couleur du texte**: 

    [![](buttons-images/storyboard02.png "Propriétés du bouton")](buttons-images/storyboard02.png#lightbox)
1. Ensuite, basculez vers l' **onglet événements** et connectez un **événement** à partir du **bouton** et appelez- `ButtonPressed`le : 

    [![](buttons-images/storyboard03.png "Onglet événements")](buttons-images/storyboard03.png#lightbox)
1. Vous serez automatiquement dirigé vers la `ViewController.cs` vue dans laquelle vous pouvez placer la nouvelle action dans votre code à l’aide des touches de direction **haut** et **bas** : 

    [![](buttons-images/storyboard04.png "Placement d’une nouvelle action dans le code")](buttons-images/storyboard04.png#lightbox)
1. Appuyez sur la touche **entrée** pour sélectionner l’emplacement : 

    [![](buttons-images/storyboard05.png "Éditeur de code")](buttons-images/storyboard05.png#lightbox)
1. Enregistrez les modifications apportées à tous les fichiers.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser un **bouton** à partir de la **bibliothèque** et déposez-le sur la vue : 

    [![](buttons-images/storyboard01vs.png "Un bouton")](buttons-images/storyboard01vs.png#lightbox)
1. Dans l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés du bouton, telles que le **titre** et la **couleur du texte**: 

    [![](buttons-images/storyboard02vs.png "Explorateur de propriétés")](buttons-images/storyboard02vs.png#lightbox)
1. Ensuite, basculez vers l' **onglet événements** et connectez un **événement** à partir du **bouton** et appelez- `ButtonPressed`le : 

    [![](buttons-images/storyboard03vs.png "Onglet événements")](buttons-images/storyboard03vs.png#lightbox)
1. Enregistrez les modifications apportées à tous les fichiers.



Modifiez le fichier de votre contrôleur `ViewController.cs`d’affichage (exemple) et ajoutez le code suivant pour gérer le bouton sélectionné :


```

using System;
using UIKit;

namespace tvRemote
{
    public partial class ViewController : UIViewController
    {
        ...

        partial void ButtonPressed (UIButton sender) {
            // Handle click here
            ...
        }
    }
}

```

-----

Tant que la propriété d' `Enabled` un bouton a `true` la valeur et qu’elle n’est pas couverte par un autre contrôle ou vue, elle peut être rendue en élément actif à l’aide de la Siri distante. Si l’utilisateur sélectionne le bouton et clique sur la surface tactile `ButtonPressed` , l’action définie ci-dessus est exécutée.

> [!IMPORTANT]
> Bien qu’il soit possible d’assigner `TouchUpInside` `UIButton` des actions telles que à dans le concepteur iOS lors de la création d’un **Gestionnaire d’événements**, il ne sera jamais appelé, car Apple TV n’a pas d’écran tactile ou de prise en charge des événements tactiles. Vous devez toujours utiliser le **type d’action** par défaut lors de la création d' **actions** pour les éléments de l’interface utilisateur tvOS.




Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Buttons-and-Code" />

## <a name="buttons-and-code"></a>Boutons et code

Éventuellement, un `UIButton` peut être créé dans C# le code et ajouté à la vue de l’application tvOS. Par exemple :

```csharp
var button = new UIButton(UIButtonType.System);
button.Frame = new CGRect (25, 25, 300, 150);
button.SetTitle ("Hello", UIControlState.Normal);
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
View.AddSubview (button);
```

Lorsque vous créez un nouveau `UIButton` dans le code, vous spécifiez son `UIButtonType` comme l’un des éléments suivants :

- **Système** : il s’agit du type de bouton standard présenté par tvOS. il s’agit du type que vous allez utiliser le plus souvent.
- **DetailDisclosure** : présente un type de bouton « arrêter » utilisé pour masquer ou afficher des informations détaillées.
- **InfoDark** : un bouton info détaillé sombre affiche un « i » dans un cercle.
- **InfoLight** : un bouton info détaillé clair affiche un « i » dans un cercle.
- **AddContact** : affiche le bouton en tant que bouton Ajouter un contact.
- **Personnalisé** : vous permet de personnaliser plusieurs traits du bouton.

Ensuite, vous définissez la taille et l’emplacement à l’écran du bouton. Exemple :

```csharp
button.Frame = new CGRect (25, 25, 300, 150);
```

Ensuite, définissez le titre du bouton. `UIButtons`sont différents de la `UIKit` plupart des contrôles dans la mesure où ils ont un État pour que vous ne puissiez pas simplement modifier le titre, vous devez `UIControlState`le modifier pour un donné. Par exemple :

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Ensuite, utilisez l' `AllEvents` événement pour voir à quel moment l’utilisateur a cliqué sur le bouton. Exemple :

```csharp
button.AllEvents += (sender, e) => {
    // Do something when the button is clicked
    ...
};
```

Enfin, vous ajoutez le bouton à la vue pour l’afficher :

```csharp
View.AddSubview (button);
```

> [!IMPORTANT]
> Bien qu’il soit possible d’assigner `TouchUpInside` des actions `UIButton`telles que à un, il ne sera jamais appelé, car Apple TV ne dispose pas d’un écran tactile ou de la prise en charge des événements tactiles. Vous devez toujours utiliser des événements tels que **AllEvents** ou **PrimaryActionTriggered**.




<a name="Styling-a-Button" />

## <a name="styling-a-button"></a>Stylisation d’un bouton

tvOS fournit plusieurs propriétés d’un `UIButton` qui peuvent être utilisées pour fournir son titre et lui appliquer un style avec des éléments tels que la couleur d’arrière-plan et les images.

<a name="Button-Titles" />

### <a name="button-titles"></a>Titres de bouton

Comme nous l’avons vu `UIButtons` plus haut, ils `UIKit` sont différents de la plupart des contrôles dans la mesure où ils ont un État pour que vous ne puissiez pas simplement modifier le `UIControlState`titre, vous devez le modifier pour un donné. Par exemple :

```csharp
button.SetTitle ("Hello", UIControlState.Normal);
```

Vous pouvez définir la couleur du titre du bouton à l' `SetTitleColor` aide de la méthode. Par exemple :

```csharp
button.SetTitleColor (UIColor.White, UIControlState.Normal);
```

Vous pouvez ajuster l’ombre du titre à l’aide `SetTitleShadowColor`de la. Par exemple :

```csharp
button.SetTitleShadowColor(UIColor.Black, UIControlState.Normal);
```

Vous pouvez définir l’ombre du titre de sorte qu’elle *passe de l'* un à l’être en *relief* quand le bouton est mis en surbrillance à l’aide du code suivant :

```csharp
button.ReverseTitleShadowWhenHighlighted = true;
```

En outre, vous pouvez utiliser le texte avec attributs comme titre du bouton. Par exemple :

```csharp
var normalAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Blue, strikethroughStyle: NSUnderlineStyle.Single);
myButton.SetAttributedTitle (normalAttributedTitle, UIControlState.Normal);

var highlightedAttributedTitle = new NSAttributedString (buttonTitle, foregroundColor: UIColor.Green, strikethroughStyle: NSUnderlineStyle.Thick);
myButton.SetAttributedTitle (highlightedAttributedTitle, UIControlState.Highlighted);
```

### <a name="button-images"></a>Images de bouton

Une `UIButton` image peut être attachée à une image et peut utiliser une image comme arrière-plan.

Pour définir l’image d’arrière-plan d’un bouton `UIControlState`pour un donné, utilisez le code suivant :

```csharp
button.SetBackgroundImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Affectez `AdjustsImageWhenHiglighted` à la `true` propriété la valeur pour dessiner l’image plus claire lorsque le bouton est mis en surbrillance (il s’agit de la valeur par défaut). Affectez `AdjustsImageWhenDisabled` à la `true` propriété la valeur pour dessiner l’image plus sombre lorsque le bouton est désactivé (là encore, il s’agit de la valeur par défaut).

Pour définir l’image affichée sur le bouton, utilisez le code suivant :

```csharp
button.SetImage(UIImage.FromFile("my image.png"), UIControlState.Normal);
```

Utilisez la `TintColor` propriété pour définir une teinte de couleur appliquée au titre et à l’image du bouton. Pour les `Custom` boutons du type, cette propriété n’a aucun effet, vous devez implémenter le `TintColor` comportement vous-même.

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation de boutons à l’intérieur d’une application Xamarin. tvOS. Il a montré comment utiliser les boutons dans le concepteur iOS et comment créer des boutons dans C# le code. Enfin, il a montré comment modifier le titre d’un bouton et modifier son style et son apparence.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
