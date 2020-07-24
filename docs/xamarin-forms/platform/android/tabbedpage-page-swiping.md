---
title: Page TabbedPage de balayage sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui permet le balayage avec un mouvement d’doigt horizontal entre les pages d’un TabbedPage.
ms.prod: xamarin
ms.assetid: D1C09CCB-7246-41A4-8BD2-FA6FABCF1C72
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: 0043d90e631c19a55b766a877a9cd30316f14650
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997377"
---
# <a name="tabbedpage-page-swiping-on-android"></a>Page TabbedPage de balayage sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour activer le balayage avec un mouvement d’doigt horizontal entre les pages d’un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) . Il est consommé en XAML en affectant [`TabbedPage.IsSwipePagingEnabled`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.IsSwipePagingEnabledProperty) une valeur à la propriété jointe `boolean` :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
            android:TabbedPage.OffscreenPageLimit="2"
            android:TabbedPage.IsSwipePagingEnabled="true">
    ...
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetOffscreenPageLimit(2)
             .SetIsSwipePagingEnabled(true);
```

La `TabbedPage.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. [ `TabbedPage.SetIsSwipePagingEnabled` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetIsSwipePagingEnabled ( :::no-loc(Xamarin.Forms)::: . BindableObject, System. Boolean)), dans l' [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour activer le balayage entre les pages d’un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) . En outre, la `TabbedPage` classe de l' `:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific` espace de noms possède également un [ `EnableSwipePaging` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. EnableSwipePaging ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})) qui active ce spécifique à la plateforme et un [ `DisableSwipePaging` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. DisableSwipePaging ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})) qui désactive ce spécifique à la plateforme. La [`TabbedPage.OffscreenPageLimit`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.OffscreenPageLimitProperty) propriété jointe et [ `SetOffscreenPageLimit` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetOffscreenPageLimit ( :::no-loc(Xamarin.Forms)::: . BindableObject, System. Int32)) sont utilisées pour définir le nombre de pages qui doivent être conservées dans un état d’inactivité de chaque côté de la page actuelle.

Le résultat est que la pagination par balayage des pages affichées par un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) est activée :

![Faire glisser la pagination via un TabbedPage](tabbedpage-page-swiping-images/tabbedpage-swipe.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
