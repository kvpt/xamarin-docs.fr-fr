---
title: Mode d’exécution WebView sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Windows qui définit le thread sur lequel un WebView héberge son contenu.
ms.prod: xamarin
ms.assetid: 4D3C4112-0FF6-47F8-BC22-579D92032807
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/10/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 314851c4697e95ef8662710c986d0b175249fad6
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940775"
---
# <a name="webview-execution-mode-on-windows"></a>Mode d’exécution WebView sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce paramètre spécifique à la plateforme définit le thread sur lequel un [`WebView`](xref:Xamarin.Forms.WebView) héberge son contenu. Il est consommé en XAML en affectant `WebView.ExecutionMode` à la propriété pouvant être liée la `WebViewExecutionMode` valeur d’énumération :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <WebView ... windows:WebView.ExecutionMode="SeparateThread" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

WebView webView = new Xamarin.Forms.WebView();
webView.On<Windows>().SetExecutionMode(WebViewExecutionMode.SeparateThread);
```

La `WebView.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. La `WebView.SetExecutionMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour définir le thread sur lequel un `WebView` héberge son contenu, avec l' `WebViewExecutionMode` énumération qui fournit trois valeurs possibles :

- `SameThread` indique que le contenu est hébergé sur le thread d’interface utilisateur. Il s’agit de la valeur par défaut pour le `WebView` sur Windows.
- `SeparateThread` indique que le contenu est hébergé sur un thread d’arrière-plan.
- `SeparateProcess` indique que le contenu est hébergé sur un processus distinct du processus de l’application. Il n’existe pas de processus distinct pour chaque instance WebView. par conséquent, toutes les instances WebView d’une application partagent le même processus distinct.

En outre, la `GetExecutionMode` méthode peut être utilisée pour retourner le en cours `WebViewExecutionMode` pour `WebView` .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
