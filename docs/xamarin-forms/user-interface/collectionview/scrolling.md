---
title: Xamarin.Forms Défilement CollectionView
description: Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, CollectionView définit deux méthodes ScrollTo, qui défilent par programmation des éléments dans la vue.
ms.prod: xamarin
ms.assetid: 2ED719AF-33D2-434D-949A-B70B479C9BA5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/17/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1872f86c0e7be6ab07b4e962d17be7d8030c1d96
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373157"
---
# <a name="no-locxamarinforms-collectionview-scrolling"></a>Xamarin.Forms Défilement CollectionView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit deux [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes qui défilent les éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue. Les deux surcharges ont des arguments supplémentaires qui peuvent être spécifiés pour indiquer le groupe auquel appartient l’élément, la position exacte de l’élément après la fin du défilement et s’il faut animer le défilement.

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit un [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) événement qui est déclenché lorsque l’une des [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes est appelée. L' [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) objet qui accompagne l' `ScrollToRequested` événement a de nombreuses propriétés, notamment `IsAnimated` , `Index` , `Item` et `ScrollToPosition` . Ces propriétés sont définies à partir des arguments spécifiés dans les `ScrollTo` appels de méthode.

En outre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit un `Scrolled` événement qui est déclenché pour indiquer que le défilement s’est produit. L' `ItemsViewScrolledEventArgs` objet qui accompagne l' `Scrolled` événement a de nombreuses propriétés. Pour plus d’informations, consultez [détecter le défilement](#detect-scrolling).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit également une `ItemsUpdatingScrollMode` propriété qui représente le comportement de défilement de `CollectionView` lorsque de nouveaux éléments y sont ajoutés. Pour plus d’informations sur cette propriété, consultez [contrôler la position de défilement quand de nouveaux éléments sont ajoutés](#control-scroll-position-when-new-items-are-added).

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. Cette fonctionnalité est appelée alignement, car les éléments sont alignés à la position lorsque le défilement s’arrête. Pour plus d’informations, consultez [points d’alignement](#snap-points).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) peut également charger des données de façon incrémentielle au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Détecter le défilement

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit un `Scrolled` événement qui est déclenché pour indiquer que le défilement s’est produit. L’exemple de code XAML suivant montre un `CollectionView` qui définit un gestionnaire d’événements pour l' `Scrolled` événement :

```xaml
<CollectionView Scrolled="OnCollectionViewScrolled">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView();
collectionView.Scrolled += OnCollectionViewScrolled;
```

Dans cet exemple de code, le `OnCollectionViewScrolled` Gestionnaire d’événements est exécuté lorsque l' `Scrolled` événement se déclenche :

```csharp
void OnCollectionViewScrolled(object sender, ItemsViewScrolledEventArgs e)
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

Dans cet exemple, le `OnCollectionViewScrolled` Gestionnaire d’événements génère les valeurs de l' `ItemsViewScrolledEventArgs` objet qui accompagne l’événement.

> [!IMPORTANT]
> L' `Scrolled` événement est déclenché pour les défilement initiés par l’utilisateur et pour les défilement par programmation.

## <a name="scroll-an-item-at-an-index-into-view"></a>Faire défiler un élément d’un index dans la vue

La première [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) surcharge de méthode fait défiler l’élément à l’index spécifié dans la vue. À partir d’un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objet nommé `collectionView` , l’exemple suivant montre comment faire défiler l’élément à l’index 12 dans la vue :

```csharp
collectionView.ScrollTo(12);
```

Un élément de données groupées peut également faire l’objet d’un défilement dans la vue en spécifiant les index d’élément et de groupe. L’exemple suivant montre comment faire défiler le troisième élément du deuxième groupe dans la vue :

```csharp
// Items and groups are indexed from zero.
collectionView.ScrollTo(2, 1);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) événement est déclenché lorsque la [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthode est appelée.

## <a name="scroll-an-item-into-view"></a>Faire défiler un élément dans l’affichage

La deuxième [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) surcharge de méthode fait défiler l’élément spécifié dans la vue. À partir d’un [`CollectionView`](xref:Xamarin.Forms.CollectionView) objet nommé `collectionView` , l’exemple suivant montre comment faire défiler l’élément de singe Proboscis dans la vue :

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey);
```

Un élément de données groupées peut également faire l’objet d’un défilement dans la vue en spécifiant l’élément et le groupe. L’exemple suivant montre comment faire défiler l’élément de singe Proboscis dans le groupe singes dans la vue :

```csharp
GroupedAnimalsViewModel viewModel = BindingContext as GroupedAnimalsViewModel;
AnimalGroup group = viewModel.Animals.FirstOrDefault(a => a.Name == "Monkeys");
Animal monkey = group.FirstOrDefault(m => m.Name == "Proboscis Monkey");
collectionView.ScrollTo(monkey, group);
```

> [!NOTE]
> L' [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) événement est déclenché lorsque la [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthode est appelée.

## <a name="disable-scroll-animation"></a>Désactiver l’animation de défilement

Une animation de défilement s’affiche lorsque vous faites défiler un élément dans l’affichage. Toutefois, cette animation peut être désactivée en affectant `animate` à l’argument de la méthode la valeur `ScrollTo` `false` :

```csharp
collectionView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Position de défilement du contrôle

Lorsque vous faites défiler un élément dans l’affichage, la position exacte de l’élément après la fin du défilement peut être spécifiée avec l' `position` argument des [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes. Cet argument accepte un [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) membre de l’énumération.

### <a name="makevisible"></a>MakeVisible

Le [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit faire l’objet d’un défilement jusqu’à ce qu’il soit visible dans la vue :

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Cet exemple de code entraîne le défilement minimal requis pour faire défiler l’élément dans l’affichage :

[![Capture d’écran d’une liste verticale CollectionView avec un élément défilé dans l’affichage, sur iOS et Android](scrolling-images/scrolltoposition-makevisible.png "CollectionView liste verticale avec élément défilé")](scrolling-images/scrolltoposition-makevisible-large.png#lightbox "CollectionView liste verticale avec élément défilé")

> [!NOTE]
> Le [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) membre est utilisé par défaut, si l' `position` argument n’est pas spécifié lors de l’appel de la `ScrollTo` méthode.

### <a name="start"></a>Démarrer

Le [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit défiler jusqu’au début de la vue :

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Cet exemple de code entraîne le défilement de l’élément jusqu’au début de la vue :

[![Capture d’écran d’une liste verticale CollectionView avec un élément défilé dans l’affichage, sur iOS et Android](scrolling-images/scrolltoposition-start.png "CollectionView liste verticale avec élément défilé")](scrolling-images/scrolltoposition-start-large.png#lightbox "CollectionView liste verticale avec élément défilé")

### <a name="center"></a>Center

Le [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit défiler jusqu’au centre de la vue :

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Cet exemple de code entraîne le défilement de l’élément au centre de la vue :

[![Capture d’écran d’une liste verticale CollectionView avec un élément défilé dans l’affichage, sur iOS et Android](scrolling-images/scrolltoposition-center.png "CollectionView liste verticale avec élément défilé")](scrolling-images/scrolltoposition-center-large.png#lightbox "CollectionView liste verticale avec élément défilé")

### <a name="end"></a>End

Le [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) membre indique que l’élément doit défiler jusqu’à la fin de la vue :

```csharp
collectionView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Cet exemple de code entraîne le défilement de l’élément à la fin de la vue :

[![Capture d’écran d’une liste verticale CollectionView avec un élément défilé dans l’affichage, sur iOS et Android](scrolling-images/scrolltoposition-end.png "CollectionView liste verticale avec élément défilé")](scrolling-images/scrolltoposition-end-large.png#lightbox "CollectionView liste verticale avec élément défilé")

## <a name="control-scroll-position-when-new-items-are-added"></a>Contrôler la position de défilement quand de nouveaux éléments sont ajoutés

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit une `ItemsUpdatingScrollMode` propriété, qui est stockée par une propriété pouvant être liée. Cette propriété obtient ou définit une `ItemsUpdatingScrollMode` valeur d’énumération qui représente le comportement de défilement de `CollectionView` quand de nouveaux éléments y sont ajoutés. L’énumération `ItemsUpdatingScrollMode` définit les membres suivants :

- `KeepItemsInView` ajuste le décalage de défilement pour maintenir le premier élément visible affiché lorsque de nouveaux éléments sont ajoutés.
- `KeepScrollOffset` conserve le décalage de défilement par rapport au début de la liste lors de l’ajout de nouveaux éléments.
- `KeepLastItemInView` ajuste le décalage de défilement pour maintenir le dernier élément visible quand de nouveaux éléments sont ajoutés.

La valeur par défaut de la `ItemsUpdatingScrollMode` propriété est `KeepItemsInView` . Par conséquent, lorsque de nouveaux éléments sont ajoutés à un, [`CollectionView`](xref:Xamarin.Forms.CollectionView) le premier élément visible dans la liste reste affiché. Pour vous assurer que les éléments récemment ajoutés sont toujours visibles au bas de la liste, la `ItemsUpdatingScrollMode` propriété doit être définie sur `KeepLastItemInView` :

```xaml
<CollectionView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilité de la barre de défilement

[`CollectionView`](xref:Xamarin.Forms.CollectionView) définit `HorizontalScrollBarVisibility` `VerticalScrollBarVisibility` les propriétés et, qui sont sauvegardées par des propriétés pouvant être liées. Ces propriétés obtiennent ou définissent une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur d’énumération qui représente le moment où la barre de défilement horizontale ou verticale est visible. L’énumération `ScrollBarVisibility` définit les membres suivants :

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

Par défaut, la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriété a la valeur, ce qui permet de s' `SnapPointsType.None` assurer que le défilement n’aligne pas les éléments, comme illustré dans les captures d’écran suivantes :

[![Capture d’écran d’une liste verticale CollectionView sans points d’alignement, sur iOS et Android](scrolling-images/snappoints-none.png "CollectionView liste verticale sans points d’alignement")](scrolling-images/snappoints-none-large.png#lightbox "CollectionView liste verticale sans points d’alignement")

### <a name="snap-points-alignment"></a>Alignement des points d’alignement

L' [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) énumération définit les `Start` `Center` membres, et `End` .

> [!IMPORTANT]
> La valeur de la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriété est respectée uniquement lorsque la [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) propriété a la valeur `Mandatory` , ou `MandatorySingle` .

#### <a name="start"></a>Démarrer

Le `SnapPointsAlignment.Start` membre indique que les points d’ancrage sont alignés sur le bord de tête des éléments.

Par défaut, la [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) propriété a la valeur `SnapPointsAlignment.Start` . Toutefois, à des fins d’exhaustivité, l’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement, l’élément supérieur est aligné avec le haut de la vue :

[![Capture d’écran d’une liste verticale CollectionView avec des points d’ancrage de début, sur iOS et Android](scrolling-images/snappoints-start.png "Liste verticale CollectionView avec points d’ancrage de début")](scrolling-images/snappoints-start-large.png#lightbox "Liste verticale CollectionView avec points d’ancrage de début")

#### <a name="center"></a>Center

Le `SnapPointsAlignment.Center` membre indique que les points d’ancrage sont alignés sur le Centre des éléments. L’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, l’élément supérieur est centré en haut de la vue :

[![Capture d’écran d’une liste verticale CollectionView avec des points d’ancrage centraux, sur iOS et Android](scrolling-images/snappoints-center.png "Liste verticale CollectionView avec points d’ancrage centraux")](scrolling-images/snappoints-center-large.png#lightbox "Liste verticale CollectionView avec points d’ancrage centraux")

#### <a name="end"></a>End

Le `SnapPointsAlignment.End` membre indique que les points d’ancrage sont alignés sur le bord de fin des éléments. L’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CollectionView ItemsSource="{Binding Monkeys}">
    <CollectionView.ItemsLayout>
        <LinearItemsLayout Orientation="Vertical"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CollectionView.ItemsLayout>
    ...
</CollectionView>
```

Le code C# équivalent est :

```csharp
CollectionView collectionView = new CollectionView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Vertical)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement, l’élément inférieur est aligné sur le bas de la vue :

[![Capture d’écran d’une liste verticale CollectionView avec des points d’ancrage de fin, sur iOS et Android](scrolling-images/snappoints-end.png "CollectionView liste verticale avec points d’ancrage de fin")](scrolling-images/snappoints-end-large.png#lightbox "CollectionView liste verticale avec points d’ancrage de fin")

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)