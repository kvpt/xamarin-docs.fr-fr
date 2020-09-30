---
title: Actions contextuelles de ViewCell sur Android
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android qui active le mode hérité des actions de contexte ViewCell.
ms.prod: xamarin
ms.assetid: 8BD71B10-5037-443F-9975-B941CE393E5A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6cbdea3656daa4df894a79edc70453c78e3844bc
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556748"
---
# <a name="viewcell-context-actions-on-android"></a>Actions contextuelles de ViewCell sur Android

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Par défaut, à partir de Xamarin.Forms 4,3, quand un [`ViewCell`](xref:Xamarin.Forms.ViewCell) dans une application Android définit des actions de contexte pour chaque élément dans un [`ListView`](xref:Xamarin.Forms.ListView) , le menu actions de contexte est mis à jour lorsque l’élément sélectionné dans `ListView` change. Toutefois, dans les versions précédentes du Xamarin.Forms menu actions de contexte n’a pas été mis à jour, et ce comportement est appelé `ViewCell` mode hérité. Ce mode hérité peut entraîner un comportement incorrect si un `ListView` utilise un [`DataTemplateSelector`](xref:Xamarin.Forms.DataTemplateSelector) pour définir son `ItemTemplate` objet à partir d' [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) objets qui définissent des actions de contexte différentes.

Ce spécifique à la plateforme Android active le [`ViewCell`](xref:Xamarin.Forms.ViewCell) menu actions de contexte en mode hérité, à des fins de compatibilité descendante, de sorte que le menu actions de contexte n’est pas mis à jour lorsque l’élément sélectionné dans un [`ListView`](xref:Xamarin.Forms.ListView) change. Il est consommé en XAML en affectant `ViewCell.IsContextActionsLegacyModeEnabled` à la propriété pouvant être liée la valeur `true` :

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

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

viewCell.On<Android>().SetIsContextActionsLegacyModeEnabled(true);
```

La `ViewCell.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `ViewCell.SetIsContextActionsLegacyModeEnabled` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour activer le [`ViewCell`](xref:Xamarin.Forms.ViewCell) mode hérité du menu actions de contexte, de sorte que le menu actions de contexte n’est pas mis à jour lorsque l’élément sélectionné dans un [`ListView`](xref:Xamarin.Forms.ListView) change. En outre, la `ViewCell.GetIsContextActionsLegacyModeEnabled` méthode peut être utilisée pour retourner une valeur indiquant si le mode hérité des actions de contexte est activé.

Les captures d’écran suivantes montrent le [`ViewCell`](xref:Xamarin.Forms.ViewCell) mode hérité des actions de contexte activé :

![Capture d’écran du mode hérité ViewCell activé, sur Android](viewcell-context-actions-images/legacy-mode-enabled.png "Mode hérité ViewCell activé")

Dans ce mode, les éléments de menu d’action de contexte affichés sont identiques pour les cellules 1 et 2, en dépit des différents éléments de menu contextuel définis pour la cellule 2.

Les captures d’écran suivantes montrent [`ViewCell`](xref:Xamarin.Forms.ViewCell) le mode hérité des actions de contexte désactivé, qui est le comportement par défaut Xamarin.Forms :

![Capture d’écran du mode hérité ViewCell désactivé, sur Android](viewcell-context-actions-images/legacy-mode-disabled.png "Mode hérité ViewCell désactivé")

Dans ce mode, les éléments de menu d’action de contexte corrects sont affichés pour les cellules 1 et 2.

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)