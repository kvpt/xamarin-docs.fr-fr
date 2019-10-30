---
title: Puis-je modifier le chemin de sortie du fichier de la Loi sur la Loi ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 6df481688bfaa1e23cc56e6e34586f23d8ab9da6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031037"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Puis-je modifier le chemin de sortie du fichier de la Loi sur la Loi ?

## <a name="for-cycle-7-and-higher"></a>Pour le cycle 7 et les versions ultérieures
Oui, vous pouvez utiliser des cibles MSBuild personnalisées pour y parvenir. L’option la plus simple consiste probablement à copier le fichier `.ipa` une fois qu’il a été généré.

Ces étapes fonctionnent pour tout projet iOS qui utilise le moteur de génération MSBuild sur Mac ou Windows. (Remarque : tous les projets API unifiée utilisent le moteur de génération MSBuild.)

1. Ouvrez le fichier `.csproj` pour le projet d’application iOS dans un éditeur de texte, puis ajoutez les lignes suivantes à la fin (juste avant la balise de `</Project>` de fermeture) :

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
        $(CreateIpaDependsOn);
        CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>
    
    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(IpaPackagePath)"
            DestinationFolder="$(OutputPath)"
        />
    </Target>
    ```

2. Définissez DestinationFolder sur le dossier de sortie souhaité. Comme d’habitude, vous pouvez utiliser les propriétés MSBuild (telles que $ (OutputPath)) dans cet argument si vous le souhaitez.

## <a name="notes"></a>Notes

- La propriété `CreateIpaDependsOn` est définie dans le fichier `Xamarin.iOS.Common.targets` qui fait partie de Xamarin. iOS. Il se comporte comme décrit dans la section [substitution des cibles prédéfinies](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) de l’article [Comment : étendre le processus de génération Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Vous pouvez utiliser une tâche de **déplacement** plutôt qu’une tâche de **copie** si vous préférez. Si vous choisissez cette option et que vous créez sur Windows, vous devez utiliser le nom de tâche complet `<Microsoft.Build.Tasks.Move>` pour éviter toute ambiguïté avec les tâches de génération XamarinVS.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Pour les versions antérieures à Xamarin Studio 6.0.0.5174 | Xamarin pour Visual Studio 4.1.0.530

Oui, vous pouvez utiliser des cibles MSBuild personnalisées pour y parvenir. L’option la plus simple consiste probablement à copier le fichier `.ipa` une fois qu’il a été généré.

Ces étapes fonctionnent pour tout projet iOS qui utilise le moteur de génération MSBuild sur Mac ou Windows. (Remarque : tous les projets API unifiée utilisent le moteur de génération MSBuild.)

1. Ouvrez le fichier `.csproj` pour le projet d’application iOS dans un éditeur de texte, puis ajoutez les lignes suivantes à la fin (juste avant la balise de `</Project>` de fermeture).

    ```xml
    <PropertyGroup>
        <CreateIpaDependsOn>
            $(CreateIpaDependsOn);
            CopyIpa
        </CreateIpaDependsOn>
    </PropertyGroup>

    <Target Name="CopyIpa"
            Condition="'$(OutputType)' == 'Exe'
            And '$(ComputedPlatform)' == 'iPhone'
            And '$(BuildIpa)' == 'true'">
        <Copy
            SourceFiles="$(OutputPath)$(IpaPackageName)"
            DestinationFolder="/Users/macuser/Desktop/"
        />
    </Target>
    ```

2. Définissez le `DestinationFolder` sur le dossier de sortie souhaité. Comme d’habitude, vous pouvez utiliser les propriétés MSBuild (comme `$(OutputPath)`) dans cet argument si vous le souhaitez.

## <a name="notes"></a>Notes

- La propriété `CreateIpaDependsOn` est définie dans le fichier `Xamarin.iOS.Common.targets` qui fait partie de Xamarin. iOS. t se comporte comme décrit dans la section [substitution des cibles prédéfinies](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) de l’article [Comment : étendre le processus de génération Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Vous pouvez utiliser une tâche de **déplacement** plutôt qu’une tâche de **copie** si vous préférez. Si vous choisissez cette option et que vous créez sur Windows, vous devez utiliser le nom de tâche complet `<Microsoft.Build.Tasks.Move>` pour éviter toute ambiguïté avec les tâches de génération XamarinVS.
