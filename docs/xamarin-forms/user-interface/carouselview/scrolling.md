---
title: Xamarin.Forms Défilement CarouselView
description: Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, CarouselView définit deux méthodes ScrollTo, qui défilent par programmation des éléments dans la vue.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4c05e6fef969e002befe2afadb8e7db9e477aaba
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940510"
---
# <a name="no-locxamarinforms-carouselview-scrolling"></a>Xamarin.Forms Défilement CarouselView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés associées au défilement suivantes :

- `HorizontalScrollBarVisibility`, de type `ScrollBarVisibility` , qui spécifie quand la barre de défilement horizontale est visible.
- `IsDragging`, de type `bool` , qui indique si le `CarouselView` fait défiler. Il s’agit d’une propriété en lecture seule, dont la valeur par défaut est `false` .
- `IsScrollAnimated`, de type `bool` , qui spécifie si une animation doit se produire lors du défilement du `CarouselView` . La valeur par défaut est `true`.
- `ItemsUpdatingScrollMode`, de type `ItemsUpdatingScrollMode` , qui représente le comportement de défilement de `CarouselView` lorsque de nouveaux éléments y sont ajoutés.
- `VerticalScrollBarVisibility`, de type `ScrollBarVisibility` , qui spécifie le moment où la barre de défilement verticale est visible.

