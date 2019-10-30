---
title: Génération de code. XIB dans Xamarin. iOS
description: Ce document décrit comment Xamarin. iOS génère du code pour mapper des fichiers. C#XIB à, ce qui rend les contrôles visuels accessibles par programme.
ms.prod: xamarin
ms.assetid: 365991A8-E07A-0420-D28E-BC4D32065E1A
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 778b8eeb82ebfb62cfb8c16e14f341c9afb8ff7a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022253"
---
# <a name="xib-code-generation-in-xamarinios"></a>Génération de code. XIB dans Xamarin. iOS

> [!IMPORTANT]
> Ce document explique l’intégration de Visual Studio pour Mac à la Interface Builder de Xcode uniquement, car les actions et les prises ne sont pas utilisées dans le Xamarin Designer pour iOS. Pour plus d’informations sur le concepteur iOS, consultez le document du [Concepteur iOS](~/ios/user-interface/designer/index.md) .

L’outil Apple Interface Builder (« IB ») peut être utilisé pour concevoir visuellement des interfaces utilisateur. Les définitions d’interface créées par IB sont enregistrées dans des fichiers **. XIB** . Les widgets et autres objets dans les fichiers **. XIB** peuvent recevoir une « identité de classe », qui peut être un type personnalisé défini par l’utilisateur. Cela vous permet de personnaliser le comportement des widgets et d’écrire des widgets personnalisés.

Ces classes d’utilisateur sont normalement des sous-classes de classes de contrôleur d’interface utilisateur. Ils possèdent des *prises* (analogues aux propriétés) et des *actions* (analogues aux événements) qui peuvent être connectées à des objets d’interface. Lors de l’exécution, lorsque le fichier IB est chargé, les objets sont créés, et les prises et actions sont connectées aux différents objets d’interface utilisateur de façon dynamique. Lors de la définition de ces classes managées, vous devez définir toutes les actions et prises correspondant à celles attendues par IB. Visual Studio pour Mac utilise un modèle de type CodeBehind pour simplifier cela. Cela est similaire à celui de Xcode pour objective-C, mais le modèle et les conventions de génération de code ont été modifiés pour être plus familiers aux développeurs .NET.

L’utilisation des fichiers **. XIB** n’est pas prise en charge actuellement dans Xamarin. IOS pour Visual Studio.

## <a name="xib-files-and-custom-classes"></a>Fichiers. XIB et classes personnalisées

En plus d’utiliser les types existants de cacao Touch, il est possible de définir des types personnalisés dans les fichiers **. XIB** . Il est également possible d’utiliser des types définis dans d’autres fichiers **. XIB** , ou définis uniquement dans C# le code. Actuellement, Interface Builder n’a pas connaissance des détails des types définis en dehors du fichier **. XIB actuel.** il ne les répertorie pas ou n’affiche pas leurs prises et leurs actions personnalisées. La suppression de cette limitation est prévue dans le futur.

Les classes personnalisées peuvent être définies dans un fichier **. XIB** à l’aide de la commande « Ajouter une sous-classe » sous l’onglet « classes » de Interface Builder. Nous faisons référence à ceux-ci en tant que classes « CodeBehind ». Si le fichier **. XIB** a un fichier équivalent « . XIB.Designer.cs » dans le projet, Visual Studio pour Mac le remplit automatiquement avec les définitions de classes partielles pour toutes les classes personnalisées dans le fichier **. XIB**. Nous faisons référence à ces classes partielles comme « classes de concepteur ».

## <a name="generating-code"></a>Génération du code

Pour tout fichier **{0}. XIB** avec une action de génération de *page*, si un fichier **{0}. XIB.Designer.cs** existe également dans le projet, Visual Studio pour Mac générera des classes partielles dans le fichier de concepteur pour toutes les classes d’utilisateur qu’il peut trouver dans le fichier **. XIB** , avec les propriétés des prises et des méthodes partielles pour toutes les actions. La génération de code est activée simplement par la présence de ce fichier.

Le fichier du concepteur est automatiquement mis à jour lorsque le fichier **. XIB** est modifié et que Visual Studio pour Mac réobtient le focus. Le fichier de concepteur ne doit pas être modifié manuellement, car les modifications seront remplacées la prochaine fois que Visual Studio pour Mac met à jour le fichier.

## <a name="registration-and-namespaces"></a>Inscription et espaces de noms

Visual Studio pour Mac génère les classes de concepteur à l’aide de l’espace de noms par défaut du projet pour l’emplacement du fichier de concepteur, afin de le rendre cohérent avec le projet .NET normal Namespacing. L’espace de noms des fichiers de concepteur est piloté par l’espace de noms par défaut du projet et ses paramètres « stratégies de nommage .NET ». ATTENTION : si l’espace de noms par défaut de votre projet change, MD régénère les classes dans le nouvel espace de noms. vous pouvez donc constater que vos classes partielles ne correspondent plus.

Pour rendre la classe détectable par le runtime objective-C, Visual Studio pour Mac applique un attribut `[Register (name)]` à la classe. Bien que Xamarin. iOS inscrive automatiquement les classes dérivées de `NSObject`, il utilise les noms .NET qualifiés complets. L’attribut appliqué par Visual Studio pour Mac Substitue cela pour garantir que chaque classe est inscrite avec le nom utilisé dans le fichier **. XIB** . Si vous utilisez des classes personnalisées en IB sans utiliser Visual Studio pour Mac pour générer des fichiers de concepteur, vous devrez peut-être les appliquer manuellement pour que vos classes managées correspondent aux noms de classe objective-C attendus.

Les classes ne peuvent pas être définies dans plus d’un **. XIB**, ou elles sont en conflit.

