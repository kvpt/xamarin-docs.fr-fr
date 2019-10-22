---
title: Naviguer dans une application Xamarin.Forms de plusieurs pages
description: Cet article explique comment activer l’application à page unique, qui est en mesure de stocker une seule note dans une application à plusieurs pages, qui est en mesure de stocker plusieurs notes.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: 9ce02b4c6412eab1f4b1003b262573c59940286c
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68653794"
---
# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Effectuer la navigation dans une application Xamarin. Forms à plusieurs pages

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Ajoutez des pages supplémentaires à une solution Xamarin. Forms.
- Effectuer une navigation entre les pages.
- Utilisez la liaison de données pour synchroniser les données entre les éléments d’interface utilisateur et leur source de données.

Le Guide de démarrage rapide vous explique comment transformer une application Xamarin. Forms multiplateforme à une seule page, qui est en mesure de stocker une seule note dans une application à plusieurs pages, afin de stocker plusieurs notes. L’application finale est indiquée ci-dessous :

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Configuration requise

Vous devez réussir le [démarrage rapide précédent](single-page.md) avant d’essayer ce guide de démarrage rapide. Vous pouvez également télécharger l' [exemple de démarrage rapide précédent](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/) et l’utiliser comme point de départ pour ce guide de démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio. Dans la fenêtre Démarrer, cliquez sur la solution **Notes** dans la liste projets/solutions récents, ou cliquez sur **ouvrir un projet ou une solution**, puis dans la boîte de dialogue **ouvrir un projet/une solution** , sélectionnez le fichier solution pour le projet Notes :

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Notes** et sélectionnez **Ajouter > nouveau dossier**:

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. Dans **Explorateur de solutions**, nommez le nouveau dossier **modèles**:

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. Dans **Explorateur de solutions**, sélectionnez le dossier **Models** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouvel élément...** :

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **éléments visuels C# > classe**, nommez le nouveau fichier **note**, puis cliquez sur le bouton **Ajouter** :

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    Cette opération ajoute une classe nommée **note** au dossier **Models** du projet **Notes** .

6. Dans **note.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Cette classe définit un modèle de `Note` qui stocke les données relatives à chaque note dans l’application.    

    Enregistrez les modifications apportées à **note.cs** en appuyant sur **CTRL + S**et fermez le fichier.

7. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Notes** et sélectionnez **Ajouter > nouvel élément.** .. Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **éléments visuels C# > page de contenu > Xamarin. Forms**, nommez le nouveau fichier **NoteEntryPage**, puis cliquez sur le bouton **Ajouter** :

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Cette opération ajoute une nouvelle page nommée **NoteEntryPage** au dossier racine du projet. Cette page sera la deuxième page de l’application.

