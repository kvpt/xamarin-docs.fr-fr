---
title: Fonctionnement de Xamarin.Mac
description: Ce document décrit le fonctionnement interne de Xamarin. Mac. En particulier, il examine les constructeurs, la gestion de la mémoire, l’avance de la compilation et le Bureau d’enregistrement.
ms.prod: xamarin
ms.assetid: C2053ABB-6DBF-4233-AEEA-B72FC6A81FE1
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 05/25/2017
ms.openlocfilehash: 24ddd71fe1468edc70ec4d487dc2cb2dbd4da1b6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769820"
---
# <a name="how-xamarinmac-works"></a>Fonctionnement de Xamarin.Mac

La plupart du temps, le développeur n’aura jamais à se soucier de la « magie » interne de Xamarin. Mac. Toutefois, avoir une compréhension approfondie de la façon dont les choses fonctionnent sous le capot vous aidera à interpréter C# la documentation existante avec une lentille et un débogage problèmes lorsqu’ils surviennent.

Dans Xamarin. Mac, une application relie deux mondes : Le runtime objective-C contient des instances de classes natives (`NSString`, `NSApplication`, etc.) et le C# Runtime contient des instances de classes managées`System.String`( `HttpClient`,, etc.). Entre ces deux mondes, Xamarin. Mac crée un pont bidirectionnel afin qu’une application puisse appeler des méthodes (sélecteurs) en objective-c ( `NSApplication.Init`comme) et objective-c peut appeler les méthodes de C# l’application (comme les méthodes sur un délégué d’application). En général, les appels à Objective-C sont gérés de façon transparente par le biais des **P/Invoke** et du code d’exécution fourni par Xamarin.

<a name="exposing-classes" />

## <a name="exposing-c-classes--methods-to-objective-c"></a>Exposition de C# classes/méthodes à Objective-C

Toutefois, pour objective-C à rappeler dans les objets d' C# une application, ils doivent être exposés de manière à ce que objective-c puisse comprendre. Cette opération s’effectue à `Register` l' `Export` aide des attributs et. Prenons l’exemple suivant :

```csharp
[Register ("MyClass")]
public class MyClass : NSObject
{
   [Export ("init")]
   public MyClass ()
   {
   }

   [Export ("run")]
   public void Run ()
   {
   }
}
```

Dans cet exemple, le runtime objective-C connaîtra désormais une classe appelée `MyClass` avec des sélecteurs appelés `init` et. `run`

Dans la plupart des cas, il s’agit d’un détail d’implémentation que le développeur peut ignorer, car la plupart des rappels qu’une application reçoit `base` sont soit via des `AppDelegate`méthodes `Delegates`substituées sur des classes (telles que,, `DataSources`), soit sur des **actions** passé dans les API. Dans tous ces cas, `Export` les attributs ne sont pas nécessaires dans le C# code.

## <a name="constructor-runthrough"></a>Révision, constructeur

Dans de nombreux cas, le développeur doit exposer l’API de construction C# des classes de l’application au runtime objective-C afin qu’il puisse être instancié à partir d’emplacements tels que, lorsqu’il est appelé dans des fichiers Storyboard ou XIB. Voici les cinq constructeurs les plus courants utilisés dans les applications Xamarin. Mac :

```csharp
// Called when created from unmanaged code
public CustomView (IntPtr handle) : base (handle)
{
   Initialize ();
}

// Called when created directly from a XIB file
[Export ("initWithCoder:")]
public CustomView (NSCoder coder) : base (coder)
{
   Initialize ();
}

// Called from C# to instance NSView with a Frame (initWithFrame)
public CustomView (CGRect frame) : base (frame)
{
}

// Called from C# to instance NSView without setting the frame (init)
public CustomView () : base ()
{
}

// This is a special case constructor that you call on a derived class when the derived called has an [Export] constructor.
// For example, if you call init on NSString then you don’t want to call init on NSObject.
public CustomView () : base (NSObjectFlag.Empty)
{
}
```

En général, le développeur doit conserver les `IntPtr` constructeurs `NSCoder` et qui sont générés lors de la création de certains types `NSViews` , tels que le seul personnalisé. Si Xamarin. Mac doit appeler l’un de ces constructeurs en réponse à une demande d’exécution objective-C et que vous l’avez supprimée, l’application se bloque dans le code natif et il peut être difficile de déterminer exactement le problème.

## <a name="memory-management-and-cycles"></a>Gestion de la mémoire et cycles

La gestion de la mémoire dans Xamarin. Mac est très similaire à celle de Xamarin. iOS. Il s’agit également d’un sujet complexe, qui dépasse le cadre de ce document. Veuillez lire les [meilleures pratiques en matière de performances et de mémoire](~/cross-platform/deploy-test/memory-perf-best-practices.md).

