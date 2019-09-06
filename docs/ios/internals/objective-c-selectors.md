---
title: Sélecteurs objective-C dans Xamarin. iOS
description: Ce document explique comment interagir avec les sélecteurs objective-C à C#partir de. Il décrit comment appeler des sélecteurs et des considérations techniques qui doivent être pris en compte lors de cette opération.
ms.prod: xamarin
ms.assetid: A80904C4-6A89-389B-0487-057AFEB70989
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 07/12/2017
ms.openlocfilehash: 17b845345175d80237bcfdb171461f2c763c364e
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291849"
---
# <a name="objective-c-selectors-in-xamarinios"></a>Sélecteurs objective-C dans Xamarin. iOS

Le langage Objective-C est basé sur les *sélecteurs*. Un sélecteur est un message qui peut être envoyé à un objet ou à une *classe*. [Xamarin. iOS](~/ios/internals/api-design/index.md) mappe les sélecteurs d’instance aux méthodes d’instance et les sélecteurs de classe aux méthodes statiques.

Contrairement aux fonctions C normales (et C++ comme les fonctions membres), vous ne pouvez pas appeler directement un sélecteur à l’aide de [P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/) à la place, les sélecteurs sont envoyés à une classe ou instance objective-C à l’aide de l’élément[`objc_msgSend`](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend)
fonctionnalités.

