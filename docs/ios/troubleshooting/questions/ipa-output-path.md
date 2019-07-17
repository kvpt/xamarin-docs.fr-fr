---
title: Puis-je modifier le chemin de sortie du fichier IPA ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: F5E5DCC6-F7CC-48E2-89E8-709E9C269502
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 8b0686a91f18b41aa8e2e7db071123c0d96723a0
ms.sourcegitcommit: 32c7cf8b0d00464779e4b0ea43e2fd996632ebe0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68290101"
---
# <a name="can-i-change-the-output-path-of-the-ipa-file"></a>Puis-je modifier le chemin de sortie du fichier IPA ?

## <a name="for-cycle-7-and-higher"></a>Cycle de 7 et versions ultérieures
Oui, vous pouvez utiliser des cibles de MSBuild personnalisées pour y parvenir. L’option la plus simple consiste probablement à copier le `.ipa` de fichiers après qu’il a été créé.

Ces étapes fonctionnent pour les projets iOS qui utilisant le moteur de génération MSBuild sur Mac ou Windows. (Remarque : tous les projets d’API unifiée utilisent le moteur de génération MSBuild.)

1. Ouvrez le `.csproj` de fichiers pour le projet d’application iOS dans un éditeur de texte et puis ajoutez les lignes suivantes à la fin (juste avant la fermeture `</Project>` balise) :
    
    ```
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

2. Définissez le DestinationFolder sur le dossier de sortie souhaité. Comme d’habitude, vous pouvez utiliser les propriétés MSBuild (par exemple, $(OutputPath)) au sein de cet argument si vous le souhaitez.

## <a name="notes"></a>Notes
- Le `CreateIpaDependsOn` propriété est définie dans le `Xamarin.iOS.Common.targets` fichier faisant partie de Xamarin.iOS. Il se comporte comme décrit dans la [substitution de cibles prédéfinies](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) section de l’article [Comment : Étendre le processus de génération Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Vous pouvez utiliser un **déplacer** tâche au lieu d’un **copie** de tâches si votre choix. Si vous choisissez qu’option et que vous générez sur Windows, vous devez utiliser le nom qualifié complet de tâches `<Microsoft.Build.Tasks.Move>` afin d’éviter une ambiguïté avec le XamarinVS tâches de génération.

## <a name="for-versions-before-xamarin-studio-6005174--xamarin-for-visual-studio-410530"></a>Pour les versions antérieures de Xamarin Studio 6.0.0.5174 | Xamarin pour Visual Studio 4.1.0.530

Oui, vous pouvez utiliser des cibles de MSBuild personnalisées pour y parvenir. L’option la plus simple consiste probablement à copier le `.ipa` de fichiers après qu’il a été créé.

Ces étapes fonctionnent pour les projets iOS qui utilisant le moteur de génération MSBuild sur Mac ou Windows. (Remarque : tous les projets d’API unifiée utilisent le moteur de génération MSBuild.)

1. Ouvrez le `.csproj` de fichiers pour le projet d’application iOS dans un éditeur de texte et puis ajoutez les lignes suivantes à la fin (juste avant la fermeture `</Project>` balise).

    ```csharp
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

2. Définir le `DestinationFolder` dans le dossier de sortie souhaité. Comme d’habitude, vous pouvez utiliser les propriétés MSBuild (comme `$(OutputPath)`) au sein de cet argument si vous le souhaitez.

## <a name="notes"></a>Notes
- Le `CreateIpaDependsOn` propriété est définie dans le `Xamarin.iOS.Common.targets` fichier faisant partie de Xamarin.iOS. t se comporte comme décrit dans la [substitution de cibles prédéfinies](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process#overriding-predefined-targets) section de l’article [Comment : Étendre le processus de génération Visual Studio](https://docs.microsoft.com/visualstudio/msbuild/how-to-extend-the-visual-studio-build-process).

- Vous pouvez utiliser un **déplacer** tâche au lieu d’un **copie** de tâches si votre choix. Si vous choisissez qu’option et que vous générez sur Windows, vous devez utiliser le nom qualifié complet de tâches `<Microsoft.Build.Tasks.Move>` afin d’éviter une ambiguïté avec le XamarinVS tâches de génération.
