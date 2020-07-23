---
title: Stocker des données dans une base de données SQLite.NET locale
description: Cet article explique comment stocker des données dans une base de données SQLite.NET locale.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: a28e391c6bacd460f095c94e30b2d9433a5191e4
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86931792"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Stocker des données dans une base de données SQLite.NET locale

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Utilisez le Gestionnaire de package NuGet pour ajouter un paquet NuGet à un projet.
- Stockez les données localement dans une base de données SQLite.NET.

Le guide de démarrage rapide explique comment stocker des données dans une base de données SQLite.NET locale. L’application finale est indiquée ci-dessous :

Page de [ ![ Commentaires](database-images/screenshots1-sml.png)](database-images/screenshots1.png#lightbox "Page de notes")page 
 [ ![ entrée note](database-images/screenshots2-sml.png)](database-images/screenshots2.png#lightbox "Page d’entrée de note")

## <a name="prerequisites"></a>Prérequis

Vous devez suivre correctement le [précédent guide de démarrage rapide](multi-page.md) avant de tenter de suivre ce guide de démarrage rapide. Vous pouvez également télécharger l’[exemple du précédent guide de démarrage rapide](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/), et l’utiliser comme point de départ pour ce guide de démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio, puis ouvrez la solution Notes.

2. Dans l’**Explorateur de solutions**, sélectionnez le projet **Notes**, cliquez avec le bouton droit, puis sélectionnez **Gérer les packages NuGet** :

    ![Ajouter des paquets NuGet](database-images/vs/add-nuget-packages.png)    

3. Dans le **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Installer** pour l’ajouter au projet :

    ![Ajouter un paquet](database-images/vs/add-package.png)

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Auteur (s) :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

4. Dans l’**Explorateur de solutions**, dans le projet **Notes**, ouvrez **Note.cs** dans le dossier **Models**, puis remplacez le code existant par le code suivant :

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Cette classe définit un modèle `Note`, qui stocke les données relatives à chaque note de l’application. La propriété `ID` est marquée avec les attributs `PrimaryKey` et `AutoIncrement` pour garantir que chaque instance de `Note` dans la base de données SQLite.NET a un ID unique fourni par SQLite.NET.

    Enregistrez les changements apportés à **Note.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

5. Dans l’**Explorateur de solutions**, ajoutez un nouveau dossier nommé **Data** au projet **Notes**.

6. Dans l’**Explorateur de solutions**, dans le projet **Notes**, ajoutez une nouvelle classe nommée **NoteDatabase** au dossier **Data**.

7. Dans **NoteDatabase.cs**, remplacez le code existant par le code suivant :

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Cette classe contient du code qui permet de créer la base de données, de lire les données existantes, d’en écrire de nouvelles ou d’en supprimer. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `NoteDatabase` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin est fourni par la classe `App` à l’étape suivante.

    Enregistrez les changements apportés à **NoteDatabase.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

8. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **App.xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Ce code définit une propriété `Database`, qui crée une instance de `NoteDatabase` en tant que singleton et passe le nom de fichier de la base de données en tant qu’argument au constructeur `NoteDatabase`. Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

    Enregistrez les modifications apportées à **App.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

9. Dans l’**Explorateur de solutions**, dans le projet **Notes**, double-cliquez sur **NotesPage.xaml.cs** pour l’ouvrir. Remplacez ensuite la méthode `OnAppearing` par le code suivant :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Ce code remplit la [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes stockées dans la base de données.

    Enregistrez les changements apportés à **NotesPage.xaml.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

10. Dans l’**Explorateur de solutions**, double-cliquez sur **NoteEntryPage.xaml.cs** pour l’ouvrir. Remplacez ensuite les méthodes `OnSaveButtonClicked` et `OnDeleteButtonClicked` par le code suivant :

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage`Stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Quand le gestionnaire d’événements `OnSaveButtonClicked` s’exécute, l’instance de `Note` est enregistrée dans la base de données, et l’application revient à la page précédente. Quand le gestionnaire d’événements `OnDeleteButtonClicked` s’exécute, l’instance de `Note` est supprimée de la base de données, et l’application revient à la page précédente.

      Enregistrez les changements apportés à **NoteEntryPage.xaml.cs** en appuyant sur **Ctrl+S**, puis fermez le fichier.

11. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un certain nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac, puis ouvrez le projet Notes.

2. Dans le **Panneau Solutions**, sélectionnez le projet **Notes**, cliquez avec le bouton droit, puis sélectionnez **Ajouter > Ajouter des paquets NuGet** :

    ![Ajouter des paquets NuGet](database-images/vsmac/add-nuget-packages.png)    

3. Dans la fenêtre **Ajouter des packages**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Ajouter un package** pour l’ajouter au projet :

    ![Ajouter un paquet](database-images/vsmac/add-package.png)

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Author :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

4. Dans le **Panneau Solutions**, dans le projet **Notes**, ouvrez **Note.cs** dans le dossier **Models**, puis remplacez le code existant par le code suivant :

    ```csharp
    using System;
    using SQLite;

    namespace Notes.Models
    {
        public class Note
        {
            [PrimaryKey, AutoIncrement]
            public int ID { get; set; }
            public string Text { get; set; }
            public DateTime Date { get; set; }
        }
    }
    ```

    Cette classe définit un modèle `Note`, qui stocke les données relatives à chaque note de l’application. La propriété `ID` est marquée avec les attributs `PrimaryKey` et `AutoIncrement` pour garantir que chaque instance de `Note` dans la base de données SQLite.NET a un ID unique fourni par SQLite.NET.

    Enregistrez les changements apportés à **Note.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

5. Dans le **Panneau Solutions**, ajoutez un nouveau dossier nommé **Data** au projet **Notes**.

6. Dans le **Panneau Solutions**, dans le projet **Notes**, ajoutez une nouvelle classe nommée **NoteDatabase** au dossier **Data**.

7. Dans **NoteDatabase.cs**, remplacez le code existant par le code suivant :

    ```csharp
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using SQLite;
    using Notes.Models;

    namespace Notes.Data
    {
        public class NoteDatabase
        {
            readonly SQLiteAsyncConnection _database;

            public NoteDatabase(string dbPath)
            {
                _database = new SQLiteAsyncConnection(dbPath);
                _database.CreateTableAsync<Note>().Wait();
            }

            public Task<List<Note>> GetNotesAsync()
            {
                return _database.Table<Note>().ToListAsync();
            }

            public Task<Note> GetNoteAsync(int id)
            {
                return _database.Table<Note>()
                                .Where(i => i.ID == id)
                                .FirstOrDefaultAsync();
            }

            public Task<int> SaveNoteAsync(Note note)
            {
                if (note.ID != 0)
                {
                    return _database.UpdateAsync(note);
                }
                else
                {
                    return _database.InsertAsync(note);
                }
            }

            public Task<int> DeleteNoteAsync(Note note)
            {
                return _database.DeleteAsync(note);
            }
        }
    }
    ```

    Cette classe contient du code qui permet de créer la base de données, de lire les données existantes, d’en écrire de nouvelles ou d’en supprimer. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `NoteDatabase` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin est fourni par la classe `App` à l’étape suivante.

    Enregistrez les changements apportés à **NoteDatabase.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

8. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **App.xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

    ```csharp
    using System;
    using System.IO;
    using Xamarin.Forms;
    using Notes.Data;

    namespace Notes
    {
        public partial class App : Application
        {
            static NoteDatabase database;

            public static NoteDatabase Database
            {
                get
                {
                    if (database == null)
                    {
                        database = new NoteDatabase(Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "Notes.db3"));
                    }
                    return database;
                }
            }

            public App()
            {
                InitializeComponent();
                MainPage = new NavigationPage(new NotesPage());
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Ce code définit une propriété `Database`, qui crée une instance de `NoteDatabase` en tant que singleton et passe le nom de fichier de la base de données en tant qu’argument au constructeur `NoteDatabase`. Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

    Enregistrez les modifications apportées à **App.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

9. Dans le **Panneau Solutions**, dans le projet **Notes**, double-cliquez sur **NotesPage.xaml.cs** pour l’ouvrir. Remplacez ensuite la méthode `OnAppearing` par le code suivant :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Ce code remplit la [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes stockées dans la base de données.

    Enregistrez les changements apportés à **NotesPage.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

    > [!WARNING]
    > À ce stade, si vous tentez de générer l’application, cela entraîne des erreurs qui seront corrigées plus tard.

10. Dans le **Panneau Solutions**, double-cliquez sur **NoteEntryPage.xaml.cs** pour l’ouvrir. Remplacez ensuite les méthodes `OnSaveButtonClicked` et `OnDeleteButtonClicked` par le code suivant :

      ```csharp
      async void OnSaveButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          note.Date = DateTime.UtcNow;
          await App.Database.SaveNoteAsync(note);
          await Navigation.PopAsync();
      }

      async void OnDeleteButtonClicked(object sender, EventArgs e)
      {
          var note = (Note)BindingContext;
          await App.Database.DeleteNoteAsync(note);
          await Navigation.PopAsync();
      }
      ```    

      `NoteEntryPage`Stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Quand le gestionnaire d’événements `OnSaveButtonClicked` s’exécute, l’instance de `Note` est enregistrée dans la base de données, et l’application revient à la page précédente. Quand le gestionnaire d’événements `OnDeleteButtonClicked` s’exécute, l’instance de `Note` est supprimée de la base de données, et l’application revient à la page précédente.

      Enregistrez les changements apportés à **NoteEntryPage.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**), puis fermez le fichier.

11. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [Génération du guide de démarrage rapide](single-page.md#building-the-quickstart).

    Dans **NotesPage**, appuyez sur le bouton **+** pour accéder à **NoteEntryPage**, puis entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un certain nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Utilisez le Gestionnaire de package NuGet pour ajouter un paquet NuGet à un projet.
- Stockez les données localement dans une base de données SQLite.NET.

Pour appliquer des styles à l’application à l’aide de styles XAML, passez au prochain guide de démarrage rapide.

> [!div class="nextstepaction"]
> [Suivant](styling.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Xamarin.FormsPrésentation approfondie des Démarrages rapides](deepdive.md)
