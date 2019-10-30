---
title: Utilisation des données dans une application iOS
description: Ce document décrit l’exemple DataAccess_Adv, qui montre comment collecter les entrées d’utilisateur et effectuer des opérations de base de données CRUD (création, lecture, mise à jour et suppression) dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 2CB8150E-CD2C-4E97-8605-1EE8CBACFEEC
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 10/11/2016
ms.openlocfilehash: 060e4b8e7856e0024e6d236652c2b04c1da16f66
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73008249"
---
# <a name="using-data-in-an-ios-app"></a>Utilisation des données dans une application iOS

L’exemple **DataAccess_Adv** illustre une application opérationnelle qui autorise les entrées d’utilisateur et les fonctionnalités de base de données *CRUD* (création, lecture, mise à jour et suppression). L’application se compose de deux écrans : une liste et un formulaire de saisie de données. Tout le code d’accès aux données peut être réutilisé dans iOS et Android sans modification.

Après avoir ajouté des données, les écrans de l’application ressemblent à ce qui suit sur iOS :

 ![](using-data-in-an-app-images/image9.png "iOS sample list")

 ![](using-data-in-an-app-images/image10.png "iOS sample detail")

Le projet iOS est illustré ci-dessous : le code présenté dans cette section est contenu dans le répertoire **ORM** :

 ![](using-data-in-an-app-images/image13.png "iOS project tree")

Le code d’interface utilisateur natif pour ViewControllers dans iOS est hors de portée pour ce document.
Pour plus d’informations sur les contrôles d’interface utilisateur, reportez-vous au Guide d' [utilisation d’iOS avec des tables et des cellules](~/ios/user-interface/controls/tables/index.md) .

## <a name="read"></a>Lecture

Il existe deux opérations de lecture dans l’exemple :

- Lecture de la liste
- Lecture d’enregistrements individuels

Les deux méthodes de la classe `StockDatabase` sont les suivantes :

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

iOS effectue le rendu des données différemment en tant que `UITableView`.

## <a name="create-and-update"></a>Créer et mettre à jour

Pour simplifier le code de l’application, une méthode Save unique est fournie, qui effectue une insertion ou une mise à jour selon que la valeur PrimaryKey a été définie ou non. Étant donné que la propriété `Id` est marquée avec un attribut `[PrimaryKey]` vous ne devez pas la définir dans votre code.
Cette méthode détecte si la valeur a été enregistrée précédemment (en vérifiant la propriété de clé primaire) et insère ou met à jour l’objet en conséquence :

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

Les applications réelles nécessitent généralement une validation (par exemple, les champs obligatoires, les longueurs minimales ou d’autres règles d’entreprise).
De bonnes applications multiplateforme implémentent autant de validation logique que possible dans le code partagé, en passant les erreurs de validation à l’interface utilisateur pour l’affichage en fonction des fonctionnalités de la plateforme.

## <a name="delete"></a>Supprimer

Contrairement aux méthodes `Insert` et `Update`, la méthode `Delete<T>` peut uniquement accepter la valeur de clé primaire plutôt qu’un objet `Stock` complet.
Dans cet exemple, un objet `Stock` est passé dans la méthode, mais seule la propriété ID est transmise à la méthode `Delete<T>`.

```csharp
public int DeleteStock(Stock stock)
{
    lock (locker) {
        return Delete<Stock> (stock.Id);
    }
}
```

## <a name="using-a-pre-populated-sqlite-database-file"></a>Utilisation d’un fichier de base de données SQLite pré-rempli

Certaines applications sont livrées avec une base de données déjà remplie de données.
Vous pouvez facilement effectuer cette opération dans votre application mobile en envoyant un fichier de base de données SQLite existant avec votre application et en le copiant dans un répertoire accessible en écriture avant d’y accéder. Étant donné que SQLite est un format de fichier standard utilisé sur de nombreuses plateformes, un certain nombre d’outils sont disponibles pour créer un fichier de base de données SQLite :

- **Extension SQLite Manager Firefox** : fonctionne sur Mac et Windows et produit des fichiers compatibles avec iOS et Android.
- **Ligne de commande** : consultez [www.sqlite.org/sqlite.html](https://www.sqlite.org/sqlite.html) .

Lorsque vous créez un fichier de base de données à distribuer avec votre application, prenez soin de nommer les tables et les colonnes pour vous assurer qu’elles correspondent à ce que votre code attend, en particulier si vous utilisez des SQLite.NET C# qui s’attendent à ce que les noms correspondent à vos classes et propriétés ( ou les attributs personnalisés associés).

Pour iOS, incluez le fichier SQLite dans votre application et assurez-vous qu’il est marqué avec **action de génération : contenu**. Placez le code dans le `FinishedLaunching` pour copier le fichier dans un répertoire accessible en écriture *avant* d’appeler des méthodes de données. Le code suivant copie une base de données existante appelée **Data. sqlite**, uniquement si elle n’existe pas déjà.

```csharp
// Copy the database across (if it doesn't exist)
var appdir = NSBundle.MainBundle.ResourcePath;
var seedFile = Path.Combine (appdir, "data.sqlite");
if (!File.Exists (Database.DatabaseFilePath))
{
  File.Copy (seedFile, Database.DatabaseFilePath);
}
```

Tout code d’accès aux données (qu’il s’agisse de ADO.NET ou d’utilisation de SQLite.NET) qui s’exécute après cette opération aura accès aux données préremplies.

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
