---
title: Style d’en-tête de groupe ListView sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si les cellules d’en-tête ListView sont flottantes lors du défilement.
ms.prod: xamarin
ms.assetid: 099B2C7F-727F-4BCF-903B-87E728108C24
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f40737799f63c6e0c61fcc6f4f59584222a49d6d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68648327"
---
# <a name="listview-group-header-style-on-ios"></a>Style d’en-tête de groupe ListView sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS [`ListView`](xref:Xamarin.Forms.ListView) indique si les cellules d’en-tête sont flottantes lors du défilement. Il est consommé en XAML en affectant `ListView.GroupHeaderStyle` à la propriété pouvant être liée la valeur `GroupHeaderStyle` de l’énumération :

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

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetGroupHeaderStyle(GroupHeaderStyle.Grouped);
```

Le `ListView.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. La `ListView.SetGroupHeaderStyle` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour [`ListView`](xref:Xamarin.Forms.ListView) contrôler si les cellules d’en-tête sont flottantes lors du défilement. L' `GroupHeaderStyle` énumération fournit deux valeurs possibles :

- `Plain`: indique que les cellules d’en- [`ListView`](xref:Xamarin.Forms.ListView) tête sont flottantes lorsque le est défilé (valeur par défaut).
- `Grouped`: indique que les cellules d’en-tête ne [`ListView`](xref:Xamarin.Forms.ListView) flottent pas lorsque le défile.

En outre, la `ListView.GetGroupHeaderStyle` méthode peut être utilisée pour retourner le `GroupHeaderStyle` appliqué au [`ListView`](xref:Xamarin.Forms.ListView).

Le résultat est qu’une valeur `GroupHeaderStyle` spécifiée est appliquée au, [`ListView`](xref:Xamarin.Forms.ListView)qui contrôle si les cellules d’en-tête sont flottantes pendant le défilement :

[![Capture d’écran des cellules d’en-tête ListView flottantes et non flottantes, sur iOS](listview-group-header-style-images/group-header-styles.png "ListView avec des cellules d’en-tête flottantes et non flottantes")](listview-group-header-style-images/group-header-styles-large.png#lightbox "ListView avec des cellules d’en-tête flottantes et non flottantes")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
