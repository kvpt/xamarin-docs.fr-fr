---
title: Exemple réel avancé (manuel)
description: Ce document explique comment utiliser la sortie de xcodebuild comme entrée pour objective Sharp, qui donne un aperçu de ce que fait l’objectif de la finesse.
ms.prod: xamarin
ms.assetid: 044FF669-0B81-4186-97A5-148C8B56EE9C
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 5e36a66949c55a85d84cbbb17fa4d276e3af1eee
ms.sourcegitcommit: acbaedbcb78bb5629d4a32e3b00f11540c93c216
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "78292195"
---
# <a name="advanced-manual-real-world-example"></a>Exemple réel avancé (manuel)

**Cet exemple utilise la [bibliothèque pop de Facebook](https://github.com/facebook/pop).**

Cette section décrit une approche plus avancée de la liaison, où nous allons utiliser l’outil de `xcodebuild` d’Apple pour commencer par créer le projet POP, puis déduire manuellement l’entrée pour objective Sharp. Cela couvre essentiellement ce qui est le plus clair dans la section précédente.

```bash
 $ git clone https://github.com/facebook/pop.git
Cloning into 'pop'...
   _(more git clone output)_

$ cd pop
```

Étant donné que la bibliothèque POP a un projet XCode (`pop.xcodeproj`), nous pouvons simplement utiliser `xcodebuild` pour générer un POP. Ce processus peut, à son tour, générer des fichiers d’en-tête qui doivent être analysés par l’objectif de la finesse. C’est la raison pour laquelle la génération avant la liaison est importante. Lors de la génération via `xcodebuild` vous devez transmettre l’identificateur et l’architecture du kit de développement logiciel 3,0 (SDK) que vous envisagez de passer à la finesse objective

```bash
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

Un grand nombre d’informations de génération sont générées dans la console dans le cadre de `xcodebuild`. Comme indiqué ci-dessus, nous voyons qu’une cible « CpHeader » a été exécutée et que les fichiers d’en-tête ont été copiés dans un répertoire de sortie de génération. C’est souvent le cas et simplifie la liaison : dans le cadre de la génération de la bibliothèque native, les fichiers d’en-tête sont souvent copiés dans un emplacement « public » qui peut faciliter l’analyse pour la liaison. Dans ce cas, nous savons que les fichiers d’en-tête du POP se trouvent dans le répertoire `build/Headers`.

Nous sommes maintenant prêts à lier POP. Nous savons que nous souhaitons créer un kit de développement logiciel (SDK) `iphoneos8.1` avec l’architecture `arm64`, et que les fichiers d’en-tête qui nous intéressent se trouvent dans `build/Headers` sous l’extraction git POP. Si nous examinons le répertoire `build/Headers`, nous verrons un certain nombre de fichiers d’en-tête :

```bash
$ ls build/Headers/POP/
POP.h                    POPAnimationTracer.h     POPDefines.h
POPAnimatableProperty.h  POPAnimator.h            POPGeometry.h
POPAnimation.h           POPAnimatorPrivate.h     POPLayerExtras.h
POPAnimationEvent.h      POPBasicAnimation.h      POPPropertyAnimation.h
POPAnimationExtras.h     POPCustomAnimation.h     POPSpringAnimation.h
POPAnimationPrivate.h    POPDecayAnimation.h
```

Si nous examinons `POP.h`, nous pouvons voir qu’il s’agit du fichier d’en-tête principal de niveau supérieur de la bibliothèque qui `#import`d’autres fichiers. Pour cette raison, nous n’avons besoin que de passer `POP.h` à la finesse objective, et Clang fera le reste en coulisses :

```bash
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

Vous remarquerez que nous avons passé un argument `-scope build/Headers` pour objective Sharp. Étant donné que les bibliothèques C et objective-C doivent `#import` ou `#include` d’autres fichiers d’en-tête qui sont des détails d’implémentation de la bibliothèque et non de l’API que vous souhaitez lier, l’argument `-scope` indique à objective d’ignorer toute API qui n’est pas définie dans un fichier quelque part dans le répertoire `-scope`.

Vous trouverez que l’argument `-scope` est souvent facultatif pour les bibliothèques implémentées correctement, mais il n’y a pas de préjudice en le fournissant explicitement.

En outre, nous avons spécifié `-c -Ibuild/headers`. Tout d’abord, l’argument `-c` indique à objective Sharp d’arrêter l’interprétation des arguments de ligne de commande et de passer tous les arguments suivants _directement au compilateur Clang_. Par conséquent, `-Ibuild/Headers` est un argument du compilateur Clang qui indique à Clang de rechercher les fichiers include sous `build/Headers`, où se trouvent les en-têtes POP. Sans cet argument, Clang ne sait pas où trouver les fichiers que `POP.h` est `#import`. _Presque tous les « problèmes » avec l’utilisation de la précision objective font l’objet_d’un passage à Clang.

### <a name="completing-the-binding"></a>Achèvement de la liaison

La finesse d’objectif a à présent généré des fichiers `Binding/ApiDefinitions.cs` et `Binding/StructsAndEnums.cs`.

Il s’agit de la première étape de base de la liaison. dans certains cas, il peut s’agir de tout ce dont vous avez besoin. Comme indiqué plus haut, toutefois, le développeur doit généralement modifier manuellement les fichiers générés une fois que l’outil a terminé son achèvement pour [résoudre les problèmes](~/cross-platform/macios/binding/objective-sharpie/platform/apidefinitions-structsandenums.md) qui n’ont pas pu être gérés automatiquement par l’outil.

Une fois les mises à jour terminées, ces deux fichiers peuvent maintenant être ajoutés à un projet de liaison dans Visual Studio pour Mac ou être transmis directement aux outils de `btouch` ou de `bmac` pour produire la liaison finale.

Pour obtenir une description complète du processus de liaison, consultez nos [instructions de procédure pas à pas](~/ios/platform/binding-objective-c/walkthrough.md).
