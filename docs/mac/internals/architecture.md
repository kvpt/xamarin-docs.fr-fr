---
title: Architecture Xamarin. Mac
description: Ce guide explore Xamarin. Mac et sa relation à Objective-C à un niveau bas. Il explique les concepts tels que la compilation, les sélecteurs, les bureaux d’enregistrement, le lancement d’applications et le générateur.
ms.prod: xamarin
ms.assetid: 74D1FF57-4F2A-4646-8669-003DE99671D4
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 04/12/2017
ms.openlocfilehash: 51900adb1dd15675e584671f3b06ad6d7572f47d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017556"
---
# <a name="xamarinmac-architecture"></a>Architecture Xamarin. Mac

_Ce guide explore Xamarin. Mac et sa relation à Objective-C à un niveau bas. Il explique les concepts tels que la compilation, les sélecteurs, les bureaux d’enregistrement, le lancement d’applications et le générateur._

## <a name="overview"></a>Vue d'ensemble

Les applications Xamarin. Mac s’exécutent dans l’environnement d’exécution mono et utilisent le compilateur de Xamarin pour compiler en langage intermédiaire (IL), qui est ensuite compilé juste-à-temps (JIT) en code natif au moment de l’exécution. S’exécute côte à côte avec le runtime objective-C. Les deux environnements d’exécution s’exécutent sur un noyau de type UNIX, en particulier XNU, et exposent diverses API au code utilisateur, ce qui permet aux développeurs d’accéder au système managé ou natif sous-jacent.

Le diagramme ci-dessous illustre une vue d’ensemble de cette architecture :

