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
ms.date: 02/07/2020
ms.openlocfilehash: fbd957c68d7a9aa2f8e44c91fab6174d8ed72014
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77068739"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Appliquer des styles dans une application Xamarin.Forms multiplateforme

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

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
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Ce code définit [`Thickness`](xref:Xamarin.Forms.Thickness) une valeur, [`Color`](xref:Xamarin.Forms.Color) une série de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) valeurs, et des styles implicites pour le et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)sont au niveau de l’application , peuvent être consommés tout au long de l’application. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Ce code ajoute un [`ListView`](xref:Xamarin.Forms.ListView) style implicite pour [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)le niveau `ListView.Margin` de la page , et `ResourceDictionary`définit la propriété à une valeur définie dans le niveau d’application . Notez que le style implicite de `ListView` a été ajouté à `ResourceDictionary` au niveau de la page, car il est uniquement consommé par `NotesPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
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

    Ce code ajoute des [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) styles implicites pour [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)le et `StackLayout.Margin` les vues au niveau de `ResourceDictionary`la page , et définit la propriété à une valeur définie dans le niveau d’application . Notez que les styles implicites de `Editor` et `Button` ont été ajoutés à `ResourceDictionary` au niveau de la page, car ils sont consommés uniquement par `NoteEntryPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

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
            <Color x:Key="AppBackgroundColor">AliceBlue</Color>
            <Color x:Key="NavigationBarColor">#1976D2</Color>
            <Color x:Key="NavigationBarTextColor">White</Color>

            <!-- Implicit styles -->
            <Style TargetType="{x:Type NavigationPage}">
                <Setter Property="BarBackgroundColor"
                        Value="{StaticResource NavigationBarColor}" />
                 <Setter Property="BarTextColor"
                        Value="{StaticResource NavigationBarTextColor}" />           
            </Style>

            <Style TargetType="{x:Type ContentPage}"
                   ApplyToDerivedTypes="True">
                <Setter Property="BackgroundColor"
                        Value="{StaticResource AppBackgroundColor}" />
            </Style>

        </Application.Resources>
    </Application>
    ```

    Ce code définit [`Thickness`](xref:Xamarin.Forms.Thickness) une valeur, [`Color`](xref:Xamarin.Forms.Color) une série de [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) valeurs, et des styles implicites pour le et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)sont au niveau de l’application , peuvent être consommés tout au long de l’application. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

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
                              TextColor="Black"
                              Detail="{Binding Date}" />
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>

    </ContentPage>
    ```

    Ce code ajoute un [`ListView`](xref:Xamarin.Forms.ListView) style implicite pour [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)le niveau `ListView.Margin` de la page , et `ResourceDictionary`définit la propriété à une valeur définie dans le niveau d’application . Notez que le style implicite de `ListView` a été ajouté à `ResourceDictionary` au niveau de la page, car il est uniquement consommé par `NotesPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

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
                <Setter Property="BackgroundColor" Value="#1976D2" />
                <Setter Property="TextColor" Value="White" />
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

    Ce code ajoute des [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) styles implicites pour [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)le et `StackLayout.Margin` les vues au niveau de `ResourceDictionary`la page , et définit la propriété à une valeur définie dans le niveau d’application . Notez que les styles implicites de `Editor` et `Button` ont été ajoutés à `ResourceDictionary` au niveau de la page, car ils sont consommés uniquement par `NoteEntryPage`. Pour plus d’informations sur l’application de styles XAML, consultez [Styles](deepdive.md#styling) dans [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md).

    Enregistrez les changements apportés à **NoteEntryPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Sur chaque page, notez la façon dont le style a changé par rapport au précédent guide de démarrage rapide.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Appliquer des styles dans une application Xamarin.Forms à l’aide de styles XAML.

Pour en savoir plus sur les notions de base relatives au développement d’applications à l’aide de Xamarin.Forms, passez au guide de démarrage rapide en immersion.

> [!div class="nextstepaction"]
> [Suivant](deepdive.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin.Forms - Démarrage rapide en immersion](deepdive.md)
