---
title: Xamarin.FormsRelativeLayout
description: Le Xamarin.Forms RelativeLayout est utilisé pour la position et la taille des enfants par rapport aux propriétés des éléments de disposition ou frères.
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/13/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b23da34239d99faa64578bd30c5a3e969cf4b289
ms.sourcegitcommit: 808ff109928a1eea16e17e23ea81f8c903a239e8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88181782"
---
# <a name="no-locxamarinforms-relativelayout"></a>Xamarin.FormsRelativeLayout

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)

[![::: No-Loc (Xamarin. Forms) ::: RelativeLayout](relativelayout-images/layouts.png)](relativelayout-images/layouts-large.png#lightbox)

Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) est utilisé pour positionner et dimensionner des enfants par rapport aux propriétés des éléments de disposition ou frères. Cela permet de créer des interfaces utilisateur qui évoluent proportionnellement entre les tailles des appareils. En outre, contrairement à d’autres classes de disposition, peut `RelativeLayout` positionner des enfants pour qu’ils se chevauchent.

La [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) classe définit les propriétés suivantes :

- [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty), de type [`Constraint`](xref:Xamarin.Forms.Constraint) , qui est une propriété jointe qui représente la contrainte sur la position X de l’enfant.
- [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty), de type [`Constraint`](xref:Xamarin.Forms.Constraint) , qui est une propriété jointe qui représente la contrainte sur la position Y de l’enfant.
- [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty), de type [`Constraint`](xref:Xamarin.Forms.Constraint) , qui est une propriété jointe qui représente la contrainte sur la largeur de l’enfant.
- [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty), de type [`Constraint`](xref:Xamarin.Forms.Constraint) , qui est une propriété jointe qui représente la contrainte sur la hauteur de l’enfant.
- [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty), de type [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) , qui est une propriété jointe qui représente la contrainte sur la position et la taille de l’enfant. Cette propriété ne peut pas être facilement utilisée à partir de XAML.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données et un style. Pour plus d’informations sur les propriétés jointes, consultez [ Xamarin.Forms propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).

> [!NOTE]
> La largeur et la hauteur d’un enfant dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) peuvent également être spécifiées via les `WidthRequest` Propriétés et de l’enfant `HeightRequest` , au lieu des [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) propriétés jointes et.

La [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) classe dérive de la `Layout<T>` classe, qui définit une `Children` propriété de type `IList<T>` . La `Children` propriété est du `ContentProperty` de la `Layout<T>` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

> [!TIP]
> Évitez d’utiliser dans la mesure du [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) possible. Le processeur aurait considérablement plus de travail à effectuer.

## <a name="constraints"></a>Contraintes

Dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) , la position et la taille des enfants sont spécifiées sous forme de contraintes à l’aide de valeurs absolues ou de valeurs relatives. Lorsque les contraintes ne sont pas spécifiées, un enfant est positionné dans le coin supérieur gauche de la disposition.

Le tableau suivant indique comment spécifier des contraintes en XAML et C# :

|     | XAML | C# |
| --- | ---- | -- |
| **Valeurs absolues** | Les contraintes absolues sont spécifiées en affectant des [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) valeurs aux propriétés attachées `double` . | Les contraintes absolues sont spécifiées par la [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) méthode ou à l’aide de la `Children.Add` surcharge qui requiert un `Func<Rectangle>` argument. |
| **Valeurs relatives** | Les contraintes relatives sont spécifiées en définissant les [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) propriétés jointes aux [`Constraint`](xref:Xamarin.Forms.Constraint) objets qui sont retournés par l' `ConstraintExpression` extension de balisage. | Les contraintes relatives sont spécifiées par [`Constraint`](xref:Xamarin.Forms.Constraint) les objets retournés par les méthodes de la `Constraint` classe. |

