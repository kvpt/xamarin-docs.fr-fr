---
title: Classes de style Xamarin. Forms
description: Les classes de style Xamarin. Forms permettent d’appliquer plusieurs styles à un contrôle, sans recourir à l’héritage de style.
ms.prod: xamarin
ms.assetid: 4762401E-2B48-48F1-B6E4-61F7AF8AA46F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/30/2019
ms.openlocfilehash: 4a353d64f0e7e29da6c64f93b8554c3661f4d389
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70228127"
---
# <a name="xamarinforms-style-classes"></a>Classes de style Xamarin. Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)

_Les classes de style Xamarin. Forms permettent d’appliquer plusieurs styles à un contrôle, sans recourir à l’héritage de style._

## <a name="create-style-classes"></a>Créer des classes de style

Une classe de style peut être créée en affectant [`Class`](xref:Xamarin.Forms.Style.Class) [`Style`](xref:Xamarin.Forms.Style) à `string` la propriété sur un qui représente le nom de la classe. L’avantage que cela offre, par rapport à la définition d' `x:Key` un style explicite à l’aide de l’attribut, est que [`VisualElement`](xref:Xamarin.Forms.VisualElement)plusieurs classes de style peuvent être appliquées à un.

> [!IMPORTANT]
> Plusieurs styles peuvent partager le même nom de classe, à condition qu’ils ciblent des types différents. Cela permet à plusieurs classes de style, qui sont nommées de manière identique, de cibler différents types.

L’exemple suivant montre trois [`BoxView`](xref:Xamarin.Forms.BoxView) classes de style et une [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe de style:

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

Les `Separator`classes `Rounded`de style `Circle` , et définissent [`BoxView`](xref:Xamarin.Forms.BoxView) les propriétés sur des valeurs spécifiques.

La `Rotated` classe style a un [`TargetType`](xref:Xamarin.Forms.Style.TargetType) de [`VisualElement`](xref:Xamarin.Forms.VisualElement), ce qui signifie qu’elle ne peut être `VisualElement` appliquée qu’aux instances. Toutefois, sa [`ApplyToDerivedTypes`](xref:Xamarin.Forms.Style.ApplyToDerivedTypes) propriété a la `true`valeur, ce qui garantit qu’il peut être appliqué à tous les contrôles qui `VisualElement`dérivent [`BoxView`](xref:Xamarin.Forms.BoxView)de, tels que. Pour plus d’informations sur l’application d’un style à un type dérivé, consultez [appliquer un style à des types dérivés](implicit.md#apply-a-style-to-derived-types).

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

Les classes de style peuvent être consommées [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) en définissant la propriété du contrôle, qui `IList<string>`est de type, à une liste de noms de classes de style. Les classes de style seront appliquées, à condition que le type du contrôle corresponde [`TargetType`](xref:Xamarin.Forms.Style.TargetType) à l’une des classes de style.

L’exemple suivant illustre trois [`BoxView`](xref:Xamarin.Forms.BoxView) instances, chacune ayant des classes de style différentes:

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

Dans cet exemple, le premier [`BoxView`](xref:Xamarin.Forms.BoxView) est stylisé pour être un séparateur de ligne, tandis que `BoxView` le troisième est circulaire. Deux classes `BoxView` de style sont appliquées à la seconde, ce qui lui donne des angles arrondis et la fait pivoter de 45 degrés:

![BoxViews avec des classes de style](style-class-images/boxviews.png)

> [!IMPORTANT]
> Plusieurs classes de style peuvent être appliquées à un contrôle, [`StyleClass`](xref:Xamarin.Forms.NavigableElement.StyleClass) car la propriété est `IList<string>`de type. Lorsque cela se produit, les classes de style sont appliquées dans l’ordre croissant de la liste. Par conséquent, lorsque plusieurs classes de style définissent des propriétés identiques, la propriété dans la classe de style qui se trouve dans la position de liste la plus élevée est prioritaire.

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

- [Styles de base (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-styles-basicstyles)
