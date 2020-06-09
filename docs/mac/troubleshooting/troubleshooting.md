---
title: Conseils de dépannage de Xamarin. Mac
description: Ce document décrit les approches permettant de résoudre les problèmes rencontrés lors du développement d’applications Xamarin. Mac. Il aborde également les façons d’accéder au support.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5CBC6822-BCD7-4DAD-8468-6511250D41C4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 683915d238f6c8aee10957285ad4438316e1e037
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84567117"
---
# <a name="xamarinmac-troubleshooting-tips"></a>Conseils de dépannage de Xamarin. Mac

## <a name="overview"></a>Vue d’ensemble

Parfois, nous sommes tous bloqués lors de l’utilisation d’un projet, soit sur l’impossibilité d’obtenir une API pour travailler comme je le souhaite, soit pour tenter de contourner un bogue. Notre objectif de Xamarin est de vous permettre de réussir à écrire vos applications mobiles et de bureau, et nous avons fourni des ressources pour vous aider.

Avec l’une de ces ressources, vous pouvez suivre certaines étapes de préparation pour les aider à résoudre rapidement votre problème :

- Déterminez la cause première du problème le mieux possible pour signaler des incidents :

  - « Mon application se bloque » est difficile à diagnostiquer. « Mon application se bloque quand je retourne un tableau vide à cet appel » est beaucoup plus facile à résoudre.

  - « Je ne peux pas faire fonctionner NSTable » est moins utile que « aucune des méthodes sur mon NSTableDelegate ne semble être appelée dans ce cas ».

- Si possible, fournissez un petit exemple de programme qui illustre le problème. L’examen des pages du code source à la recherche du problème prend plus de temps et de travail.

- Le fait de connaître les modifications que vous avez apportées à votre application pour faire apparaître un problème peut rapidement limiter la source du problème. Notez si vous avez récemment mis à niveau des versions de Xamarin. Mac, si vous avez tronqué les sections de votre application pour trouver la partie à l’origine du problème, ou si vous testez les builds précédentes pour trouver la modification introduite par le problème, il peut être très utile.

### <a name="what-to-do-when-your-app-crashes-with-no-output"></a>Que faire lorsque votre application se bloque sans sortie

Dans la plupart des cas, le débogueur de Visual Studio pour Mac intercepte les exceptions et les incidents dans votre application et vous aide à identifier la cause racine. Toutefois, dans certains cas, votre application rebondit sur le Dock, puis s’arrête avec peu ou pas de sortie. Ces stratégies peuvent inclure :

- Problèmes de signature de code.
- Certaines défaillances de l’exécution mono.
- Certaines exceptions objective-c et se bloquent.
- Certains se bloquent très tôt dans la durée de vie du processus.
- Un dépassement de capacité de la pile.
- La version macOS indiquée dans votre fichier **info. plist** est plus récente que la version MacOS actuellement installée ou n’est pas valide.

Le débogage de ces programmes peut être frustrant, car la recherche des informations nécessaires peut être difficile. Voici quelques approches qui peuvent vous aider :

