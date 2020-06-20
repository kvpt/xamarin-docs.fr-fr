---
title: Ombre MasterDetailPage sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si une ombre est appliquée à la page de détails d’un MasterDetailPage, lors de la révélation de la page maître.
ms.prod: xamarin
ms.assetid: FB907EA2-C00A-43A5-84B1-A43584C867A5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: aaf94536d41da47aec10fc655f9d053b753da5a2
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135965"
---
# <a name="masterdetailpage-shadow-on-ios"></a>Ombre MasterDetailPage sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle propre à la plateforme détermine si une ombre est appliquée à la page de détails d’une [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , lors de la révélation de la page maître. Il est consommé en XAML en affectant `MasterDetailPage.ApplyShadow` à la propriété pouvant être liée la valeur `true` :

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                  ios:MasterDetailPage.ApplyShadow="true">
    ...
</MasterDetailPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSMasterDetailPageCS : MasterDetailPage
{
    public iOSMasterDetailPageCS(ICommand restore)
    {
        On<iOS>().SetApplyShadow(true);
        // ...
    }
}
```

La `MasterDetailPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `MasterDetailPage.SetApplyShadow` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si une ombre est appliquée à la page de détails d’une [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) , lors de la révélation de la page maître. En outre, la `GetApplyShadow` méthode peut être utilisée pour déterminer si l’ombre est appliquée à la page de détails d’un `MasterDetailPage` .

Le résultat est que l’ombrage de la page de détails d’un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) peut être appliqué lors de la révélation de la page maître :

[![Capture d’écran d’un MasterDetailPage avec et sans ombre](masterdetailpage-shadow-images/shadow.png "MasterDetailPage avec et sans ombre")](masterdetailpage-shadow-images/shadow-large.png#lightbox "MasterDetailPage avec et sans ombre")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