Toutes ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit également deux [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes qui défilent les éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue. Les deux surcharges ont des arguments supplémentaires qui peuvent être spécifiés pour indiquer la position exacte de l’élément une fois le défilement terminé, et s’il faut animer le défilement.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) événement qui est déclenché lorsque l’une des [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes est appelée. L' [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objet qui accompagne l' `ScrollToRequested` événement a de nombreuses propriétés, notamment `IsAnimated` , `Index` , `Item` et `ScrollToPosition` . Ces propriétés sont définies à partir des arguments spécifiés dans les `ScrollTo` appels de méthode.

En outre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un `Scrolled` événement qui est déclenché pour indiquer que le défilement s’est produit. L' `ItemsViewScrolledEventArgs` objet qui accompagne l' `Scrolled` événement a de nombreuses propriétés. Pour plus d’informations, consultez [détecter le défilement](#detect-scrolling).

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. Cette fonctionnalité est appelée alignement, car les éléments sont alignés à la position lorsque le défilement s’arrête. Pour plus d’informations, consultez [points d’alignement](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) peut également charger des données de façon incrémentielle au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Détecter le défilement

La `IsDragging` propriété peut être examinée pour déterminer si le [`CarouselView`](xref:Xamarin.Forms.CarouselView) fait actuellement défiler les éléments.

En outre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un `Scrolled` événement qui est déclenché pour indiquer que le défilement s’est produit. Cet événement doit être consommé lorsque les données relatives au défilement sont requises.

L’exemple de code XAML suivant montre un `CarouselView` qui définit un gestionnaire d’événements pour l' `Scrolled` événement :

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

Dans cet exemple de code, le `OnCarouselViewScrolled` Gestionnaire d’événements est exécuté lorsque l' `Scrolled` événement se déclenche :

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

Dans cet exemple, le `OnCarouselViewScrolled` Gestionnaire d’événements génère les valeurs de l' `ItemsViewScrolledEventArgs` objet qui accompagne l’événement.

> [!IMPORTANT]
> L' `Scrolled` événement est déclenché pour les défilement initiés par l’utilisateur et pour les défilement par programmation.

## <a name="scroll-an-item-at-an-index-into-view"></a>Faire défiler un élément d’un index dans la vue

La première [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) surcharge de méthode fait défiler l’élément à l’index spécifié dans la vue. À partir d’un [`CarouselView`](xref:Xamarin.Forms.CarouselView) objet nommé `carouselView` , l’exemple suivant montre comment faire défiler l’élément à l’index 6 dans la vue :

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) événement est déclenché lorsque la [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthode est appelée.

## <a name="scroll-an-item-into-view"></a>Faire défiler un élément dans l’affichage

La deuxième [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) surcharge de méthode fait défiler l’élément spécifié dans la vue. À partir d’un [`CarouselView`](xref:Xamarin.Forms.CarouselView) objet nommé `carouselView` , l’exemple suivant montre comment faire défiler l’élément de singe Proboscis dans la vue :

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) événement est déclenché lorsque la [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthode est appelée.

## <a name="disable-scroll-animation"></a>Désactiver l’animation de défilement

Une animation de défilement s’affiche lorsque vous passez d’un élément à un autre [`CarouselView`](xref:Xamarin.Forms.CarouselView) . Cette animation se produit à la fois pour les défilement initiés par l’utilisateur et pour les défilement par programmation. L’affectation de la valeur `IsScrollAnimated` à la propriété `false` désactive l’animation pour les deux catégories de défilement.

L' `animate` argument de la `ScrollTo` méthode peut également avoir la valeur `false` pour désactiver l’animation de défilement sur les défilement par programmation :

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Position de défilement du contrôle

Lorsque vous faites défiler un élément dans l’affichage, la position exacte de l’élément après la fin du défilement peut être spécifiée avec l' `position` argument des [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes. Cet argument accepte un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membre de l’énumération.

### <a name="makevisible"></a>MakeVisible

Le [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit faire l’objet d’un défilement jusqu’à ce qu’il soit visible dans la vue :

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Cet exemple de code entraîne le défilement minimal requis pour faire défiler l’élément dans l’affichage.

> [!NOTE]
> Le [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membre est utilisé par défaut, si l' `position` argument n’est pas spécifié lors de l’appel de la `ScrollTo` méthode.

### <a name="start"></a>Démarrer

Le [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit défiler jusqu’au début de la vue :

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Cet exemple de code entraîne le défilement de l’élément jusqu’au début de la vue.

### <a name="center"></a>Center

Le [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit défiler jusqu’au centre de la vue :

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Cet exemple de code entraîne le défilement de l’élément au centre de la vue.

### <a name="end"></a>End

Le [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit défiler jusqu’à la fin de la vue :

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Cet exemple de code entraîne le défilement de l’élément à la fin de la vue.

## <a name="control-scroll-position-when-new-items-are-added"></a>Contrôler la position de défilement quand de nouveaux éléments sont ajoutés

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit une `ItemsUpdatingScrollMode` propriété, qui est stockée par une propriété pouvant être liée. Cette propriété obtient ou définit une `ItemsUpdatingScrollMode` valeur d’énumération qui représente le comportement de défilement de `CarouselView` quand de nouveaux éléments y sont ajoutés. L’énumération `ItemsUpdatingScrollMode` définit les membres suivants :

- `KeepItemsInView` conserve le premier élément de la liste affiché lors de l’ajout de nouveaux éléments.
- `KeepScrollOffset` garantit que la position de défilement actuelle est conservée lors de l’ajout de nouveaux éléments.
- `KeepLastItemInView` ajuste le décalage de défilement pour conserver le dernier élément de la liste affiché lorsque de nouveaux éléments sont ajoutés.

La valeur par défaut de la `ItemsUpdatingScrollMode` propriété est `KeepItemsInView` . Par conséquent, lorsque de nouveaux éléments sont ajoutés à un, [`CarouselView`](xref:Xamarin.Forms.CarouselView) le premier élément de la liste reste affiché. Pour vous assurer que le dernier élément de la liste est affiché lors de l’ajout de nouveaux éléments, affectez à la propriété la valeur `ItemsUpdatingScrollMode` `KeepLastItemInView` :

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilité de la barre de défilement

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` les propriétés et, qui sont sauvegardées par des propriétés pouvant être liées. Ces propriétés obtiennent ou définissent une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur d’énumération qui représente le moment où la barre de défilement horizontale ou verticale est visible. L’énumération `ScrollBarVisibility` définit les membres suivants :

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indique le comportement par défaut de la barre de défilement pour la plateforme, et est la valeur par défaut pour les `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` Propriétés et.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indique que les barres de défilement sont visibles, même lorsque le contenu s’ajuste à la vue.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indique que les barres de défilement ne sont pas visibles, même si le contenu ne tient pas dans la vue.

## <a name="snap-points"></a>Points d’ancrage

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. Cette fonctionnalité est appelée alignement, car les éléments sont alignés à la position lorsque le défilement s’arrête et sont contrôlés par les propriétés suivantes de la [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) classe :

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de type [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) , spécifie le comportement des points d’alignement lors du défilement.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de type [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) , spécifie la manière dont les points d’alignement sont alignés avec les éléments.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> Lorsque l’alignement se produit, il se produit dans la direction qui produit le moins de mouvement.

### <a name="snap-points-type"></a>Type des points d’alignement

L' [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) énumération définit les membres suivants :

- `None` indique que le défilement n’est pas aligné sur les éléments.
- `Mandatory` indique que le contenu s’aligne toujours sur le point d’alignement le plus proche jusqu’où le défilement s’arrête naturellement, le long de la direction d’inertie.
- `MandatorySingle` indique le même comportement que `Mandatory` , mais ne fait que faire défiler un élément à la fois.

Par défaut, sur un [`CarouselView`](xref:Xamarin.Forms.CarouselView) , la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriété a la valeur, ce qui `SnapPointsType.MandatorySingle` garantit que le défilement ne fait que faire défiler un élément à la fois.

Les captures d’écran suivantes montrent un [`CarouselView`](xref:Xamarin.Forms.CarouselView) avec l’alignement désactivé :

[![Capture d’écran d’un CarouselView sans points d’alignement, sur iOS et Android](scrolling-images/snappoints-none.png "CarouselView sans points d’alignement")](scrolling-images/snappoints-none-large.png#lightbox "CarouselView sans points d’alignement")

### <a name="snap-points-alignment"></a>Alignement des points d’alignement

L' [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) énumération définit les `Start` `Center` membres, et `End` .

> [!IMPORTANT]
> La valeur de la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriété est respectée uniquement lorsque la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriété a la valeur `Mandatory` , ou `MandatorySingle` .

#### <a name="start"></a>Démarrer

Le `SnapPointsAlignment.Start` membre indique que les points d’ancrage sont alignés sur le bord de tête des éléments. L’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement dans un défilement horizontal [`CarouselView`](xref:Xamarin.Forms.CarouselView) , l’élément de gauche est aligné sur la gauche de la vue :

[![Capture d’écran d’un CarouselView avec points d’ancrage de début, sur iOS et Android](scrolling-images/snappoints-start.png "CarouselView avec points d’ancrage de début")](scrolling-images/snappoints-start-large.png#lightbox "CarouselView avec points d’ancrage de début")

#### <a name="center"></a>Center

Le `SnapPointsAlignment.Center` membre indique que les points d’ancrage sont alignés sur le Centre des éléments.

Par défaut, sur un [`CarouselView`](xref:Xamarin.Forms.CarouselView) , la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriété a la valeur `Center` . Toutefois, à des fins d’exhaustivité, l’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement dans un défilement horizontal [`CarouselView`](xref:Xamarin.Forms.CarouselView) , l’élément central est aligné sur le centre de la vue :

[![Capture d’écran d’un CarouselView avec points d’ancrage centraux, sur iOS et Android](scrolling-images/snappoints-center.png "CarouselView avec points d’ancrage centraux")](scrolling-images/snappoints-center-large.png#lightbox "CarouselView avec points d’ancrage centraux")

#### <a name="end"></a>End

Le `SnapPointsAlignment.End` membre indique que les points d’ancrage sont alignés sur le bord de fin des éléments. L’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              PeekAreaInsets="100">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement dans un défilement horizontal [`CarouselView`](xref:Xamarin.Forms.CarouselView) , l’élément de droite est aligné sur la droite de la vue.

[![Capture d’écran d’un CarouselView avec des points d’ancrage de fin, sur iOS et Android](scrolling-images/snappoints-end.png "CarouselView avec points d’ancrage de fin")](scrolling-images/snappoints-end-large.png#lightbox "CarouselView avec points d’ancrage de fin")

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)
