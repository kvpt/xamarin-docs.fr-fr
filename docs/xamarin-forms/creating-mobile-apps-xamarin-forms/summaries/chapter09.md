---
title: Résumé du chapitre 9. Appels d’API spécifiques de la plateforme
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 9. Appels d’API spécifiques de la plateforme'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 4FFA1BD4-B3ED-461C-9B00-06ABF70D471D
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 3aec84ec6598a45bb989d4bbc1705fd797382755
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334563"
---
# <a name="summary-of-chapter-9-platform-specific-api-calls"></a>Résumé du chapitre 9. Appels d’API spécifiques de la plateforme

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)

> [!NOTE] 
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Il est parfois nécessaire d’exécuter un code qui varie selon la plate-forme. Ce chapitre explore les techniques.

## <a name="preprocessing-in-the-shared-asset-project"></a>Prétraitement dans le projet d’actifs partagés

Un projet d’actif partagé Xamarin.Forms peut exécuter un code `#if`différent `#elif`pour `endif`chaque plate-forme en utilisant les directives de préprocesseur C ' , , et . C’est ce qui ressort de [**PlatInfoSap1**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap1):

[![Triple capture d’écran du paragraphe formaté variable](images/ch09fg01-small.png "Modèle et système d’exploitation de l’appareil")](images/ch09fg01-large.png#lightbox "Modèle et système d’exploitation de l’appareil")

Cependant, le code résultant peut être laid et difficile à lire.

## <a name="parallel-classes-in-the-shared-asset-project"></a>Classes parallèles dans le projet d’actifs partagés

Une approche plus structurée de l’exécution du code spécifique à la plate-forme dans le SAP est démontrée dans l’échantillon [**PlatInfoSap2.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/PlatInfoSap2) Chacun des projets de plate-forme contient une classe et des méthodes identiques, mais mis en œuvre pour cette plate-forme particulière. Le SAP permet alors simplement d’instantanér la classe et d’appeler la méthode.

## <a name="dependencyservice-and-the-portable-class-library"></a>DependencyService et la bibliothèque de classe portable

> [!NOTE] 
> Les bibliothèques de classe portatives ont été remplacées par des bibliothèques standard .NET. Tout le code de l’échantillon du livre a été converti pour utiliser des bibliothèques standard .NET.

Une bibliothèque ne peut normalement pas accéder aux classes dans les projets d’application. Cette restriction semble empêcher la technique montrée dans **PlatInfoSap2** d’être utilisée dans une bibliothèque. Cependant, Xamarin.Forms contient [`DependencyService`](xref:Xamarin.Forms.DependencyService) une classe nommée qui utilise la réflexion .NET pour accéder aux classes publiques dans le projet d’application de la bibliothèque.

La bibliothèque doit `interface` définir un avec les membres qu’elle doit utiliser dans chaque plate-forme. Ensuite, chacune des plates-formes contient une implémentation de cette interface. La classe qui implémente l’interface doit être identifiée avec une [dépendanceat](xref:Xamarin.Forms.DependencyAttribute) au niveau de l’assemblage.

La bibliothèque utilise [`Get`](xref:Xamarin.Forms.DependencyService.Get*) ensuite `DependencyService` la méthode générique pour obtenir une instance de la classe de plate-forme qui implémente l’interface.

Ceci est démontré dans l’échantillon [**DisplayPlatformInfo.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/DisplayPlatformInfo)

## <a name="platform-specific-sound-generation"></a>Génération sonore spécifique à la plate-forme

[**L’échantillon MonkeyTapWithSound**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09/MonkeyTapWithSound) ajoute des bips au programme **MonkeyTap** en accédant aux installations de génération sonore de chaque plate-forme.

## <a name="related-links"></a>Liens connexes

- [Chapitre 9 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch09-Apr2016.pdf)
- [Échantillons du chapitre 9](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter09)
- [Service de dépendance](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
