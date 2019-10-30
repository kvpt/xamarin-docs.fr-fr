---
title: Optimisations de build
description: Ce document explique les différentes optimisations appliquées au moment de la génération pour les applications Xamarin. iOS et Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 84B67E31-B217-443D-89E5-CFE1923CB14E
author: davidortinau
ms.author: daortin
ms.date: 04/16/2018
ms.openlocfilehash: 22028743742a618bd7347d5e49153defecd4e3bb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015167"
---
# <a name="build-optimizations"></a>Optimisations de build

Ce document explique les différentes optimisations appliquées au moment de la génération pour les applications Xamarin. iOS et Xamarin. Mac.

## <a name="remove-uiapplicationensureuithread--nsapplicationensureuithread"></a>Supprimer UIApplication. EnsureUIThread/NSApplication. EnsureUIThread

Supprime les appels à [UIApplication. EnsureUIThread][1] (pour Xamarin. IOS) ou `NSApplication.EnsureUIThread` (pour Xamarin. Mac).

Cette optimisation modifie le type de code suivant :

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    global::UIKit.UIApplication.EnsureUIThread ();
    // ...
}
```

dans ce qui suit :

```csharp
public virtual void AddChildViewController (UIViewController childController)
{
    // ...
}
```

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Par défaut, il est activé pour les versions release.

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]remove-uithread-checks` à mTouch/MMP.

[1]: https://docs.microsoft.com/dotnet/api/UIKit.UIApplication.EnsureUIThread

## <a name="inline-intptrsize"></a>IntPtr. taille Inline

Incorpore la valeur constante de `IntPtr.Size` en fonction de la plateforme cible.

Cette optimisation modifie le type de code suivant :

```csharp
if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

dans les éléments suivants (lors de la génération d’une plateforme 64 bits) :

```csharp
if (8 == 8) {
    Console.WriteLine ("64-bit platform");
} else {
    Console.WriteLine ("32-bit platform");
}
```

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Par défaut, il est activé si vous ciblez une seule architecture ou pour l’assembly de plateforme (**Xamarin. iOS. dll**, **Xamarin. TVOS. dll**, **Xamarin. Watchos. dll** ou **Xamarin. Mac. dll**).

Si vous ciblez plusieurs architectures, cette optimisation crée des assemblys différents pour la version 32 bits et la version 64 bits de l’application, et les deux versions doivent être incluses dans l’application, ce qui a pour effet d’améliorer la taille finale de l’application au lieu de diminuer tel.

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]inline-intptr-size` à mTouch/MMP.

## <a name="inline-nsobjectisdirectbinding"></a>Inline NSObject. IsDirectBinding

`NSObject.IsDirectBinding` est une propriété d’instance qui détermine si une instance particulière est d’un type de wrapper ou non (un type de wrapper est un type managé qui est mappé à un type natif ; par exemple, le type de `UIKit.UIView` managé est mappé au type de `UIView` natif-l’inverse est un type d’utilisateur , dans ce cas `class MyUIView : UIKit.UIView` serait un type d’utilisateur).

Il est nécessaire de connaître la valeur de `IsDirectBinding` lors de l’appel à Objective-C, car la valeur détermine la version de `objc_msgSend` à utiliser.

Fournissez uniquement le code suivant :

```csharp
class UIView : NSObject {
    public virtual string SomeProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class NSUrl : NSObject {
    public virtual string SomeOtherProperty {
        get {
            if (IsDirectBinding) {
                return "true";
            } else {
                return "false"
            }
        }
    }
}

class MyUIView : UIView {
}
```

Nous pouvons déterminer que dans `UIView.SomeProperty` la valeur de `IsDirectBinding` n’est pas une constante et ne peut pas être inline :

```csharp
void uiView = new UIView ();
Console.WriteLine (uiView.SomeProperty); /* prints 'true' */
void myView = new MyUIView ();
Console.WriteLine (myView.SomeProperty); // prints 'false'
```

