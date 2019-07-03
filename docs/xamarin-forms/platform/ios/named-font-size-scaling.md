---
title: Mise à l’échelle d’accessibilité pour les tailles de police nommé sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser la plateforme spécifique à iOS qui désactive l’accessibilité de la mise à l’échelle pour les tailles de police nommée.
ms.prod: xamarin
ms.assetid: B443BAF6-E6F6-4A0E-80B5-CAACE6B550EF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/28/2019
ms.openlocfilehash: 1fc6fefe1f9fe48fe2abb367b376a5a6f3484462
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67517948"
---
# <a name="accessibility-scaling-for-named-font-sizes-on-ios"></a>Mise à l’échelle d’accessibilité pour les tailles de police nommé sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Cette spécifiques à la plateforme iOS désactive d’accessibilité de la mise à l’échelle pour les tailles de police nommée. Elle est consommée dans XAML en définissant le `Application.EnableAccessibilityScalingForNamedFontSizes` propriété pouvant être liée `false`:

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

Le `Application.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Application.SetEnableAccessibilityScalingForNamedFontSizes` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour désactiver les tailles de police nommée mise à l’échelle par les paramètres d’accessibilité iOS. En outre, le `Application.GetEnableAccessibilityScalingForNamedFontSizes` méthode peut être utilisée pour retourner si les tailles de police nommés sont mis à l’échelle par les paramètres d’accessibilité iOS.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
