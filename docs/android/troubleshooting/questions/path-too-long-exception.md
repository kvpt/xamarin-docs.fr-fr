---
title: Comment résoudre une erreur PathTooLongException ?
description: Cet article explique comment résoudre une conception PathTooLongException qui peut se produire lors de la construction d’une application.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/29/2018
ms.openlocfilehash: ffe88546ff58387865d71268bd64ec05c8aec3c5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026790"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Comment résoudre une erreur PathTooLongException ?

## <a name="cause"></a>Cause

Les noms de chemin générés dans un projet Xamarin.Android peuvent être assez longs.
Par exemple, un chemin comme celui-ci pourrait être généré au cours d’une build :

**c:\\\\Some\\\\Directory\\Solution\\Project\\obj Debug__library_projects__\\Xamarin.Forms.Platform.Android\\library_project_imports\\actifs**

Sur Windows (où la longueur maximale pour un chemin est [de 260 caractères](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), une **conception PathTooLongException** pourrait être produite tout en construisant le projet si un chemin généré dépasse la longueur maximale. 

## <a name="fix"></a>Fix

La `UseShortFileNames` propriété MSBuild `True` est configurée pour contourner cette erreur par défaut. Lorsque cette propriété `True`est définie à , le processus de construction utilise des noms de chemin plus courts pour réduire la probabilité de produire un **PathTooLongException**.
Par exemple, `UseShortFileNames` lorsqu’il est réglé sur `True`, le chemin ci-dessus est raccourci au chemin qui est similaire à ce qui suit:

**C:\\\\Some\\Directory\\Solution\\\\Project\\obj\\Debug lp 1\\jl\\assets**

Pour définir cette propriété manuellement, ajoutez la propriété MSBuild suivante au fichier **projet .csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si la configuration de ce drapeau ne corrige pas l’erreur **PathTooLongException,** une autre `IntermediateOutputPath` approche consiste à spécifier une racine de sortie intermédiaire commune pour [les](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) projets de votre solution en définissant le fichier projet **.csproj.** Essayez d’utiliser un chemin relativement court. Par exemple :

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Pour plus d’informations sur le réglage des propriétés de construction, voir [Processus de construction](~/android/deploy-test/building-apps/build-process.md).
