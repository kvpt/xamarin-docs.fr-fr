---
title: Xamarin.Forms AbsoluteLayout
description: Le Xamarin.Forms AbsoluteLayout est utilisé pour positionner et dimensionner des éléments à l’aide de valeurs explicites, ou des valeurs proportionnelles à la taille de la disposition.
ms.prod: xamarin
ms.assetid: 01A5CCE0-AD45-4806-84FD-72C007005B38
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/07/2020
ms.custom: contperfq1
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3fa88976c49d3ccb89b74a0ce40de0583fa20795
ms.sourcegitcommit: 9bd6b1b20d126b3f837c4cf859b25895c242e54e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88648159"
---
# <a name="no-locxamarinforms-absolutelayout"></a>Xamarin.Forms AbsoluteLayout

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)

[![::: No-Loc (Xamarin. Forms) ::: AbsoluteLayout](absolutelayout-images/layouts.png)](absolutelayout-images/layouts-large.png#lightbox)

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) est utilisé pour positionner et dimensionner des enfants à l’aide de valeurs explicites. La position est spécifiée par l’angle supérieur gauche de l’enfant par rapport au coin supérieur gauche du `AbsoluteLayout` , en unités indépendantes du périphérique. `AbsoluteLayout` implémente également une fonctionnalité de positionnement et de dimensionnement proportionnel. En outre, contrairement à d’autres classes de disposition, peut `AbsoluteLayout` positionner des enfants pour qu’ils se chevauchent.

Une [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) doit être considérée comme une disposition spéciale à utiliser uniquement lorsque vous pouvez imposer une taille aux enfants ou lorsque la taille de l’élément n’affecte pas le positionnement d’autres enfants.

La [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) classe définit les propriétés suivantes :

