---
title: Xamarin.Forms Présentation de CarouselView
description: CarouselView est une vue pour la présentation des données dans une disposition à défilement, où les utilisateurs peuvent faire glisser pour parcourir une collection d’éléments.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2aa33b0bd2e11d854f4f4dcfe03258a621301395
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939024"
---
# <a name="no-locxamarinforms-carouselview-introduction"></a>Xamarin.Forms Présentation de CarouselView

[`CarouselView`](xref:Xamarin.Forms.CarouselView) est une vue qui permet de présenter des données dans une disposition à défilement, où les utilisateurs peuvent effectuer un balayage pour se déplacer dans une collection d’éléments. Par défaut, `CarouselView` affiche ses éléments dans une orientation horizontale. Un seul élément s’affiche à l’écran, avec des mouvements de balayage qui entraînent la navigation vers l’avant et vers l’arrière dans la collection d’éléments. En outre, des indicateurs peuvent s’afficher pour représenter chaque élément dans le `CarouselView` :

[![Capture d’écran d’un CarouselView et d’un IndicatorView, sur iOS et Android](populate-data-images/indicators.png "Cercles IndicatorView")](populate-data-images/indicators-large.png#lightbox "Cercles IndicatorView")

Par défaut, [`CarouselView`](xref:Xamarin.Forms.CarouselView) fournit un accès en boucle à sa collection d’éléments. Par conséquent, le balayage vers l’arrière à partir du premier élément de la collection affiche le dernier élément de la collection. De même, le balayage par progression à partir du dernier élément de la collection retourne au premier élément de la collection.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) partage une grande partie de son implémentation avec [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Toutefois, les deux contrôles ont des cas d’usage différents. `CollectionView` est généralement utilisé pour présenter des listes de données de n’importe quelle longueur, tandis que `CarouselView` est utilisé en général pour mettre en évidence les informations dans une liste de longueur limitée.
