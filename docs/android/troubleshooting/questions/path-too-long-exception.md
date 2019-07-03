---
title: Comment résoudre une erreur PathTooLongException ?
description: Cet article explique comment résoudre une exception PathTooLongException qui peut-être se produire lors de la création d’une application.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 443c3cc742ceb919e64a781e18c5a97c342abb44
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522926"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Comment résoudre une erreur PathTooLongException ?

## <a name="cause"></a>Cause

Les noms de chemin d’accès généré dans un projet Xamarin.Android peuvent être assez longues.
Par exemple, Impossible de générer un chemin d’accès comme suit lors de la génération :

**C:\\Some\\Directory\\Solution\\Project\\obj\\Debug\\__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\assets**

Sur Windows (où la longueur maximale pour un chemin d’accès est [260 caractères](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** pourrait être produite lors de la génération du projet si un chemin d’accès généré dépasse la longueur maximale. 

## <a name="fix"></a>Corriger

Le `UseShortFileNames` a la valeur de propriété MSBuild `True` de contourner cette erreur par défaut. Lorsque cette propriété a la valeur `True`, le processus de génération utilise des noms de chemin d’accès plus courts pour réduire le risque de produire un **PathTooLongException**.
Par exemple, lorsque `UseShortFileNames` est défini sur `True`, le chemin d’accès ci-dessus est abrégé en chemin d’accès qui est similaire à ce qui suit :

**C:\\certains\\Directory\\Solution\\projet\\obj\\déboguer\\lp\\1\\jl\\actifs**

Pour définir cette propriété manuellement, ajoutez la propriété MSBuild suivante au projet **.csproj** fichier :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si la définition de cet indicateur ne résout pas le **PathTooLongException** erreur, une autre approche consiste à spécifier un [racine de sortie intermédiaire commune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) pour les projets dans votre solution en définissant `IntermediateOutputPath` dans le projet **.csproj** fichier. Essayez d’utiliser un chemin d’accès relativement courte. Par exemple :

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Pour plus d’informations sur la définition des propriétés de build, consultez [processus de génération](~/android/deploy-test/building-apps/build-process.md).
