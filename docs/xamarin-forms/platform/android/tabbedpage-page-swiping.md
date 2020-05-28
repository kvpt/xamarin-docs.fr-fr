---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61c0137788303363769fdec80a16542e2d8bea5e
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128607"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Page TabbedPage de balayage sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour activer le balayage avec un mouvement d’doigt horizontal entre les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Il est consommé en XAML en affectant [`TabbedPage.IsSwipePagingEnabled`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) une valeur à la propriété jointe `boolean` :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

La `TabbedPage.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `TabbedPage.SetIsSwipePagingEnabled` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled ( Xamarin.Forms . BindableObject, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour activer le balayage entre les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . En outre, la `TabbedPage` classe de l' `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` espace de noms possède également un [ `EnableSwipePaging` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) qui active ce spécifique à la plateforme et un [ `DisableSwipePaging` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) qui désactive ce spécifique à la plateforme. La [`TabbedPage.OffscreenPageLimit`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propriété jointe et [ `SetOffscreenPageLimit` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit ( Xamarin.Forms . BindableObject, System. Int32)) sont utilisées pour définir le nombre de pages qui doivent être conservées dans un état d’inactivité de chaque côté de la page actuelle.

Le résultat est que la pagination par balayage des pages affichées par un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) est activée :

![](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
