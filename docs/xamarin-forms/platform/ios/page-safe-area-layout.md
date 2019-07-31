---
title: Guide de disposition en zone sécurisée sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le propre à la plateforme iOS, qui garantit que le contenu de la page est placé sur une zone de l’écran qui est sécurisée pour tous les appareils qui utilisent iOS 11 et versions ultérieures.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c6a2ec5a4d1466b7118e6cc7b03cc5518b27e2fb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644544"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guide de disposition en zone sécurisée sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour s’assurer que le contenu de la page est positionné sur une zone de l’écran qui est sécurisée pour tous les appareils qui utilisent iOS 11 et versions ultérieures. Plus précisément, il vous aidera à vous assurer que le contenu n’est pas découpé par les angles arrondis d’appareil, l’indicateur d’accueil ou le boîtier de capteur sur un iPhone X. Elle est consommée dans XAML en définissant le `Page.UseSafeArea` propriété jointe un `boolean` valeur :

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

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

Le `Page.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Page.SetUseSafeArea` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) contrôle l’espace de noms, si le repère de disposition de zone protégée est activée.

Le résultat est que le contenu de la page peut être positionné sur une zone de l’écran qui est sécurisé pour tous les iPhone :

[![](page-safe-area-images/safe-area-layout.png "Repère de disposition de zone protégée")](page-safe-area-images/safe-area-layout-large.png#lightbox "repère de disposition de zone protégée")

> [!NOTE]
> La zone protégée définie par Apple est utilisée dans Xamarin.Forms pour définir le [ `Page.Padding` ](xref:Xamarin.Forms.Page.Padding) propriété et remplacent toutes les valeurs précédentes de cette propriété qui ont été définis.

La zone protégée peut être personnalisée en récupérant sa [ `Thickness` ](xref:Xamarin.Forms.Thickness) valeur avec le `Page.SafeAreaInsets` méthode à partir de la [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms. Il peut ensuite être modifié en tant que nécessaire et réaffecté à la `Padding` propriété dans le constructeur de page ou [ `OnAppearing` ](xref:Xamarin.Forms.Page.OnAppearing) remplacer :

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
