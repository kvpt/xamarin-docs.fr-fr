---
title: Options de l’éditeur de liens Xamarin.Mac
description: Ce document décrit la liaison dans Xamarin.Mac. La liaison est un puissant outil d’optimisation qui réduit la taille de votre application en supprimant le code inutilisé.
ms.prod: xamarin
ms.assetid: F03176C3-F8D4-4DE8-870C-7F27D8CE525A
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: dd42de4dda4b5d5b09156898fd7a8bab331adf88
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865904"
---
# <a name="xamarinmac-linker-options"></a>Options de l’éditeur de liens Xamarin.Mac

_La liaison est un puissant outil d’optimisation qui réduit la taille de votre application en supprimant le code inutilisé._

## <a name="overview"></a>Vue d'ensemble

Selon le [framework cible](~/mac/platform/target-framework.md) qu’utilise votre projet, les options de l’éditeur de liens disponibles peuvent être limitées. En effet, la liaison nécessite la création d’un graphique d’objet de chaque type utilisé par votre application, ce qui est impossible avec Complet (ou Non pris en charge) en raison de System.Configuration.

Quatre options sont disponibles :

- **Aucune** : désactive toutes les liaisons. Option par défaut dans la configuration Debug de Moderne et dans toutes les configurations de Complet.
- **Kit de développement logiciel (SDK)** : lie tous les assemblys du SDK, à l’exclusion des assemblys utilisateur. Option par défaut dans la configuration Release de Moderne. Non disponible avec Complet.
- **Complet** : lie tous les assemblys. Cette option exige que le code utilisateur soit sécurisé pour l’éditeur de liens. Consultez les [remarques](~/ios/deploy-test/linker.md) pour plus d’informations. Non disponible avec Complet.
- **Plateforme** : lie uniquement Xamarin.Mac.dll. Pour plus d'informations, consultez ce qui suit.

## <a name="platform-linking"></a>Liaison de plateforme

La liaison d’applications à l’aide du [framework cible](~/mac/platform/target-framework.md) Complet n’est généralement pas sécurisée, mais il existe certains cas où une forme très limitée de liaison est nécessaire.

Par exemple, les applications soumises à l’App Store macOS ne doivent pas référencer certaines API déconseillées (par exemple, QTKit), dont Xamarin.Mac contient des liaisons pour plusieurs d’entre elles. Même si une application n’appelle pas ces liaisons, l’appel existera dans le SDK et sera rejeté.

La liaison de plateforme part du principe que l’application et la bibliothèque de classes de base ne sont pas sécurisées pour l’éditeur de liens, et supprime simplement le code inutilisé de Xamarin.Mac.dll. 

Toutes les applications sans réflexion sur les types de Xamarin.Mac.dll verront leur démarrage légèrement amélioré après la suppression des types inutiles.

La liaison de plateforme est généralement utile uniquement pour les applications utilisant le framework cible Complet, étant donné que l’application Moderne peut utiliser l’option Kit de développement logiciel (SDK) plus puissante.

## <a name="setting-the-linker-configuration"></a>Configuration de l’éditeur de liens

Pour modifier la configuration de l’éditeur de liens pour un projet Xamarin.Mac, procédez comme suit :

1. Ouvrez le projet Xamarin.Mac dans Visual Studio pour Mac.
2. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier projet pour ouvrir la boîte de dialogue **Options du projet**.
3. Dans l’onglet **Build Mac**, sélectionnez le type de **comportement de l’éditeur de liens** adapté aux besoins de votre application :

    ![Sélection du comportement de l’éditeur de liens à utiliser](linker-images/link-behavior.png "Sélection du comportement de l’éditeur de liens à utiliser")

4. La liaison de plateforme pour les frameworks cibles Complet sera intégrée dans l’IDE dans une prochaine mise à jour. En attendant, ajoutez `--linkplatform` aux **arguments mmp supplémentaires** à la place.
5. Cliquez sur le bouton **OK** pour enregistrer vos changements.


## <a name="related-links"></a>Liens associés

- [Liaison sur iOS](~/ios/deploy-test/linker.md)