Pour plus d’informations sur la spécification de contraintes à l’aide de valeurs absolues, consultez [positionnement et dimensionnement absolus](#absolute-positioning-and-sizing). Pour plus d’informations sur la spécification de contraintes à l’aide de valeurs relatives, consultez [positionnement et dimensionnement relatifs](#relative-positioning-and-sizing).

En C#, les enfants peuvent être ajoutés à [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) par trois `Add` surcharges. La première surcharge requiert un `Expression<Func<Rectangle>>` pour spécifier la position et la taille d’un enfant. La deuxième surcharge requiert `Expression<Func<double>>` des objets facultatifs pour les `x` `y` arguments,, `width` et `height` . La troisième surcharge requiert `Constraint` des objets facultatifs pour les `x` `y` arguments,, `width` et `height` .

Il est possible de modifier la position et la taille d’un enfant dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) avec les [`SetXConstraint`](xref:Xamarin.Forms.RelativeLayout.SetXConstraint*) méthodes,, [`SetYConstraint`](xref:Xamarin.Forms.RelativeLayout.SetYConstraint*) [`SetWidthConstraint`](xref:Xamarin.Forms.RelativeLayout.SetWidthConstraint*) et [`SetHeightConstraint`](xref:Xamarin.Forms.RelativeLayout.SetHeightConstraint*) . Le premier argument de chacune de ces méthodes est l’enfant, tandis que le second est un [`Constraint`](xref:Xamarin.Forms.Constraint) objet. En outre, la [`SetBoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.SetBoundsConstraint*) méthode peut également être utilisée pour modifier la position et la taille d’un enfant. Le premier argument de cette méthode est l’enfant, tandis que le second est un [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) objet.

## <a name="absolute-positioning-and-sizing"></a>Positionnement et dimensionnement absolus

Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) peut positionner et dimensionner des enfants à l’aide de valeurs absolues, spécifiées dans des unités indépendantes du périphérique, qui définissent explicitement où les enfants doivent être placés dans la disposition. Pour ce faire, il suffit d’ajouter des enfants à la `Children` collection d’un `RelativeLayout` et de définir les [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) Propriétés,, [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) et [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) jointes sur chaque enfant à des valeurs de position et/ou de taille absolues.

> [!WARNING]
> L’utilisation de valeurs absolues pour le positionnement et le dimensionnement des enfants peut être problématique, car les différents appareils ont des tailles et des résolutions d’écran différentes. Par conséquent, les coordonnées du centre de l’écran sur un appareil peuvent être décalées sur d’autres appareils.

Le code XAML suivant montre un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) dont les enfants sont positionnés à l’aide de valeurs absolues :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.StylishHeaderDemoPage"
             Title="Stylish header demo">
    <RelativeLayout Margin="20">
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="10"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="0"
                 RelativeLayout.YConstraint="20"
                 RelativeLayout.WidthConstraint="200"
                 RelativeLayout.HeightConstraint="5" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="10"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <BoxView Color="Silver"
                 RelativeLayout.XConstraint="20"
                 RelativeLayout.YConstraint="0"
                 RelativeLayout.WidthConstraint="5"
                 RelativeLayout.HeightConstraint="65" />
        <Label Text="Stylish header"
               FontSize="24"
               RelativeLayout.XConstraint="30"
               RelativeLayout.YConstraint="25" />
    </RelativeLayout>
</ContentPage>
```

Dans cet exemple, la position de chaque [`BoxView`](xref:Xamarin.Forms.BoxView) objet est définie à l’aide des valeurs spécifiées dans les [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) propriétés jointes et. La taille de chaque `BoxView` est définie à l’aide des valeurs spécifiées dans les [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) propriétés jointes et. La position de l' [`Label`](xref:Xamarin.Forms.Label) objet est également définie à l’aide des valeurs spécifiées dans les `XConstraint` `YConstraint` propriétés jointes et. Toutefois, les valeurs de taille ne sont pas spécifiées pour le `Label` et, par conséquent, elles sont sans contrainte et se redimensionne lui-même. Dans tous les cas, les valeurs absolues représentent des unités indépendantes du périphérique.

Les captures d’écran suivantes illustrent la disposition obtenue :

![Enfants placés dans un RelativeLayout à l’aide de valeurs absolues](relativelayout-images/absolute-values.png)

Le code C# équivalent est illustré ci-dessous :

```csharp
public class StylishHeaderDemoPageCS : ContentPage
{
    public StylishHeaderDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout
        {
            Margin = new Thickness(20)
        };

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 10, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(0, 20, 200, 5));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(10, 0, 5, 65));

        relativeLayout.Children.Add(new BoxView
        {
            Color = Color.Silver
        }, () => new Rectangle(20, 0, 5, 65));

        relativeLayout.Children.Add(new Label
        {
            Text = "Stylish Header",
            FontSize = 24
        }, Constraint.Constant(30), Constraint.Constant(25));

        Title = "Stylish header demo";
        Content = relativeLayout;
    }
}
```

Dans cet exemple, [`BoxView`](xref:Xamarin.Forms.BoxView) les objets sont ajoutés au à l' [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) aide d’une `Add` surcharge qui requiert un `Expression<Func<Rectangle>>` pour spécifier la position et la taille de chaque enfant. La position du [`Label`](xref:Xamarin.Forms.Label) est définie à l’aide d’une `Add` surcharge qui requiert [`Constraint`](xref:Xamarin.Forms.Constraint) des objets facultatifs, dans ce cas créé par la [`Constraint.Constant`](xref:Xamarin.Forms.Constraint.Constant*) méthode.

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) qui utilise des valeurs absolues peut positionner et dimensionner des enfants pour qu’ils ne tiennent pas dans les limites de la disposition.

