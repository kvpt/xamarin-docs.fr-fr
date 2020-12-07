---
title: Regroupement d’exceptions dans Xamarin. iOS
description: Ce document décrit comment utiliser les exceptions natives et managées dans une application Xamarin. iOS. Il aborde les problèmes qui peuvent se produire et une solution à ces problèmes.
ms.prod: xamarin
ms.assetid: BE4EE969-C075-4B9A-8465-E393556D8D90
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/05/2017
ms.openlocfilehash: a54a0012f7b5ed3d147242e3ee02b2ed6fe890bf
ms.sourcegitcommit: 0a41c4aa6db72cd2d0cecbe0dc893024cecac71d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96749876"
---
# <a name="exception-marshaling-in-xamarinios"></a>Regroupement d’exceptions dans Xamarin. iOS

_Xamarin. iOS contient de nouveaux événements pour vous aider à répondre aux exceptions, en particulier dans le code natif._

Le code managé et objective-C prennent en charge les exceptions Runtime (clauses try/catch/finally).

Toutefois, leurs implémentations sont différentes, ce qui signifie que les bibliothèques Runtime (le runtime mono et les bibliothèques Runtime objective-C) rencontrent des problèmes lorsqu’ils doivent gérer des exceptions, puis exécuter du code écrit dans d’autres langages.

Ce document explique les problèmes qui peuvent se produire, ainsi que les solutions possibles.

Il comprend également un exemple de projet, le [marshaling des exceptions](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling), qui peut être utilisé pour tester différents scénarios et leurs solutions.

## <a name="problem"></a>Problème

Le problème se produit lorsqu’une exception est levée, et Pendant le déroulement de la pile, un frame qui ne correspond pas au type d’exception qui a été levée est rencontré.

Un exemple typique de cette opération pour Xamarin. iOS ou Xamarin. Mac est lorsqu’une API native lève une exception objective-C, puis cette exception objective-C doit être gérée lorsque le processus de déroulement de la pile atteint un frame managé.

L’action par défaut consiste à ne rien faire. Pour l’exemple ci-dessus, cela signifie que les frames managés du déroulement de l’exécution objective-C sont en effet. Cela pose problème, car le runtime objective-C ne sait pas comment dérouler les frames managés ; par exemple, il n’exécutera pas de `catch` `finally` clauses or dans ce frame.

### <a name="broken-code"></a>Code endommagé

Considérez l’exemple de code suivant :

``` csharp
var dict = new NSMutableDictionary ();
dict.LowlevelSetObject (IntPtr.Zero, IntPtr.Zero); 
```

Cela lèvera un NSInvalidArgumentException objective-C en code natif :

```
NSInvalidArgumentException *** setObjectForKey: key cannot be nil
```

Et la trace de la pile ressemblera à ce qui suit :

```
0   CoreFoundation          __exceptionPreprocess + 194
1   libobjc.A.dylib         objc_exception_throw + 52
2   CoreFoundation          -[__NSDictionaryM setObject:forKey:] + 1015
3   libobjc.A.dylib         objc_msgSend + 102
4   TestApp                 ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
5   TestApp                 Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr)
6   TestApp                 ExceptionMarshaling.Exceptions.ThrowObjectiveCException ()
```

Les frames 0-3 sont des frames natifs, et le déroulateur de pile dans le runtime objective-C _peut_ dérouler ces frames. En particulier, elle exécute toutes les clauses objective-C `@catch` ou `@finally` .

Toutefois, le dérouleur de pile objective-C ne peut _pas_ dérouler correctement les frames managés (frames 4-6), dans le fait que les frames seront déroulés, mais que la logique d’exception managée ne sera pas exécutée.

Cela signifie qu’il n’est généralement pas possible d’intercepter ces exceptions de la manière suivante :

```csharp
try {
    var dict = new NSMutableDictionary ();
    dict.LowLevelSetObject (IntPtr.Zero, IntPtr.Zero);
} catch (Exception ex) {
    Console.WriteLine (ex);
} finally {
    Console.WriteLine ("finally");
}
```

Cela est dû au fait que le dérouleur de pile objective-C ne connaît pas la clause managée `catch` , et aucune des `finally` clauses ne sera exécutée.

