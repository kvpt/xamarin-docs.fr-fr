---
title: Xamarin.Forms IndicatorView
description: Le IndicatorView est un contrôle qui affiche des indicateurs qui représentent le nombre d’éléments et la position actuelle dans un CarouselView.
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 938bdc55cc577bda53fcf8c6d70bc71ea5cb97a0
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93371961"
---
# <a name="no-locxamarinforms-indicatorview"></a>Xamarin.Forms IndicatorView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

Le `IndicatorView` est un contrôle qui affiche des indicateurs qui représentent le nombre d’éléments, et la position actuelle, dans un `CarouselView` :

[![Capture d’écran d’un CarouselView et d’un IndicatorView, sur iOS et Android](indicatorview-images/circles.png "Cercles IndicatorView")](indicatorview-images/circles-large.png#lightbox "Cercles IndicatorView")

`IndicatorView` définit les propriétés suivantes :

- `Count`, de type `int` , le nombre d’indicateurs.
- `HideSingle`, de type `bool` , indique si l’indicateur doit être masqué lorsqu’il n’en existe qu’un seul. La valeur par défaut est `true`.
- `IndicatorColor`, de type `Color` , la couleur des indicateurs.
- `IndicatorSize`, de type `double` , la taille des indicateurs. La valeur par défaut est 6,0.
- `IndicatorLayout`, de type `Layout<View>` , définit la classe de disposition utilisée pour restituer le `IndicatorView` . Cette propriété est définie par Xamarin.Forms et n’a généralement pas besoin d’être définie par les développeurs.
- `IndicatorTemplate`, de type `DataTemplate` , le modèle qui définit l’apparence de chaque indicateur.
- `IndicatorsShape`, de type `IndicatorShape` , la forme de chaque indicateur.
- `ItemsSource`, de type `IEnumerable` , la collection pour laquelle les indicateurs seront affichés. Cette propriété est définie automatiquement lorsque la `CarouselView.IndicatorView` propriété est définie.
- `MaximumVisible`, de type `int` , le nombre maximal d’indicateurs visibles. La valeur par défaut est `int.MaxValue`.
- `Position`, de type `int` , l’index d’indicateur actuellement sélectionné. Cette propriété utilise une `TwoWay` liaison. Cette propriété est définie automatiquement lorsque la `CarouselView.IndicatorView` propriété est définie.
- `SelectedIndicatorColor`, de type `Color` , la couleur de l’indicateur qui représente l’élément actuel dans `CarouselView` .

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

## <a name="create-an-indicatorview"></a>Créer un IndicatorView

L’exemple suivant montre comment instancier un `IndicatorView` en XAML :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

Dans cet exemple, le `IndicatorView` est rendu sous le `CarouselView` , avec un indicateur pour chaque élément dans `CarouselView` . Le `IndicatorView` est rempli avec les données en affectant `CarouselView.IndicatorView` à la propriété l' `IndicatorView` objet. Chaque indicateur est un cercle gris clair, tandis que l’indicateur qui représente l’élément actuel dans la `CarouselView` est gris foncé.

> [!IMPORTANT]
> La définition de la propriété entraîne la liaison de la propriété `CarouselView.IndicatorView` `IndicatorView.Position` à la propriété `CarouselView.Position` et la `IndicatorView.ItemsSource` liaison de la propriété à la `CarouselView.ItemsSource` propriété.

## <a name="change-indicator-shape"></a>Modifier la forme d’indicateur

La `IndicatorView` classe a une `IndicatorsShape` propriété, qui détermine la forme des indicateurs. Cette propriété peut être définie sur l’un des `IndicatorShape` membres de l’énumération :

- `Circle` Spécifie que les formes d’indicateur seront circulaires. C’est la valeur par défaut de la propriété `IndicatorView.IndicatorsShape`.
- `Square` indique que les formes d’indicateur seront carrées.

L’exemple suivant montre un `IndicatorView` configuré pour utiliser des indicateurs carrés :

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorsShape="Square"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="change-indicator-size"></a>Modifier la taille de l’indicateur

La `IndicatorView` classe a une `IndicatorSize` propriété, de type `double` , qui détermine la taille des indicateurs dans les unités indépendantes du périphérique. La valeur par défaut de cette propriété est 6,0.

L’exemple suivant montre un `IndicatorView` configuré pour afficher des indicateurs plus grands :

```xaml
<IndicatorView x:Name="indicatorView"
               IndicatorSize="18" />
```

## <a name="limit-the-number-of-indicators-displayed"></a>Limiter le nombre d’indicateurs affichés

La `IndicatorView` classe a une `MaximumVisible` propriété, de type `int` , qui détermine le nombre maximal d’indicateurs visibles.

L’exemple suivant montre un `IndicatorView` configuré pour afficher un maximum de six indicateurs :

```xaml
<IndicatorView x:Name="indicatorView"
               MaximumVisible="6" />
```

## <a name="define-indicator-appearance"></a>Définir l’apparence de l’indicateur

L’apparence de chaque indicateur peut être définie en affectant `IndicatorView.IndicatorTemplate` à la propriété la valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) :

```xaml
<StackLayout>
    <CarouselView ItemsSource="{Binding Monkeys}"
                  IndicatorView="indicatorView">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView x:Name="indicatorView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="Black"
                   HorizontalOptions="Center">
        <IndicatorView.IndicatorTemplate>
            <DataTemplate>
                <Image Source="{FontImage &#xf30c;, FontFamily={OnPlatform iOS=Ionicons, Android=ionicons.ttf#}, Size=12}" />
            </DataTemplate>
        </IndicatorView.IndicatorTemplate>
    </IndicatorView>
</StackLayout>
```

Les éléments spécifiés dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque indicateur. Dans cet exemple, chaque indicateur est un [`Image`](xref:Xamarin.Forms.Image) qui affiche une icône de police à l’aide de l' `FontImage` extension de balisage.

Les captures d’écran suivantes montrent les indicateurs affichés à l’aide d’une icône de police :

[![Capture d’écran d’un IndicatorView basé sur un modèle, sur iOS et Android](indicatorview-images/templated.png "IndicatorView basé sur un modèle")](indicatorview-images/templated-large.png#lightbox "IndicatorView basé sur un modèle")

Pour plus d’informations sur l' `FontImage` extension de balisage, consultez [FontImage Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Liens connexes

- [IndicatorView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [FontImage, extension de balisage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)