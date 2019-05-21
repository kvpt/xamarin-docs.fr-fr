---
title: Page visibilité de barre d’état sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser la plateforme spécifique à iOS qui définit la visibilité de la barre d’état sur une Page.
ms.prod: xamarin
ms.assetid: D8BB7C24-A27F-4758-8557-6A81F909ABD9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 66bd6c1ca2ccc3e5335a790950cbf1a792a7df82
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65925401"
---
# <a name="page-status-bar-visibility-on-ios"></a>Page visibilité de barre d’état sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Cette spécifiques à la plateforme iOS est utilisé pour définir la visibilité de la barre d’état sur un [ `Page` ](xref:Xamarin.Forms.Page), et il inclut la possibilité de contrôler comment la barre d’état entre ou quitte le `Page`. Elle est consommée dans XAML en définissant le `Page.PrefersStatusBarHidden` propriété attachée à une valeur de la `StatusBarHiddenMode` énumération et éventuellement le `Page.PreferredStatusBarUpdateAnimation` propriété attachée à une valeur de la `UIStatusBarAnimation` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.PrefersStatusBarHidden="True"
             ios:Page.PreferredStatusBarUpdateAnimation="Fade">
  ...
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetPrefersStatusBarHidden(StatusBarHiddenMode.True)
         .SetPreferredStatusBarUpdateAnimation(UIStatusBarAnimation.Fade);
```

Le `Page.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Page.SetPrefersStatusBarHidden` (méthode), dans le `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espace de noms, est utilisé pour définir la visibilité de la barre d’état sur un [ `Page` ](xref:Xamarin.Forms.Page) en spécifiant une de le `StatusBarHiddenMode` valeurs d’énumération : `Default`, `True` , ou `False`. Le `StatusBarHiddenMode.True` et `StatusBarHiddenMode.False` valeurs définir la visibilité de barre d’état, quel que soit l’orientation de l’appareil et le `StatusBarHiddenMode.Default` valeur masque la barre d’état dans un environnement compact verticalement.

Le résultat est que la visibilité de la barre d’état sur un [ `Page` ](xref:Xamarin.Forms.Page) peuvent être définies :

![](page-status-bar-visibility-images/hide-status-bar.png "Barre d’état visibilité spécifiques à la plateforme")

> [!NOTE]
> Sur un [ `TabbedPage` ](xref:Xamarin.Forms.TabbedPage), spécifié `StatusBarHiddenMode` valeur d’énumération mettra met également à jour la barre d’état sur toutes les pages de l’enfant. Sur tous les autres [ `Page` ](xref:Xamarin.Forms.Page)-types dérivés, spécifiés `StatusBarHiddenMode` valeur d’énumération met à jour uniquement la barre d’état sur la page actuelle.

Le `Page.SetPreferredStatusBarUpdateAnimation` méthode est utilisée pour définir la façon dont la barre d’état entre ou quitte le [ `Page` ](xref:Xamarin.Forms.Page) en spécifiant une de le `UIStatusBarAnimation` valeurs d’énumération : `None`, `Fade`, ou `Slide`. Si le `Fade` ou `Slide` valeur d’énumération est spécifiée, une seconde 0,25 animation s’exécute comme la barre d’état entre ou quitte le `Page`.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
