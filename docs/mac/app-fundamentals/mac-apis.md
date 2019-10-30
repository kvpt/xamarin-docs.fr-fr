---
title: API macOS pour les développeurs Xamarin. Mac
description: Ce document décrit comment lire les sélecteurs objective-C et comment trouver les méthodes C# correspondantes dans une application Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 9F7451FA-E07E-4C7B-B5CF-27AFC157ECDA
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/02/2017
ms.openlocfilehash: cd427d13bb79fd31e1e814726aaaf61788ae10ec
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030078"
---
# <a name="macos-apis-for-xamarinmac-developers"></a>API macOS pour les développeurs Xamarin. Mac

## <a name="overview"></a>Vue d'ensemble

Pour la plupart de votre temps, le développement avec Xamarin. Mac vous permet de penser, de lire C# et d’écrire sans trop de préoccupation avec les API objective-C sous-jacentes. Toutefois, vous aurez parfois besoin de lire la documentation de l’API auprès d’Apple, de traduire une réponse de Stack Overflow à une solution pour votre problème ou de la comparer à un exemple existant.

## <a name="reading-enough-objective-c-to-be-dangerous"></a>En lisant suffisamment objective-C pour être dangereux

Il est parfois nécessaire de lire une définition objective-C ou un appel de méthode et de la traduire C# en méthode équivalente. Jetons un coup d’œil à une définition de fonction objective-C et décomposons les éléments. Vous pouvez trouver cette méthode (un *Sélecteur* dans objective-C) sur `NSTableView`:

```objc
- (BOOL)canDragRowsWithIndexes:(NSIndexSet *)rowIndexes atPoint:(NSPoint)mouseDownPoint
```

La déclaration peut être lue de gauche à droite :

- Le préfixe `-` signifie qu’il s’agit d’une méthode d’instance (non statique). + signifie qu’il s’agit d’une méthode de classe (statique)
- `(BOOL)` est le type de retour (bool C#dans)
- `canDragRowsWithIndexes` est la première partie du nom.
- `(NSIndexSet *)rowIndexes` est le premier paramètre et son type. Le premier paramètre est au format : `(Type) pararmName`
- `atPoint:(NSPoint)mouseDownPoint` est le second paramètre et son type. Chaque paramètre après le premier est le format : `selectorPart:(Type) pararmName`
- Le nom complet de ce sélecteur de messages est le suivant : `canDragRowsWithIndexes:atPoint:`. Notez que le `:` à la fin, il est important.
- La liaison Xamarin. Mac C# réelle est : `bool CanDragRows (NSIndexSet rowIndexes, PointF mouseDownPoint)`

Cet appel du sélecteur peut être lu de la même façon :

```objc
[v canDragRowsWithIndexes:set atPoint:point];
```

- Le sélecteur de `canDragRowsWithIndexes:atPoint` de l’instance `v` est appelé avec deux paramètres, `set` et `point`, passé.
- Dans C#, l’appel de la méthode se présente comme suit : `x.CanDragRows (set, point);`

<a name="finding_selector" />

## <a name="finding-the-c-member-for-a-given-selector"></a>Recherche du C# membre pour un sélecteur donné

Maintenant que vous avez trouvé le sélecteur objective-C que vous devez appeler, l’étape suivante consiste à le mapper à C# un membre équivalent. Vous pouvez essayer quatre approches (en poursuivant avec l’exemple `NSTableView CanDragRows`) :

1. Utilisez la liste de saisie semi-automatique pour rechercher rapidement un nom identique. Étant donné que nous savons qu’il s’agit d’une instance de `NSTableView` vous pouvez taper :

    - `NSTableView x;`
    - `x.` [Ctrl + Espace si la liste n’apparaît pas).
    - `CanDrag` [entrée]
    - Cliquez avec le bouton droit sur la méthode, accédez à déclaration pour ouvrir le navigateur de l’assembly dans lequel vous pouvez comparer l’attribut `Export` au sélecteur en question

2. Recherche la liaison de classe entière. Étant donné que nous savons qu’il s’agit d’une instance de `NSTableView` vous pouvez taper :

    - `NSTableView x;`
    - Cliquez avec le bouton droit sur `NSTableView`, accédez à déclaration du navigateur d’assembly
    - Rechercher le sélecteur en question

3. Vous pouvez utiliser la [documentation en ligne de l’API Xamarin. Mac](https://docs.microsoft.com/dotnet/api/?view=xamarinmac-3.0) .

4. Miguel fournit une vue « Rosetta Stone » des API Xamarin. Mac dans [laquelle vous](https://tirania.org/tmp/rosetta.html) pouvez rechercher une API donnée. Si votre API n’est pas AppKit ou macOS spécifique, vous pouvez y accéder.

<!--
Note: In some cases, the assembly browser can hit a bug where it will open but not jump to the right definition. Keep that tab open, switch back to your source code and try again.
Note: The assembly browser tricks currently only works with Xamarin.Mac Classic. This will be fixed in a future version.
-->
