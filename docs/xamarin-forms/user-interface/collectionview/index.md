---
title: Xamarin. Forms CollectionView
description: Le CollectionView est une vue flexible et performante pour la présentation de listes de données à l’aide de différentes spécifications de disposition.
ms.prod: xamarin
ms.assetid: 2BC9B223-2D5C-4B09-849C-B9D578954557
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/24/2019
ms.openlocfilehash: b3841ed1287c980212ce37078f38f4984393c414
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69888595"
---
# <a name="xamarinforms-collectionview"></a>Xamarin. Forms CollectionView

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

Est [`CollectionView`](xref:Xamarin.Forms.CollectionView) une vue flexible et performante pour la présentation de listes de données à l’aide de différentes spécifications de disposition.

## <a name="datapopulate-datamd"></a>[Données](populate-data.md)

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est rempli avec des données en affectant à sa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété `IEnumerable`une collection qui implémente. L’apparence de chaque élément de la liste peut être définie en affectant [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à la propriété la valeur.

## <a name="layoutlayoutmd"></a>[Disposition](layout.md)

Par défaut, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche ses éléments dans une liste verticale. Toutefois, les listes et grilles verticales et horizontales peuvent être spécifiées.

## <a name="selectionselectionmd"></a>[Sélection](selection.md)

Par défaut, [`CollectionView`](xref:Xamarin.Forms.CollectionView) la sélection est désactivée. Toutefois, une sélection unique et une sélection multiple peuvent être activées.

## <a name="empty-viewsemptyviewmd"></a>[Affichages vides](emptyview.md)

Dans [`CollectionView`](xref:Xamarin.Forms.CollectionView), une vue vide peut être spécifiée pour fournir des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.

## <a name="scrollingscrollingmd"></a>[Défilement](scrolling.md)

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit deux [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes, qui défilent par programmation des éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue.

## <a name="groupinggroupingmd"></a>[Regroupement](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)peut afficher des données regroupées correctement en `IsGrouped` affectant `true`à sa propriété la valeur.
