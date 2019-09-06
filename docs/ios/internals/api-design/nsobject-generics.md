---
title: Sous-classes génériques de NSObject dans Xamarin. iOS
description: Ce document décrit comment créer des sous-classes génériques de NSObject. Il examine ce qui peut et ne peut pas être fait, discute du Bureau d’enregistrement statique et examine les performances.
ms.prod: xamarin
ms.assetid: BB99EBD7-308A-C865-1829-4DFFDB1BBCA4
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/21/2017
ms.openlocfilehash: 531d0b2b6141cc0e1f4014f1d3422af3c6f8643a
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291976"
---
# <a name="generic-subclasses-of-nsobject-in-xamarinios"></a>Sous-classes génériques de NSObject dans Xamarin. iOS

## <a name="using-generics-with-nsobjects"></a>Utilisation de génériques avec NSObjects

Il est possible d’utiliser des génériques dans les sous-classes `NSObject`de, par exemple [UIView](xref:UIKit.UIView):

```csharp
class Foo<T> : UIView {
    public Foo (CGRect x) : base (x) {}
    public override void Draw (CoreGraphics.CGRect rect)
    {
        Console.WriteLine ("T: {0}. Type: {1}", typeof (T), GetType ().Name);
    }
}
```

Étant donné que les objets `NSObject` de la sous-classe sont enregistrés avec le runtime objective-C, il existe certaines limitations quant à ce qui `NSObject` est possible avec les sous-classes génériques des types.

## <a name="considerations-for-generic-subclasses-of-nsobject"></a>Considérations relatives aux sous-classes génériques de NSObject

Ce document décrit en détail les limitations de la prise en charge limitée des sous `NSObjects`-classes génériques de.

### <a name="generic-type-arguments-in-member-signatures"></a>Arguments de type générique dans les signatures de membre

Tous les arguments de type générique dans une signature de membre exposée à objective `NSObject` -C doivent avoir une contrainte.

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

**Raison**: Le paramètre de type générique est `NSObject`un, donc la signature du `myMethod:` sélecteur pour peut être exposée en toute sécurité à Objective-C `NSObject` (elle sera toujours ou une sous-classe de celle-ci).

**Incorrect**:

```csharp
class Generic<T> : NSObject
{
    [Export ("myMethod:")]
    public void MyMethod (T value)
    {
    }
}
```

**Raison**: il n’est pas possible de créer une signature objective-c pour les membres exportés que le code objective-c peut appeler, puisque la signature diffère selon le type exact du `T`type générique.

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

**Raison**: il est possible d’avoir des arguments de type générique sans contrainte tant qu’ils ne font pas partie de la signature de membre exportée.

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

**Raison**: le `T` paramètre de l’Objective-C `MyMethod` exporté est contraint à être un `NSObject`, le type `U` sans contrainte ne fait pas partie de la signature.

### <a name="instantiations-of-generic-types-from-objective-c"></a>Instanciations de types génériques de Objective-C

L’instanciation de types génériques à partir d’Objective-C n’est pas autorisée. Cela se produit généralement lorsqu’un type managé est utilisé dans un XIB ou un Storyboard.

Considérons cette définition de classe, qui expose un constructeur qui `IntPtr` prend un (la méthode Xamarin. IOS de construction C# d’un objet à partir d’une instance objective-C native) :

```csharp
class Generic<T> : NSObject where T : NSObject
{
    public Generic () {}
    public Generic (IntPtr ptr) : base (ptr) {}
}
```

Alors que la construction ci-dessus est correcte, lors de l’exécution, cela lèvera une exception à si objective-C tente de créer une instance de celle-ci.

Cela se produit car objective-C n’a pas de concept de types génériques et ne peut pas spécifier le type générique exact à créer.

Ce problème peut être contourné en créant une sous-classe spécialisée du type générique. Par exemple :

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

Désormais, il n’y a plus d’ambiguïté, `GenericUIView` la classe peut être utilisée dans des XIB ou des storyboards.

## <a name="no-support-for-generic-methods"></a>Aucune prise en charge des méthodes génériques

### <a name="generic-methods-are-not-allowed"></a>Les méthodes génériques ne sont pas autorisées.

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

**Raison**: Cela n’est pas autorisé, car Xamarin. iOS ne sait pas quel type utiliser pour l’argument `T` de type lorsque la méthode est appelée à partir de Objective-C.

Une alternative consiste à créer une méthode spécialisée et à l’exporter à la place :

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

### <a name="no-exported-static-members-allowed"></a>Aucun membre statique exporté n’est autorisé

Vous ne pouvez pas exposer des membres statiques à Objective-C s’ils sont hébergés à l' `NSObject`intérieur d’une sous-classe générique de.

Exemple de scénario non pris en charge :

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

**Donc** Tout comme les méthodes génériques, le runtime Xamarin. iOS doit être en mesure de savoir quel type utiliser pour l’argument `T`de type générique.

Pour les membres d’instance, l’instance elle-même est utilisée (étant donné `Generic<T>`qu’il n’y aura `Generic<SomeSpecificClass>`jamais d’instance, elle sera toujours), mais pour les membres statiques, ces informations ne sont pas présentes.

Notez que cela s’applique même si le membre en question n’utilise pas l’argument `T` de type de quelque manière que ce soit.

Dans ce cas, l’alternative consiste à créer une sous-classe spécialisée :

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

Le Bureau d’enregistrement statique ne peut pas résoudre un membre exporté dans un type générique au moment de la génération, comme il le fait habituellement, il doit être recherché lors de l’exécution. Cela signifie que l’appel d’une telle méthode à partir d’Objective-C est légèrement plus lent que l’appel de membres à partir de classes non génériques.

