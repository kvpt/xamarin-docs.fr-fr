---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 594e436c9db7c123fea4f9aa262c9d27af765b07
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136004"
---
# <a name="listview-row-animations-on-ios"></a>Animations de lignes ListView sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

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
> [`ListView`](xref:Xamarin.Forms.ListView)les animations de lignes sont activées par défaut. Par conséquent, une animation se produit lorsqu’une nouvelle ligne est insérée dans un `ListView` .

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
