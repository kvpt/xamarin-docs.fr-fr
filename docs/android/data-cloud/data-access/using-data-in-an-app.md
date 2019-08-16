---
title: Utilisation des données dans une application Android
ms.prod: xamarin
ms.assetid: D5932AEB-0B6E-4F37-8B32-9BE4775AEE85
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/08/2018
ms.openlocfilehash: 62e3e6944118e82cbd7712a80eb8098f3c6541fc
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525986"
---
# <a name="using-data-in-an-app"></a>Utilisation de données dans une application

L’exemple **DataAccess_Adv** illustre une application opérationnelle qui autorise les entrées d’utilisateur et les fonctionnalités de base de données CRUD (création, lecture, mise à jour et suppression). L’application se compose de deux écrans: une liste et un formulaire de saisie de données. Tout le code d’accès aux données peut être réutilisé dans iOS et Android sans modification.

Après avoir ajouté des données, les écrans de l’application ressemblent à ce qui suit sur Android:

![Liste d’exemples Android](using-data-in-an-app-images/image11.png "Liste d’exemples Android")

![Exemple de détail Android](using-data-in-an-app-images/image12.png "Exemple de détail Android")

Le projet Android est affiché sous &ndash; le code présenté dans cette section est contenu dans le répertoire **ORM** :

![Arborescence de projet Android](using-data-in-an-app-images/image14.png "Arborescence de projet Android")

Le code d’interface utilisateur natif pour les activités dans Android est hors de portée pour ce document. Pour plus d’informations sur les contrôles d’interface utilisateur, consultez le guide des [ListView et des adaptateurs Android](~/android/user-interface/layouts/list-view/index.md) .

## <a name="read"></a>Lecture

Il existe deux opérations de lecture dans l’exemple:

- Lecture de la liste
- Lecture d’enregistrements individuels

Les deux méthodes de la `StockDatabase` classe sont:

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

Android restitue les données sous la `ListView`forme d’un.

## <a name="create-and-update"></a>Créer et mettre à jour

Pour simplifier le code de l’application, une méthode Save unique est fournie, qui effectue une insertion ou une mise à jour selon que la valeur PrimaryKey a été définie ou non. Étant donné `Id` que la propriété est marquée `[PrimaryKey]` avec un attribut, vous ne devez pas la définir dans votre code. Cette méthode détecte si la valeur a été enregistrée précédemment (en vérifiant la propriété de clé primaire) et insère ou met à jour l’objet en conséquence:

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

Les applications réelles nécessitent généralement une validation (par exemple, les champs obligatoires, les longueurs minimales ou d’autres règles d’entreprise). De bonnes applications multiplateforme implémentent autant de validation logique que possible dans le code partagé, en passant les erreurs de validation à l’interface utilisateur pour l’affichage en fonction des fonctionnalités de la plateforme.

## <a name="delete"></a>Supprimer

Contrairement aux méthodes `Update` `Delete<T>` et, la méthode peut uniquement accepter la valeur de clé primaire plutôt qu’un objet complet `Stock`. `Insert` Dans cet exemple, `Stock` un objet est passé dans la méthode, mais seule la propriété ID est passée à `Delete<T>` la méthode.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Utilisation d’un fichier de base de données SQLite pré-rempli

Certaines applications sont livrées avec une base de données déjà remplie de données. Vous pouvez facilement effectuer cette opération dans votre application mobile en envoyant un fichier de base de données SQLite existant avec votre application et en le copiant dans un répertoire accessible en écriture avant d’y accéder. Étant donné que SQLite est un format de fichier standard utilisé sur de nombreuses plateformes, un certain nombre d’outils sont disponibles pour créer un fichier de base de données SQLite:

- **Extension SQLite Manager Firefox** &ndash; Fonctionne sur Mac et Windows et produit des fichiers compatibles avec iOS et Android.

- **Ligne de commande** Consultez [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) . &ndash;

Lorsque vous créez un fichier de base de données à distribuer avec votre application, prenez soin de nommer les tables et les colonnes pour vous assurer qu’elles correspondent à ce que votre code attend, en particulier si vous utilisez des SQLite.NET C# qui s’attendent à ce que les noms correspondent à vos classes et propriétés ( ou les attributs personnalisés associés).

Pour vous assurer que du code s’exécute avant toute autre chose dans votre application Android, vous pouvez le placer dans la première activité à charger ou vous `Application` pouvez créer une sous-classe qui est chargée avant toute activité. Le code ci-dessous `Application` montre une sous-classe qui copie un fichier de base de données existant **Data. sqlite** en dehors du répertoire **/Resources/RAW/** .

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

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
