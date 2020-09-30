---
title: Reconnaissance simultanée des mouvements de panoramique sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui permet l’utilisation simultanée de la reconnaissance de mouvement de panoramique dans une application.
ms.prod: xamarin
ms.assetid: 883D89DA-F8FF-4B97-9C3F-2DD05C96A495
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 5bea8b58d8b80ced97856fc7c981afdd5c2102a7
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562403"
---
# <a name="simultaneous-pan-gesture-recognition-on-ios"></a>Reconnaissance simultanée des mouvements de panoramique sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Lorsqu’un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) est attaché à une vue à l’intérieur d’une vue de défilement, tous les mouvements de panoramique sont capturés par `PanGestureRecognizer` et ne sont pas passés à la vue de défilement. Par conséquent, la vue de défilement ne défilera plus.

Ce spécifique à la plateforme iOS permet `PanGestureRecognizer` à un dans une vue de défilement de capturer et de partager le mouvement panoramique avec la vue de défilement. Il est consommé en XAML en affectant [`Application.PanGestureRecognizerShouldRecognizeSimultaneously`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Application.PanGestureRecognizerShouldRecognizeSimultaneouslyProperty) à la propriété jointe la valeur `true` :

```xaml
<Application ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Application.PanGestureRecognizerShouldRecognizeSimultaneously="true">
    ...
</Application>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

Xamarin.Forms.Application.Current.On<iOS>().SetPanGestureRecognizerShouldRecognizeSimultaneously(true);
```

La `Application.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `Application.SetPanGestureRecognizerShouldRecognizeSimultaneously` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. application. SetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, permet de contrôler si un module de reconnaissance de mouvement de panoramique dans une vue de défilement capture le mouvement de panoramique, ou capture et partage le mouvement de panoramique avec la vue de défilement. En outre, le [ `Application.GetPanGestureRecognizerShouldRecognizeSimultaneously` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. application. GetPanGestureRecognizerShouldRecognizeSimultaneously ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . Application})) peut être utilisée pour retourner une valeur indiquant si le mouvement de panoramique est partagé avec la vue de défilement qui contient [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) .

Par conséquent, avec cette activée spécifique à la plateforme, quand un [`ListView`](xref:Xamarin.Forms.ListView) contient un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) , le `ListView` et le `PanGestureRecognizer` reçoivent le mouvement de panoramique et le traitent. Toutefois, avec cette désactivation spécifique à la plateforme, lorsqu’un `ListView` contient un `PanGestureRecognizer` , le `PanGestureRecognizer` capture le mouvement panoramique et le traite, et `ListView` ne reçoit pas le mouvement Pan.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)