## <a name="ahead-of-time-compilation"></a>Compilation anticipée

En règle générale, les applications .NET ne se compilent pas en code machine lorsqu’elles sont générées, mais elles se compilent en une couche intermédiaire appelée code IL (Intermediate Language) qui obtient la compilation _juste-à-temps_ (JIT) dans le code machine lorsque l’application est lancée.

Le temps nécessaire au runtime mono pour compiler JIT ce code machine peut ralentir le lancement d’une application Xamarin. Mac jusqu’à 20%, car il faut du temps pour que le code machine nécessaire soit généré.

En raison des limitations imposées par Apple sur iOS, la compilation JIT du code IL n’est pas disponible pour Xamarin. iOS. Par conséquent, toutes les applications Xamarin. iOS sont entièrement compilées dans le code machine pendant le cycle _de_ génération.

La nouveauté de Xamarin. Mac est la capacité à AOT le code de langage intermédiaire pendant le cycle de génération de l’application, tout comme Xamarin. iOS peut le faire. Xamarin. Mac utilise une approche AOA _hybride_ qui compile une majorité du code machine nécessaire, mais permet au runtime de compiler les trampolines nécessaires et la flexibilité de continuer à prendre en charge la réflexion. Emit (et d’autres cas d’usage qui fonctionnent actuellement sur Xamarin. Mac).

L’AOA peut aider à obtenir une application Xamarin. Mac dans deux domaines majeurs :

- **Amélioration des journaux des pannes « natifs** » : si une application Xamarin. Mac plante en code natif, ce qui est souvent le cas lors de l’exécution d’appels `null` non valides dans des API de cacao (comme l’envoi d’un dans une méthode qui ne l’accepte pas), les journaux de défaillance natifs avec des frames JIT sont difficiles à analyser. Étant donné que les frames JIT n’ont pas d’informations de débogage, il y aura plusieurs lignes avec des décalages hexadécimaux et aucune indication sur ce qui se passait. L’AOA génère des frames nommés « réels » et les suivis sont beaucoup plus faciles à lire. Cela signifie également que l’application Xamarin. Mac interagira mieux avec les outils natifs tels que **lldb** et **instruments**.
- **Amélioration des performances de lancement** : pour les applications Xamarin. Mac volumineuses, avec un temps de démarrage de plusieurs secondes, la compilation JIT de tout le code peut prendre beaucoup de temps. AOA effectue ce travail à l’avance.

### <a name="enabling-aot-compilation"></a>Activation de la compilation AOA

L’AOA est activé dans Xamarin. Mac en double-cliquant sur le **nom du projet** dans le **Explorateur de solutions**, en accédant à `--aot:[options]` la **Build Mac** et en ajoutant au champ **MMP arguments supplémentaires :** (où `[options]` est un ou plusieurs options pour contrôler le type AOA, voir ci-dessous). Par exemple :

![Ajout de l’AOA à des arguments MMP supplémentaires](how-it-works-images/aot01.png "Ajout de l’AOA à des arguments MMP supplémentaires")

> [!IMPORTANT]
> L’activation de la compilation AOA augmente considérablement le temps de génération, parfois jusqu’à plusieurs minutes, mais elle peut améliorer les temps de lancement des applications en moyenne de 20%. Par conséquent, la compilation AOA doit uniquement être activée dans les versions **Release** d’une application Xamarin. Mac.

### <a name="aot-compilation-options"></a>Options de compilation AOA

Il existe plusieurs options qui peuvent être ajustées lors de l’activation de la compilation AOA sur une application Xamarin. Mac :

- `none`-Aucune compilation AOA. Il s’agit du paramètre par défaut.
- `all`-AOT compile chaque assembly du monogroupement.
- `core`-AOT compile les `Xamarin.Mac`assemblys, `System` et `mscorlib` .
- `sdk`-AOT compile les assemblys et `Xamarin.Mac` les bibliothèques de classes de base (BCL).
- `|hybrid`-L’ajout de cette option à l’une des options ci-dessus active l’AOA hybride qui autorise la suppression du langage intermédiaire, mais entraîne des temps de compilation plus longs.
- `+`-Comprend un seul fichier pour la compilation AOA.
- `-`-Supprime un seul fichier de la compilation AOA.

Par exemple, `--aot:all,-MyAssembly.dll` activerait la compilation AOA sur tous les assemblys du monogroupe, _à l’exception_ `MyAssembly.dll` de et `--aot:core|hybrid,+MyOtherAssembly.dll,-mscorlib.dll` activerait Hybrid, l' `MyOtherAssembly.dll` AOA de code `mscorlib.dll`inclut le et l’exclusion de.

