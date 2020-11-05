---
title: Style SearchBar sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le propre à la plateforme iOS qui contrôle si un SearchBar a un arrière-plan.
ms.prod: xamarin
ms.assetid: 3D512DD6-078E-4BC6-926E-62BA6F4DE640
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/05/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9cb48c5ddca6f5e6f6aeba76d27d9cd2c95dd450
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373677"
---
# <a name="searchbar-style-on-ios"></a>Style SearchBar sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme iOS détermine si un [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un arrière-plan. Il est consommé en XAML en affectant `SearchBar.SearchBarStyle` à la propriété pouvant être liée la valeur de l' `UISearchBarStyle` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

La `SearchBar.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `SearchBar.SetSearchBarStyle` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si le [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un arrière-plan. L' `UISearchBarStyle` énumération fournit trois valeurs possibles :

- `Default` indique que le [`SearchBar`](xref:Xamarin.Forms.SearchBar) a le style par défaut. Il s’agit de la valeur par défaut de la `SearchBar.SearchBarStyle` propriété pouvant être liée.
- `Prominent` indique que le [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un arrière-plan translucide et que le champ de recherche est opaque.
- `Minimal` indique que n' [`SearchBar`](xref:Xamarin.Forms.SearchBar) a pas d’arrière-plan et que le champ de recherche est transparent.

En outre, la `SearchBar.GetSearchBarStyle` méthode peut être utilisée pour retourner le `UISearchBarStyle` appliqué au `SearchBar` .

Le résultat est qu’un `UISearchBarStyle` membre spécifié est appliqué à un [`SearchBar`](xref:Xamarin.Forms.SearchBar) , qui contrôle si le `SearchBar` a un arrière-plan :

![Capture d’écran des styles SearchBar, sur iOS](searchbar-style-images/searchbar-styles.png "Styles SearchBar sur iOS")

Les captures d’écran suivantes montrent les `UISearchBarStyle` membres appliqués aux [`SearchBar`](xref:Xamarin.Forms.SearchBar) objets dont la `BackgroundColor` propriété est définie :

![Capture d’écran des styles SearchBar avec la couleur d’arrière-plan, sur iOS](searchbar-style-images/searchbar-background-styles.png "Styles SearchBar avec couleur d’arrière-plan sur iOS")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)