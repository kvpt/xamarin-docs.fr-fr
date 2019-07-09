---
title: Accès aux données de Xamarin.Android
description: La plupart des applications a une exigence pour enregistrer les données sur l’appareil localement. À moins que la quantité de données est très petite, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès de base de données.  Android a le moteur de base de données SQLite 'intégré' et accès pour stocker et récupérer des données est simplifié par la plateforme de Xamarin. Ce document montre comment accéder à une base de données SQLite de manière inter-plateformes.
ms.prod: xamarin
ms.assetid: 6B47E864-C6E7-4AA2-8DEF-2C8BF551D17C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 6858e290d93007d6054ba0ef63dce86e6e2e53e3
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67649623"
---
# <a name="xamarinandroid-data-access"></a>Accès aux données de Xamarin.Android

_La plupart des applications a une exigence pour enregistrer les données sur l’appareil localement. À moins que la quantité de données est très petite, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès de base de données.  Android a le moteur de base de données SQLite 'intégré' et accès pour stocker et récupérer des données est simplifié par la plateforme de Xamarin. Ce document montre comment accéder à une base de données SQLite de manière inter-plateformes._

## <a name="data-access-overview"></a>Vue d’ensemble de données Access

La plupart des applications a une exigence pour enregistrer les données sur l’appareil localement. À moins que la quantité de données est très petite, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès de base de données. Android à la fois possède le moteur de base de données SQLite « intégré » et accès aux données est simplifié par la plateforme de Xamarin qui est fourni avec le fournisseur de données SQLite.

Xamarin.Android prend en charge API d’accès de base de données telles que :

- Framework d’ADO.NET.
- SQLite-NET 3e de bibliothèque tierce.

La majorité du code dans cette section est complètement inter-plateformes et s’exécuteront sur iOS ou Android sans modification. Il existe deux exemples d’applications abordées :

- [**DataAccess_Basic** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic) &ndash; des opérations de données Simple écrit les résultats dans un texte affichent le contrôle ;

- [**DataAccess_Advanced** ](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced) &ndash; intègre les opérations de données en une petite application qui répertorie et modifie une structure de données simple.

Les deux exemples de solutions contiennent des projets iOS et Android exemple application.

Pour les applications Xamarin.Forms, consultez [des bases de données](~/xamarin-forms/data-cloud/data/databases.md) qui explique comment utiliser SQLite dans une bibliothèque PCL avec Xamarin.Forms.

Les rubriques de cette section traitent des accès aux données dans Xamarin.Android à l’aide de SQLite en tant que le moteur de base de données. La base de données sont accessibles « directement » à l’aide de syntaxe ADO.NET ou vous pouvez inclure le SQLite.NET ORM et effectuer des opérations de données en c#.

Deux exemples sont examinés : une qui contient le code d’accès aux données très simple qui créent des sorties pour un champ de texte et une application simple qui inclut, lire, mettre à jour et supprimer des fonctionnalités. Threading et comment amorcer votre application avec une base de données SQLite préremplie sont également abordés.

Pour obtenir des exemples supplémentaires d’accès aux données d’inter-plateformes, consultez notre [Tasky Pro](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md) étude de cas.


## <a name="related-links"></a>Liens associés

- [DataAccess Basic (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Basic)
- [DataAccess avancé (exemple)](https://github.com/xamarin/mobile-samples/tree/master/DataAccess/Advanced)
- [Recettes de données Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/data)
- [Accès aux données de Xamarin.Forms](~/xamarin-forms/data-cloud/data/databases.md)
