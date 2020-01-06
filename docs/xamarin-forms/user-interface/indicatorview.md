---
title: Xamarin. Forms IndicatorView
description: Le IndicatorView est un contrôle qui affiche des indicateurs qui représentent le nombre d’éléments et la position actuelle dans un CarouselView.
ms.prod: xamarin
ms.assetId: BBCC223B-4B02-46B7-80BB-EE0E86A67CE2
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/17/2019
ms.openlocfilehash: 6b7845011470d83d8f2187e0227950c23e46d52d
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490401"
---
# <a name="xamarinforms-indicatorview"></a>Xamarin. Forms IndicatorView

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)

Le `IndicatorView` est un contrôle qui affiche des indicateurs qui représentent le nombre d’éléments et la position actuelle dans un `CarouselView`:

[![Capture d’écran d’un CarouselView et d’un IndicatorView, sur iOS et Android](indicatorview-images/circles.png "Cercles IndicatorView")](indicatorview-images/circles-large.png#lightbox "Cercles IndicatorView")

`IndicatorView` est disponible dans Xamarin. Forms 4,4 sur les plateformes iOS et Android. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre classe `AppDelegate` sur iOS, ou à votre classe `MainActivity` sur Android, avant d’appeler `Forms.Init`:

```csharp
Forms.SetFlags("IndicatorView_Experimental");
```

`IndicatorView` définit les propriétés suivantes :

- `Count`, de type `int`, le nombre d’indicateurs.
- `HideSingle`, de type `bool`, indique si l’indicateur doit être masqué lorsqu’il n’en existe qu’un seul. La valeur par défaut est `true`.
- `IndicatorColor`, de type `Color`, la couleur des indicateurs.
- `IndicatorSize`, de type `double`, taille des indicateurs. La valeur par défaut est 6,0.
- `IndicatorLayout`, de type `Layout<View>`, définit la classe de disposition utilisée pour restituer le `IndicatorView`. Cette propriété est définie par Xamarin. Forms et n’a généralement pas besoin d’être définie par les développeurs.
- `IndicatorTemplate`, de type `DataTemplate`, le modèle qui définit l’apparence de chaque indicateur.
- `IndicatorsShape`, de type `IndicatorShape`, forme de chaque indicateur.
- `ItemsSource`, de type `IEnumerable`, la collection pour laquelle les indicateurs sont affichés. Cette propriété est définie automatiquement lorsque la propriété `ItemsSourceBy` est définie.
- `ItemsSourceBy`, de type `VisualElement`, l’objet `CarouselView` pour lequel afficher les indicateurs.
- `MaximumVisible`, de type `int`, le nombre maximal d’indicateurs visibles. La valeur par défaut est `int.MaxValue`.
- `Position`, de type `int`, l’index d’indicateur actuellement sélectionné. Cette propriété utilise une liaison de `TwoWay`. Cette propriété est définie automatiquement lorsque la propriété `ItemsSourceBy` est définie.
- `SelectedIndicatorColor`, de type `Color`, la couleur de l’indicateur qui représente l’élément actuel dans le `CarouselView`.

Ces propriétés sont soulignes par [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et un style.

## <a name="create-an-indicatorview"></a>Créer un IndicatorView

L’exemple suivant montre comment instancier un `IndicatorView` en XAML :

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
                   IndicatorColor="LightGray"
                   SelectedIndicatorColor="DarkGray"
                   HorizontalOptions="Center" />
</StackLayout>
```

Dans cet exemple, le `IndicatorView` est rendu sous le `CarouselView`, avec un indicateur pour chaque élément dans le `CarouselView`. La `IndicatorView` est remplie avec des données en affectant à la propriété `ItemsSourceBy` la valeur de l’objet `CarouselView`. Chaque indicateur est un cercle gris clair, tandis que l’indicateur qui représente l’élément actuel dans le `CarouselView` est gris foncé.

> [!IMPORTANT]
> La définition de la propriété `ItemsSourceBy` entraîne la liaison de la propriété `Position` à la propriété `CarouselView.Position` et la propriété `ItemsSource` à la propriété `CarouselView.ItemsSource`.

## <a name="change-indicator-shape"></a>Modifier la forme d’indicateur

La classe `IndicatorView` a une propriété `IndicatorsShape`, qui indique la forme des indicateurs. Cette propriété peut être définie sur l’un des membres de l’énumération `IndicatorShape` :

- `Circle` spécifie que les formes d’indicateur seront circulaires. C’est la valeur par défaut de la propriété `IndicatorView.IndicatorsShape`.
- `Square` indique que les formes d’indicateur seront carrées.

L’exemple suivant montre une `IndicatorView` configurée pour utiliser des indicateurs carrés :

```xaml
<IndicatorView IndicatorsShape="Square"
               ItemsSourceBy="carouselView"
               IndicatorColor="LightGray"
               SelectedIndicatorColor="DarkGray" />
```

## <a name="define-indicator-appearance"></a>Définir l’apparence de l’indicateur

L’apparence de chaque indicateur peut être définie en affectant à la propriété `IndicatorView.IndicatorTemplate` la valeur d’un [`DataTemplate`](xref:Xamarin.Forms.DataTemplate):

```xaml
<StackLayout>
    <CarouselView x:Name="carouselView"
                  ItemsSource="{Binding Monkeys}">
        <CarouselView.ItemTemplate>
            <!-- DataTemplate that defines item appearance -->
        </CarouselView.ItemTemplate>
    </CarouselView>
    <IndicatorView ItemsSourceBy="carouselView"
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

Les éléments spécifiés dans le [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) définissent l’apparence de chaque indicateur. Dans cet exemple, chaque indicateur est un [`Image`](xref:Xamarin.Forms.Image) qui affiche une icône de police à l’aide de l’extension de balisage `FontImage`.

Les captures d’écran suivantes montrent les indicateurs affichés à l’aide d’une icône de police :

[![Capture d’écran d’un IndicatorView basé sur un modèle, sur iOS et Android](indicatorview-images/templated.png "IndicatorView basé sur un modèle")](indicatorview-images/templated-large.png#lightbox "IndicatorView basé sur un modèle")

Pour plus d’informations sur l’extension de balisage `FontImage`, consultez [FontImage Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension).

## <a name="related-links"></a>Liens connexes

- [IndicatorView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-indicatorviewdemos/)
- [Extension de balisage FontImage](~/xamarin-forms/xaml/markup-extensions/consuming.md#fontimage-markup-extension)
