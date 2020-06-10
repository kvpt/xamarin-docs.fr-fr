---
titre : « effectuer la navigation dans une application multipage Xamarin.Forms » Description : cet article explique comment activer l’application à page unique, qui permet de stocker une seule note dans une application à plusieurs pages, qui est en mesure de stocker plusieurs remarques.
zone_pivot_groups : plateforme ms. topic : démarrage rapide ms. Prod : xamarin ms. AssetID : 9DC3B3D6-6CBC-4705-BE80-3D86A9E65F92 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 04/01/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="perform-navigation-in-a-multi-page-xamarinforms-application"></a>Effectuer une navigation dans une application à plusieurs pages Xamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Ajoutez des pages supplémentaires à une Xamarin.Forms solution.
- Naviguer entre des pages
- Utiliser la liaison de données pour synchroniser les données entre les éléments d’interface utilisateur et leur source de données

Le Guide de démarrage rapide vous guide dans la manière de transformer une application multiplateforme à une seule page, qui est Xamarin.Forms en mesure de stocker une seule note dans une application à plusieurs pages, qui est en mesure de stocker plusieurs notes. L’application finale est indiquée ci-dessous :

[![](multi-page-images/screenshots1-sml.png "Notes Page")](multi-page-images/screenshots1.png#lightbox "Notes Page")
[![](multi-page-images/screenshots2-sml.png "Note Entry Page")](multi-page-images/screenshots2.png#lightbox "Note Entry Page")

### <a name="prerequisites"></a>Prérequis

Vous devez suivre correctement le [précédent guide de démarrage rapide](single-page.md) avant de tenter de suivre ce guide de démarrage rapide. Vous pouvez également télécharger l’[exemple du précédent guide de démarrage rapide](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-singlepage/), et l’utiliser comme point de départ pour ce guide de démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio. Dans la fenêtre de démarrage, cliquez dans la liste des derniers projets/solutions sur la solution **Notes**, ou cliquez sur **Ouvrir un projet ou une solution**, puis dans la boîte de dialogue **Ouvrir une solution/un projet**, sélectionnez le fichier solution du projet Notes :

    ![](multi-page-images/vs/open-solution.png "Open Project")

2. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Notes**, puis sélectionnez **Ajouter > Nouveau dossier** :

    ![](multi-page-images/vs/add-new-item.png "Add New Item")

3. Dans l’**Explorateur de solutions**, nommez le nouveau dossier **Modèles** :

    ![](multi-page-images/vs/name-folder.png "Models Folder")

4. Dans l’**Explorateur de solutions**, sélectionnez le dossier **Modèles**, cliquez avec le bouton droit, puis sélectionnez **Ajouter > Nouvel élément** :

    ![](multi-page-images/vs/add-new-models-file.png "Add New File")

5. Dans la boîte de dialogue **Ajouter un nouvel élément**, sélectionnez **Éléments Visual C# > Classe**, nommez le nouveau fichier **Note**, puis cliquez sur le bouton **Ajouter** :

    ![](multi-page-images/vs/add-note-class.png "Add Note Class")

    Cela permet d’ajouter une classe nommée **Note** au dossier **Modèles** du projet **Notes**.

6. Dans **Note.cs**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

    Cette classe définit un modèle `Note`, qui stocke les données relatives à chaque note de l’application.    

    Enregistrez les changements apportés à **Note.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

7. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Notes** et sélectionnez **Ajouter > nouvel élément.**.. Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **éléments Visual C# > Xamarin.Forms > page contenu**, nommez le nouveau fichier **NoteEntryPage**, puis cliquez sur le bouton **Ajouter** :

    ![](multi-page-images/vs/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Cela permet d’ajouter une nouvelle page nommée **NoteEntryPage** au dossier racine du projet. Cette page est la deuxième page de l’application.

8. Dans **NoteEntryPage.xaml**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

      Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`Editor`](xref:Xamarin.Forms.Editor) pour la saisie de texte, et de deux [`Button`](xref:Xamarin.Forms.Button) instances qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux `Button` instances sont présentées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid) , avec `Editor` et `Grid` disposés verticalement dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . De plus, `Editor` utilise la liaison de données pour établir une liaison à la propriété `Text` du modèle `Note`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [ Xamarin.Forms exploration approfondie des Démarrages rapides](deepdive.md).

      Enregistrez les changements apportés à **NoteEntryPage.xaml** en appuyant sur **Ctrl+S**, puis fermez le fichier.

9. Dans **NoteEntryPage.xaml.cs**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

      Ce code stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Lorsque l' **enregistrement** [`Button`](xref:Xamarin.Forms.Button) est activé `OnSaveButtonClicked` , le gestionnaire d’événements est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans un nouveau fichier avec un nom de fichier généré de manière aléatoire ou un fichier existant si une note est mise à jour. Dans les deux cas, le fichier est stocké dans le dossier de données d’application local de l’application. La méthode revient ensuite à la page précédente. Lorsque la **suppression** `Button` est activée, le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, ce qui a pour effet de supprimer le fichier, à condition qu’il existe, et de revenir à la page précédente. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

      Enregistrez les changements apportés à **NoteEntryPage.xaml.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

      > [!WARNING]
      > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

10. Dans **Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Notes** et sélectionnez **Ajouter > nouvel élément.**.. Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **éléments Visual C# > Xamarin.Forms > page contenu**, nommez le nouveau fichier **NotesPage**, puis cliquez sur le bouton **Ajouter** .

      Cela permet d’ajouter une page nommée **NotesPage** au dossier racine du projet. Cette page représente la page racine de l’application.

11. Dans **NotesPage.xaml**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) et d’un [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . `ListView` utilise la liaison de données pour afficher les notes récupérées par l’application. La sélection d’une note permet d’accéder à `NoteEntryPage`, où elle peut être modifiée. Vous pouvez également créer une note en appuyant sur `ToolbarItem`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [ Xamarin.Forms exploration approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les changements apportés à **NotesPage.xaml** en appuyant sur **Ctrl+S**, puis fermez le fichier.

12. Dans **NotesPage.xaml.cs**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

    Ce code définit les fonctionnalités de `NotesPage`. Lorsque la page s’affiche, la `OnAppearing` méthode est exécutée, qui remplit le [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes qui ont été récupérées à partir du dossier de données d’application local. Lorsque le [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) est activé `OnNoteAddedClicked` , le gestionnaire d’événements est exécuté. Cette méthode navigue vers `NoteEntryPage` , en affectant la valeur [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `NoteEntryPage` à une nouvelle `Note` instance de. Quand un élément de `ListView` est sélectionné, le gestionnaire d’événements `OnListViewItemSelected` s’exécute. Cette méthode navigue vers `NoteEntryPage` , en affectant [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) à l' `NoteEntryPage` instance sélectionnée la valeur de `Note` . Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les changements apportés à **NotesPage.xaml.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

13. Dans l’**Explorateur de solutions**, double-cliquez sur **App.xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code ajoute une déclaration d’espace de noms pour l’espace de noms `System.IO` ainsi qu’une déclaration pour une propriété `FolderPath` statique de type `string`. La propriété `FolderPath` permet de stocker le chemin sur l’appareil où les données de notes doivent être stockées. En outre, le code initialise la `FolderPath` propriété dans le `App` constructeur et initialise la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriété pour qu’elle soit un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui héberge une instance de `NotesPage` . Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **App.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

14. Dans l’**Explorateur de solutions**, dans le projet **Notes**, cliquez avec le bouton droit sur **MainPage.xaml**, puis sélectionnez **Supprimer**. Dans la boîte de dialogue qui s’affiche, appuyez sur le bouton **OK** pour supprimer le fichier de votre disque dur.

    Cela entraîne la suppression d’une page qui n’est plus utilisée.

15. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un certain nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac. Dans la fenêtre de démarrage, cliquez sur **Ouvrir**, puis dans la boîte de dialogue, sélectionnez le fichier solution du projet Notes :

    ![](multi-page-images/vsmac/open-solution.png "Open Solution")

2. Dans le **Panneau Solutions**, sélectionnez le projet **Notes**, cliquez avec le bouton droit, puis sélectionnez **Ajouter > Nouveau dossier** :

    ![](multi-page-images/vsmac/add-new-folder.png "Add New Folder")

3. Dans le **Panneau Solutions**, nommez le nouveau dossier **Modèles** :

    ![](multi-page-images/vsmac/name-folder.png "Models Folder")

4. Dans le **Panneau Solutions**, sélectionnez le dossier **Modèles**, cliquez avec le bouton droit, puis sélectionnez **Ajouter > Nouveau fichier** :

    ![](multi-page-images/vsmac/add-new-models-file.png "Add New File")

5. Dans la boîte de dialogue **Nouveau fichier**, sélectionnez **Général > Classe vide**, nommez le nouveau fichier **Note**, puis cliquez sur le bouton **Nouveau** :

    ![](multi-page-images/vsmac/add-note-class.png "Add Note Class")

    Cela permet d’ajouter une classe nommée **Note** au dossier **Modèles** du projet **Notes**.

6. Dans **Note.cs**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

    Cette classe définit un modèle `Note`, qui stocke les données relatives à chaque note de l’application.

    Enregistrez les changements apportés à **Note.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

7. Dans le **panneau solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouveau fichier...**. Dans la boîte de dialogue **nouveau fichier** , sélectionnez **formulaires > Forms ContentPage XAML**, nommez le nouveau fichier **NoteEntryPage**, puis cliquez sur le bouton **nouveau** :

    ![](multi-page-images/vsmac/add-note-entry-page.png "Add Xamarin.Forms ContentPage")

    Cela permet d’ajouter une nouvelle page nommée **NoteEntryPage** au dossier racine du projet. Cette page est la deuxième page de l’application.

8. Dans **NoteEntryPage.xaml**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

      Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`Editor`](xref:Xamarin.Forms.Editor) pour la saisie de texte, et de deux [`Button`](xref:Xamarin.Forms.Button) instances qui indiquent à l’application d’enregistrer ou de supprimer un fichier. Les deux `Button` instances sont présentées horizontalement dans un [`Grid`](xref:Xamarin.Forms.Grid) , avec `Editor` et `Grid` disposés verticalement dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) . De plus, `Editor` utilise la liaison de données pour établir une liaison à la propriété `Text` du modèle `Note`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [ Xamarin.Forms exploration approfondie des Démarrages rapides](deepdive.md).

      Enregistrez les changements apportés à **NoteEntryPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

9. Dans **NoteEntryPage.xaml.cs**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

      Ce code stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Lorsque l' **enregistrement** [`Button`](xref:Xamarin.Forms.Button) est activé `OnSaveButtonClicked` , le gestionnaire d’événements est exécuté, ce qui permet d’enregistrer le contenu du `Editor` dans un nouveau fichier avec un nom de fichier généré de manière aléatoire ou un fichier existant si une note est mise à jour. Dans les deux cas, le fichier est stocké dans le dossier de données d’application local de l’application. La méthode revient ensuite à la page précédente. Lorsque la **suppression** `Button` est activée, le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, ce qui a pour effet de supprimer le fichier, à condition qu’il existe, et de revenir à la page précédente. Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

      Enregistrez les changements apportés à **NoteEntryPage.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

      > [!WARNING]
      > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

10. Dans le **panneau solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit, puis sélectionnez **Ajouter > nouveau fichier...**. Dans la boîte de dialogue **nouveau fichier** , sélectionnez **formulaires > Forms ContentPage XAML**, nommez le nouveau fichier **NotesPage**, puis cliquez sur le bouton **nouveau** .

      Cela permet d’ajouter une page nommée **NotesPage** au dossier racine du projet. Cette page représente la page racine de l’application.

11. Dans **NotesPage.xaml**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui se compose d’un [`ListView`](xref:Xamarin.Forms.ListView) et d’un [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) . `ListView` utilise la liaison de données pour afficher les notes récupérées par l’application. La sélection d’une note permet d’accéder à `NoteEntryPage`, où elle peut être modifiée. Vous pouvez également créer une note en appuyant sur `ToolbarItem`. Pour plus d’informations sur la liaison de données, consultez [liaison de données](deepdive.md#data-binding) dans l' [ Xamarin.Forms exploration approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les changements apportés à **NotesPage.xaml** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

12. Dans **NotesPage.xaml.cs**, supprimez l’ensemble du code du modèle, puis remplacez-le par le code suivant :

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

    Ce code définit les fonctionnalités de `NotesPage`. Lorsque la page s’affiche, la `OnAppearing` méthode est exécutée, qui remplit le [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes qui ont été récupérées à partir du dossier de données d’application local. Lorsque le [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) est activé `OnNoteAddedClicked` , le gestionnaire d’événements est exécuté. Cette méthode navigue vers `NoteEntryPage` , en affectant la valeur [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) `NoteEntryPage` à une nouvelle `Note` instance de. Quand un élément de `ListView` est sélectionné, le gestionnaire d’événements `OnListViewItemSelected` s’exécute. Cette méthode navigue vers `NoteEntryPage` , en affectant [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) à l' `NoteEntryPage` instance sélectionnée la valeur de `Note` . Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les changements apportés à **NotesPage.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

13. Dans le **Panneau Solutions**, double-cliquez sur **App.xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code ajoute une déclaration d’espace de noms pour l’espace de noms `System.IO` ainsi qu’une déclaration pour une propriété `FolderPath` statique de type `string`. La propriété `FolderPath` permet de stocker le chemin sur l’appareil où les données de notes doivent être stockées. En outre, le code initialise la `FolderPath` propriété dans le `App` constructeur et initialise la [`MainPage`](xref:Xamarin.Forms.Application.MainPage) propriété pour qu’elle soit un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) qui héberge une instance de `NotesPage` . Pour plus d’informations sur la navigation, consultez [navigation](deepdive.md#navigation) dans la présentation [ Xamarin.Forms approfondie des Démarrages rapides](deepdive.md).

    Enregistrez les modifications apportées à **App.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

14. Dans le **Panneau Solutions**, dans le projet **Notes**, cliquez avec le bouton droit sur **MainPage.xaml**, puis sélectionnez **Supprimer**. Dans la boîte de dialogue qui s’affiche, appuyez sur le bouton **Supprimer** pour supprimer le fichier de votre disque dur.

    Cela entraîne la suppression d’une page qui n’est plus utilisée.

15. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un certain nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Ajoutez des pages supplémentaires à une Xamarin.Forms solution.
- Naviguer entre des pages
- Utiliser la liaison de données pour synchroniser les données entre les éléments d’interface utilisateur et leur source de données

Pour modifier l’application afin qu’elle stocke ses données dans une base de données SQLite.NET locale, passez au prochain guide de démarrage rapide.

> [!div class="nextstepaction"]
> [Suivant](database.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/)
- [Xamarin.FormsPrésentation approfondie des Démarrages rapides](deepdive.md)
