---
title: Styliser une application Xamarin.Forms multiplateforme
description: Cet article explique comment une application de Xamarin.Forms multiplateforme de style avec les styles XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/02/2019
ms.openlocfilehash: 1b68afc1f3d3c57a5c336e9d30c97ce2375acb9f
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864344"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Style d’une Application Xamarin.Forms d’inter-plateformes

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)

Dans ce démarrage rapide, vous allez apprendre comment :

- Style d’une application Xamarin.Forms à l’aide de styles XAML.

Le démarrage rapide vous montre comment une application de Xamarin.Forms multiplateforme de style avec les styles XAML. L’application finale est indiquée ci-dessous :

[![](styling-images/screenshots1-sml.png "Page de commentaires")](styling-images/screenshots1.png#lightbox "Page de commentaires")
[![](styling-images/screenshots2-sml.png "Notez Page entrée")](styling-images/screenshots2.png#lightbox "Remarque Page d’entrée")

### <a name="prerequisites"></a>Prérequis

Vous devez mener à bien le [démarrage rapide précédent](database.md) avant de tenter de ce démarrage rapide. Vous pouvez également télécharger le [précédent exemple de démarrage rapide](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/) et l’utiliser comme point de départ pour ce démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio et ouvrez la solution de Notes.

2. Dans **l’Explorateur de solutions**, dans le **Notes** de projet, double-cliquez sur **App.xaml** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code définit un [ `Thickness` ](xref:Xamarin.Forms.Thickness) valeur, une série de [ `Color` ](xref:Xamarin.Forms.Color) valeurs et les styles implicites pour le [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) et [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui se trouvent dans le niveau de l’application [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), peuvent être utilisées dans toute l’application. Pour plus d’informations sur les styles de XAML, consultez [styles](deepdive.md#styling) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **App.xaml** en appuyant sur **CTRL + S**et fermez le fichier.

3. Dans **l’Explorateur de solutions**, dans le **Notes** de projet, double-cliquez sur **NotesPage.xaml** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code ajoute un style implicite pour la [ `ListView` ](xref:Xamarin.Forms.ListView) au niveau de la page [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et définit le `ListView.Margin` propriété à une valeur définie dans le niveau de l’application `ResourceDictionary` . Notez que le `ListView` style implicite a été ajouté au niveau de la page `ResourceDictionary`, car elle est consommée uniquement par le `NotesPage`. Pour plus d’informations sur les styles de XAML, consultez [styles](deepdive.md#styling) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage.xaml** en appuyant sur **CTRL + S**et fermez le fichier.

4. Dans **l’Explorateur de solutions**, dans le **Notes** de projet, double-cliquez sur **NoteEntryPage.xaml** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code ajoute les styles implicites pour le [ `Editor` ](xref:Xamarin.Forms.Editor) et [ `Button` ](xref:Xamarin.Forms.Button) vues au niveau de la page [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et définit le `StackLayout.Margin` propriété à une valeur définie dans le niveau de l’application `ResourceDictionary`. Notez que le `Editor` et `Button` styles implicites ont été ajoutés au niveau de la page `ResourceDictionary`, car ils sont uniquement utilisés par le `NoteEntryPage`. Pour plus d’informations sur les styles de XAML, consultez [styles](deepdive.md#styling) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **NoteEntryPage.xaml** en appuyant sur **CTRL + S**et fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [création le démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** appuyez sur le **+** bouton pour accéder à la **NoteEntryPage** et entrez une note. Sur chaque page, observez la manière dont le style a changé dans le Guide de démarrage rapide précédent.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac et ouvrez le projet de Notes.

2. Dans le **panneau solutions**, dans le **Notes** de projet, double-cliquez sur **App.xaml** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code définit un [ `Thickness` ](xref:Xamarin.Forms.Thickness) valeur, une série de [ `Color` ](xref:Xamarin.Forms.Color) valeurs et les styles implicites pour le [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) et [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Notez que ces styles, qui se trouvent dans le niveau de l’application [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary), peuvent être utilisées dans toute l’application. Pour plus d’informations sur les styles de XAML, consultez [styles](deepdive.md#styling) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **App.xaml** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

3. Dans le **panneau solutions**, dans le **Notes** de projet, double-cliquez sur **NotesPage.xaml** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code ajoute un style implicite pour la [ `ListView` ](xref:Xamarin.Forms.ListView) au niveau de la page [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et définit le `ListView.Margin` propriété à une valeur définie dans le niveau de l’application `ResourceDictionary` . Notez que le `ListView` style implicite a été ajouté au niveau de la page `ResourceDictionary`, car elle est consommée uniquement par le `NotesPage`. Pour plus d’informations sur les styles de XAML, consultez [styles](deepdive.md#styling) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage.xaml** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

4. Dans le **panneau solutions**, dans le **Notes** de projet, double-cliquez sur **NoteEntryPage.xaml** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code ajoute les styles implicites pour le [ `Editor` ](xref:Xamarin.Forms.Editor) et [ `Button` ](xref:Xamarin.Forms.Button) vues au niveau de la page [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)et définit le `StackLayout.Margin` propriété à une valeur définie dans le niveau de l’application `ResourceDictionary`. Notez que le `Editor` et `Button` styles implicites ont été ajoutés au niveau de la page `ResourceDictionary`, car ils sont uniquement utilisés par le `NoteEntryPage`. Pour plus d’informations sur les styles de XAML, consultez [styles](deepdive.md#styling) dans le [présentation approfondie du Guide de démarrage rapide Xamarin.Forms](deepdive.md).

    Enregistrez les modifications apportées à **NoteEntryPage.xaml** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [création le démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** appuyez sur le **+** bouton pour accéder à la **NoteEntryPage** et entrez une note. Sur chaque page, observez la manière dont le style a changé dans le Guide de démarrage rapide précédent.

::: zone-end


## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Style d’une application Xamarin.Forms à l’aide de styles XAML.

Pour en savoir plus sur les principes fondamentaux du développement d’applications à l’aide de Xamarin.Forms, continuez à immersion dans le Guide de démarrage rapide.

> [!div class="nextstepaction"]
> [Next](deepdive.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Styled/)
- [Immersion dans Xamarin.Forms-démarrage rapide](deepdive.md)
