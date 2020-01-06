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
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490088"
---
# <a name="xamarinforms-imagebutton"></a>Xamarin.Forms ImageButton

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)

_ImageButton affiche une image et répond à un cliquez ou appuyez sur dirigeant d’une application à exécuter une tâche particulière._

Le `ImageButton` afficher combine le [ `Button` ](xref:Xamarin.Forms.Button) vue et [ `Image` ](xref:Xamarin.Forms.Image) vue pour créer un bouton dont le contenu est une image. L’utilisateur appuie sur le `ImageButton` avec un doigt ou clique dessus avec la souris pour diriger l’application d’effectuer une tâche particulière. Cependant, contrairement à la `Button` vue, le `ImageButton` vue n’a aucun concept de texte et l’apparence du texte.

> [!NOTE]
> Bien que le [ `Button` ](xref:Xamarin.Forms.Button) vue définit un [ `Image` ](xref:Xamarin.Forms.Button.Image) propriété, ce qui vous permet d’afficher une image sur le `Button`, cette propriété est destinée à être utilisée lors de l’affichage d’une petite icône à côté du `Button` texte.

Les exemples de code dans ce guide sont extraites de la [FormsGallery exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery).

## <a name="setting-the-image-source"></a>Définition de la source d’image

`ImageButton` définit un `Source` propriété qui doit être définie sur l’image à afficher dans le bouton, avec la source de l’image en cours d’un fichier, un URI, une ressource ou un flux de données. Pour plus d’informations sur le chargement des images à partir de différentes sources, consultez [Images dans Xamarin.Forms](images.md).

L’exemple suivant montre comment instancier un `ImageButton` dans XAML :

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

Le `Source` propriété spécifie l’image qui apparaît dans le `ImageButton`. Dans cet exemple, il est défini dans un fichier local qui sera chargé à partir de chaque projet de plateforme, ce qui entraîne les captures d’écran suivante :

