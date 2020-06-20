---
title: Xamarin.FormsImageButton
description: Le ImageButton affiche une image et répond à un TAP ou un clic qui indique à une application d’effectuer une tâche particulière.
ms.prod: xamarin
ms.assetid: B5906AB6-3F79-4FCB-8C78-1F0AF18AB39E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/04/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7d81c0ce4dc2a46a840a34cc9084c8f2388a0169
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137642"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.FormsImageButton

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_Le ImageButton affiche une image et répond à un TAP ou un clic qui indique à une application d’effectuer une tâche particulière._

La `ImageButton` vue combine la [`Button`](xref:Xamarin.Forms.Button) vue et la [`Image`](xref:Xamarin.Forms.Image) vue pour créer un bouton dont le contenu est une image. L’utilisateur appuie sur le `ImageButton` avec un doigt ou clique dessus avec la souris pour indiquer à l’application d’effectuer une tâche particulière. Toutefois, contrairement à la `Button` vue, la `ImageButton` vue n’a aucun concept de texte et d’apparence de texte.

> [!NOTE]
> Alors que la [`Button`](xref:Xamarin.Forms.Button) vue définit une [`Image`](xref:Xamarin.Forms.Button.Image) propriété, qui vous permet d’afficher une image sur le `Button` , cette propriété est destinée à être utilisée lors de l’affichage d’une petite icône en regard du `Button` texte.

Les exemples de code de ce guide sont tirés de l' [exemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Définition de la source de l’image

`ImageButton`définit une `Source` propriété qui doit être définie sur l’image à afficher dans le bouton, la source de l’image étant un fichier, un URI, une ressource ou un flux. Pour plus d’informations sur le chargement d’images à partir de différentes sources, consultez [images dans Xamarin.Forms ](images.md).

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

La `Source` propriété spécifie l’image qui apparaît dans le `ImageButton` . Dans cet exemple, il s’agit d’un fichier local qui sera chargé à partir de chaque projet de plateforme, ce qui génère les captures d’écran suivantes :

