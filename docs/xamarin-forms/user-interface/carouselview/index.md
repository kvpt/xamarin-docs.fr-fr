---
title: Xamarin. Forms CarouselView
description: Le CarouselView est une vue pour la présentation de listes de données dans une mise en page de type carrousel.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 06d20341053c4f77cb72aac9e1abdc85d9f95fdb
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908267"
---
# <a name="xamarinforms-carouselview"></a>Xamarin. Forms CarouselView

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

> [!IMPORTANT]
> La documentation CarouselView est en cours de développement et certains liens peuvent faire référence à la documentation CollectionView. Dans la plupart des cas, les informations doivent être applicables en raison de la nature de CarouselView basée sur CollectionView.

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

Est [`CarouselView`](xref:Xamarin.Forms.CarouselView) une vue pour la présentation de données dans une mise en page de type carrousel. Son implémentation est basée sur celle de [`CollectionView`](xref:Xamarin.Forms.CollectionView).

## <a name="datacollectionviewpopulate-datamd"></a>[Données](../collectionview/populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) est rempli avec des données en affectant à sa [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) propriété `IEnumerable`une collection qui implémente. L’apparence de chaque élément de la liste peut être définie en affectant [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)à la propriété la valeur.

## <a name="layoutlayoutmd"></a>[Disposition](layout.md)

Par défaut, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche ses éléments dans une liste horizontale. Toutefois, il a également accès aux mêmes dispositions que CollectionView, y compris une orientation verticale.

## <a name="empty-viewscollectionviewemptyviewmd"></a>[Affichages vides](../collectionview/emptyview.md)

Dans [`CarouselView`](xref:Xamarin.Forms.CarouselView), une vue vide peut être spécifiée pour fournir des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.

## <a name="scrollingcollectionviewscrollingmd"></a>[Défilement](../collectionview/scrolling.md)

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit deux [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes, qui défilent par programmation des éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue.
