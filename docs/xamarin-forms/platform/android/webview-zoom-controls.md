---
title: Zoom WebView sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme Android qui active le zoom sur une WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68655999"
---
# <a name="webview-zoom-on-android"></a>Zoom WebView sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android active le pincement-zoom et un contrôle de zoom [`WebView`](xref:Xamarin.Forms.WebView)sur un. Il est consommé en XAML en affectant `WebView.EnableZoomControls` aux `WebView.DisplayZoomControls` `boolean` propriétés et pouvant être liées la valeur :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La `WebView.EnableZoomControls` propriété pouvant être liée détermine si le pincement de zoom est activé sur [`WebView`](xref:Xamarin.Forms.WebView)le, et `WebView.DisplayZoomControls` la propriété pouvant être liée détermine si les contrôles de zoom sont `WebView`superposés sur le.

Vous pouvez également utiliser la plate-forme spécifique à la C# plateforme à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

Le `WebView.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. La `WebView.EnableZoomControls` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour contrôler si le pincement de zoom est activé [`WebView`](xref:Xamarin.Forms.WebView)sur le. La `WebView.DisplayZoomControls` méthode, dans le même espace de noms, est utilisée pour contrôler si les contrôles de zoom sont `WebView`superposés sur le. En outre, les `WebView.ZoomControlsEnabled` méthodes `WebView.ZoomControlsDisplayed` et peuvent être utilisées pour retourner si les contrôles pince-to-zoom et zoom sont activés, respectivement.

Le résultat est que le pincement de zoom peut être activé sur un [`WebView`](xref:Xamarin.Forms.WebView), et les contrôles de zoom peuvent être superposés `WebView`sur :

[![Capture d’écran de WebView avec zoom sur Android](webview-zoom-controls-images/webview-zoom.png "WebView avec zoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "WebView avec zoom")

> [!IMPORTANT]
> Les contrôles de zoom doivent être activés et affichés, via les propriétés ou méthodes pouvant être liées, pour être superposées sur un [`WebView`](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
