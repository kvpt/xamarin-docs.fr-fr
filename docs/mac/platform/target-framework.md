---
title: Framework cible pour Xamarin.Mac
description: Cet article couvre les frameworks cibles (bibliothèques de classes de Base) disponibles pour Xamarin.Mac, ainsi que les implications de leur utilisation dans votre projet Xamarin.Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: e9e20b4966e9e6cb8a4ce3ad6724cf0ba2565c33
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865863"
---
# <a name="target-framework-for-xamarinmac"></a>Framework cible pour Xamarin.Mac

_Cet article couvre les frameworks cibles (bibliothèques de classes de Base) disponibles pour Xamarin.Mac, ainsi que les implications de leur utilisation dans votre projet Xamarin.Mac._

![Cibler des options de framework pour Xamarin.Mac](target-framework-images/select-target.png "Target framework des options pour Xamarin.Mac")

## <a name="background"></a>Présentation

Chaque programme .NET ou la bibliothèque dépend des fonctionnalités fournies par la bibliothèque de classes de Base (BCL). Cette BCL inclut des assemblys qui fournissent des fonctionnalités communes intégrée à tous les langages .NET tels que mscorlib, System, System.Net.Http et System.Xml.

Au fil des années, vous avez développé plusieurs versions différentes de cette bibliothèque BCL, optimisés pour différents cas d’usage. La BCL « bureau » inclut un ensemble plus riche de bibliothèques qui peuvent être trop lourdes pour les autres cas d’utilisation, bien que mobile se concentre sur le maintien des Qu'api sont sécurisés pour la liaison, ce qui supprime le code inutilisé pour réduire l’encombrement de l’application.

Une des répercussions qu’entraînerait plus importante de ces différentes infrastructures cibles, est que tous les assemblys dans un programme donné *doit* cibler des assemblys BCL compatibles. Si ce n’est pas le cas, vous pourriez avoir deux assemblys liés avec différentes versions de la **System.dll** désaccord sur la signature d’un type donné. Une bibliothèque partagée peut soit cible [.NET Standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), qui est le sous-ensemble commun des Frameworks cibles, ou un framework cible spécifique.

Il existe trois options de Framework cible pour Xamarin.Mac, chacune avec différents avantages et les compromis :

- **Modernes** (appelé Mobile dans la documentation plus ancienne) – un sous-ensemble très similaire et les puissances Xamarin.iOS, hautement optimisés pour les performances et la taille. Cette infrastructure cible est l’éditeur de liens sécurisé, ces projets peuvent avoir leur empreinte finale considérablement réduit en supprimant le code inutilisé.

- **Complète** (appelé XM 4.5 dans la documentation plus ancienne) – un sous-ensemble très similaire à la BCL « bureau », avec quelques suppressions de petites. Comme le Framework cible est presque identique à net45 (et versions ultérieures), il peut facilement consommer nombreux packages NuGet qui ne fournit pas soit netstandard2 ou Xamarin.Mac spécifique des builds. Toutefois, en raison de l’utilisation de System.Configuration, il est incompatible avec la liaison.

- **Non pris en charge** (appelé système dans la documentation plus ancienne) – à la place de la liaison à une BCL fournie par Xamarin.Mac, utiliser mono système installé actuel. Cela fournit l’ensemble plus complète des assemblys, notamment certaines problématiques (System.Drawing par exemple). Cette option existe uniquement a « dernier recours » et il est fortement recommandé d’épuiser les autres options avant de l’utiliser. Comme son nom l’indique, l’utilisation est pris en charge par les canaux de prise en charge officielle.

## <a name="setting-the-target-framework"></a>Définition du framework cible

Pour modifier le type de Framework cible pour un projet Xamarin.Mac, procédez comme suit :

1. Ouvrez le projet Xamarin.Mac dans Visual Studio pour Mac.
2. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier projet pour ouvrir la boîte de dialogue **Options du projet**.
3. À partir de la **général** , sélectionnez le type de **Framework cible** adapté aux besoins de votre application :

    [![À l’aide de la fenêtre Options de projet pour choisir un framework cible](target-framework-images/select-target-full.png "à l’aide de la fenêtre Options de projet pour choisir un framework cible")](target-framework-images/select-target-full-large.png#lightbox)

4. Cliquez sur le bouton **OK** pour enregistrer vos changements.

Vous devez **Clean** , puis **reconstruire** votre projet Xamarin.Mac après avoir basculé le type de Framework cible.

## <a name="summary"></a>Récapitulatif

Cet article a présenté brièvement les différents types de Frameworks cibles (bibliothèques de classes de Base) disponible pour une application Xamarin.Mac et quand utiliser chaque type de framework.


## <a name="related-links"></a>Liens associés

- [iOS et le partage de code Mac](~/cross-platform/macios/index.md)
- [API unifiée](~/cross-platform/macios/unified/index.md)
- [Bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md)
- [Assemblys](~/cross-platform/internals/available-assemblies.md)
- [La mise à jour des applications Mac existantes](~/cross-platform/macios/unified/updating-mac-apps.md)
