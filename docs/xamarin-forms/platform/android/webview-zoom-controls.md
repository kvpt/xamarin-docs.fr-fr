---
title: Zoom WebView sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui active le zoom sur une WebView.
ms.prod: xamarin
ms.assetid: DC1A3762-6A42-4298-929C-445F416C3E60
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/09/2019
ms.openlocfilehash: 2142882add91d613263d11fa4c1e6d7ad142c7c7
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68655999"
---
# <a name="webview-zoom-on-android"></a>Zoom WebView sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android active le pincement de zoom et un contrôle de zoom sur un [`WebView`](xref:Xamarin.Forms.WebView). Il est consommé en XAML en définissant le `WebView.EnableZoomControls` et `WebView.DisplayZoomControls` propriétés pouvant être liées à `boolean` valeurs :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView Source="https://www.xamarin.com"
             android:WebView.EnableZoomControls="true"
             android:WebView.DisplayZoomControls="true" />
</ContentPage>
```

La propriété pouvant être liée `WebView.EnableZoomControls` contrôle si le pincement de zoom est activé sur le [`WebView`](xref:Xamarin.Forms.WebView), et si la propriété pouvant être liée `WebView.DisplayZoomControls` contrôle si les contrôles de zoom sont superposés sur le `WebView`.

Vous pouvez également utiliser la plate-forme spécifique à la C# plateforme à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>()
    .EnableZoomControls(true)
    .DisplayZoomControls(true);
```

La méthode `WebView.On<Android>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La méthode `WebView.EnableZoomControls`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , est utilisée pour contrôler si le pincement de zoom est activé sur le [`WebView`](xref:Xamarin.Forms.WebView). La méthode `WebView.DisplayZoomControls`, dans le même espace de noms, est utilisée pour contrôler si les contrôles de zoom sont superposés sur le `WebView`. En outre, les méthodes `WebView.ZoomControlsEnabled` et `WebView.ZoomControlsDisplayed` peuvent être utilisées pour retourner si les contrôles de pincement et de zoom sont activés, respectivement.

Le résultat est que le pincement de zoom peut être activé sur un [`WebView`](xref:Xamarin.Forms.WebView), et les contrôles de zoom peuvent être superposés sur le `WebView` :

[![Capture d’écran de WebView avec zoom sur Android](webview-zoom-controls-images/webview-zoom.png "WebView avec zoom")](webview-zoom-controls-images/webview-zoom-large.png#lightbox "WebView avec zoom")

> [!IMPORTANT]
> Les contrôles de zoom doivent être activés et affichés, via les propriétés ou méthodes pouvant être liées, pour être superposées sur un [`WebView`](xref:Xamarin.Forms.WebView).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
