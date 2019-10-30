---
title: Accès aux données Xamarin. Android
description: La plupart des applications ont besoin d’enregistrer des données sur l’appareil localement. À moins que la quantité de données ne soit réduite de façon triviale, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès aux bases de données.  Android a le moteur de base de données SQLite’intégré’et l’accès au stockage et à la récupération des données est simplifié par la plate-forme de Xamarin. Ce document montre comment accéder à une base de données SQLite de manière multiplateforme.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 9906e617b7072ebf7b1213a7278d117dc4f560ab
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023857"
---
# <a name="xamarinandroid-data-access"></a>Accès aux données Xamarin. Android

_La plupart des applications ont besoin d’enregistrer des données sur l’appareil localement. À moins que la quantité de données ne soit réduite de façon triviale, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès aux bases de données.  Android a le moteur de base de données SQLite’intégré’et l’accès au stockage et à la récupération des données est simplifié par la plate-forme de Xamarin. Ce document montre comment accéder à une base de données SQLite de manière multiplateforme._

## <a name="data-access-overview"></a>Vue d’ensemble de l’accès aux données

La plupart des applications ont besoin d’enregistrer des données sur l’appareil localement. À moins que la quantité de données ne soit réduite de façon triviale, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès aux bases de données. Android a le moteur de base de données SQLite « intégré » et l’accès aux données est simplifié par la plateforme de Xamarin, qui est fournie avec le Fournisseur de données SQLite.

Xamarin. Android prend en charge les API d’accès aux bases de données telles que :

- Framework ADO.NET.
- Bibliothèque SQLite-NET tiers.

La majeure partie du code de cette section est entièrement multiplateforme et s’exécute sur iOS ou Android sans modification. Deux exemples d’applications sont abordés :

- [**DataAccess_Basic**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; opérations de données simples écrit les résultats dans un contrôle d’affichage de texte.

- [**DataAccess_Advanced**](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; intègre les opérations de données dans une petite application opérationnelle qui répertorie et modifie une structure de données simple.

Les deux exemples de solutions contiennent des exemples de projets d’application iOS et Android.

Pour les applications Xamarin. Forms, consultez [utilisation des bases de données](~/xamarin-forms/data-cloud/data/databases.md) qui explique comment utiliser SQLite dans une bibliothèque PCL avec Xamarin. Forms.

Les rubriques de cette section traitent de l’accès aux données dans Xamarin. Android à l’aide de SQLite en tant que moteur de base de données. La base de données est accessible directement à l’aide de la syntaxe ADO.NET, ou vous pouvez inclure l’ORM SQLite.NET et effectuer C#des opérations de données dans.

Deux exemples sont revus : un qui contient un code d’accès aux données très simple qui génère un résultat dans un champ de texte, et une application simple qui inclut la fonctionnalité de création, lecture, mise à jour et suppression. Le Threading et comment amorcer votre application avec une base de données SQLite préremplie est également abordé.

Pour obtenir des exemples supplémentaires d’accès aux données multiplateforme, consultez notre étude de cas [Pro Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) .

## <a name="related-links"></a>Liens associés

- [DataAccess de base (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accès aux données Xamarin. Forms](~/xamarin-forms/data-cloud/data/databases.md)