Toutefois, il est possible d’examiner tous les types de l’application et de déterminer qu’il n’existe pas de types qui héritent de `NSUrl`, et il est donc possible d’incorporer en toute sécurité la valeur de `IsDirectBinding` à une `true`constante :

```csharp
void myURL = new NSUrl ();
Console.WriteLine (myURL.SomeOtherProperty); // prints 'true'
// There's no way to make SomeOtherProperty print anything but 'true', since there are no NSUrl subclasses.
```

En particulier, cette optimisation modifie le type de code suivant (il s’agit du code de liaison pour `NSUrl.AbsoluteUrl`) :

```csharp
if (IsDirectBinding) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

dans les éléments suivants (lorsqu’il est possible de déterminer qu’il n’existe aucune sous-classe de `NSUrl` dans l’application) :

```csharp
if (true) {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSend (this.Handle, Selector.GetHandle ("absoluteURL")));
} else {
    return Runtime.GetNSObject<NSUrl> (global::ObjCRuntime.Messaging.IntPtr_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("absoluteURL")));
}
```

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Elle est toujours activée par défaut pour Xamarin. iOS et toujours désactivée par défaut pour Xamarin. Mac (étant donné qu’il est possible de charger dynamiquement des assemblys dans Xamarin. Mac, il n’est pas possible de déterminer qu’une classe particulière n’est jamais sous-classée).

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]inline-isdirectbinding` à mTouch/MMP.

## <a name="inline-runtimearch"></a>Runtime Inline. Arch

Cette optimisation modifie le type de code suivant :

```csharp
if (Runtime.Arch == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

dans les éléments suivants (lors de la génération pour l’appareil) :

```csharp
if (Arch.DEVICE == Arch.DEVICE) {
    Console.WriteLine ("Running on device");
} else {
    Console.WriteLine ("Running in the simulator");
}
```

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Elle est toujours activée par défaut pour Xamarin. iOS (elle n’est pas disponible pour Xamarin. Mac).

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]inline-runtime-arch` à mTouch.

## <a name="dead-code-elimination"></a>Élimination du code mort

Cette optimisation modifie le type de code suivant :

```csharp
if (true) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

dans

```csharp
Console.WriteLine ("Doing this");
```

Il évalue également les comparaisons constantes, comme suit :

```csharp
if (8 == 8) {
    Console.WriteLine ("Doing this");
} else {
    Console.WriteLine ("Not doing this");
}
```

et déterminent que l’expression `8 == 8` est toujours true et la réduit à :

```csharp
Console.WriteLine ("Doing this");
```

Il s’agit d’une optimisation puissante lorsqu’elle est utilisée avec les optimisations d’incorporation, car elle peut transformer le type de code suivant (il s’agit du code de liaison pour `NFCIso15693ReadMultipleBlocksConfiguration.Range`) :

```csharp
NSRange ret;
if (IsDirectBinding) {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret (out ret, this.Handle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
    }
} else {
    if (Runtime.Arch == Arch.DEVICE) {
        if (IntPtr.Size == 8) {
            ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
        } else {
            global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret (out ret, this.SuperHandle, Selector.GetHandle ("range"));
        }
    } else if (IntPtr.Size == 8) {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    } else {
        ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("range"));
    }
}
return ret;
```

dans cela (lors de la génération pour un appareil 64 bits, et lorsqu’il est également possible de s’assurer qu’il n’existe pas de sous-classes `NFCIso15693ReadMultipleBlocksConfiguration` dans l’application) :

```csharp
NSRange ret;
ret = global::ObjCRuntime.Messaging.NSRange_objc_msgSend (this.Handle, Selector.GetHandle ("range"));
return ret;
```

Le compilateur AOA est déjà en mesure d’éliminer le code mort comme celui-ci, mais cette optimisation est effectuée à l’intérieur de l’éditeur de liens, ce qui signifie que l’éditeur de liens peut voir qu’il existe plusieurs méthodes qui ne sont plus utilisées et peuvent donc être supprimées (sauf si elles sont utilisées ailleurs). :

* `global::ObjCRuntime.Messaging.NSRange_objc_msgSend_stret`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper`
* `global::ObjCRuntime.Messaging.NSRange_objc_msgSendSuper_stret`

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Il est toujours activé par défaut (lorsque l’éditeur de liens est activé).

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]dead-code-elimination` à mTouch/MMP.

## <a name="optimize-calls-to-blockliteralsetupblock"></a>Optimiser les appels à BlockLiteral. SetupBlock

Le runtime Xamarin. iOS/Mac doit connaître la signature de bloc lors de la création d’un bloc objective-C pour un délégué managé. Cela peut être une opération relativement coûteuse. Cette optimisation calcule la signature de bloc au moment de la génération et modifie l’IL pour appeler une méthode `SetupBlock` qui prend la signature comme argument à la place. Cela vous évite d’avoir à calculer la signature au moment de l’exécution.

Les bancs d’essai montrent que cela accélère l’appel d’un bloc d’un facteur de 10 à 15.

Il transforme le [code](https://github.com/xamarin/xamarin-macios/blob/018f7153441d9d7e0f58e2046f39eeb46f1ff480/src/UIKit/UIAccessibility.cs#L198-L211)suivant :

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlock (callback, completionHandler);
    // ...
}
```

