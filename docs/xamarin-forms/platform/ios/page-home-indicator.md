---
title: Visibilité de l’indicateur de démarrage sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui définit la visibilité de l’indicateur de démarrage sur une page.
ms.prod: xamarin
ms.assetid: F81022E0-3C6C-49C0-A000-FAF6574D3FB7
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 20d8bb1140599b5b858c52fde1e54393e66d7bd1
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940549"
---
# <a name="home-indicator-visibility-on-ios"></a>Visibilité de l’indicateur de démarrage sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS définit la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page) . Il est consommé en XAML en affectant [`Page.PrefersHomeIndicatorAutoHidden`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Page.PrefersHomeIndicatorAutoHiddenProperty) à la propriété pouvant être liée la valeur `boolean` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersHomeIndicatorAutoHidden="true">
    ...
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersHomeIndicatorAutoHidden(true);
```

La `Page.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `Page.SetPrefersHomeIndicatorAutoHidden` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. page. SetPrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, contrôle la visibilité de l’indicateur de début. En outre, le [ `Page.PrefersHomeIndicatorAutoHidden` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. page. PrefersHomeIndicatorAutoHidden ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Page}))), vous pouvez utiliser la méthode pour récupérer la visibilité de l’indicateur de début.

Le résultat est que la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page) peut être contrôlée :

![Capture d’écran de la visibilité de l’indicateur de démarrage sur une page iOS](page-home-indicator-images/home-indicator-visibility.png "Visibilité de l’indicateur de la page d’accueil")

> [!NOTE]
> Ce spécifique à la plateforme peut être appliqué [`ContentPage`](xref:Xamarin.Forms.ContentPage) aux [`FlyoutPage`](xref:Xamarin.Forms.FlyoutPage) objets,, [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)