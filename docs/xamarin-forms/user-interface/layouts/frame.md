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
ms.sourcegitcommit: dad4dfcd194b63ec9e903363351b6d9e543d4888
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/18/2019
ms.locfileid: "69976499"
---
# <a name="xamarinforms-frame"></a>Frame Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)

La classe Xamarin. Forms [`Frame`](xref:Xamarin.Forms.Frame) est une disposition utilisée pour encapsuler une vue avec une bordure qui peut être configurée avec une couleur, une ombre et d’autres options. Les frames sont couramment utilisés pour créer des bordures autour des contrôles, mais peuvent être utilisés pour créer une interface utilisateur plus complexe. Pour plus d’informations, consultez [Utilisation avancée des frames](#advanced-frame-usage).

La capture d’écran suivante montre les contrôles de `Frame` sur iOS et Android :

[![exemples de cadre sur iOS et Android](frame-images/frame-cropped.png)](frame-images/frame-full.png#lightbox "Exemples de frames sur iOS et Android")

La classe `Frame` définit les propriétés suivantes :

* [`BorderColor`](xref:Xamarin.Forms.Frame.BorderColor) est une valeur de `Color` qui détermine la couleur de la bordure de `Frame`.
* [`CornerRadius`](xref:Xamarin.Forms.Frame.CornerRadius) est une valeur de `float` qui détermine le rayon arrondi de l’angle.
* [`HasShadow`](xref:Xamarin.Forms.Frame.HasShadow) est une valeur `bool` qui détermine si le cadre a une ombre portée.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les `Frame` peuvent être la cible des liaisons de données.

> [!NOTE]
> Le comportement de la propriété `HasShadow` dépend de la plateforme. La valeur par défaut est `true` sur toutes les plateformes. Toutefois, les ombres portées UWP ne sont pas rendues. Les ombres portées sont rendues sur Android et iOS, mais les ombres portées sur iOS sont plus sombres et occupent davantage d’espace.

## <a name="create-a-frame"></a>Créer un frame

Un `Frame` peut être instancié en XAML. L’objet `Frame` par défaut a un arrière-plan blanc, une ombre portée et aucune bordure. Un objet `Frame` encapsule généralement un autre contrôle. L’exemple suivant montre une `Frame` par défaut encapsulant un objet `Label` :

```xaml
<Frame>
  <Label Text="Example" />
</Frame>
```

Vous pouvez également créer un `Frame` dans le code :

```csharp
Frame defaultFrame = new Frame
{
    Content = new Label { Text = "Example" }
};
```

les objets `Frame` peuvent être personnalisés avec des angles arrondis, des bordures colorées et des ombres portées en définissant des propriétés dans le XAML. L’exemple suivant montre un objet `Frame` personnalisé :

```xaml
<Frame BorderColor="Orange"
       CornerRadius="10"
       HasShadow="True">
  <Label Text="Example" />
</Frame>
```

Ces propriétés d’instance peuvent également être définies dans le code :

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

La classe `Frame` hérite de `ContentView`, ce qui signifie qu’elle peut contenir tout type d’objet `View`, y compris des objets `Layout`. Cette fonctionnalité permet d’utiliser les `Frame` pour créer des objets d’interface utilisateur complexes tels que des cartes.

### <a name="create-a-card-with-a-frame"></a>Créer une carte avec un cadre

La combinaison d’un objet `Frame` avec un objet `Layout` tel qu’un objet `StackLayout` permet la création d’une interface utilisateur plus complexe. La capture d’écran suivante montre un exemple de carte créée à l’aide d’un objet `Frame` :

[![capture d’écran d’une carte créée à l’aide d’un cadre](frame-images/frame-card-cropped.png)](frame-images/frame-full.png#lightbox "Capture d’écran d’une carte créée à l’aide d’un frame")

Le code XAML suivant montre comment créer une carte avec la classe `Frame` :

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

Une carte peut également être créée dans le code :

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

La propriété `CornerRadius` du contrôle `Frame` peut être utilisée pour créer une image de cercle. La capture d’écran suivante montre un exemple d’image ronde créée à l’aide d’un objet `Frame` :

[Capture d’écran ![ d’une image de cercle créée à l’aide d’un cadre](frame-images/circle-image-cropped.png)](frame-images/frame-full.png#lightbox "Capture d’écran d’une image de cercle créée à l’aide d’un frame")

Le code XAML suivant montre comment créer une image de cercle en XAML :

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

Une image de cercle peut également être créée dans le code :

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
> Les angles arrondis se comportent légèrement différemment sur les plateformes. La `Margin` de l’objet `Image` doit être la moitié de la différence entre la largeur de l’image et la largeur du cadre parent, et doit être négative pour centrer l’image uniformément dans l’objet `Frame`. Toutefois, la largeur et la hauteur demandées ne sont pas garanties. par conséquent, il peut être nécessaire de modifier les propriétés de `Margin`, `HeightRequest` et `WidthRequest` en fonction de la taille de votre image et d’autres choix de disposition.

## <a name="related-links"></a>Liens connexes

* [Démonstrations de frame](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-frame/)
* [Images dans Xamarin. Forms](~/xamarin-forms/user-interface/images.md)
