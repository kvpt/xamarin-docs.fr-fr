---
title: Couleur d’arrière-plan de cellule sur iOS
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui définit la couleur d’arrière-plan par défaut des cellules sur iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 38bc85cb06f89ecbc673d8f159c716136d183570
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93373222"
---
# <a name="cell-background-color-on-ios"></a>Couleur d’arrière-plan de cellule sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce paramètre spécifique à la plateforme iOS définit la couleur d’arrière-plan par défaut des [`Cell`](xref:Xamarin.Forms.Cell) instances. Il est consommé en XAML en affectant `Cell.DefaultBackgroundColor` à la propriété pouvant être liée la valeur [`Color`](xref:Xamarin.Forms.Color) :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  IsGroupingEnabled="true">
            <ListView.GroupHeaderTemplate>
                <DataTemplate>
                    <ViewCell ios:Cell.DefaultBackgroundColor="Teal">
                        <Label Margin="10,10"
                               Text="{Binding Key}"
                               FontAttributes="Bold" />
                    </ViewCell>
                </DataTemplate>
            </ListView.GroupHeaderTemplate>
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

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

La `ListView.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Cell.SetDefaultBackgroundColor` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, définit la couleur d’arrière-plan de la cellule sur un spécifié [`Color`](xref:Xamarin.Forms.Color) . En outre, la `Cell.DefaultBackgroundColor` méthode peut être utilisée pour récupérer la couleur d’arrière-plan de la cellule active.

Le résultat est que la couleur d’arrière-plan dans un [`Cell`](xref:Xamarin.Forms.Cell) peut être définie sur un spécifique [`Color`](xref:Xamarin.Forms.Color) :

[![Capture d’écran des cellules d’en-tête de groupe bleu-vert, sur iOS](cell-background-color-images/group-header-cell-color.png "ListView avec cellules d’en-tête de groupe bleu-vert")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView avec cellules d’en-tête de groupe bleu-vert")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)