- [`LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty), de type [`Rectangle`](xref:Xamarin.Forms.Rectangle) , qui est une propriété jointe qui représente la position et la taille d’un enfant. La valeur par défaut de cette propriété est (0, 0, redimensionnement automatique).
- [`LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty), de type [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , qui est une propriété jointe qui indique si les propriétés des limites de disposition utilisées pour positionner et dimensionner l’enfant sont interprétées proportionnellement. La valeur par défaut de cette propriété est `AbsoluteLayoutFlags.None`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données et un style. Pour plus d’informations sur les propriétés jointes, consultez [ Xamarin.Forms propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).

La [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) classe dérive de la `Layout<T>` classe, qui définit une `Children` propriété de type `IList<T>` . La `Children` propriété est du `ContentProperty` de la `Layout<T>` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

> [!TIP]
> Pour obtenir les meilleures performances possibles en matière de disposition, suivez les instructions fournies dans [optimiser les performances](~/xamarin-forms/deploy-test/performance.md#optimize-layout-performance)de la disposition.

## <a name="position-and-size-children"></a>Enfants de position et de taille

La position et la taille des enfants dans un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) sont définies en définissant la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe de chaque enfant, en utilisant des valeurs absolues ou des valeurs proportionnelles. Les valeurs absolues et proportionnelles peuvent être mélangées pour les enfants lorsque la position doit être mise à l’échelle, mais la taille doit rester fixe, ou vice versa. Pour plus d’informations sur les valeurs absolues, consultez [positionnement et dimensionnement absolus](#absolute-positioning-and-sizing). Pour plus d’informations sur les valeurs proportionnelles, consultez [positionnement proportionnel et dimensionnement](#proportional-positioning-and-sizing).

La [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe peut être définie à l’aide de deux formats, que les valeurs absolues ou proportionnelles soient utilisées :

- `x, y`. Avec ce format, les `x` `y` valeurs et indiquent la position de l’angle supérieur gauche de l’enfant par rapport à son parent. L’enfant est sans contrainte et se redimensionne lui-même.
- `x, y, width, height`. Avec ce format, les `x` `y` valeurs et indiquent la position de l’angle supérieur gauche de l’enfant par rapport à son parent, tandis que les `width` `height` valeurs et indiquent la taille de l’enfant.

Pour spécifier qu’un enfant se redimensionne horizontalement ou verticalement, ou les deux, définissez les `width` valeurs et/ou `height` sur la [`AbsoluteLayout.AutoSize`](xref:Xamarin.Forms.AbsoluteLayout.AutoSize) propriété. Toutefois, cette propriété peut nuire aux performances de l’application, car elle fait en sorte que le moteur de disposition effectue des calculs de disposition supplémentaires.

> [!IMPORTANT]
> Les [`HorizontalOptions`](xref:Xamarin.Forms.View.HorizontalOptions) Propriétés et n' [`VerticalOptions`](xref:Xamarin.Forms.View.VerticalOptions) ont aucun effet sur les enfants d’un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) .

## <a name="absolute-positioning-and-sizing"></a>Positionnement et dimensionnement absolus

Par défaut, [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) les positions et les tailles enfants utilisent des valeurs absolues, spécifiées dans les unités indépendantes du périphérique, qui définissent de manière explicite où les enfants doivent être placés dans la disposition. Pour ce faire, il suffit d’ajouter des enfants à la `Children` collection d’un `AbsoluteLayout` et de définir la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe sur chaque enfant à des valeurs de position et/ou de taille absolues.

> [!WARNING]
> L’utilisation de valeurs absolues pour le positionnement et le dimensionnement des enfants peut être problématique, car les différents appareils ont des tailles et des résolutions d’écran différentes. Par conséquent, les coordonnées du centre de l’écran sur un appareil peuvent être décalées sur d’autres appareils.

Le code XAML suivant montre un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) dont les enfants sont positionnés à l’aide de valeurs absolues :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <AbsoluteLayout Margin="20">
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 10, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="0, 20, 200, 5" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="10, 0, 5, 65" />
        <BoxView Color="Silver"
                 AbsoluteLayout.LayoutBounds="20, 0, 5, 65" />
        <Label Text="Stylish Header"
               FontSize="24"
               AbsoluteLayout.LayoutBounds="30, 25" />
    </AbsoluteLayout>
</ContentPage>
```

Dans cet exemple, la position de chaque [`BoxView`](xref:Xamarin.Forms.BoxView) objet est définie à l’aide des deux premières valeurs absolues spécifiées dans la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe. La taille de chaque `BoxView` est définie à l’aide des troisième et quatrième valeurs. La position de l' [`Label`](xref:Xamarin.Forms.Label) objet est définie à l’aide des deux valeurs absolues spécifiées dans la `AbsoluteLayout.LayoutBounds` propriété jointe. Les valeurs de taille ne sont pas spécifiées pour le `Label` et, par conséquent, elles sont sans contrainte et se redimensionne lui-même. Dans tous les cas, les valeurs absolues représentent des unités indépendantes du périphérique.

La capture d’écran suivante montre la disposition résultante :

![Enfants placés dans un AbsoluteLayout à l’aide de valeurs absolues](absolutelayout-images/absolute-values.png)

Le code C# équivalent est illustré ci-dessous :

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        AbsoluteLayout absoluteLayout = new AbsoluteLayout
        {
            Margin = new Thickness(20)
        };

        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver,
        }, new Rectangle(0, 10, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(0, 20, 200, 5));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(10, 0, 5, 65));
        absoluteLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, new Rectangle(20, 0, 5, 65));

        absoluteLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, new Point(30,25));                    

        Title = "Stylish header demo";
        Content = absoluteLayout;
    }
}
```

Dans cet exemple, la position et la taille de chaque [`BoxView`](xref:Xamarin.Forms.BoxView) sont définies à l’aide d’un [`Rectangle`](xref:Xamarin.Forms.Rectangle) objet. La position du [`Label`](xref:Xamarin.Forms.Label) est définie à l’aide d’un [`Point`](xref:Xamarin.Forms.Point) objet.

En C#, il est également possible de définir la position et la taille d’un enfant d’un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) après qu’il a été ajouté à la `Children` collection, à l’aide de la [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) méthode. Le premier argument de cette méthode est l’enfant, tandis que le second est un [`Rectangle`](xref:Xamarin.Forms.Rectangle) objet.

> [!NOTE]
> Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) qui utilise des valeurs absolues peut positionner et dimensionner des enfants pour qu’ils ne tiennent pas dans les limites de la disposition.

## <a name="proportional-positioning-and-sizing"></a>Positionnement et dimensionnement proportionnels

Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) peut positionner et dimensionner des enfants à l’aide de valeurs proportionnelles. Pour ce faire, il suffit d’ajouter des enfants à la `Children` collection du `AbsoluteLayout` et de définir la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe sur chaque enfant en fonction de la position proportionnelle et/ou des valeurs de taille dans la plage 0-1. Les valeurs de position et de taille sont rendues proportionnelles en définissant la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propriété jointe sur chaque enfant.