dans

```csharp
public static void RequestGuidedAccessSession (bool enable, Action<bool> completionHandler)
{
    // ...
    block_handler.SetupBlockImpl (callback, completionHandler, true, "v@?B");
    // ...
}
```

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Elle est activée par défaut lors de l’utilisation du Bureau d’enregistrement statique (dans Xamarin. iOS, le Bureau d’enregistrement statique est activé par défaut pour les builds d’appareils, et dans Xamarin. Mac, le Bureau d’enregistrement statique est activé par défaut pour les versions release).

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]blockliteral-setupblock` à mTouch/MMP.

## <a name="optimize-support-for-protocols"></a>Optimiser la prise en charge des protocoles

Le runtime Xamarin. iOS/Mac a besoin d’informations sur la manière dont les types managés implémentent les protocoles objective-C. Ces informations sont stockées dans des interfaces (et des attributs sur ces interfaces), qui n’est pas un format très efficace, et ne sont pas compatibles avec l’éditeur de liens.

Par exemple, ces interfaces stockent des informations sur tous les membres de protocole dans un attribut `[ProtocolMember]`, qui, entre autres choses, contiennent des références aux types de paramètres de ces membres. Cela signifie qu’en implémentant simplement une telle interface, l’éditeur de liens doit conserver tous les types utilisés dans cette interface, même pour les membres facultatifs, l’application n’appelle jamais ou n’implémente jamais.

Cette optimisation fera en sorte que le Bureau d’enregistrement statique enregistre toutes les informations requises dans un format efficace qui utilise peu de mémoire, ce qui est facile et rapide à trouver lors de l’exécution.

Il apprend également à l’éditeur de liens qu’il n’a pas nécessairement besoin de conserver ces interfaces, ni aucun des attributs associés.

Cette optimisation nécessite l’activation de l’éditeur de liens et du Bureau d’enregistrement statique.

Sur Xamarin. iOS, cette optimisation est activée par défaut lorsque l’éditeur de liens et le Bureau d’enregistrement statique sont activés.

Sur Xamarin. Mac, cette optimisation n’est jamais activée par défaut, car Xamarin. Mac prend en charge le chargement dynamique d’assemblys, et ces assemblys peuvent ne pas avoir été connus au moment de la génération (et donc non optimisés).

Le comportement par défaut peut être substitué en passant `--optimize=-register-protocols` à mTouch/MMP.

## <a name="remove-the-dynamic-registrar"></a>Supprimer le Bureau d’enregistrement dynamique

Le runtime Xamarin. iOS et Xamarin. Mac incluent la prise en charge de l' [inscription des types managés](~/ios/internals/registrar.md) avec le runtime objective-C. Elle peut être effectuée au moment de la génération ou au moment de l’exécution (ou partiellement au moment de la génération et du reste au moment de l’exécution), mais si elle est complètement effectuée au moment de la génération, cela signifie que le code de prise en charge pour le faire au moment de l’exécution peut être supprimé. Cela entraîne une diminution significative de la taille de l’application, en particulier pour les petites applications telles que les extensions ou les applications Watchos.

Cette optimisation nécessite l’activation des bureaux d’enregistrement statiques et de l’éditeur de liens.

L’éditeur de liens tente de déterminer s’il est possible de supprimer le Bureau d’enregistrement dynamique, et si tel est le cas, il essaie de le supprimer.

Étant donné que Xamarin. Mac prend en charge le chargement dynamique des assemblys au moment de l’exécution (qui n’étaient pas connus au moment de la génération), il est impossible de déterminer au moment de la génération s’il s’agit d’une optimisation sécurisée. Cela signifie que cette optimisation n’est jamais activée par défaut pour les applications Xamarin. Mac.

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]remove-dynamic-registrar` à mTouch/MMP.

