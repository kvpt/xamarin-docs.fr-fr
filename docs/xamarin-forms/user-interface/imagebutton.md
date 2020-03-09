---
title: Xamarin.Forms ImageButton
description: ImageButton affiche une image et répond à un cliquez ou appuyez sur dirigeant d’une application à exécuter une tâche particulière.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
ms.openlocfilehash: 7c6647a0299b5ece3caaaa1d322ec1a0efac3557
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78912205"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Le ImageButton affiche une image et répond à un TAP ou un clic qui indique à une application d’effectuer une tâche particulière._

La vue `ImageButton` combine la vue de [`Button`](xref:Xamarin.Forms.Button) et [`Image`](xref:Xamarin.Forms.Image) vue pour créer un bouton dont le contenu est une image. L’utilisateur appuie sur le `ImageButton` avec un doigt ou clique dessus avec la souris pour indiquer à l’application d’effectuer une tâche particulière. Toutefois, contrairement à la vue `Button`, la vue de `ImageButton` n’a aucun concept de texte et d’apparence de texte.

> [!NOTE]
> Tandis que la vue [`Button`](xref:Xamarin.Forms.Button) définit une propriété [`Image`](xref:Xamarin.Forms.Button.Image) , qui vous permet d’afficher une image sur le `Button`, cette propriété est destinée à être utilisée lors de l’affichage d’une petite icône à côté du texte de l' `Button`.

Les exemples de code de ce guide sont tirés de l' [exemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Définition de la source d’image

`ImageButton` définit une propriété `Source` qui doit être définie sur l’image à afficher dans le bouton, la source de l’image étant un fichier, un URI, une ressource ou un flux. Pour plus d’informations sur le chargement d’images à partir de différentes sources, consultez [images dans Xamarin. Forms](images.md).

L’exemple suivant montre comment instancier un `ImageButton` en XAML :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

La propriété `Source` spécifie l’image qui apparaît dans le `ImageButton`. Dans cet exemple, il est défini dans un fichier local qui sera chargé à partir de chaque projet de plateforme, ce qui entraîne les captures d’écran suivante :

[![ImageButton de base](imagebutton-images/BasicImageButton.png "ImageButton de base")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton de base")

Par défaut, le `ImageButton` est rectangulaire, mais vous pouvez lui attribuer des angles arrondis à l’aide de la propriété `CornerRadius`. Pour plus d’informations sur l’apparence des `ImageButton`, consultez [apparence de ImageButton](#imagebutton-appearance).

> [!NOTE]
> Bien qu’un `ImageButton` puisse charger une image GIF animée, il n’affiche que la première image de l’image GIF.

L’exemple suivant montre comment créer une page qui est fonctionnellement équivalente à l’exemple XAML précédent, mais entièrement en C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children = { header, imageButton }
        };
    }
}
```

## <a name="handling-imagebutton-clicks"></a>Gestion des ImageButton clique sur

`ImageButton` définit un événement `Clicked` qui est déclenché quand l’utilisateur appuie sur le `ImageButton` avec un pointeur Finger ou Mouse. L’événement est déclenché lorsque le bouton de doigt ou de la souris est relâché à partir de la surface du `ImageButton`. La propriété `IsEnabled` de la `ImageButton` doit être définie sur `true` pour répondre aux pressions.

L’exemple suivant montre comment instancier un `ImageButton` en XAML et gérer son événement `Clicked` :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="FormsGallery.XamlExamples.ImageButtonDemoPage"
             Title="ImageButton Demo">
    <StackLayout>
        <Label Text="ImageButton"
               FontSize="50"
               FontAttributes="Bold"
               HorizontalOptions="Center" />

       <ImageButton Source="XamarinLogo.png"
                    HorizontalOptions="Center"
                    VerticalOptions="CenterAndExpand"
                    Clicked="OnImageButtonClicked" />

        <Label x:Name="label"
               Text="0 ImageButton clicks"
               FontSize="Large"
               HorizontalOptions="Center"
               VerticalOptions="CenterAndExpand" />
    </StackLayout>
</ContentPage>
```

L’événement `Clicked` est défini sur un gestionnaire d’événements nommé `OnImageButtonClicked` qui se trouve dans le fichier code-behind :

```csharp
public partial class ImageButtonDemoPage : ContentPage
{
    int clickTotal;

    public ImageButtonDemoPage()
    {
        InitializeComponent();
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

Lorsque le `ImageButton` est frappé, la méthode `OnImageButtonClicked` s’exécute. L’argument `sender` est le `ImageButton` responsable de cet événement. Vous pouvez l’utiliser pour accéder à l’objet `ImageButton`, ou pour faire la distinction entre plusieurs objets `ImageButton` partageant le même `Clicked` événement.

Ce gestionnaire de `Clicked` particulier incrémente un compteur et affiche la valeur du compteur dans un [`Label`](xref:Xamarin.Forms.Label):

[![Clic ImageButton de base](imagebutton-images/ImageButton.png "Clic ImageButton de base")](imagebutton-images/ImageButton-Large.png#lightbox "Clic ImageButton de base")

L’exemple suivant montre comment créer une page qui est fonctionnellement équivalente à l’exemple XAML précédent, mais entièrement en C#:

```csharp
public class ImageButtonDemoPage : ContentPage
{
    Label label;
    int clickTotal = 0;

