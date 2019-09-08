---
title: Comment faire résoudre une erreur PathTooLongException ?
description: Cet article explique comment résoudre un PathTooLongException qui peut se produire lors de la génération d’une application.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 60EE1C8D-BE44-4612-B3B5-70316D71B1EA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/29/2018
ms.openlocfilehash: 915f557db7955dc7b8b9f1bc5e014a683740052b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70760817"
---
# <a name="how-do-i-resolve-a-pathtoolongexception-error"></a>Comment faire résoudre une erreur PathTooLongException ?

## <a name="cause"></a>Cause

Les noms de chemin d’accès générés dans un projet Xamarin. Android peuvent être assez longs.
Par exemple, un chemin d’accès semblable à ce qui suit peut être généré pendant une génération :

**C :\\un\\répertoire\\de\\projet\\desolution\\d’annuaire obj Debug__library_projects__Xamarin. Forms. Platform. Android\\\\\\ressourceslibrary_project_imports\\**

Sur Windows (où la longueur maximale d’un chemin d’accès est de [260 caractères](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx)), un **PathTooLongException** peut être généré lors de la génération du projet si un chemin d’accès généré dépasse la longueur maximale. 

## <a name="fix"></a>Corriger

La `UseShortFileNames` propriété MSBuild a la `True` valeur pour contourner cette erreur par défaut. Quand cette propriété a la valeur `True`, le processus de génération utilise des noms de chemin d’accès plus courts pour réduire la probabilité de produire un **PathTooLongException**.
Par exemple, lorsque `UseShortFileNames` a la `True`valeur, le chemin d’accès ci-dessus est abrégé en chemin d’accès semblable à ce qui suit :

**C :\\quelques\\\\\\\\\\ressources de\\\\projetdesolutiond’annuaireobjdedébogagederessourcesLP\\dedébogage\\**

Pour définir cette propriété manuellement, ajoutez la propriété MSBuild suivante au fichier Project **. csproj** :

```xml
<PropertyGroup>
    <UseShortFileNames>True</UseShortFileNames>
</PropertyGroup>
```

Si la définition de cet indicateur ne résout pas l’erreur **PathTooLongException** , une autre approche consiste à spécifier une [racine de sortie intermédiaire commune](https://blogs.msdn.microsoft.com/kirillosenkov/2015/04/04/using-a-common-intermediate-and-output-directory-for-your-solution/) pour les `IntermediateOutputPath` projets de votre solution en définissant dans le fichier Project **. csproj** . Essayez d’utiliser un chemin d’accès relativement bref. Par exemple :

```xml
<PropertyGroup>
    <IntermediateOutputPath>C:\Projects\MyApp</IntermediateOutputPath>
</PropertyGroup>
```

Pour plus d’informations sur la définition des propriétés de génération, consultez [processus de génération](~/android/deploy-test/building-apps/build-process.md).
