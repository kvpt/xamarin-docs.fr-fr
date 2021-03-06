---
title: Xamarin.Forms Classes de style
description: Xamarin.Forms les classes de style permettent d’appliquer plusieurs styles à un contrôle, sans recourir à l’héritage de style.
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: abe0b4804cdcca7fa5864dbadbfa2254b6c8c469
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93366488"
---
# <a name="no-locxamarinforms-style-classes"></a>Xamarin.Forms Classes de style

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Xamarin.Forms les classes de style permettent d’appliquer plusieurs styles à un contrôle, sans recourir à l’héritage de style._

## <a name="create-style-classes"></a>Créer des classes de style

Une classe de style peut être créée en affectant [`Class`](xref:Xamarin.Forms.Style.Class) à la propriété sur un [`Style`](xref:Xamarin.Forms.Style) `string` qui représente le nom de la classe. L’avantage que cela offre, par rapport à la définition d’un style explicite à l’aide de l' `x:Key` attribut, est que plusieurs classes de style peuvent être appliquées à un [`VisualElement`](xref:Xamarin.Forms.VisualElement) .

> [!IMPORTANT]
> Plusieurs styles peuvent partager le même nom de classe, à condition qu’ils ciblent des types différents. Cela permet à plusieurs classes de style, qui sont nommées de manière identique, de cibler différents types.

L’exemple suivant montre trois [`BoxView`](xref:Xamarin.Forms.BoxView) classes de style et une [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe de style :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        <Style TargetType="BoxView"
               Class="Separator">
            <Setter Property="BackgroundColor"
                    Value="#CCCCCC" />
            <Setter Property="HeightRequest"
                    Value="1" />
        </Style>

        <Style TargetType="BoxView"
               Class="Rounded">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="10" />
        </Style>    

        <Style TargetType="BoxView"
               Class="Circle">
            <Setter Property="BackgroundColor"
                    Value="#1FAECE" />
            <Setter Property="WidthRequest"
                    Value="100" />
            <Setter Property="HeightRequest"
                    Value="100" />
            <Setter Property="HorizontalOptions"
                    Value="Start" />
            <Setter Property="CornerRadius"
                    Value="50" />
        </Style>

        <Style TargetType="VisualElement"
               Class="Rotated"
               ApplyToDerivedTypes="true">
            <Setter Property="Rotation"
                    Value="45" />
        </Style>        
    </ContentPage.Resources>
</ContentPage>
```

Les `Separator` `Rounded` classes de style, et `Circle` définissent les [`BoxView`](xref:Xamarin.Forms.BoxView) Propriétés sur des valeurs spécifiques.

La `Rotated` classe style a un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de [`VisualElement`](xref:Xamarin.Forms.VisualElement) , ce qui signifie qu’elle ne peut être appliquée qu’aux `VisualElement` instances. Toutefois, sa [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriété a la valeur `true` , ce qui garantit qu’il peut être appliqué à tous les contrôles qui dérivent de `VisualElement` , tels que [`BoxView`](xref:Xamarin.Forms.BoxView) . Pour plus d’informations sur l’application d’un style à un type dérivé, consultez [appliquer un style à des types dérivés](implicit.md#apply-a-style-to-derived-types).

Le code C# équivalent est :

```csharp
var separatorBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Separator",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#CCCCCC")
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 1
        }
    }
};

var roundedBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Rounded",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 10
        }
    }
};

var circleBoxViewStyle = new Style(typeof(BoxView))
{
    Class = "Circle",
    Setters =
    {
        new Setter
        {
            Property = VisualElement.BackgroundColorProperty,
            Value = Color.FromHex("#1FAECE")
        },
        new Setter
        {
            Property = VisualElement.WidthRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = VisualElement.HeightRequestProperty,
            Value = 100
        },
        new Setter
        {
            Property = View.HorizontalOptionsProperty,
            Value = LayoutOptions.Start
        },
        new Setter
        {
            Property = BoxView.CornerRadiusProperty,
            Value = 50
        }
    }
};

var rotatedVisualElementStyle = new Style(typeof(VisualElement))
{
    Class = "Rotated",
    ApplyToDerivedTypes = true,
    Setters =
    {
        new Setter
        {
            Property = VisualElement.RotationProperty,
            Value = 45
        }
    }
};

Resources = new ResourceDictionary
{
    separatorBoxViewStyle,
    roundedBoxViewStyle,
    circleBoxViewStyle,
    rotatedVisualElementStyle
};
```

## <a name="consume-style-classes"></a>Utiliser des classes de style

Les classes de style peuvent être consommées en définissant la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propriété du contrôle, qui est de type `IList<string>` , à une liste de noms de classes de style. Les classes de style seront appliquées, à condition que le type du contrôle corresponde à l’une [`TargetType`](xref:Xamarin.Forms.Style.TargetType) des classes de style.

L’exemple suivant illustre trois [`BoxView`](xref:Xamarin.Forms.BoxView) instances, chacune ayant des classes de style différentes :

```xaml
<ContentPage ...>
    <ContentPage.Resources>
        ...
    </ContentPage.Resources>
    <StackLayout Margin="20">
        <BoxView StyleClass="Separator" />       
        <BoxView WidthRequest="100"
                 HeightRequest="100"
                 HorizontalOptions="Center"
                 StyleClass="Rounded, Rotated" />
        <BoxView HorizontalOptions="Center"
                 StyleClass="Circle" />
    </StackLayout>
</ContentPage>    
```

Dans cet exemple, le premier [`BoxView`](xref:Xamarin.Forms.BoxView) est stylisé pour être un séparateur de ligne, tandis que le troisième `BoxView` est circulaire. `BoxView`Deux classes de style sont appliquées à la seconde, ce qui lui donne des angles arrondis et la fait pivoter de 45 degrés :

![BoxViews avec des classes de style](style-class-images/boxviews.png)

> [!IMPORTANT]
> Plusieurs classes de style peuvent être appliquées à un contrôle, car la [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) propriété est de type `IList<string>` . Lorsque cela se produit, les classes de style sont appliquées dans l’ordre croissant de la liste. Par conséquent, lorsque plusieurs classes de style définissent des propriétés identiques, la propriété dans la classe de style qui se trouve dans la position de liste la plus élevée est prioritaire.

Le code C# équivalent est :

```csharp
...
Content = new StackLayout
{
    Children =
    {
        new BoxView { StyleClass = new [] { "Separator" } },
        new BoxView { WidthRequest = 100, HeightRequest = 100, HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Rounded", "Rotated" } },
        new BoxView { HorizontalOptions = LayoutOptions.Center, StyleClass = new [] { "Circle" } }
    }
};
```

## <a name="related-links"></a>Liens connexes

- [Styles de base (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)