---
title: Sous-classes génériques NSObject dans Xamarin.iOS
description: Ce document décrit comment créer des sous-classes génériques NSObject de créer. Il examine ce qui peut et ne peut pas être effectué, décrit le bureau d’enregistrement statique et examine les performances.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: becdd842f46cc7100bd7d9a6fd7347b541685c35
ms.sourcegitcommit: 85c45dc28ab3625321c271804768d8e4fce62faf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67039634"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sous-classes génériques NSObject dans Xamarin.iOS

## <a name="using-generics-with-nsobjects"></a>L’utilisation de génériques avec NSObjects

Depuis Xamarin.iOS 7.2.1, vous pouvez utiliser des génériques dans les sous-classes de `NSObject` (par exemple [UIView](xref:UIKit.UIView).

Vous pouvez désormais créer des classes génériques, comme celle-ci :

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Objets depuis cette sous-classe `NSObject` sont enregistrés avec le runtime Objective-C, il existe certaines limitations quant à ce qui est possible avec des sous-classes génériques de `NSObject` types.
    
## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considérations pour les sous-classes génériques NSObject

Ce document décrit en détail les limitations de la prise en charge limitée pour les sous-classes génériques de `NSObjects` introduite avec Xamarin.iOS 7.2.1.
    
### <a name="generic-type-arguments-in-member-signatures"></a>Arguments de Type générique dans les Signatures de membre

Tous les arguments de type générique dans une signature de membre exposé à Objective-C doivent avoir un `NSObject` contrainte.

**Bonne**:

```csharp
class Generic<T> : NSObject where T: NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Raison**: Le paramètre de type générique est un `NSObject`, de sorte que la signature de sélecteur pour `myMethod:` peut être exposé en toute sécurité à Objective-C (il sera toujours `NSObject` ou une sous-classe de celui-ci).

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

**Raison**: il n’est pas possible de créer une signature Objective-C pour les membres exportés code Objective-C peut appeler, étant donné que la signature est diffèrent selon le type exact du type générique `T`.

**Bonne**:

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

**Raison**: il est possible d’avoir des arguments de type générique sans contrainte tant qu’ils ne font pas partie de la signature de membre exporté.

**Bonne**:

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

**Raison**: le `T` exporté de paramètre dans le Objective-C `MyMethod` est contraint à être un `NSObject`, le type sans contrainte `U` ne fait pas partie de la signature.
    
### <a name="instantiations-of-generic-types-from-objective-c"></a>Instanciations de Types génériques à partir d’Objective-C

Instanciation des types génériques à partir d’Objective-C n’est pas autorisée. Cela se produit généralement lorsqu’un type managé est utilisé dans un xib.

Examinez cette définition de classe qui expose un constructeur qui accepte un `IntPtr` (la façon de Xamarin.iOS de construction d’un C# objet à partir d’une instance de Objective-C native) :
    
```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Pendant la construction ci-dessus est parfaite, lors de l’exécution, cela lève une exception au si Objective-C tente de créer une instance de celle-ci.

Il s’agit en effet, Objective-C n’a aucun concept des types génériques, et il ne peut pas spécifier le type générique exact à créer.

Ce problème peut être contourné en créant une sous-classe spécialisée du type générique.   Exemple :
    
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

Maintenant il n’existe aucune ambiguïté de plus, la classe `GenericUIView` peut être utilisé dans des XIB.

## <a name="no-support-for-generic-methods"></a>Aucune prise en charge pour les méthodes génériques

### <a name="generic-methods-are-not-allowed"></a>Méthodes génériques ne sont pas autorisés.

Le code suivant n’est pas compilé :

```csharp
class MyClass : NSObject
{
    [Export ("myMethod")]
    public void MyMethod<T> (T argument)
    {
    }
}
```

**Raison**: Cela n’est pas autorisé parce que Xamarin.iOS ne connaît pas le type à utiliser pour l’argument de type `T` lorsque la méthode est appelée à partir d’Objective-C.

Une alternative consiste à créer une méthode spécialisée et à la place de l’exporter :

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

Vous ne pouvez pas exposer des membres statiques et Objective-C si elle est hébergée à l’intérieur d’une sous-classe générique de `NSObject`.

Exemple d’un scénario non pris en charge :

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

**Raison :** Tout comme les méthodes génériques, le runtime Xamarin.iOS doit être en mesure de connaître le type à utiliser pour l’argument de type générique T.

Par exemple les membres que l’instance elle-même est utilisé (dans la mesure où il y aura jamais d’une instance générique<T>, il sera toujours générique<SomeSpecificClass>), mais pour les membres statiques ne sont pas présents.

Notez que cela s’applique même si le membre en question n’utilise pas l’argument de type T en aucune façon.

L’alternative est dans ce cas consiste à créer une sous-classe spécialisée :

```csharp
class GenericUIView : Generic<UIView>
{
    [Export ("myUIViewMethod")]
    public static void MyUIViewMethod ()
    {
        MyMethod ();
    }

    [Export ("myProperty")]
    public static UIView MyUIVIewProperty {
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

### <a name="requires-new-static-registrar"></a>Requiert les nouveau bureau d’enregistrement statique

La prise en charge des génériques nécessite la nouvelle [système d’inscription](~/ios/internals/registrar.md).

Si vous essayez d’utiliser l’ancien système d’inscription hérité affichage d’avertissements quand il rencontre des types génériques (en outre ne pas générer de code correct, ce qui entraîne un comportement non défini).
    
## <a name="performance"></a>Performances

Le bureau d’enregistrement statique ne peut pas résoudre un membre exporté dans un type générique au moment de la génération, comme c’est généralement le cas, elles doivent être recherchés lors de l’exécution. Cela signifie que l’appel d’une telle méthode à partir d’Objective-C est légèrement plus lente que l’appel de membres à partir de classes non génériques.

