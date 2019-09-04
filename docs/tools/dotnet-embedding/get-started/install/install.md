---
title: Installation de l’incorporation .NET
description: Ce document décrit comment installer l’incorporation .NET. Il explique comment exécuter les outils manuellement, comment générer automatiquement des liaisons, comment utiliser des cibles MSBuild personnalisées et effectuer des étapes postérieures à la génération.
ms.prod: xamarin
ms.assetid: 47106AF3-AC6E-4A0E-B30B-9F73C116DDB3
author: chamons
ms.author: chhamo
ms.date: 04/18/2018
ms.openlocfilehash: 47efbaa12475f627b5963cb6613c3441a1d96aac
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227841"
---
# <a name="installing-net-embedding"></a>Installation de l’incorporation .NET

## <a name="installing-net-embedding-from-nuget"></a>Installation de l’incorporation .NET à partir de NuGet

Choisissez **ajouter > ajouter des packages NuGet...** et installer **Embeddinator-4000** à partir du gestionnaire de package NuGet:

![Gestionnaire de package NuGet](images/visualstudionuget.png)

Cette opération installe **Embeddinator-4000. exe** et **objcgen** dans le répertoire **packages/Embeddinator-4000/Tools** .

En général, la dernière version de Embeddinator-4000 doit être sélectionnée pour le téléchargement. La prise en charge de Objective-C requiert 0,4 ou une version ultérieure.

## <a name="running-manually"></a>Exécution manuelle

Maintenant que NuGet est installé, vous pouvez exécuter les outils manuellement.

- Ouvrir un terminal (macOS) ou une invite de commandes (Windows)
- Accédez à la racine de votre solution
- Les outils sont installés dans:
  - **./Packages/Embeddinator-4000. [VERSION]/Tools/objcgen** (objective-C)
  - **./Packages/Embeddinator-4000. [VERSION]/tools/Embeddinator-4000.exe** (Java/C)
- Sur macOS, **objcgen** peut être exécutée directement.
- Sur Windows, **Embeddinator-4000. exe** peut être exécuté directement.
- Sur macOS, **Embeddinator-4000. exe** doit être exécuté avec **mono**:
  - `mono ./packages/Embeddinator-4000.[VERSION]/tools/Embeddinator-4000.exe`

Chaque appel de commande nécessite un certain nombre de listés dans la documentation spécifique à la plateforme.

## <a name="automatic-binding-generation"></a>Génération de liaison automatique

Il existe deux approches pour exécuter automatiquement une partie de votre processus de génération.

- Cibles MSBuild personnalisées
- Étapes postérieures à la génération

Bien que ce document décrira les deux, il est préférable d’utiliser une cible MSBuild personnalisée. Les étapes après génération ne s’exécutent pas nécessairement lors de la génération à partir de la ligne de commande.

### <a name="custom-msbuild-targets"></a>Cibles MSBuild personnalisées

Pour personnaliser votre build avec des cibles MSbuild, commencez par créer un fichier **Embeddinator-4000. targets** à côté de votre csproj, similaire à ce qui suit:

```xml
<Project DefaultTargets="Build" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="RunEmbeddinator" AfterTargets="AfterBuild" Inputs="$(OutputPath)/$(AssemblyName).dll" Outputs="$(IntermediateOutputPath)/Embeddinator/$(AssemblyName).framework/$(AssemblyName)">
        <Exec Command="" />
    </Target>
</Project>
```

Ici, le texte de la commande doit être rempli avec l’un des appels de l’incorporation .NET répertoriés dans la documentation spécifique à la plateforme.

Pour utiliser cette cible:

- Fermez votre projet dans Visual Studio 2017 ou Visual Studio pour Mac
- Ouvrir la bibliothèque csproj dans un éditeur de texte
- Ajoutez cette ligne au bas de la ligne, au `</Project>` -dessus de la dernière ligne:

```xml
 <Import Project="Embeddinator-4000.targets" />
```

- Rouvrir votre projet

### <a name="post-build-steps"></a>Étapes postérieures à la génération

Les étapes d’ajout d’une étape après génération pour exécuter l’incorporation .NET varient selon l’environnement de développement intégré (IDE):

#### <a name="visual-studio-for-mac"></a>Visual Studio pour Mac

Dans Visual Studio pour Mac, accédez à **Options du projet > générer > commandes personnalisées** et ajoutez une étape **après la génération** .

Configurez la commande indiquée dans la documentation spécifique à la plateforme.

> [!NOTE]
> Veillez à utiliser le numéro de version que vous avez installé à partir de NuGet

Si vous envisagez d’effectuer un développement en continu C# sur le projet, vous pouvez également ajouter une commande personnalisée pour nettoyer le répertoire de **sortie** avant d’exécuter l’incorporation .net:

```shell
rm -Rf '${SolutionDir}/output/'
```

![Action de génération personnalisée](images/visualstudiocustombuild.png)

> [!NOTE]
> La liaison générée sera placée dans le répertoire indiqué par le `--outdir` paramètre ou. `--out` Le nom de liaison généré peut varier, car certaines plateformes ont des limitations sur les noms de packages.

#### <a name="visual-studio-2017"></a>Visual Studio 2017

Nous allons essentiellement configurer la même chose, mais les menus de Visual Studio 2017 sont légèrement différents. Les commandes de l’interpréteur de commandes sont également légèrement différentes.

Accédez à **Options du projet > événements de build** , puis entrez la commande indiquée dans la documentation spécifique à la plateforme dans la zone ligne de commande de l' **événement après génération** . Par exemple :

![Intégration .NET sur Windows](images/visualstudiowindows.png)
 