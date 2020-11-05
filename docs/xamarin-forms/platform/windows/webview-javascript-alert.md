---
title: Afficher des alertes JavaScript sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui permet à une WebView d’afficher des alertes JavaScript dans une boîte de dialogue de message UWP.
ms.prod: xamarin
ms.assetid: 95A153A1-72A0-4C0B-A452-ACE966BB12CB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c0a52bc7d0f8b5c8dc07975653c819aa7f3ec2be
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367394"
---
# <a name="webview-javascript-alerts-on-windows"></a>Afficher des alertes JavaScript sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme permet [`WebView`](xref:Xamarin.Forms.WebView) à un d’afficher des alertes JavaScript dans une boîte de dialogue de message UWP. Il est consommé en XAML en affectant [`WebView.IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabledProperty) une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.IsJavaScriptAlertEnabled="true" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var webView = new Xamarin.Forms.WebView
{
  Source = new HtmlWebViewSource
  {
    Html = @"<html><body><button onclick=""window.alert('Hello World from JavaScript');"">Click Me</button></body></html>"
  }
};
webView.On<Windows>().SetIsJavaScriptAlertEnabled(true);
```

La `WebView.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. [ `WebView.SetIsJavaScriptAlertEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. WebView. SetIsJavaScriptAlertEnabled ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . WebView}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, permet de contrôler si les alertes JavaScript sont activées. En outre, la `WebView.SetIsJavaScriptAlertEnabled` méthode peut être utilisée pour basculer les alertes JavaScript en appelant la [`IsJavaScriptAlertEnabled`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.WebView.IsJavaScriptAlertEnabled*) méthode pour retourner si elles sont activées :

```csharp
_webView.On<Windows>().SetIsJavaScriptAlertEnabled(!_webView.On<Windows>().IsJavaScriptAlertEnabled());
```

Le résultat est que les alertes JavaScript peuvent être affichées dans une boîte de dialogue de message UWP :

![Alertes JavaScript WebView spécifiques à la plateforme](webview-javascript-alert-images/webview-javascript-alert.png "Alertes JavaScript WebView spécifiques à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)