## <a name="relative-positioning-and-sizing"></a>Positionnement et dimensionnement relatifs

Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) peut positionner et dimensionner des enfants à l’aide de valeurs relatives aux propriétés de la disposition ou des éléments frères. Pour ce faire, il suffit d’ajouter des enfants à la `Children` collection du `RelativeLayout` et de définir les [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) Propriétés,, [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) et [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) jointes sur chaque enfant à des valeurs relatives à l’aide d' [`Constraint`](xref:Xamarin.Forms.Constraint) objets.

Les contraintes peuvent être une constante, relative à un parent, ou relative à un frère. Le type de contrainte est représenté par l' [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) énumération, qui définit les membres suivants :

- `RelativeToParent`, qui indique une contrainte relative à un parent.
- `RelativeToView`, qui indique une contrainte relative à une vue (ou frère).
- `Constant`, qui indique une contrainte de constante.

### <a name="constraint-markup-extension"></a>Contrainte (extension de balisage)

En XAML, un [`Constraint`](xref:Xamarin.Forms.Constraint) objet peut être créé par l' [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) extension de balisage. Cette extension de balisage est généralement utilisée pour relier la position et la taille d’un enfant dans un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) à son parent ou à un frère.

La [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) classe définit les propriétés suivantes :

- [`Constant`](xref:Xamarin.Forms.ConstraintExpression.Constant), de type `double` , qui représente la valeur de constante de contrainte.
- [`ElementName`](xref:Xamarin.Forms.ConstraintExpression.ElementName), de type `string` , qui représente le nom d’un élément source par rapport auquel calculer la contrainte.
- [`Factor`](xref:Xamarin.Forms.ConstraintExpression.Factor), de type `double` , qui représente le facteur de mise à l’échelle d’une dimension avec restriction par rapport à l’élément source. La valeur par défaut de cette propriété est 1.
- [`Property`](xref:Xamarin.Forms.ConstraintExpression.Property), de type `string` , qui représente le nom de la propriété sur l’élément source à utiliser dans le calcul de la contrainte.
- [`Type`](xref:Xamarin.Forms.ConstraintExpression.Type), de type [`ConstraintType`](xref:Xamarin.Forms.ConstraintType) , qui représente le type de la contrainte.

Pour plus d’informations sur les Xamarin.Forms extensions de balisage, consultez [extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md).

Le code XAML suivant montre un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) dont les enfants sont limités par l' [`ConstraintExpression`](xref:Xamarin.Forms.ConstraintExpression) extension de balisage :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="RelativeLayoutDemos.Views.RelativePositioningAndSizingDemoPage"
             Title="RelativeLayout demo">
    <RelativeLayout>
        <BoxView Color="Red"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Green"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=Constant, Constant=0}" />
        <BoxView Color="Blue"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=Constant, Constant=0}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />
        <BoxView Color="Yellow"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Constant=-40}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Constant=-40}" />

        <!-- Centered and 1/3 width and height of parent -->
        <BoxView x:Name="oneThird"
                 Color="Silver"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0.33}" />

        <!-- 1/3 width and height of previous -->
        <BoxView Color="Black"
                 RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=X}"
                 RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Y}"
                 RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Width, Factor=0.33}"
                 RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToView, ElementName=oneThird, Property=Height, Factor=0.33}" />
    </RelativeLayout>
