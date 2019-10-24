---
title: Contenu mixte WebView sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui affiche du contenu mixte dans une WebView dans des applications qui ciblent l’API 21 ou une version ultérieure.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: f9b4a12d3049cea37235cc04805a7411a9bdb236
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72696490"
---
# <a name="webview-mixed-content-on-android"></a>Contenu mixte WebView sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme Android indique si une [`WebView`](xref:Xamarin.Forms.WebView) peut afficher du contenu mixte dans les applications qui ciblent l’API 21 ou une version ultérieure. Le contenu mixte est un contenu qui est initialement chargé sur une connexion HTTPs, mais qui charge des ressources (telles que des images, des fichiers audio, des vidéos, des feuilles de style, des scripts) sur une connexion HTTP. Il est consommé en XAML en affectant à la propriété jointe [`WebView.MixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) la valeur de l’énumération [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Elle peut également être utilisée à partir de C# l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

La méthode `WebView.On<Android>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La méthode [`WebView.SetMixedContentMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) , dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , est utilisée pour contrôler si le contenu mixte peut être affiché, avec l’énumération [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) qui fournit trois valeurs possibles :

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) : indique que le [`WebView`](xref:Xamarin.Forms.WebView) permettra à une origine https de charger le contenu à partir d’une origine http.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) : indique que le [`WebView`](xref:Xamarin.Forms.WebView) n’autorise pas une origine HTTPS à charger le contenu à partir d’une origine http.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) : indique que le [`WebView`](xref:Xamarin.Forms.WebView) essaiera d’être compatible avec l’approche du dernier navigateur Web de l’appareil. Certains contenus HTTP peuvent être autorisés à être chargés par une origine HTTPs et d’autres types de contenu seront bloqués. Les types de contenu bloqués ou autorisés peuvent changer avec chaque version du système d’exploitation.

Le résultat est qu’une valeur de [`MixedContentHandling`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) spécifiée est appliquée au [`WebView`](xref:Xamarin.Forms.WebView), qui contrôle si le contenu mixte peut être affiché :

[![WebView gestion du contenu mixte spécifique à la plateforme](webview-mixed-content-images/webview-mixedcontent.png "WebView gestion du contenu mixte spécifique à la plateforme")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView gestion du contenu mixte spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
