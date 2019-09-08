---
title: Configuration
ms.prod: xamarin
ms.assetid: 44526226-4E4E-4FFF-9A16-CA7B1E01BB8F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 10/11/2016
ms.openlocfilehash: 5ebafa70239305210da631c3e9c34278f83b272b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70754662"
---
# <a name="configuration"></a>Configuration

Pour utiliser SQLite dans votre application Xamarin. Android, vous devez déterminer l’emplacement de fichier approprié pour votre fichier de base de données.

## <a name="database-file-path"></a>Chemin du fichier de base de données

Quelle que soit la méthode d’accès aux données que vous utilisez, vous devez créer un fichier de base de données pour que les données puissent être stockées avec SQLite. Selon la plateforme que vous ciblez, l’emplacement des fichiers sera différent. Pour Android, vous pouvez utiliser la classe d’environnement pour construire un chemin d’accès valide, comme illustré dans l’extrait de code suivant :

```csharp
string dbPath = Path.Combine (
        Environment.GetFolderPath (Environment.SpecialFolder.Personal),
        "database.db3");
// dbPath contains a valid file path for the database file to be stored
```

Il y a d’autres éléments à prendre en considération lorsque vous décidez de l’emplacement de stockage du fichier de base de données. Par exemple, sur Android, vous pouvez choisir d’utiliser le stockage interne ou externe.

Si vous souhaitez utiliser un emplacement différent sur chaque plateforme de votre application multiplateforme, vous pouvez utiliser une directive de compilateur comme indiqué pour générer un chemin d’accès différent pour chaque plateforme :

```csharp
var sqliteFilename = "MyDatabase.db3";
#if __ANDROID__
// Just use whatever directory SpecialFolder.Personal returns
string libraryPath = Environment.GetFolderPath(Environment.SpecialFolder.Personal); ;
#else
// we need to put in /Library/ on iOS5.1 to meet Apple's iCloud terms
// (they don't want non-user-generated data in Documents)
string documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
string libraryPath = Path.Combine (documentsPath, "..", "Library"); // Library folder instead
#endif
var path = Path.Combine (libraryPath, sqliteFilename);
```

Pour obtenir des conseils sur l’utilisation du système de fichiers dans Android, reportez-vous à la recette [Parcourir les fichiers](https://github.com/xamarin/recipes/tree/master/Recipes/android/data/files/browse_files) . Pour plus d’informations sur l’utilisation des directives de compilateur pour écrire du code spécifique à chaque plateforme, consultez le document [Building Cross Platform applications](~/cross-platform/app-fundamentals/building-cross-platform-applications/index.md) .

## <a name="threading"></a>Thread

Vous ne devez pas utiliser la même connexion de base de données SQLite sur plusieurs threads. Veillez à ouvrir, à utiliser, puis à fermer toutes les connexions que vous créez sur le même thread.

Pour vous assurer que votre code n’essaie pas d’accéder à la base de données SQLite à partir de plusieurs threads en même temps, prenez manuellement un verrou chaque fois que vous accédez à la base de données, comme suit :

```csharp
object locker = new object(); // class level private field
// rest of class code
lock (locker){
    // Do your query or insert here
}
```

Tous les accès aux bases de données (lectures, écritures, mises à jour, etc.) doivent être encapsulés avec le même verrou. Vous devez veiller à éviter une situation de blocage en veillant à ce que le travail à l’intérieur de la clause Lock soit simple et ne fasse pas appel à d’autres méthodes qui peuvent également prendre un verrou !

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
