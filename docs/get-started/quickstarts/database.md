---
title: Stocker des données dans une base de données SQLite.NET locale
description: Cet article explique comment stocker des données dans une base de données SQLite.NET local.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 5BF901BD-FDE8-4B74-B4AB-418E81745A3B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/01/2019
ms.openlocfilehash: e9aed82ef0db9de35bd4cffe7daebcaa9389e42e
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832138"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Store les données dans une base de données locale SQLite.NET

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)

Dans ce démarrage rapide, vous allez apprendre comment :

- Utilisez le Gestionnaire de Package NuGet pour ajouter un package NuGet à un projet.
- Store localement des données dans une base de données SQLite.NET.

Le démarrage rapide vous montre comment stocker des données dans une base de données SQLite.NET local. L’application finale est indiquée ci-dessous :

[![](database-images/screenshots1-sml.png "Page de commentaires")](database-images/screenshots1.png#lightbox "Page de commentaires")
[![](database-images/screenshots2-sml.png "Notez Page entrée")](database-images/screenshots2.png#lightbox "Remarque Page d’entrée")

### <a name="prerequisites"></a>Prérequis

Vous devez mener à bien le [démarrage rapide précédent](multi-page.md) avant de tenter de ce démarrage rapide. Vous pouvez également télécharger le [précédent exemple de démarrage rapide](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/MultiPage/) et l’utiliser comme point de départ pour ce démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio et ouvrez la solution de Notes.

2. Dans **l’Explorateur de solutions**, sélectionnez le **Notes** de projet, avec le bouton droit et sélectionnez **gérer les Packages NuGet...** :

    ![](database-images/vs/add-nuget-packages.png "Ajouter les paquets NuGet")    

3. Dans le **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Installer** pour l’ajouter au projet :

    ![](database-images/vs/add-package.png "Ajouter le Package")

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Author(s) :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

4. Dans **l’Explorateur de solutions**, dans le **Notes** projet, ouvrez **Note.cs** dans le **modèles** dossier et remplacer les existantes de code avec le code suivant :

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

    Cette classe définit un `Note` modèle qui stockera les données relatives à chaque note dans l’application. Le `ID` propriété est marquée avec `PrimaryKey` et `AutoIncrement` attributs pour vous assurer que chaque `Note` instance dans la base de données SQLite.NET aura un id unique fourni par SQLite.NET.

    Enregistrez les modifications apportées à **Note.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

5. Dans **l’Explorateur de solutions**, ajoutez un nouveau dossier nommé **données** à la **Notes** projet.

6. Dans **l’Explorateur de solutions**, dans le **Notes** de projet, ajoutez une nouvelle classe nommée **NoteDatabase** à la **données** dossier.

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

    Cette classe contient le code pour créer la base de données, lire les données à partir de celui-ci, écrire des données et supprimer des données à partir de celui-ci. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `NoteDatabase` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin d’accès est assurée par le `App` classe à l’étape suivante.

    Enregistrez les modifications apportées à **NoteDatabase.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

8. Dans **l’Explorateur de solutions**, dans le **Notes** de projet, double-cliquez sur **App.xaml.cs** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code définit un `Database` propriété qui crée un `NoteDatabase` instance comme un singleton, en passant le nom de fichier de la base de données comme argument à la `NoteDatabase` constructeur. Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

    Enregistrez les modifications apportées à **App.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

9. Dans **l’Explorateur de solutions**, dans le **Notes** de projet, double-cliquez sur **NotesPage.xaml.cs** pour l’ouvrir. Remplacez ensuite le `OnAppearing` méthode avec le code suivant :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Ce code remplit la [ `ListView` ](xref:Xamarin.Forms.ListView) avec des notes stockées dans la base de données.

    Enregistrez les modifications apportées à **NotesPage.xaml.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

10. Dans **l’Explorateur de solutions**, double-cliquez sur **NoteEntryPage.xaml.cs** pour l’ouvrir. Remplacez ensuite le `OnSaveButtonClicked` et `OnDeleteButtonClicked` méthodes avec le code suivant :

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

      Le `NoteEntryPage` stocke un `Note` instance, ce qui représente une seule note, dans le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Lorsque le `OnSaveButtonClicked` Gestionnaire d’événements est exécuté, le `Note` instance est enregistrée dans la base de données et l’application revient à la page précédente. Lorsque le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, le `Note` instance est supprimée de la base de données et l’application revient à la page précédente.

      Enregistrez les modifications apportées à **NoteEntryPage.xaml.cs** en appuyant sur **CTRL + S**et fermez le fichier.

11. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [création le démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** appuyez sur le **+** bouton pour accéder à la **NoteEntryPage** et entrez une note. Une fois que l’enregistrement de la note de l’application permet d’accéder à la **NotesPage**.

    Entrez le nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac et ouvrez le projet de Notes.

2. Dans le **panneau solutions**, sélectionnez le **Notes** de projet, avec le bouton droit et sélectionnez **Ajouter > ajouter des Packages NuGet...** :

    ![](database-images/vsmac/add-nuget-packages.png "Ajouter les paquets NuGet")    

3. Dans la fenêtre **Ajouter des packages**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Ajouter un package** pour l’ajouter au projet :

    ![](database-images/vsmac/add-package.png "Ajouter le Package")

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Author :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

4. Dans le **panneau solutions**, dans le **Notes** projet, ouvrez **Note.cs** dans le **modèles** dossier et remplacez le code existant par le code suivant code :

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

    Cette classe définit un `Note` modèle qui stockera les données relatives à chaque note dans l’application. Le `ID` propriété est marquée avec `PrimaryKey` et `AutoIncrement` attributs pour vous assurer que chaque `Note` instance dans la base de données SQLite.NET aura un id unique fourni par SQLite.NET.

    Enregistrez les modifications apportées à **Note.cs** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

5. Dans le **panneau solutions**, ajoutez un nouveau dossier nommé **données** à la **Notes** projet.

6. Dans le **panneau solutions**, dans le **Notes** de projet, ajoutez une nouvelle classe nommée **NoteDatabase** à la **données** dossier.

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

    Cette classe contient le code pour créer la base de données, lire les données à partir de celui-ci, écrire des données et supprimer des données à partir de celui-ci. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `NoteDatabase` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin d’accès est assurée par le `App` classe à l’étape suivante.

    Enregistrez les modifications apportées à **NoteDatabase.cs** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

8. Dans le **panneau solutions**, dans le **Notes** de projet, double-cliquez sur **App.xaml.cs** pour l’ouvrir. Puis remplacez le code existant par le code suivant :

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

    Ce code définit un `Database` propriété qui crée un `NoteDatabase` instance comme un singleton, en passant le nom de fichier de la base de données comme argument à la `NoteDatabase` constructeur. Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

    Enregistrez les modifications apportées à **App.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

9. Dans le **panneau solutions**, dans le **Notes** de projet, double-cliquez sur **NotesPage.xaml.cs** pour l’ouvrir. Remplacez ensuite le `OnAppearing` méthode avec le code suivant :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Ce code remplit la [ `ListView` ](xref:Xamarin.Forms.ListView) avec des notes stockées dans la base de données.

    Enregistrez les modifications apportées à **NotesPage.xaml.cs** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

    > [!WARNING]
    > Une tentative de génération de l’application à ce stade provoquera des erreurs qui seront résolus dans les étapes suivantes.

10. Dans le **panneau solutions**, double-cliquez sur **NoteEntryPage.xaml.cs** pour l’ouvrir. Remplacez ensuite le `OnSaveButtonClicked` et `OnDeleteButtonClicked` méthodes avec le code suivant :

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

      Le `NoteEntryPage` stocke un `Note` instance, ce qui représente une seule note, dans le [ `BindingContext` ](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. Lorsque le `OnSaveButtonClicked` Gestionnaire d’événements est exécuté, le `Note` instance est enregistrée dans la base de données et l’application revient à la page précédente. Lorsque le `OnDeleteButtonClicked` Gestionnaire d’événements est exécuté, le `Note` instance est supprimée de la base de données et l’application revient à la page précédente.

      Enregistrez les modifications apportées à **NoteEntryPage.xaml.cs** en choisissant **fichier > Enregistrer** (ou en appuyant sur  **&#8984; + S**) et fermez le fichier.

11. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [création le démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** appuyez sur le **+** bouton pour accéder à la **NoteEntryPage** et entrez une note. Une fois que l’enregistrement de la note de l’application permet d’accéder à la **NotesPage**.

    Entrez le nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris comment :

- Utilisez le Gestionnaire de Package NuGet pour ajouter un package NuGet à un projet.
- Store localement des données dans une base de données SQLite.NET.

Pour appliquer un style l’application avec des styles XAML, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Next](styling.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://developer.xamarin.com/samples/xamarin-forms/GetStarted/Notes/Database/)
- [Immersion dans Xamarin.Forms-démarrage rapide](deepdive.md)