</ContentPage>
```

Dans cet exemple, la position de chaque [`BoxView`](xref:Xamarin.Forms.BoxView) objet est définie en définissant [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) les [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) propriétés jointes et. La première `BoxView` a ses `XConstraint` `YConstraint` Propriétés et jointes définies sur des constantes, qui sont des valeurs absolues. La position des objets restants `BoxView` est définie à l’aide d’au moins une valeur relative. Par exemple, l' `BoxView` objet jaune affecte `XConstraint` à la propriété jointe la largeur de son parent (le [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ) moins 40. De même, `BoxView` définit la `YConstraint` propriété jointe sur la hauteur de son parent moins 40. Cela permet de s’assurer que le jaune `BoxView` apparaît dans le coin inférieur droit de l’écran.

> [!NOTE]
> [`BoxView`](xref:Xamarin.Forms.BoxView)les objets qui ne spécifient pas une taille sont automatiquement dimensionnés à 40 x 40 par Xamarin.Forms .

Le [`BoxView`](xref:Xamarin.Forms.BoxView) nommé Silver `oneThird` est positionné de manière centralisée par rapport à son parent. Elle est également dimensionnée par rapport à son parent, qui est un tiers de sa largeur et de sa hauteur. Cela est possible en affectant [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) à la propriété et aux propriétés jointes la largeur du parent (le [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) ), multiplié par 0,33. De même, les [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) propriétés jointes et sont définies sur la hauteur du parent, multiplié par 0,33.

Le noir [`BoxView`](xref:Xamarin.Forms.BoxView) est positionné et dimensionné par rapport au `oneThird` `BoxView` . Cela est possible en définissant [`XConstraint`](xref:Xamarin.Forms.RelativeLayout.XConstraintProperty) ses [`YConstraint`](xref:Xamarin.Forms.RelativeLayout.YConstraintProperty) Propriétés et jointes sur les `X` `Y` valeurs et, respectivement, de l’élément frère. De même, sa taille est définie à un tiers de la largeur et de la hauteur de son élément frère. Pour ce faire, définissez ses [`WidthConstraint`](xref:Xamarin.Forms.RelativeLayout.WidthConstraintProperty) [`HeightConstraint`](xref:Xamarin.Forms.RelativeLayout.HeightConstraintProperty) Propriétés et jointes aux `Width` `Height` valeurs et de l’élément frère, respectivement, qui sont ensuite multipliées par 0,33.

La capture d’écran suivante montre la disposition résultante :

![Enfants placés dans un RelativeLayout à l’aide de valeurs relatives](relativelayout-images/relative-values.png)

### <a name="constraint-objects"></a>Objets de contrainte

La [`Constraint`](xref:Xamarin.Forms.Constraint) classe définit les méthodes statiques publiques suivantes, qui retournent des `Constraint` objets :

- [`Constant`](xref:Xamarin.Forms.Constraint.Constant*), qui limite un enfant à une taille spécifiée avec un `double` .
- [`FromExpression`](xref:Xamarin.Forms.Constraint.FromExpression*), qui limite un enfant à l’aide d’une expression lambda.
- [`RelativeToParent`](xref:Xamarin.Forms.Constraint.RelativeToParent*), qui limite un enfant par rapport à la taille de son parent.
- [`RelativeToView`](xref:Xamarin.Forms.Constraint.RelativeToView*), qui limite un enfant par rapport à la taille d’une vue.

En outre, la [`BoundsConstraint`](xref:Xamarin.Forms.BoundsConstraint) classe définit une méthode unique, [`FromExpression`](xref:Xamarin.Forms.BoundsConstraint.FromExpression*) , qui retourne un `BoundsConstraint` qui limite la position et la taille d’un enfant à un `Expression<Func<Rectangle>>` . Cette méthode peut être utilisée pour définir la [`BoundsConstraint`](xref:Xamarin.Forms.RelativeLayout.BoundsConstraintProperty) propriété jointe.

Le code C# suivant montre un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) dont les enfants sont limités par des [`Constraint`](xref:Xamarin.Forms.Constraint) objets :

```csharp
public class RelativePositioningAndSizingDemoPageCS : ContentPage
{
    public RelativePositioningAndSizingDemoPageCS()
    {
        RelativeLayout relativeLayout = new RelativeLayout();

        // Four BoxView's
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Red },
            Constraint.Constant(0),
            Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Green },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }), Constraint.Constant(0));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Blue },
            Constraint.Constant(0),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        relativeLayout.Children.Add(
            new BoxView { Color = Color.Yellow },
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width - 40;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height - 40;
            }));

        // Centered and 1/3 width and height of parent
        BoxView silverBoxView = new BoxView { Color = Color.Silver };
        relativeLayout.Children.Add(
            silverBoxView,
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Width * 0.33;
            }),
            Constraint.RelativeToParent((parent) =>
            {
                return parent.Height * 0.33;
            }));

        // 1/3 width and height of previous
        relativeLayout.Children.Add(
            new BoxView { Color = Color.Black },
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.X;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Y;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Width * 0.33;
            }),
            Constraint.RelativeToView(silverBoxView, (parent, sibling) =>
            {
                return sibling.Height * 0.33;
            }));

        Title = "RelativeLayout demo";
        Content = relativeLayout;
    }
}
```

Dans cet exemple, les enfants sont ajoutés au [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) à l’aide de la `Add` surcharge qui requiert un `Constraint` objet facultatif pour les `x` `y` arguments,, `width` et `height` .

> [!NOTE]
> Un [`RelativeLayout`](xref:Xamarin.Forms.RelativeLayout) qui utilise des valeurs relatives peut positionner et dimensionner des enfants pour qu’ils ne tiennent pas dans les limites de la disposition.

## <a name="related-links"></a>Liens connexes

- [Démonstrations RelativeLayout (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-relativelayoutdemos)
- [Xamarin.FormsPropriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
- [Extensions de balisage XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
- [Choisir une Xamarin.Forms disposition](choose-layout.md)
- [Améliorer les Xamarin.Forms performances des applications](~/xamarin-forms/deploy-test/performance.md)
