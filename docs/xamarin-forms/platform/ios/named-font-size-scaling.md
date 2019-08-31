---
title: Mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui désactive la mise à l’échelle de l’accessibilité pour les tailles de police nommées.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 13ca4945ed447ae811ee95e308238f04e58ac0bd
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200080"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS désactive la mise à l’échelle de l’accessibilité pour les tailles de police nommées. Elle est consommée dans XAML en définissant le `Application.EnableAccessibilityScalingForNamedFontSizes` propriété pouvant être liée `false`:

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.EnableAccessibilityScalingForNamedFontSizes="false">
    ...
</Application>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetEnableAccessibilityScalingForNamedFontSizes(false);
```

Le `Application.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. La `Application.SetEnableAccessibilityScalingForNamedFontSizes` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour désactiver les tailles de police nommées mises à l’échelle par les paramètres d’accessibilité iOS. En outre, la `Application.GetEnableAccessibilityScalingForNamedFontSizes` méthode peut être utilisée pour retourner si les tailles de police nommées sont mises à l’échelle par les paramètres d’accessibilité iOS.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
