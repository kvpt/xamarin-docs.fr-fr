---
title: Introduction à Xamarin. Forms CollectionView
description: Le CollectionView est une vue flexible et performante pour la présentation de listes de données à l’aide de différentes spécifications de disposition.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 5832cb28162602a41d31026bc4a0ed54ac6bfb34
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888609"
---
# <a name="xamarinforms-collectionview-introduction"></a>Introduction à Xamarin. Forms CollectionView

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) est une vue de présentation de listes de données à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView). Par exemple, les captures d’écran suivantes `CollectionView` montrent un qui utilise une grille verticale à deux colonnes et qui permet une sélection multiple:

[ ![Capture d’écran d’une disposition de grille verticale CollectionView, sur iOS et Android](introduction-images/verticalgrid-multipleselection.png "CollectionView grille verticale avec sélection multiple") ] (introduction-images/verticalgrid-multipleselection-large.png#lightbox "Disposition de grille verticale CollectionView avec sélection multiple")

[`CollectionView`](xref:Xamarin.Forms.CollectionView)est disponible dans Xamarin. Forms 4,0. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante `AppDelegate` à votre classe sur iOS, ou `MainActivity` à votre classe sur Android, `Forms.Init`avant d’appeler:

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView)est disponible sur iOS et Android, mais n’est disponible que partiellement sur le plateforme Windows universelle.

## <a name="collectionview-and-listview-differences"></a>Différences entre CollectionView et ListView

Les [`CollectionView`](xref:Xamarin.Forms.CollectionView) API et [`ListView`](xref:Xamarin.Forms.ListView) sont similaires, mais il existe des différences notables:

- [`CollectionView`](xref:Xamarin.Forms.CollectionView)dispose d’un modèle de disposition flexible, qui permet de présenter les données verticalement ou horizontalement, dans une liste ou une grille.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)prend en charge la sélection unique et multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)n’a pas de concept de cellules. Au lieu de cela, un modèle de données est utilisé pour définir l’apparence de chaque élément de données de la liste.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)utilise automatiquement la virtualisation fournie par les contrôles natifs sous-jacents.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)réduit la surface de l' [`ListView`](xref:Xamarin.Forms.ListView)API de. De nombreuses propriétés et événements [`ListView`](xref:Xamarin.Forms.ListView) de ne sont pas `CollectionView`présents dans.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView)n’inclut pas de séparateurs intégrés.

## <a name="move-from-listview-to-collectionview"></a>Passer de ListView à CollectionView

[`ListView`](xref:Xamarin.Forms.ListView)les implémentations dans les implémentations de Xamarin. Forms existantes peuvent [`CollectionView`](xref:Xamarin.Forms.CollectionView) être migrées vers des implémentations à l’aide du tableau suivant:

| Concept | API ListView | CollectionView |
|---|---|---|
| Données | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est rempli avec des données en définissant sa `ItemsSource` propriété. Pour plus d’informations, consultez [remplir un CollectionView avec des données](populate-data.md#populate-a-collectionview-with-data). |
| Apparence de l’élément | `ItemTemplate` | L’apparence de chaque élément dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être définie en affectant `ItemTemplate` à [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)la propriété la valeur. Pour plus d’informations, consultez [définir l’apparence des éléments](populate-data.md#define-item-appearance). |
| Cellules | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)n’a pas de concept de cellules. Au lieu de cela, un modèle de données est utilisé pour définir l’apparence de chaque élément de données de la liste. |
| Séparateurs de lignes | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)n’inclut pas de séparateurs intégrés. Elles peuvent être fournies, si vous le souhaitez, dans le modèle d’élément. |
| Sélection | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)prend en charge la sélection unique et multiple. Pour plus d’informations, consultez [Xamarin. Forms CollectionView Selection](selection.md). |
| Hauteur de ligne | `HasUnevenRows`, `RowHeight` | Dans un `CollectionView`, la hauteur de ligne de chaque élément est déterminée par `ItemSizingStrategy` la propriété. Pour plus d’informations, consultez Dimensionnement d’un [élément](layout.md#item-sizing).|
| Mise en cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)utilise automatiquement la virtualisation fournie par les contrôles natifs sous-jacents. |
| En-têtes et pieds de page | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)peut présenter un en-tête et un pied de page qui défilent avec les éléments `Header`de la `HeaderTemplate`liste, `FooterTemplate` à l’aide des propriétés, `Footer`, et. Pour plus d’informations, consultez [en-têtes et pieds de page](layout.md#headers-and-footers). |
| Regroupement | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)affiche les données regroupées correctement en `IsGrouped` affectant `true`à sa propriété la valeur. Les `GroupHeaderTemplate` en-têtes de groupe et les pieds de page de groupe peuvent `GroupFooterTemplate` être personnalisés [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) en affectant aux propriétés et des objets. Pour plus d’informations, consultez [Xamarin. Forms CollectionView GROUPING](grouping.md). |
| Extraire pour actualiser | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | L’extraction vers l’actualisation n’est pas prise `CollectionView`en charge actuellement dans, mais sera ajoutée dans une version ultérieure. |
| Actions contextuelles | `ContextActions` | Les actions de contexte ne sont actuellement pas `CollectionView`prises en charge dans, mais seront ajoutées dans une version ultérieure. |
| Défilement | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView)définit `ScrollTo` des méthodes qui défilent les éléments dans la vue. Pour plus d’informations, consultez [défilement](scrolling.md). |

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
