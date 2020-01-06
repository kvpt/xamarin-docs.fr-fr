---
title: Mode de transition par balayage SwipeView sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme Android qui contrôle la transition utilisée lors de l’ouverture d’un SwipeView.
ms.prod: xamarin
ms.assetid: 6B1F8213-9D62-4C40-9F04-881F1371B5AA
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 077d4a8a9530bf074fde710dd08c1fbea49668ef
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75490352"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>Mode de transition par balayage SwipeView sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme Android contrôle la transition utilisée lors de l’ouverture d’un `SwipeView`. Il est consommé en XAML en affectant à la `SwipeView.SwipeTransitionMode` propriété pouvant être liée la valeur de l’énumération `SwipeTransitionMode` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core" >
    <StackLayout>
        <SwipeView android:SwipeView.SwipeTransitionMode="Drag">
            <SwipeView.LeftItems>
                <SwipeItems>
                    <SwipeItem Text="Delete"
                               IconImageSource="delete.png"
                               BackgroundColor="LightPink"
                               Invoked="OnDeleteSwipeItemInvoked" />
                </SwipeItems>
            </SwipeView.LeftItems>
            <!-- Content -->
        </SwipeView>
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

Le `SwipeView.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. La méthode `SwipeView.SetSwipeTransitionMode`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , est utilisée pour contrôler la transition utilisée lors de l’ouverture d’un `SwipeView`. L’énumération `SwipeTransitionMode` fournit deux valeurs possibles :

- `Reveal` indique que les éléments de balayage seront révélés à mesure que le contenu de la `SwipeView` est reglissé et qu’il s’agit de la valeur par défaut de la propriété `SwipeView.SwipeTransitionMode`.
- `Drag` indique que les éléments de balayage seront déplacés dans la vue au fur et à mesure que le contenu `SwipeView` est déplacé.

En outre, la méthode `SwipeView.GetSwipeTransitionMode` peut être utilisée pour retourner le `SwipeTransitionMode` appliqué au `SwipeView`.

Le résultat est qu’une valeur de `SwipeTransitionMode` spécifiée est appliquée au `SwipeView`, qui contrôle la transition utilisée lors de l’ouverture de la `SwipeView`:

[![Capture d’écran de SwipeView SwipeTransitionModes, sur Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes sur Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes sur Android")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
