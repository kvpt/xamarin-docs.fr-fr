---
title: Mode de couleur VisualElement hérité sur Windows
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le Windows spécifique à la plateforme qui désactive le mode de couleur hérité de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: B8759309-07C7-4DCA-A18A-C1A198A7951B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: a3b6611ab5c7b318938dfc40b702d6f65787cfbf
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2019
ms.locfileid: "65924844"
---
# <a name="visualelement-legacy-color-mode-on-windows"></a>Mode de couleur VisualElement hérité sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Certaines des vues Xamarin.Forms fonctionnalité un mode couleur hérité. Dans ce mode, lorsque le [ `IsEnabled` ](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété de la vue est définie sur `false`, la vue remplace les couleurs définies par l’utilisateur avec les couleurs natif par défaut de l’état désactivé. Pour vers l’arrière, compatibilité, ce mode hérité couleur reste le comportement par défaut pour les vues prises en charge.

Cette spécifiques à la plateforme Universal Windows Platform désactive ce mode couleur hérité, afin que les couleurs définissent sur une vue par l’utilisateur restent même lorsque la vue est désactivée. Elle est consommée dans XAML en définissant le [ `VisualElement.IsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.IsLegacyColorModeEnabledProperty) propriété jointe `false`:

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Editor Text="Enter text here"
                TextColor="Blue"
                BackgroundColor="Bisque"
                windows:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

_legacyColorModeDisabledEditor.On<Windows>().SetIsLegacyColorModeEnabled(false);
```

Le `VisualElement.On<Windows>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Windows. Le [ `VisualElement.SetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.SetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement},System.Boolean)) (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.WindowsSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisé pour contrôler si le mode hérité est désactivé. En outre, le [ `VisualElement.GetIsLegacyColorModeEnabled` ](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.GetIsLegacyColorModeEnabled(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Windows,Xamarin.Forms.VisualElement})) méthode peut être utilisée pour retourner si le mode hérité est désactivé.

Le résultat est que le mode hérité de couleur peut être désactivé, afin que les couleurs définies sur une vue par l’utilisateur restent même lorsque la vue est désactivée :

![](legacy-color-mode-images/legacy-color-mode-disabled.png "Mode hérité couleur désactivé")

> [!NOTE]
> Lorsque vous définissez un [ `VisualStateGroup` ](xref:Xamarin.Forms.VisualStateGroup) sur une vue, le mode hérité de couleur est complètement ignoré. Pour plus d’informations sur les états visuels, consultez [le Gestionnaire d’état visuel Xamarin.Forms](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [WindowsSpecific API](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
