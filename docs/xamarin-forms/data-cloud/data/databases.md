---
title: Bases de données locales Xamarin.Forms
description: Xamarin.Forms prend en charge les applications pilotées par base de données à l’aide du moteur de base de données SQLite, ce qui permet de charger et d’enregistrer des objets dans du code partagé. Cet article explique comment les applications Xamarin.Forms peuvent lire et écrire des données dans une base de données SQLite locale à l’aide de SQLite.Net.
ms.prod: xamarin
ms.assetid: F687B24B-7DF0-4F8E-A21A-A9BB507480EB
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2018
ms.openlocfilehash: 9ea105b27aacef9ca9d63af0c57de880d039ff53
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "68739181"
---
# <a name="xamarinforms-local-databases"></a>Bases de données locales Xamarin.Forms

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)

_Xamarin. Forms prend en charge les applications pilotées par base de données à l’aide du moteur de base de données SQLite, ce qui permet de charger et d’enregistrer des objets dans du code partagé. Cet article décrit comment les applications Xamarin. Forms peuvent lire et écrire des données dans une base de données SQLite locale à l’aide de SQLite.Net._

## <a name="overview"></a>Vue d'ensemble

Les applications Xamarin.Forms peuvent utiliser le package [SQLite.NET PCL NuGet](https://www.nuget.org/packages/sqlite-net-pcl/) pour incorporer des opérations de base de données dans du code partagé, en référençant les classes `SQLite` fournies avec le package NuGet. Les opérations de base de données peuvent être définies dans le projet de bibliothèque .NET Standard de la solution Xamarin.Forms.

L’[exemple d’application](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) qui l’accompagne est une simple application de liste de tâches. Les captures d’écran suivantes montrent comment l’exemple s’affiche sur chaque plateforme :

Captures d’écran d’exemples [![de bases de données Xamarin. Forms](databases-images/todo-list-sml.png "Captures d’écran de la première page TodoList")](databases-images/todo-list.png#lightbox "Captures d’écran de la première page TodoList") [ ![Xamarin. Forms](databases-images/todo-list-sml.png "Captures d’écran de la première page TodoList")](databases-images/todo-list.png#lightbox "Captures d’écran de la première page TodoList")

<a name="Using_SQLite_with_PCL" />

## <a name="using-sqlite"></a>Utilisation de SQLite

Pour ajouter la prise en charge SQLite dans une bibliothèque.NET Standard Xamarin.Forms, utilisez la fonction de recherche de NuGet pour trouver **sqlite-net-pcl** et installer le package le plus récent :

![Ajouter le package SQLite.NET PCL NuGet](databases-images/vs2017-sqlite-pcl-nuget.png "Ajouter le package SQLite.NET PCL NuGet")

Il existe plusieurs packages NuGet avec des noms similaires. Le bon package a les attributs suivants :

- **Créé par :** Frank A. Krueger
- **ID**  sqlite-net-pcl
- **Lien NuGet :** [sqlite-net-pcl](https://www.nuget.org/packages/sqlite-net-pcl/)

> [!NOTE]
> Ne vous fiez pas au nom du package. Vous devez utiliser le package NuGet **sqlite-net-pcl**, même dans les projets .NET Standard.

Une fois que la référence a été ajoutée, ajoutez une propriété à la classe `App` qui retourne un chemin de fichier local pour le stockage de la base de données :

```csharp
static TodoItemDatabase database;

public static TodoItemDatabase Database
{
  get
  {
    if (database == null)
    {
      database = new TodoItemDatabase(
        Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "TodoSQLite.db3"));
    }
    return database;
  }
}
```

Le constructeur `TodoItemDatabase`, qui prend le chemin du fichier de base de données en tant qu’argument, est indiqué ci-dessous :

```csharp
public TodoItemDatabase(string dbPath)
{
  database = new SQLiteAsyncConnection(dbPath);
  database.CreateTableAsync<TodoItem>().Wait();
}
```

Lorsque vous exposez la base de données comme un singleton, une connexion de base de données unique est créée et reste ouverte lors de l’exécution de l’application. Ainsi, vous évitez les dépenses liées à l’ouverture et à la fermeture du fichier de base de données chaque fois qu’une opération de base de données est effectuée.

Le reste de la classe `TodoItemDatabase` contient des requêtes SQLite qui s’exécutent sur plusieurs plateformes. Un exemple de code de requête est fourni ci-dessous (vous trouverez plus d’informations sur la syntaxe dans [Utilisation de SQLite.NET avec Xamarin.iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

```csharp
public Task<List<TodoItem>> GetItemsAsync()
{
  return database.Table<TodoItem>().ToListAsync();
}

public Task<List<TodoItem>> GetItemsNotDoneAsync()
{
  return database.QueryAsync<TodoItem>("SELECT * FROM [TodoItem] WHERE [Done] = 0");
}

public Task<TodoItem> GetItemAsync(int id)
{
  return database.Table<TodoItem>().Where(i => i.ID == id).FirstOrDefaultAsync();
}

public Task<int> SaveItemAsync(TodoItem item)
{
  if (item.ID != 0)
  {
    return database.UpdateAsync(item);
  }
  else {
    return database.InsertAsync(item);
  }
}

public Task<int> DeleteItemAsync(TodoItem item)
{
  return database.DeleteAsync(item);
}
```

> [!NOTE]
> L’avantage d’utiliser l’API SQLite.Net asynchrone est que les opérations de base de données sont déplacées vers les threads d’arrière-plan. En outre, il n’est pas nécessaire d’écrire du code de gestion de la concurrence, car l’API s’en charge.

## <a name="summary"></a>Récapitulatif

Xamarin.Forms prend en charge les applications pilotées par base de données à l’aide du moteur de base de données SQLite, ce qui permet de charger et d’enregistrer des objets dans du code partagé.

Cet article vous a montré comment **accéder** à une base de données SQLite à l’aide de Xamarin.Forms. Pour plus d’informations sur l’utilisation de SQLite.Net, reportez-vous à la documentation [SQLite.NET sur Android](~/android/data-cloud/data-access/using-sqlite-orm.md) ou [SQLite.NET sur iOS](~/ios/data-cloud/data/using-sqlite-orm.md).

## <a name="related-links"></a>Liens associés

- [Exemple de liste de tâches](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo)
- [Exemples Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
