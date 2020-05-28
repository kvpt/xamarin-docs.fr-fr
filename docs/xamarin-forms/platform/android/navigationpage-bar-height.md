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
ms.openlocfilehash: 2dcabe3c0067734250834c2927fd4cbb83906943
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128789"
---
# <a name="navigationpage-bar-height-on-android"></a>Hauteur de la barre NavigationPage sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android définit la hauteur de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Il est consommé en XAML en affectant [`NavigationPage.BarHeight`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat.NavigationPage.BarHeightProperty) à la propriété pouvant être liée la valeur d’un entier :

```xaml
<NavigationPage ...
                xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;assembly=Xamarin.Forms.Core"
                android:NavigationPage.BarHeight="450">
    ...
</NavigationPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat;
...

public class AndroidNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public AndroidNavigationPageCS()
    {
        On<Android>().SetBarHeight(450);
    }
}
```

La `NavigationPage.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur l’application compat Android. [ `NavigationPage.SetBarHeight` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. SetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage}, System. Int32)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espace de noms, est utilisé pour définir la hauteur de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . En outre, le [ `NavigationPage.GetBarHeight` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. AppCompat. NavigationPage. GetBarHeight ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . NavigationPage}))) peut être utilisée pour retourner la hauteur de la barre de navigation dans le `NavigationPage` .

Le résultat est que la hauteur de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) peut être définie :

![](navigationpage-bar-height-images/navigationpage-barheight.png "NavigationPage navigation bar height")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
