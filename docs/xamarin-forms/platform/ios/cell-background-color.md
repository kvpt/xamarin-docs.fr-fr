---
title: Couleur d’arrière-plan de cellule sur iOS
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser le spécifique à la plateforme iOS qui définit la couleur d’arrière-plan par défaut des cellules sur iOS.
ms.prod: xamarin
ms.assetid: 2A3FDACF-5AE2-40DE-8488-6FE41733712F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 24276dce97e4935ba41d7012cf6a9aa8fa2658a8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "68651378"
---
# <a name="cell-background-color-on-ios"></a>Couleur d’arrière-plan de cellule sur iOS

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce paramètre spécifique à la plateforme iOS définit la couleur d' [`Cell`](xref:Xamarin.Forms.Cell) arrière-plan par défaut des instances. Il est consommé en XAML en affectant `Cell.DefaultBackgroundColor` [`Color`](xref:Xamarin.Forms.Color)à la propriété pouvant être liée la valeur :

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

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var viewCell = new ViewCell { View = ... };
viewCell.On<iOS>().SetDefaultBackgroundColor(Color.Teal);
```

Le `ListView.On<iOS>` méthode spécifie que cette plateforme spécifique s’exécute uniquement sur iOS. La `Cell.SetDefaultBackgroundColor` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, définit la couleur d’arrière- [`Color`](xref:Xamarin.Forms.Color)plan de la cellule sur un spécifié. En outre, la `Cell.DefaultBackgroundColor` méthode peut être utilisée pour récupérer la couleur d’arrière-plan de la cellule active.

Le résultat est que la couleur d’arrière- [`Cell`](xref:Xamarin.Forms.Cell) plan dans un peut être définie [`Color`](xref:Xamarin.Forms.Color)sur un spécifique :

[![Capture d’écran des cellules d’en-tête de groupe bleu-vert, sur iOS](cell-background-color-images/group-header-cell-color.png "ListView avec cellules d’en-tête de groupe bleu-vert")](cell-background-color-images/group-header-cell-color-large.png#lightbox "ListView avec cellules d’en-tête de groupe bleu-vert")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
