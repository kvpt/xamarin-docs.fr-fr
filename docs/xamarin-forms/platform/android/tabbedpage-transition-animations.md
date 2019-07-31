---
title: Animations de transition de page TabbedPage sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme Android qui désactive les animations de transition lors de la navigation dans les pages d’un TabbedPage.
ms.prod: xamarin
ms.assetid: 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4f8d6ec2b06855364970bc9b672c3d3f7b9bfdfc
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68649844"
---
# <a name="tabbedpage-page-transition-animations-on-android"></a>Animations de transition de page TabbedPage sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour désactiver les animations de transition lors de la navigation dans les pages, par programmation ou à l’aide de la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage)barre d’onglets dans un. Elle est consommée dans XAML en définissant le `TabbedPage.IsSmoothScrollEnabled` propriété pouvant être liée `false`:

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

Le `TabbedPage.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. Le `TabbedPage.SetIsSmoothScrollEnabled` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour contrôler si les animations de transition seront affichera lors de la navigation entre les pages dans un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage). En outre, le `TabbedPage` classe dans le `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espace de noms comporte également les méthodes suivantes :

- `IsSmoothScrollEnabled`, qui est utilisée pour récupérer si les animations de transition seront affichera lors de la navigation entre les pages dans un `TabbedPage`.
- `EnableSmoothScroll`, qui est utilisée pour activer les animations de transition lors de la navigation entre les pages dans un `TabbedPage`.
- `DisableSmoothScroll`, qui est utilisé pour désactiver les animations de transition lors de la navigation entre les pages dans un `TabbedPage`.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
