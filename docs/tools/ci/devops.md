---
title: DevOps avec Xamarin
ms.assetid: ff978cc2-5a25-46d6-921b-e51adaa65992
author: davidortinau
ms.author: daortin
manager: crdun
ms.workload:
- xamarin
ms.date: 10/23/2018
ms.openlocfilehash: 61a7017d2ba784770d1199b6332d781b36b6d0e0
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452477"
---
# <a name="devops-with-xamarin"></a>DevOps avec Xamarin

Xamarin vous permet de générer des applications mobiles multiplateformes ciblant Android, iOS et Windows à l’aide de C#, .NET et Visual Studio. Xamarin autorise le partage d’une grande partie du code entre les plateformes. Seul un petit pourcentage du code doit être spécifique à chaque plateforme.

Le développement d'applications pour des plateformes modernes implique de nombreuses activités qui vont bien au-delà de la simple écriture de code. Ces activités, appelées DevOps (développement + opérations), couvrent le cycle de vie complet de l’application et incluent la planification et le suivi du travail, la conception et l’implémentation du code, la gestion d’un dépôt de code source, l’exécution des builds, la gestion des intégrations continues et des déploiements continus, les tests (notamment les tests unitaires et les tests d’IU), l’exécution de différentes formes de diagnostic dans les environnements de développement et de production, ainsi que la surveillance en temps réel des performances des applications et des comportements des utilisateurs via la télémétrie et l’analyse.

Visual Studio, en combinaison avec Azure DevOps Services et Team Foundation Server, offre différentes fonctionnalités DevOps. Bon nombre d'entre elles sont entièrement applicables aux projets multiplateformes. Cela est particulièrement vrai en ce qui concerne les applications Xamarin, car elles font appel à du code C# et .NET, sur lequel certains outils DevOps sont basés. D'autres outils nécessitent une intégration étroite avec les environnements de génération et d'exécution. Étant donné que les applications Xamarin s'exécutent sur des plateformes non-Windows et qu'elles utilisent l'implémentation Mono de .NET, Xamarin fournit des outils spécialisés pour répondre à certains besoins.

Les tableaux suivants identifient les fonctionnalités DevOps de Visual Studio qui donnent de bons résultats avec un projet Xamarin, et celles qui présentent des limites. Pour plus d'informations sur les fonctionnalités, cliquez sur les liens correspondants.

## <a name="agile-tools"></a>Outils agiles

Lien de référence : **[À propos des outils agiles et de la gestion de projet agile](/azure/devops/boards/backlogs/backlogs-overview?view=azure-devops)**

Commentaire général : toutes les fonctionnalités de planification et de suivi sont indépendantes du type de projet et des langages de codage.

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|Gérer les backlogs et les sprints|Oui||
|Suivi du travail|Oui||
|Collaboration dans la salle d'équipe|Oui||
|Tableaux kanban|Oui||
|Créer des rapports sur la progression et la visualiser|Oui||

## <a name="modeling"></a>Modélisation

Lien de référence : **[Analyser et modéliser l’architecture](/visualstudio/modeling/analyze-and-model-your-architecture)**

