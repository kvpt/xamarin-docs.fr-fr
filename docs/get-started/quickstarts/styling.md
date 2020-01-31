---
title: Styliser une application Xamarin.Forms multiplateforme
description: Cet article explique comment appliquer des styles dans une application Xamarin.Forms multiplateforme à l’aide de styles XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: a7e1cdd59b463c38be1a49e962112cb893eed50f
ms.sourcegitcommit: 3f0e4f10e5def19122588bb05f26ab2baa9df6eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2020
ms.locfileid: "75488866"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Appliquer des styles dans une application Xamarin.Forms multiplateforme

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Appliquer des styles dans une application Xamarin.Forms à l’aide de styles XAML.

Le guide de démarrage rapide explique comment appliquer des styles dans une application Xamarin.Forms multiplateforme à l’aide de styles XAML. L’application finale est indiquée ci-dessous :

[![](styling-images/screenshots1-sml.png "Notes Page")](styling-images/screenshots1.png#lightbox "Notes Page")
[![](styling-images/screenshots2-sml.png "Note Entry Page")](styling-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prérequis

Vous devez suivre correctement le [précédent guide de démarrage rapide](database.md) avant de tenter de suivre ce guide de démarrage rapide. Vous pouvez également télécharger l’[exemple du précédent guide de démarrage rapide](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/), et l’utiliser comme point de départ pour ce guide de démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio, puis ouvrez la solution Notes.

2. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **App.xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Ce code définit une valeur [`Thickness`](xref:Xamarin.Forms.Thickness), une série de valeurs [`Color`](xref:Xamarin.Forms.Color) ainsi que des styles implicites pour [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui se trouvent dans [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de l’application, peuvent être consommés dans l’ensemble de l’application. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **App.xaml** en appuyant sur **Ctrl+S**, puis fermez le fichier.

3. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **NotesPage.xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Ce code ajoute un style implicite pour [`ListView`](xref:Xamarin.Forms.ListView) à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de la page, puis affecte à la propriété `ListView.Margin` une valeur définie dans `ResourceDictionary` au niveau de l’application. Notez que le style implicite de `ListView` a été ajouté à `ResourceDictionary` au niveau de la page, car il est uniquement consommé par `NotesPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **NotesPage.xaml** en appuyant sur **Ctrl+S**, puis fermez le fichier.

4. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **NoteEntryPage.xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Ce code ajoute des styles implicites pour les vues [`Editor`](xref:Xamarin.Forms.Editor) et [`Button`](xref:Xamarin.Forms.Button) à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de la page, puis affecte à la propriété `StackLayout.Margin` une valeur définie dans `ResourceDictionary` au niveau de l’application. Notez que les styles implicites de `Editor` et `Button` ont été ajoutés à `ResourceDictionary` au niveau de la page, car ils sont consommés uniquement par `NoteEntryPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **NoteEntryPage.xaml** en appuyant sur **Ctrl+S**, puis fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Sur chaque page, notez la façon dont le style a changé par rapport au précédent guide de démarrage rapide.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac, puis ouvrez le projet Notes.

2. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **App.xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <Application xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.App">
        <Application.Resources>

            <Thickness x:Key="PageMargin">20</Thickness>

            <!-- Colors -->
            <Color x:Key="AppBackgroundColor">WhiteSmoke</Color>
            <Color x:Key="iOSNavigationBarColor">WhiteSmoke</Color>
            <Color x:Key="AndroidNavigationBarColor">#2196F3</Color>
            <Color x:Key="iOSNavigationBarTextColor">Black</Color>
            <Color x:Key="AndroidNavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarColor},
                                           Android={StaticResource AndroidNavigationBarColor}}" />
                 <Setter Property="BarTextColor"
                        Value="{OnPlatform iOS={StaticResource iOSNavigationBarTextColor},
                                           Android={StaticResource AndroidNavigationBarTextColor}}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Ce code définit une valeur [`Thickness`](xref:Xamarin.Forms.Thickness), une série de valeurs [`Color`](xref:Xamarin.Forms.Color) ainsi que des styles implicites pour [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui se trouvent dans [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de l’application, peuvent être consommés dans l’ensemble de l’application. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **App.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

3. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **NotesPage.xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type ListView}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>
        </ContentPage.Resources>

        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>

        <ListView x:Name="listView"
                  Margin="{StaticResource PageMargin}"
                  ItemSelected="OnListViewItemSelected">
            <ListView.ItemTemplate>
                <DataTemplate>
                    <TextCell Text="{Binding Text}"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Ce code ajoute un style implicite pour [`ListView`](xref:Xamarin.Forms.ListView) à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de la page, puis affecte à la propriété `ListView.Margin` une valeur définie dans `ResourceDictionary` au niveau de l’application. Notez que le style implicite de `ListView` a été ajouté à `ResourceDictionary` au niveau de la page, car il est uniquement consommé par `NotesPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **NotesPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

4. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **NoteEntryPage.xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NoteEntryPage"
                 Title="Note Entry">
        <ContentPage.Resources>
            <!-- Implicit styles -->
            <Style TargetType="{x:Type Editor}">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

            <Style TargetType="Button"
                   ApplyToDerivedTypes="True"
                   CanCascade="True">
                <Setter Property="FontSize" Value="Medium" />
                <Setter Property="BackgroundColor" Value="LightGray" />
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="CornerRadius" Value="5" />
            </Style>
        </ContentPage.Resources>

        <StackLayout Margin="{StaticResource PageMargin}">
            <Editor Placeholder="Enter your note"
                    Text="{Binding Text}"
                    HeightRequest="100" />
            <Grid>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="*" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Button Text="Save"
                        Clicked="OnSaveButtonClicked" />
                <Button Grid.Column="1"
                        Text="Delete"
                        Clicked="OnDeleteButtonClicked" />
            </Grid>
        </StackLayout>

    </ContentPage>
    ```

    Ce code ajoute des styles implicites pour les vues [`Editor`](xref:Xamarin.Forms.Editor) et [`Button`](xref:Xamarin.Forms.Button) à [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) au niveau de la page, puis affecte à la propriété `StackLayout.Margin` une valeur définie dans `ResourceDictionary` au niveau de l’application. Notez que les styles implicites de `Editor` et `Button` ont été ajoutés à `ResourceDictionary` au niveau de la page, car ils sont consommés uniquement par `NoteEntryPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **NoteEntryPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Sur chaque page, notez la façon dont le style a changé par rapport au précédent guide de démarrage rapide.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à :

- Appliquer des styles dans une application Xamarin.Forms à l’aide de styles XAML.

Pour en savoir plus sur les notions de base relatives au développement d’applications à l’aide de Xamarin.Forms, passez au guide de démarrage rapide en immersion.

> [!div class="nextstepaction"]
> [Next](deepdive.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md)
