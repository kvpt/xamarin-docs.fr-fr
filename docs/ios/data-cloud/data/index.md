---
title: Accès aux données de Xamarin.iOS
description: Ce document contient des liens vers des guides qui décrivent comment travailler avec des bases de données locales dans une application Xamarin.iOS. Contenu lié traite SQLite.NET, ADO.NET et bien plus encore.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 420f52a055dc1c03a017723ab34c2fc3b5363656
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650229"
---
# <a name="xamarinios-data-access"></a>Accès aux données de Xamarin.iOS

Xamarin.iOS prend en charge les API d’accès de base de données telles que :

-  Framework d’ADO.NET.
-  SQLite-NET 3e de bibliothèque tierce.

Ce guide fournit une vue d’ensemble des bases de données en général, avant de décrire comment configurer SQLite.NET et ADO.NET pour accéder aux bases de données SQLite dans vos applications Xamarin.iOS. 

La majorité du code dans ce document est complètement inter-plateformes et s’exécuteront sur iOS ou Android sans modification. Il existe deux exemples d’applications abordées :

-  [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) – opérations de données Simple écrit les résultats dans un texte affichent le contrôle ;
-  [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) – intègre les opérations de données en une petite application qui répertorie et modifie une structure de données simple.

Les deux exemples de solutions contiennent des projets iOS et Android exemple application.

Pour les applications Xamarin.Forms, consultez [des bases de données](~/xamarin-forms/data-cloud/data/databases.md) qui explique comment utiliser SQLite dans une bibliothèque PCL avec Xamarin.Forms.

## <a name="sections"></a>Sections

-  [Introduction](introduction.md)
-  [Configuration](configuration.md)
-  [Utilisation de SQLite.NET ORM](using-sqlite-orm.md)
-  [Utilisation d'ADO.NET](using-adonet.md)
-  [Utilisation de données dans une application](using-data-in-an-app.md)

## <a name="summary"></a>Récapitulatif

Ce chapitre décrit l’accès aux données dans Xamarin.iOS à l’aide de SQLite en tant que le moteur de base de données. La base de données est accessible comme « directement » à l’aide de la syntaxe ADO.NET ou vous pouvez inclure le SQLite.NET ORM et effectuer des opérations de données en c#.

Nous avons examiné les deux exemples : une qui contient le code d’accès aux données très simple qui créent des sorties pour un champ de texte et une application simple qui inclut, lire, mettre à jour et supprimer des fonctionnalités. Nous avons abordé également threading et comment amorcer votre application avec une base de données SQLite préremplie.

Pour obtenir des exemples supplémentaires d’accès aux données d’inter-plateformes, consultez notre [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) étude de cas.

## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [iOS recettes de données](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
