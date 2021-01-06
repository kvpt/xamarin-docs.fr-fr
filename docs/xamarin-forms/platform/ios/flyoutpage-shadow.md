---
title: Ombre FlyoutPage sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si une ombre est appliquée à la page de détails d’un FlyoutPage, lors de la révélation de la page du menu volant.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b05cb37a2399f438b9003e39341feb138bce490
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940742"
---
# <a name="flyoutpage-shadow-on-ios"></a>Ombre FlyoutPage sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle propre à la plateforme détermine si une ombre est appliquée à la page de détails d’une [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) , lors de la révélation de la page de menu volant. Il est consommé en XAML en affectant `FlyoutPage.ApplyShadow` à la propriété pouvant être liée la valeur `true` :

```xaml
<FlyoutPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:FlyoutPage.ApplyShadow="true">
    ...
</FlyoutPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSFlyoutPageCS : FlyoutPage
{
    public iOSFlyoutPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

La `FlyoutPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `FlyoutPage.SetApplyShadow` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si une ombre est appliquée à la page de détails d’une [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) , lors de la révélation de la page de menu volant. En outre, la `GetApplyShadow` méthode peut être utilisée pour déterminer si l’ombre est appliquée à la page de détails d’un `FlyoutPage` .

Le résultat est que la page de détails d’un [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) peut avoir une ombre appliquée, lors de la révélation de la page de menu volant :

[![Capture d’écran d’un FlyoutPage avec et sans ombre](flyoutpage-shadow-images/shadow.png "FlyoutPage avec et sans ombre")](flyoutpage-shadow-images/shadow-large.png#lightbox "FlyoutPage avec et sans ombre")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
