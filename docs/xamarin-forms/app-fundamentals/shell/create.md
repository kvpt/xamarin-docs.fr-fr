---
title: Créer une application Shell Xamarin.Forms
description: Le processus de création d’une application Shell Xamarin.Forms consiste à créer un fichier XAML sous-classant la classe Shell, à définir la propriété MainPage de la classe App de l’application sur l’objet Shell sous-classé, puis à décrire la hiérarchie visuelle de l’application dans la classe Shell sous-classée.
ms.prod: xamarin
ms.assetid: 2A51D78F-6CD5-4BC4-A62E-11CEFA799987
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/24/2019
ms.openlocfilehash: eec20ff6ceb4aee7e8fde59992576899690616c3
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "68739306"
---
# <a name="create-a-xamarinforms-shell-application"></a>Créer une application Shell Xamarin.Forms

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Pour créer une application Shell Xamarin.Forms, procédez comme suit :

1. Créez une application Xamarin.Forms ou charger une application existante que vous souhaitez convertir en application Shell.
1. Ajoutez un fichier XAML au projet de code partagé destiné à sous-classer la classe `Shell`. Pour plus d’informations, consultez [Sous-classer la classe Shell](#subclass-the-shell-class).
1. Définissez [`MainPage`](xref:Xamarin.Forms.Application.MainPage) la propriété de `App` la catégorie de `Shell` l’application à l’objet sous-classé. Pour plus d’informations, consultez [Amorcer l’application Shell](#bootstrap-the-shell-application).
1. Décrivez la hiérarchie visuelle de l’application dans la classe `Shell` sous-classée. Pour plus d’informations, consultez [Décrire la hiérarchie visuelle de l’application](#describe-the-visual-hierarchy-of-the-application).

## <a name="subclass-the-shell-class"></a>Sous-classer la classe Shell

La première étape de la création d’une application Xamarin.Forms Shell consiste à ajouter un fichier XAML au projet de code partagé qui sous-classe la classe `Shell`. Pour ce fichier, vous pouvez entrer le nom de votre choix, mais nous recommandons d’utiliser **AppShell**. L’exemple de code suivant montre un fichier **AppShell.xaml** qui vient d’être créé :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       x:Class="Xaminals.AppShell">

</Shell>
```

L’exemple suivant montre le fichier code-behind **AppShell.xaml.cs** :

```csharp
using Xamarin.Forms;

namespace Xaminals
{
    public partial class AppShell : Shell
    {
        public AppShell()
        {
            InitializeComponent();
        }
    }
}
```

## <a name="bootstrap-the-shell-application"></a>Amorcer une application Shell

Après la création du fichier XAML qui sous-classe `Shell` l’objet, la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriété de la `App` classe doit être réglée à l’objet sous-classé `Shell` :

```csharp
namespace Xaminals
{
    public partial class App : Application
    {
        public App()
        {
            InitializeComponent();
            MainPage = new AppShell();
        }
        ...
    }
}
```

Dans cet exemple, la classe `AppShell` représente le fichier XAML qui dérive de la classe `Shell`.

> [!NOTE]
> Si vous l’exécutez pendant qu’une application Shell vide est générée, une exception `InvalidOperationException` est levée.

## <a name="describe-the-visual-hierarchy-of-the-application"></a>Décrire la hiérarchie visuelle de l’application

La dernière étape de création d’une application Shell Xamarin.Forms consiste à décrire la hiérarchie visuelle de l’application dans la classe `Shell` sous-classée. Une classe `Shell` sous-classée comprend trois objets hiérarchiques principaux :

- `FlyoutItem` ou `TabBar`. Un `FlyoutItem` représente au moins un élément dans le menu volant et doit être utilisé lorsque le modèle de navigation de l’application comprend un menu volant. Un `TabBar` représente la barre d’onglet en bas et doit être utilisé lorsque le modèle de navigation de l’application commence par des onglets en bas. Chaque objet `FlyoutItem` ou `TabBar` est un enfant de l’objet `Shell`.
- `Tab`, qui représente le contenu regroupé, accessible via les onglets inférieurs. Chaque objet `Tab` est un enfant d’un objet `FlyoutItem` ou `TabBar`.
- `ShellContent`, qui représente les objets `ContentPage` dans votre application. Chaque objet `ShellContent` est un enfant d’un objet `Tab`. Lorsque plusieurs objets `ShellContent` sont présents dans un `Tab`, les objets sont accessibles via les onglets supérieurs.

Aucun de ces objets ne représente une interface utilisateur, mais plutôt l’organisation de la hiérarchie visuelle de l’application. Shell sélectionnera ces objets et générera l’interface utilisateur de navigation pour le contenu.

Le fichier XAML suivant montre un exemple d’une classe `Shell` sous-classée :

```xaml
<Shell xmlns="http://xamarin.com/schemas/2014/forms"
       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
       xmlns:views="clr-namespace:Xaminals.Views"
       x:Class="Xaminals.AppShell">
    ...
    <FlyoutItem Title="Animals"
                FlyoutDisplayOptions="AsMultipleItems">
        <Tab Title="Domestic"
             Icon="paw.png">
            <ShellContent Title="Cats"
                          Icon="cat.png">
                <views:CatsPage />
            </ShellContent>
            <ShellContent Title="Dogs"
                          Icon="dog.png">
                <views:DogsPage />
            </ShellContent>
        </Tab>
        <ShellContent Title="Monkeys"
                      Icon="monkey.png">
            <views:MonkeysPage />
        </ShellContent>
        <ShellContent Title="Elephants"
                      Icon="elephant.png">  
            <views:ElephantsPage />
        </ShellContent>
        <ShellContent Title="Bears"
                      Icon="bear.png">
            <views:BearsPage />
        </ShellContent>
    </FlyoutItem>
    ...
</Shell>
```

Lorsqu’il est exécuté, ce fichier XAML affiche la `CatsPage` car il s’agit du premier élément de contenu déclaré dans la classe `Shell` sous-classée :

[![Capture d’écran d’une application Shell, sur iOS et Android](create-images/cats.png "Application Shell")](create-images/cats-large.png#lightbox "Application Shell")

Appuyez sur l’icône du menu latéral, ou balayez depuis la gauche, pour afficher le menu volant :

[![Capture d’écran d’un menu volant Shell, sur iOS et Android](create-images/flyout-reduced.png "Menu volant Shell")](create-images/flyout-reduced-large.png#lightbox "Menu volant Shell")

> [!IMPORTANT]
> Dans une application [`ContentPage`](xref:Xamarin.Forms.ContentPage) Shell, chacun d’eux est un enfant d’un `ShellContent` objet est créé lors du démarrage de l’application. L’ajout d’autres objets `ShellContent` avec cette approche crée des pages supplémentaires au démarrage de l’application, ce qui peut nuire à l’expérience de démarrage. Mais Shell est également capable de créer des pages à la demande, en réponse à la navigation. Pour plus d’informations, consultez [Chargement efficace d’une page](tabs.md#efficient-page-loading) dans le guide [Onglets Shell Xamarin.Forms](tabs.md).

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