[![Diagramme montrant une vue d’ensemble de base de l’architecture](architecture-images/mac-arch.png "Diagramme montrant une vue d’ensemble de base de l’architecture")](architecture-images/mac-arch-large.png#lightbox)

### <a name="native-and-managed-code"></a>Code natif et managé

Lors du développement pour Xamarin, les termes *natif* et code *managé* sont souvent utilisés. Le code managé est du code dont l’exécution est gérée par le Common Language Runtime .NET Framework, ou dans le cas de Xamarin : le runtime mono.

Le code natif est du code qui s’exécute en mode natif sur la plateforme spécifique (par exemple, Objective-C ou code compilé de l’AOA, sur un processeur ARM). Ce guide explore la manière dont votre code managé est compilé en code natif et explique le fonctionnement d’une application Xamarin. Mac, en utilisant pleinement les API Mac d’Apple à l’aide de liaisons, tout en ayant également accès à. La BCL du NET et un langage sophistiqué tel C#que.

## <a name="requirements"></a>spécifications

Les éléments suivants sont requis pour développer une application macOS avec Xamarin.Mac :

- Un Mac exécutant macOS Sierra (10,12) ou une version ultérieure.
- La dernière version de Xcode (installée à partir de l' [App Store](https://itunes.apple.com/us/app/xcode/id497799835?mt=12))
- La dernière version de Xamarin. Mac et Visual Studio pour Mac

L’exécution d’applications créées Mac avec Xamarin.Mac a la configuration requise suivante :

- Un Mac exécutant Mac OS X 10,7 ou une version ultérieure.

## <a name="compilation"></a>Compilation

Quand vous compilez une application de plateforme Xamarin C# , le F#compilateur mono (ou) s’exécute et C# compile F# votre code et en langage MSIL (Microsoft Intermediate Language). Xamarin. Mac utilise ensuite un compilateur juste-à- *temps (JIT)* au moment de l’exécution pour compiler le code natif, ce qui permet une exécution sur l’architecture appropriée, si nécessaire.

Cela diffère de Xamarin. iOS qui utilise la compilation AOA. Lorsque vous utilisez le compilateur AOA, tous les assemblys et toutes les méthodes qu’ils contiennent sont compilés au moment de la génération. Avec JIT, la compilation se produit à la demande uniquement pour les méthodes exécutées.

Avec les applications Xamarin. Mac, mono est généralement incorporé dans l’offre groupée d’applications (et appelé **mono incorporé**). Lors de l’utilisation de l’API Xamarin. Mac classique, l’application peut utiliser à la place le **système mono**, mais ce n’est pas pris en charge dans la API unifiée. Mono système fait référence à mono qui a été installé dans le système d’exploitation. Lors du lancement de l’application, l’application Xamarin. Mac l’utilise.

## <a name="selectors"></a>Sélecteurs

Avec Xamarin, nous avons deux écosystèmes distincts, .NET et Apple, que nous devons rassembler pour qu’ils semblent aussi rationalisés que possible, afin de s’assurer que l’objectif final est une expérience utilisateur fluide. Nous avons vu dans la section précédente Comment les deux runtimes communiquent, et vous pouvez très bien avoir entendu parler du terme « liaisons » qui permet d’utiliser les API Mac natives dans Xamarin. Les liaisons sont expliquées en détail dans la [documentation relative à la liaison objective-C](~/mac/platform/binding.md), donc pour l’instant, examinons le fonctionnement de Xamarin. Mac.

Tout d’abord, il doit exister un moyen d’exposer objective- C#C à, ce qui est effectué via des sélecteurs. Un sélecteur est un message qui est envoyé à un objet ou à une classe. Avec Objective-C, cette opération s’effectue à l’aide des fonctions [objc_msgSend](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html) . Pour plus d’informations sur l’utilisation des sélecteurs, reportez-vous au guide [des sélecteurs objective-C](~/ios/internals/objective-c-selectors.md) iOS. Il doit également exister un moyen d’exposer le code managé à Objective-C, ce qui est plus compliqué en raison du fait que objective-C ne sait rien du code géré. Pour contourner ce propos, nous utilisons un [Bureau](~/mac/internals/registrar.md)d’enregistrement. Cela est expliqué plus en détail dans la section suivante.

## <a name="registrar"></a>Inscription

Comme indiqué ci-dessus, le Bureau d’enregistrement est du code qui expose du code managé à Objective-C. Pour ce faire, il crée une liste de toutes les classes managées qui dérivent de NSObject :

- Pour toutes les classes qui n’encapsulent pas une classe objective-C existante, elle crée une nouvelle classe objective-C avec des membres objective-c qui reflètent tous les membres managés ayant un attribut `[Export]`.
- Dans les implémentations pour chaque membre objective – C, le code est ajouté automatiquement pour appeler le membre managé mis en miroir.

Le pseudo-code ci-dessous montre un exemple de cette opération :

**C#(code managé) :**

```csharp
class MyViewController : UIViewController{
    [Export ("myFunc")]
    public void MyFunc ()
    {
    }
 }
 ```

**Objective-C (code natif) :**

```objc
@interface MyViewController : UIViewController
 - (void)myFunc;
@end 

@implementation MyViewController
- (void)myFunc {
    // Code to call the managed C# MyFunc method in MyViewController
}
@end
```

Le code managé peut contenir les attributs, `[Register]` et `[Export]`, que le Bureau d’enregistrement utilise pour savoir que l’objet doit être exposé à Objective-C. L’attribut [register] est utilisé pour spécifier le nom de la classe objective-C générée si le nom généré par défaut n’est pas approprié. Toutes les classes dérivées de NSObject sont automatiquement inscrites avec Objective-C. L’attribut [Export] requis contient une chaîne, qui est le sélecteur utilisé dans la classe objective-C générée.

Il existe deux types d’bureaux d’enregistrement utilisés dans Xamarin. Mac, dynamiques et statiques :

- Registraires dynamiques : il s’agit du Bureau d’enregistrement par défaut pour toutes les builds Xamarin. Mac. Le Bureau d’enregistrement dynamique procède à l’inscription de tous les types de votre assembly au moment de l’exécution. Pour ce faire, il utilise les fonctions fournies par l’API Runtime objective-C. Le Bureau d’enregistrement dynamique a donc un démarrage plus lent, mais une génération plus rapide. Les fonctions natives (généralement en C), appelées trampolines, sont utilisées en tant qu’implémentations de méthode lors de l’utilisation des registres dynamiques. Ils varient en fonction des différentes architectures.
- Bureaux d’enregistrement statiques : le Bureau d’enregistrement statique génère du code Objective-C pendant la génération, qui est ensuite compilé dans une bibliothèque statique et lié à l’exécutable. Cela permet un démarrage plus rapide, mais prend plus de temps pendant la génération.

## <a name="application-launch"></a>Lancement de l’application

La logique de démarrage de Xamarin. Mac varie selon que l’utilisation de la fonction intégrée ou mono système est utilisée. Pour afficher le code et les étapes du lancement de l’application Xamarin. Mac, reportez-vous au fichier d' [en-tête Launch](https://github.com/xamarin/xamarin-macios/blob/master/runtime/xamarin/launch.h) dans le référentiel public Xamarin-MaCiO.

## <a name="generator"></a>Générateur

Xamarin. Mac contient des définitions pour chaque API Mac. Vous pouvez parcourir n’importe lequel d’entre eux sur le [référentiel MaCiO GitHub](https://github.com/xamarin/xamarin-macios/tree/master/src). Ces définitions contiennent des interfaces avec des attributs, ainsi que toutes les méthodes et propriétés nécessaires. Par exemple, le code suivant est utilisé pour définir un NSBox dans l' [espace de noms AppKit](https://github.com/xamarin/xamarin-macios/blob/master/src/appkit.cs#L1465-L1526). Notez qu’il s’agit d’une interface avec un certain nombre de méthodes et de propriétés :

```csharp
[BaseType (typeof (NSView))]
public interface NSBox {

    …

    [Export ("borderRect")]
    CGRect BorderRect { get; }

    [Export ("titleRect")]
    CGRect TitleRect { get; }

    [Export ("titleCell")]
    NSObject TitleCell { get; }

    [Export ("sizeToFit")]
    void SizeToFit ();

    [Export ("contentViewMargins")]
    CGSize ContentViewMargins { get; set; }

    [Export ("setFrameFromContentFrame:")]
    void SetFrameFromContentFrame (CGRect contentFrame);

    …

}
```

Le générateur, appelé `bmac` dans Xamarin. Mac, prend ces fichiers de définition et utilise les outils .NET pour les compiler dans un assembly temporaire. Toutefois, cet assembly temporaire n’est pas utilisable pour appeler du code Objective-C. Le générateur lit ensuite l’assembly temporaire et C# génère du code qui peut être utilisé au moment de l’exécution. C’est pourquoi, par exemple, si vous ajoutez un attribut aléatoire à votre fichier Definition. cs, il n’apparaît pas dans le code généré. Le générateur ne le sait pas, et par conséquent `bmac` ne sait pas qu’il le fait dans l’assembly temporaire pour le générer.

Une fois le fichier Xamarin. Mac. dll créé, le gestionnaire de package `mmp`regroupe tous les composants ensemble.

À un niveau élevé, il y parvient en exécutant les tâches suivantes :

- Créez une structure de bundle d’applications.
- Copiez dans vos assemblys managés.
- Si la liaison est activée, exécutez l’éditeur de liens managé pour optimiser vos assemblys en supprimant les parties inutilisées.
- Créer une application de lancement, la liaison dans le code de lancement est discutée avec le code d’inscription en mode statique.

Elle est ensuite exécutée dans le cadre du processus de génération de l’utilisateur qui compile le code utilisateur dans un assembly qui fait référence à Xamarin. Mac. dll et qui s’exécute `mmp` pour en faire un package

Pour plus d’informations sur l’éditeur de liens et son utilisation, reportez-vous au Guide de l' [éditeur de liens](~/ios/deploy-test/linker.md) iOS.

## <a name="summary"></a>Récapitulatif

Ce guide a examiné la compilation des applications Xamarin. Mac et explorait Xamarin. Mac et sa relation avec Objective-C.
