---
title: Contenu mixte WebView sur Android
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser la plateforme Android spécifique qui affichait du contenu mixte dans une WebView dans des applications qui ciblent l’API 21 ou une version ultérieure.
ms.prod: xamarin
ms.assetid: 68F908F3-04C5-4B91-B6E5-B7E8357B4154
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 286a7dceead327d727110d4ebbcecbc2341345b3
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68656069"
---
# <a name="webview-mixed-content-on-android"></a>Contenu mixte WebView sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme Android indique [`WebView`](xref:Xamarin.Forms.WebView) si un peut afficher du contenu mixte dans les applications qui ciblent l’API 21 ou une version ultérieure. Contenu mixte est un contenu qui sont initialement chargée sur une connexion HTTPS, mais qui charge des ressources (telles que des images, audio, vidéo, feuilles de style, scripts) via une connexion HTTP. Elle est consommée dans XAML en définissant le [ `WebView.MixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.MixedContentModeProperty) propriété attachée à une valeur de la [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) énumération :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <WebView ... android:WebView.MixedContentMode="AlwaysAllow" />
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

webView.On<Android>().SetMixedContentMode(MixedContentHandling.AlwaysAllow);
```

Le `WebView.On<Android>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur Android. Le [ `WebView.SetMixedContentMode` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.WebView.SetMixedContentMode(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.WebView},Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling)) (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour contrôler si le contenu mixte peut être affiché, avec le [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) énumération en fournissant trois valeurs possibles :

- [`AlwaysAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.AlwaysAllow) – Indique que le [ `WebView` ](xref:Xamarin.Forms.WebView) permettra une origine HTTPS charger le contenu à partir d’une origine HTTP.
- [`NeverAllow`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.NeverAllow) – Indique que le [ `WebView` ](xref:Xamarin.Forms.WebView) n’autorise pas une origine HTTPS charger le contenu à partir d’une origine HTTP.
- [`CompatibilityMode`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling.CompatibilityMode) – Indique que le [ `WebView` ](xref:Xamarin.Forms.WebView) tentera d’être compatible avec l’approche de la dernière version du navigateur web appareil. Partie du contenu HTTP peut-être être autorisé à être chargés par une entité origin HTTPS et autres types de contenu seront bloqués. Les types de contenu qui sont bloqués ou autorisés peuvent changer avec chaque version de système d’exploitation.

Le résultat qui est spécifié [ `MixedContentHandling` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.MixedContentHandling) valeur est appliquée à la [ `WebView` ](xref:Xamarin.Forms.WebView), qui contrôle si le contenu mixte peut s’afficher :

[![WebView mixte de gestion de contenu spécifique à la plateforme](webview-mixed-content-images/webview-mixedcontent.png "WebView mixte de gestion de contenu spécifique à la plateforme")](webview-mixed-content-images/webview-mixedcontent-large.png#lightbox "WebView mixte de gestion de contenu spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
