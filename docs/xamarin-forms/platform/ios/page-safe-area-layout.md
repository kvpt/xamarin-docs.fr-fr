---
title: Guide de disposition en zone sécurisée sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le propre à la plateforme iOS, qui garantit que le contenu de la page est placé sur une zone de l’écran qui est sécurisée pour tous les appareils qui utilisent iOS 11 et versions ultérieures.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5ca30481fbc0e5631ff75000c688dd805793e670
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128054"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guide de disposition en zone sécurisée sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour s’assurer que le contenu de la page est positionné sur une zone de l’écran qui est sécurisée pour tous les appareils qui utilisent iOS 11 et versions ultérieures. Plus précisément, il permet de s’assurer que le contenu n’est pas coupé par des angles de périphérique arrondis, l’indicateur de début ou le boîtier du capteur sur un iPhone X. Il est consommé en XAML en affectant `Page.UseSafeArea` une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

La `Page.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Page.SetUseSafeArea` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, détermine si le repère de disposition de zone sécurisée est activé.

Le résultat est que le contenu de la page peut être positionné sur une zone de l’écran qui est sûre pour tous les iPhone :

[![](page-safe-area-images/safe-area-layout.png "Safe Area Layout Guide")](page-safe-area-images/safe-area-layout-large.png#lightbox "Safe Area Layout Guide")

> [!NOTE]
> La zone sécurisée définie par Apple est utilisée dans Xamarin.Forms pour définir la [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) propriété et remplace toutes les valeurs précédentes de cette propriété qui ont été définies.

La zone sécurisée peut être personnalisée en extrayant sa [`Thickness`](xref:Xamarin.Forms.Thickness) valeur avec la `Page.SafeAreaInsets` méthode de l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms. Elle peut ensuite être modifiée en fonction des besoins et être réaffectée à la `Padding` propriété dans le constructeur de page ou la [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) substitution :

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
