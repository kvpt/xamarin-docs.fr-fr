---
title: Comment puis-je copier les fichiers de sortie de la liste de réutilisation dans le dossier de dépôt TFS ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4493b1a0d06e2f44ee9a11a250395f058baa0548
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031023"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Comment puis-je copier les fichiers de sortie de la liste de réutilisation dans le dossier de dépôt TFS ?

Ouvrez le fichier `.csproj` pour le projet d’application iOS dans un éditeur de texte, puis ajoutez les lignes suivantes à la fin (juste avant la balise de `</Project>` de fermeture) :

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
        And '$(BuildIpa)' == 'true'
        And '$(TF_BUILD)' == 'true'">
    <Copy
        SourceFiles="$(OutputPath)$(IpaPackageName)"
        DestinationFolder="$(TF_BUILD_BINARIESDIRECTORY)"
    />
</Target>
```

## <a name="notes"></a>Notes

- Il s’agit de la même technique générale que celle abordée dans la [possibilité de modifier le chemin de sortie du fichier de la loi](~/ios/troubleshooting/questions/ipa-output-path.md). Les deux points importants sont la définition de `$(TF_BUILD_BINARIESDIRECTORY)` comme dossier de destination et l’ajout d’une condition supplémentaire afin que `CopyIpa` s’exécute uniquement pour les builds TFS.

- Pour obtenir une description des `TF_BUILD_BINARIESDIRECTORY` consultez [variables de build prédéfinies](https://docs.microsoft.com/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Références supplémentaires

- [Documentation sur l’installation de TFS pour une utilisation avec Xamarin](https://docs.microsoft.com/azure/devops/repos/tfvc/overview)
- [Tâche de génération Azure DevOps : Xamarin. Android](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-android)
- [Tâche de génération Azure DevOps : Xamarin. iOS](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Étapes suivantes

Ce document décrit le comportement actuel de Xamarin 3.11.666 pour Visual Studio et Xamarin. iOS 8.10.3 sur l’hôte de build Mac. Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après l’utilisation des informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions et la façon de signaler un nouveau bogue si nécessaire. .
