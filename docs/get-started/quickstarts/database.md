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
ms.openlocfilehash: 2cd4726566e73aece5d0deef90ad1feedefaa2d8
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71249677"
---
# <a name="store-data-in-a-local-sqlitenet-database"></a>Stocker des données dans une base de données SQLite.NET locale

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)

Dans ce guide de démarrage rapide, vous allez apprendre à :

- Utilisez le gestionnaire de package NuGet pour ajouter un package NuGet à un projet.
- Stockez les données localement dans une base de données SQLite.NET.

Le Guide de démarrage rapide explique comment stocker des données dans une base de données SQLite.NET locale. L’application finale est indiquée ci-dessous :

[![](database-images/screenshots1-sml.png "")Page de commentaires](database-images/screenshots1.png#lightbox "Page de notes")page entrée Note
[(database-images/screenshots2-sml.png "") ![]](database-images/screenshots2.png#lightbox "Page d’entrée de note")

## <a name="prerequisites"></a>Prérequis

Vous devez réussir le [démarrage rapide précédent](multi-page.md) avant d’essayer ce guide de démarrage rapide. Vous pouvez également télécharger l' [exemple de démarrage rapide précédent](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-multipage/) et l’utiliser comme point de départ pour ce guide de démarrage rapide.

::: zone pivot="windows"

## <a name="update-the-app-with-visual-studio"></a>Mettre à jour l’application avec Visual Studio

1. Lancez Visual Studio et ouvrez la solution notes.

2. Dans **Explorateur de solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit et sélectionnez **gérer les packages NuGet...** :

    ![](database-images/vs/add-nuget-packages.png "Ajouter les paquets NuGet")    

3. Dans le **Gestionnaire de package NuGet**, sélectionnez l’onglet **Parcourir**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Installer** pour l’ajouter au projet :

    ![](database-images/vs/add-package.png "Ajouter un package")

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Author(s) :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

4. Dans **Explorateur de solutions**, dans le projet **Notes** , ouvrez **note.cs** dans le dossier **Models** et remplacez le code existant par le code suivant :

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

    Cette classe définit un `Note` modèle qui stocke les données relatives à chaque note dans l’application. La `ID` propriété est marquée avec `PrimaryKey` les `AutoIncrement` attributs et pour garantir que `Note` chaque instance de la base de données SQLite.net aura un ID unique fourni par sqlite.net.

    Enregistrez les modifications apportées à **note.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

5. Dans **Explorateur de solutions**, ajoutez un nouveau dossier nommé **Data** au projet **Notes** .

6. Dans **Explorateur de solutions**, dans le projet **Notes** , ajoutez une nouvelle classe nommée **NoteDatabase** au dossier de **données** .

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

    Cette classe contient le code permettant de créer la base de données, de lire des données, d’y écrire des données et de supprimer des données de celle-ci. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `NoteDatabase` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin d’accès est fourni par `App` la classe à l’étape suivante.

    Enregistrez les modifications apportées à **NoteDatabase.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

8. Dans **Explorateur de solutions**, dans le projet **Notes** , double-cliquez sur **app.Xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code définit une `Database` propriété qui crée `NoteDatabase` une instance en tant que singleton, en passant le nom de fichier de la base de données `NoteDatabase` en tant qu’argument au constructeur. Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

    Enregistrez les modifications apportées à **App.xaml.cs** en appuyant sur **Ctrl+S** et fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

9. Dans **Explorateur de solutions**, dans le projet **Notes** , double-cliquez sur **NotesPage.Xaml.cs** pour l’ouvrir. Remplacez ensuite la `OnAppearing` méthode par le code suivant :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Ce code remplit la [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes stockées dans la base de données.

    Enregistrez les modifications apportées à **NotesPage.Xaml.cs** en appuyant sur **CTRL + S**et fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

10. Dans **Explorateur de solutions**, double-cliquez sur **NoteEntryPage.Xaml.cs** pour l’ouvrir. Remplacez ensuite les `OnSaveButtonClicked` méthodes `OnDeleteButtonClicked` et par le code suivant :

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

      Stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. `NoteEntryPage` Lorsque le `OnSaveButtonClicked` gestionnaire d’événements est exécuté, `Note` l’instance est enregistrée dans la base de données et l’application revient à la page précédente. Lorsque le `OnDeleteButtonClicked` gestionnaire d’événements est exécuté, `Note` l’instance est supprimée de la base de données et l’application revient à la page précédente.

      Enregistrez les modifications apportées à **NoteEntryPage.Xaml.cs** en appuyant sur **CTRL + S**et fermez le fichier.

11. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [génération du démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** , appuyez **+** sur le bouton pour accéder au **NoteEntryPage** et entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end
::: zone pivot="macos"

## <a name="update-the-app-with-visual-studio-for-mac"></a>Mettre à jour l’application avec Visual Studio pour Mac

1. Lancez Visual Studio pour Mac et ouvrez le projet notes.

2. Dans le **panneau solutions**, sélectionnez le projet **Notes** , cliquez avec le bouton droit et sélectionnez **Ajouter > ajouter des packages NuGet...** :

    ![](database-images/vsmac/add-nuget-packages.png "Ajouter les paquets NuGet")    

3. Dans la fenêtre **Ajouter des packages**, recherchez et sélectionnez le package NuGet **sqlite-net-pcl**, puis cliquez sur le bouton **Ajouter un package** pour l’ajouter au projet :

    ![](database-images/vsmac/add-package.png "Ajouter un package")

    > [!NOTE]
    > Il existe plusieurs packages NuGet portant des noms similaires. Le package correct possède ces attributs :
    > - **Author :** Frank A. Krueger
    > - **ID**  sqlite-net-pcl
    > - **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)  
    >
    > Ne vous fiez pas au nom du package. Vous devez utiliser ce package NuGet dans les projets .NET Standard.

    Ce package sera utilisé pour incorporer les opérations de base de données dans l’application.

4. Dans le **panneau solutions**, dans le projet **Notes** , ouvrez **note.cs** dans le dossier **Models** et remplacez le code existant par le code suivant :

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

    Cette classe définit un `Note` modèle qui stocke les données relatives à chaque note dans l’application. La `ID` propriété est marquée avec `PrimaryKey` les `AutoIncrement` attributs et pour garantir que `Note` chaque instance de la base de données SQLite.net aura un ID unique fourni par sqlite.net.

    Enregistrez les modifications apportées à **note.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

5. Dans la **panneau solutions**, ajoutez un nouveau dossier nommé **Data** au projet **Notes** .

6. Dans le **panneau solutions**, dans le projet **Notes** , ajoutez une nouvelle classe nommée **NoteDatabase** au dossier de **données** .

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

    Cette classe contient le code permettant de créer la base de données, de lire des données, d’y écrire des données et de supprimer des données de celle-ci. Le code utilise des API SQLite.NET asynchrones qui déplacent les opérations de base de données vers les threads d’arrière-plan. De plus, le constructeur `NoteDatabase` prend le chemin du fichier de base de données en tant qu’argument. Ce chemin d’accès est fourni par `App` la classe à l’étape suivante.

    Enregistrez les modifications apportées à **NoteDatabase.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

8. Dans le **panneau solutions**, dans le projet **Notes** , double-cliquez sur **app.Xaml.cs** pour l’ouvrir. Remplacez ensuite le code existant par le code suivant :

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

    Ce code définit une `Database` propriété qui crée `NoteDatabase` une instance en tant que singleton, en passant le nom de fichier de la base de données `NoteDatabase` en tant qu’argument au constructeur. Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

    Enregistrez les modifications apportées à **App.xaml.cs** en choisissant **Fichier > Enregistrer** (ou en appuyant sur **&#8984;+S**) et fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

9. Dans le **panneau solutions**, dans le projet **Notes** , double-cliquez sur **NotesPage.Xaml.cs** pour l’ouvrir. Remplacez ensuite la `OnAppearing` méthode par le code suivant :

    ```csharp
    protected override async void OnAppearing()
    {
        base.OnAppearing();

        listView.ItemsSource = await App.Database.GetNotesAsync();
    }
    ```    

    Ce code remplit la [`ListView`](xref:Xamarin.Forms.ListView) avec toutes les notes stockées dans la base de données.

    Enregistrez les modifications apportées à **NotesPage.Xaml.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

    > [!WARNING]
    > Toute tentative de génération de l’application à ce stade entraînera des erreurs qui seront corrigées dans les étapes suivantes.

10. Dans la **panneau solutions**, double-cliquez sur **NoteEntryPage.Xaml.cs** pour l’ouvrir. Remplacez ensuite les `OnSaveButtonClicked` méthodes `OnDeleteButtonClicked` et par le code suivant :

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

      Stocke une `Note` instance, qui représente une seule note dans le [`BindingContext`](xref:Xamarin.Forms.BindableObject.BindingContext) de la page. `NoteEntryPage` Lorsque le `OnSaveButtonClicked` gestionnaire d’événements est exécuté, `Note` l’instance est enregistrée dans la base de données et l’application revient à la page précédente. Lorsque le `OnDeleteButtonClicked` gestionnaire d’événements est exécuté, `Note` l’instance est supprimée de la base de données et l’application revient à la page précédente.

      Enregistrez les modifications apportées à **NoteEntryPage.Xaml.cs** en choisissant **fichier > enregistrer** (ou en appuyant sur  **&#8984; + S**), puis fermez le fichier.

11. Générez et exécutez le projet sur chaque plateforme. Pour plus d’informations, consultez [génération du démarrage rapide](single-page.md#building-the-quickstart).

    Sur le **NotesPage** , appuyez **+** sur le bouton pour accéder au **NoteEntryPage** et entrez une note. Une fois que vous avez enregistré la note, l’application revient à **NotesPage**.

    Entrez un nombre de notes, de longueur variable, pour observer le comportement de l’application.

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez appris à:

- Utilisez le gestionnaire de package NuGet pour ajouter un package NuGet à un projet.
- Stockez les données localement dans une base de données SQLite.NET.

Pour styliser l’application à l’aide de styles XAML, passez au démarrage rapide suivant.

> [!div class="nextstepaction"]
> [Next](styling.md)

## <a name="related-links"></a>Liens connexes

- [Notes (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/getstarted-notes-database/)
- [Présentation approfondie des Démarrages rapides Xamarin. Forms](deepdive.md)