Lorsque l’exemple de code ci-dessus _est_ efficace, c’est parce que objective-c a une méthode qui est notifiée des exceptions objective-c non gérées, [`NSSetUncaughtExceptionHandler`][2] que Xamarin. iOS et Xamarin. Mac utilisent, et à ce stade, tente de convertir les exceptions objective-c en exceptions managées.

## <a name="scenarios"></a>Scénarios

### <a name="scenario-1---catching-objective-c-exceptions-with-a-managed-catch-handler"></a>Scénario 1 : interception des exceptions objective-C avec un gestionnaire catch managé

Dans le scénario suivant, il est possible d’intercepter les exceptions objective-C à l’aide de gestionnaires managés `catch` :

1. Une exception objective-C est levée.
2. Le runtime objective-C parcourt la pile (mais ne la déroulera pas), en recherchant un `@catch` gestionnaire natif qui peut gérer l’exception.
3. Le runtime objective-C ne trouve pas de `@catch` gestionnaires, appelle `NSGetUncaughtExceptionHandler` et appelle le gestionnaire installé par Xamarin. iOS/Xamarin. Mac.
4. Le gestionnaire Xamarin. iOS/Xamarin. Mac convertit l’exception objective-C en une exception managée et la lève. Étant donné que le runtime objective-C n’a pas déroulé la pile (ne l’a pas fait), le frame actuel est le même que celui où l’exception objective-C a été levée.

Un autre problème se produit ici, car le runtime mono ne sait pas comment dérouler correctement les frames objective C.

Lorsque le rappel d’exception objective-C Xamarin. iOS est appelé, la pile se présente comme suit :

```
 0 libxamarin-debug.dylib   exception_handler(exc=name: "NSInvalidArgumentException" - reason: "**_ setObjectForKey: key cannot be nil")
 1 CoreFoundation           __handleUncaughtException + 809
 2 libobjc.A.dylib          _objc_terminate() + 100
 3 libc++abi.dylib          std::__terminate(void (_)()) + 14
 4 libc++abi.dylib          __cxa_throw + 122
 5 libobjc.A.dylib          objc_exception_throw + 337
 6 CoreFoundation           -[__NSDictionaryM setObject:forKey:] + 1015
 7 libxamarin-debug.dylib   xamarin_dyn_objc_msgSend + 102
 8 TestApp                  ObjCRuntime.Messaging.void_objc_msgSend_IntPtr_IntPtr (intptr,intptr,intptr,intptr)
 9 TestApp                  Foundation.NSMutableDictionary.LowlevelSetObject (intptr,intptr) [0x00000]
10 TestApp                  ExceptionMarshaling.Exceptions.ThrowObjectiveCException () [0x00013]
```

Ici, les seuls frames managés sont des trames 8-10, mais l’exception managée est levée dans le frame 0. Cela signifie que le runtime mono doit dérouler les frames natifs 0-7, ce qui provoque un problème équivalant au problème décrit ci-dessus : bien que le runtime mono déroulera les frames natifs, il n’exécutera pas les clauses objective-C `@catch` ou `@finally` .

Exemple de code :

```objc
-(id) setObject: (id) object forKey: (id) key
{
    @try {
        if (key == nil)
            [NSException raise: @"NSInvalidArgumentException"];
    } @finally {
        NSLog (@"This will not be executed");
    }
}
```

Et la `@finally` clause ne sera pas exécutée, car le runtime mono qui déroule ce frame ne le connaît pas.

Une variation de cela consiste à lever une exception managée dans le code managé, puis à effectuer un déroulement à travers des frames natifs pour obtenir la première clause managée `catch` :

```csharp
class AppDelegate : UIApplicationDelegate {
    public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
    {
        throw new Exception ("An exception");
    }
    static void Main (string [] args)
    {
        try {
            UIApplication.Main (args, null, typeof (AppDelegate));
        } catch (Exception ex) {
            Console.WriteLine ("Managed exception caught.");
        }
    }
}
```

La méthode managée `UIApplication:Main` appellera la `UIApplicationMain` méthode native, puis iOS fera beaucoup d’exécution du code natif avant d’appeler la méthode managée `AppDelegate:FinishedLaunching` , avec un grand nombre de frames natifs sur la pile lorsque l’exception managée est levée :

