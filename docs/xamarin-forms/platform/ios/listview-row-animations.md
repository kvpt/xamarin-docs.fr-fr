---
title: Animations de lignes ListView sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle si les animations de lignes sont désactivées lors de la mise à jour de la collection d’éléments ListView.
ms.prod: xamarin
ms.assetid: E8F5103F-4D8E-4A5A-A16C-7FA14EE786AC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/21/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7f64dcd0bfcd68f7e5145ce8191dcd4a580fb2a6
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93372507"
---
# <a name="listview-row-animations-on-ios"></a>Animations de lignes ListView sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme iOS détermine si les animations de lignes sont désactivées lors de la [`ListView`](xref:Xamarin.Forms.ListView) mise à jour de la collection d’éléments. Il est consommé en XAML en affectant `ListView.RowAnimationsEnabled` à la propriété pouvant être liée la valeur `false` :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.RowAnimationsEnabled="false">
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

listView.On<iOS>().SetRowAnimationsEnabled(false);
```

La `ListView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `ListView.SetRowAnimationsEnabled` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si les animations de lignes sont désactivées lors de la [`ListView`](xref:Xamarin.Forms.ListView) mise à jour de la collection d’éléments. En outre, la `ListView.GetRowAnimationsEnabled` méthode peut être utilisée pour retourner si les animations de lignes sont désactivées sur le `ListView` .

> [!NOTE]
> [`ListView`](xref:Xamarin.Forms.ListView) les animations de lignes sont activées par défaut. Par conséquent, une animation se produit lorsqu’une nouvelle ligne est insérée dans un `ListView` .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)