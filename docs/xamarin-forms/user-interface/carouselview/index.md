---
title: Xamarin. Forms CarouselView
description: Le CarouselView est une vue qui permet de présenter des données dans une disposition à défilement, où les utilisateurs peuvent faire glisser pour parcourir une collection d’éléments.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
ms.openlocfilehash: 816c1b6e4ab497d0ada0f80fa3ad4800912587c3
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696985"
---
# <a name="xamarinforms-carouselview"></a>Xamarin. Forms CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

## <a name="introductionintroductionmd"></a>[Introduction](introduction.md)

La [`CarouselView`](xref:Xamarin.Forms.CarouselView) est une vue pour la présentation des données dans une disposition à défilement, où les utilisateurs peuvent effectuer un balayage pour se déplacer dans une collection d’éléments.

## <a name="datapopulate-datamd"></a>[Data](populate-data.md)

Une [`CarouselView`](xref:Xamarin.Forms.CarouselView) est remplie avec des données en définissant sa propriété [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) sur n’importe quelle collection qui implémente `IEnumerable`. L’apparence de chaque élément peut être définie en affectant à la propriété [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) la valeur d’une [`DataTemplate`](xref:Xamarin.Forms.DataTemplate).

## <a name="layoutlayoutmd"></a>[Disposition](layout.md)

Par défaut, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) affichera ses éléments dans une liste horizontale. Toutefois, il a également accès aux mêmes dispositions que CollectionView, y compris une orientation verticale.

## <a name="interactioninteractionmd"></a>[Interaction](interaction.md)

L’élément actuellement affiché dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView) est accessible par le biais des propriétés `CurrentItem` et `Position`.

## <a name="empty-viewsemptyviewmd"></a>[Affichages vides](emptyview.md)

Dans [`CarouselView`](xref:Xamarin.Forms.CarouselView), il est possible de spécifier une vue vide qui fournit des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.

## <a name="scrollingscrollingmd"></a>[Défilement](scrolling.md)

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit deux méthodes de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , qui défilent par programmation les éléments dans l’affichage. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue.