```
 0: TestApp                 ExceptionMarshaling.IOS.AppDelegate:FinishedLaunching (UIKit.UIApplication,Foundation.NSDictionary)
 1: TestApp                 (wrapper runtime-invoke) <Module>:runtime_invoke_bool__this___object_object (object,intptr,intptr,intptr) 
 2: libmonosgen-2.0.dylib   mono_jit_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 3: libmonosgen-2.0.dylib   do_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>, error=<unavailable>)
 4: libmonosgen-2.0.dylib   mono_runtime_invoke [inlined] mono_runtime_invoke_checked(method=<unavailable>, obj=<unavailable>, params=<unavailable>, error=0xbff45758)
 5: libmonosgen-2.0.dylib   mono_runtime_invoke(method=<unavailable>, obj=<unavailable>, params=<unavailable>, exc=<unavailable>)
 6: libxamarin-debug.dylib  xamarin_invoke_trampoline(type=<unavailable>, self=<unavailable>, sel="application:didFinishLaunchingWithOptions:", iterator=<unavailable>), context=<unavailable>)
 7: libxamarin-debug.dylib  xamarin_arch_trampoline(state=0xbff45ad4)
 8: libxamarin-debug.dylib  xamarin_i386_common_trampoline
 9: UIKit                   -[UIApplication _handleDelegateCallbacksWithOptions:isSuspended:restoreState:]
10: UIKit                   -[UIApplication _callInitializationDelegatesForMainScene:transitionContext:]
11: UIKit                   -[UIApplication _runWithMainScene:transitionContext:completion:]
12: UIKit                   __84-[UIApplication _handleApplicationActivationWithScene:transitionContext:completion:]_block_invoke.3124
13: UIKit                   -[UIApplication workspaceDidEndTransaction:]
14: FrontBoardServices      __37-[FBSWorkspace clientEndTransaction:]_block_invoke_2
15: FrontBoardServices      __40-[FBSWorkspace _performDelegateCallOut:]_block_invoke
16: FrontBoardServices      __FBSSERIALQUEUE_IS_CALLING_OUT_TO_A_BLOCK__
17: FrontBoardServices      -[FBSSerialQueue _performNext]
18: FrontBoardServices      -[FBSSerialQueue _performNextFromRunLoopSource]
19: FrontBoardServices      FBSSerialQueueRunLoopSourceHandler
20: CoreFoundation          __CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
21: CoreFoundation          __CFRunLoopDoSources0
22: CoreFoundation          __CFRunLoopRun
23: CoreFoundation          CFRunLoopRunSpecific
24: CoreFoundation          CFRunLoopRunInMode
25: UIKit                   -[UIApplication _run]
26: UIKit                   UIApplicationMain
27: TestApp                 (wrapper managed-to-native) UIKit.UIApplication:UIApplicationMain (int,string[],intptr,intptr)
28: TestApp                 UIKit.UIApplication:Main (string[],intptr,intptr)
29: TestApp                 UIKit.UIApplication:Main (string[],string,string)
30: TestApp                 ExceptionMarshaling.IOS.Application:Main (string[])
```

Les trames 0-1 et 27-30 sont gérées, alors que toutes les entre elles sont natives. Si mono se déroule à travers ces frames, aucune clause objective-C ou n’est `@catch` `@finally` exécutée.

### <a name="scenario-2---not-able-to-catch-objective-c-exceptions"></a>Scénario 2-ne peut pas intercepter les exceptions objective-C

Dans le scénario suivant, il n’est _pas_ possible d’intercepter les exceptions objective-c à l’aide de gestionnaires managés `catch` , car l’exception objective-c a été gérée d’une autre façon :

1. Une exception objective-C est levée.
2. Le runtime objective-C parcourt la pile (mais ne la déroulera pas), en recherchant un `@catch` gestionnaire natif qui peut gérer l’exception.
3. Le runtime objective-C trouve un `@catch` Gestionnaire, déroule la pile et démarre l’exécution du `@catch` Gestionnaire.

Ce scénario se trouve généralement dans les applications Xamarin. iOS, car sur le thread principal, il y a généralement un code similaire à celui-ci :

``` objective-c
void UIApplicationMain ()
{
    @try {
        while (true) {
            ExecuteRunLoop ();
        }
    } @catch (NSException *ex) {
        NSLog (@"An unhandled exception occured: %@", exc);
        abort ();
    }
}

```

