---
title: Positionnement de la barre d’outils de la page sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser la propre plateforme Windows qui modifie le positionnement d’une barre d’outils sur une page.
ms.prod: xamarin
ms.assetid: 99F29E95-0C36-4A3B-BDE8-7E9F119E844E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 4caf352024d7ae2a89d49ff021f0f90f35fdd8eb
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372208"
---
# <a name="page-toolbar-placement-on-windows"></a>Positionnement de la barre d’outils de la page sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce plateforme Windows universelle spécifique à la plateforme est utilisé pour modifier le positionnement d’une barre d’outils sur un [`Page`](xref:Xamarin.Forms.Page) et est consommé en XAML en affectant [`Page.ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.Page.ToolbarPlacementProperty) à la propriété jointe la valeur de l' [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) énumération :

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core"
            windows:Page.ToolbarPlacement="Bottom">
  ...
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

page.On<Windows>().SetToolbarPlacement(ToolbarPlacement.Bottom);
```

La `Page.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Windows. [ `Page.SetToolbarPlacement` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. page. SetToolbarPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Page}, Xamarin.Forms . La méthode PlatformConfiguration. WindowsSpecific. ToolbarPlacement)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour définir le positionnement de la barre d’outils, avec l' [`ToolbarPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement) énumération fournissant trois valeurs : [`Default`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Default) , [`Top`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Top) et [`Bottom`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ToolbarPlacement.Bottom) .

Le résultat est que la position de la barre d’outils spécifiée est appliquée à l' [`Page`](xref:Xamarin.Forms.Page) instance :

[![Positionnement de la barre d’outils spécifique à la plateforme](page-toolbar-placement-images/toolbar-placement.png)](page-toolbar-placement-images/toolbar-placement-large.png#lightbox "Platform-Specific de positionnement de la barre d’outils")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)