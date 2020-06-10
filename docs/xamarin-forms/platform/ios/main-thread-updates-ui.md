---
titre : « principales mises à jour du contrôle de thread sur iOS » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS, qui permet d’effectuer le contrôle de mise en page et de rendu des mises à jour sur le thread principal.
ms. Prod : xamarin ms. AssetID : 945E711D-9BD2-4BF9-9FB3-CBE0D5B25A49 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="main-thread-control-updates-on-ios"></a>Mises à jour du contrôle de thread principal sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS permet l’exécution des mises à jour de la disposition du contrôle et du rendu sur le thread principal, au lieu d’être exécuté sur un thread d’arrière-plan. Elle doit être rarement nécessaire, mais dans certains cas peut empêcher des blocages. Son consommé en XAML en affectant `Application.HandleControlUpdatesOnMainThread` à la propriété pouvant être liée la valeur `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.HandleControlUpdatesOnMainThread="true">
    ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetHandleControlUpdatesOnMainThread(true);
```

La `Application.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Application.SetHandleControlUpdatesOnMainThread` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si la disposition des contrôles et les mises à jour de rendu sont effectuées sur le thread principal, au lieu d’être exécutées sur un thread d’arrière-plan. En outre, la `Application.GetHandleControlUpdatesOnMainThread` méthode peut être utilisée pour retourner si la disposition du contrôle et les mises à jour du rendu sont exécutées sur le thread principal.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
