---
ms.assetid: 814857C5-D54E-469F-97ED-EE1CAA0156BB
title: Guide de portage des applications de bureau
description: Explication simple de la façon de découpler des applications Windows Forms ou WPF existantes pour créer des applications multiplateformes à exécuter sur macOS, iOS, Android, ainsi que UWP/Windows 10.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 181034a4936b2da010a2fcd280ded1a3419d43ae
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016446"
---
# <a name="desktop-app-porting-guidance"></a>Guide de portage des applications de bureau

La plupart des codes d’application peuvent être classés dans l’un des domaines suivants :

- Code d’interface utilisateur (par exemple, fenêtres et boutons)
- contrôles tiers (par exemple, anneaux
- Logique métier (par exemple, règles de validation)
- Stockage et accès aux données locales
- Services Web et accès aux données à distance

Pour les Windows Forms et les applications WPF C# écrites avec (ou Visual Basic.net), une quantité surprenante de la logique métier, de l’accès aux données locales et du code des services Web peut être partagée entre les plateformes.

## <a name="net-portability-analyzer"></a>Analyseur de portabilité .NET

Visual Studio 2017 et versions ultérieures prennent en charge l' [Analyseur de portabilité .net](https://docs.microsoft.com/dotnet/articles/standard/portability-analyzer) ([Télécharger pour Windows](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)) qui peut examiner vos applications existantes et vous indiquer la quantité de code qui peut être portée « en l’aspect » sur d’autres plateformes. Vous pouvez en savoir plus à ce sujet à partir de cette [vidéo Channel 9](https://channel9.msdn.com/Blogs/Seth-Juarez/A-Brief-Look-at-the-NET-Portability-Analyzer).

Un outil en ligne de commande peut également être téléchargé à partir de l' [Analyseur de portabilité sur GitHub](https://github.com/Microsoft/dotnet-apiport) et utilisé pour fournir les mêmes rapports.

## <a name="x-of-my-code-is-portable-what-next"></a>«x% de mon code est portable. Et ensuite ?»

Avec un peu de chance, l’analyseur montre qu’une grande partie de votre code est portable, mais il existe certainement des parties de chaque application qui _ne peuvent pas_ être déplacées vers d’autres plateformes.

Des blocs de code différents seront probablement classés dans l’un de ces compartiments, expliqués plus en détail ci-dessous :

- Code portable réutilisable
- Code nécessitant des modifications
- Code qui n’est pas portable et qui requiert une réécriture

### <a name="re-useable-portable-code"></a>Code portable réutilisable

Le code .NET écrit par rapport aux API disponibles sur toutes les plates-formes peut être pris sans modification. Idéalement, vous pourrez déplacer tout ce code dans une bibliothèque de classes portable, une bibliothèque partagée ou une bibliothèque de .NET Standard, puis le tester dans votre application existante.

Cette bibliothèque partagée peut ensuite être ajoutée aux projets d’application pour d’autres plateformes (par exemple, Android, iOS, macOS).

### <a name="code-that-requires-changes"></a>Code nécessitant des modifications

Certaines API .NET peuvent ne pas être disponibles sur toutes les plateformes. Si ces API existent dans votre code, vous devrez réécrire ces sections pour utiliser des API multiplateformes.

Voici des exemples d’utilisation des API de réflexion disponibles dans .NET 4,6, mais qui ne sont pas disponibles sur toutes les plateformes.

Une fois que vous avez réécrit le code à l’aide des API portables, vous devez être en mesure d’empaqueter ce code dans une bibliothèque partagée et de le tester dans votre application existante.

### <a name="code-that-isnt-portable-and-requires-a-re-write"></a>Code qui n’est pas portable et qui requiert une réécriture

Parmi les exemples de code qui ne sont pas susceptibles d’être multiplateformes, citons :

- **Interface utilisateur** : les écrans Windows Forms ou WPF ne peuvent pas être utilisés dans les projets Android ou iOS, par exemple. Votre interface utilisateur doit être réécrite, à l’aide de cette [Comparaison de contrôles](~/cross-platform/desktop/controls/index.md) en tant que référence.

- Code de **stockage spécifique** à la plateforme qui repose sur une technologie spécifique à une plateforme (par exemple, une base de données SQL Server Express locale). Vous devez réécrire cette méthode à l’aide d’une alternative multiplateforme (telle que SQLite pour le moteur de base de données).
Certaines opérations du système de fichiers peuvent également avoir besoin d’être ajustées, car UWP a des API légèrement différentes pour Android et iOS (par exemple, certains systèmes de fichiers respectent la casse et d’autres non).

- **composants** tiers : Vérifiez si des composants tiers de vos applications sont disponibles sur d’autres plateformes. Certains, tels que les packages NuGet non visuels, peuvent être disponibles, mais d’autres (surtout des contrôles visuels tels que des graphiques ou des lecteurs multimédias)

## <a name="tips-for-making-code-portable"></a>Conseils pour rendre le code portable

- **Injection de dépendances** : fournissez différentes implémentations pour chaque plateforme, et

- **Approche en couche** : qu’il s’agisse de MVVM, MVC, MVP ou d’un autre modèle qui vous aide à séparer le code portable du code spécifique à la plateforme.

- **Messagerie** : vous pouvez utiliser le passage de messages dans votre code pour découpler les interactions entre les différentes parties de l’application.
