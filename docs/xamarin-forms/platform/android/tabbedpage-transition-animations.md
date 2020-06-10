---
titre : « TabbedPage page transition animations on Android » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui désactive les animations de transition lors de la navigation dans les pages d’un TabbedPage.
ms. Prod : xamarin ms. AssetID : 2DB4EA6D-9CED-4137-BAB2-B20A457B1CA3 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="tabbedpage-page-transition-animations-on-android"></a>Animations de transition de page TabbedPage sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour désactiver les animations de transition lors de la navigation dans les pages, par programmation ou à l’aide de la barre d’onglets dans un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Il est consommé en XAML en affectant `TabbedPage.IsSmoothScrollEnabled` à la propriété pouvant être liée la valeur `false` :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.IsSmoothScrollEnabled="false">
    ...
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetIsSmoothScrollEnabled(false);
```

La `TabbedPage.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `TabbedPage.SetIsSmoothScrollEnabled` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour contrôler si les animations de transition seront affichées lors de la navigation entre les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . En outre, la `TabbedPage` classe de l' `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espace de noms possède également les méthodes suivantes :

- `IsSmoothScrollEnabled`, qui est utilisé pour récupérer si les animations de transition seront affichées lors de la navigation entre les pages d’un `TabbedPage` .
- `EnableSmoothScroll`, qui est utilisé pour activer les animations de transition lors de la navigation entre les pages d’un `TabbedPage` .
- `DisableSmoothScroll`, qui est utilisé pour désactiver les animations de transition lors de la navigation entre les pages d’un `TabbedPage` .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
