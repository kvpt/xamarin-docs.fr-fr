---
title: Style d’en-tête de groupe ListView sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si les cellules d’en-tête ListView sont flottantes lors du défilement.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68648327"
---
# <a name="listview-group-header-style-on-ios"></a>Style d’en-tête de groupe ListView sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS indique si les cellules d’en-tête sont [`ListView`](xref:Xamarin.Forms.ListView) flottantes lors du défilement. Il est consommé en XAML en affectant à la `ListView.GroupHeaderStyle` propriété pouvant être liée la valeur de l’énumération `GroupHeaderStyle` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.GroupHeaderStyle="Grouped">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

La méthode `ListView.On<iOS>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La méthode `ListView.SetGroupHeaderStyle`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) , est utilisée pour contrôler si [`ListView`](xref:Xamarin.Forms.ListView) cellules d’en-tête sont flottantes lors du défilement. L’énumération `GroupHeaderStyle` fournit deux valeurs possibles :

- `Plain` : indique que les cellules d’en-tête sont flottantes lors du défilement de la [`ListView`](xref:Xamarin.Forms.ListView) (valeur par défaut).
- `Grouped` : indique que les cellules d’en-tête ne flottent pas lors du défilement du [`ListView`](xref:Xamarin.Forms.ListView) .

En outre, la méthode `ListView.GetGroupHeaderStyle` peut être utilisée pour retourner le `GroupHeaderStyle` appliqué au [`ListView`](xref:Xamarin.Forms.ListView).

Le résultat est qu’une valeur de `GroupHeaderStyle` spécifiée est appliquée au [`ListView`](xref:Xamarin.Forms.ListView), qui contrôle si les cellules d’en-tête sont flottantes pendant le défilement :

[![Capture d’écran des cellules d’en-tête ListView flottantes et non flottantes, sur iOS](listview-group-header-style-images/group-header-styles.png "ListView avec des cellules d’en-tête flottantes et non flottantes")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView avec des cellules d’en-tête flottantes et non flottantes")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
