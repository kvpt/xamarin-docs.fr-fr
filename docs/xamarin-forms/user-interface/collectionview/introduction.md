---
title: Présentation de Xamarin.Forms CollectionView
description: Le CollectionView est une vue flexible et plus performant pour la présentation des listes de données à l’aide des spécifications de mise en page différente.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: 456c83808ff685a8c2199cf80c96d63b9334675e
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512733"
---
# <a name="xamarinforms-collectionview-introduction"></a>Présentation de Xamarin.Forms CollectionView

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) est une vue pour la présentation des listes de données à l’aide de spécifications de mise en page différente. Il vise à fournir la plus flexible et performante devoir [ `ListView` ](xref:Xamarin.Forms.ListView). Par exemple, les captures d’écran suivantes montrent un `CollectionView` qui utilise un quadrillage vertical de deux colonnes, et qui permet la sélection multiple :

[![Capture d’écran d’une disposition de grille verticale CollectionView, sur iOS et Android](introduction-images/verticalgrid-multipleselection.png "mise en page de grille verticale CollectionView avec la sélection multiple")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "mise en page de grille verticale CollectionView avec sélection multiple")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) est disponible dans Xamarin.Forms 4.0. Toutefois, il est actuellement en phase expérimentale et peut uniquement être utilisé en ajoutant la ligne suivante de code à votre `AppDelegate` classe sur iOS ou à votre `MainActivity` classe sur Android, avant d’appeler `Forms.Init`:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) est disponible sur iOS et Android, mais est partiellement disponible uniquement sur la plateforme Windows universelle.

## <a name="collectionview-and-listview-differences"></a>Différences CollectionView et ListView

Bien que le [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) et [ `ListView` ](xref:Xamarin.Forms.ListView) API sont similaires, il existe des différences notables :

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) a un modèle de disposition flexible qui permet d’être présentés verticalement ou horizontalement, dans une liste ou d’une grille de données.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge unique et la sélection multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’a aucun concept de cellules. Au lieu de cela, un modèle de données est utilisé pour définir l’apparence de chaque élément de données dans la liste.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilise automatiquement la virtualisation fournie par les contrôles natifs sous-jacent.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) réduit la surface d’API de [ `ListView` ](xref:Xamarin.Forms.ListView). De nombreuses propriétés et événements de [ `ListView` ](xref:Xamarin.Forms.ListView) ne sont pas présents dans `CollectionView`.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’inclut pas de séparateurs intégrés.

## <a name="move-from-listview-to-collectionview"></a>Déplacer à partir de ListView pour CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) les implémentations dans les implémentations de Xamarin.Forms existantes peuvent être migrées vers [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) mises en œuvre à l’aide du tableau suivant :

| Concept | API de ListView | CollectionView |
|---|---|---|
| Données | `ItemsSource` | Un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) est rempli avec des données en définissant son `ItemsSource` propriété. Pour plus d’informations, consultez [remplir un CollectionView de données](populate-data.md#populate-a-collectionview-with-data). |
| Apparence de l’élément | `ItemTemplate` | L’apparence de chaque élément dans un [ `CollectionView` ](xref:Xamarin.Forms.CollectionView) peut être définie en configurant le `ItemTemplate` propriété à un [ `DataTemplate` ](xref:Xamarin.Forms.DataTemplate). Pour plus d’informations, consultez [définissent l’apparence de l’élément](populate-data.md#define-item-appearance). |
| Cellules | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’a aucun concept de cellules. Au lieu de cela, un modèle de données est utilisé pour définir l’apparence de chaque élément de données dans la liste. |
| Séparateurs de lignes | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’inclut pas de séparateurs intégrés. Il peuvent être fournis si vous le souhaitez, dans le modèle d’élément. |
| Sélection | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge unique et la sélection multiple. Pour plus d’informations, consultez [Xamarin.Forms CollectionView sélection](selection.md). |
| Hauteur de ligne | `HasUnevenRows`, `RowHeight` | Dans un `CollectionView`, la hauteur de ligne de chaque élément est déterminée par le `ItemSizingStrategy` propriété. Pour plus d’informations, consultez [élément dimensionnement](layout.md#item-sizing).|
| Mise en cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilise automatiquement la virtualisation fournie par les contrôles natifs sous-jacent. |
| En-têtes et pieds de page | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | En-têtes et pieds de page sont actuellement pas pris en charge dans `CollectionView`, mais sera ajoutée dans une version ultérieure.|
| Regroupement | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | Regroupement n’est actuellement pas pris en charge dans `CollectionView`, mais sera ajoutée dans une version ultérieure. |
| Pour actualiser | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | Pour actualiser n’est actuellement pas pris en charge dans `CollectionView`, mais sera ajoutée dans une version ultérieure. |
| Actions contextuelles | `ContextActions` | Actions contextuelles sont actuellement pas pris en charge dans `CollectionView`, mais sera ajoutée dans une version ultérieure. |
| Défilement | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit `ScrollTo` méthodes, que faire défiler les éléments dans la vue. Pour plus d’informations, consultez [défilement](scrolling.md). |

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CollectionViewDemos/)
