---
title: Barre de navigation MasterDetailPage sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui réduit la barre de navigation sur un MasterDetailPage.
ms.prod: xamarin
ms.assetid: 0E7436C9-FA3E-40CD-801C-3F7ED95C412D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 876e5449e17276efc3b368ab7d2c9e57365f948f
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937824"
---
# <a name="masterdetailpage-navigation-bar-on-windows"></a>Barre de navigation MasterDetailPage sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme est utilisé pour réduire la barre de navigation sur un [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) et est consommé en XAML en définissant les [`MasterDetailPage.CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapseStyleProperty) [`MasterDetailPage.CollapsedPaneWidth`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.MasterDetailPage.CollapsedPaneWidthProperty) propriétés jointes et :

```xaml
<MasterDetailPage ...
                  xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
                  windows:MasterDetailPage.CollapseStyle="Partial"
                  windows:MasterDetailPage.CollapsedPaneWidth="48">
  ...
</MasterDetailPage>

```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetCollapseStyle(CollapseStyle.Partial).CollapsedPaneWidth(148);
```

La `MasterDetailPage.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Windows. [ `Page.SetCollapseStyle` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. MasterDetailPage. SetCollapseStyle ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . MasterDetailPage}, Xamarin.Forms . La méthode PlatformConfiguration. WindowsSpecific. CollapseStyle)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour spécifier le style de réduction, avec l' [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) énumération fournissant deux valeurs : [`Full`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Full) et [`Partial`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle.Partial) . [ `MasterDetailPage.CollapsedPaneWidth` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. MasterDetailPage. CollapsedPaneWidth ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . MasterDetailPage}, System. double)) est utilisée pour spécifier la largeur d’une barre de navigation partiellement réduite.

Le résultat est qu’un spécifié [`CollapseStyle`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.CollapseStyle) est appliqué à l' [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) instance, avec la largeur également spécifiée :

[![Barre de navigation réduite spécifique à la plateforme](masterdetailpage-navigation-bar-images/collapsed-navigation-bar.png)](masterdetailpage-navigation-bar-images/collapsed-navigation-bar-large.png#lightbox "Barre de navigation réduite spécifique à la plateforme")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
