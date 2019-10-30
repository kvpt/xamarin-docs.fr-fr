---
title: Bureau d’enregistrement Xamarin. Mac
description: Ce document décrit l’objectif de l’registraire Xamarin. Mac et ses configurations d’utilisation statiques, statiques et statiques (hybrides).
ms.prod: xamarin
ms.assetid: 7CAAA6B7-D654-4AD3-BAEC-9DD01210978A
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 11/10/2017
ms.openlocfilehash: 991d9b2d911b5aa4ac07225fd1df34877451df49
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017320"
---
# <a name="xamarinmac-registrar"></a>Bureau d’enregistrement Xamarin. Mac

_Ce document décrit l’objectif du Bureau d’enregistrement Xamarin. Mac et ses différentes configurations d’utilisation._

## <a name="overview"></a>Vue d'ensemble

Xamarin. Mac permet de combler l’écart entre le runtime du monde géré (.NET) et le runtime de cacao, ce qui permet aux classes managées d’appeler des classes objective-C non managées et d’être rappelées lorsque des événements se produisent. Le travail requis pour préformer ce « Magic » est géré par le Bureau d’enregistrement et est, en général, masqué de l’affichage.

Cette inscription a des répercussions sur les performances, en particulier sur le temps de démarrage de l’application, et la compréhension d’un peu de ce qui se passe « en coulisse » peut parfois être utile.

## <a name="configurations"></a>Configurations

Fondamentalement, la tâche du Bureau d’enregistrement au démarrage peut être divisée en deux catégories :

- Analysez chaque classe managée pour ceux qui dérivent de NSObject et collectez une liste d’éléments à exposer au runtime objective-C.
- Enregistrez ces informations avec le runtime objective-C.

Au fil du temps, trois configurations de bureaux d’enregistrement différentes ont été créées pour couvrir différents cas d’utilisation. Chacun a des conséquences différentes au moment de la génération et de l’exécution :

- **Registraire dynamique** : au démarrage, utilisez la réflexion .net pour analyser chaque type chargé, déterminer la liste des éléments pertinents et informer le runtime natif. Cette option ajoute zéro heure à la build, mais elle est très coûteuse à calculer au cours du lancement (jusqu’à plusieurs secondes).
- **Registraire statique** : lors de la génération, calculez l’ensemble des éléments à inscrire et générez du code Objective-C pour gérer l’inscription. Ce code est appelé au démarrage pour inscrire rapidement tous les éléments. Ajoute une pause significative à la build, mais peut réduire le temps de démarrage de l’application.
- **Statique « partielle »** : approche « hybride » plus récente qui offre la plupart des avantages des deux. Étant donné que les exportations de **Xamarin. Mac. dll** sont constantes, enregistrez une bibliothèque précalculée pour gérer son inscription et liez-la dans. Utilisez la réflexion pour gérer les bibliothèques utilisateur, mais comme les bibliothèques utilisateur exportent beaucoup moins de types que les liaisons de la plateforme sont souvent assez rapides. Un impact négligeable sur la durée de la génération et réduit une grande majorité du « coût » de Dynamic.

Aujourd’hui, la valeur statique partielle est la valeur par défaut pour la configuration de débogage et statique est la valeur par défaut pour les configurations Release.

Voici quelques scénarios :

- Plug-ins chargés après le lancement avec des classes dérivant de NSObject
- Instances de classes créées dynamiquement dérivant de NSObject

Lorsque le Bureau d’enregistrement n’est pas en mesure de savoir qu’il doit inscrire un type au démarrage. La méthode `ObjCRuntime.Runtime.RegisterAssembly` est fournie pour informer le Bureau d’enregistrement qu’il a des types supplémentaires à prendre en compte.
