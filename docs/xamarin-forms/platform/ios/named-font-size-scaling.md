---
title : "Description de la mise à l’échelle des polices nommées sur iOS" Description : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités qui ne sont disponibles que sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui désactive la mise à l’échelle de l’accessibilité pour les tailles de police nommées.
ms. Prod : xamarin ms. AssetID : B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 06/28/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS désactive la mise à l’échelle de l’accessibilité pour les tailles de police nommées. Il est consommé en XAML en affectant `Application.EnableAccessibilityScalingForNamedFontSizes` à la propriété pouvant être liée la valeur `false` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

La `Application.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Application.SetEnableAccessibilityScalingForNamedFontSizes` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour désactiver les tailles de police nommées mises à l’échelle par les paramètres d’accessibilité iOS. En outre, la `Application.GetEnableAccessibilityScalingForNamedFontSizes` méthode peut être utilisée pour retourner si les tailles de police nommées sont mises à l’échelle par les paramètres d’accessibilité iOS.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
