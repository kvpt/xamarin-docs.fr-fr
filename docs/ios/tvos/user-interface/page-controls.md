---
title: Utilisation des contrôles de page tvOS dans Xamarin
description: Ce document explique comment utiliser les contrôles de page tvOS dans une application générée avec Xamarin. Il fournit une description de haut niveau des contrôles de page, explique comment les configurer dans les storyboards et examine comment répondre aux événements de changement de page.
ms.prod: xamarin
ms.assetid: 19198D46-7BBE-4D04-9BFA-7D1C5C9F9FC6
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: ec4b99fd1c8ec28f6b4bbb30695613da6c40d60c
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70288951"
---
# <a name="working-with-tvos-page-controls-in-xamarin"></a>Utilisation des contrôles de page tvOS dans Xamarin

Parfois, vous devrez peut-être afficher une série de pages ou d’images dans votre application Xamarin. tvOS. Un contrôle de page a été conçu pour montrer clairement la page dont l’utilisateur se trouve en dehors du nombre maximal de pages. Un contrôle de page affiche une série de points par rapport à un arrière-plan de forme ovale sombre. La page active affiche un point rempli, toutes les autres pages s’affichent sous forme de points creux. Le contrôle de page découpera les points les plus extérieurs s’ils sont trop nombreux pour s’ajuster à la zone d’arrière-plan.

[![](page-controls-images/page01.png "Exemple de contrôle de page")](page-controls-images/page01.png#lightbox)

Contrôle de page dans un élément non interactif conçu pour fournir des commentaires à l’utilisateur uniquement. Vous devrez ajouter d’autres contrôles pour modifier le numéro de page actuel (tels que les gestes ou les boutons).

Apple présente les suggestions suivantes lors de l’utilisation d’un contrôle de page :

- **Utilisation sur les collections entières uniquement** : les contrôles de page fonctionnent mieux dans un environnement plein écran pour afficher plusieurs pages qui existent dans une collection unique.
- **Limitez le nombre de contrôles de page de pages** au mieux pour dix (10) ou moins de pages, et un maximum de vingt (20) pages. Pour plus de vingt pages, envisagez d’utiliser une [vue de collection](~/ios/tvos/user-interface/collection-views.md) et d’afficher les pages dans une grille.

<a name="Page-Controls-and-Storyboards" />

## <a name="page-controls-and-storyboards"></a>Contrôles de page et storyboards

Le moyen le plus simple d’utiliser les contrôles de page dans une application Xamarin. tvOS consiste à les ajouter à l’interface utilisateur de l’application à l’aide du concepteur iOS.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)


1. Dans la **panneau solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser un **contrôle page** de la **boîte à outils** et déposez-le sur la vue :

    [![](page-controls-images/page02.png "Contrôle de page")](page-controls-images/page02.png#lightbox)
1. Dans l' **onglet widget** du **panneau Propriétés**, vous pouvez ajuster plusieurs propriétés du contrôle de page, telles que sa **page actuelle** et le **nombre de pages**:

    [![](page-controls-images/page03.png "Onglet widget")](page-controls-images/page03.png#lightbox)
1. Ensuite, ajoutez des contrôles ou des gestes à la vue pour vous déplacer vers l’avant et vers l’arrière dans la collection de pages.
1. Enfin, assignez des **noms** aux contrôles pour pouvoir y répondre dans C# le code. Par exemple :

    [![](page-controls-images/page04.png "Nommer le contrôle")](page-controls-images/page04.png#lightbox)
1. Enregistrez les modifications apportées.


# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)


1. Dans la **Explorateur de solutions**, double-cliquez sur `Main.storyboard` le fichier et ouvrez-le pour le modifier.
1. Faites glisser un **contrôle page** de la **boîte à outils** et déposez-le sur la vue :

    [![](page-controls-images/page02-vs.png "Contrôle de page")](page-controls-images/page02-vs.png#lightbox)
1. Dans l' **onglet widget** de l' **Explorateur de propriétés**, vous pouvez ajuster plusieurs propriétés du contrôle de page, telles que sa **page actuelle** et le **nombre de pages**:

    [![](page-controls-images/page03-vs.png "Onglet widget")](page-controls-images/page03-vs.png#lightbox)
1. Ensuite, ajoutez des contrôles ou des gestes à la vue pour vous déplacer vers l’avant et vers l’arrière dans la collection de pages.
1. Enfin, assignez des **noms** aux contrôles pour pouvoir y répondre dans C# le code. Par exemple :

    [![](page-controls-images/page04-vs.png "Nommer le contrôle")](page-controls-images/page04-vs.png#lightbox)
1. Enregistrez les modifications apportées.


-----

> [!IMPORTANT]
> Bien qu’il soit possible d’assigner `TouchUpInside` des événements tels que à un élément d’interface utilisateur (par exemple, un UIButton) dans le concepteur iOS, il ne sera jamais appelé, car Apple TV n’a pas d’écran tactile ou de prise en charge des événements tactiles. Vous devez toujours utiliser l' `Primary Action` événement lors de la création de gestionnaires d’événements pour les éléments d’interface utilisateur tvOS.

Modifiez le fichier de votre contrôleur `ViewController.cs`d’affichage (exemple) et ajoutez le code pour gérer les pages en cours de modification. Par exemple :

```csharp
using System;
using Foundation;
using UIKit;

namespace MySingleView
{
    public partial class ViewController : UIViewController
    {
        #region Computed Properties
        public nint PageNumber { get; set; } = 0;
        #endregion

        #region Constructors
        public ViewController (IntPtr handle) : base (handle)
        {
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Initialize
            PageView.Pages = 6;
            ShowCat ();
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Custom Actions
        partial void NextCat (UIBarButtonItem sender) {

            // Display next Cat
            if (++PageNumber > 5) {
                PageNumber = 5;
            }
            ShowCat();

        }

        partial void PreviousCat (UIBarButtonItem sender) {
            // Display previous cat
            if (--PageNumber < 0) {
                PageNumber = 0;
            }
            ShowCat();
        }
        #endregion

        #region Private Methods
        private void ShowCat() {

            // Adjust UI
            PreviousButton.Enabled = (PageNumber > 0);
            NextButton.Enabled = (PageNumber < 5);
            PageView.CurrentPage = PageNumber;

            // Display new cat
            CatView.Image = UIImage.FromFile(string.Format("Cat{0:00}.jpg",PageNumber+1));
        }
        #endregion
    }
}
```

Examinons de plus près deux propriétés du contrôle page. Tout d’abord, pour spécifier le nombre maximal de pages, utilisez ce qui suit :

```csharp
PageView.Pages = 6;
```

Pour modifier le numéro de page actuel, utilisez le code suivant :

```csharp
PageView.CurrentPage = PageNumber;
```

La `CurrentPage` propriété a une valeur de zéro (0). par conséquent, la première page est égale à zéro et la dernière à un moins le nombre maximal de pages.

Pour plus d’informations sur l’utilisation des storyboards, consultez notre [Guide de démarrage rapide Hello, tvOS](~/ios/tvos/get-started/hello-tvos.md).

<a name="Summary" />

## <a name="summary"></a>Récapitulatif

Cet article a abordé la conception et l’utilisation du contrôle de page dans une application Xamarin. tvOS.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guides de l’interface utilisateur tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guide de programmation d’applications pour tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
