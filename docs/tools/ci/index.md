---
title: Présentation de l’intégration continue avec Xamarin
description: Ce document contient des liens vers des guides qui décrivent l’intégration continue avec Xamarin. Contenu lié fournit une vue d’ensemble de l’intégration continue et décrit App Center Build, TeamCity et Jenkins.
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: conceptdev
ms.author: crdun
ms.date: 10/23/2018
ms.openlocfilehash: 6e1d90152fa47fef0638c93777f1e7179e97e387
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292549"
---
# <a name="continuous-integration-with-xamarin"></a>Intégration continue avec Xamarin

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Présentation de l’intégration continue](~/tools/ci/intro-to-ci.md)

Cette section traite des différents composants impliqués dans l’intégration continue et leurs relations. Il décrit les environnements d’intégration continue qui sont abordés dans les sections spécifiques ci-dessous.

## <a name="devops-with-xamarintoolscidevopsmd"></a>[DevOps avec Xamarin](~/tools/ci/devops.md)

Cette section identifie les fonctionnalités DevOps dans Azure et Visual Studio que vous pouvez vous attendre à utiliser avec un projet Xamarin.

## <a name="working-with-continuous-integration-environments"></a>Utilisation d’environnements d’intégration continue

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[Créer des applications Xamarin avec Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

Utilisez Azure Pipelines pour créer automatiquement des applications Xamarin pour Android et iOS.

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[Créer des applications Xamarin à l’aide de App Center](https://docs.microsoft.com/appcenter/build/xamarin/)

Générez des solutions Xamarin. iOS et Xamarin. Android avec App Center, directement à partir de GitHub, Azure DevOps ou bitbucket.

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[Créer des applications Xamarin avec TeamCity](~/tools/ci/teamcity.md)

Ce guide décrit les étapes nécessaires à l’utilisation de TeamCity pour compiler des applications mobiles, puis les envoyer à App Center test.

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[Créer des applications Xamarin avec Jenkins](~/tools/ci/jenkins-walkthrough.md)

Ce guide illustre la configuration de Jenkins en tant que serveur d’intégration continue et l’automatisation de la compilation d’applications mobiles créées avec Xamarin. Il décrit comment installer Jenkins sur OS X, le configurer et configurer des travaux pour compiler les applications Xamarin. iOS et Xamarin. Android lorsque des modifications sont validées dans le système de contrôle de version.
