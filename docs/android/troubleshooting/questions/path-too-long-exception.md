---
title: Comment faire résoudre une erreur PathTooLongException ?
description: Cet article explique comment résoudre un PathTooLongException qui peut se produire lors de la génération d’une application.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: d58cb676b347caac00c39a381de94954219d1865
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91456858"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Comment faire résoudre une erreur PathTooLongException ?

## <a name="cause"></a>Cause

Les noms de chemin d’accès générés dans un projet Xamarin. Android peuvent être assez longs.
Par exemple, un chemin d’accès semblable à ce qui suit peut être généré pendant une génération :

**C : \\ une partie du \\ projet de solution d’annuaire \\ \\ \\ obj \\ Debug \\ __Library_projects__ \\ Xamarin. Forms. Platform. Android \\ library_project_imports \\ ressources**

Sur Windows (où la longueur maximale d’un chemin d’accès est de [260 caractères](/windows/win32/fileio/naming-a-file)), un **PathTooLongException** peut être généré lors de la génération du projet si un chemin d’accès généré dépasse la longueur maximale. 

## <a name="fix"></a>Fix

La `UseShortFileNames` propriété MSBuild a la valeur `True` pour contourner cette erreur par défaut. Quand cette propriété a la valeur `True` , le processus de génération utilise des noms de chemin d’accès plus courts pour réduire la probabilité de produire un **PathTooLongException**.
Par exemple, lorsque `UseShortFileNames` a la valeur `True` , le chemin d’accès ci-dessus est abrégé en chemin d’accès semblable à ce qui suit :

**C : \\ quelques \\ ressources de projet de solution d’annuaire \\ obj de \\ \\ \\ débogage de \\ \\ \\ \\ ressources LP de débogage**

Pour définir cette propriété manuellement, ajoutez la propriété MSBuild suivante au fichier Project **. csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si la définition de cet indicateur ne résout pas l’erreur **PathTooLongException** , une autre approche consiste à spécifier une [racine de sortie intermédiaire commune](/archive/blogs/kirillosenkov/using-a-common-intermediate-and-output-directory-for-your-solution) pour les projets de votre solution en définissant `IntermediateOutputPath` dans le fichier Project **. csproj** . Essayez d’utiliser un chemin d’accès relativement bref. Par exemple :

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Pour plus d’informations sur la définition des propriétés de génération, consultez [processus de génération](~/android/deploy-test/building-apps/build-process.md).