---
title: VisualElement mode de couleurs héritées sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui désactive le mode de Xamarin.Forms couleurs hérité.
ms.prod: xamarin
ms.assetid: 60FFBA67-6E06-439B-A5EB-8C808285E2CD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d10b48399fd0457bbf9cf63b4a57e17bce8f0f8f
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372715"
---
# <a name="visualelement-legacy-color-mode-on-ios"></a>VisualElement mode de couleurs héritées sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Certains des Xamarin.Forms affichages sont dotés d’un mode de couleurs hérité. Dans ce mode, lorsque la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété de la vue est définie sur `false` , la vue se substitue aux couleurs définies par l’utilisateur avec les couleurs natives par défaut pour l’état désactivé. À des fins de compatibilité descendante, ce mode de couleurs hérité reste le comportement par défaut pour les vues prises en charge.

Cette plateforme iOS spécifique désactive ce mode de couleurs hérité sur un, de [`VisualElement`](xref:Xamarin.Forms.VisualElement) sorte que les couleurs définies sur une vue par l’utilisateur restent même lorsque la vue est désactivée. Il est consommé en XAML en affectant [`VisualElement.IsLegacyColorModeEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsLegacyColorModeEnabledProperty) à la propriété jointe la valeur `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button Text="Button"
                TextColor="Blue"
                BackgroundColor="Bisque"
                ios:VisualElement.IsLegacyColorModeEnabled="False" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

_legacyColorModeDisabledButton.On<iOS>().SetIsLegacyColorModeEnabled(false);
```

La `VisualElement.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `VisualElement.SetIsLegacyColorModeEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, permet de contrôler si le mode de couleurs hérité est désactivé. En outre, le [ `VisualElement.GetIsLegacyColorModeEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetIsLegacyColorModeEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}))) peut être utilisé pour retourner une valeur indiquant si le mode de couleurs hérité est désactivé.

Le résultat est que le mode de couleurs hérité peut être désactivé, afin que les couleurs définies sur une vue par l’utilisateur restent même lorsque la vue est désactivée :

![Mode de couleurs hérité désactivé](legacy-color-mode-images/legacy-color-mode-disabled.png)

> [!NOTE]
> Lors de la définition d’un [`VisualStateGroup`](xref:Xamarin.Forms.VisualStateGroup) sur une vue, le mode de couleurs hérité est complètement ignoré. Pour plus d’informations sur les États visuels, consultez [le Xamarin.Forms Gestionnaire d’état visuel](~/xamarin-forms/user-interface/visual-state-manager.md).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)