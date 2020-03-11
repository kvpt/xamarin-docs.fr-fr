---
title: Résumé du chapitre 9. Appels d’API spécifiques à la plateforme
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 9. Appels d’API spécifiques à la plateforme'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "61334563"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Résumé du chapitre 9. Appels d’API spécifiques à la plateforme

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Il est parfois nécessaire d’exécuter du code qui varie par plateforme. Ce chapitre explore les techniques.

## <a name="preprocessing-in-the-shared-asset-project"></a>Prétraitement dans le projet actif partagé

Un projet de ressource partagée Xamarin. Forms peut exécuter un code différent pour chaque C# plateforme à l’aide des directives de préprocesseur `#if`, `#elif`et `endif`. Cela est illustré dans [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Capture d’écran triple du paragraphe mis en forme variable](images/ch09fg01-small.png "Modèle d’appareil et système d’exploitation")](images/ch09fg01-large.png#lightbox "Modèle d’appareil et système d’exploitation")

Toutefois, le code résultant peut être horrible et difficile à lire.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes parallèles dans le projet de ressources partagées

Une approche plus structurée de l’exécution de code spécifique à la plateforme dans SAP est présentée dans l’exemple [**PlatInfoSap2**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) . Chacun des projets de plateforme contient une classe portant le même nommée et des méthodes, mais implémentée pour cette plate-forme particulière. SAP ensuite simplement instancie la classe et appelle la méthode.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService et la bibliothèque de classes Portable

> [!NOTE] 
> Bibliothèques de classes portables ont été remplacés par des bibliothèques .NET Standard. Exemples de code à partir de l’ouvrage a été converti pour utiliser les bibliothèques .NET standard.

Une bibliothèque ne peut pas accéder normalement les classes dans les projets d’application. Cette restriction semble empêcher l’utilisation de la technique indiquée dans **PlatInfoSap2** dans une bibliothèque. Toutefois, Xamarin. Forms contient une classe nommée [`DependencyService`](xref:Xamarin.Forms.DependencyService) qui utilise la réflexion .net pour accéder aux classes publiques dans le projet d’application à partir de la bibliothèque.

La bibliothèque doit définir une `interface` avec les membres qu’elle doit utiliser dans chaque plateforme. Ensuite, chacune des plateformes contient une implémentation de cette interface. La classe qui implémente l’interface doit être identifiée par un [DependencyAttribute](xref:Xamarin.Forms.DependencyAttribute) au niveau de l’assembly.

La bibliothèque utilise ensuite la méthode générique [`Get`](xref:Xamarin.Forms.DependencyService.Get*) de `DependencyService` pour obtenir une instance de la classe Platform qui implémente l’interface.

Cela est illustré dans l’exemple [**DisplayPlatformInfo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo) .

## <a name="platform-specific-sound-generation"></a>Génération de sons spécifiques à la plateforme

L’exemple [**MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) ajoute des signaux sonores au programme **MonkeyTap** en accédant à des fonctionnalités de génération de sons sur chaque plateforme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 9 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Exemples du chapitre 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Service de dépendance](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
