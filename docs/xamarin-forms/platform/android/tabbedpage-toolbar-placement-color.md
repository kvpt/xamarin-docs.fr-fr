---
title: Couleur et positionnement de la barre d’outils TabbedPage sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui définit la position et la couleur de la barre d’outils sur un TabbedPage.
ms.prod: xamarin
ms.assetid: A5C68D6A-9A5F-42EE-845D-1E5B0CB1544E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e2483599687ec735260be162f67c2f3723eaa689
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91562455"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Couleur et positionnement de la barre d’outils TabbedPage sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Les caractéristiques spécifiques à la plateforme qui définissent la couleur de la barre d’outils d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) sont désormais obsolètes et ont été remplacées par les [`SelectedTabColor`](xref:Xamarin.Forms.TabbedPage.SelectedTabColor) [`UnselectedTabColor`](xref:Xamarin.Forms.TabbedPage.UnselectedTabColor) Propriétés et. Pour plus d’informations, consultez [créer un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Ces caractéristiques spécifiques à la plateforme sont utilisées pour définir l’emplacement et la couleur de la barre d’outils sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Elles sont consommées en XAML en affectant [`TabbedPage.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) à la propriété jointe une valeur de l' [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) énumération, et aux [`TabbedPage.BarItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) [`TabbedPage.BarSelectedItemColor`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) propriétés jointes et à [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Elles peuvent également être consommées à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

La `TabbedPage.On<Android>` méthode spécifie que ces caractéristiques spécifiques à la plateforme s’exécuteront uniquement sur Android. [ `TabbedPage.SetToolbarPlacement` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . PlatformConfiguration. AndroidSpecific. ToolbarPlacement)), dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour définir l’emplacement de la barre d’outils sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) , avec l' [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) énumération fournissant les valeurs suivantes :

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default) : indique que la barre d’outils est placée à l’emplacement par défaut sur la page. Il s’agit du haut de la page sur les téléphones et du bas de la page sur les autres idiomes d’appareils.
- [`Top`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top) : indique que la barre d’outils est placée en haut de la page.
- [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom) : indique que la barre d’outils est placée en bas de la page.

En outre, le [ `TabbedPage.SetBarItemColor` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Color)) et [ `TabbedPage.SetBarSelectedItemColor` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage}, Xamarin.Forms . Color)) sont utilisées pour définir la couleur des éléments de barre d’outils et des éléments de barre d’outils sélectionnés, respectivement.

> [!NOTE]
> [ `GetToolbarPlacement` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), [ `GetBarItemColor` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})) et [ `GetBarSelectedItemColor` ] (XREF : Xamarin.Forms . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Android, Xamarin.Forms . TabbedPage})), vous pouvez utiliser les méthodes pour récupérer le positionnement et la couleur de la [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barre d’outils.

Le résultat est que le positionnement de la barre d’outils, la couleur des éléments de la barre d’outils et la couleur de l’élément de barre d’outils sélectionné peuvent être définis sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) :

![Configuration de la barre d’outils TabbedPage](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)