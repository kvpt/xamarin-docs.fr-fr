---
title: Barre d’onglets translucide TabbedPage sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser l’objet spécifique à la plateforme iOS qui définit le mode translucidité de la barre d’onglets sur un TabbedPage.
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 55fda0be2e260c5aa4a34ab2dcc1ac3cac33b92a
ms.sourcegitcommit: 6c60914b380ff679bbffd7790edd4d5e18005d0a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2020
ms.locfileid: "80070290"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>Barre d’onglets translucide TabbedPage sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour définir le mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Il est consommé en XAML en affectant à la `TabbedPage.TranslucencyMode` propriété pouvant être liée une valeur d’énumération `TranslucencyMode` :

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

La méthode `TabbedPage.On<iOS>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La méthode `TabbedPage.SetTranslucencyMode`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , est utilisée pour définir le mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) en spécifiant l’une des valeurs d’énumération `TranslucencyMode` suivantes :

- `Default`, qui définit la barre d’onglets en mode translucidité par défaut. C’est la valeur par défaut de la propriété `TabbedPage.TranslucencyMode`.
- `Translucent`, qui définit la barre d’onglets pour qu’elle soit translucide.
- `Opaque`, qui définit la barre d’onglets pour qu’elle soit opaque.

En outre, la méthode `GetTranslucencyMode` peut être utilisée pour récupérer la valeur actuelle de l’énumération `TranslucencyMode` appliquée au [`TabbedPage`](xref:Xamarin.Forms.TabbedPage).

Le résultat est que le mode translucidité de la barre d’onglets d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être défini :

![Capture d’écran des barres d’onglet translucides et opaques sur iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barres d’onglet translucides et opaques")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
