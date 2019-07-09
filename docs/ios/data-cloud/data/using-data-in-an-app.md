---
title: À l’aide des données dans une application iOS
description: Ce document décrit le DataAccess_Adv exemple qui montre comment recueillir les entrées utilisateur et effectuer créer, lire, mettre à jour et supprimer (CRUD) des opérations de base de données dans une application Xamarin.iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: a47ab26777c594658810b014025477486bbe5cc2
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650149"
---
# <a name="using-data-in-an-ios-app"></a>À l’aide des données dans une application iOS

Le **DataAccess_Adv** exemple montre une application opérationnelle qui autorise l’entrée d’utilisateur et *CRUD* les fonctionnalités de base de données (Create, Read, Update et Delete). L’application se compose de deux écrans : une liste et un formulaire de saisie de données. Tout le code d’accès aux données est réutilisable dans iOS et Android sans modification.

Après avoir ajouté des données les écrans de l’application ressembler à ceci sur iOS :

 ![](using-data-in-an-app-images/image9.png "liste d’exemples iOS")

 ![](using-data-in-an-app-images/image10.png "détails d’échantillon iOS")

IOS le projet est indiqué ci-dessous : le code présenté dans cette section est contenu dans le **Orm** directory :

 ![](using-data-in-an-app-images/image13.png "arborescence du projet iOS")

Le code de l’interface utilisateur natif pour le ViewControllers dans iOS est hors de portée pour ce document.
Reportez-vous à la [iOS utilisation des Tables et cellules](~/ios/user-interface/controls/tables/index.md) guide pour plus d’informations sur les contrôles d’interface utilisateur.

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

iOS restitue les données différemment, comme un `UITableView`.

## <a name="create-and-update"></a>Créer et mettre à jour

Pour simplifier le code d’application, une méthode de sauvegarde unique est fournie qui effectue une insertion ou mise à jour selon que la clé primaire a été défini. Étant donné que le `Id` propriété est marquée avec un `[PrimaryKey]` attribut pas définissez-le dans votre code.
Cette méthode détecte si la valeur a été précédente enregistrée (en vérifiant la propriété de clé primaire) et insérer ou mettre à jour l’objet en conséquence :

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



Les applications réelles nécessitent généralement des validations (telles que les champs obligatoires, les longueurs minimale ou les autres règles d’entreprise).
Bonne des applications multiplateformes implémentent autant de la validation logique que possible dans le code partagé, en passant des erreurs de validation vers l’interface utilisateur pour l’affichage selon les fonctionnalités de la plateforme.

## <a name="delete"></a>Supprimer

Contrairement à la `Insert` et `Update` méthodes, le `Delete<T>` méthode peut accepter uniquement la valeur de clé primaire plutôt que complète `Stock` objet.
Dans cet exemple un `Stock` objet est passé à la méthode, mais uniquement la propriété Id est passée à la `Delete<T>` (méthode).

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>À l’aide d’un fichier de base de données SQLite prérempli

Certaines applications sont livrées avec une base de données déjà remplie avec des données.
Vous pouvez le faire facilement dans votre application mobile par un fichier de base de données SQLite existant avec votre application d’expédition et de les copier dans un répertoire accessible en écriture avant d’y accéder. Étant donné que SQLite est un format de fichier standard qui est utilisé sur de nombreuses plateformes, il existe plusieurs outils disponibles pour créer un fichier de base de données SQLite :

-  **Extension de Firefox SQLite Manager** – fonctionne sur Mac et Windows et génère des fichiers qui sont compatibles avec iOS et Android.
-  **Ligne de commande** – consultez [www.sqlite.org/sqlite.html](http://www.sqlite.org/sqlite.html) .


Lorsque vous créez un fichier de base de données de distribution avec votre application, soyez attentif avec l’affectation des noms de tables et de colonnes pour vous assurer qu’ils correspondent aux attentes de votre code, en particulier si vous utilisez SQLite.NET qui s’attendent les noms pour faire correspondre vos classes c# et les propriétés (ou attributs personnalisés associés).

Pour iOS, incluez le fichier de sqlite dans votre application et vérifiez qu’il est marqué avec **Action de génération : Contenu**. Placez le code dans le `FinishedLaunching` pour copier le fichier dans un répertoire accessible en écriture *avant* vous appelez les méthodes de données. Le code suivant copie une base de données appelée **data.sqlite**, uniquement s’il n’existe pas déjà.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

N’importe quel code d’accès aux données (Si ADO.NET ou à l’aide de SQLite.NET) qui s’exécute une fois que cela a seront terminée ont accès aux données préremplies.


## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recettes de données](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
