---
title: Modification des métadonnées NuGet
description: Ce document décrit comment utiliser les options de projet pour modifier les métadonnées NuGet pour les bibliothèques multiplateformes. Il aborde les métadonnées obligatoires et facultatives.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: a3e1e8d1be1f84f707c80c42adb6b8d1e3f234a9
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457014"
---
# <a name="editing-nuget-metadata"></a>Modification des métadonnées NuGet

_Utiliser les options de projet pour modifier les métadonnées NuGet pour les bibliothèques multiplateformes_

Les types de projets de bibliothèque (tels que PCL ou .NET Standard, ou le nouveau type de projet NuGet) disposent d’une section **package NuGet** dans la fenêtre **Options du projet** .

La section **Metadata** configure les valeurs utilisées dans le [fichier manifeste du package NuGet **. NuSpec** ](/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informations requises

L’onglet **général** contient quatre champs qui doivent être entrés pour générer un package NuGet :

[![Fenêtre métadonnées requises du package NuGet](metadata-images/metadata-general-sml.png)](metadata-images/metadata-general.png#lightbox)

- **ID** : identificateur du package, qui doit être unique au sein de NuGet.org (ou à l’emplacement où le package sera distribué). Suivez ces [instructions](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) et utilisez uniquement des caractères qui sont valides dans une URL (sans espaces, et évitez la plupart des caractères spéciaux).
- **Version** : choisissez un numéro de version cohérent avec les [règles de contrôle de version de NuGet](/nuget/create-packages/dependency-versions).
- **Authors** : liste de noms séparés par des virgules.
- **Description** : vue d’ensemble des fonctionnalités du package qui s’affichent lorsque les utilisateurs sélectionnent le package.

> [!NOTE]
> N’oubliez pas d’incrémenter le numéro de version lors de la génération de nouvelles versions pour la distribution à NuGet ou à d’autres utilisateurs.

Pour plus d’informations, consultez la documentation sur les [éléments requis](/nuget/schema/nuspec#required-metadata-elements) pour plus d’informations, ainsi que ces instructions détaillées sur le [choix d’un identificateur de package unique et la définition du numéro de version et la](/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) [définition d’un type de package](/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Tous les champs de cet onglet doivent être entrés. dans le cas contraire, un message d’erreur s’affiche : _«le projet n’a pas de métadonnées NuGet, de sorte qu’aucun package NuGet ne sera créé. Les métadonnées du package NuGet peuvent être spécifiées dans la section métadonnées des options du projet_ .

## <a name="optional-metadata"></a>Métadonnées facultatives

L’onglet **Détails** contient des champs facultatifs à inclure dans le fichier manifeste du package NuGet.

[![Fenêtre des métadonnées facultatives du package NuGet](metadata-images/metadata-detail-sml.png)](metadata-images/metadata-detail.png#lightbox)

Pour plus d’informations sur les champs obligatoires et facultatifs, reportez-vous à la [référence des éléments facultatifs](/nuget/schema/nuspec#optional-metadata-elements) .

> [!NOTE]
> Si le package NuGet est distribué sur [NuGet.org](https://www.nuget.org) , il est recommandé de fournir autant d’informations que possible.

## <a name="related-links"></a>Liens associés

- [Référence. NuSpec](/nuget/schema/nuspec#general-form-and-schema)