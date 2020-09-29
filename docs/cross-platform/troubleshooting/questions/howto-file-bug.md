---
title: Quand et comment dois-je classer un rapport de bogues ?
description: Ce document décrit quand, où et comment documenter un rapport de bogue. Il fournit également des recommandations en matière de rapports de bogues qui permettent aux ingénieurs de mieux diagnostiquer le problème.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 8AD9CFBF-282A-4C1F-95E9-25F21141B052
author: davidortinau
ms.author: daortin
ms.date: 08/01/2018
ms.openlocfilehash: 38a96f5f499b760aedcfc51dc9e6326c9c62e95b
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457872"
---
# <a name="when-and-how-should-i-file-a-bug-report"></a>Quand et comment dois-je classer un rapport de bogues ?

> [!TIP]
> Utilisez l’élément de menu **signaler un problème** dans Visual Studio &ndash; pour envoyer des informations de diagnostic et votre rapport de bogues pour aider à résoudre le problème.
>
> Vous trouverez des instructions détaillées pour [Visual studio 2019 ou Visual studio 2017](/visualstudio/ide/how-to-report-a-problem-with-visual-studio) et [Visual Studio pour Mac](/visualstudio/mac/report-a-problem).
>
> Vous pouvez rechercher des rapports existants sur le site Web de la [communauté de développeurs Visual Studio](https://developercommunity.visualstudio.com/) .

## <a name="file-a-bug-if"></a>Signaler un bogue si...

Vous avez un ensemble d’étapes que vous pensez que les ingénieurs pourront utiliser pour reproduire un problème.

OR

Vous pouvez décrire avec soin les symptômes visibles du problème, en particulier si vous pouvez également décrire des circonstances précises liées au problème. <sup> [[1]](#note-1)</sup>

## <a name="best-practices-to-help-address-bugs-quickly-and-efficiently"></a>Meilleures pratiques pour aider à résoudre les bogues rapidement et efficacement

1. <a name="ref-1"></a>Recherchez dans la [communauté des développeurs Visual Studio](https://developercommunity.visualstudio.com/) et le Web des rapports de bogues ou des suggestions d’utilisation existants qui peuvent résoudre le problème directement. <sup>[[2]](#note-2)</sup><sup>[[3]](#note-3)</sup>

1. <a name="ref-2"></a>Décrivez le problème aussi clairement que possible, y compris une description de ce qui s’est produit et devrait se produire.

1. <a name="ref-3"></a>Inclure les traces de pile, le texte du message d’erreur ou les journaux d’incidents appropriés (si vous utilisez la fonctionnalité **signaler un problème** , celles-ci peuvent être incluses automatiquement). <sup>[4](#note-4)</sup>

1. <a name="ref-4"></a>Notez les messages d’erreur importants qui s’affichent également dans les pièces jointes de capture d’écran en tant que texte brut.

1. <a name="ref-5"></a>Incluez un petit cas de test autonome qui reproduit le bogue avec le moins de code possible.  Si vous ne pouvez pas reproduire le problème avec un nouveau projet (créé à l’aide d’un des modèles intégrés), vous devez alors créer un projet qui démontre le problème et l’attacher au rapport de bogue.  Rendez l’exemple de projet aussi simple que possible avant de l’attacher. <sup>[[5]](#note-5)</sup><sup>[[6]](#note-6)</sup>

1. <a name="ref-6"></a>Décrivez l’environnement dans lequel le bogue a été rencontré, y compris le système d’exploitation et les [versions de Xamarin](~/cross-platform/troubleshooting/questions/version-logs.md) et toutes les dépendances.

## <a name="additional-details"></a>Informations supplémentaires

1. <a name="note-1"></a>[*^*](#ref-1) Dans l’idéal, la description des symptômes visibles doit inclure suffisamment de détails afin que d’autres clients puissent confirmer s’ils voient le même problème (mêmes messages d’erreur, même dégradation des performances, même trace de la pile d’un incident, _etc._). Pour les « circonstances précises », un bon exemple serait si vous pouvez obtenir quelque chose comme : « je rencontre normalement le problème 75% du temps, mais si je change cette chose, je peux éviter complètement le problème ». Un autre exemple similaire de « circonstance précise » est si la rétrogradation vers une version antérieure de Xamarin arrête le problème.

1. <a name="note-2"></a>[*^*](#ref-2) Comme vous pouvez vous y attendre, les extraits de texte d’erreur (ou tout autre texte descriptif unique) sont généralement les meilleurs termes de recherche. Si le rapport de bogue existant est incomplet, vous êtes invité à ajouter des détails ou à créer un nouveau rapport de bogue.

1. <a name="note-3"></a>[*^*](#ref-3) Une autre question intéressante est de savoir si le même problème a été signalé pour des applications Java, Objective-C ou SWIFT. Dans ce cas, le problème est probablement lié à Android ou iOS lui-même plutôt qu’à une partie de Xamarin.

1. <a name="note-4"></a>[*^*](#ref-4) Voici quelques exemples d’informations à inclure :

    1. Pour les erreurs qui se produisent lors de la génération d’un projet, incluez la sortie de la [génération de diagnostic](~/android/troubleshooting/troubleshooting.md#Diagnostic_MSBuild_Output) complète sur le rapport de bogue.

    1. Pour les erreurs qui se produisent lors de la génération ou du débogage d’un projet iOS à partir de Visual Studio, exécutez l' **aide > Xamarin > les journaux zip** après avoir atteint l’erreur et incluez le fichier. zip résultant dans le rapport de bogue.

    1. Pour les exceptions ou les blocages dans les applications Android ou iOS, veuillez inclure les [journaux de débogage appropriés pour les applications Xamarin. Android et Xamarin. iOS](~/cross-platform/troubleshooting/questions/version-logs.md#debug-logs-for-xamarin-apps).

1. <a name="note-5"></a>[*^*](#ref-5) Si cela est possible pour votre problème particulier, une option consiste à recréer le problème en ajoutant un petit nombre de fichiers de votre solution d’origine dans une toute nouvelle solution. L’équipe Xamarin sera souvent en mesure d’examiner les problèmes même dans des cas de test plus importants (en supposant que les étapes à reproduire sont clairement expliquées), mais les cas de test plus simples offrent la meilleure chance que le bogue soit résolu rapidement.

1. <a name="note-6"></a>[*^*](#ref-6) S’il n’est _pas_ possible de reproduire le problème en ajoutant un petit nombre de fichiers à une nouvelle solution, vous pouvez compresser et attacher l’intégralité du dossier de solution pour votre application complète. Supprimez les `bin` `obj` dossiers,, `Components` et `packages` pour réduire la taille du fichier zip. (L’IDE et le processus de génération restaurent généralement ou recréent le contenu de ces dossiers si nécessaire.) Vous pouvez également supprimer autant de fichiers de code et de ressources que vous le souhaitez à partir du projet, à condition que la solution qui en résulte illustre toujours le problème d’origine.