## <a name="non-designer-class-parts"></a>Parties de classe non concepteur

Les classes partielles du concepteur ne sont pas destinées à être utilisées telles quelles. Les prises sont privées et aucune classe de base n’est spécifiée. Il est supposé que chaque classe aura une partie de classe « non-concepteur » correspondante dans un autre fichier, qui définit la classe de base, utilise ou expose les prises et définit des constructeurs qui sont requis pour instancier la classe à partir du code natif lors du chargement de **. XIB** . Les modèles default **. XIB** font cela, mais pour toutes les classes personnalisées supplémentaires que vous définissez dans un **. XIB**, vous devez ajouter manuellement la partie non du concepteur.

Cela est dû à la flexibilité. Par exemple, plusieurs classes CodeBehind peuvent sous-classe une classe abstraite managée commune, qui sous-classe la classe à sous-classée par IB.

Il est conventionnel de les placer dans un fichier **{0}. XIB.cs** à côté du fichier **{0}. XIB.Designer.cs** designer.

<a name="generated" />

## <a name="generated-actions-and-outlets"></a>Actions et prises générées

Dans les classes de concepteur partiel, Visual Studio pour Mac génère des propriétés correspondant à des prises connectées définies en IB, et des méthodes partielles correspondant à toutes les actions connectées.

### <a name="outlet-properties"></a>Propriétés de la sortie

Les classes de concepteur contiennent des propriétés correspondant à toutes les prises définies sur la classe personnalisée. Le fait qu’il s’agit de propriétés est un détail d’implémentation du pont Xamarin. iOS vers objective C, pour permettre la liaison tardive. Vous devez les considérer comme équivalents à des champs privés, destinés à être utilisés uniquement à partir de la classe CodeBehind. Si vous souhaitez les rendre publics, ajoutez des propriétés d’accesseur à la partie non de classe de concepteur, comme vous le feriez pour tout autre champ privé.

Si les propriétés de la prise sont définies pour avoir un type de `id` (équivalent à `NSObject`), le générateur de code du concepteur détermine actuellement le type le plus fort possible en fonction des objets connectés à cette sortie, pour des raisons pratiques.
Toutefois, cela n’est peut-être pas pris en charge dans les versions ultérieures. il est donc recommandé de taper explicitement les prises lors de la définition de la classe personnalisée.

### <a name="action-properties"></a>Propriétés de l’action

Les classes de concepteur contiennent des méthodes partielles correspondant à toutes les actions définies sur la classe personnalisée. Il s’agit de méthodes sans implémentation. L’objectif des méthodes partielles est double :

1. Si vous tapez `partial` dans le corps de classe de la partie de classe non concepteur, Visual Studio pour Mac offrira la saisie semi-automatique des signatures de toutes les méthodes partielles non implémentées.
2. Les signatures de méthode partielles ont un attribut appliqué qui les expose au monde objective-C, afin qu’ils puissent être gérés en tant qu’action correspondante.

Si vous le souhaitez, vous pouvez ignorer la méthode partielle et implémenter l’action en appliquant l’attribut à une autre méthode, ou le laisser passer à une classe de base.

Si les actions sont définies pour avoir un type d’expéditeur `id` (équivalent à `NSObject`), le générateur de code du concepteur détermine actuellement le type le plus fort possible en fonction des objets connectés à cette action. Toutefois, cela n’est peut-être pas pris en charge dans les versions ultérieures. il est donc recommandé de taper explicitement les actions lors de la définition de la classe personnalisée.

Notez que ces méthodes partielles sont créées uniquement C#pour, car CodeDom ne prend pas en charge les méthodes partielles, donc elles ne sont pas générées pour d’autres langages.

## <a name="cross-xib-class-usage"></a>Utilisation des classes Cross-XIB

Parfois, les utilisateurs souhaitent faire référence à la même classe à partir de plusieurs fichiers **. XIB** , par exemple avec les contrôleurs d’onglets. Pour ce faire, il suffit de référencer explicitement la définition de classe à partir d’un autre fichier **. XIB** ou de redéfinir le même nom de classe dans le second **. XIB**.

Ce dernier cas peut être problématique en raison de Visual Studio pour Mac traitement des fichiers **. XIB.** Il ne peut pas détecter et fusionner automatiquement les définitions dupliquées. vous risquez donc de vous retrouver avec des conflits d’application de l’attribut Register à plusieurs moments lorsque la même classe partielle est définie dans plusieurs fichiers de concepteur. Les versions récentes de Visual Studio pour Mac tentent de résoudre ce comportement, mais elles ne fonctionnent pas toujours comme prévu. À l’avenir, cela risque de ne pas être pris en charge et, à la place Visual Studio pour Mac rend tous les types définis dans tous les fichiers **. XIB** et le code managé dans le projet directement visibles à partir de tous les fichiers **. XIB** .

## <a name="type-resolution"></a>Résolution de type

Les types utilisés dans IB sont des noms de type objective-C. Ils sont mappés aux types CLR par le biais de l’utilisation d’attributs de registre. Lors de la génération du code de sortie et d’action, Visual Studio pour Mac résout les types CLR correspondants pour tous les types objective-C encapsulés par le noyau Xamarin. iOS et qualifie entièrement leurs noms de types.

Toutefois, le générateur de code ne peut pas actuellement résoudre les types CLR à partir des noms de type objective-C dans le code utilisateur ou les bibliothèques. dans ce cas, il renvoie le nom de type Verbatim. Cela signifie que le type CLR correspondant doit avoir le même nom que le type objective-C et se trouver dans le même espace de noms que le code qui l’utilise. Il est prévu de corriger ce problème à l’avenir en prenant en compte tous les types objective-C dans le projet lors de la génération du code.
