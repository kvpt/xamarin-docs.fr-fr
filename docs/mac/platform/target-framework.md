---
title: Framework cible pour Xamarin. Mac
description: Cet article traite des frameworks cibles (bibliothèques de classes de base) disponibles pour Xamarin. Mac, ainsi que des implications de leur utilisation dans votre projet Xamarin. Mac.
ms.prod: xamarin
ms.assetid: AF21BE16-3F92-4121-AB4C-D51AC863D92D
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 11/10/2017
ms.openlocfilehash: 4ae8834427580c387de7a38a69d711207b04821e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290885"
---
# <a name="target-framework-for-xamarinmac"></a>Framework cible pour Xamarin. Mac

_Cet article traite des frameworks cibles (bibliothèques de classes de base) disponibles pour Xamarin. Mac, ainsi que des implications de leur utilisation dans votre projet Xamarin. Mac._

![Options du Framework cible pour Xamarin. Mac](target-framework-images/select-target.png "Options du Framework cible pour Xamarin. Mac")

## <a name="background"></a>Présentation

Chaque programme ou bibliothèque .NET dépend de la fonctionnalité fournie par la bibliothèque de classes de base (BCL). Ce BCL comprend des assemblys tels que mscorlib, System, System .net. http et System. XML qui fournissent les fonctionnalités communes intégrées à tous les langages .NET.

Au fil des années, il a développé plusieurs versions différentes de ce BCL, optimisées pour différents cas d’utilisation. La BCL « Desktop » comprend un ensemble de bibliothèques plus riche qui peut être trop lourd pour d’autres cas d’usage, tandis que mobile se focalise sur la garantie d’une liaison sécurisée des API, ce qui supprime le code inutilisé pour réduire l’encombrement des applications.

L’une des répercussions les plus importantes de ces différents frameworks cibles est que tous les assemblys d’un programme donné *doivent* cibler des assemblys BCL compatibles. Si ce n’est pas le cas, vous pouvez avoir deux assemblys liés par les différentes versions de **System. dll** qui sont en désaccord avec la signature d’un type donné. Une bibliothèque partagée peut cibler [.NET standard 2](https://blog.xamarin.com/share-code-net-standard-2-0/), qui est le sous-ensemble commun des frameworks cibles, ou un Framework cible spécifique.

Trois options de Framework cible sont disponibles pour Xamarin. Mac, chacune présentant des avantages et des inconvénients différents :

- **Moderne** (appelée mobile dans une documentation plus ancienne) : un sous-ensemble similaire à ce qui alimente Xamarin. iOS, hautement optimisé pour les performances et la taille. Ce Framework cible étant sécurisé pour l’éditeur de liens, l’encombrement final de ces projets peut être considérablement réduit en supprimant le code inutilisé.

- **Plein** (appelé XM 4,5 dans une documentation plus ancienne) : sous-ensemble similaire à la BCL « Desktop », avec quelques suppressions minimes. Étant donné que la version cible de .NET Framework est presque identique à Net45 (et versions ultérieures), elle peut facilement consommer de nombreux packages NuGet qui ne fournissent pas de versions netstandard2 ou Xamarin spécifiques. Toutefois, en raison de l’utilisation de System. Configuration, il est incompatible avec la liaison.

- **Non pris en charge** (appelé système dans une documentation plus ancienne) : au lieu de lier à une BCL fournie par Xamarin. Mac, utilisez le système actuel installé mono. Cela fournit le jeu d’assemblys le plus complet, y compris certains problèmes connus (System. Drawing, par exemple). Cette option existe uniquement en dernier recours et il est fortement recommandé d’épuiser d’autres options avant de l’utiliser. Comme son nom l’indique, l’utilisation n’est pas prise en charge par les canaux de support officiels.

## <a name="setting-the-target-framework"></a>Définition de la version cible du .NET Framework

Pour passer au type de Framework cible pour un projet Xamarin. Mac, procédez comme suit :

1. Ouvrez le projet Xamarin.Mac dans Visual Studio pour Mac.
2. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier projet pour ouvrir la boîte de dialogue **Options du projet**.
3. Dans l’onglet **général** , sélectionnez le type de **Framework cible** qui répond aux besoins de votre application :

    [![Utilisation de la fenêtre Options de projet pour choisir une version cible de .NET Framework](target-framework-images/select-target-full.png "Utilisation de la fenêtre Options de projet pour choisir une version cible de .NET Framework")](target-framework-images/select-target-full-large.png#lightbox)

4. Cliquez sur le bouton **OK** pour enregistrer vos changements.

Vous devez **nettoyer** puis **régénérer** votre projet Xamarin. Mac après avoir basculé le type de Framework cible.

## <a name="summary"></a>Récapitulatif

Cet article a brièvement abordé les différents types de frameworks cibles (bibliothèques de classes de base) disponibles pour une application Xamarin. Mac et le moment où chaque type d’infrastructure doit être utilisé.


## <a name="related-links"></a>Liens associés

- [partage de code iOS et Mac](~/cross-platform/macios/index.md)
- [API unifiée](~/cross-platform/macios/unified/index.md)
- [Bibliothèques de classes portables](~/cross-platform/app-fundamentals/pcl.md)
- [Assemblys](~/cross-platform/internals/available-assemblies.md)
- [Mise à jour des applications Mac existantes](~/cross-platform/macios/unified/updating-mac-apps.md)
