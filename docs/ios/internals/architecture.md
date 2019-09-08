---
title: Architecture des applications iOS
description: Ce document décrit Xamarin. iOS à un niveau bas, en expliquant comment interagir avec le code natif et managé, la compilation AOA, les sélecteurs, les bureaux d’enregistrement, le lancement d’applications et le générateur.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: b0cece7f553d0169c311e6614428ed37c5c77813
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768536"
---
# <a name="ios-app-architecture"></a>Architecture des applications iOS

Les applications Xamarin. iOS s’exécutent dans l’environnement d’exécution mono et utilisent la compilation de l’heure d’avance C# complète (AOA) pour compiler le code en langage assembleur ARM. S’exécute côte à côte avec le [Runtime objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Les deux environnements d’exécution s’exécutent sur un noyau de type UNIX, en particulier [xnu](https://en.wikipedia.org/wiki/XNU), et exposent diverses API au code utilisateur, ce qui permet aux développeurs d’accéder au système managé ou natif sous-jacent.

Le diagramme ci-dessous illustre une vue d’ensemble de cette architecture :

[![](architecture-images/ios-arch-small.png "Ce diagramme illustre une vue d’ensemble de base de l’architecture de compilation de l’heure d’été")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Code natif et managé : Explication

Lors du développement pour Xamarin, les termes *natif et code managé* sont souvent utilisés. Le code [managé](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) est du code dont l’exécution est gérée par le [common Language Runtime .NET Framework](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx), ou dans le cas de Xamarin : le runtime mono. C’est ce que nous appelons un langage intermédiaire.

Le code natif est du code qui s’exécute en mode natif sur la plateforme spécifique (par exemple, Objective-C ou code compilé de l’AOA, sur un processeur ARM). Ce guide explore la manière dont AOA compile votre code managé en code natif et explique comment fonctionne une application Xamarin. iOS, en utilisant pleinement les API iOS d’Apple via l’utilisation de liaisons, tout en ayant également accès à. La BCL du NET et un langage sophistiqué tel C#que.

## <a name="aot"></a>AOT

Quand vous compilez une application de plateforme Xamarin C# , le F#compilateur mono (ou) s’exécute et C# compile F# votre code et en langage MSIL (Microsoft Intermediate Language). Si vous exécutez une application Xamarin. Android, Xamarin. Mac, ou même une application Xamarin. iOS sur le simulateur, le [clr (Common Language Runtime) .net](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compile le MSIL à l’aide d’un compilateur juste-à-temps (JIT). Au moment de l’exécution, cela est compilé en code natif, qui peut s’exécuter sur l’architecture appropriée pour votre application.

Toutefois, il existe une restriction de sécurité sur iOS, définie par Apple, qui interdit l’exécution de code généré dynamiquement sur un appareil.
Pour garantir que nous respectons ces protocoles de sécurité, Xamarin. iOS utilise à la place un compilateur d’anticipation du temps (AOA) pour compiler le code managé. Cela génère un fichier binaire iOS natif, éventuellement optimisé avec LLVM pour les appareils, qui peut être déployé sur le processeur ARM d’Apple. Un diagramme approximatif de la façon dont cela s’ajuste à l’ensemble est illustré ci-dessous :

[![](architecture-images/aot.png "Diagramme approximatif de la façon dont cela s’ajuste")](architecture-images/aot-large.png#lightbox)

L’utilisation de l’AOA présente un certain nombre de limitations, qui sont détaillées dans le guide des [limitations](~/ios/internals/limitations.md) . Il fournit également un certain nombre d’améliorations par rapport à JIT grâce à une réduction du temps de démarrage et à diverses optimisations de performances.

Maintenant que nous avons exploré la manière dont le code est compilé du code source en code natif, jetons un coup d’œil pour voir comment Xamarin. iOS nous permet d’écrire des applications iOS entièrement natives

## <a name="selectors"></a>Sélecteurs

Avec Xamarin, nous avons deux écosystèmes distincts, .NET et Apple, que nous devons rassembler pour qu’ils semblent aussi rationalisés que possible, afin de s’assurer que l’objectif final est une expérience utilisateur fluide. Nous avons vu dans la section précédente Comment les deux runtimes communiquent, et vous pouvez très bien avoir entendu parler du terme « liaisons » qui permet d’utiliser les API iOS natives dans Xamarin. Les liaisons sont expliquées en détail dans notre documentation sur la [liaison objective-C](~/cross-platform/macios/binding/overview.md) . pour l’instant, examinons le fonctionnement d’iOS.

Tout d’abord, il doit exister un moyen d’exposer objective- C#C à, ce qui est effectué via des sélecteurs. Un sélecteur est un message qui est envoyé à un objet ou à une classe. Avec Objective-C, cette opération s’effectue à l’aide des fonctions [objc_msgSend](~/cross-platform/macios/binding/overview.md) .
Pour plus d’informations sur l’utilisation des sélecteurs, reportez-vous au guide [des sélecteurs objective-C](~/ios/internals/objective-c-selectors.md) . Il doit également exister un moyen d’exposer le code managé à Objective-C, ce qui est plus compliqué en raison du fait que objective-C ne sait rien du code géré. Pour contourner ce propos, nous utilisons des *bureaux*d’enregistrement. Celles-ci sont expliquées plus en détail dans la section suivante.

## <a name="registrars"></a>Registres

Comme indiqué ci-dessus, le Bureau d’enregistrement est du code qui expose du code managé à Objective-C. Pour ce faire, il crée une liste de toutes les classes managées qui dérivent de NSObject :

- Pour toutes les classes qui n’encapsulent pas une classe objective-c existante, elle crée une nouvelle classe objective-c avec des membres objective-c qui reflètent tous`Export`les membres managés ayant un attribut [].

- Dans les implémentations pour chaque membre objective – C, le code est ajouté automatiquement pour appeler le membre managé mis en miroir.

Le pseudo-code ci-dessous montre un exemple de cette opération :

**C#(Code managé)**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**Objective-C :**

```objectivec
@interface MyViewController : UIViewController { }

    -(void)myFunc;
@end

@implementation MyViewController {}

    -(void) myFunc
    {
        /* code to call the managed MyViewController.MyFunc method */
    }
@end

```

Le code managé peut contenir les attributs, `[Register]` et `[Export]`, que le Bureau d’enregistrement utilise pour savoir que l’objet doit être exposé à Objective-C.
L' `[Register]` attribut est utilisé pour spécifier le nom de la classe objective-C générée si le nom généré par défaut n’est pas approprié. Toutes les classes dérivées de NSObject sont automatiquement inscrites avec Objective-C.
L’attribut `[Export]` required contient une chaîne, qui est le sélecteur utilisé dans la classe objective-C générée.

Il existe deux types d’bureaux d’enregistrement utilisés dans Xamarin. iOS : dynamique et statique :

- Registraires **dynamiques** : le Bureau d’enregistrement dynamique procède à l’inscription de tous les types de votre assembly au moment de l’exécution. Pour ce faire, il utilise les fonctions fournies par l' [API Runtime objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Le Bureau d’enregistrement dynamique a donc un démarrage plus lent, mais une génération plus rapide. Il s’agit de la valeur par défaut pour le simulateur iOS. Les fonctions natives (généralement en C), appelées trampolines, sont utilisées en tant qu’implémentations de méthode lors de l’utilisation des registres dynamiques. Ils varient en fonction des différentes architectures.

- **Bureaux d’enregistrement statiques** : le Bureau d’enregistrement statique génère du code Objective-C pendant la génération, qui est ensuite compilé dans une bibliothèque statique et lié à l’exécutable. Cela permet un démarrage plus rapide, mais prend plus de temps pendant la génération. Cela est utilisé par défaut pour les builds d’appareils. Le Bureau d’enregistrement statique peut également être utilisé avec le simulateur `--registrar:static` IOS en `mtouch` passant en tant qu’attribut dans les options de génération de votre projet, comme indiqué ci-dessous :

    [![](architecture-images/image1.png "Définition d’arguments mTouch supplémentaires")](architecture-images/image1.png#lightbox)

Pour plus d’informations sur les caractéristiques du système d’inscription de type iOS utilisé par Xamarin. iOS, reportez-vous au Guide du [registraire de type](~/ios/internals/registrar.md) .

## <a name="application-launch"></a>Lancement de l’application

Le point d’entrée de tous les exécutables Xamarin. IOS est fourni par `xamarin_main`une fonction appelée, qui initialise mono.

En fonction du type de projet, les opérations suivantes sont effectuées :

- Pour les applications iOS et tvOS normales, la méthode main managée, fournie par l’application Xamarin, est appelée. Cette méthode main managée appelle `UIApplication.Main`alors, qui est le point d’entrée pour objective-C. UIApplication. main est la liaison de la `UIApplicationMain` méthode objective-C.
- Pour les extensions, la fonction native `NSExtensionMain` – ou`NSExtensionmain` (pour les extensions espionneos), fournie par les bibliothèques Apple, est appelée. Étant donné que ces projets sont des bibliothèques de classes et non des projets exécutables, il n’existe aucune méthode main managée à exécuter.

Toute cette séquence de lancement est compilée dans une bibliothèque statique, qui est ensuite liée à votre exécutable final pour que votre application sache comment sortir du sol.

À ce stade, notre application a démarré, mono est en cours d’exécution, nous sommes en code géré et nous savons comment appeler du code natif et être rappelé. La prochaine chose à faire est de commencer à ajouter des contrôles et à rendre l’application interactive.

## <a name="generator"></a>Générateur

Xamarin. iOS contient des définitions pour chaque API iOS unique. Vous pouvez parcourir n’importe lequel d’entre eux sur le [référentiel MaCiO GitHub](https://github.com/xamarin/xamarin-macios/tree/master/src). Ces définitions contiennent des interfaces avec des attributs, ainsi que toutes les méthodes et propriétés nécessaires. Par exemple, le code suivant est utilisé pour définir un UIToolbar dans l' [espace de noms](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327)uikit. Notez qu’il s’agit d’une interface avec un certain nombre de méthodes et de propriétés :

```csharp
[BaseType (typeof (UIView))]
public interface UIToolbar : UIBarPositioning {
    [Export ("initWithFrame:")]
    IntPtr Constructor (CGRect frame);

    [Export ("barStyle")]
    UIBarStyle BarStyle { get; set; }

    [Export ("items", ArgumentSemantic.Copy)][NullAllowed]
    UIBarButtonItem [] Items { get; set; }

    [Export ("translucent", ArgumentSemantic.Assign)]
    bool Translucent { [Bind ("isTranslucent")] get; set; }

    // done manually so we can keep this "in sync" with 'Items' property
    //[Export ("setItems:animated:")][PostGet ("Items")]
    //void SetItems (UIBarButtonItem [] items, bool animated);

    [Since (5,0)]
    [Export ("setBackgroundImage:forToolbarPosition:barMetrics:")]
    [Appearance]
    void SetBackgroundImage ([NullAllowed] UIImage backgroundImage, UIToolbarPosition position, UIBarMetrics barMetrics);

    [Since (5,0)]
    [Export ("backgroundImageForToolbarPosition:barMetrics:")]
    [Appearance]
    UIImage GetBackgroundImage (UIToolbarPosition position, UIBarMetrics barMetrics);

    ...
}
```

Le générateur, appelé [`btouch`](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) dans Xamarin. iOS, prend ces fichiers de définition et utilise les outils .net pour [les compiler dans un assembly temporaire](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Toutefois, cet assembly temporaire n’est pas utilisable pour appeler du code Objective-C. Le générateur lit ensuite l’assembly temporaire et C# génère du code qui peut être utilisé au moment de l’exécution.
C’est pourquoi, par exemple, si vous ajoutez un attribut aléatoire à votre fichier Definition. cs, il n’apparaît pas dans le code généré. Le générateur ne le sait pas et, `btouch` par conséquent, ne le recherche pas dans l’assembly temporaire pour le générer.

Une fois le fichier Xamarin. iOS. dll créé, mTouch regroupe tous les composants.

À un niveau élevé, il y parvient en exécutant les tâches suivantes :

- Créez une structure de bundle d’applications.
- Copiez dans vos assemblys managés.
- Si la liaison est activée, exécutez l’éditeur de liens managé pour optimiser vos assemblys en extrayant les parties inutilisées.
- Compilation AOA.
- Créez un exécutable natif, qui génère une série de bibliothèques statiques (une pour chaque assembly) qui sont liées à l’exécutable natif, afin que l’exécutable natif se compose du code du lanceur, du code d’inscription (si statique) et de toutes les sorties de l’AOA. compiler

Pour plus d’informations sur l’éditeur de liens et son utilisation, reportez-vous au Guide de l' [éditeur de liens](~/ios/deploy-test/linker.md) .

## <a name="summary"></a>Récapitulatif

Ce guide a examiné la compilation AOA des applications Xamarin. iOS et explorait Xamarin. iOS et sa relation à Objective-C en profondeur.

## <a name="related-links"></a>Liens associés

- [Limitations](~/ios/internals/limitations.md)
- [Liaison Objective-C](~/cross-platform/macios/binding/overview.md)
- [Sélecteurs objective-C](~/ios/internals/objective-c-selectors.md)
- [Inscription du type](~/ios/internals/registrar.md)
- [Éditeur de liens](~/ios/deploy-test/linker.md)
