---
title: À l’aide des données dans une application Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 7d402e6f665baa8db68d571945490a8d1ae18881
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649555"
---
# <a name="using-data-in-an-app"></a>Utilisation de données dans une application

Le **DataAccess_Adv** exemple montre une application opérationnelle qui permet l’entrée d’utilisateur et les fonctionnalités de base de données CRUD (Create, Read, Update et Delete). L’application se compose de deux écrans : une liste et un formulaire de saisie de données. Tout le code d’accès aux données est réutilisable dans iOS et Android sans modification.

Après avoir ajouté des données les écrans de l’application ressembler à ceci sur Android :

![Liste d’exemples Android](using-data-in-an-app-images/image11.png "liste d’exemples Android")

![Détail de l’exemple Android](using-data-in-an-app-images/image12.png "détail de l’exemple d’Android")

Le projet Android est indiqué ci-dessous &ndash; le code présenté dans cette section est contenu dans le **Orm** directory :

![Arborescence du projet Android](using-data-in-an-app-images/image14.png "arborescence du projet Android")

Le code de l’interface utilisateur natif pour les activités dans Android est hors de portée pour ce document. Reportez-vous à la [Android ListViews et Adapters](~/android/user-interface/layouts/list-view/index.md) guide pour plus d’informations sur les contrôles d’interface utilisateur.

## <a name="read"></a>Lecture

Il existe quelques opérations de lecture dans l’exemple :

-  Lecture de la liste
-  Lecture des enregistrements individuels

Les deux méthodes dans la `StockDatabase` classe sont :

```csharp
public IEnumerable<Stock> GetStocks ()
{
    lock (locker) {
        return (from i in Table<Stock> () select i).ToList ();
    }
}
public Stock GetStock (int id)
{
    lock (locker) {
        return Table<Stock>().FirstOrDefault(x => x.Id == id);
    }
}
```

Android restitue les données comme un `ListView`.

## <a name="create-and-update"></a>Créer et mettre à jour

Pour simplifier le code d’application, une méthode de sauvegarde unique est fournie qui effectue une insertion ou mise à jour selon que la clé primaire a été défini. Étant donné que le `Id` propriété est marquée avec un `[PrimaryKey]` attribut pas définissez-le dans votre code. Cette méthode détecte si la valeur a été précédente enregistrée (en vérifiant la propriété de clé primaire) et insérer ou mettre à jour l’objet en conséquence :

```csharp
public int SaveStock (Stock item)
{
    lock (locker) {
        if (item.Id != 0) {
            Update (item);
            return item.Id;
    } else {
            return Insert (item);
        }
    }
}
```

Les applications réelles nécessitent généralement des validations (telles que les champs obligatoires, les longueurs minimale ou les autres règles d’entreprise). Bonne des applications multiplateformes implémentent autant de la validation logique que possible dans le code partagé, en passant des erreurs de validation vers l’interface utilisateur pour l’affichage selon les fonctionnalités de la plateforme.

## <a name="delete"></a>Supprimer

Contrairement à la `Insert` et `Update` méthodes, le `Delete<T>` méthode peut accepter uniquement la valeur de clé primaire plutôt que complète `Stock` objet. Dans cet exemple un `Stock` objet est passé à la méthode, mais uniquement la propriété Id est passée à la `Delete<T>` (méthode).

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>À l’aide d’un fichier de base de données SQLite prérempli

Certaines applications sont livrées avec une base de données déjà remplie avec des données. Vous pouvez le faire facilement dans votre application mobile par un fichier de base de données SQLite existant avec votre application d’expédition et de les copier dans un répertoire accessible en écriture avant d’y accéder. Étant donné que SQLite est un format de fichier standard qui est utilisé sur de nombreuses plateformes, il existe plusieurs outils disponibles pour créer un fichier de base de données SQLite :

-   **Extension de Firefox SQLite Manager** &ndash; fonctionne sur Mac et Windows et génère des fichiers qui sont compatibles avec iOS et Android.

-   **Ligne de commande** &ndash; consultez [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .

Lorsque vous créez un fichier de base de données de distribution avec votre application, soyez attentif avec l’affectation des noms de tables et de colonnes pour vous assurer qu’ils correspondent aux attentes de votre code, en particulier si vous utilisez SQLite.NET qui s’attendent les noms pour faire correspondre vos classes c# et les propriétés (ou attributs personnalisés associés).

Pour vous assurer que le code s’exécute avant rien d’autre dans votre application Android, vous pouvez le placer dans la première activité à charger, ou vous pouvez créer un `Application` sous-classe est chargé avant toutes les activités. Le code ci-dessous montre un `Application` sous-classe qui copie un fichier de base de données existant **data.sqlite** hors du **/ressources/Raw/** directory.

```csharp
[Application]
public class YourAndroidApp : Application {
    public override void OnCreate ()
    {
        base.OnCreate ();
        var docFolder = Environment.GetFolderPath(Environment.SpecialFolder.Personal);
        Console.WriteLine ("Data path:" + Database.DatabaseFilePath);
        var dbFile = Path.Combine(docFolder, "data.sqlite"); // FILE NAME TO USE WHEN COPIED
        if (!System.IO.File.Exists(dbFile)) {
            var s = Resources.OpenRawResource(Resource.Raw.data);  // DATA FILE RESOURCE ID
            FileStream writeStream = new FileStream(dbFile, FileMode.OpenOrCreate, FileAccess.Write);
            ReadWriteStream(s, writeStream);
        }
    }
    // readStream is the stream you need to read
    // writeStream is the stream you want to write to
    private void ReadWriteStream(Stream readStream, Stream writeStream)
    {
        int Length = 256;
        Byte[] buffer = new Byte[Length];
        int bytesRead = readStream.Read(buffer, 0, Length);
        // write the required bytes
        while (bytesRead > 0)
        {
            writeStream.Write(buffer, 0, bytesRead);
            bytesRead = readStream.Read(buffer, 0, Length);
        }
        readStream.Close();
        writeStream.Close();
    }
}
```


## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
