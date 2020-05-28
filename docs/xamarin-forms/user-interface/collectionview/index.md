---
title: Xamarin.FormsCollectionView
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a2c9fd9e6e48192bc2237d6b451b533fcee6e6ed
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136446"
---
# <a name="xamarinforms-collectionview"></a>Xamarin.FormsCollectionView

## <a name="introduction"></a>[Introduction](introduction.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)Est une vue flexible et performante pour la présentation de listes de données à l’aide de différentes spécifications de disposition.

## <a name="data"></a>[Données](populate-data.md)

Un [`CollectionView`](xref:Xamarin.Forms.CollectionView) est rempli avec des données en affectant [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) à sa propriété une collection qui implémente `IEnumerable` . L’apparence de chaque élément de la liste peut être définie en affectant [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) à la propriété la valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Disposition](layout.md)

Par défaut, un [`CollectionView`](xref:Xamarin.Forms.CollectionView) affiche ses éléments dans une liste verticale. Toutefois, les listes et grilles verticales et horizontales peuvent être spécifiées.

## <a name="selection"></a>[Sélection](selection.md)

Par défaut, la [`CollectionView`](xref:Xamarin.Forms.CollectionView) sélection est désactivée. Toutefois, une sélection unique et une sélection multiple peuvent être activées.

## <a name="empty-views"></a>[Affichages vides](emptyview.md)

Dans [`CollectionView`](xref:Xamarin.Forms.CollectionView) , une vue vide peut être spécifiée pour fournir des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.

## <a name="scrolling"></a>[Défilement](scrolling.md)

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, [`CollectionView`](xref:Xamarin.Forms.CollectionView) définit deux [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes, qui défilent par programmation des éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue.

## <a name="grouping"></a>[Regroupement](grouping.md)

[`CollectionView`](xref:Xamarin.Forms.CollectionView)peut afficher des données regroupées correctement en affectant à sa propriété la valeur `IsGrouped` `true` .
