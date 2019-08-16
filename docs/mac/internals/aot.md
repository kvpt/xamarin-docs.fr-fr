---
title: Compilation anticipée de Xamarin. Mac
description: Ce document décrit la compilation d’avance dans Xamarin. Mac. Il compare la compilation AOA à la compilation JIT, explique comment activer AOT et examine l’AOA hybride.
ms.prod: xamarin
ms.assetid: 38B8A017-5A58-429C-A6E9-9860A1DCEF63
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 11/10/2017
ms.openlocfilehash: 08bc93e7a17cef35bc992afe0f6fb655a4e69aef
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528912"
---
# <a name="xamarinmac-ahead-of-time-compilation"></a>Compilation anticipée de Xamarin. Mac

## <a name="overview"></a>Présentation

La compilation à l’avance (AOA) est une technique d’optimisation puissante pour améliorer les performances de démarrage. Toutefois, cela affecte également le temps de génération, la taille de l’application et l’exécution du programme de manière profonde. Pour comprendre les compromis qu’il impose, nous allons explorer un peu la compilation et l’exécution d’une application.

Le code écrit dans des langages managés, tel que C# et F#, est compilé en une représentation intermédiaire appelée il. Ce langage intermédiaire, stocké dans vos assemblys de bibliothèque et de programme, est relativement compact et portable entre les architectures de processeur. Toutefois, IL n’est qu’un ensemble d’instructions intermédiaires et, à un moment donné, qu’IL devra être traduit en code machine spécifique au processeur.

Ce traitement peut être effectué de deux points:

- **Juste-à-temps (JIT)** : lors du démarrage et de l’exécution de votre application, le langage intermédiaire est compilé en mémoire en code machine.
- À l' **avance (AOA)** : lors de la génération, le langage intermédiaire est compilé et écrit dans les bibliothèques natives et stocké dans votre offre groupée d’applications.

Chaque option présente un certain nombre d’avantages et de compromis:

- **JIT**
  - **Heure de démarrage** : la compilation JIT doit être effectuée au démarrage. Pour la plupart des applications, il s’agit de l’ordre de 100 $, mais pour les applications volumineuses, cette durée peut être beaucoup plus importante.
  - **Exécution** : comme le code JIT peut être optimisé pour le processeur utilisé, un code légèrement meilleur peut être généré. Dans la plupart des applications, il s’agit de quelques points de pourcentage plus rapides.
- **AOA**
  - **Temps de démarrage** : le chargement des dylibs précompilés est beaucoup plus rapide que les assemblys JIT.
  - **Espace disque** : ces dylibs peuvent prendre une quantité significative d’espace disque toutefois. Selon les assemblys qui sont AOTed, la taille de la partie code de votre application peut être double ou supérieure.
  - **Temps de génération** : la compilation AOA est beaucoup plus lente que JIT et ralentira les builds qui l’utilisent. Ce ralentissement peut aller de quelques secondes à une minute ou plus, en fonction de la taille et du nombre d’assemblys compilés.
  - L' **obscurcissement** : comme il, qui est beaucoup plus facile à rétroconcevoir que le code machine, n’est pas nécessairement obligatoire, il peut être supprimé pour aider à obscurcir le code sensible. Cela nécessite l’option «hybride» décrite ci-dessous.

## <a name="enabling-aot"></a>Activation de l’AOA

Les options AOA seront ajoutées au volet Build Mac dans une prochaine mise à jour. Jusqu’à ce moment, l’activation de l’AOA nécessite le passage d’un argument de ligne de commande via le champ «arguments MMP supplémentaires» dans la build Mac. Les options sont les suivantes :

```
--aot[=VALUE]          Specify assemblies that should be AOT compiled
                          - none - No AOT (default)
                          - all - Every assembly in MonoBundle
                          - core - Xamarin.Mac, System, mscorlib
                          - sdk - Xamarin.Mac.dll and BCL assemblies
                          - |hybrid after option enables hybrid AOT which
                          allows IL stripping but is slower (only valid
                          for 'all')
                          - Individual files can be included for AOT via +
                          FileName.dll and excluded via -FileName.dll

                          Examples:
                            --aot:all,-MyAssembly.dll
                            --aot:core,+MyOtherAssembly.dll,-mscorlib.dll
```


## <a name="hybrid-aot"></a>AOA hybride

Lors de l’exécution d’une application macOS, le runtime utilise par défaut le code machine chargé à partir des bibliothèques natives produites par la compilation AOA. Toutefois, il existe certaines zones de code telles que trampolines, où la compilation JIT peut produire des résultats beaucoup plus optimisés. Cela nécessite que le langage intermédiaire des assemblys managés soit disponible. Sur iOS, les applications sont limitées à toute utilisation de la compilation JIT; ces sections de code sont également compilées par l’AOA.

L’option hybride indique au compilateur de compiler cette section (comme iOS), mais aussi de supposer que le langage intermédiaire n’est pas disponible au moment de l’exécution. Ce langage intermédiaire peut ensuite être supprimé après la génération. Comme indiqué ci-dessus, le runtime sera contraint d’utiliser des routines moins optimisées à certains endroits.

## <a name="further-considerations"></a>Autres considérations

Conséquences négatives de l’échelle de l’AOA avec les tailles et le nombre d’assemblys traités. Le [Framework cible](~/mac/platform/target-framework.md) complet, par exemple, contient une bibliothèque de classes de base (BCL) beaucoup plus volumineuse que la version moderne, et par conséquent, l’AOA prendra beaucoup plus de temps et produira des offres plus volumineuses. Cela est composé de l’incompatibilité de l’infrastructure cible complète avec la liaison, ce qui supprime le code inutilisé. Envisagez de déplacer votre application vers moderne et d’activer la liaison pour obtenir les meilleurs résultats.

L’un des autres avantages offerts par l’AOA est l’amélioration des interactions avec le débogage et le profilage natifs chaînes. Étant donné qu’une grande majorité du code base sera compilée à l’avance, il aura des noms de fonctions et des symboles plus faciles à lire dans les rapports d’incidents natifs, le profilage et le débogage. Les fonctions générées juste-à-temps n’ont pas ces noms et apparaissent souvent comme des décalages hexadécimaux sans nom qui sont très difficiles à résoudre.
