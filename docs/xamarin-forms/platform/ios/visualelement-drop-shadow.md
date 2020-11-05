---
title: VisualElement supprimer les ombres sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui active une ombre portée sur un VisualElement.
ms.prod: xamarin
ms.assetid: 2147FD66-058E-4BE5-840A-369842B26EC4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2e4c669ee24c72cc5962feff0ec8e55b54f7d372
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93375118"
---
# <a name="visualelement-drop-shadows-on-ios"></a>VisualElement supprimer les ombres sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour activer une ombre portée sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Il est consommé en XAML en affectant [`VisualElement.IsShadowEnabled`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.VisualElement.IsShadowEnabledProperty) à la propriété jointe `true` , ainsi qu’à un certain nombre de propriétés jointes facultatives supplémentaires qui contrôlent l’ombre portée :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <BoxView ...
                 ios:VisualElement.IsShadowEnabled="true"
                 ios:VisualElement.ShadowColor="Purple"
                 ios:VisualElement.ShadowOpacity="0.7"
                 ios:VisualElement.ShadowRadius="12">
            <ios:VisualElement.ShadowOffset>
                <Size>
                    <x:Arguments>
                        <x:Double>10</x:Double>
                        <x:Double>10</x:Double>
                    </x:Arguments>
                </Size>
            </ios:VisualElement.ShadowOffset>
         </BoxView>
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var boxView = new BoxView { Color = Color.Aqua, WidthRequest = 100, HeightRequest = 100 };
boxView.On<iOS>()
       .SetIsShadowEnabled(true)
       .SetShadowColor(Color.Purple)
       .SetShadowOffset(new Size(10,10))
       .SetShadowOpacity(0.7)
       .SetShadowRadius(12);
```

La `VisualElement.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `VisualElement.SetIsShadowEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, permet de contrôler si une ombre portée est activée sur le `VisualElement` . En outre, les méthodes suivantes peuvent être appelées pour contrôler l’ombre portée :

- [ `SetShadowColor` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, Xamarin.Forms . Couleur) : définit la couleur de l’ombre portée. La couleur par défaut est [`Color.Default`](xref:Xamarin.Forms.Color.Default*) .
- [ `SetShadowOffset` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, Xamarin.Forms . Taille) : définit le décalage de l’ombre portée. Le décalage change le sens de conversion de l’ombre et est spécifié en tant que [`Size`](xref:Xamarin.Forms.Size) valeur. Les `Size` valeurs de la structure sont exprimées en unités indépendantes du périphérique, avec la première valeur étant la distance à gauche (valeur négative) ou droite (valeur positive), et la deuxième valeur étant la distance au-dessus (valeur négative) ou inférieure (valeur positive). La valeur par défaut de cette propriété est (0,0, 0,0), ce qui entraîne le cast de l’ombre autour de chaque côté de `VisualElement` .
- [ `SetShadowOpacity` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. double) : définit l’opacité de l’ombre portée, la valeur étant comprise entre 0,0 (transparent) et 1,0 (opaque). La valeur d’opacité par défaut est 0,5.
- [ `SetShadowRadius` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. SetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}, System. double) : définit le rayon de flou utilisé pour le rendu de l’ombre portée. La valeur par défaut du rayon est 10,0.

> [!NOTE]
> L’état d’une ombre portée peut être interrogé en appelant la [ `GetIsShadowEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetIsShadowEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowColor` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowOffset` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})), [ `GetShadowOpacity` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowOpacity ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement})) et [ `GetShadowRadius` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. VisualElement. GetShadowRadius ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . VisualElement}))).

Le résultat est qu’une ombre portée peut être activée sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement) :

![Ombre portée activée](drop-shadow-images/drop-shadow.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)