8. Dans **NoteEntryPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’une [`Editor`](xref:Xamarin.Forms.Editor) pour l’entrée de texte, et de deux instances [`Button`](xref:Xamarin.Forms.Button) qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux instances `Button` sont disposées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid), avec la `Editor` et `Grid` à disposition verticale dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). En outre, le `Editor` utilise la liaison de données pour établir une liaison à la propriété `Text` du modèle `Note`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [expérience approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

      Enregistrez les modifications apportées à **NoteEntryPage. Xaml** en appuyant sur **CTRL + S**et fermez le fichier.

9. Dans **NoteEntryPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Ce code stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Lorsque l' [`Button`](xref:Xamarin.Forms.Button) d’enregistrement est appuyée, le gestionnaire d’événements `OnSaveButtonClicked` est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans un nouveau fichier avec un nom de fichier généré de manière aléatoire ou un fichier existant si une note est mise à jour. Dans les deux cas, le fichier est stocké dans le dossier de données d’application local de l’application. Ensuite, la méthode revient à la page précédente. Lorsque l' `Button` **Delete** est activée, le gestionnaire d’événements `OnDeleteButtonClicked` est exécuté, ce qui a pour effet de supprimer le fichier, à condition qu’il existe, et de revenir à la page précédente. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la formation [approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

      Enregistrez les modifications apportées à **NoteEntryPage.Xaml.cs** en appuyant sur **CTRL + S**et fermez le fichier.

      > [!WARNING]
      > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

10. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Notes** et sélectionnez **Ajouter > nouvel élément.** .. Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **éléments visuels C# > page de contenu > Xamarin. Forms**, nommez le nouveau fichier **NotesPage**, puis cliquez sur le bouton **Ajouter** .

      Cette opération ajoute une page nommée **NotesPage** au dossier racine du projet. Cette page sera la page racine de l’application.

11. Dans **NotesPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) et d’un [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). L' `ListView` utilise la liaison de données pour afficher les notes récupérées par l’application, et la sélection d’une note permet d’accéder à la `NoteEntryPage` dans laquelle la note peut être modifiée. Vous pouvez également créer une nouvelle note en appuyant sur la `ToolbarItem`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [expérience approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage. Xaml** en appuyant sur **CTRL + S**et fermez le fichier.

12. Dans **NotesPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Ce code définit les fonctionnalités de l' `NotesPage`. Lorsque la page s’affiche, la méthode `OnAppearing` est exécutée, ce qui remplit le [`ListView`](xref:Xamarin.Forms.ListView) avec les notes qui ont été récupérées à partir du dossier de données d’application local. Quand la [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) est appuyée, le gestionnaire d’événements `OnNoteAddedClicked` est exécuté. Cette méthode navigue jusqu’à la `NoteEntryPage`, en affectant à la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la `NoteEntryPage` une nouvelle instance de `Note`. Lorsqu’un élément de la `ListView` est sélectionné, le gestionnaire d’événements `OnListViewItemSelected` est exécuté. Cette méthode navigue jusqu’au `NoteEntryPage`, en affectant à l’instance de `Note` sélectionnée la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du `NoteEntryPage`. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la formation [approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage.Xaml.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

13. Dans **Explorateur de solutions**, double-cliquez sur **app.Xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Ce code ajoute une déclaration d’espace de noms pour l’espace de noms `System.IO` et ajoute une déclaration pour une propriété `FolderPath` statique de type `string`. La propriété `FolderPath` est utilisée pour stocker le chemin d’accès sur l’appareil où les données de note seront stockées. En outre, le code initialise la propriété `FolderPath` dans le constructeur `App` et initialise la propriété [`MainPage`](xref:Xamarin.Forms.Application.MainPage) en tant que [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui héberge une instance de `NotesPage`. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la formation [approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

    Enregistrez les modifications apportées à **App.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

14. Dans **Explorateur de solutions**, dans le projet **Notes** , cliquez avec le bouton droit sur **MainPage. Xaml**, puis sélectionnez **supprimer**. Dans la boîte de dialogue qui s’affiche, appuyez sur le bouton **OK** pour supprimer le fichier de votre disque dur.

    Cela supprime une page qui n’est plus utilisée.

15. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [génération du démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage** , appuyez sur le bouton **+** pour accéder au **NoteEntryPage** et entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac. Dans la fenêtre Démarrer, cliquez sur **ouvrir**, puis dans la boîte de dialogue, sélectionnez le fichier solution pour le projet Notes :

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. Dans le **panneau solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouveau dossier**:

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. Dans la **panneau solutions**, nommez le nouveau dossier **modèles**:

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. Dans le **panneau solutions**, sélectionnez le dossier **Models** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouveau fichier...** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. Dans la boîte de dialogue **nouveau fichier** , sélectionnez **général > classe vide**, nommez le nouveau fichier **note**, puis cliquez sur le bouton **nouveau** :

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    Cette opération ajoute une classe nommée **note** au dossier **Models** du projet **Notes** .

6. Dans **note.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;

    namespace Notes.Models
    {
        public class Note
        {
            public string Filename { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Cette classe définit un modèle de `Note` qui stocke les données relatives à chaque note dans l’application.

    Enregistrez les modifications apportées à **note.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

7. Dans le **panneau solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouveau fichier...** . Dans la boîte de dialogue **nouveau fichier** , sélectionnez **formulaires > Forms ContentPage XAML**, nommez le nouveau fichier **NoteEntryPage**, puis cliquez sur le bouton **nouveau** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Cette opération ajoute une nouvelle page nommée **NoteEntryPage** au dossier racine du projet. Cette page sera la deuxième page de l’application.

8. Dans **NoteEntryPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

      ```xaml
      <?xml version="1.0" encoding="UTF-8"?>
      <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                   xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                   x:Class="Notes.NoteEntryPage"
                   Title="Note Entry">
          <StackLayout Margin="20">
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
                          Clicked="OnDeleteButtonClicked"/>
              </Grid>
          </StackLayout>
      </ContentPage>
      ```

      Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’une [`Editor`](xref:Xamarin.Forms.Editor) pour l’entrée de texte, et de deux instances [`Button`](xref:Xamarin.Forms.Button) qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux instances `Button` sont disposées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid), avec la `Editor` et `Grid` à disposition verticale dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout). En outre, le `Editor` utilise la liaison de données pour établir une liaison à la propriété `Text` du modèle `Note`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [expérience approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

      Enregistrez les modifications apportées à **NoteEntryPage. Xaml** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

9. Dans **NoteEntryPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

      ```csharp
      using System;
      using System.IO;
      using Xamarin.Forms;
      using Notes.Models;

      namespace Notes
      {
          public partial class NoteEntryPage : ContentPage
          {
              public NoteEntryPage()
              {
                  InitializeComponent();
              }

              async void OnSaveButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (string.IsNullOrWhiteSpace(note.Filename))
                  {
                      // Save
                      var filename = Path.Combine(App.FolderPath, $"{Path.GetRandomFileName()}.notes.txt");
                      File.WriteAllText(filename, note.Text);
                  }
                  else
                  {
                      // Update
                      File.WriteAllText(note.Filename, note.Text);
                  }

                  await Navigation.PopAsync();
              }

              async void OnDeleteButtonClicked(object sender, EventArgs e)
              {
                  var note = (Note)BindingContext;

                  if (File.Exists(note.Filename))
                  {
                      File.Delete(note.Filename);
                  }

                  await Navigation.PopAsync();
              }
          }
      }
      ```

      Ce code stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Lorsque l' [`Button`](xref:Xamarin.Forms.Button) d’enregistrement est appuyée, le gestionnaire d’événements `OnSaveButtonClicked` est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans un nouveau fichier avec un nom de fichier généré de manière aléatoire ou un fichier existant si une note est mise à jour. Dans les deux cas, le fichier est stocké dans le dossier de données d’application local de l’application. Ensuite, la méthode revient à la page précédente. Lorsque l' `Button` **Delete** est activée, le gestionnaire d’événements `OnDeleteButtonClicked` est exécuté, ce qui a pour effet de supprimer le fichier, à condition qu’il existe, et de revenir à la page précédente. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la formation [approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

      Enregistrez les modifications apportées à **NoteEntryPage.Xaml.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

      > [!WARNING]
      > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

10. Dans le **panneau solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouveau fichier...** . Dans la boîte de dialogue **nouveau fichier** , sélectionnez **formulaires > Forms ContentPage XAML**, nommez le nouveau fichier **NotesPage**, puis cliquez sur le bouton **nouveau** .

      Cette opération ajoute une page nommée **NotesPage** au dossier racine du projet. Cette page sera la page racine de l’application.

11. Dans **NotesPage. Xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="Notes.NotesPage"
                 Title="Notes">
        <ContentPage.ToolbarItems>
            <ToolbarItem Text="+"
                         Clicked="OnNoteAddedClicked" />
        </ContentPage.ToolbarItems>
        <ListView x:Name="listView"
                  Margin="20"
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

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) et d’un [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem). L' `ListView` utilise la liaison de données pour afficher les notes récupérées par l’application, et la sélection d’une note permet d’accéder à la `NoteEntryPage` dans laquelle la note peut être modifiée. Vous pouvez également créer une nouvelle note en appuyant sur la `ToolbarItem`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [expérience approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage. Xaml** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

12. Dans **NotesPage.Xaml.cs**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using Xamarin.Forms;
    using Notes.Models;

    namespace Notes
    {
        public partial class NotesPage : ContentPage
        {
            public NotesPage()
            {
                InitializeComponent();
            }

            protected override void OnAppearing()
            {
                base.OnAppearing();

                var notes = new List<Note>();

                var files = Directory.EnumerateFiles(App.FolderPath, "*.notes.txt");
                foreach (var filename in files)
                {
                    notes.Add(new Note
                    {
                        Filename = filename,
                        Text = File.ReadAllText(filename),
                        Date = File.GetCreationTime(filename)
                    });
                }

                listView.ItemsSource = notes
                    .OrderBy(d => d.Date)
                    .ToList();
            }

            async void OnNoteAddedClicked(object sender, EventArgs e)
            {
                await Navigation.PushAsync(new NoteEntryPage
                {
                    BindingContext = new Note()
                });
            }

            async void OnListViewItemSelected(object sender, SelectedItemChangedEventArgs e)
            {
                if (e.SelectedItem != null)
                {
                    await Navigation.PushAsync(new NoteEntryPage
                    {
                        BindingContext = e.SelectedItem as Note
                    });
                }
            }
        }
    }
    ```    

    Ce code définit les fonctionnalités de l' `NotesPage`. Lorsque la page s’affiche, la méthode `OnAppearing` est exécutée, ce qui remplit le [`ListView`](xref:Xamarin.Forms.ListView) avec les notes qui ont été récupérées à partir du dossier de données d’application local. Quand la [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) est appuyée, le gestionnaire d’événements `OnNoteAddedClicked` est exécuté. Cette méthode navigue jusqu’à la `NoteEntryPage`, en affectant à la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la `NoteEntryPage` une nouvelle instance de `Note`. Lorsqu’un élément de la `ListView` est sélectionné, le gestionnaire d’événements `OnListViewItemSelected` est exécuté. Cette méthode navigue jusqu’au `NoteEntryPage`, en affectant à l’instance de `Note` sélectionnée la [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) du `NoteEntryPage`. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la formation [approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

    Enregistrez les modifications apportées à **NotesPage.Xaml.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

13. Dans la **panneau solutions**, double-cliquez sur **app.Xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;

    namespace Notes
    {
        public partial class App : Application
        {
            public static string FolderPath { get; private set; }

            public App()
            {
                InitializeComponent();
                FolderPath = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData));
                MainPage = new NavigationPage(new NotesPage());
            }
            // ...
        }
    }
    ```

    Ce code ajoute une déclaration d’espace de noms pour l’espace de noms `System.IO` et ajoute une déclaration pour une propriété `FolderPath` statique de type `string`. La propriété `FolderPath` est utilisée pour stocker le chemin d’accès sur l’appareil où les données de note seront stockées. En outre, le code initialise la propriété `FolderPath` dans le constructeur `App` et initialise la propriété [`MainPage`](xref:Xamarin.Forms.Application.MainPage) en tant que [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui héberge une instance de `NotesPage`. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la formation [approfondie de démarrage rapide Xamarin. Forms](deepdive.md).

    Enregistrez les modifications apportées à **App.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

14. Dans le **panneau solutions**, dans le projet **Notes** , cliquez avec le bouton droit sur **MainPage. Xaml**, puis sélectionnez **supprimer**. Dans la boîte de dialogue qui s’affiche, appuyez sur le bouton **supprimer** pour supprimer le fichier de votre disque dur.

    Cela supprime une page qui n’est plus utilisée.

15. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [génération du démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage** , appuyez sur le bouton **+** pour accéder au **NoteEntryPage** et entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à :

- Ajoutez des pages supplémentaires à une solution Xamarin. Forms.
- Effectuer une navigation entre les pages.
- Utilisez la liaison de données pour synchroniser les données entre les éléments d’interface utilisateur et leur source de données.

Pour modifier l’application afin qu’elle stocke ses données dans une base de données SQLite.NET locale, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Suivant](database.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Présentation approfondie des Démarrages rapides Xamarin. Forms](deepdive.md)
