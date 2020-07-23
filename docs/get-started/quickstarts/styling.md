---
title: Styliser une application multiplateforme Xamarin.Forms
description: Cet article explique comment styliser une Xamarin.Forms application multiplateforme à l’aide de styles XAML.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: CCCF8E57-D021-4542-8709-5808570FC26A
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/07/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 05560ae7c3d255140c0782e5f442dd2356d86cad
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86937421"
---
# <a name="style-a-cross-platform-xamarinforms-application"></a>Styliser une application multiplateforme Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Stylisez une Xamarin.Forms application à l’aide de styles XAML.

Le Guide de démarrage rapide explique comment styliser une Xamarin.Forms application multiplateforme à l’aide de styles XAML. L’application finale est indiquée ci-dessous :

Page de [ ![ Commentaires](styling-images/screenshots1-sml.png)](styling-images/screenshots1.png#lightbox "Page de notes")page 
 [ ![ entrée note](styling-images/screenshots2-sml.png)](styling-images/screenshots2.png#lightbox "Page d’entrée de note")

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

    Ce code définit une [`Thickness`](xref:Xamarin.Forms.Thickness) valeur, une série de [`Color`](xref:Xamarin.Forms.Color) valeurs et des styles implicites pour [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Notez que ces styles, qui sont au niveau de l’application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , peuvent être utilisés dans l’ensemble de l’application. Pour plus d’informations sur le style XAML, consultez l’article sur les [styles](deepdive.md#styling) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

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

    Ce code ajoute un style implicite à au niveau de la [`ListView`](xref:Xamarin.Forms.ListView) page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) et affecte à la `ListView.Margin` propriété une valeur définie au niveau de l’application `ResourceDictionary` . Notez que le style implicite de `ListView` a été ajouté à `ResourceDictionary` au niveau de la page, car il est uniquement consommé par `NotesPage`. Pour plus d’informations sur le style XAML, consultez l’article sur les [styles](deepdive.md#styling) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

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

    Ce code ajoute des styles implicites pour les [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) vues et au niveau de la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , et affecte `StackLayout.Margin` à la propriété une valeur définie au niveau de l’application `ResourceDictionary` . Notez que les styles implicites de `Editor` et `Button` ont été ajoutés à `ResourceDictionary` au niveau de la page, car ils sont consommés uniquement par `NoteEntryPage`. Pour plus d’informations sur le style XAML, consultez l’article sur les [styles](deepdive.md#styling) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

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

    Ce code définit une [`Thickness`](xref:Xamarin.Forms.Thickness) valeur, une série de [`Color`](xref:Xamarin.Forms.Color) valeurs et des styles implicites pour [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et [`ContentPage`](xref:Xamarin.Forms.ContentPage) . Notez que ces styles, qui sont au niveau de l’application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , peuvent être utilisés dans l’ensemble de l’application. Pour plus d’informations sur le style XAML, consultez l’article sur les [styles](deepdive.md#styling) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

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

    Ce code ajoute un style implicite à au niveau de la [`ListView`](xref:Xamarin.Forms.ListView) page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) et affecte à la `ListView.Margin` propriété une valeur définie au niveau de l’application `ResourceDictionary` . Notez que le style implicite de `ListView` a été ajouté à `ResourceDictionary` au niveau de la page, car il est uniquement consommé par `NotesPage`. Pour plus d’informations sur le style XAML, consultez l’article sur les [styles](deepdive.md#styling) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

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

    Ce code ajoute des styles implicites pour les [`Editor`](xref:Xamarin.Forms.Editor) [`Button`](xref:Xamarin.Forms.Button) vues et au niveau de la page [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) , et affecte `StackLayout.Margin` à la propriété une valeur définie au niveau de l’application `ResourceDictionary` . Notez que les styles implicites de `Editor` et `Button` ont été ajoutés à `ResourceDictionary` au niveau de la page, car ils sont consommés uniquement par `NoteEntryPage`. Pour plus d’informations sur le style XAML, consultez l’article sur les [styles](deepdive.md#styling) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les changements apportés à **NoteEntryPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

5. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Sur chaque page, notez la façon dont le style a changé par rapport au précédent guide de démarrage rapide.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Stylisez une Xamarin.Forms application à l’aide de styles XAML.

Pour en savoir plus sur les notions de base du développement d’applications à l’aide de Xamarin.Forms , passez à la présentation approfondie des Démarrages rapides.

> [!div class="nextstepaction"]
> [Suivant](deepdive.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-styled/)
- [Xamarin.FormsPrésentation approfondie des Démarrages rapides](deepdive.md)
