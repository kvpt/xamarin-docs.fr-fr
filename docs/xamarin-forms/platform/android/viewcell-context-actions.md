---
title: Actions de contexte ViewCell sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui active le mode hérité des actions de contexte ViewCell.
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
ms.openlocfilehash: b040c7c5b7f132b0832469efba91dd89d6b2ddbd
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697429"
---
# <a name="viewcell-context-actions-on-android"></a>Actions de contexte ViewCell sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Par défaut, à partir de Xamarin. Forms 4,3, lorsqu’un [`ViewCell`](xref:Xamarin.Forms.ViewCell) dans une application Android définit des actions de contexte pour chaque élément d’une [`ListView`](xref:Xamarin.Forms.ListView), le menu actions de contexte est mis à jour lorsque l’élément sélectionné dans l' `ListView` change. Toutefois, dans les versions précédentes de Xamarin. Forms, le menu actions de contexte n’a pas été mis à jour et ce comportement est appelé `ViewCell` mode hérité. Ce mode hérité peut entraîner un comportement incorrect si un `ListView` utilise un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pour définir ses `ItemTemplate` à partir d' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets qui définissent des actions de contexte différentes.

Ce spécifique à la plateforme Android active le menu actions de contexte [`ViewCell`](xref:Xamarin.Forms.ViewCell) le mode hérité, pour la compatibilité descendante, de sorte que le menu actions de contexte n’est pas mis à jour lorsque l’élément sélectionné dans un [`ListView`](xref:Xamarin.Forms.ListView) change. Il est consommé en XAML en affectant à la propriété `ViewCell.IsContextActionsLegacyModeEnabled` pouvant être liée la valeur `true` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ItemsSource="{Binding Items}">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <ViewCell android:ViewCell.IsContextActionsLegacyModeEnabled="true">
                        <ViewCell.ContextActions>
                            <MenuItem Text="{Binding Item1Text}" />
                            <MenuItem Text="{Binding Item2Text}" />
                        </ViewCell.ContextActions>
                        <Label Text="{Binding Text}" />
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

La méthode `ViewCell.On<Android>` spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La méthode `ViewCell.SetIsContextActionsLegacyModeEnabled`, dans l’espace de noms [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) , est utilisée pour activer le mode hérité du menu actions du contexte [`ViewCell`](xref:Xamarin.Forms.ViewCell) , de sorte que le menu actions de contexte n’est pas mis à jour lorsque l’élément sélectionné dans un [`ListView`](xref:Xamarin.Forms.ListView) change. En outre, la méthode `ViewCell.GetIsContextActionsLegacyModeEnabled` peut être utilisée pour retourner une valeur indiquant si le mode hérité des actions de contexte est activé.

Les captures d’écran suivantes montrent [`ViewCell`](xref:Xamarin.Forms.ViewCell) mode hérité des actions de contexte activé :

![Capture d’écran du mode hérité ViewCell activé, sur Android](viewcell-context-actions-images/legacy-mode-enabled.png "Mode hérité ViewCell activé")

Dans ce mode, les éléments de menu d’action de contexte affichés sont identiques pour les cellules 1 et 2, en dépit des différents éléments de menu contextuel définis pour la cellule 2.

Les captures d’écran suivantes montrent [`ViewCell`](xref:Xamarin.Forms.ViewCell) mode hérité des actions de contexte désactivé, qui est le comportement Xamarin. Forms par défaut :

![Capture d’écran du mode hérité ViewCell désactivé, sur Android](viewcell-context-actions-images/legacy-mode-disabled.png "Mode hérité ViewCell désactivé")

Dans ce mode, les éléments de menu d’action de contexte corrects sont affichés pour les cellules 1 et 2.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
