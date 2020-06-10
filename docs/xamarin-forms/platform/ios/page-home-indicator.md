---
title : "Description de la visibilité de l’indicateur d’hébergement sur iOS" : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui définit la visibilité de l’indicateur de démarrage sur une page.»
ms. Prod : xamarin ms. AssetID : F81022E0-3C6C-49C0-A000-FAF6574D3FB7 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 05/09/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="home-indicator-visibility-on-ios"></a>Visibilité de l’indicateur de démarrage sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS définit la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page) . Il est consommé en XAML en affectant [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) à la propriété pouvant être liée la valeur `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

La `Page.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. page. SetPrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, contrôle la visibilité de l’indicateur de début. En outre, le [ `Page.PrefersHomeIndicatorAutoHidden` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. page. PrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page}))), vous pouvez utiliser la méthode pour récupérer la visibilité de l’indicateur de début.

Le résultat est que la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page) peut être contrôlée :

![Capture d’écran de la visibilité de l’indicateur de démarrage sur une page iOS](page-home-indicator-images/home-indicator-visibility.png "Visibilité de l’indicateur de la page d’accueil")

> [!NOTE]
> Ce spécifique à la plateforme peut être appliqué [`ContentPage`](xref:Xamarin.Forms.ContentPage) aux [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) objets,, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