[![ImageButton de base](imagebutton-images/BasicImageButton.png "ImageButton de base")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton de base")

Par défaut, `ImageButton` est rectangulaire, mais vous pouvez lui attribuer des angles arrondis à l’aide de la `CornerRadius` propriété. Pour plus d’informations sur l' `ImageButton` apparence, consultez [apparence de ImageButton](#imagebutton-appearance).

> [!NOTE]
> Alors qu’un `ImageButton` peut charger un fichier GIF animé, il n’affiche que la première image de l’image GIF.

L’exemple suivant montre comment créer une page qui est fonctionnellement équivalente à l’exemple XAML précédent, mais entièrement en C# :

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

## <a name="handling-imagebutton-clicks"></a>Gestion des clics ImageButton

`ImageButton`définit un `Clicked` événement qui est déclenché quand l’utilisateur appuie `ImageButton` sur le avec un pointeur Finger ou Mouse. L’événement est déclenché lorsque le bouton de doigt ou de la souris est relâché à partir de la surface du `ImageButton` . La `ImageButton` propriété doit avoir la `IsEnabled` valeur `true` pour répondre aux pressions.

L’exemple suivant montre comment instancier un `ImageButton` en XAML et gérer son `Clicked` événement :

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

L' `Clicked` événement est défini sur un gestionnaire d’événements nommé `OnImageButtonClicked` qui se trouve dans le fichier code-behind :

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

Lorsque vous appuyez sur `ImageButton`, la méthode `OnImageButtonClicked` s’exécute. L' `sender` argument est le `ImageButton` responsable de cet événement. Vous pouvez l’utiliser pour accéder à l' `ImageButton` objet, ou pour faire la distinction entre plusieurs `ImageButton` objets qui partagent le même `Clicked` événement.

Ce `Clicked` gestionnaire particulier incrémente un compteur et affiche la valeur du compteur dans un [`Label`](xref:Xamarin.Forms.Label) :

[![Clic ImageButton de base](imagebutton-images/ImageButton.png "Clic ImageButton de base")](imagebutton-images/ImageButton-Large.png#lightbox "Clic ImageButton de base")

L’exemple suivant montre comment créer une page qui est fonctionnellement équivalente à l’exemple XAML précédent, mais entièrement en C# :

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

## <a name="disabling-the-imagebutton"></a>Désactivation du ImageButton

Parfois, une application se trouve dans un état particulier où un `ImageButton` clic particulier n’est pas une opération valide. Dans ce cas, `ImageButton` doit être désactivé en affectant à sa propriété la valeur `IsEnabled` `false` .

## <a name="using-the-command-interface"></a>Utilisation de l’interface de commande

Une application peut répondre à des `ImageButton` pressions sans gérer l' `Clicked` événement. Le `ImageButton` implémente un autre mécanisme de notification appelé _commande_ ou interface de _commande_ . Il s’agit de deux propriétés :

- `Command`de type [`ICommand`](xref:System.Windows.Input.ICommand) , une interface définie dans l' [`System.Windows.Input`](xref:System.Windows.Input) espace de noms.
- `CommandParameter`propriété de type [`Object`](xref:System.Object) .

Cette approche est appropriée dans le cadre de la liaison de données, et en particulier lors de l’implémentation de l’architecture MVVM (Model-View-ViewModel).

Pour plus d’informations sur l’utilisation de l’interface de commande, consultez [utilisation de l’interface de commande](button.md#using-the-command-interface) dans le Guide du [bouton](button.md) .

## <a name="pressing-and-releasing-the-imagebutton"></a>Appuyer et relâcher le ImageButton

Outre l’événement `Clicked`, `ImageButton` définit également les événements `Pressed` et `Released`. L' `Pressed` événement se produit lorsqu’un doigt appuie sur un `ImageButton` , ou lorsqu’un bouton de la souris est enfoncé avec le pointeur positionné sur le `ImageButton` . L' `Released` événement se produit lorsque le bouton de doigt ou de la souris est relâché. En règle générale, l' `Clicked` événement est également déclenché en même temps que l' `Released` événement, mais si le pointeur de la souris ou du doigt s’éloigne de la surface du `ImageButton` avant d’être relâché, il `Clicked` se peut que l’événement ne se produise pas.

Pour plus d’informations sur ces événements, consultez [pression et relâchement du bouton](button.md#pressing-and-releasing-the-button) dans le [bouton du bouton](button.md) .

## <a name="imagebutton-appearance"></a>Apparence ImageButton

En plus des propriétés qui `ImageButton` héritent de la [`View`](xref:Xamarin.Forms.View) classe, `ImageButton` définit également plusieurs propriétés qui affectent son apparence :

- `Aspect`indique comment l’image sera mise à l’échelle pour s’ajuster à la zone d’affichage.
- `BorderColor`couleur d’une zone entourant le `ImageButton` .
- `BorderWidth`largeur de la bordure.
- `CornerRadius`est le rayon de l’angle de `ImageButton` .

La `Aspect` propriété peut être définie sur l’un des membres de l' [`Aspect`](xref:Xamarin.Forms.Aspect) énumération :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill)-étire l’image pour qu’elle remplisse complètement et exactement le `ImageButton` . Cela peut entraîner la déformation de l’image.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill)-découpe l’image pour qu’elle remplisse le `ImageButton` tout en conservant les proportions.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit)-cadres l’image (si nécessaire) pour que l’image entière s’ajuste au `ImageButton` , avec un espace ajouté en haut/bas ou sur les côtés selon que l’image est grande ou en hauteur. Il s’agit de la valeur par défaut de l' [`Aspect`](xref:Xamarin.Forms.Aspect) énumération.

> [!NOTE]
> La `ImageButton` classe possède également [`Margin`](xref:Xamarin.Forms.View.Margin) des `Padding` Propriétés et qui contrôlent le comportement de disposition de `ImageButton` . Pour plus d’informations, consultez la page [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>États visuels ImageButton

`ImageButton`a un `Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour initier une modification visuelle du `ImageButton` lorsqu’il est enfoncé par l’utilisateur, à condition qu’il soit activé.

L’exemple de code XAML suivant montre comment définir un état visuel pour l' `Pressed` État :

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

`Pressed` [`VisualState`](xref:Xamarin.Forms.VisualState) Spécifie que lorsque le `ImageButton` est enfoncé, sa [`Scale`](xref:Xamarin.Forms.VisualElement.Scale) valeur par défaut de 1 à 0,8 est remplacée. `Normal` `VisualState` Spécifie que lorsque le `ImageButton` est dans un état normal, sa `Scale` propriété prend la valeur 1. Par conséquent, l’effet global est que lorsque l' `ImageButton` utilisateur appuie sur le, il est mis à l’échelle pour être légèrement plus petit et, lorsque le `ImageButton` est relâché, il est redimensionné à sa taille par défaut.

Pour plus d’informations sur les États visuels, consultez [le Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Exemple FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
