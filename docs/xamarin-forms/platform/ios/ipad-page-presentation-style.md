---
title: Style de présentation de page modale sur iPad
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser la plateforme iOS-specific définit le style de présentation d’une page modale sur un iPad.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c8962c46e0b496844bd3fc00346a117b6753f818
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651943"
---
# <a name="ipad-modal-page-presentation-style"></a>Style de présentation de page modale sur iPad

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour définir le style de présentation d’une page modale sur un iPad. Elle est consommée dans XAML en définissant le `Page.ModalPresentationStyle` propriété pouvant être liée à un `UIModalPresentationStyle` valeur d’énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="FormSheet">
    ...
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de C# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.FormSheet);
        ...
    }
}
```

Le `Page.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. Le `Page.SetModalPresentationStyle` (méthode), dans le [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisé pour définir le style de présentation modale sur un [ `Page` ](xref:Xamarin.Forms.Page) en spécifiant une des opérations suivantes `UIModalPresentationStyle` énumération valeurs :

- `FullScreen`, qui définit le style de présentation modale pour englober la totalité de l’écran. Par défaut, les pages modales sont affichés à l’aide de ce style de présentation.
- `FormSheet`, qui définit le style de présentation modale centrée sur et inférieure à l’écran.

En outre, le `GetModalPresentationStyle` méthode peut être utilisée pour récupérer la valeur actuelle de la `UIModalPresentationStyle` énumération est appliquée à la [ `Page` ](xref:Xamarin.Forms.Page).

Le résultat est que le style de présentation modale sur un [ `Page` ](xref:Xamarin.Forms.Page) peuvent être définies :

[![](page-presentation-style-images/modal-presentation-style-small.png "Styles de présentation modale sur un iPad")](page-presentation-style-images/modal-presentation-style-large.png#lightbox "modale Styles de présentation sur un iPad")

> [!NOTE]
> Les pages qui utilisent cette plateforme spécifique pour définir le style de présentation modale doivent utiliser la navigation modale. Pour plus d’informations, consultez [Pages modales Xamarin.Forms](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
