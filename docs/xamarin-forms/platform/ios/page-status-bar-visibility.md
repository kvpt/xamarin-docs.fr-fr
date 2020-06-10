---
title : "Description de la barre d’état de la page sur iOS" Description : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui définit la visibilité de la barre d’État sur une page.»
ms. Prod : xamarin ms. AssetID : D8BB7C24-A27F-4758-8557-6A81F909ABD9 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="page-status-bar-visibility-on-ios"></a>Visibilité de la barre d’état de la page sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour définir la visibilité de la barre d’État sur un [`Page`](xref:Xamarin.Forms.Page) et permet de contrôler l’entrée ou la sortie de la barre d’état `Page` . Il est consommé en XAML en affectant `Page.PrefersStatusBarHidden` à la propriété jointe une valeur de l' `StatusBarHiddenMode` énumération, et éventuellement `Page.PreferredStatusBarUpdateAnimation` à la propriété jointe la valeur de l' `UIStatusBarAnimation` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

La `Page.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Page.SetPrefersStatusBarHidden` méthode, dans l' `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espace de noms, est utilisée pour définir la visibilité de la barre d’État sur un [`Page`](xref:Xamarin.Forms.Page) en spécifiant l’une des `StatusBarHiddenMode` valeurs d’énumération : `Default` , `True` ou `False` . Les `StatusBarHiddenMode.True` `StatusBarHiddenMode.False` valeurs et définissent la visibilité de la barre d’État, quelle que soit l’orientation de l’appareil, et la `StatusBarHiddenMode.Default` valeur masque la barre d’État dans un environnement compact.

Le résultat est que la visibilité de la barre d’État sur un [`Page`](xref:Xamarin.Forms.Page) peut être définie :

![](page-status-bar-visibility-images/hide-status-bar.png "Status Bar Visibility Platform-Specific")

> [!NOTE]
> Sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , la `StatusBarHiddenMode` valeur d’énumération spécifiée met également à jour la barre d’État sur toutes les pages enfants. Sur tous les autres [`Page`](xref:Xamarin.Forms.Page) types dérivés de, la `StatusBarHiddenMode` valeur d’énumération spécifiée met uniquement à jour la barre d’État sur la page actuelle.

La `Page.SetPreferredStatusBarUpdateAnimation` méthode est utilisée pour définir le mode d’entrée ou de sortie de la barre d’état [`Page`](xref:Xamarin.Forms.Page) en spécifiant l’une des `UIStatusBarAnimation` valeurs d’énumération : `None` , `Fade` ou `Slide` . Si la `Fade` `Slide` valeur d’énumération ou est spécifiée, une animation de 0,25 secondes s’exécute lorsque la barre d’État entre dans ou quitte le `Page` .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
