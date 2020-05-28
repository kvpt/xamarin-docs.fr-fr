---
title: ''
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
ms.openlocfilehash: c420fe65b020067169230dd06dbcd5ce65c036ab
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128620"
---
# <a name="swipeview-swipe-transition-mode-on-android"></a>Mode de transition par balayage SwipeView sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme Android contrôle la transition utilisée lors de l’ouverture d’un `SwipeView` . Il est consommé en XAML en affectant `SwipeView.SwipeTransitionMode` à la propriété pouvant être liée la valeur de l' `SwipeTransitionMode` énumération :

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

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<Android>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

La `SwipeView.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `SwipeView.SetSwipeTransitionMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler la transition utilisée lors de l’ouverture d’un `SwipeView` . L' `SwipeTransitionMode` énumération fournit deux valeurs possibles :

- `Reveal`indique que les éléments de balayage seront révélés à mesure que le `SwipeView` contenu est balayé et qu’il s’agit de la valeur par défaut de la `SwipeView.SwipeTransitionMode` propriété.
- `Drag`indique que les éléments de balayage sont glissés dans l’affichage à mesure que le `SwipeView` contenu est déplacé.

En outre, la `SwipeView.GetSwipeTransitionMode` méthode peut être utilisée pour retourner le `SwipeTransitionMode` appliqué au `SwipeView` .

Le résultat est qu’une `SwipeTransitionMode` valeur spécifiée est appliquée au `SwipeView` , qui contrôle la transition utilisée lors de l’ouverture de `SwipeView` :

[![Capture d’écran de SwipeView SwipeTransitionModes, sur Android](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes sur Android")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes sur Android")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
