---
title: Architecture d’application iOS
description: Ce document décrit Xamarin.iOS à un bas niveau étudiant comment natif et managé code interagir, compilation AOT, sélecteurs, bureaux d’enregistrement, lancement de l’application et le générateur.
ms.prod: xamarin
ms.assetid: F40F2275-17DA-4B4D-9678-618FF25C6803
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 426c5ef5cc32877546ebb88cb485a81723816e6e
ms.sourcegitcommit: 58d8bbc19ead3eb535fb8248710d93ba0892e05d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67675067"
---
# <a name="ios-app-architecture"></a>Architecture d’application iOS

Les applications Xamarin.iOS s’exécutent dans l’environnement d’exécution Mono et utiliser la compilation à l’avance de la Time (AOT) complète pour compiler C# code de langage d’assembly ARM. Le logiciel s’exécute côte à côte avec les [Runtime Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Les deux environnements d’exécution s’exécuter sur un noyau de type UNIX, en particulier [XNU](https://en.wikipedia.org/wiki/XNU)et exposent les différentes API pour le code utilisateur permettant aux développeurs d’accéder au système natif ou managé sous-jacent.

Le diagramme suivant montre une vue d’ensemble de cette architecture :

[![](architecture-images/ios-arch-small.png "Ce diagramme montre une vue d’ensemble de l’architecture de la compilation à l’avance de la Time (AOT)")](architecture-images/ios-arch.png#lightbox)

## <a name="native-and-managed-code-an-explanation"></a>Natif et code managé : Une explication

Lors du développement pour Xamarin les termes du contrat *natif et managé* code sont souvent utilisés. [Le code managé](https://blogs.msdn.microsoft.com/brada/2004/01/09/what-is-managed-code/) est le code qui a son exécution gérée par le [Common Language Runtime de .NET Framework](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx), ou dans les cas de Xamarin : le Runtime Mono. Voici ce que nous appelons un langage intermédiaire.

Le code natif est le code qui s’exécute en mode natif sur la plateforme spécifique (par exemple, Objective-C ou même du code compilé de AOT, sur un processeur ARM). Ce guide explore comment AOT compile votre code managé en code natif et explique comment le fonctionnement d’une application Xamarin.iOS, rendre pleinement parti des API d’iOS d’Apple via l’utilisation de liaisons, tout en ayant accès à. NET BCL et un langage sophistiqué telles que C#.

## <a name="aot"></a>AOT

Lorsque vous compilez une application de plateforme de Xamarin, Mono C# (ou F#) compilateur s’exécutera et compilera votre C# et F# code en langage MSIL (Microsoft Intermediate Language). Si vous exécutez une application Xamarin.Android, une application Xamarin.Mac ou même une application Xamarin.iOS sur le simulateur, le [.NET Common Language Runtime (CLR)](https://msdn.microsoft.com/library/8bs2ecf4(v=vs.110).aspx) compile le code MSIL à l’aide d’un juste dans le compilateur de temps (JIT). Lors de l’exécution de que ceci est compilé en code natif, ce qui peut exécuter sur l’architecture appropriée pour votre application.

Toutefois, il existe une restriction de sécurité sur iOS, définies par Apple, qui n’autorise pas l’exécution de code généré dynamiquement sur un appareil.
Pour vous assurer que nous respectons ces protocoles de sécurité, Xamarin.iOS utilise à la place un compilateur à l’avance de la Time (AOT) pour compiler le code managé. Cela produit un iOS native binaire, éventuellement optimisée avec LLVM pour les appareils, ce qui peuvent être déployés sur le processeur ARM d’Apple. Un diagramme général de comment cela s’ajuste est illustré ci-dessous :

[![](architecture-images/aot.png "Un diagramme général de comment cela s’ajuste")](architecture-images/aot-large.png#lightbox)

À l’aide d’AOA a un certain nombre de limites, qui sont détaillées dans le [Limitations](~/ios/internals/limitations.md) guide. Il fournit également un certain nombre d’améliorations sur JIT par une réduction dans le temps de démarrage et diverses optimisations des performances

Maintenant que nous avons étudié la façon dont le code est compilé à partir de la source en code natif, jetons un œil sous le capot pour voir comment Xamarin.iOS nous permet d’écrire des applications iOS entièrement natives

## <a name="selectors"></a>Sélecteurs

Grâce à Xamarin, nous avons deux écosystèmes distincts, .NET et Apple, que nous devons mettre ensemble semblent comme rationalisée que possible, pour vous assurer que l’objectif final est une expérience utilisateur. Nous l’avons vu dans la section ci-dessus de la façon dont les deux runtimes communiquent, et vous pouvez très bien avoir entendu du terme « liaisons » qui permet des API à utiliser dans Xamarin iOS natif. Les liaisons sont expliquées en détail dans notre [liaison Objective-C](~/cross-platform/macios/binding/overview.md) documentation, par conséquent, pour l’instant nous allons explorer le fonctionnement d’iOS sous le capot.

Tout d’abord, il doit exister un moyen d’exposer Objective-C à C#, ce qui est fait par le biais de sélecteurs. Un sélecteur est un message qui est envoyé à un objet ou une classe. Avec Objective-C cette opération est effectuée le [objc_msgSend](~/cross-platform/macios/binding/overview.md) fonctions.
Pour plus d’informations sur l’utilisation de sélecteurs, reportez-vous à la [sélecteurs Objective-C](~/ios/internals/objective-c-selectors.md) guide. Il doit également être un moyen d’exposer le code managé et Objective-C, qui est plus compliqué dû au fait que Objective-C ne sait pas quoi que ce soit sur le code managé. Pour contourner ce problème, nous utilisons *bureaux d’enregistrement*. Ils sont expliqués plus en détail dans la section suivante.

## <a name="registrars"></a>Bureaux d’enregistrement

Comme mentionné ci-dessus, le bureau d’enregistrement est de code que le code managé expose à Objective-C. Il effectue cela en créant une liste de chaque classe managée qui dérive de NSObject :

- Pour toutes les classes qui ne sont pas encapsulant une classe Objective-C existante, il crée une nouvelle classe Objective-C avec les membres Objective-C mise en miroir de tous les membres managés qui ont une [`Export`] attribut.

- Dans les implémentations pour chaque membre Objective-C, le code est ajouté automatiquement à appeler le membre géré mise en miroir.

Le pseudo-code ci-dessous montre un exemple de comment procéder :

**C#(Code managé)**

```csharp
 class MyViewController : UIViewController{
     [Export ("myFunc")]
     public void MyFunc ()
     {
     }
 }
```

**Objective-C:**

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

Le code managé peut contenir les attributs, `[Register]` et `[Export]`, que le bureau d’enregistrement utilise que l’objet doit être exposé à Objective-C.
Le `[Register]` attribut est utilisé pour spécifier le nom de la classe Objective-C générée au cas où le nom généré par défaut ne convient pas. Toutes les classes dérivées du NSObject sont automatiquement enregistrés avec Objective-C.
Requis `[Export]` attribut contient une chaîne, qui est le sélecteur utilisé dans la classe Objective-C générée.

Il existe deux types de bureaux d’enregistrement utilisé dans Xamarin.iOS – dynamique et statique :


- **Bureaux d’enregistrement dynamique** – le bureau d’enregistrement dynamique est l’inscription de tous les types dans votre assembly lors de l’exécution. Pour ce faire à l’aide des fonctions fournies par [API du runtime de Objective-C](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/). Le bureau d’enregistrement dynamique a par conséquent un démarrage plus lent, mais plus rapide d’heure de création. Il s’agit par défaut pour le simulateur iOS. Fonctions natives (généralement en C), appelées trampolines, sont utilisées comme des implémentations de méthode lorsque vous utilisez le bureau d’enregistrement dynamique. Ils varient entre les différentes architectures.

- **Bureaux d’enregistrement statique** – le bureau d’enregistrement statique génère le code Objective-C pendant la génération, qui est ensuite compilée dans une bibliothèque statique et liée dans le fichier exécutable. Cela permet un démarrage plus rapide, mais prend plus de temps au moment de la build. Cela est utilisé par défaut pour les builds d’appareil. Le bureau d’enregistrement statique peut également servir avec le simulateur iOS en passant `--registrar:static` comme un `mtouch` d’attribut dans les options de génération de votre projet, comme indiqué ci-dessous :

    [![](architecture-images/image1.png "Définition des arguments mtouch supplémentaires")](architecture-images/image1.png#lightbox)

Pour plus d’informations sur les spécificités du système d’enregistrement de Type utilisé par Xamarin.iOS iOS, reportez-vous à la [bureau d’enregistrement de Type](~/ios/internals/registrar.md) guide.

## <a name="application-launch"></a>Lancement de l’application

Le point d’entrée de tous les exécutables de Xamarin.iOS est fourni par une fonction appelée `xamarin_main`, qui initialise mono.

Selon le type de projet, ce qui suit s’effectue :

- Pour iOS normales et tvOS, la méthode Main gérée, fournie par l’application Xamarin est appelée. Cela géré la méthode Main appelle ensuite `UIApplication.Main`, qui est le point d’entrée pour Objective-C. UIApplication.Main est la liaison pour Objective-C `UIApplicationMain` (méthode).
- Pour les extensions, la fonction native – `NSExtensionMain` ou (`NSExtensionmain` pour les extensions WatchOS) – fournies par Apple bibliothèques est appelée. Étant donné que ces projets sont des bibliothèques de classes et des projets non exécutables, il n’existe aucune méthodes Main de géré à exécuter.

Toute cette séquence de lancement est compilé dans une bibliothèque statique, qui est ensuite liée à votre fichier exécutable final pour que votre application sache comment obtenir décoller.

Notre application a démarré à ce stade, Mono est en cours d’exécution, nous sommes dans le code managé et nous savons comment appeler du code natif et d’être rappelé. La prochaine chose que nous devons faire consiste à commencer à ajouter des contrôles de réellement et de rendre l’application interactive.


## <a name="generator"></a>Générateur

Xamarin.iOS contient des définitions pour chaque API iOS unique. Vous pouvez parcourir ces sur le [MaciOS github référentiel](https://github.com/xamarin/xamarin-macios/tree/master/src). Ces définitions contiennent des interfaces avec des attributs, ainsi que les méthodes nécessaires et propriétés. Par exemple, le code suivant est utilisé pour définir un UIToolbar dans le UIKit [espace de noms](https://github.com/xamarin/xamarin-macios/blob/master/src/uikit.cs#L11277-L11327). Notez qu’il s’agit d’une interface avec un nombre de méthodes et propriétés :

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

Le générateur, appelé [ `btouch` ](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs) dans Xamarin.iOS, prend ces fichiers de définition et utilise les outils de .NET à [les compiler dans un assembly temporaire](https://github.com/xamarin/xamarin-macios/blob/master/src/btouch.cs#L318). Toutefois, cet assembly temporaire n’est pas utilisable pour appeler le code Objective-C. Le générateur, puis lit l’assembly temporaire et génère C# code qui peut être utilisé lors de l’exécution.
C’est pourquoi, par exemple, si vous ajoutez un attribut aléatoire à votre fichier .cs de définition, il n’apparaît pas dans le code généré. Le générateur ne connaît et par conséquent `btouch` ne sait pas à rechercher dans l’assembly temporaire, la sortie.

Une fois que le Xamarin.iOS.dll a été créé, mtouch sera regrouper tous les composants.

À un niveau élevé, pour cela, en exécutant les tâches suivantes :

-   Créer une structure de bundle d’application.
-   Copiez vos assemblys managés.
-   Si la liaison est activée, exécutez l’éditeur de liens géré pour optimiser vos assemblys par extraction de pièces inutilisées.
-   Compilation AOT.
-   Créer un fichier exécutable natif, ce qui génère une série de bibliothèques statiques (une pour chaque assembly) qui sont liées dans l’exécutable natif, afin que l’exécutable natif comprend le code de lanceur, le code d’inscription (si elle est statique) et toutes les sorties à partir de l’AOA compilateur


Pour plus d’informations sur l’éditeur de liens et son utilisation, reportez-vous à la [l’éditeur de liens](~/ios/deploy-test/linker.md) guide.

## <a name="summary"></a>Récapitulatif

Ce guide examiné la compilation AOT d’applications Xamarin.iOS et exploré Xamarin.iOS et sa relation avec Objective-C en profondeur.

## <a name="related-links"></a>Liens associés

- [Limitations](~/ios/internals/limitations.md)
- [Liaison Objective-C](~/cross-platform/macios/binding/overview.md)
- [Sélecteurs objective-C](~/ios/internals/objective-c-selectors.md)
- [Bureau d’enregistrement de type](~/ios/internals/registrar.md)
- [Éditeur de liens](~/ios/deploy-test/linker.md)
