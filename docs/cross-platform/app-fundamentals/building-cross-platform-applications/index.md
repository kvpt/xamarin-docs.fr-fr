---
title: Création d’applications multiplateformes
description: Cette section décrit, dans un résumé, plus six parties, comment créer des applications à l’aide de la plateforme de développement Xamarin, de la compréhension de la façon dont Xamarin travaille pour concevoir des applications mobiles, puis le test et le déploiement des différents magasins d’applications.
ms.prod: xamarin
ms.assetid: 442FC40A-84DD-A218-0D15-EAD86594B6D7
author: davidortinau
ms.author: daortin
ms.date: 01/28/2016
ms.openlocfilehash: f725fe06a5438e4dbdca2773d93befc13bc8ff95
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457495"
---
# <a name="building-cross-platform-applications"></a>Création d’applications multiplateformes

Il existe deux options pour partager du code entre des applications mobiles multiplateformes : les projets de ressources partagées et les bibliothèques de classes portables. Ces options sont [décrites ici](~/cross-platform/app-fundamentals/code-sharing.md). des informations supplémentaires sur les [bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md) et les [projets partagés](~/cross-platform/app-fundamentals/shared-projects.md) sont également disponibles.

<a name="Sections"></a>

 [Vue d'ensemble](~/cross-platform/app-fundamentals/building-cross-platform-applications/overview.md)

 [Partie 1 : comprendre la plate-forme mobile Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/understanding-the-xamarin-mobile-platform.md)

 [Partie 2 – architecture](~/cross-platform/app-fundamentals/building-cross-platform-applications/architecture.md)

 [Partie 3 : configuration d’une solution Cross Platform Xamarin](~/cross-platform/app-fundamentals/building-cross-platform-applications/setting-up-a-xamarin-cross-platform-solution.md)

 [Partie 4 : gestion de plusieurs plateformes](~/cross-platform/app-fundamentals/building-cross-platform-applications/platform-divergence-abstraction-divergent-implementation.md)

 [Partie 5 : stratégies de partage de code pratiques](~/cross-platform/app-fundamentals/building-cross-platform-applications/practical-code-sharing-strategies.md)

 [Partie 6-approbations de test et d’App Store](~/cross-platform/app-fundamentals/building-cross-platform-applications/testing-and-app-store-approvals.md)

 <a name="Cross-Platform_Mobile_Application_Case_Studies"></a>

## <a name="case-studies"></a>Études de cas

Les principes énoncés dans ce document sont mis en pratique dans l’exemple d’application *Tasky*, ainsi que des [Applications prédéfinies](https://xamarin.com/prebuilt) telles que [Xamarin CRM](https://xamarin.com/prebuilt/#xamarincrm).

 <a name="Tasky"></a>

### <a name="tasky"></a>Tasky

Tasky est une application de liste de tâches simple pour iOS, Android et Windows Phone.
Il montre les principes fondamentaux de la création d’une application multiplateforme avec Xamarin et utilise une base de données SQLite locale.

 [ ![ liste](images/iphone-list-sml.png)](images/iphone-list.png#lightbox) des tâches de [ ![ liste de tâches](images/iphone-list-sml.png)](images/iphone-list.png#lightbox)

Lisez l' [étude de cas Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md).

## <a name="summary"></a>Résumé

Cette section présente les outils de développement d’applications de Xamarin et explique comment créer des applications qui ciblent plusieurs plateformes mobiles.

Il couvre une architecture en couches qui structure le code en vue de sa réutilisation sur plusieurs plateformes, et décrit les différents modèles de logiciels qui peuvent être utilisés dans cette architecture.

Des exemples sont fournis pour les fonctions d’application courantes (comme les opérations de fichiers et de réseau) et la façon dont elles peuvent être créées de manière multiplateforme.

Enfin, il aborde brièvement les tests et fournit des références à une étude de cas qui met ces principes en action.

## <a name="related-links"></a>Liens associés

- [Options de partage de code](~/cross-platform/app-fundamentals/code-sharing.md)
- [Étude de cas : Tasky](~/cross-platform/app-fundamentals/building-cross-platform-applications/case-study-tasky.md)
- [Application d’exemple Tasky (GitHub)](/samples/xamarin/mobile-samples/taskyportable/)
- [Développement d’applications mobiles Xamarin : notions de base des plateformes C# et Xamarin. Forms multiplateforme (Amazon)](https://www.amazon.com/Xamarin-Mobile-Application-Development-Cross-Platform/dp/1484202155/)
- [Développement mobile en C# par Greg-manilles (O’Reilly)](https://shop.oreilly.com/product/0636920024002.do)