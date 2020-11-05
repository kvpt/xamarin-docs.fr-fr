---
title: Xamarin.Forms Modèles de conception Dual-Screen
description: Ce guide explique comment Xamarin.Forms prend en charge un large éventail de modèles de conception optimisés pour les appareils à deux écrans.
ms.prod: xamarin
ms.assetid: 3176d792-6dba-4e00-b463-497c58678ee9
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 02/08/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8cd98f529c324e89a4b935a364a09ff794b6c5be
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369426"
---
# <a name="no-locxamarinforms-dual-screen-design-patterns"></a>Xamarin.Forms modèles de conception à deux écrans

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)

Ce guide présente nos modèles de conception recommandés pour les appareils double écran. Il contient également du code et des exemples pour vous aider à créer des interfaces offrant des expériences utilisateur engageantes et pratiques.

## <a name="extended-canvas-pattern"></a>Modèle de canevas étendu

Le modèle de canevas étendu traite les deux écrans comme un grand canevas pour afficher une carte, une image, une feuille de calcul ou tout autre contenu pouvant bénéficier d’une expansion pour consommer le maximum d’espace :

![Exemple de canevas étendu](design-patterns-images/extended-canvas-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:d="http://xamarin.com/schemas/2014/forms/design"
             xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
             mc:Ignorable="d"
             x:Class="Xamarin.Duo.Forms.Samples.ExtendCanvas">
    <Grid>
        <WebView x:Name="webView"
                 HorizontalOptions="FillAndExpand"
                 VerticalOptions="FillAndExpand" />
        <SearchBar x:Name="searchBar"
                   Placeholder="Find a place..."
                   BackgroundColor="DarkGray"
                   Opacity="0.8"
                   HorizontalOptions="FillAndExpand"
                   VerticalOptions="Start" />
    </Grid>
</ContentPage>
```

Dans cet exemple, `Grid` et le contenu interne sont développés pour consommer tout l’espace disponible, qu’il s’agisse d’un affichage sur un seul écran ou d’une répartition sur deux écrans.

## <a name="master-detail-pattern"></a>Modèle maître/détail

Le modèle maître/détail désigne une configuration dans laquelle un utilisateur sélectionne un élément de contenu dans une vue maître, en général une liste du côté gauche, pour obtenir des détails sur cet élément du côté droit :

![Exemple maître/détail](design-patterns-images/master-detail-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Class="Xamarin.Duo.Forms.Samples.MasterDetail">
    <dualScreen:TwoPaneView MinWideModeWidth="4000"
                            MinTallModeHeight="4000">
        <dualScreen:TwoPaneView.Pane1>
            <local:Master x:Name="masterPage" />
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:Details x:Name="detailsPage" />
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

Dans cet exemple, vous pouvez utiliser `TwoPaneView` pour définir une liste dans un volet et une vue détaillée dans l’autre.

## <a name="two-page-pattern"></a>Modèle à deux pages

Le modèle à deux pages est idéal pour le contenu qui se prête à une disposition de type « deux pages à la fois » (lecteur de document, notes, planche à dessin, etc.) :

![Exemple avec deux pages](design-patterns-images/two-page-sample.png)

```xaml
<Grid x:Name="layout">
    <CollectionView x:Name="cv"
                    BackgroundColor="LightGray">
        <CollectionView.ItemsLayout>
            <GridItemsLayout SnapPointsAlignment="Start"
                             SnapPointsType="MandatorySingle"
                             Orientation="Horizontal"
                             HorizontalItemSpacing="{Binding Source={x:Reference mainPage}, Path=HingeWidth}" />
        </CollectionView.ItemsLayout>
        <CollectionView.ItemTemplate>
            <DataTemplate>
                <Frame BackgroundColor="LightGray"
                       Padding="0"
                       Margin="0"
                       WidthRequest="{Binding Source={x:Reference mainPage}, Path=ContentWidth}"
                       HeightRequest="{Binding Source={x:Reference mainPage}, Path=ContentHeight}">
                    <Frame Margin="20"
                           BackgroundColor="White">
                        <Label FontSize="Large"
                               Text="{Binding .}"
                               VerticalTextAlignment="Center"
                               HorizontalTextAlignment="Center"
                               HorizontalOptions="Center"
                               VerticalOptions="Center" />
                    </Frame>
                </Frame>
            </DataTemplate>
        </CollectionView.ItemTemplate>
    </CollectionView>
</Grid>
```

Le [`CollectionView`](xref:Xamarin.Forms.CollectionView) , avec une disposition en grille qui se divise sur la largeur de la charnière, fait une approche idéale pour offrir cette expérience à deux écrans.

## <a name="dual-view-pattern"></a>Modèle d’affichage double

Le modèle d’affichage double peut sembler être simplement une vue « Deux pages », mais la différence est dans le contenu et le scénario utilisateur. Dans ce modèle, vous comparez le contenu côte à côte, par exemple pour modifier un document ou une photo, pour comparer différents menus de restaurant ou pour trouver les différences dans un conflit de fusion pour des fichiers de code :

![Exemple d’affichage double](design-patterns-images/dual-view-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:dualScreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Class="Xamarin.Duo.Forms.Samples.DualViewListPage">
    <dualScreen:TwoPaneView>
        <dualScreen:TwoPaneView.Pane1>
            <CollectionView x:Name="mapList"
                            SelectionMode="Single">
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Grid Padding="10,5,10,5">
                            <Frame Visual="Material"
                                   BorderColor="LightGray">
                                <StackLayout Padding="5">
                                    <Label FontSize="Title"
                                           Text="{Binding Title}" />
                                </StackLayout>
                            </Frame>
                        </Grid>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualScreen:TwoPaneView.Pane1>
        <dualScreen:TwoPaneView.Pane2>
            <local:DualViewMap x:Name="mapPage" />
        </dualScreen:TwoPaneView.Pane2>
    </dualScreen:TwoPaneView>
</ContentPage>
```

## <a name="companion-pattern"></a>Modèle compagnon

Le modèle compagnon montre comment utiliser le deuxième écran pour fournir un second niveau de contenu lié à la vue principale, comme dans le cas d’une application de dessin, d’un jeu ou d’un éditeur multimédia :

![Exemple de volet compagnon](design-patterns-images/companion-pane-sample.png)

```xaml
<ContentPage xmlns:local="clr-namespace:Xamarin.Duo.Forms.Samples"
             xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:dualscreen="clr-namespace:Xamarin.Forms.DualScreen;assembly=Xamarin.Forms.DualScreen"
             x:Name="mainPage"
             x:Class="Xamarin.Duo.Forms.Samples.CompanionPane"
             BackgroundColor="LightGray"
             Visual="Material">
    <dualscreen:TwoPaneView x:Name="twoPaneView"
                            MinWideModeWidth="4000"
                            MinTallModeHeight="4000">
        <dualscreen:TwoPaneView.Pane1>
            <CarouselView x:Name="cv"
                          BackgroundColor="LightGray"
                          IsScrollAnimated="False" >
                <CarouselView.ItemTemplate>
                    <DataTemplate>
                        <Frame BackgroundColor="LightGray"
                               Padding="0"
                               Margin="0"
                               WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Width}"
                               HeightRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane1.Height}">
                            <Frame Margin="20"
                                   BackgroundColor="White">
                                <Label FontSize="Large"
                                       Text="{Binding ., StringFormat='Slide Content {0}'}"
                                       VerticalTextAlignment="Center"
                                       HorizontalTextAlignment="Center"
                                       HorizontalOptions="Center"
                                       VerticalOptions="Center" />
                            </Frame>
                        </Frame>
                    </DataTemplate>
                </CarouselView.ItemTemplate>
            </CarouselView>
        </dualscreen:TwoPaneView.Pane1>
        <dualscreen:TwoPaneView.Pane2>
            <CollectionView x:Name="indicators"
                            SelectionMode="Single"
                            Margin="20, 20, 20, 20"
                            BackgroundColor="LightGray"
                            WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
                            ItemsSource="{Binding Source={x:Reference cv}, Path=ItemsSource}">
                <CollectionView.Resources>
                    <ResourceDictionary>
                        <Style TargetType="Frame">
                            <Setter Property="VisualStateManager.VisualStateGroups">
                                <VisualStateGroupList>
                                    <VisualStateGroup x:Name="CommonStates">
                                        <VisualState x:Name="Normal">
                                            <VisualState.Setters>
                                                <Setter Property="Padding"
                                                        Value="0" />
                                            </VisualState.Setters>
                                        </VisualState>
                                        <VisualState x:Name="Selected">
                                            <VisualState.Setters>
                                                <Setter Property="BorderColor"
                                                        Value="Green" />
                                                <Setter Property="Padding"
                                                        Value="1" />
                                            </VisualState.Setters>
                                        </VisualState>
                                    </VisualStateGroup>
                                </VisualStateGroupList>
                            </Setter>
                        </Style>
                    </ResourceDictionary>
                </CollectionView.Resources>
                <CollectionView.ItemsLayout>
                    <LinearItemsLayout Orientation="Vertical"
                                       ItemSpacing="10" />
                </CollectionView.ItemsLayout>
                <CollectionView.ItemTemplate>
                    <DataTemplate>
                        <Frame WidthRequest="{Binding Source={x:Reference twoPaneView}, Path=Pane2.Width}"
                               CornerRadius="10"
                               HeightRequest="60"
                               BackgroundColor="White"
                               Margin="0">
                            <StackLayout HorizontalOptions="Fill"
                                         VerticalOptions="Fill"
                                         Orientation="Horizontal">
                                <Label FontSize="Micro"
                                       Padding="20,0,20,0"
                                       VerticalTextAlignment="Center"
                                       WidthRequest="140" Text="{Binding ., StringFormat='Slide Content {0}'}" />
                                <Label FontSize="Small"
                                       Padding="20,0,20,0"
                                       VerticalTextAlignment="Center"
                                       HorizontalOptions="FillAndExpand"
                                       BackgroundColor="DarkGray"
                                       Grid.Column="1"
                                       Text="{Binding ., StringFormat='Slide {0}'}" />
                            </StackLayout>
                        </Frame>
                    </DataTemplate>
                </CollectionView.ItemTemplate>
            </CollectionView>
        </dualscreen:TwoPaneView.Pane2>
    </dualscreen:TwoPaneView>
</ContentPage>
```

## <a name="related-links"></a>Liens connexes

- [DualScreen (sample)](/samples/xamarin/xamarin-forms-samples/userinterface-dualscreendemos/)
- [Créer des applications pour appareils double écran](index.md)