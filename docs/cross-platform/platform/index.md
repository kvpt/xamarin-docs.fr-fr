---
title: Programmation de prise en charge linguistique dans Xamarin
description: Ce document décrit les différents langages de programmation pris en charge par Xamarin. Il aborde C#, F#, portable Visual Basic.NET et modèles Razor.
ms.prod: xamarin
ms.assetid: CEE8C464-67D7-45F4-9614-EAEF5217CACC
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 6c0b8e6de0c414fb708c4027f4c536a21b6b011a
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864377"
---
# <a name="programming-language-support-in-xamarin"></a>Programmation de prise en charge linguistique dans Xamarin

## <a name="c"></a>C# 

### <a name="async-support-overviewcross-platformplatformasyncmd"></a>[Vue d’ensemble de la prise en charge asynchrone](~/cross-platform/platform/async.md)

Version 5 du C# introduit deux nouveaux mots clés pour exprimer des opérations asynchrones : async et await. Ces mots clés vous permettent d’écrire du code simple qui utilise la bibliothèque parallèle de tâches pour exécuter des opérations longues (par exemple, l’accès réseau) dans un autre thread et facilement accéder aux résultats à la fin. Les dernières versions de Xamarin.iOS et Xamarin.Android prennent en charge d’async et await : ce document fournit des explications et un exemple d’utilisation de la nouvelle syntaxe avec Xamarin.

### <a name="c-6-language-featurescross-platformplatformcsharp-sixmd"></a>[Fonctionnalités du langage C# 6](~/cross-platform/platform/csharp-six.md)

La dernière version de la C# langue – version 6 – continue d’évoluer de la langue pour avoir moins réutilisable, améliorer la clarté et la cohérence plus. Syntaxe d’initialisation plus propre, la possibilité d’utiliser `await` dans `catch/finally` blocs et la condition null `?` opérateur sont particulièrement utiles.

## <a name="ffsharpindexmd"></a>[F#](fsharp/index.md)

Création d’applications mobiles avec F# et Xamarin.

## <a name="portable-visual-basicnetcross-platformplatformvisual-basicindexmd"></a>[Portable Visual Basic.NET](~/cross-platform/platform/visual-basic/index.md)

Visual Studio prend en charge la création de bibliothèques de classes portables à l’aide de Visual Basic.NET qui peuvent ensuite être incorporées dans les applications Xamarin. Cet article montre comment créer une nouvelle bibliothèque de classes portable Visual Basic et alors l’utiliser dans un exemple d’application Xamarin.iOS, Xamarin.Android et Windows Phone.

## <a name="building-html-views-using-razor-templatescross-platformplatformrazor-html-templatesindexmd"></a>[Vues HTML de génération à l’aide de modèles Razor](~/cross-platform/platform/razor-html-templates/index.md)

Xamarin permet aux développeurs d’exploiter le moteur de création de modèles Razor, introduit initialement avec ASP.NET MVC, avec C# facilement combiner des données avec HTML, Javascript et CSS sans vous préoccuper des contraintes de la création manuelle des chaînes HTML dans le code.
Cet article montre comment utiliser des modèles Razor avec Xamarin pour Android et iOS.
