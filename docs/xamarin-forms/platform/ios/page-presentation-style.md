---
title: Style de présentation de page modal sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le propre à la plateforme iOS qui définit le style de présentation d’une page modale.
ms.prod: xamarin
ms.assetid: C791F7CF-330A-44BA-987A-4CFCCBB9278B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/02/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 20bf6d3d8c802c1d4919cd16885d3939cb0102aa
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86930622"
---
# <a name="modal-page-presentation-style-on-ios"></a>Style de présentation de page modal sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour définir le style de présentation d’une page modale et peut également être utilisé pour afficher les pages modales qui ont des arrière-plans transparents. Il est consommé en XAML en affectant `Page.ModalPresentationStyle` à la propriété pouvant être liée la `UIModalPresentationStyle` valeur d’énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             ios:Page.ModalPresentationStyle="OverFullScreen">
    ...
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSModalFormSheetPageCS : ContentPage
{
    public iOSModalFormSheetPageCS()
    {
        On<iOS>().SetModalPresentationStyle(UIModalPresentationStyle.OverFullScreen);
        ...
    }
}
```

La `Page.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Page.SetModalPresentationStyle` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour définir le style de présentation modale sur un [`Page`](xref:Xamarin.Forms.Page) en spécifiant l’une des `UIModalPresentationStyle` valeurs d’énumération suivantes :

- `FullScreen`, qui définit le style de présentation modal pour englober la totalité de l’écran. Par défaut, les pages modales sont affichées à l’aide de ce style de présentation.
- `FormSheet`, qui définit le style de présentation modal à centrer sur une valeur inférieure à l’écran.
- `Automatic`, qui définit le style de présentation modal à la valeur par défaut choisie par le système. Pour la plupart des contrôleurs d’affichage, `UIKit` mappe ce à `UIModalPresentationStyle.PageSheet` , mais certains contrôleurs de vue système peuvent le mapper à un style différent.
- `OverFullScreen`, qui définit le style de présentation modale pour couvrir l’écran.
- `PageSheet`, qui définit le style de présentation modale pour couvrir le contenu sous-jacent.

En outre, la `GetModalPresentationStyle` méthode peut être utilisée pour récupérer la valeur actuelle de l' `UIModalPresentationStyle` énumération appliquée au [`Page`](xref:Xamarin.Forms.Page) .

Le résultat est que le style de présentation modal sur un [`Page`](xref:Xamarin.Forms.Page) peut être défini :

[![Styles de présentation modaux](page-presentation-style-images/modal-presentation-style-small.png)](page-presentation-style-images/modal-presentation-style-large.png#lightbox "Styles de présentation modaux")

> [!NOTE]
> Les pages qui utilisent ce propre à la plateforme pour définir le style de présentation modal doivent utiliser la navigation modale. Pour plus d’informations, consultez [ Xamarin.Forms pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
