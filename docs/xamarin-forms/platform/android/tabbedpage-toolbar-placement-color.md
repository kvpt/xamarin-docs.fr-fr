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
- ':::no-loc(Xamarin.Forms):::'
- ':::no-loc(Xamarin.Essentials):::'
ms.openlocfilehash: e32c516c4a0a8b12bd8a76478557905149890c6a
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997330"
---
# <a name="tabbedpage-toolbar-placement-and-color-on-android"></a>Couleur et positionnement de la barre d’outils TabbedPage sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

> [!IMPORTANT]
> Les caractéristiques spécifiques à la plateforme qui définissent la couleur de la barre d’outils d’un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) sont désormais obsolètes et ont été remplacées par les [`SelectedTabColor`](xref::::no-loc(Xamarin.Forms):::.TabbedPage.SelectedTabColor) [`UnselectedTabColor`](xref::::no-loc(Xamarin.Forms):::.TabbedPage.UnselectedTabColor) Propriétés et. Pour plus d’informations, consultez [créer un TabbedPage](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md#create-a-tabbedpage).

Ces caractéristiques spécifiques à la plateforme sont utilisées pour définir l’emplacement et la couleur de la barre d’outils sur un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) . Elles sont consommées en XAML en affectant [`TabbedPage.ToolbarPlacement`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.ToolbarPlacementProperty) à la propriété jointe une valeur de l' [`ToolbarPlacement`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) énumération, et aux [`TabbedPage.BarItemColor`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.BarItemColorProperty) [`TabbedPage.BarSelectedItemColor`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.TabbedPage.BarSelectedItemColorProperty) propriétés jointes et à [`Color`](xref::::no-loc(Xamarin.Forms):::.Color) :

```xaml
<TabbedPage ...
            xmlns:android="clr-namespace::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;assembly=:::no-loc(Xamarin.Forms):::.Core"
            android:TabbedPage.ToolbarPlacement="Bottom"
            android:TabbedPage.BarItemColor="Black"
            android:TabbedPage.BarSelectedItemColor="Red">
    ...
</TabbedPage>
```

Elles peuvent également être consommées à partir de C# à l’aide de l’API Fluent :

```csharp
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration;
using :::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific;
...

On<Android>().SetToolbarPlacement(ToolbarPlacement.Bottom)
             .SetBarItemColor(Color.Black)
             .SetBarSelectedItemColor(Color.Red);
```

La `TabbedPage.On<Android>` méthode spécifie que ces caractéristiques spécifiques à la plateforme s’exécuteront uniquement sur Android. [ `TabbedPage.SetToolbarPlacement` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetToolbarPlacement ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage}, :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. ToolbarPlacement)), dans l' [`:::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisé pour définir l’emplacement de la barre d’outils sur un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) , avec l' [`ToolbarPlacement`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement) énumération fournissant les valeurs suivantes :

- [`Default`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Default): indique que la barre d’outils est placée à l’emplacement par défaut sur la page. Il s’agit du haut de la page sur les téléphones et du bas de la page sur les autres idiomes d’appareils.
- [`Top`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Top): indique que la barre d’outils est placée en haut de la page.
- [`Bottom`](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.ToolbarPlacement.Bottom): indique que la barre d’outils est placée en bas de la page.

En outre, le [ `TabbedPage.SetBarItemColor` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage}, :::no-loc(Xamarin.Forms)::: . Color)) et [ `TabbedPage.SetBarSelectedItemColor` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. SetBarSelectedItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage}, :::no-loc(Xamarin.Forms)::: . Color)) sont utilisées pour définir la couleur des éléments de barre d’outils et des éléments de barre d’outils sélectionnés, respectivement.

> [!NOTE]
> [ `GetToolbarPlacement` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. GetToolbarPlacement ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})), [ `GetBarItemColor` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})) et [ `GetBarSelectedItemColor` ] (XREF : :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. AndroidSpecific. TabbedPage. GetBarSelectedItemColor ( :::no-loc(Xamarin.Forms)::: . IPlatformElementConfiguration { :::no-loc(Xamarin.Forms)::: . PlatformConfiguration. Android, :::no-loc(Xamarin.Forms)::: . TabbedPage})), vous pouvez utiliser les méthodes pour récupérer le positionnement et la couleur de la [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) barre d’outils.

Le résultat est que le positionnement de la barre d’outils, la couleur des éléments de la barre d’outils et la couleur de l’élément de barre d’outils sélectionné peuvent être définis sur un [`TabbedPage`](xref::::no-loc(Xamarin.Forms):::.TabbedPage) :

![Configuration de la barre d’outils TabbedPage](tabbedpage-toolbar-placement-color-images/tabbedpage-toolbar-placement.png)

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref::::no-loc(Xamarin.Forms):::.PlatformConfiguration.AndroidSpecific.AppCompat)
