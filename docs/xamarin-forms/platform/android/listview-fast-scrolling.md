---
title : "Description du défilement rapide de ListView sur Android" Description : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme Android, qui permet de faire défiler rapidement les données d’un ListView.
ms. Prod : xamarin ms. AssetID : 37D95A2D-74AC-488A-B903-2BDD799EAA5C ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/10/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="listview-fast-scrolling-on-android"></a>Défilement rapide de ListView sur Android

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme Android est utilisé pour permettre le défilement rapide des données dans un [`ListView`](xref:Xamarin.Forms.ListView) . Il est consommé en XAML en affectant `ListView.IsFastScrollEnabled` une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage ...
             xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        ...
        <ListView ItemsSource="{Binding GroupedEmployees}"
                  GroupDisplayBinding="{Binding Key}"
                  IsGroupingEnabled="true"
                  android:ListView.IsFastScrollEnabled="true">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

var listView = new Xamarin.Forms.ListView { IsGroupingEnabled = true, ... };
listView.SetBinding(ItemsView<Cell>.ItemsSourceProperty, "GroupedEmployees");
listView.GroupDisplayBinding = new Binding("Key");
listView.On<Android>().SetIsFastScrollEnabled(true);
```

La `ListView.On<Android>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur Android. La `ListView.SetIsFastScrollEnabled` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms, est utilisée pour permettre le défilement rapide des données dans un [`ListView`](xref:Xamarin.Forms.ListView) . En outre, la `SetIsFastScrollEnabled` méthode peut être utilisée pour activer le défilement rapide en appelant la `IsFastScrollEnabled` méthode pour retourner si le défilement rapide est activé :

```csharp
listView.On<Android>().SetIsFastScrollEnabled(!listView.On<Android>().IsFastScrollEnabled());
```

Le résultat est que le défilement rapide des données dans un [`ListView`](xref:Xamarin.Forms.ListView) peut être activé, ce qui modifie la taille du curseur de défilement :

[![](listview-fast-scrolling-images/fastscroll.png "ListView FastScroll Platform-Specific")](listview-fast-scrolling-images/fastscroll-large.png#lightbox "ListView FastScroll Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API AndroidSpecific](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [API AndroidSpecific. AppCompat](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
