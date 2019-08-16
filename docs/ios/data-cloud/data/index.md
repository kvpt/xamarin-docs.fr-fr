---
title: Accès aux données Xamarin. iOS
description: Ce document contient des liens vers des guides qui décrivent comment utiliser les bases de données locales dans une application Xamarin. iOS. Le contenu lié aborde SQLite.NET, ADO.NET et bien plus encore.
ms.prod: xamarin
ms.assetid: 3AEDFD8D-FB10-4CEF-BE04-CCD14E95F02C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 10/11/2016
ms.openlocfilehash: 110d6c95c1bcc93d541b908807fe26c700eb656e
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69526585"
---
# <a name="xamarinios-data-access"></a>Accès aux données Xamarin. iOS

Xamarin. iOS prend en charge les API d’accès aux bases de données telles que:

- Framework ADO.NET.
- Bibliothèque SQLite-NET tiers.

Ce guide fournit une vue d’ensemble globale des bases de données en général, avant de décrire comment configurer ADO.NET et SQLite.NET pour accéder aux bases de données SQLite dans vos applications Xamarin. iOS. 

La majeure partie du code de ce document est entièrement multiplateforme et s’exécute sur iOS ou Android sans modification. Deux exemples d’applications sont abordés:

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) : les opérations de données simples écrivent les résultats dans un contrôle d’affichage de texte.
- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) : intègre des opérations de données dans une petite application opérationnelle qui répertorie et modifie une structure de données simple.

Les deux exemples de solutions contiennent des exemples de projets d’application iOS et Android.

Pour les applications Xamarin. Forms, consultez [utilisation des bases de données](~/xamarin-forms/data-cloud/data/databases.md) qui explique comment utiliser SQLite dans une bibliothèque PCL avec Xamarin. Forms.

## <a name="sections"></a>Sections

- [Introduction](introduction.md)
- [Configuration](configuration.md)
- [Utilisation de SQLite.NET ORM](using-sqlite-orm.md)
- [Utilisation d'ADO.NET](using-adonet.md)
- [Utilisation de données dans une application](using-data-in-an-app.md)

## <a name="summary"></a>Récapitulatif

Ce chapitre a abordé l’accès aux données dans Xamarin. iOS à l’aide de SQLite en tant que moteur de base de données. La base de données est accessible directement à l’aide de la syntaxe ADO.NET, ou vous pouvez inclure l’ORM SQLite.NET et C#effectuer des opérations de données dans.

Nous avons examiné deux exemples: un qui contient un code d’accès aux données très simple qui génère un résultat dans un champ de texte, et une application simple qui inclut la fonctionnalité de création, lecture, mise à jour et suppression. Nous avons également abordé les threads et comment amorcer votre application avec une base de données SQLite préremplie.

Pour obtenir des exemples supplémentaires d’accès aux données multiplateforme, consultez notre étude de cas [Pro Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) .

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données iOS](https://github.com/xamarin/recipes/tree/master/Recipes/ios/data/sqlite)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
