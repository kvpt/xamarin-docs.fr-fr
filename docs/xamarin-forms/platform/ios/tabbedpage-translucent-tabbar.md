---
title: Barre d’onglets translucide TabbedPage sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser l’objet spécifique à la plateforme iOS qui définit le mode translucidité de la barre d’onglets sur un TabbedPage.
ms.prod: xamarin
ms.assetid: 9581AE81-9557-47AD-8B07-25A1AC5F055B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8127191aab80d81fc2e532e3d5e14931b834eeae
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137031"
---
# <a name="tabbedpage-translucent-tab-bar-on-ios"></a>Barre d’onglets translucide TabbedPage sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour définir le mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Il est consommé en XAML en affectant `TabbedPage.TranslucencyMode` à la propriété pouvant être liée la `TranslucencyMode` valeur d’énumération :

```xaml
<TabbedPage ...
            xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
            ios:TabbedPage.TranslucencyMode="Opaque">
    ...
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetTranslucencyMode(TranslucencyMode.Opaque);
```

La `TabbedPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `TabbedPage.SetTranslucencyMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour définir le mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) en spécifiant l’une des `TranslucencyMode` valeurs d’énumération suivantes :

- `Default`, qui définit la barre d’onglets en mode translucidité par défaut. C’est la valeur par défaut de la propriété `TabbedPage.TranslucencyMode`.
- `Translucent`, qui définit la barre d’onglets pour qu’elle soit translucide.
- `Opaque`, qui définit la barre d’onglets pour qu’elle soit opaque.

En outre, la `GetTranslucencyMode` méthode peut être utilisée pour récupérer la valeur actuelle de l' `TranslucencyMode` énumération appliquée au [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

Le résultat est que le mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) peut être défini :

![Capture d’écran des barres d’onglet translucides et opaques sur iOS](tabbedpage-translucent-tabbar-images/translucencymodes.png "Barres d’onglet translucides et opaques")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