Pour plus d’informations sur les messages en Objective-C, consultez le guide [utilisation d’Apple avec des objets](https://developer.apple.com/library/archive/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/WorkingwithObjects/WorkingwithObjects.html#//apple_ref/doc/uid/TP40011210-CH4-SW2) .

## <a name="example"></a>Exemple

Supposons que vous souhaitiez appeler l’objet[`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont)
sélecteur activé [`NSString`](https://developer.apple.com/documentation/foundation/nsstring).
La déclaration (à partir de la documentation d’Apple) est la suivante :

```objc
- (CGSize)sizeWithFont:(UIFont *)font forWidth:(CGFloat)width lineBreakMode:(UILineBreakMode)lineBreakMode
```

Cette API présente les caractéristiques suivantes :

- Le type de retour `CGSize` est pour le API unifiée.
- Le `font` paramètre est un [UIFont](xref:UIKit.UIFont) (et un type (indirect) dérivé de [NSObject](xref:Foundation.NSObject), et est mappé à [System. IntPtr](xref:System.IntPtr).
- Le `width` paramètre, un `CGFloat`, est mappé à `nfloat`.
- Le `lineBreakMode` [paramètre`UILineBreakMode`](https://developer.apple.com/documentation/uikit/uilinebreakmode?language=objc), a déjà été lié dans Xamarin. IOS en tant que[`UILineBreakMode`](xref:UIKit.UILineBreakMode)
Enumeration.

La déclaration doit correspondre à ce `objc_msgSend` qui suit :

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

Si la valeur renvoyée était une structure d’une taille inférieure à 8 octets (comme l’ancien `SizeF` utilisé avant de basculer vers les API unifiées), le code ci-dessus aurait pu s’exécuter sur le simulateur, mais s’est bloqué sur l’appareil. Pour appeler un sélecteur qui retourne une valeur inférieure à 8 bits en taille, déclarez `objc_msgSend_stret` la fonction :

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

Une cible sélecteur est soit une instance d’objet, soit une classe objective-C. Si la cible est une instance et provient d’un type Xamarin. iOS lié, utilisez la [`ObjCRuntime.INativeObject.Handle`](xref:ObjCRuntime.INativeObject.Handle) propriété.

Si la cible est une classe, utilisez [`ObjCRuntime.Class`](xref:ObjCRuntime.Class) pour obtenir une référence à l’instance de classe, puis utilisez [`Class.Handle`](xref:ObjCRuntime.Class.Handle) la propriété.

### <a name="selector-names"></a>Noms des sélecteurs

Les noms de sélecteur sont répertoriés dans la documentation d’Apple. Par exemple, [`NSString`](https://developer.apple.com/documentation/foundation/nsstring?language=objc) comprend [`sizeWithFont:`](https://developer.apple.com/documentation/foundation/nsstring/1619917-sizewithfont?language=objc) les [`sizeWithFont:forWidth:lineBreakMode:`](https://developer.apple.com/documentation/foundation/nsstring/1619914-sizewithfont?language=objc) sélecteurs et. Les deux-points incorporés et de fin font partie du nom du sélecteur et ne peuvent pas être omis.

Une fois que vous avez un nom de sélecteur, vous [`ObjCRuntime.Selector`](xref:ObjCRuntime.Selector) pouvez créer une instance de celui-ci.

### <a name="calling-objc_msgsend"></a>Appel de objc_msgSend

`objc_msgSend`envoie un message (sélecteur) à un objet. Cette famille de fonctions prend au moins deux arguments obligatoires : la cible du sélecteur (un handle d’instance ou de classe), le sélecteur lui-même et tous les arguments requis pour le sélecteur. Les arguments d’instance et de sélecteur `System.IntPtr`doivent être, et tous les arguments restants doivent correspondre au type attendu par le `nint` sélecteur, `int`par exemple un `System.IntPtr` pour un `NSObject`ou un pour tous les types dérivés de. Utilisez l'[`NSObject.Handle`](xref:Foundation.NSObject.Handle)
pour obtenir une `IntPtr` pour une instance de type objective-C.

Il existe plusieurs `objc_msgSend` fonctions :

- Utilisez [`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc) pour les sélecteurs qui retournent un struct. Sur ARM, cela comprend tous les types de retour qui ne sont pas une énumération ou l’un des types intégrés`char`C `short`( `int`, `long`, `float`, `double`,,). Sur x86 (le simulateur), cette méthode doit être utilisée pour toutes les structures dont la taille est supérieure à`CGSize` 8 octets (8 octets et `objc_msgSend_stret` n’est pas utilisé dans le simulateur). 
- Utilisez [`objc_msgSend_fpret`](https://developer.apple.com/documentation/objectivec/1456697-objc_msgsend_fpret?language=objc) pour les sélecteurs qui retournent une valeur à virgule flottante sur x86 uniquement. Cette fonction n’a pas besoin d’être utilisée sur ARM. Utilisez `objc_msgSend`plutôt. 
- La fonction [objc_msgSend](https://developer.apple.com/documentation/objectivec/1456712-objc_msgsend) principale est utilisée pour tous les autres sélecteurs.

Une fois que vous avez `objc_msgSend` décidé de la ou des fonctions que vous devez appeler (le simulateur et l’appareil peuvent nécessiter une méthode différente), [`[DllImport]`](xref:System.Runtime.InteropServices.DllImportAttribute) vous pouvez utiliser une méthode normale pour déclarer la fonction en vue d’un appel ultérieur.

Un ensemble de `objc_msgSend` déclarations prédéfinies est disponible dans `ObjCRuntime.Messaging`.

## <a name="different-invocations-on-simulator-and-device"></a>Différents appels sur le simulateur et l’appareil

Comme décrit ci-dessus, Objective-C a `objc_msgSend` trois genres de méthodes : l’un pour les appels normaux, l’autre pour les appels qui retournent des valeurs à virgule flottante (x86 uniquement) et l’autre pour les appels qui retournent des valeurs struct. Ce dernier comprend le `_stret` suffixe `ObjCRuntime.Messaging`dans.

Si vous appelez une méthode qui retourne certains structs (règles décrites ci-dessous), vous devez appeler la méthode avec la valeur de retour comme premier paramètre comme `out` valeur :

```csharp
// The following returns a PointF structure:
PointF ret;
Messaging.PointF_objc_msgSend_stret_PointF_IntPtr (out ret, this.Handle, selConvertPointFromWindow.Handle, point, window.Handle);
```

La règle d’utilisation de la `_stret_` méthode diffère sur x86 et ARM.
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

Lorsque vous créez pour ARM, utilisez l'[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
pour tout type valeur qui n’est pas une énumération ou l’un des types de base pour`int`une `byte`énumération `long`( `double`, `float`, `short`,,,).

Lors de la génération pour x86, utilisez[`objc_msgSend_stret`](https://developer.apple.com/documentation/objectivec/1456730-objc_msgsend_stret?language=objc)
pour tout type valeur qui n’est pas une énumération ou l’un des types de base pour`int`une `byte`énumération `long`( `double`, `float`, `short`,,,) et dont la taille native est supérieure à 8 octets.

### <a name="creating-your-own-signatures"></a>Création de vos propres signatures

Le [Registre](https://gist.github.com/rolfbjarne/981b778a99425a6e630c) suivant peut être utilisé pour créer vos propres signatures, si nécessaire.

## <a name="related-links"></a>Liens connexes

- Exemple [de sélecteurs objective-C](https://developer.xamarin.com/samples/mac-ios/Objective-C/)
