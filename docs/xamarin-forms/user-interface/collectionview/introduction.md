---
title: Xamarin.Forms Présentation de CollectionView
description: Le CollectionView est une alternative flexible et performante à ListView.
ms.prod: xamarin
ms.assetid: 5C08F687-B9E6-4CE4-8726-F287F6D0B6A7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1ad9ef36471b14ad44b108189b94bb22d1b3db41
ms.sourcegitcommit: f2942b518f51317acbb263be5bc0c91e66239f50
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2020
ms.locfileid: "94590244"
---
# <a name="no-locxamarinforms-collectionview-introduction"></a>Xamarin.Forms Présentation de CollectionView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) est une vue pour la présentation de listes de données à l’aide de différentes spécifications de disposition. Il vise à fournir une alternative plus flexible et plus performante à [`ListView`](xref:Xamarin.Forms.ListView) . Par exemple, les captures d’écran suivantes montrent un `CollectionView` qui utilise une grille verticale à deux colonnes et qui permet une sélection multiple :

[![Capture d’écran d’une disposition de grille verticale CollectionView, sur iOS et Android](introduction-images/verticalgrid-multipleselection.png "Disposition de grille verticale CollectionView avec sélection multiple")](introduction-images/verticalgrid-multipleselection-large.png#lightbox "Disposition de grille verticale CollectionView avec sélection multiple")

[`CollectionView`](xref:Xamarin.Forms.CollectionView) doit être utilisé pour présenter des listes de données qui nécessitent un défilement ou une sélection. Une disposition pouvant être liée peut être utilisée lorsque les données à afficher ne nécessitent pas de défilement ou de sélection. Pour plus d’informations, consultez [dispositions pouvant être liées Xamarin.Forms dans ](~/xamarin-forms/user-interface/layouts/bindable-layouts.md).

[`CollectionView`](xref:Xamarin.Forms.CollectionView) est disponible à partir de Xamarin.Forms 4,3.

> [!IMPORTANT]
> [`CollectionView`](xref:Xamarin.Forms.CollectionView) est disponible sur iOS et Android, mais n’est [disponible que partiellement](https://gist.github.com/hartez/7d0edd4182dbc7de65cebc6c67f72e14) sur le plateforme Windows universelle.

## <a name="collectionview-and-listview-differences"></a>Différences entre CollectionView et ListView

Les [`CollectionView`](xref:Xamarin.Forms.CollectionView) API et [`ListView`](xref:Xamarin.Forms.ListView) sont similaires, mais il existe des différences notables :

- [`CollectionView`](xref:Xamarin.Forms.CollectionView) dispose d’un modèle de disposition flexible, qui permet de présenter les données verticalement ou horizontalement, dans une liste ou une grille.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge la sélection unique et multiple.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’a pas de concept de cellules. Au lieu de cela, un modèle de données est utilisé pour définir l’apparence de chaque élément de données de la liste.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilise automatiquement la virtualisation fournie par les contrôles natifs sous-jacents.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) réduit la surface de l’API de [`ListView`](xref:Xamarin.Forms.ListView) . De nombreuses propriétés et événements de [`ListView`](xref:Xamarin.Forms.ListView) ne sont pas présents dans `CollectionView` .
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’inclut pas de séparateurs intégrés.
- [`CollectionView`](xref:Xamarin.Forms.CollectionView) lèvera une exception si son [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) est mis à jour à partir du thread d’interface utilisateur.

## <a name="move-from-listview-to-collectionview"></a>Passer de ListView à CollectionView

[`ListView`](xref:Xamarin.Forms.ListView) les implémentations dans Xamarin.Forms les applications existantes peuvent être migrées vers des [`CollectionView`](xref:Xamarin.Forms.CollectionView) implémentations à l’aide du tableau suivant :

| Concept | API ListView | CollectionView |
|---|---|---|
| Données | `ItemsSource` | Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est rempli avec des données en définissant sa `ItemsSource` propriété. Pour plus d’informations, consultez [remplir un CollectionView avec des données](populate-data.md#populate-a-collectionview-with-data). |
| Apparence de l’élément | `ItemTemplate` | L’apparence de chaque élément dans un [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut être définie en affectant `ItemTemplate` à la propriété la valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) . Pour plus d’informations, consultez [définir l’apparence des éléments](populate-data.md#define-item-appearance). |
| Cellules | `TextCell`, `ImageCell`, `ViewCell` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’a pas de concept de cellules et, par conséquent, aucun concept d’indicateurs de divulgation. Au lieu de cela, un modèle de données est utilisé pour définir l’apparence de chaque élément de données de la liste. |
| Séparateurs de lignes | `SeparatorColor`, `SeparatorVisibility` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) n’inclut pas de séparateurs intégrés. Elles peuvent être fournies, si vous le souhaitez, dans le modèle d’élément. |
| Sélection | `SelectionMode`, `SelectedItem` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) prend en charge la sélection unique et multiple. Pour plus d’informations, consultez [ Xamarin.Forms CollectionView Selection](selection.md). |
| Hauteur de ligne | `HasUnevenRows`, `RowHeight` | Dans un `CollectionView` , la hauteur de ligne de chaque élément est déterminée par la `ItemSizingStrategy` propriété. Pour plus d’informations, consultez [dimensionnement](layout.md#item-sizing)d’un élément.|
| Mise en cache | `CachingStrategy` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) utilise automatiquement la virtualisation fournie par les contrôles natifs sous-jacents. |
| En-têtes et pieds de page | `Header`, `HeaderElement`, `HeaderTemplate`, `Footer`, `FooterElement`, `FooterTemplate` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) peut présenter un en-tête et un pied de page qui défilent avec les éléments de la liste, à l’aide des `Header` Propriétés,, `Footer` `HeaderTemplate` et `FooterTemplate` . Pour plus d’informations, consultez [en-têtes et pieds de page](layout.md#headers-and-footers). |
| Regroupement | `GroupDisplayBinding`, `GroupHeaderTemplate`, `GroupShortNameBinding`, `IsGroupingEnabled` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche les données regroupées correctement en affectant à sa propriété la valeur `IsGrouped` `true` . Les en-têtes de groupe et les pieds de page de groupe peuvent être personnalisés en affectant `GroupHeaderTemplate` `GroupFooterTemplate` aux propriétés et des  [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets. Pour plus d’informations, consultez [ Xamarin.Forms regroupement CollectionView](grouping.md). |
| Tirer pour actualiser | `IsPullToRefreshEnabled`, `IsRefreshing`, `RefreshAllowed`, `RefreshCommand`, `RefreshControlColor`, `BeginRefresh()`, `EndRefresh()` | La fonctionnalité d’extraction vers l’actualisation est prise en charge en définissant [`CollectionView`](xref:Xamarin.Forms.CollectionView) comme enfant d’un `RefreshView` . Pour plus d’informations, consultez [Extraire pour actualiser](populate-data.md#pull-to-refresh). |
| Éléments de menu contextuel | `ContextActions` | Les éléments de menu contextuel sont pris en charge en définissant un `SwipeView` comme affichage racine dans [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) qui définit l’apparence de chaque élément de données dans le [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Pour plus d’informations, consultez [menus contextuels](populate-data.md#context-menus). |
| Défilement | `ScrollTo()` | [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit `ScrollTo` des méthodes qui défilent les éléments dans la vue. Pour plus d’informations, consultez [défilement](scrolling.md). |

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-collectionviewdemos/)
- [Dispositions pouvant être liées dans Xamarin.Forms](~/xamarin-forms/user-interface/layouts/bindable-layouts.md)
