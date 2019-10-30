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
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026790"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Comment faire résoudre une erreur PathTooLongException ?

## <a name="cause"></a>Cause

Les noms de chemin d’accès générés dans un projet Xamarin. Android peuvent être assez longs.
Par exemple, un chemin d’accès semblable à ce qui suit peut être généré pendant une génération :

**C :\\certains\\Directory\\solution\\projet\\obj\\Debug\\__library_projects__\\Xamarin. Forms. Platform. Android\\library_project_imports\\ressources**

Sur Windows (où la longueur maximale d’un chemin d’accès est de [260 caractères](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** peut être généré lors de la génération du projet si un chemin d’accès généré dépasse la longueur maximale. 

## <a name="fix"></a>Corriger

La propriété `UseShortFileNames` MSBuild est définie sur `True` pour contourner cette erreur par défaut. Quand cette propriété a la valeur `True`, le processus de génération utilise des noms de chemin d’accès plus courts pour réduire la probabilité de produire un **PathTooLongException**.
Par exemple, lorsque `UseShortFileNames` est défini sur `True`, le chemin d’accès ci-dessus est abrégé en chemin d’accès semblable à ce qui suit :

**C :\\certains\\Directory\\solution\\projet\\obj\\Debug\\LP\\1\\JL\\ressources**

Pour définir cette propriété manuellement, ajoutez la propriété MSBuild suivante au fichier Project **. csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si la définition de cet indicateur ne résout pas l’erreur **PathTooLongException** , une autre approche consiste à spécifier une [racine de sortie intermédiaire commune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) pour les projets de votre solution en définissant `IntermediateOutputPath` dans le fichier Project **. csproj** . Essayez d’utiliser un chemin d’accès relativement bref. Exemple :

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Pour plus d’informations sur la définition des propriétés de génération, consultez [processus de génération](~/android/deploy-test/building-apps/build-process.md).
