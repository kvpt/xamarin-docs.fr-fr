---
title: Xamarin.Forms RelativeLayout
description: Cet article explique comment utiliser la classe de RelativeLayout de Xamarin.Forms pour créer des interfaces utilisateur qui n’importe quelle taille d’écran à l’échelle.
ms.prod: xamarin
ms.assetid: 2530BCB8-01B8-4C4F-BF14-CA53659F1B5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/25/2015
ms.openlocfilehash: d8c2cc4f31b148ee3181629e5b3b5faf01016617
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70772536"
---
# <a name="xamarinforms-relativelayout"></a>Xamarin.Forms RelativeLayout

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)

`RelativeLayout` est utilisé pour la position et les vues de taille par rapport aux propriétés des vues frère ou de mise en page. Contrairement aux `AbsoluteLayout`, `RelativeLayout` n’a pas le concept de l’ancrage du déplacement et n’a pas de fonctionnalités pour positionner des éléments par rapport à bas ou bords droits de la disposition. `RelativeLayout` prend en charge de positionnement des éléments en dehors de ses propres limites.

[![](relative-layout-images/layouts-sml.png "Dispositions Xamarin.Forms")](relative-layout-images/layouts.png#lightbox "dispositions Xamarin.Forms")

## <a name="purpose"></a>Objectif

`RelativeLayout` peut être utilisé pour positionner des vues sur l’écran par rapport à la disposition générale ou à d’autres vues.

![](relative-layout-images/flag.png "Exploration de RelativeLayout")

## <a name="usage"></a>Utilisation

### <a name="understanding-constraints"></a>Contraintes de présentation

Positionnement et dimensionnement d’une vue dans un `RelativeLayout` s’effectue avec des contraintes. Une expression de contrainte peut inclure les informations suivantes :

- **Type** &ndash; si la contrainte est relatif au parent ou à un autre affichage.
- **Propriété** &ndash; la propriété à utiliser comme base pour la contrainte.
- **Facteur** &ndash; le facteur à appliquer à la valeur de propriété.
- **Constante** &ndash; la valeur à utiliser en tant qu’offset de la valeur.
- **ElementName** &ndash; le nom de la vue de la contrainte est relative.

Dans XAML, les contraintes sont exprimées sous forme `ConstraintExpression`s. Prenons l'exemple suivant :

```xaml
<BoxView Color="Green" WidthRequest="50" HeightRequest="50"
    RelativeLayout.XConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Width,
                             Factor=0.5,
                             Constant=-100}"
    RelativeLayout.YConstraint =
      "{ConstraintExpression Type=RelativeToParent,
                             Property=Height,
                             Factor=0.5,
                             Constant=-100}" />
```

Dans C#, contraintes sont exprimées en un peu différemment, à l’aide de fonctions plutôt que des expressions sur la vue. Les contraintes sont spécifiées en tant qu’arguments à la mise en page `Add` méthode :

```csharp
layout.Children.Add(box, Constraint.RelativeToParent((parent) =>
    {
      return (.5 * parent.Width) - 100;
    }),
    Constraint.RelativeToParent((parent) =>
    {
        return (.5 * parent.Height) - 100;
    }),
    Constraint.Constant(50), Constraint.Constant(50));
```

Notez les aspects suivants de la disposition ci-dessus :

- Le `x` et `y` les contraintes sont spécifiées avec leurs propres contraintes.
- Dans C#, relatifs contraintes sont définies en tant que fonctions. Concepts comme `Factor` ne sont pas actuellement, mais peuvent être implémentées manuellement.
- La zone `x` coordonnée est définie en tant que la moitié de la largeur de la page parente, -100.
- La zone `y` coordonnée est définie en tant que la moitié de la hauteur de la page parente, -100.

> [!NOTE]
> En raison du mode contraintes sont définies, il est possible d’élaborer des dispositions plus complexes dans C# que peut être spécifié avec XAML.

Les deux exemples ci-dessus définissent des contraintes comme `RelativeToParent` &ndash; , autrement dit, leurs valeurs sont par rapport à l’élément parent. Il est également possible de définir des contraintes par rapport à une autre vue. Cela permet des dispositions plus intuitives (pour le développeur) et peut rendre l’intention de votre code de mise en page plus évidentes.

Envisagez une mise en page où un élément doit être inférieure à une autre de 20 pixels. Si les deux éléments sont définis avec des valeurs constantes, faible peut avoir son `Y` contrainte définie en tant que constante qui est de 20 pixels supérieure à la `Y` contrainte de l’élément supérieur. Cette approche s’avère insuffisant, si l’élément supérieur est positionné à l’aide d’une part, afin que la taille en pixels n’est pas connue. Dans ce cas, il est plus robuste de contraindre l’élément en fonction de la position d’un autre élément :

```xaml
<RelativeLayout>
    <BoxView Color="Red" x:Name="redBox"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent,
            Property=Height,Factor=.15,Constant=0}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=1,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.8,Constant=0}" />
    <BoxView Color="Blue"
        RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=Y,Factor=1,Constant=20}"
        RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView,
            ElementName=redBox,Property=X,Factor=1,Constant=20}"
        RelativeLayout.WidthConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Width,Factor=.5,Constant=0}"
        RelativeLayout.HeightConstraint="{ConstraintExpression
            Type=RelativeToParent,Property=Height,Factor=.5,Constant=0}" />
</RelativeLayout>
```

Pour accomplir la même disposition dans C#:

```csharp
layout.Children.Add (redBox, Constraint.RelativeToParent ((parent) => {
        return parent.X;
    }), Constraint.RelativeToParent ((parent) => {
        return parent.Y * .15;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .8;
    }));
layout.Children.Add (blueBox, Constraint.RelativeToView (redBox, (Parent, sibling) => {
        return sibling.X + 20;
    }), Constraint.RelativeToView (blueBox, (parent, sibling) => {
        return sibling.Y + 20;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Width * .5;
    }), Constraint.RelativeToParent((parent) => {
        return parent.Height * .5;
    }));
```

Cela génère la sortie suivante, avec la position de la zone bleue déterminée _relatif_ à la position de la zone rouge :

![](relative-layout-images/red-blue-box.png "RelativeLayout avec BoxViews rouge et bleu")

### <a name="sizing"></a>Dimensionnement

Vues disposés par `RelativeLayout` ont deux options pour spécifier leur taille :

- `HeightRequest & WidthRequest`
- `RelativeLayout.WidthConstraint` & `RelativeLayout.HeightConstraint`

`HeightRequest` et `WidthRequest` spécifient la hauteur prévue et la largeur de la vue, mais peut être substituée par les dispositions en fonction des besoins. `WidthConstraint` et `HeightConstraint` en charge la définition de la hauteur et largeur en tant que valeur par rapport aux propriétés de la mise en page ou d’une autre vue, ou comme une valeur constante.

## <a name="exploring-a-complex-layout"></a>Exploration d’une disposition complexe
Chacune des mises en page ont des forces et faiblesses pour créer des dispositions particuliers. Tout au long de cette série d’articles de la mise en page, un exemple d’application a été créé avec la même disposition de page implémentée à l’aide de trois dispositions différentes.

Prenez en compte le XAML suivant :

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="TheBusinessTumble.RelativeLayoutPage"
BackgroundColor="Maroon"
Title="RelativeLayout">
    <ContentPage.Content>
    <ScrollView>
      <RelativeLayout>
        <BoxView Color="Gray" HeightRequest="100"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Button BorderRadius="35" x:Name="imageCircleBack"
            BackgroundColor="Maroon" HeightRequest="70" WidthRequest="70" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent,Property=Width, Factor=.5, Constant = -35}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=70}" />
        <Button BorderRadius="30" BackgroundColor="Red" HeightRequest="60"
            WidthRequest="60" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToView, ElementName=imageCircleBack, Property=X, Factor=1,Constant=5}" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Factor=0, Property=Y, Constant=75}" />
        <Label Text="User Name" FontAttributes="Bold" FontSize="26"
            HorizontalTextAlignment="Center" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=140}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <Entry Text="Bio + Hashtags" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=180}" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" />
        <RelativeLayout BackgroundColor="White" RelativeLayout.YConstraint="
            {ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=220}" HeightRequest="60" RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=1}" >
            <BoxView BackgroundColor="Black" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=5}" />
            <BoxView BackgroundColor="Maroon" WidthRequest="50"
                HeightRequest="50" RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=5}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5}" />
            <Label FontSize="14" TextColor="Black" Text="Accent Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=X, Factor=0, Constant=60}" />
            <Label FontSize="14" TextColor="Black" Text="Primary Color"
                RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Y, Factor=0, Constant=20}" RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.5, Constant=55}" />
        </RelativeLayout>
        <RelativeLayout Padding="5,0,0,0">
          <Label FontSize="14" Text="Age:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=305}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="35" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=280}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Interests:" TextColor="White"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=345}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin.Forms" TextColor="White" BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=320}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
        <RelativeLayout  Padding="5,0,0,0">
          <Label FontSize="14" Text="Ask me about:" TextColor="White"
            LineBreakMode="WordWrap"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=395}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0, Constant=10}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=.25,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
          <Entry Text="Xamarin, C#, .NET, Mono" TextColor="White"
            BackgroundColor="Maroon"
            RelativeLayout.YConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=370}"
            RelativeLayout.XConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width, Factor=0.3, Constant=0}"
            RelativeLayout.WidthConstraint="{ConstraintExpression Type=RelativeToParent, Property=Width,Factor=0.75,Constant=0}"
            RelativeLayout.HeightConstraint="{ConstraintExpression Type=RelativeToParent, Property=Height, Factor=0,Constant=50}" />
        </RelativeLayout>
      </RelativeLayout>
    </ScrollView>
  </ContentPage.Content>
</ContentPage>
```

Le code ci-dessus génère la disposition suivante :

![](relative-layout-images/relative.png "RelativeLayout complexe")

Notez que `RelativeLayouts`s sont imbriquées, car dans certains cas des dispositions d’imbrication peut être plus facile que de présenter tous les éléments dans la même disposition. Notez également que certains éléments sont `RelativeToView`, car qui permet de disposition plus facile et plus intuitive lorsque les relations entre les vues de guident de positionnement.

## <a name="related-links"></a>Liens associés

- [Disposition (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-layout)
- [Exemple BusinessTumble (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-businesstumble)