La [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propriété jointe, de type [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) , vous permet de définir un indicateur qui signale que les limites de disposition de position et de taille d’un enfant sont proportionnelles à la taille de `AbsoluteLayout` . Lors de la disposition d’un enfant, `AbsoluteLayout` met à l’échelle les valeurs de position et de taille de manière appropriée, en fonction de la taille de l’appareil.

L' [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) énumération définit les membres suivants :

- `None`, indique que les valeurs seront interprétées comme absolues. Il s’agit de la valeur par défaut de la [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) propriété jointe.
- `XProportional`, indique que la `x` valeur sera interprétée comme proportionnelle, tout en traitant toutes les autres valeurs comme absolues.
- `YProportional`, indique que la `y` valeur sera interprétée comme proportionnelle, tout en traitant toutes les autres valeurs comme absolues.
- `WidthProportional`, indique que la `width` valeur sera interprétée comme proportionnelle, tout en traitant toutes les autres valeurs comme absolues.
- `HeightProportional`, indique que la `height` valeur sera interprétée comme proportionnelle, tout en traitant toutes les autres valeurs comme absolues.
- `PositionProportional`, indique que les `x` `y` valeurs et seront interprétées comme étant proportionnelles, tandis que les valeurs de taille sont interprétées comme absolues.
- `SizeProportional`, indique que les `width` `height` valeurs et seront interprétées comme étant proportionnelles, tandis que les valeurs de position sont interprétées comme absolues.
- `All`, indique que toutes les valeurs seront interprétées comme étant proportionnelles.

> [!TIP]
> L' [`AbsoluteLayoutFlags`](xref:Xamarin.Forms.AbsoluteLayoutFlags) énumération est une `Flags` énumération, ce qui signifie que les membres de l’énumération peuvent être combinés. Cela s’effectue en XAML avec une liste séparée par des virgules, et en C# avec l’opérateur de bits or.

Par exemple, si vous utilisez l' `SizeProportional` indicateur et définissez la largeur d’un enfant sur 0,25 et la hauteur sur 0,1, l’enfant correspond à un quart de la largeur de et à [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) un dixième de la hauteur. L' `PositionProportional` indicateur est similaire. Une position de (0,0) place l’enfant dans l’angle supérieur gauche, tandis qu’une position de (1,1) place l’enfant dans le coin inférieur droit, et une position de (0,5, 0,5) Centre l’enfant dans le `AbsoluteLayout` .

Le code XAML suivant montre un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) dont les enfants sont positionnés à l’aide de valeurs proportionnelles :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="AbsoluteLayoutDemos.Views.ProportionalDemoPage"
             Title="Proportional demo">
    <AbsoluteLayout>
        <BoxView Color="Blue"
                 AbsoluteLayout.LayoutBounds="0.5,0,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Green"
                 AbsoluteLayout.LayoutBounds="0,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Red"
                 AbsoluteLayout.LayoutBounds="1,0.5,25,100"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <BoxView Color="Black"
                 AbsoluteLayout.LayoutBounds="0.5,1,100,25"
                 AbsoluteLayout.LayoutFlags="PositionProportional" />
        <Label Text="Centered text"
               AbsoluteLayout.LayoutBounds="0.5,0.5,110,25"
               AbsoluteLayout.LayoutFlags="PositionProportional" />
    </AbsoluteLayout>
