---
title: Xamarin.FormsCarouselView
description: Le CarouselView est une vue qui permet de présenter des données dans une disposition à défilement, où les utilisateurs peuvent faire glisser pour parcourir une collection d’éléments.
ms.prod: xamarin
ms.assetid: 5b673347-cdba-4532-820f-fb5f070c86bc
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/08/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2ff88431869c4a14919fb13e5059cd11fcdbdc3c
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87918639"
---
# <a name="no-locxamarinforms-carouselview"></a>Xamarin.FormsCarouselView

![API de la version préliminaire](~/media/shared/preview.png)

## <a name="introduction"></a>[Introduction](introduction.md)

[`CarouselView`](xref:Xamarin.Forms.CarouselView)Est une vue pour la présentation des données dans une disposition à défilement, où les utilisateurs peuvent effectuer un balayage pour se déplacer dans une collection d’éléments.

## <a name="data"></a>[Données](populate-data.md)

Un [`CarouselView`](xref:Xamarin.Forms.CarouselView) est rempli avec des données en affectant [`ItemsSource`](xref:Xamarin.Forms.ItemsView.ItemsSource) à sa propriété une collection qui implémente `IEnumerable` . L’apparence de chaque élément peut être définie en affectant [`ItemTemplate`](xref:Xamarin.Forms.ItemsView.ItemTemplate) à la propriété la valeur [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) .

## <a name="layout"></a>[Disposition](layout.md)

Par défaut, un [`CarouselView`](xref:Xamarin.Forms.CarouselView) affiche ses éléments dans une liste horizontale. Toutefois, il a également accès aux mêmes dispositions que CollectionView, y compris une orientation verticale.

## <a name="interaction"></a>[Interaction](interaction.md)

L’élément actuellement affiché dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView) est accessible par le biais `CurrentItem` des `Position` Propriétés et.

## <a name="empty-views"></a>[Affichages vides](emptyview.md)

Dans [`CarouselView`](xref:Xamarin.Forms.CarouselView) , une vue vide peut être spécifiée pour fournir des commentaires à l’utilisateur quand aucune donnée n’est disponible pour l’affichage. La vue vide peut être une chaîne, une vue ou plusieurs vues.

## <a name="scrolling"></a>[Défilement](scrolling.md)

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit deux [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) méthodes, qui défilent par programmation des éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue.