Cela signifie que sur le thread principal, il n’y a jamais vraiment d’exception objective-C non gérée. par conséquent, notre rappel qui convertit les exceptions objective-C en exceptions managées n’est jamais appelé.

Cela est également assez courant lors du débogage d’applications Xamarin. Mac sur une version Mac OS antérieure à celle prise en charge par Xamarin. Mac, car l’inspection de la plupart des objets d’interface utilisateur dans le débogueur tente d’extraire des propriétés qui correspondent à des sélecteurs qui n’existent pas sur la plateforme en cours d’exécution (car Xamarin. L’appel de tels sélecteurs lèvera une `NSInvalidArgumentException` (« sélecteur non reconnu envoyé à... »), ce qui finit par provoquer le blocage du processus.

Pour résumer, le fait d’avoir le runtime objective-C ou les frames de déroulement d’exécution mono qu’ils ne sont pas programmés pour gérer peut entraîner des comportements non définis, tels que des incidents, des fuites de mémoire et d’autres types de comportements imprévisibles (mis).

## <a name="solution"></a>Solution

Dans Xamarin. iOS 10 et Xamarin. Mac 2,10, nous avons ajouté la prise en charge de l’interception des exceptions managées et objective-C sur n’importe quelle limite managée-native, et de la conversion de cette exception en l’autre type.

En pseudo-code, il ressemble à ceci :

``` csharp
[DllImport ("libobjc.dylib")]
static extern void objc_msgSend (IntPtr handle, IntPtr selector);

static void DoSomething (NSObject obj)
{
    objc_msgSend (obj.Handle, Selector.GetHandle ("doSomething"));
}
```

L’appel P/Invoke à objc_msgSend est intercepté, ce qui est appelé à la place :

``` objective-c
void
xamarin_dyn_objc_msgSend (id obj, SEL sel)
{
    @try {
        objc_msgSend (obj, sel);
    } @catch (NSException *ex) {
        convert_to_and_throw_managed_exception (ex);
    }
}
```

Et tout ce qui est similaire est effectué pour le cas inverse (marshaling d’exceptions managées à des exceptions objective-C).

L’interception des exceptions sur la limite Native managée n’est pas sans coût, donc il n’est pas toujours activé par défaut :

- Xamarin. iOS/tvOS : l’interception des exceptions objective-C est activée dans le simulateur.
- Xamarin. Watchos : l’interception est appliquée dans tous les cas, car laisser les frames managés de déroulement d’exécution objective-C confondra le garbage collector et le fera bloquer ou se bloquer.
- Xamarin. Mac : l’interception des exceptions objective-C est activée pour les versions Debug.

La section [indicateurs au moment](#build_time_flags) de la génération explique comment activer l’interception lorsqu’elle n’est pas activée par défaut.

## <a name="events"></a>Événements

Deux nouveaux événements sont déclenchés une fois qu’une exception est interceptée : `Runtime.MarshalManagedException` et `Runtime.MarshalObjectiveCException` .

Les deux événements sont passés `EventArgs` à un objet qui contient l’exception d’origine qui a été levée (la `Exception` propriété) et une `ExceptionMode` propriété pour définir la manière dont l’exception doit être marshalée.

La `ExceptionMode` propriété peut être modifiée dans le gestionnaire d’événements pour modifier le comportement en fonction de tout traitement personnalisé effectué dans le gestionnaire. Un exemple serait d’abandonner le processus si une certaine exception se produit.

La modification de la `ExceptionMode` propriété s’applique à l’événement unique, mais elle n’affecte pas les exceptions interceptées à l’avenir.

Les modes suivants sont disponibles :

- `Default`: La valeur par défaut varie selon la plateforme. C’est `ThrowObjectiveCException` le cas si le GC est en mode coopératif (Watchos) et dans le `UnwindNativeCode` cas contraire (iOS/Watchos/MacOS). La valeur par défaut peut changer à l’avenir.
- `UnwindNativeCode`: Il s’agit du comportement précédent (non défini). Cette option n’est pas disponible lors de l’utilisation du GC en mode coopératif (qui est la seule option sur Watchos. il ne s’agit donc pas d’une option valide sur Watchos), mais il s’agit de l’option par défaut pour toutes les autres plateformes.
- `ThrowObjectiveCException`: Convertissez l’exception managée en une exception objective-C et levez l’exception objective-C. Il s’agit de la valeur par défaut sur Watchos.
- `Abort`: Abandonner le processus.
- `Disable`: Désactive l’interception des exceptions. il n’est donc pas judicieux de définir cette valeur dans le gestionnaire d’événements, mais une fois que l’événement est déclenché, il est trop tard pour le désactiver. Dans tous les cas, s’il est défini, il se comporte comme `UnwindNativeCode` .

Pour le marshaling des exceptions objective-C vers du code managé, les modes suivants sont disponibles :

- `Default`: La valeur par défaut varie selon la plateforme. C’est `ThrowManagedException` le cas si le GC est en mode coopératif (Watchos) et dans le `UnwindManagedCode` cas contraire (iOS/TvOS/MacOS). La valeur par défaut peut changer à l’avenir.
- `UnwindManagedCode`: Il s’agit du comportement précédent (non défini). Cela n’est pas possible lors de l’utilisation du GC en mode coopératif (qui est le seul mode GC valide sur Watchos ; il ne s’agit donc pas d’une option valide sur Watchos), mais c’est la valeur par défaut pour toutes les autres plateformes.
- `ThrowManagedException`: Convertit l’exception objective-C en une exception managée et lève l’exception managée. Il s’agit de la valeur par défaut sur Watchos.
- `Abort`: Abandonner le processus.
- `Disable`:D isables l’interception des exceptions, il n’est pas judicieux de définir cette valeur dans le gestionnaire d’événements, mais une fois que l’événement est déclenché, il est trop tard pour le désactiver. Dans tous les cas, s’il est défini, le processus est abandonné.

Ainsi, pour voir chaque fois qu’une exception est marshalée, vous pouvez effectuer cette opération :

``` csharp
Runtime.MarshalManagedException += (object sender, MarshalManagedExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling managed exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
    
};
Runtime.MarshalObjectiveCException += (object sender, MarshalObjectiveCExceptionEventArgs args) =>
{
    Console.WriteLine ("Marshaling Objective-C exception");
    Console.WriteLine ("    Exception: {0}", args.Exception);
    Console.WriteLine ("    Mode: {0}", args.ExceptionMode);
};
```

<a name="build_time_flags"></a>

## <a name="build-time-flags"></a>Indicateurs de Build-Time

Il est possible de passer les options suivantes à **mTouch** (pour les applications Xamarin. IOS) et **MMP** (pour les applications Xamarin. Mac), qui déterminent si l’interception des exceptions est activée et définit l’action par défaut qui doit se produire :

- `--marshal-managed-exceptions=`
  - `default`
  - `unwindnativecode`
  - `throwobjectivecexception`
  - `abort`
  - `disable`

- `--marshal-objectivec-exceptions=`
  - `default`
  - `unwindmanagedcode`
  - `throwmanagedexception`
  - `abort`
  - `disable`

À l’exception de `disable` , ces valeurs sont identiques aux `ExceptionMode` valeurs transmises aux `MarshalManagedException` `MarshalObjectiveCException` événements et.

L' `disable` option désactivera _principalement_ l’interception, sauf que nous intercepterons toujours les exceptions quand elles n’ajouteront pas de surcharge d’exécution. Les événements de marshaling sont toujours déclenchés pour ces exceptions, avec le mode par défaut étant le mode par défaut pour la plateforme en cours d’exécution.

## <a name="limitations"></a>Limitations

Nous interceptons uniquement les appels P/Invoke à la `objc_msgSend` famille de fonctions quand vous tentez d’intercepter des exceptions objective-C. Cela signifie qu’un appel P/Invoke à une autre fonction C, qui lève ensuite des exceptions objective-C, s’exécutera toujours dans le comportement ancien et non défini (cela peut être amélioré à l’avenir).

[2]: https://developer.apple.com/reference/foundation/1409609-nssetuncaughtexceptionhandler?language=objc

## <a name="related-links"></a>Liens associés

- [Marshaling d’exceptions (exemple)](https://github.com/xamarin/mac-ios-samples/tree/master/ExceptionMarshaling)
