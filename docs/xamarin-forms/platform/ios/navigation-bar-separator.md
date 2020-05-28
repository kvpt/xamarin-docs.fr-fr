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
ms.openlocfilehash: 39d203cf0fb7fff026106d98cfb512aad42f83d2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128243"
---
# <a name="navigationpage-bar-separator-on-ios"></a>Séparateur de barre NavigationPage sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS masque la ligne de séparation et l’ombre situées en bas de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Il est consommé en XAML en affectant [`NavigationPage.HideNavigationBarSeparator`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.HideNavigationBarSeparatorProperty) à la propriété pouvant être liée la valeur `false` :

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ios:NavigationPage.HideNavigationBarSeparator="true">

</NavigationPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;

public class iOSTitleViewNavigationPageCS : Xamarin.Forms.NavigationPage
{
    public iOSTitleViewNavigationPageCS()
    {
        On<iOS>().SetHideNavigationBarSeparator(true);
    }
}
```

La `NavigationPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `NavigationPage.SetHideNavigationBarSeparator` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetHideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, System. Boolean)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, permet de contrôler si le séparateur de barre de navigation est masqué. En outre, le [ `NavigationPage.HideNavigationBarSeparator` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. HideNavigationBarSeparator ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}))) peut être utilisé pour retourner une valeur indiquant si le séparateur de la barre de navigation est masqué.

Le résultat est que le séparateur de barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) peut être masqué :

![](navigation-bar-separator-images/navigationpage-hideseparatorbar.png "NavigationPage navigation bar hidden")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