## <a name="partial-static-registrar"></a>Bureau d’enregistrement statique partiel

Lors du développement d’une application Xamarin. Mac, il peut être important de réduire le temps entre l’exécution d’une modification et le test de l’échéance du développement. Des stratégies telles que la modularisation des codes base et des tests unitaires peuvent aider à réduire les temps de compilation, car ils réduisent le nombre de fois où une application nécessite une reconstruction complète coûteuse.

En outre, et une nouveauté de Xamarin. Mac, le Bureau d’enregistrement _statique partiel_ (tel que novateur par Xamarin. IOS) peut réduire considérablement les temps de lancement d’une application Xamarin. Mac dans la configuration de **débogage** . Comprendre comment l’utilisation du Bureau d’enregistrement statique partiel peut compresser une amélioration apportée à 5 fois au lancement du débogage prend un peu plus d’informations sur ce que fait le Bureau d’enregistrement, sur la différence entre les éléments statiques et dynamiques, ainsi que sur ce que fait cette version statique partielle.

### <a name="about-the-registrar"></a>À propos du Bureau d’enregistrement

Dans le cadre d’une application Xamarin. Mac, l’infrastructure de cacao est basée sur Apple et le runtime objective-C. La création d’un pont entre ce « monde natif » et le « monde géré C# » de est la responsabilité principale de Xamarin. Mac. Une partie de cette tâche est gérée par le Bureau d’enregistrement, `NSApplication.Init ()` qui est exécuté à l’intérieur de la méthode. C’est l’une des raisons pour lesquelles toute utilisation des API de cacao dans `NSApplication.Init` Xamarin. Mac nécessite d’être appelée en premier.

La tâche du Bureau d’enregistrement est d’informer le runtime objective-C de l’existence des C# classes de l’application qui dérivent de `NSWindow`classes telles `NSObject`que `NSApplicationDelegate`, `NSView`, et. Cela nécessite une analyse de tous les types dans l’application pour déterminer ce qui doit être inscrit et les éléments de chaque type à signaler.

Cette analyse peut être effectuée de **manière dynamique**, au démarrage de l’application avec réflexion, ou **statiquement**, en tant qu’étape de génération. Lors du choix d’un type d’enregistrement, le développeur doit connaître les éléments suivants :

- L’inscription statique peut réduire considérablement les temps de lancement, mais elle peut ralentir considérablement les builds (généralement plus de deux fois le temps de génération du débogage). Il s’agit de la valeur par défaut pour les builds de configuration **Release** .
- L’inscription dynamique diffère ce travail jusqu’à ce que l’application démarre et ignore la génération de code, mais ce travail supplémentaire peut créer une pause visible (au moins deux secondes) au lancement de l’application. Cela est particulièrement visible dans les builds de configuration de débogage, qui sont par défaut l’inscription dynamique et dont la réflexion est plus lente.

L’inscription statique partielle, introduite pour la première fois dans Xamarin. iOS 8,13, offre au développeur le meilleur des deux options. En précalculant les informations d’enregistrement de chaque élément `Xamarin.Mac.dll` dans et en expédiant ces informations avec Xamarin. Mac dans une bibliothèque statique (qui ne doit être liée qu’au moment de la génération), Microsoft a supprimé la majeure partie du temps de réflexion de la dynamique. Bureau d’enregistrement sans impact sur le temps de génération.

### <a name="enabling-the-partial-static-registrar"></a>Activation du Registre statique partiel

Le Bureau d’enregistrement statique partiel est activé dans Xamarin. Mac en double-cliquant sur le **nom du projet** dans le **Explorateur de solutions**, en accédant `--registrar:static` à la **Build Mac** et en ajoutant au champ **MMP arguments supplémentaires :** . Par exemple :

![Ajout du Bureau d’enregistrement statique partiel aux arguments MMP supplémentaires](how-it-works-images/psr01.png "Ajout du Bureau d’enregistrement statique partiel aux arguments MMP supplémentaires")

## <a name="additional-resources"></a>Ressources supplémentaires

Voici des explications plus détaillées sur la façon dont les choses fonctionnent en interne :

- [Sélecteurs objective-C](~/ios/internals/objective-c-selectors.md)
- [Inscription](~/ios/internals/registrar.md)
- [API unifiée Xamarin pour iOS et OS X](~/cross-platform/macios/unified/index.md)
- [Notions de base de Theading](~/ios/app-fundamentals/threading.md)
- [Délégués, protocoles et événements](~/ios/app-fundamentals/delegates-protocols-and-events.md)
- [Obtenir`newrefcount`](~/ios/internals/newrefcount.md)
