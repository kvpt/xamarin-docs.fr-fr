---
title: Styliser une application Xamarin.Forms multiplateforme
description: Cet article explique comment styliser une application Xamarin. Forms multiplateforme à l’aide de styles XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 688b0e87bb6281923d3099c0d269b1c2554b6c7a
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70756758"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Styliser une application Xamarin. Forms multiplateforme

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Styliser une application Xamarin. Forms à l’aide de styles XAML.

Le Guide de démarrage rapide explique comment styliser une application Xamarin. Forms multiplateforme à l’aide de styles XAML. L’application finale est indiquée ci-dessous :

[![](styling-images/screenshots1-sml.png "")Page de commentaires](styling-images/screenshots1.png#lightbox "Page de notes")page entrée Note
[(styling-images/screenshots2-sml.png "") ![]](styling-images/screenshots2.png#lightbox "Page d’entrée de note")

### <a name="prerequisites"></a>Prérequis

Vous devez réussir le [démarrage rapide précédent](database.md) avant d’essayer ce guide de démarrage rapide. Vous pouvez également télécharger l' [exemple de démarrage rapide précédent](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/) et l’utiliser comme point de départ pour ce guide de démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio et ouvrez la solution notes.

2. Dans **Explorateur de solutions**, dans le projet **Notes** , double-cliquez sur **app. Xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code définit une [`Thickness`](xref:Xamarin.Forms.Thickness) valeur, une série de [`Color`](xref:Xamarin.Forms.Color) valeurs [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et des styles implicites pour et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui sont au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de l’application, peuvent être utilisés dans l’ensemble de l’application. Pour plus d’informations sur le style XAML, consultez [stylisation](deepdive.md#styling) in the [Xamarin. Forms-présentation approfondie](deepdive.md).

    Enregistrez les modifications apportées à **app. Xaml** en appuyant sur **CTRL + S**et fermez le fichier.

3. Dans **Explorateur de solutions**, dans le projet **Notes** , double-cliquez sur **NotesPage. Xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code ajoute un [`ListView`](xref:Xamarin.Forms.ListView) style implicite à au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de la page et affecte à la `ListView.Margin` propriété une valeur définie au niveau `ResourceDictionary`de l’application. Notez que le `ListView` style implicite a été ajouté au niveau `ResourceDictionary`de la page, car il `NotesPage`est consommé uniquement par. Pour plus d’informations sur le style XAML, consultez [stylisation](deepdive.md#styling) in the [Xamarin. Forms-présentation approfondie](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage. Xaml** en appuyant sur **CTRL + S**et fermez le fichier.

4. Dans **Explorateur de solutions**, dans le projet **Notes** , double-cliquez sur **NoteEntryPage. Xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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
                <Setter Property="BorderRadius" Value="5" />
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

    Ce code ajoute des styles implicites [`Editor`](xref:Xamarin.Forms.Editor) pour [`Button`](xref:Xamarin.Forms.Button) les vues et au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de la page, et `StackLayout.Margin` affecte à la propriété une valeur définie au niveau `ResourceDictionary`de l’application. Notez que les `Editor` styles `Button` implicites et ont été ajoutés au niveau `ResourceDictionary`de la page, car ils sont utilisés uniquement `NoteEntryPage`par le. Pour plus d’informations sur le style XAML, consultez [stylisation](deepdive.md#styling) in the [Xamarin. Forms-présentation approfondie](deepdive.md).

    Enregistrez les modifications apportées à **NoteEntryPage. Xaml** en appuyant sur **CTRL + S**et fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [génération du démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** , appuyez **+** sur le bouton pour accéder au **NoteEntryPage** et entrez une note. Sur chaque page, observez comment le style a été modifié par rapport au démarrage rapide précédent.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac et ouvrez le projet notes.

2. Dans le **panneau solutions**, dans le projet **Notes** , double-cliquez sur **app. Xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code définit une [`Thickness`](xref:Xamarin.Forms.Thickness) valeur, une série de [`Color`](xref:Xamarin.Forms.Color) valeurs [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et des styles implicites pour et [`ContentPage`](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui sont au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de l’application, peuvent être utilisés dans l’ensemble de l’application. Pour plus d’informations sur le style XAML, consultez [stylisation](deepdive.md#styling) in the [Xamarin. Forms-présentation approfondie](deepdive.md).

    Enregistrez les modifications apportées à **app. Xaml** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

3. Dans le **panneau solutions**, dans le projet **Notes** , double-cliquez sur **NotesPage. Xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code ajoute un [`ListView`](xref:Xamarin.Forms.ListView) style implicite à au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de la page et affecte à la `ListView.Margin` propriété une valeur définie au niveau `ResourceDictionary`de l’application. Notez que le `ListView` style implicite a été ajouté au niveau `ResourceDictionary`de la page, car il `NotesPage`est consommé uniquement par. Pour plus d’informations sur le style XAML, consultez [stylisation](deepdive.md#styling) in the [Xamarin. Forms-présentation approfondie](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage. Xaml** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

4. Dans le **panneau solutions**, dans le projet **Notes** , double-cliquez sur **NoteEntryPage. Xaml** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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
                <Setter Property="BorderRadius" Value="5" />
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

    Ce code ajoute des styles implicites [`Editor`](xref:Xamarin.Forms.Editor) pour [`Button`](xref:Xamarin.Forms.Button) les vues et au niveau [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary)de la page, et `StackLayout.Margin` affecte à la propriété une valeur définie au niveau `ResourceDictionary`de l’application. Notez que les `Editor` styles `Button` implicites et ont été ajoutés au niveau `ResourceDictionary`de la page, car ils sont utilisés uniquement `NoteEntryPage`par le. Pour plus d’informations sur le style XAML, consultez [stylisation](deepdive.md#styling) in the [Xamarin. Forms-présentation approfondie](deepdive.md).

    Enregistrez les modifications apportées à **NoteEntryPage. Xaml** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [génération du démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** , appuyez **+** sur le bouton pour accéder au **NoteEntryPage** et entrez une note. Sur chaque page, observez comment le style a été modifié par rapport au démarrage rapide précédent.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à:

- Styliser une application Xamarin. Forms à l’aide de styles XAML.

Pour en savoir plus sur les notions de base du développement d’applications à l’aide de Xamarin. Forms, passez à la présentation approfondie des Démarrages rapides.

> [!div class="nextstepaction"]
> [Next](deepdive.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Présentation approfondie des Démarrages rapides Xamarin. Forms](deepdive.md)
