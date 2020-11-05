---
title: Style d’en-tête de groupe ListView sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si les cellules d’en-tête ListView sont flottantes lors du défilement.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a856dd74bb319436dbe1c8506d34dfcdb268834
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93374171"
---
# <a name="listview-group-header-style-on-ios"></a>Style d’en-tête de groupe ListView sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS indique si les [`ListView`](xref:Xamarin.Forms.ListView) cellules d’en-tête sont flottantes lors du défilement. Il est consommé en XAML en affectant `ListView.GroupHeaderStyle` à la propriété pouvant être liée la valeur de l' `GroupHeaderStyle` énumération :

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

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

La `ListView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `ListView.SetGroupHeaderStyle` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si les [`ListView`](xref:Xamarin.Forms.ListView) cellules d’en-tête sont flottantes lors du défilement. L' `GroupHeaderStyle` énumération fournit deux valeurs possibles :

- `Plain` : indique que les cellules d’en-tête sont flottantes lorsque le [`ListView`](xref:Xamarin.Forms.ListView) est défilé (valeur par défaut).
- `Grouped` : indique que les cellules d’en-tête ne flottent pas lorsque le [`ListView`](xref:Xamarin.Forms.ListView) défile.

En outre, la `ListView.GetGroupHeaderStyle` méthode peut être utilisée pour retourner le `GroupHeaderStyle` appliqué au [`ListView`](xref:Xamarin.Forms.ListView) .

Le résultat est qu’une `GroupHeaderStyle` valeur spécifiée est appliquée au [`ListView`](xref:Xamarin.Forms.ListView) , qui contrôle si les cellules d’en-tête sont flottantes pendant le défilement :

[![Capture d’écran des cellules d’en-tête ListView flottantes et non flottantes, sur iOS](listview-group-header-style-images/group-header-styles.png "ListView avec des cellules d’en-tête flottantes et non flottantes")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView avec des cellules d’en-tête flottantes et non flottantes")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)