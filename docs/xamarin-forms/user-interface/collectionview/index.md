---
title: Xamarin. Forms CollectionView
description: Le CollectionView est une vue flexible et performante pour la présentation de listes de données à l’aide de différentes spécifications de disposition.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: 8050d952556ce0b55a7ce72bc5f25de903fee6e5
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696999"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

La [`CollectionView`](xref:Xamarin.Forms.CollectionView) est une vue flexible et performante pour la présentation de listes de données à l’aide de différentes spécifications de disposition.

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

Une [`CollectionView`](xref:Xamarin.Forms.CollectionView) est remplie avec des données en définissant sa propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sur n’importe quelle collection qui implémente `IEnumerable`. L’apparence de chaque élément de la liste peut être définie en affectant à la propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) la valeur d’une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Disposition](layout.md)

Par défaut, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) affichera ses éléments dans une liste verticale. Toutefois, les listes et grilles verticales et horizontales peuvent être spécifiées.

## <a name="selectionselectionmd"></a>[Sélection](selection.md)

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) sélection est désactivée. Toutefois, une sélection unique et une sélection multiple peuvent être activées.

## <a name="empty-viewsemptyviewmd"></a>[Affichages vides](emptyview.md)

Dans [`CollectionView`](xref:Xamarin.Forms.CollectionView), il est possible de spécifier une vue vide qui fournit des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.

## <a name="scrollingscrollingmd"></a>[Défilement](scrolling.md)

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit deux méthodes de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , qui défilent par programmation les éléments dans l’affichage. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue.

## <a name="groupinggroupingmd"></a>[Regroupement](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView) pouvez afficher des données regroupées correctement en affectant à sa propriété `IsGrouped` la valeur `true`.