Les fonctionnalités de conception sont indépendantes du langage de codage ou fonctionnent avec les langages .NET tels que C#. Consultez [rôles des diagrammes d’architecture et de modélisation dans le développement de logiciels](/visualstudio/modeling/scenario-change-your-design-using-visualization-and-modeling#ModelingDiagramsTools) pour connaître les aspects liés au code.

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|Diagrammes de séquence|Oui||
|Graphiques de dépendance|Oui||
|Hiérarchie d'appels|Oui||
|Concepteur de classes|Oui||
|Navigateur de l'architecture|Oui||
|Diagrammes UML (cas d'usage, activité, classe, composant, séquence et DSL)|Oui||
|Diagrammes de couche|Oui||
|Validation de couche|Oui||

## <a name="code"></a>Code

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|[Utiliser Team Foundation Version Control (TFVC)](/azure/devops/repos/tfvc/overview?view=vsts) ou Azure Repos|Oui||
|[Bien démarrer avec Git dans Azure Repos](/azure/devops/repos/git/gitquickstart?view=vsts&tabs=visual-studio)|Oui||
|[Améliorer la qualité du code](/visualstudio/test/improve-code-quality)|Oui||
|[Rechercher les modifications de code et autres historiques](/visualstudio/ide/find-code-changes-and-other-history-with-codelens)|Oui|Sauf au-delà des limites spécifiques de la plateforme où l’implémentation n’est résolue qu’au moment de l’exécution.|
|[Utiliser des cartes du code pour déboguer vos applications](/visualstudio/modeling/use-code-maps-to-debug-your-applications)|Oui||

## <a name="build"></a>Build

Lien de référence : **[Azure Pipelines](/azure/devops/pipelines/index?view=vsts)**

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|Serveur TFS local|Oui|Xamarin doit être installé sur les machines de build. Celles-ci peuvent être liées à un ordinateur OSX afin de générer des applications pour iOS. Voir [utiliser TFVC](/azure/devops/repos/tfvc/overview?view=vsts)|
|Serveur de builds local lié à Azure Pipelines|Oui|Pour obtenir des instructions, consultez [agents de build et de mise en version](/azure/devops/pipelines/agents/agents?view=vsts) .|
|Service de contrôleur hébergé d’Azure Pipelines|Oui|Consultez [Générez votre application Xamarin](/azure/devops/pipelines/languages/xamarin?view=vsts&tabs=vsts).|
|Définitions de builds avec des pré-scripts et des post-scripts|Oui||
|Intégration continue, y compris les archivages contrôlés|Oui|Archivages contrôlés pour TFVC uniquement si Git utilise un modèle de requête d'extraction plutôt que des archivages.|

## <a name="test"></a>Test

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|Planification de tests, création de cas de test et organisation de suites de tests|Oui||
|Test manuel|Oui||
|Gestionnaire de tests (enregistrer et rejouer des tests)|Oui|Appareils Windows et émulateurs Android uniquement à partir de Visual Studio.|
|Couverture du code|n/a||
|[Test unitaire de votre code](/visualstudio/test/unit-test-your-code/)|Oui|Pour des cibles Windows et Android, vous pouvez utiliser les outils MSTest intégrés. Pour exécuter des tests unitaires sur Windows, Android et iOS, Xamarin recommande NUnit. Consultez [Utiliser TFVC](/azure/devops/repos/tfvc/overview?view=vsts).|
|[Utiliser UI Automation pour tester votre code](/visualstudio/test/use-ui-automation-to-test-your-code/)|Windows uniquement|L’enregistreur de test d’IU de Visual Studio est réservé à Windows. Pour toutes les plateformes, consultez [Xamarin.UITest](/appcenter/test-cloud/uitest/).|

## <a name="improve-code-quality"></a>Améliorer la qualité du code

Lien de référence : **[Améliorer la qualité du code](/visualstudio/test/improve-code-quality)**

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|[Analyser la qualité du code managé](/visualstudio/code-quality/analyzing-managed-code-quality-by-using-code-analysis)|Oui||
|[Rechercher du code dupliqué à l’aide de la détection de clone de code](/previous-versions/hh205279(v=vs.140))|Oui||
|[Mesurer la complexité et la facilité de maintenance du code managé](/visualstudio/code-quality/measuring-complexity-and-maintainability-of-managed-code)|Oui||
|[Explorateur de performances](/visualstudio/profiling/performance-explorer)|Non|Utilisez à la place le [Xamarin Profiler](../profiler/index.md) via Visual Studio pour Mac. Notez que Xamarin Profiler est actuellement disponible en version préliminaire et qu'il ne fonctionne pas pour le moment avec les cibles Windows.|
|[Analyser des problèmes de mémoire liés à .NET Framework](/visualstudio/misc/analyze-dotnet-framework-memory-issues)|Non|Visual Studio Tools ne possède pas de hook à l'infrastructure Mono pour le profilage.|

## <a name="release-management"></a>Gestion des mises en production

Lien de référence : ** [Build et mise en publication dans Azure pipelines et TFS](/azure/devops/pipelines/overview?view=vsts)**

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|Gérer les processus de publication des versions|Oui||
|Déploiement sur des serveurs pour le chargement de version test via des scripts|Oui||
|Télécharger vers le magasin d'applications|Partial|Il existe des extensions qui peuvent automatiser ce processus pour certains magasins d’applications.  Consultez [Extensions pour Azure DevOps Services](https://marketplace.visualstudio.com/VSTS), par exemple [l’extension pour Google Play](https://marketplace.visualstudio.com/items?itemName=ms-vsclient.google-play).|

## <a name="monitor-with-hockeyapp"></a>Analyser avec HockeyApp

Lien de référence : **[Analyser avec HockeyApp](https://www.hockeyapp.net/features/)**

|Caractéristique|Prise en charge par Xamarin|Commentaires supplémentaires|
|-------------|----------------------------|-------------------------|
|Analyse des incidents, télémétrie et distribution des bêta|Oui||