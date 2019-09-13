---
title: Xamarin. Forms CarouselView
description: Par défaut, un CarouselView affiche ses éléments dans une liste horizontale. Toutefois, il a également accès aux mêmes dispositions que CollectionView, y compris une orientation verticale.
ms.prod: xamarin
ms.assetid: fede0382-c972-4023-a4ea-fe5cadec91a6
ms.technology: xamarin-forms
author: pauldipietro
ms.author: padipi
ms.date: 09/09/2019
ms.openlocfilehash: 5bbaeead524089ea604cfd9a9fd7f3a85d04e724
ms.sourcegitcommit: e83035c746f165ee6d03f2e9fd0066ee4f20a9fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70908281"
---
# <a name="xamarinforms-carouselview-layouts"></a>Dispositions du CarouselView Xamarin. Forms

![](~/media/shared/preview.png "Cette API est actuellement en préversion")

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)

## <a name="introduction"></a>Présentation

La plupart des fonctionnalités de présentation disponibles pour CarouselView proviennent de CollectionView. Vous pouvez vous référer à la [documentation de disposition](../collectionview/layout.md) de CollectionView pour voir l’utilisation de différentes dispositions.

## <a name="differences-from-collectionview"></a>Différences par rapport à CollectionView

Par défaut, les éléments d’un CarouselView sont orientés horizontalement, comme la fonction classique d’un carrousel dans les applications.

CarouselView fournit également des propriétés supplémentaires :

> [!IMPORTANT]
> Les propriétés supplémentaires pour CarouselView sont toujours en cours de développement et cette liste n’est pas encore terminée.

| API | Fonction |
|---|---|---|
| NumberOfSideItems | Définit le nombre d’éléments qui apparaissent de chaque côté de l’élément actuel. La valeur par défaut est 0.
| PeekAreaInsets | Fournit un moyen d’indiquer à l’utilisateur que le CarouselView a des éléments supplémentaires à faire défiler en ajustant son niveau de visibilité à côté de l’élément actuel.

## <a name="setting-the-number-of-fully-visible-items"></a>Définition du nombre d’éléments entièrement visibles

Par défaut, le CarouselView affiche un élément dans son intégralité à l’écran. Les utilisateurs peuvent définir `NumberOfSideItems` la propriété pour autoriser l’affichage de plus d’éléments adjacents à l’élément actuel. Notez que toute valeur définie sur `PeekAreaInsets` s’appliquera toujours.

```xaml
<StackLayout Margin="20">
    <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" NumberOfSideItems="1">
        <CarouselView.ItemTemplate>
            <DataTemplate>
                <Frame BorderColor="Black">
                    <Grid>
                        <Grid.RowDefinitions>
                            <RowDefinition Height="Auto" />
                            <RowDefinition Height="Auto" />
                        </Grid.RowDefinitions>
                        <Grid.ColumnDefinitions>
                            <ColumnDefinition Width="Auto" />
                            <ColumnDefinition Width="Auto" />
                        </Grid.ColumnDefinitions>
                        <Label Grid.Column="1"
                            Text="{Binding Name}"
                            FontAttributes="Bold"
                            FontSize="14"/>
                        <Label Grid.Row="1"
                            Grid.Column="1"
                            Text="{Binding Location}"
                            FontAttributes="Italic"
                            FontSize="12"
                            VerticalOptions="End" />
                    </Grid>
                </Frame>
            </DataTemplate>
        </CarouselView.ItemTemplate>
    </CarouselView>
</StackLayout>
```

[ ![Capture d’écran d’un CarouselView avec des éléments secondaires, sur les](carouselview-images/side-items.png "éléments côté Android CarouselView") ] (carouselview-images/side-items-large.png#lightbox "Éléments côté CarouselView")

## <a name="making-adjacent-items-partially-visible"></a>Rendre des éléments adjacents partiellement visibles

Quand vous utilisez un CarouselView dans votre application, il peut être utile d’indiquer à l’utilisateur que CarouselView fonctionne de cette manière en affectant à `PeekAreaInsets` la propriété une valeur différente de zéro (valeur par défaut), qui les expose partiellement à l’écran.

```xaml
<StackLayout Margin="20">
  <CarouselView ItemsSource="{Binding Monkeys}" HeightRequest="125" PeekAreaInsets="100">
      <CarouselView.ItemTemplate>
          <DataTemplate>
              <Frame BorderColor="Black">
                  <Grid>
                      <Grid.RowDefinitions>
                          <RowDefinition Height="Auto" />
                          <RowDefinition Height="Auto" />
                      </Grid.RowDefinitions>
                      <Grid.ColumnDefinitions>
                          <ColumnDefinition Width="Auto" />
                          <ColumnDefinition Width="Auto" />
                      </Grid.ColumnDefinitions>
                      <Label Grid.Column="1"
                          Text="{Binding Name}"
                          FontAttributes="Bold"
                          FontSize="14"/>
                      <Label Grid.Row="1"
                          Grid.Column="1"
                          Text="{Binding Location}"
                          FontAttributes="Italic"
                          FontSize="12"
                          VerticalOptions="End" />
                  </Grid>
              </Frame>
          </DataTemplate>
      </CarouselView.ItemTemplate>
  </CarouselView>
</StackLayout>
```

[ ![Capture d’écran d’un CarouselView avec des éléments secondaires, sur les](carouselview-images/peek-area-insets.png "éléments côté Android CarouselView") ] (carouselview-images/peek-area-insets-large.png#lightbox "Éléments côté CarouselView")

## <a name="related-links"></a>Liens connexes

- [CollectionView (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/CarouselViewDemos/)
- [Documentation sur la disposition CollectionView](../collectionview/layout.md)
