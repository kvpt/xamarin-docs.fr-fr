---
title: Exemple réel avancé (manuel)
description: Ce document explique comment utiliser la sortie de xcodebuild comme entrée pour objective Sharp, qui donne un aperçu de ce que fait l’objectif de la finesse.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: a4a6df4916ae5dcc2a0f826d2f0ab9d09167ba5f
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290046"
---
# <a name="advanced-manual-real-world-example"></a>Exemple réel avancé (manuel)

**Cet exemple utilise la [bibliothèque pop de Facebook](https://github.com/facebook/pop).**

Cette section décrit une approche plus avancée de la liaison, où nous allons utiliser l' `xcodebuild` outil d’Apple pour commencer par créer le projet pop, puis déduire manuellement l’entrée pour objective Sharp. Cela couvre essentiellement ce qui est le plus clair dans la section précédente.

```
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Étant donné que la bibliothèque pop a un projet`pop.xcodeproj`Xcode (), nous pouvons `xcodebuild` simplement utiliser pour générer un pop. Ce processus peut, à son tour, générer des fichiers d’en-tête qui doivent être analysés par l’objectif de la finesse. C’est la raison pour laquelle la génération avant la liaison est importante. Lors de la `xcodebuild` création via, vous devez transmettre l’identificateur et l’architecture du kit de développement logiciel 3,0 (SDK) que vous envisagez de passer à la finesse objective.

```
$ xcodebuild -sdk iphoneos9.0 -arch arm64

Build settings from command line:
    ARCHS = arm64
    SDKROOT = iphoneos8.1
 
=== BUILD TARGET pop OF PROJECT pop WITH THE DEFAULT CONFIGURATION (Release) ===
 
...
 
CpHeader pop/POPAnimationTracer.h build/Headers/POP/POPAnimationTracer.h
    cd /Users/aaron/src/sharpie/pop
    export PATH="/Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin:/Applications/Xcode.app/Contents/Developer/usr/bin:/Users/aaron/bin::/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/git/bin:/Users/aaron/.rvm/bin"
    builtin-copy -exclude .DS_Store -exclude CVS -exclude .svn -exclude .git -exclude .hg -strip-debug-symbols -strip-tool /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/strip -resolve-src-symlinks /Users/aaron/src/sharpie/pop/pop/POPAnimationTracer.h /Users/aaron/src/sharpie/pop/build/Headers/POP
 
...
 
** BUILD SUCCEEDED **
```

Un grand nombre d’informations de génération sont générées dans la console dans le `xcodebuild`cadre de. Comme indiqué ci-dessus, nous voyons qu’une cible « CpHeader » a été exécutée et que les fichiers d’en-tête ont été copiés dans un répertoire de sortie de génération. C’est souvent le cas et simplifie la liaison : dans le cadre de la génération de la bibliothèque native, les fichiers d’en-tête sont souvent copiés dans un emplacement « public » qui peut faciliter l’analyse pour la liaison. Dans ce cas, nous savons que les fichiers d’en-tête du `build/Headers` pop se trouvent dans le répertoire.

Nous sommes maintenant prêts à lier POP. Nous savons que nous souhaitons créer un kit de `iphoneos8.1` développement logiciel `arm64` (SDK) avec l’architecture, et que les fichiers d' `build/Headers` en-tête qui nous intéressent se trouvent dans le cadre de l’extraction de git pop. Si nous examinons le `build/Headers` répertoire, nous verrons un certain nombre de fichiers d’en-tête :

```
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Si nous examinons `POP.h`, nous pouvons voir que c’est le fichier d’en-tête principal de la `#import`bibliothèque qui contient d’autres fichiers. Pour cette raison, nous devons simplement passer `POP.h` à objective Sharp, et Clang fera le reste en coulisses :

```
$ sharpie bind -output Binding -sdk iphoneos8.1 \
    -scope build/Headers build/Headers/POP/POP.h \
    -c -Ibuild/Headers -arch arm64

Parsing Native Code...

Binding...
  [write] ApiDefinitions.cs
  [write] StructsAndEnums.cs

Binding Analysis:
  Automated binding is complete, but there are a few APIs which have
  been flagged with [Verify] attributes. While the entire binding
  should be audited for best API design practices, look more closely
  at APIs with the following Verify attribute hints:

  ConstantsInterfaceAssociation (1 instance):
    There's no fool-proof way to determine with which Objective-C
    interface an extern variable declaration may be associated.
    Instances of these are bound as [Field] properties in a partial
    interface into a near-by concrete interface to produce a more
    intuitive API, possibly eliminating the 'Constants' interface
    altogether.

  StronglyTypedNSArray (4 instances):
    A native NSArray* was bound as NSObject[]. It might be possible
    to more strongly type the array in the binding based on
    expectations set through API documentation (e.g. comments in the
    header file) or by examining the array contents through testing.
    For example, an NSArray* containing only NSNumber* instances can
    be bound as NSNumber[] instead of NSObject[].

  MethodToProperty (2 instances):
    An Objective-C method was bound as a C# property due to
    convention such as taking no parameters and returning a value (
    non-void return). Often methods like these should be bound as
    properties to surface a nicer API, but sometimes false-positives
    can occur and the binding should actually be a method.

  Once you have verified a Verify attribute, you should remove it
  from the binding source code. The presence of Verify attributes
  intentionally cause build failures.

  For more information about the Verify attribute hints above,
  consult the Objective Sharpie documentation by running 'sharpie
  docs' or visiting the following URL:

    http://xmn.io/sharpie-docs

Submitting usage data to Xamarin...
  Submitted - thank you for helping to improve Objective Sharpie!

Done.
```

Vous remarquerez que nous avons `-scope build/Headers` passé un argument à la finesse objective. Étant donné que les bibliothèques c et objective `#include` -c doivent `#import` ou d’autres fichiers d’en-tête qui sont des détails d’implémentation de la `-scope` Bibliothèque et non des API que vous souhaitez lier, l’argument indique à objective d’avoir la possibilité d’ignorer toute API qui n’est pas définie dans un fichier situé dans le `-scope` répertoire.

Vous trouverez que l' `-scope` argument est souvent facultatif pour les bibliothèques implémentées correctement, mais il n’y a pas de préjudice dans sa fourniture explicite.

En outre, nous avons `-c -Ibuild/headers`spécifié. Tout d’abord `-c` , l’argument indique à objective Sharp d’arrêter l’interprétation des arguments de ligne de commande et de passer tous les arguments suivants _directement au compilateur Clang_. Par conséquent `-Ibuild/Headers` , est un argument du compilateur Clang qui indique à Clang de rechercher les `build/Headers`fichiers include sous, où se trouvent les en-têtes pop. Sans cet argument, Clang ne sait pas où trouver les fichiers `POP.h` `#import`en place. _Presque tous les « problèmes » avec l’utilisation de la précision objective font l’objet_d’un passage à Clang.

### <a name="completing-the-binding"></a>Achèvement de la liaison

La finesse d’objectif a à `Binding/ApiDefinitions.cs` présent `Binding/StructsAndEnums.cs` généré des fichiers et.

Il s’agit de la première étape de base de la liaison. dans certains cas, il peut s’agir de tout ce dont vous avez besoin. Comme indiqué plus haut, toutefois, le développeur doit généralement modifier manuellement les fichiers générés une fois que l’outil a terminé son achèvement pour [résoudre les problèmes](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) qui n’ont pas pu être gérés automatiquement par l’outil.

Une fois les mises à jour terminées, ces deux fichiers peuvent maintenant être ajoutés à un projet de liaison dans Visual Studio pour Mac ou être passés directement `btouch` aux `bmac` outils ou pour produire la liaison finale.

Pour obtenir une description complète du processus de liaison, consultez nos [instructions de procédure pas à pas](~/ios/platform/binding-objective-c/walkthrough.md).
