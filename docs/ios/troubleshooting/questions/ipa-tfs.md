---
title: Comment puis-je copier les fichiers de sortie de la liste de réutilisation dans le dossier de dépôt TFS ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: B0F1E09E-7315-45BA-B7FF-44D2063EE19C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 2542eace19cf08a994be99379566e9e621c27190
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91435576"
---
# <a name="how-can-i-copy-ipa-output-files-to-the-tfs-drop-folder"></a>Comment puis-je copier les fichiers de sortie de la liste de réutilisation dans le dossier de dépôt TFS ?

Ouvrez le `.csproj` fichier pour le projet d’application iOS dans un éditeur de texte, puis ajoutez les lignes suivantes à la fin (juste avant la `</Project>` balise de fermeture) :

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

- Il s’agit de la même technique générale que celle abordée dans la [possibilité de modifier le chemin de sortie du fichier de la loi](~/ios/troubleshooting/questions/ipa-output-path.md). Les deux points importants doivent être définis `$(TF_BUILD_BINARIESDIRECTORY)` en tant que dossier de destination et ajouter une condition supplémentaire pour que `CopyIpa` ne s’exécute que pour les builds TFS.

- Pour obtenir une description de `TF_BUILD_BINARIESDIRECTORY` , consultez [variables de build prédéfinies](/azure/devops/pipelines/build/variables).

## <a name="additional-references"></a>Références supplémentaires

- [Documentation sur l’installation de TFS pour une utilisation avec Xamarin](/azure/devops/repos/tfvc/overview)
- [Tâche de génération Azure DevOps : Xamarin. Android](/azure/devops/pipelines/tasks/build/xamarin-android)
- [Tâche de génération Azure DevOps : Xamarin. iOS](/azure/devops/pipelines/tasks/build/xamarin-ios)

### <a name="next-steps"></a>Étapes suivantes

Ce document décrit le comportement actuel de Xamarin 3.11.666 pour Visual Studio et Xamarin. iOS 8.10.3 sur l’hôte de build Mac. Pour obtenir de l’aide, pour nous contacter ou, si le problème persiste même après avoir pris en charge les informations ci-dessus, consultez [les options de support disponibles pour Xamarin ?](~/cross-platform/troubleshooting/support-options.md) pour plus d’informations sur les options de contact, les suggestions, ainsi que la façon de signaler un nouveau bogue si nécessaire.