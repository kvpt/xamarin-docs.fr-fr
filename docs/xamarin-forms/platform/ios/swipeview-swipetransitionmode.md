---
title : "mode de transition par balayage SwipeView sur iOS" Description : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle la transition utilisée lors de l’ouverture d’un SwipeView.
ms. Prod : xamarin ms. AssetID : C667F24C-BAD8-47E0-9285-D3546BEF703B ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 12/11/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="swipeview-swipe-transition-mode-on-ios"></a>Mode de transition par balayage SwipeView sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS contrôle la transition utilisée lors de l’ouverture d’un `SwipeView` . Il est consommé en XAML en affectant `SwipeView.SwipeTransitionMode` à la propriété pouvant être liée la valeur de l' `SwipeTransitionMode` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SwipeView ios:SwipeView.SwipeTransitionMode="Drag">
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
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SwipeView swipeView = new Xamarin.Forms.SwipeView();
swipeView.On<iOS>().SetSwipeTransitionMode(SwipeTransitionMode.Drag);
// ...
```

La `SwipeView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `SwipeView.SetSwipeTransitionMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler la transition utilisée lors de l’ouverture d’un `SwipeView` . L' `SwipeTransitionMode` énumération fournit deux valeurs possibles :

- `Reveal`indique que les éléments de balayage seront révélés à mesure que le `SwipeView` contenu est balayé et qu’il s’agit de la valeur par défaut de la `SwipeView.SwipeTransitionMode` propriété.
- `Drag`indique que les éléments de balayage sont glissés dans l’affichage à mesure que le `SwipeView` contenu est déplacé.

En outre, la `SwipeView.GetSwipeTransitionMode` méthode peut être utilisée pour retourner le `SwipeTransitionMode` appliqué au `SwipeView` .

Le résultat est qu’une `SwipeTransitionMode` valeur spécifiée est appliquée au `SwipeView` , qui contrôle la transition utilisée lors de l’ouverture de `SwipeView` :

[![Capture d’écran de SwipeView SwipeTransitionModes, sur iOS](swipeview-swipetransitionmode-images/swipetransitionmode.png "SwipeTransitionModes sur iOS")](swipeview-swipetransitionmode-images/swipetransitionmode-large.png#lightbox "SwipeTransitionModes sur iOS")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