    public ImageButtonDemoPage()
    {
        Label header = new Label
        {
            Text = "ImageButton",
            FontSize = 50,
            FontAttributes = FontAttributes.Bold,
            HorizontalOptions = LayoutOptions.Center
        };

        ImageButton imageButton = new ImageButton
        {
            Source = "XamarinLogo.png",
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };
        imageButton.Clicked += OnImageButtonClicked;

        label = new Label
        {
            Text = "0 ImageButton clicks",
            FontSize = Device.GetNamedSize(NamedSize.Large, typeof(Label)),
            HorizontalOptions = LayoutOptions.Center,
            VerticalOptions = LayoutOptions.CenterAndExpand
        };

        // Build the page.
        Title = "ImageButton Demo";
        Content = new StackLayout
        {
            Children =
            {
                header,
                imageButton,
                label
            }
        };
    }

    void OnImageButtonClicked(object sender, EventArgs e)
    {
        clickTotal += 1;
        label.Text = $"{clickTotal} ImageButton click{(clickTotal == 1 ? "" : "s")}";
    }
}
```

## <a name="disabling-the-imagebutton"></a>La désactivation ImageButton

Parfois, une application se trouve dans un état particulier où un clic de `ImageButton` spécifique n’est pas une opération valide. Dans ce cas, le `ImageButton` doit être désactivé en affectant à sa propriété `IsEnabled` la valeur `false`.

## <a name="using-the-command-interface"></a>À l’aide de l’interface de commande

Une application peut répondre à `ImageButton` taraudages sans gérer l’événement `Clicked`. Le `ImageButton` implémente un autre mécanisme de notification appelé _commande_ ou interface de _commande_ . Cela se compose de deux propriétés :

- `Command` de type [`ICommand`](xref:System.Windows.Input.ICommand), une interface définie dans l’espace de noms [`System.Windows.Input`](xref:System.Windows.Input) .
- `CommandParameter` propriété de type [`Object`](xref:System.Object).

Cette approche convient dans le cadre de liaison de données et en particulier lors de l’implémentation de l’architecture Model-View-ViewModel (MVVM).

Pour plus d’informations sur l’utilisation de l’interface de commande, consultez [utilisation de l’interface de commande](button.md#using-the-command-interface) dans le Guide du [bouton](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Appuyer et de relâcher ImageButton

Outre l’événement `Clicked`, `ImageButton` définit également les événements `Pressed` et `Released`. L’événement `Pressed` se produit lorsqu’un doigt appuie sur un `ImageButton`, ou lorsqu’un bouton de la souris est enfoncé avec le pointeur positionné sur le `ImageButton`. L’événement `Released` se produit lorsque le bouton de doigt ou de la souris est relâché. En règle générale, l’événement `Clicked` est également déclenché en même temps que l’événement `Released`, mais si le pointeur de la souris ou du doigt s’éloigne de la surface de l' `ImageButton` avant d’être relâché, l’événement `Clicked` peut ne pas se produire.

Pour plus d’informations sur ces événements, consultez [pression et relâchement du bouton](button.md#pressing-and-releasing-the-button) dans le [bouton du bouton](button.md) .

## <a name="imagebutton-appearance"></a>Apparence de ImageButton

En plus des propriétés que `ImageButton` hérite de la classe [`View`](xref:Xamarin.Forms.View) , `ImageButton` définit également plusieurs propriétés qui affectent son apparence :

- `Aspect` est la manière dont l’image est mise à l’échelle pour s’ajuster à la zone d’affichage.
- `BorderColor` est la couleur d’une zone entourant le `ImageButton`.
- `BorderWidth` est la largeur de la bordure.
- `CornerRadius` est le rayon de l’angle du `ImageButton`.

La propriété `Aspect` peut être définie sur l’un des membres de l’énumération [`Aspect`](xref:Xamarin.Forms.Aspect) :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -étire l’image pour qu’elle remplisse complètement et exactement le `ImageButton`. Cela peut entraîner l’image est déformée.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) : découpe l’image pour qu’elle remplisse le `ImageButton` tout en préservant les proportions.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) : cadres l’image (si nécessaire) pour que l’image entière s’ajuste à la `ImageButton`, avec un espace vide en haut/bas ou sur les côtés selon que l’image est grande ou haute. Il s’agit de la valeur par défaut de l’énumération [`Aspect`](xref:Xamarin.Forms.Aspect) .

> [!NOTE]
> La classe `ImageButton` possède également des propriétés [`Margin`](xref:Xamarin.Forms.View.Margin) et `Padding` qui contrôlent le comportement de disposition de l' `ImageButton`. Pour plus d’informations, consultez la page [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>États visuels ImageButton

`ImageButton` a un [`VisualState`](xref:Xamarin.Forms.VisualState) `Pressed` qui peut être utilisé pour initier une modification visuelle de l' `ImageButton` lorsqu’il est appuyé par l’utilisateur, à condition qu’il soit activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour l’État `Pressed` :

```xaml
<ImageButton Source="XamarinLogo.png"
             ...>
    <VisualStateManager.VisualStateGroups>
        <VisualStateGroup x:Name="CommonStates">
            <VisualState x:Name="Normal">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="1" />
                </VisualState.Setters>
            </VisualState>

            <VisualState x:Name="Pressed">
                <VisualState.Setters>
                    <Setter Property="Scale"
                            Value="0.8" />
                </VisualState.Setters>
            </VisualState>

        </VisualStateGroup>
    </VisualStateManager.VisualStateGroups>
</ImageButton>
```

L' `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) spécifie que lorsque l' `ImageButton` est enfoncé, sa valeur par défaut de la propriété [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) est remplacée par 0,8. L' `Normal` `VisualState` spécifie que lorsque le `ImageButton` est dans un état normal, sa propriété `Scale` est définie sur 1. Par conséquent, l’effet global est que lorsque le `ImageButton` est enfoncé, il est mis à l’échelle pour être légèrement plus petit, et lorsque la `ImageButton` est libérée, elle est redimensionnée à sa taille par défaut.

Pour plus d’informations sur les États visuels, consultez [le gestionnaire d’état visuel Xamarin. Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Exemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