- Assurez-vous que la version macOS indiquée dans le fichier **info. plist** est la même que la version de MacOS actuellement installée sur l’ordinateur.
- Vérifiez la sortie de l’application Visual Studio pour Mac (**Afficher**les résultats de l'  ->  **Pads**  ->  **application**PAD) pour les traces de la pile ou la sortie en rouge à partir du cacao qui peut décrire la sortie.
- Exécutez votre application à partir de la ligne de commande et examinez la sortie (dans l’application **Terminal** ) à l’aide de :

  `MyApp.app/Contents/MacOS/MyApp`(où `MyApp` est le nom de votre application)
- Vous pouvez augmenter la sortie en ajoutant « MONO_LOG_LEVEL » à votre commande sur la ligne de commande, par exemple :

  `MONO_LOG_LEVEL=debug MyApp.app/Contents/MacOS/MyApp`
- Vous pouvez attacher un débogueur natif ( `lldb` ) à votre processus pour voir s’il fournit des informations supplémentaires (cela nécessite une licence payante). Par exemple, procédez comme suit :

  1. Entrez `lldb MyApp.app/Contents/MacOS/MyApp` dans le terminal.
  2. Entrez `run` dans le terminal.
  3. Entrez `c` dans le terminal.
  4. Quittez lorsque vous avez terminé le débogage.
- En dernier recours, avant d’appeler `NSApplication.Init` dans votre `Main` méthode (ou dans d’autres emplacements, le cas échéant), vous pouvez écrire du texte dans un fichier à un emplacement connu pour effectuer le suivi de l’étape de lancement que vous rencontrez.

## <a name="known-issues"></a>Problèmes connus

Les sections suivantes couvrent les problèmes connus et leurs solutions.

### <a name="unable-to-connect-to-the-debugger-in-sandboxed-apps"></a>Impossible de se connecter au débogueur dans les applications bac à sable (sandbox)

Le débogueur se connecte aux applications Xamarin. Mac via TCP, ce qui signifie que par défaut, lorsque vous activez le sandboxing, il ne peut pas se connecter à l’application. par conséquent, si vous essayez d’exécuter l’application sans que les autorisations appropriées soient activées, vous recevez une erreur *« Impossible de se connecter au débogueur »*.

[![Modification des droits](troubleshooting-images/debug01.png "Modification des droits")](troubleshooting-images/debug01-large.png#lightbox)

L’autorisation **autoriser les connexions réseau sortantes (client)** est celle qui est requise pour le débogueur, l’activation de celle-ci permet le débogage normal. Étant donné que vous ne pouvez pas effectuer de débogage sans cela, nous avons mis à jour la `CompileEntitlements` cible pour pour `msbuild` Ajouter automatiquement cette autorisation aux droits pour toute application qui est bac à sable (sandbox) pour les versions de débogage uniquement. Les versions release doivent utiliser les droits spécifiés dans le fichier de droits, non modifié.

### <a name="systemnotsupportedexception-no-data-is-available-for-encoding-437"></a>System. NotSupportedException : aucune donnée n’est disponible pour l’encodage 437

Lorsque vous incluez des bibliothèques tierces dans votre application Xamarin. Mac, vous pouvez obtenir une erreur au format « System. NotSupportedException : aucune donnée n’est disponible pour l’encodage 437 » lors de la tentative de compilation et d’exécution de l’application. Par exemple, les bibliothèques, telles que `Ionic.Zip.ZipFile` , peuvent lever cette exception pendant l’opération.

Cela peut être résolu en ouvrant les options pour le projet Xamarin. Mac, en accédant à **Mac Build**  >  l'**internationalisation** de la build Mac et en vérifiant l’internationalisation de l' **Ouest** :

[![Modification des options de build](troubleshooting-images/issue01.png "Modification des options de build")](troubleshooting-images/issue01-large.png#lightbox)

### <a name="failed-to-compile-mm5103"></a>Échec de la compilation (mm5103)

Cette erreur se produit généralement lorsqu’une nouvelle version de Xcode est publiée et que vous avez installé la nouvelle version mais que vous ne l’avez pas encore exécutée. Avant d’essayer de compiler avec une nouvelle version de Xcode, vous devez d’abord exécuter cette version au moins une fois.

La première fois que vous exécutez une nouvelle version de Xcode, elle installe plusieurs outils en ligne de commande qui sont requis par Xamarin. Mac. En outre, vous devez effectuer une nouvelle build après avoir mis à jour Xcode ou votre version de Xamarin. Mac.

Si vous ne parvenez pas à résoudre ce problème, signalez [un bogue](#filing-a-bug).

### <a name="missing-entitlementsplist"></a>Droits manquants. plist

La dernière version de Visual Studio pour Mac a supprimé la section des droits de l’éditeur **info. plist** et l’a placée dans un éditeur de **droits. plist** distinct (pour une meilleure prise en charge multiplateforme avec Xamarin. IOS).

Une fois le nouveau Visual Studio pour Mac installé, lorsque vous créez un projet d’application Xamarin. Mac, un fichier **Entitlements. plist** est automatiquement ajouté à l’arborescence du projet :

![Sélection de droits](troubleshooting-images/entitlements01.png "Sélection de droits")

Si vous double-cliquez sur le fichier **habilitations. plist** , l’éditeur de droits s’affiche :

[![Modification des droits](troubleshooting-images/entitlements02.png "Modification des droits")](troubleshooting-images/entitlements02-large.png#lightbox)

Pour les projets Xamarin. Mac existants, vous devez créer manuellement le fichier **Entitlements. plist** en cliquant avec le bouton droit sur le projet dans le **panneau solutions** puis en sélectionnant **Ajouter**  >  **un nouveau fichier...**. Ensuite, sélectionnez **Xamarin. Mac**,  >  **liste de propriétés vide**:

![Ajout d’une nouvelle liste de propriétés](troubleshooting-images/entitlements03.png "Ajout d’une nouvelle liste de propriétés")

Entrez `Entitlements` pour le nom et cliquez sur le bouton **nouveau** . Si votre projet comprenait précédemment un fichier de droits, vous serez invité à l’ajouter au projet au lieu de créer un nouveau fichier :

[![Vérification du remplacement d’un fichier](troubleshooting-images/entitlements04.png "Vérification du remplacement d’un fichier")](troubleshooting-images/entitlements04-large.png#lightbox)

## <a name="community-support-on-the-forums"></a>Support de la communauté sur les forums

La communauté de développeurs utilisant des produits Xamarin est incroyable et de nombreux visitent nos [Forums Xamarin. Mac](https://forums.xamarin.com/categories/xamarin-mac) pour partager des expériences et leur expertise. En outre, les ingénieurs Xamarin consultent régulièrement le Forum pour vous aider.

<a name="filing-a-bug"></a>

## <a name="filing-a-bug"></a>Enregistrement d’un bogue

Vos commentaires sont très importants pour nous. Si vous rencontrez des problèmes avec Xamarin. Mac :

- Recherchez dans le [dépôt des problèmes](https://github.com/xamarin/xamarin-macios/issues)
- Avant de devenir des problèmes GitHub, les problèmes Xamarin étaient suivis sur [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Recherchez-y des problèmes correspondants.
- Si vous ne trouvez pas de problème correspondant, ouvrez un nouveau problème dans le [dépôt de problèmes GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Les problèmes GitHub sont tous publics. Il n’est pas possible de masquer des commentaires ou des pièces jointes.

Dans la mesure du possible, essayez d’inclure :

- Un exemple simple reproduisant le problème. Si vous pouvez en fournir un, c’est **inestimable**.
- La trace de pile complète du plantage.
- Le code C# se rapportant au plantage.