Si la valeur par défaut est substituée pour supprimer le Bureau d’enregistrement dynamique, l’éditeur de liens émettra des avertissements s’il détecte qu’il n’est pas sûr (mais que le Bureau d’enregistrement dynamique sera toujours supprimé).

## <a name="inline-runtimedynamicregistrationsupported"></a>Runtime Inline. DynamicRegistrationSupported

Inserte la valeur de `Runtime.DynamicRegistrationSupported` telle qu’elle est déterminée au moment de la génération.

Si le Bureau d’enregistrement dynamique est supprimé (voir [Supprimer l’optimisation du greffier dynamique](#remove-the-dynamic-registrar) ), il s’agit d’une valeur constante `false`, sinon il s’agit d’une valeur de `true` constante.

Cette optimisation modifie le type de code suivant :

```csharp
if (Runtime.DynamicRegistrationSupported) {
    Console.WriteLine ("do something");
} else {
    throw new Exception ("dynamic registration is not supported");
}
```

dans les éléments suivants lorsque le Bureau d’enregistrement dynamique est supprimé :

```csharp
throw new Exception ("dynamic registration is not supported");
```

dans ce qui suit lorsque le Bureau d’enregistrement dynamique n’est pas supprimé :

```csharp
Console.WriteLine ("do something");
```

Cette optimisation nécessite l’activation de l’éditeur de liens et s’applique uniquement aux méthodes avec l’attribut `[BindingImpl (BindingImplOptions.Optimizable)]`.

Il est toujours activé par défaut (lorsque l’éditeur de liens est activé).

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]inline-dynamic-registration-supported` à mTouch/MMP.

## <a name="precompute-methods-to-create-managed-delegates-for-objective-c-blocks"></a>Méthodes precompute pour créer des délégués managés pour les blocs objective-C

Quand objective-C appelle un sélecteur qui prend un bloc comme paramètre, et que le code managé a remplacé cette méthode, le runtime Xamarin. iOS/Xamarin. Mac doit créer un délégué pour ce bloc.

Le code de liaison généré par le générateur de liaisons inclura un attribut `[BlockProxy]`, qui spécifie le type avec une méthode `Create` qui peut le faire.

Compte tenu du code Objective-C suivant :

```objc
@interface ObjCBlockTester : NSObject {
}
-(void) classCallback: (void (^)())completionHandler;
-(void) callClassCallback;
@end

@implementation ObjCBlockTester
-(void) classCallback: (void (^)())completionHandler
{
}

-(void) callClassCallback
{
    [self classCallback: ^()
    {
        NSLog (@"called!");
    }];
}
@end
```

et le code de liaison suivant :

```csharp
[BaseType (typeof (NSObject))]
interface ObjCBlockTester
{
    [Export ("classCallback:")]
    void ClassCallback (Action completionHandler);
}
```

le générateur produira les éléments suivants :

```csharp
[Register("ObjCBlockTester", true)]
public unsafe partial class ObjCBlockTester : NSObject {
    // unrelated code...

    [Export ("callClassCallback")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public virtual void CallClassCallback ()
    {
        if (IsDirectBinding) {
            ApiDefinition.Messaging.void_objc_msgSend (this.Handle, Selector.GetHandle ("callClassCallback"));
        } else {
            ApiDefinition.Messaging.void_objc_msgSendSuper (this.SuperHandle, Selector.GetHandle ("callClassCallback"));
        }
    }

    [Export ("classCallback:")]
    [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
    public unsafe virtual void ClassCallback ([BlockProxy (typeof (Trampolines.NIDActionArity1V0))] System.Action completionHandler)
    {
        // ...

    }
}

static class Trampolines
{
    [UnmanagedFunctionPointerAttribute (CallingConvention.Cdecl)]
    [UserDelegateType (typeof (System.Action))]
    internal delegate void DActionArity1V0 (IntPtr block);

    static internal class SDActionArity1V0 {
        static internal readonly DActionArity1V0 Handler = Invoke;

        [MonoPInvokeCallback (typeof (DActionArity1V0))]
        static unsafe void Invoke (IntPtr block) {
            var descriptor = (BlockLiteral *) block;
            var del = (System.Action) (descriptor->Target);
            if (del != null)
                del (obj);
        }
    }

    internal class NIDActionArity1V0 {
        IntPtr blockPtr;
        DActionArity1V0 invoker;

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe NIDActionArity1V0 (BlockLiteral *block)
        {
            blockPtr = _Block_copy ((IntPtr) block);
            invoker = block->GetDelegateForBlock<DActionArity1V0> ();
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        ~NIDActionArity1V0 ()
        {
            _Block_release (blockPtr);
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        public unsafe static System.Action Create (IntPtr block)
        {
            if (block == IntPtr.Zero)
                return null;
            if (BlockLiteral.IsManagedBlock (block)) {
                var existing_delegate = ((BlockLiteral *) block)->Target as System.Action;
                if (existing_delegate != null)
                    return existing_delegate;
            }
            return new NIDActionArity1V0 ((BlockLiteral *) block).Invoke;
        }

        [Preserve (Conditional=true)]
        [BindingImpl (BindingImplOptions.GeneratedCode | BindingImplOptions.Optimizable)]
        unsafe void Invoke ()
        {
            invoker (blockPtr);
        }
    }
}
```

Quand objective-C appelle `[ObjCBlockTester callClassCallback]`, le runtime Xamarin. iOS/Xamarin. Mac examine l’attribut `[BlockProxy (typeof (Trampolines.NIDActionArity1V0))]` sur le paramètre. Il recherche ensuite la méthode `Create` sur ce type et appelle cette méthode pour créer le délégué.

Cette optimisation trouvera la méthode `Create` au moment de la génération, et le Bureau d’enregistrement statique générera du code qui recherche la méthode au moment de l’exécution à l’aide des jetons de métadonnées à la place à l’aide de l’attribut et de la réflexion (cette opération est beaucoup plus rapide et permet également à l’éditeur de liens de Supprimez le code d’exécution correspondant, ce qui réduit la taille de l’application.

Si MMP/mTouch ne parvient pas à trouver la méthode `Create`, un avertissement MT4174/MM4174 s’affiche, et la recherche est effectuée au moment de l’exécution.
La cause la plus probable est l’écriture manuelle du code de liaison sans les attributs `[BlockProxy]` requis.

Cette optimisation nécessite l’activation du Bureau d’enregistrement statique.

Il est toujours activé par défaut (à condition que le Bureau d’enregistrement statique soit activé).

Le comportement par défaut peut être substitué en passant `--optimize=[+|-]static-delegate-to-block-lookup` à mTouch/MMP.