[![ImageButton de base](imagebutton-images/BasicImageButton.png "ImageButton de base")](imagebutton-images/BasicImageButton-Large.png#lightbox "ImageButton de base")

Par défaut, le `ImageButton` est rectangulaire, mais vous pouvez donner des angles d’informatique arrondi à l’aide de la `CornerRadius` propriété. Pour plus d’informations sur `ImageButton` apparence, consultez [ImageButton apparence](#imagebutton-appearance).

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

`ImageButton` définit un `Clicked` événement est déclenché lorsque l’utilisateur actionne le `ImageButton` avec un pointeur doigt ou de la souris. L’événement est déclenché lorsque le bouton doigt ou de la souris est relâché à partir de la surface de la `ImageButton`. Le `ImageButton` doit avoir son `IsEnabled` propriété définie sur `true` pour répondre aux clics.

L’exemple suivant montre comment instancier un `ImageButton` dans XAML et de gérer ses `Clicked` événement :

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

Le `Clicked` événement est défini sur un gestionnaire d’événements nommé `OnImageButtonClicked` qui se trouve dans le fichier code-behind :

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

Lorsque le `ImageButton` est activé par un clic, le `OnImageButtonClicked` méthode s’exécute. Le `sender` argument est le `ImageButton` responsable de cet événement. Vous pouvez l’utiliser pour accéder à la `ImageButton` objet, ou faire la distinction entre plusieurs `ImageButton` objets partagent la même `Clicked` événement.

Cette particulier `Clicked` Gestionnaire incrémente un compteur et affiche la valeur de compteur dans un [ `Label` ](xref:Xamarin.Forms.Label):

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

Parfois, une application est dans un état particulier où un particulier `ImageButton` clic n’est pas une opération valide. Dans ce cas, le `ImageButton` doit être désactivée en définissant son `IsEnabled` propriété `false`.

## <a name="using-the-command-interface"></a>À l’aide de l’interface de commande

Il est possible pour une application répondre aux `ImageButton` robinets sans traitement le `Clicked` événement. Le `ImageButton` implémente un mécanisme de notification secondaire appelé le _commande_ ou _commandes_ interface. Cela se compose de deux propriétés :

- `Command` de type [ `ICommand` ](xref:System.Windows.Input.ICommand), une interface définie dans le [ `System.Windows.Input` ](xref:System.Windows.Input) espace de noms.
- `CommandParameter` propriété de type [ `Object` ](xref:System.Object).

Cette approche convient dans le cadre de liaison de données et en particulier lors de l’implémentation de l’architecture Model-View-ViewModel (MVVM).

Pour plus d’informations sur l’utilisation de l’interface de commande, consultez [à l’aide de l’interface de commande](button.md#using-the-command-interface) dans le [bouton](button.md) guide.

## <a name="pressing-and-releasing-the-imagebutton"></a>Appuyer et de relâcher ImageButton

Outre le `Clicked` événement, `ImageButton` définit également `Pressed` et `Released` événements. Le `Pressed` événement se produit lorsqu’un doigt appuie sur un `ImageButton`, ou un bouton de la souris est enfoncé avec le pointeur sur le `ImageButton`. Le `Released` événement se produit lorsque le bouton doigt ou de la souris est relâché. En règle générale, le `Clicked` événement est également déclenché en même temps que le `Released` événement, mais si le pointeur doigt ou de la souris diapositives en dehors de la surface de la `ImageButton` avant la version, le `Clicked` événement ne peut pas se produire.

Pour plus d’informations sur ces événements, consultez [en appuyant sur et relâchez le bouton de](button.md#pressing-and-releasing-the-button) dans le [bouton](button.md) guide.

## <a name="imagebutton-appearance"></a>Apparence de ImageButton

Outre les propriétés qui `ImageButton` hérite le [ `View` ](xref:Xamarin.Forms.View) classe, `ImageButton` définit également plusieurs propriétés qui affectent son apparence :

- `Aspect` est la façon dont l’image est redimensionnée pour s’ajuster à la zone d’affichage.
- `BorderColor` est la couleur d’une zone qui entoure la `ImageButton`.
- `BorderWidth` correspond à la largeur de la bordure.
- `CornerRadius` est le rayon d’angle de la `ImageButton`.

Le `Aspect` propriété peut être définie à un des membres de la [ `Aspect` ](xref:Xamarin.Forms.Aspect) énumération :

- [`Fill`](xref:Xamarin.Forms.Aspect.Fill) -étire l’image pour remplir des complètement et exactement le `ImageButton`. Cela peut entraîner l’image est déformée.
- [`AspectFill`](xref:Xamarin.Forms.Aspect.AspectFill) -découpe l’image afin qu’il remplisse la `ImageButton` tout en conservant les proportions.
- [`AspectFit`](xref:Xamarin.Forms.Aspect.AspectFit) -partout l’image (si nécessaire) afin que l’image entière s’adapte à la `ImageButton`, avec espace ajouté pour le haut/bas ou les côtés selon que l’image est la largeur ou hauteur. C’est la valeur par défaut de la [ `Aspect` ](xref:Xamarin.Forms.Aspect) énumération.

> [!NOTE]
> Le `ImageButton` classe a également [ `Margin` ](xref:Xamarin.Forms.View.Margin) et `Padding` propriétés qui contrôlent le comportement de disposition de la `ImageButton`. Pour plus d’informations, consultez [Marge et remplissage](~/xamarin-forms/user-interface/layouts/margin-and-padding.md).

## <a name="imagebutton-visual-states"></a>États visuels ImageButton

`ImageButton` a un `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) qui peut être utilisé pour lancer un plus grand changement pour le `ImageButton` lorsque vous appuyez sur par l’utilisateur, à condition qu’il soit activé.

L’exemple XAML suivant montre comment définir un état visuel pour le `Pressed` état :

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

Le `Pressed` [ `VisualState` ](xref:Xamarin.Forms.VisualState) Spécifie que quand le `ImageButton` est enfoncé, son [ `Scale` ](xref:Xamarin.Forms.VisualElement.Scale) propriété va être changée de sa valeur par défaut de 1 à 0,8. Le `Normal` `VisualState` Spécifie que quand le `ImageButton` est dans un état normal, son `Scale` propriété est définie sur 1. Par conséquent, l’effet global est que lorsque le `ImageButton` est enfoncé, il est remises à l’échelle pour être légèrement plus petits et lorsque le `ImageButton` est publié, il est remises à l’échelle à sa taille par défaut.

Pour plus d’informations sur les états visuels, consultez [le Gestionnaire d’état visuel Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [Exemple de FormsGallery](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/formsgallery)
