---
title: Frame Xamarin. Forms
description: La classe Frame Xamarin. Forms est une disposition utilisée pour encapsuler une vue ou une disposition avec une bordure qui peut être configurée avec une couleur, une ombre et d’autres options.
ms.prod: xamarin
ms.assetId: 4E074714-0928-41C8-A468-B60E23236A8C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/06/2019
ms.openlocfilehash: 619b29a9d65594b1badd805c3361fe1a174d7174
ms.sourcegitcommit: 1341f2950b775a4daa7d0548a51fdef759afd6e3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69976499"
---
# <a name="xamarinforms-frame"></a>Frame Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

La classe Xamarin. [`Frame`](xref:Xamarin.Forms.Frame) Forms est une disposition utilisée pour encapsuler une vue avec une bordure qui peut être configurée avec une couleur, une ombre et d’autres options. Les frames sont couramment utilisés pour créer des bordures autour des contrôles, mais peuvent être utilisés pour créer une interface utilisateur plus complexe. Pour plus d’informations, consultez [Utilisation avancée](#advanced-frame-usage)des frames.

La capture d’écran `Frame` suivante montre les contrôles sur iOS et Android:

Exemples de frames [![sur iOS et Android sur iOS et Android](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "sur iOS et Android sur iOS et Android")

La `Frame` classe définit les propriétés suivantes:

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor)est une `Color` valeur qui détermine la couleur de la `Frame` bordure.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius)`float` valeur qui détermine le rayon arrondi de l’angle.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow)est une `bool` valeur qui détermine si le frame a une ombre portée.

Ces propriétés sont sauvegardées par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) des objets, ce qui `Frame` signifie que peut être la cible des liaisons de données.

> [!NOTE]
> Le `HasShadow` comportement de la propriété dépend de la plateforme. La valeur par défaut `true` est sur toutes les plateformes. Toutefois, les ombres portées UWP ne sont pas rendues. Les ombres portées sont rendues sur Android et iOS, mais les ombres portées sur iOS sont plus sombres et occupent davantage d’espace.

## <a name="create-a-frame"></a>Créer un frame

Un `Frame` peut être instancié en XAML. L’objet `Frame` par défaut a un arrière-plan blanc, une ombre portée et aucune bordure. Un `Frame` objet encapsule généralement un autre contrôle. L’exemple suivant illustre une encapsulation par `Label` défaut `Frame` d’un objet:

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

Un `Frame` peut également être créé dans le code:

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

`Frame`les objets peuvent être personnalisés avec des angles arrondis, des bordures coloriées et des ombres portées en définissant des propriétés dans le XAML. L’exemple suivant montre un objet `Frame` personnalisé:

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

Ces propriétés d’instance peuvent également être définies dans le code:

```csharp
Frame frame = new Frame
{
    BorderColor = Color.Orange,
    CornerRadius = 10,
    HasShadow = true,
    Content = new Label { Text = "Example" }
};
```

## <a name="advanced-frame-usage"></a>Utilisation avancée des frames

La `Frame` classe hérite de `ContentView`, ce qui signifie qu’elle peut contenir tout `View` type d' `Layout` objet, y compris les objets. Cette capacité permet à `Frame` d’être utilisé pour créer des objets d’interface utilisateur complexes tels que des cartes.

### <a name="create-a-card-with-a-frame"></a>Créer une carte avec un cadre

La combinaison `Frame` d’un objet `Layout` avec un objet tel `StackLayout` qu’un objet permet la création d’une interface utilisateur plus complexe. La capture d’écran suivante montre un exemple de carte, `Frame` créé à l’aide d’un objet:

[«Capture d’écran d’une carte créée à l’aide d’un cadre» d’une carte créée à l’aide d’un cadre ![](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "")

Le code XAML suivant montre comment créer une carte avec la `Frame` classe:

```xaml
<Frame BorderColor="Gray"
       CornerRadius="5"
       Padding="8">
  <StackLayout>
    <Label Text="Card Example"
           FontSize="Medium"
           FontAttributes="Bold" />
    <BoxView Color="Gray"
             HeightRequest="2"
             HorizontalOptions="Fill" />
    <Label Text="Frames can wrap more complex layouts to create more complex UI components, such as this card!"/>
  </StackLayout>
</Frame>
```

Une carte peut également être créée dans le code:

```csharp
Frame cardFrame = new Frame
{
    BorderColor = Color.Gray,
    CornerRadius = 5,
    Padding = 8,
    Content = new StackLayout
    {
        Children =
        {
            new Label
            {
                Text = "Card Example",
                FontSize = Device.GetNamedSize(NamedSize.Medium, typeof(Label)),
                FontAttributes = FontAttributes.Bold
            },
            new BoxView
            {
                Color = Color.Gray,
                HeightRequest = 2,
                HorizontalOptions = LayoutOptions.Fill
            },
            new Label
            {
                Text = "Frames can wrap more complex layouts to create more complex UI components, such as this card!"
            }
        }
    }
};
```

### <a name="round-elements"></a>Éléments arrondis

La `CornerRadius` propriété`Frame` du contrôle peut être utilisée pour créer une image de cercle. La capture d’écran suivante montre un exemple d’image ronde créée à l' `Frame` aide d’un objet:

[![cercle créée à l’aide d’un cadre ](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Capture d’écran d’une image de")

Le code XAML suivant montre comment créer une image de cercle en XAML:

```xaml
<Frame Margin="10"
       BorderColor="Black"
       CornerRadius="50"
       HeightRequest="60"
       WidthRequest="60"
       IsClippedToBounds="True"
       HorizontalOptions="Center"
       VerticalOptions="Center">
  <Image Source="outdoors.jpg"
         Aspect="AspectFill"
         Margin="-20"
         HeightRequest="100"
         WidthRequest="100" />
</Frame>
```

Une image de cercle peut également être créée dans le code:

```csharp
Frame circleImageFrame = new Frame
{
    Margin = 10,
    BorderColor = Color.Black,
    CornerRadius = 50,
    HeightRequest = 60,
    WidthRequest = 60,
    IsClippedToBounds = true,
    HorizontalOptions = LayoutOptions.Center,
    VerticalOptions = LayoutOptions.Center,
    Content = new Image
    {
        Source = ImageSource.FromFile("outdoors.jpg"),
        Aspect = Aspect.AspectFill,
        Margin = -20,
        HeightRequest = 100,
        WidthRequest = 100
    }
};
```

L’image **Outdoor. jpg** doit être ajoutée à chaque projet de plateforme, et la façon dont cela est réalisé varie selon la plateforme. Pour plus d’informations, consultez [images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md).

> [!NOTE]
> Les angles arrondis se comportent légèrement différemment sur les plateformes. La `Image` valeur de `Margin` l’objet doit être la moitié de la différence entre la largeur de l’image et la largeur du cadre parent, et doit être négative pour centrer l’image uniformément au sein de l' `Frame` objet. Toutefois, la largeur et la hauteur demandées ne sont pas garanties `Margin`. `HeightRequest` par `WidthRequest` conséquent, les propriétés, et doivent être modifiées en fonction de la taille de votre image et d’autres choix de disposition.

## <a name="related-links"></a>Liens connexes

* [Démonstrations de frame](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
