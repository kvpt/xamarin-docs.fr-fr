---
title: Introduction à Xamarin. Forms CarouselView
description: Le CarouselView est une vue pour la présentation de données dans une mise en page de type carrousel.
ms.prod: xamarin
ms.assetid: 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 7979f6ed362c580d9cf80f19b3bc0ea7550ca70c
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985970"
---
# <a name="xamarinforms-carouselview-introduction"></a>Introduction à Xamarin. Forms CarouselView

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)est une vue pour la présentation d’informations d’une manière similaire à un carrousel.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)est disponible dans Xamarin. Forms 4,3. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante `AppDelegate` à votre classe sur iOS, ou `MainActivity` à votre classe sur Android, `Forms.Init`avant d’appeler :

```csharp
Forms.SetFlags("CollectionView_Experimental");
```

_Remarque : À la rédaction de cet article, le CarouselView utilise toujours l’indicateur CollectionView pour activer ses fonctionnalités._

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)est disponible sur iOS et Android, mais certaines fonctionnalités peuvent uniquement être partiellement disponibles sur le plateforme Windows universelle.

## <a name="when-to-use-carouselview"></a>Quand utiliser CarouselView

Tandis que CarouselView base la majeure partie de son implémentation de CollectionView, son objectif est plus concentré. Bien que l’utilisation de CollectionView et de CarouselView soit à votre discrétion, les carrousels dans les applications sont généralement utilisés pour mettre en évidence les informations et sont limités dans le nombre total d’éléments utilisés.
