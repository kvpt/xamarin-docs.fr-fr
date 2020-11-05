---
title: Zoom WebView sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui active le zoom sur une WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 12c8eafd9768ee0da232ead4aac6cd6bdc3c9a2d
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369608"
---
# <a name="webview-zoom-on-android"></a>Zoom WebView sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android active le pincement-zoom et un contrôle de zoom sur un [`WebView`](xref:Xamarin.Forms.WebView) . Il est consommé en XAML en affectant `WebView.EnableZoomControls` `WebView.DisplayZoomControls` aux propriétés et pouvant être liées la valeur `boolean` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La `WebView.EnableZoomControls` propriété pouvant être liée détermine si le pincement de zoom est activé sur le [`WebView`](xref:Xamarin.Forms.WebView) , et la `WebView.DisplayZoomControls` propriété pouvant être liée détermine si les contrôles de zoom sont superposés sur le `WebView` .

En guise d’alternative, le propre à la plateforme peut être consommé à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

La `WebView.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `WebView.EnableZoomControls` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour contrôler si le pincement de zoom est activé sur le [`WebView`](xref:Xamarin.Forms.WebView) . La `WebView.DisplayZoomControls` méthode, dans le même espace de noms, est utilisée pour contrôler si les contrôles de zoom sont superposés sur le `WebView` . En outre, les `WebView.ZoomControlsEnabled` `WebView.ZoomControlsDisplayed` méthodes et peuvent être utilisées pour retourner si les contrôles pince-to-zoom et zoom sont activés, respectivement.

Le résultat est que le pincement de zoom peut être activé sur un [`WebView`](xref:Xamarin.Forms.WebView) , et les contrôles de zoom peuvent être superposés sur `WebView` :

[![Capture d’écran de WebView avec zoom sur Android](webview-zoom-controls-images/webview-zoom.png "WebView avec zoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "WebView avec zoom")

> [!IMPORTANT]
> Les contrôles de zoom doivent être activés et affichés, via les propriétés ou méthodes pouvant être liées, pour être superposées sur un [`WebView`](xref:Xamarin.Forms.WebView) .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)