---
title: Sélecteurs objective-C dans Xamarin. iOS
description: Ce document explique comment interagir avec les sélecteurs objective-C à C#partir de. Il décrit comment appeler des sélecteurs et des considérations techniques qui doivent être pris en compte lors de cette opération.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/12/2017
ms.openlocfilehash: 79f226c137c3ab6b1dd2de9f92cb868056aa9d59
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022292"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Sélecteurs objective-C dans Xamarin. iOS

Le langage Objective-C est basé sur les *sélecteurs*. Un sélecteur est un message qui peut être envoyé à un objet ou à une *classe*. [Xamarin. iOS](~/ios/internals/api-design/index.md) mappe les sélecteurs d’instance aux méthodes d’instance et les sélecteurs de classe aux méthodes statiques.

Contrairement aux fonctions C normales (et C++ comme les fonctions membres), vous ne pouvez pas appeler directement un sélecteur à l’aide de [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) à la place, les sélecteurs sont envoyés à une classe ou instance objective-C à l’aide de l' [`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
fonctionnalités.

Pour plus d’informations sur les messages en Objective-C, consultez le guide [utilisation d’Apple avec des objets](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) .

## <a name="example"></a>Exemple

Supposons que vous souhaitiez appeler le [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
sélecteur sur [`NSString`](https://developer.apple.com/documentation/foundation/nsstring).
La déclaration (à partir de la documentation d’Apple) est la suivante :

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Cette API présente les caractéristiques suivantes :

- Le type de retour est `CGSize` pour le API unifiée.
- Le paramètre `font` est un [UIFont](xref:UIKit.UIFont) (et un type (indirect) dérivé de [NSObject](xref:Foundation.NSObject), et est mappé à [System. IntPtr](xref:System.IntPtr).
- Le paramètre `width`, un `CGFloat`, est mappé à `nfloat`.
- Le paramètre `lineBreakMode`, un [`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), a déjà été lié dans Xamarin. IOS en tant que [`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Enumeration.

La déclaration de `objc_msgSend` doit correspondre à ce qui suit :

```csharp
CGSize objc_msgSend(
    IntPtr target, 
    IntPtr selector, 
    IntPtr font, 
    nfloat width, 
    UILineBreakMode mode
);
```

Déclarez-le comme suit :

```csharp
[DllImport (Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend")]
static extern CGSize cgsize_objc_msgSend_IntPtr_float_int (
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Pour appeler cette méthode, utilisez le code suivant :

```csharp
NSString target = ...
Selector selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont font = ...
nfloat width = ...
UILineBreakMode mode = ...

CGSize size = cgsize_objc_msgSend_IntPtr_float_int(
    target.Handle, 
    selector.Handle,
    font == null ? IntPtr.Zero : font.Handle,
    width,
    mode
);
```

Si la valeur renvoyée était une structure d’une taille inférieure à 8 octets (comme l’ancien `SizeF` utilisé avant de basculer vers les API unifiées), le code ci-dessus aurait été exécuté sur le simulateur, mais s’est bloqué sur l’appareil. Pour appeler un sélecteur qui retourne une valeur inférieure à 8 bits, déclarez la fonction `objc_msgSend_stret` :

```csharp
[DllImport (MonoTouch.Constants.ObjectiveCLibrary, EntryPoint="objc_msgSend_stret")]
static extern void cgsize_objc_msgSend_stret_IntPtr_float_int (
    out CGSize retval,
    IntPtr target, 
    IntPtr selector,
    IntPtr font,
    nfloat width,
    UILineBreakMode mode
);
```

Pour appeler cette méthode, utilisez le code suivant :

```csharp
NSString      target = ...
Selector    selector = new Selector ("sizeWithFont:forWidth:lineBreakMode:");
UIFont          font = ...
nfloat          width = ...
UILineBreakMode mode = ...

CGSize size;

if (Runtime.Arch == Arch.SIMULATOR)
    size = cgsize_objc_msgSend_IntPtr_float_int(
        target.Handle, 
        selector.Handle,
        font == null ? IntPtr.Zero : font.Handle,
        width,
        mode
    );
else
    cgsize_objc_msgSend_stret_IntPtr_float_int(
        out size,
        target.Handle, selector.Handle,
        font == null ? IntPtr.Zero: font.Handle,
        width,
        mode
    );
```

## <a name="invoking-a-selector"></a>Appel d’un sélecteur

L’appel d’un sélecteur comporte trois étapes :

1. Obtient la cible du sélecteur.
2. Obtient le nom du sélecteur.
3. Appelez `objc_msgSend` avec les arguments appropriés.

### <a name="selector-targets"></a>Cibles de sélecteur

Une cible sélecteur est soit une instance d’objet, soit une classe objective-C. Si la cible est une instance et provient d’un type Xamarin. iOS lié, utilisez la propriété [`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle) .

Si la cible est une classe, utilisez [`ObjCRuntime.Class`](xref:ObjCRuntime.Class) pour obtenir une référence à l’instance de classe, puis utilisez la propriété [`Class.Handle`](xref:ObjCRuntime.Class.Handle) .

### <a name="selector-names"></a>Noms des sélecteurs

Les noms de sélecteur sont répertoriés dans la documentation d’Apple. Par exemple, [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc) comprend des sélecteurs de [`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) et de [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) . Les deux-points incorporés et de fin font partie du nom du sélecteur et ne peuvent pas être omis.

Une fois que vous avez un nom de sélecteur, vous pouvez créer une instance de [`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector) pour celui-ci.

### <a name="calling-objc_msgsend"></a>Appel de objc_msgSend

`objc_msgSend` envoie un message (sélecteur) à un objet. Cette famille de fonctions prend au moins deux arguments obligatoires : la cible du sélecteur (un handle d’instance ou de classe), le sélecteur lui-même et tous les arguments requis pour le sélecteur. Les arguments d’instance et de sélecteur doivent être `System.IntPtr`, et tous les arguments restants doivent correspondre au type attendu par le sélecteur, par exemple une `nint` pour un `int`, ou un `System.IntPtr` pour tous les types dérivés de `NSObject`. Utilisez la [`NSObject.Handle`](xref:Foundation.NSObject.Handle)
pour obtenir une `IntPtr` pour une instance de type objective-C.

Il existe plusieurs fonctions `objc_msgSend` :

- Utilisez [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) pour les sélecteurs qui retournent un struct. Sur ARM, cela comprend tous les types de retour qui ne sont pas une énumération ou l’un des types intégrés C (`char`, `short`, `int`, `long`, `float`, `double`). Sur x86 (le simulateur), cette méthode doit être utilisée pour toutes les structures dont la taille est supérieure à 8 octets (`CGSize` est de 8 octets et n’utilise pas `objc_msgSend_stret` dans le simulateur). 
- Utilisez [`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) pour les sélecteurs qui retournent une valeur à virgule flottante sur x86 uniquement. Cette fonction n’a pas besoin d’être utilisée sur ARM. Utilisez plutôt `objc_msgSend`. 
- La fonction [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) principale est utilisée pour tous les autres sélecteurs.

Une fois que vous avez choisi les fonctions `objc_msgSend` que vous devez appeler (le simulateur et l’appareil peuvent nécessiter une méthode différente), vous pouvez utiliser une méthode de [`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute) normale pour déclarer la fonction en vue d’un appel ultérieur.

Vous trouverez un ensemble de déclarations `objc_msgSend` prédéfinies dans `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Différents appels sur le simulateur et l’appareil

Comme décrit ci-dessus, Objective-C a trois genres de méthodes de `objc_msgSend` : une pour les appels normaux, une pour les appels qui retournent des valeurs à virgule flottante (x86 uniquement) et une pour les appels qui retournent des valeurs struct. Ce dernier comprend le suffixe `_stret` dans `ObjCRuntime.Messaging`.

Si vous appelez une méthode qui retourne certains structs (règles décrites ci-dessous), vous devez appeler la méthode avec la valeur de retour comme premier paramètre comme valeur `out` :

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La règle relative à l’utilisation de la méthode `_stret_` diffère sur x86 et ARM.
Si vous souhaitez que vos liaisons fonctionnent à la fois sur le simulateur et sur l’appareil, ajoutez le code suivant :

```csharp
if (Runtime.Arch == Arch.DEVICE)
{
    PointF ret;
    Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, myHandle, selector.Handle);
    return ret;
} 
else
{
    return Messaging.PointF_objc_msgSend_PointF_IntPtr (myHandle, selector.Handle);
}
```

### <a name="using-the-objc_msgsend_stret-method"></a>Utilisation de la méthode objc_msgSend_stret

Lorsque vous créez pour ARM, utilisez le [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
pour tout type valeur qui n’est pas une énumération ou l’un des types de base pour une énumération (`int`, `byte`, `short`, `long`, `double`, `float`).

Lors de la génération pour x86, utilisez [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
pour tout type valeur qui n’est pas une énumération ou l’un des types de base pour une énumération (`int`, `byte`, `short`, `long`, `double`, `float`) et dont la taille native est supérieure à 8 octets.

### <a name="creating-your-own-signatures"></a>Création de vos propres signatures

Le [Registre](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) suivant peut être utilisé pour créer vos propres signatures, si nécessaire.

## <a name="related-links"></a>Liens connexes

- Exemple [de sélecteurs objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/)