</ContentPage>
```

Dans cet exemple, chaque enfant est positionné à l’aide de valeurs proportionnelles mais dimensionnées à l’aide de valeurs absolues. Pour ce faire, affectez [`AbsoluteLayout.LayoutFlags`](xref:Xamarin.Forms.AbsoluteLayout.LayoutFlagsProperty) à la propriété jointe de chaque enfant la valeur `PositionProportional` . Les deux premières valeurs spécifiées dans la [`AbsoluteLayout.LayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.LayoutBoundsProperty) propriété jointe, pour chaque enfant, définissent la position à l’aide de valeurs proportionnelles. La taille de chaque enfant est définie avec les troisième et quatrième valeurs absolues, à l’aide d’unités indépendantes du périphérique.

La capture d’écran suivante montre la disposition résultante :

![Enfants placés dans un AbsoluteLayout à l’aide de valeurs de position proportionnelles](absolutelayout-images/proportional-position.png)

Le code C# équivalent est illustré ci-dessous :

```csharp
public class ProportionalDemoPageCS : ContentPage
{
    public ProportionalDemoPageCS()
    {
        BoxView blue = new BoxView { Color = Color.Blue };
        AbsoluteLayout.SetLayoutBounds(blue, new Rectangle(0.5, 0, 100, 25));
        AbsoluteLayout.SetLayoutFlags(blue, AbsoluteLayoutFlags.PositionProportional);

        BoxView green = new BoxView { Color = Color.Green };
        AbsoluteLayout.SetLayoutBounds(green, new Rectangle(0, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(green, AbsoluteLayoutFlags.PositionProportional);

        BoxView red = new BoxView { Color = Color.Red };
        AbsoluteLayout.SetLayoutBounds(red, new Rectangle(1, 0.5, 25, 100));
        AbsoluteLayout.SetLayoutFlags(red, AbsoluteLayoutFlags.PositionProportional);

        BoxView black = new BoxView { Color = Color.Black };
        AbsoluteLayout.SetLayoutBounds(black, new Rectangle(0.5, 1, 100, 25));
        AbsoluteLayout.SetLayoutFlags(black, AbsoluteLayoutFlags.PositionProportional);

        Label label = new Label { Text = "Centered text" };
        AbsoluteLayout.SetLayoutBounds(label, new Rectangle(0.5, 0.5, 110, 25));
        AbsoluteLayout.SetLayoutFlags(label, AbsoluteLayoutFlags.PositionProportional);

        Title = "Proportional demo";
        Content = new AbsoluteLayout
        {
            Children = { blue, green, red, black, label }
        };
    }
}
```

Dans cet exemple, la position et la taille de chaque enfant sont définies à l’aide de la [`AbsoluteLayout.SetLayoutBounds`](xref:Xamarin.Forms.AbsoluteLayout.SetLayoutBounds*) méthode. Le premier argument de la méthode est l’enfant, tandis que le second est un [`Rectangle`](xref:Xamarin.Forms.Rectangle) objet. La position de chaque enfant est définie avec des valeurs proportionnelles, tandis que la taille de chaque enfant est définie avec des valeurs absolues, à l’aide d’unités indépendantes du périphérique.

> [!NOTE]
> Un [`AbsoluteLayout`](xref:Xamarin.Forms.AbsoluteLayout) qui utilise des valeurs proportionnelles peut positionner et dimensionner des enfants pour qu’ils ne tiennent pas dans les limites de la disposition à l’aide de valeurs en dehors de la plage 0-1.

## <a name="related-links"></a>Liens connexes

- [Démonstrations AbsoluteLayout (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-absolutelayoutdemos)
- [Xamarin.Forms Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
- [Choisir une Xamarin.Forms disposition](choose-layout.md)
- [Améliorer les Xamarin.Forms performances des applications](~/xamarin-forms/deploy-test/performance.md)
