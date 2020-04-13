---
title: Sous-classes génériques de NSObject dans Xamarin.iOS
description: Ce document décrit comment créer des sous-classes génériques de NSObject. Il examine ce qui peut et ne peut pas être fait, discute du registraire statique et jette un coup d’œil à la performance.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 279fcac1611038613bf442e1b766fda45dd5a429
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73022366"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sous-classes génériques de NSObject dans Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>Utilisation de génériques avec NSObjects

Il est possible d’utiliser des génériques dans des sous-classes de `NSObject`, par exemple [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Étant donné que `NSObject` les objets qui sous-classe sont enregistrés avec l’exécution Objective-C `NSObject` il ya certaines limites quant à ce qui est possible avec des sous-classes génériques de types.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considérations pour les sous-classes génériques de NSObject

Ce document détaille les limites dans le `NSObjects`support limité pour les sous-classes génériques de .

### <a name="generic-type-arguments-in-member-signatures"></a>Arguments de type génériques dans les signatures des membres

Tous les arguments de type générique dans une `NSObject` signature de membre exposés à Objective-C doivent avoir une contrainte.

**Bon**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motif**: Le paramètre `NSObject`de type générique `myMethod:` est un , de sorte que la `NSObject` signature du sélecteur pour peut être en toute sécurité exposée à l’Objectif-C (il sera toujours ou une sous-classe de celui-ci).

**Mauvais**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Motif**: il n’est pas possible de créer une signature Objective-C pour les membres exportés que le `T`code Objectif-C peut appeler, puisque la signature différerait selon le type exact du type générique.

**Bon**:

```csharp
class Generic<T> : NSObject
{
    T storage;

    [Export ("myMethod:")]
    public void MyMethod (NSObject value)
    {
    }
}
```

**Motif**: il est possible d’avoir des arguments de type générique sans contrainte tant qu’ils ne prennent pas part à la signature du membre exporté.

**Bon**:

```csharp
class Generic<T, U> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
        Console.WriteLine (typeof (U));
    }
}
```

**Motif**: `T` le paramètre de `MyMethod` l’Objectif-C `NSObject`exporté est contraint `U` d’être un , le type sans contrainte ne fait pas partie de la signature.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Instantiations de types génériques de l’objectif-C

L’instantanéisation des types génériques de l’Objectif-C n’est pas autorisée. Cela se produit généralement quand un type géré est utilisé dans un xib ou un storyboard.

Considérez cette définition de classe, qui `IntPtr` expose un constructeur qui prend une (la façon Xamarin.iOS de construire un objet C ' à partir d’une instance objective-C indigène) :

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Bien que la construction ci-dessus est très bien, à l’heure de pointe, cela jettera une exception à si Objectif-C tente de créer un exemple de celui-ci.

Cela se produit parce que l’objectif C n’a pas de concept de types génériques, et il ne peut pas spécifier le type générique exact à créer.

Ce problème peut être travaillé autour en créant une sous-classe spécialisée du type générique. Par exemple :

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}

class GenericUIView : Generic<UIView>
{
}
```

Maintenant, il n’y a `GenericUIView` plus d’ambiguïté, la classe peut être utilisée dans des xibs ou des storyboards.

## <a name="no-support-for-generic-methods"></a>Aucun support pour les méthodes génériques

### <a name="generic-methods-are-not-allowed"></a>Les méthodes génériques ne sont pas autorisées.

Le code suivant ne compilera pas :

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Motif**: Cela n’est pas permis parce que Xamarin.iOS `T` ne sait pas quel type utiliser pour l’argument de type lorsque la méthode est invoquée à partir d’Objectif-C .

Une alternative consiste à créer une méthode spécialisée et à exporter qui:

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyUIViewMethod (UIView argument)
    {
        MyMethod<UIView> (argument);
    }
    public void MyMethod<T> (T argument)
    {
    }
}
```

### <a name="no-exported-static-members-allowed"></a>Aucun membre statique exporté autorisé

Vous ne pouvez pas exposer un membre statique à Objective-C `NSObject`si elle est hébergée à l’intérieur d’une sous-classe générique de .

Exemple d’un scénario non pris en compte :

```csharp
class Generic<T> : NSObject where T : NSObject
{
    [Export ("myMethod:")]
    public static void MyMethod ()
    {
    }

    [Export ("myProperty")]
    public static T MyProperty { get; set; }
}
```

**Motif:** Tout comme les méthodes génériques, le temps d’exécution Xamarin.iOS doit `T`être en mesure de savoir quel type à utiliser pour l’argument de type générique .

Par exemple, les membres l’instance elle-même `Generic<T>`est utilisée `Generic<SomeSpecificClass>`(puisqu’il n’y aura jamais d’instance, elle le sera toujours), mais pour les membres statiques, cette information n’est pas présente.

Notez que cela s’applique même si le `T` membre en question n’utilise pas l’argument de type de quelque façon que ce soit.

L’alternative dans ce cas est de créer une sous-classe spécialisée:

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIViewProperty {
        get { return MyProperty; }
        set { MyProperty = value; }
    }
}

class Generic<T> : NSObject where T : NSObject
{
    public static void MyMethod () {}
    public static T MyProperty { get; set; }
}
```

## <a name="performance"></a>Performances

Le registraire statique ne peut pas résoudre un membre exporté dans un type générique au moment de la construction comme il le fait habituellement, il doit être consulté à l’heure d’exécution. Cela signifie que l’invocation d’une telle méthode de l’Objectif-C est légèrement plus lente que d’invoquer les membres de classes non génériques.
