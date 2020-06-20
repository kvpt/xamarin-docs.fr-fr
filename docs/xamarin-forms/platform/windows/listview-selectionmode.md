---
title: Contrôle SelectionMode de ListView sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui contrôle si les éléments d’un ListView peuvent répondre aux gestes TAP.
ms.prod: xamarin
ms.assetid: 57EF3A7F-1407-4B31-AE21-D149293D4228
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6c73f46d2845be7bb54e24cd02ec22f3c2cd386d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137243"
---
# <a name="listview-selectionmode-on-windows"></a>Contrôle SelectionMode de ListView sur Windows

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Sur la plateforme Windows universelle, par défaut, Xamarin.Forms [`ListView`](xref:Xamarin.Forms.ListView) utilise l' `ItemClick` événement natif pour répondre à l’interaction, plutôt qu’à l' `Tapped` événement natif. Cela fournit des fonctionnalités d’accessibilité afin que le narrateur Windows et le clavier puissent interagir avec le `ListView` . Toutefois, il affiche également les gestes TAP dans le `ListView` inopérant.

Ce plateforme Windows universelle contrôle spécifique à la plateforme si les éléments d’un [`ListView`](xref:Xamarin.Forms.ListView) peuvent répondre aux gestes TAP, et par conséquent si le natif `ListView` déclenche l' `ItemClick` `Tapped` événement ou. Il est consommé en XAML en affectant [`ListView.SelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListView.SelectionModeProperty) à la propriété jointe une valeur de l' [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) énumération :

```xaml
<ContentPage ...
             xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <ListView ... windows:ListView.SelectionMode="Inaccessible">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

listView.On<Windows>().SetSelectionMode(ListViewSelectionMode.Inaccessible);
```

La `ListView.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. [ `ListView.SetSelectionMode` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. SetSelectionMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView}, Xamarin.Forms . La méthode PlatformConfiguration. WindowsSpecific. ListViewSelectionMode)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisée pour contrôler si les éléments d’un [`ListView`](xref:Xamarin.Forms.ListView) peuvent répondre aux gestes TAP, avec l' [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) énumération qui fournit deux valeurs possibles :

- [`Accessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Accessible): indique que le `ListView` déclenche l’événement natif `ItemClick` pour gérer l’interaction, et fournit donc des fonctionnalités d’accessibilité. Par conséquent, le narrateur Windows et le clavier peuvent interagir avec le `ListView` . Toutefois, les éléments dans le `ListView` ne peuvent pas répondre aux gestes TAP. Il s’agit du comportement par défaut pour `ListView` les instances sur le plateforme Windows universelle.
- [`Inaccessible`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode.Inaccessible): indique que le `ListView` déclenchera l' `Tapped` événement natif pour gérer l’interaction. Par conséquent, les éléments dans `ListView` peuvent répondre aux gestes TAP. Toutefois, il n’existe aucune fonctionnalité d’accessibilité et, par conséquent, le narrateur Windows et le clavier ne peuvent pas interagir avec le `ListView` .

> [!NOTE]
> Les `Accessible` modes de sélection et s’excluent `Inaccessible` mutuellement. vous devez choisir entre un accessible [`ListView`](xref:Xamarin.Forms.ListView) ou un `ListView` qui peut répondre aux gestes TAP.

En outre, le [ `GetSelectionMode` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. ListView. GetSelectionMode ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . ListView}))) peut être utilisé pour retourner le actuel [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) .

Le résultat est qu’un spécifié [`ListViewSelectionMode`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.ListViewSelectionMode) est appliqué au [`ListView`](xref:Xamarin.Forms.ListView) , qui contrôle si les éléments dans `ListView` peuvent répondre aux mouvements de TAP, et par conséquent si le natif `ListView` déclenche l' `ItemClick` `Tapped` événement ou.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)
