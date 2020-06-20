---
title: Style de séparateur ListView sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si le séparateur entre les cellules d’un ListView utilise la largeur complète de ListView.
ms.prod: xamarin
ms.assetid: A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c8d660896684283ba9b40cde168adbfe30ca0c51
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135991"
---
# <a name="listview-separator-style-on-ios"></a>Style de séparateur ListView sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme iOS détermine si le séparateur entre les cellules d’un objet [`ListView`](xref:Xamarin.Forms.ListView) utilise la largeur complète du `ListView` . Il est consommé en XAML en affectant [`ListView.SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) à la propriété jointe une valeur de l' [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
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

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

La `ListView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `ListView.SetSeparatorStyle` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. ListView. SetSeparatorStyle ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . ListView}, Xamarin.Forms . La méthode PlatformConfiguration. iOSSpecific. SeparatorStyle)), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si le séparateur entre les cellules dans [`ListView`](xref:Xamarin.Forms.ListView) utilise la largeur complète du `ListView` , avec l' [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) énumération qui fournit deux valeurs possibles :

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default): indique le comportement de séparateur iOS par défaut. Il s’agit du comportement par défaut dans Xamarin.Forms .
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth): indique que les séparateurs seront dessinés d’un bord de `ListView` à l’autre.

Le résultat est qu’une [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valeur spécifiée est appliquée au [`ListView`](xref:Xamarin.Forms.ListView) , qui contrôle la largeur du séparateur entre les cellules :

![](listview-separator-style-images/listview-separatorstyle.png "ListView SeparatorStyle Platform-Specific")

> [!NOTE]
> Une fois le style de séparateur défini sur `FullWidth` , il ne peut pas être rétabli au moment de l' `Default` exécution.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
