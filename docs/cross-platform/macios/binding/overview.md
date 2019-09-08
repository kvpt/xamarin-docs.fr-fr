---
title: Vue d’ensemble des liaisons objective-C
description: Ce document fournit une vue d’ensemble des différentes façons C# de créer des liaisons pour du code Objective-C, notamment des liaisons de ligne de commande, des projets de liaison et une netteté objective. Il aborde également le fonctionnement de la liaison.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: conceptdev
ms.author: crdun
ms.date: 11/25/2015
ms.openlocfilehash: db37a6a912cae3c2d53d8838ba2d2bd0224e8df7
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765595"
---
# <a name="overview-of-objective-c-bindings"></a>Vue d’ensemble des liaisons objective-C

_Détails sur le fonctionnement du processus de liaison_

La liaison d’une bibliothèque objective-C pour une utilisation avec Xamarin s’effectue en trois étapes :

1. Écrivez une C# « définition d’API » pour décrire comment l’API native est exposée dans .net et comment elle est mappée à l’Objective-C sous-jacent. Pour ce faire, utilisez C# des constructions standard `interface` comme et différents **attributs** de liaison (consultez cet [exemple simple](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Une fois que vous avez écrit la « définition de C#l’API » dans, vous la compilez pour produire un assembly de « liaison ». Cela peut être fait sur la [**ligne de commande**](#commandline) ou à l’aide d’un [**projet de liaison**](#bindingproject) dans Visual Studio pour Mac ou Visual Studio.

3. Cet assembly de « liaison » est ensuite ajouté à votre projet d’application Xamarin. vous pouvez donc accéder aux fonctionnalités natives à l’aide de l’API que vous avez définie.
   Le projet de liaison est complètement distinct de vos projets d’application.

   > [!NOTE]
   > L’étape 1 peut être automatisée avec l’assistance de la [**finesse objective**](#objectivesharpie). Il examine l’API objective-C et génère une « C# définition d’API » proposée. Vous pouvez personnaliser les fichiers créés par la netteté objective et les utiliser dans un projet de liaison (ou sur la ligne de commande) pour créer votre assembly de liaison. La netteté objective ne crée pas de liaisons proprement dites, il s’agit simplement d’une partie facultative du processus plus large.

Vous pouvez également lire des détails techniques supplémentaires sur [son fonctionnement](#howitworks), qui vous aideront à écrire vos liaisons.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Liaisons de ligne de commande

Vous pouvez utiliser le `btouch-native` pour Xamarin. iOS (ou `bmac-native` si vous utilisez Xamarin. Mac) pour créer des liaisons directement. Il fonctionne en passant les C# définitions d’API que vous avez créées manuellement (ou à l’aide de la finesse objective) à l'`btouch-native` outil en ligne `bmac-native` de commande (pour iOS ou pour Mac).

La syntaxe générale pour l’appel de ces outils est la suivante :

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

La commande ci-dessus génère le `cocos2d.dll` fichier dans le répertoire actif et contiendra la bibliothèque entièrement liée que vous pouvez utiliser dans votre projet. Il s’agit de l’outil que Visual Studio pour Mac utilise pour créer vos liaisons si vous utilisez un projet de liaison (décrit [ci-dessous](#bindingproject)).

<a name="bindingproject" />

## <a name="binding-project"></a>Projet de liaison

Un projet de liaison peut être créé dans Visual Studio pour Mac ou Visual Studio (Visual Studio ne prend en charge que les liaisons iOS) et facilite la modification et la génération de définitions d’API pour la liaison (par opposition à l’utilisation de la ligne de commande).

Suivez ce [Guide de prise](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) en main pour découvrir comment créer et utiliser un projet de liaison pour produire une liaison.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

La finesse d’objectif est un autre outil en ligne de commande distinct qui vous aide à créer une liaison. Il ne crée pas de liaison seul, mais il automatise l’étape initiale de génération d’une définition d’API pour la bibliothèque Native cible.

Lisez les [documents objective Sharp](~/cross-platform/macios/binding/objective-sharpie/index.md) pour découvrir comment analyser des bibliothèques natives, des frameworks natifs et des CocoaPods dans des définitions d’API qui peuvent être intégrés dans des liaisons.

<a name="howitworks" />

## <a name="how-binding-works"></a>Fonctionnement de la liaison

Il est possible d’utiliser l’attribut [[register]](xref:Foundation.RegisterAttribute) , l’attribut [[Export]](xref:Foundation.ExportAttribute) et l' [appel manuel d’objective-c](~/ios/internals/objective-c-selectors.md) pour lier manuellement de nouveaux types objective-c (précédemment détachés).

Tout d’abord, recherchez un type que vous souhaitez lier. À des fins de discussion (et de simplicité), nous allons lier le type [NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) (qui a déjà été lié dans [Foundation. NSEnumerator](xref:Foundation.NSEnumerator); l’implémentation ci-dessous n’est qu’à titre d’exemple).

Ensuite, nous devons créer le C# type. Nous voulons probablement placer cela dans un espace de noms. comme objective-c ne prend pas en charge les espaces de noms, `[Register]` nous devrons utiliser l’attribut pour modifier le nom de type que Xamarin. iOS inscrira avec le runtime objective-C. Le C# type doit également hériter de [Foundation. NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Troisièmement, passez en revue la documentation objective-C et créez des instances de [ObjCRuntime. Selector](xref:ObjCRuntime.Selector) pour chaque sélecteur que vous souhaitez utiliser. Placez-les dans le corps de la classe :

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quatrièmement, votre type devra fournir des constructeurs. Vous *devez* chaîner l’appel de votre constructeur au constructeur de classe de base. Les `[Export]` attributs permettent au code Objective-C d’appeler les constructeurs avec le nom de sélecteur spécifié :

```csharp
[Export("init")]
public NSEnumerator()
    : base(NSObjectFlag.Empty)
{
    Handle = Messaging.IntPtr_objc_msgSend(this.Handle, selInit.Handle);
}
```

```csharp
// This constructor must be present so that Xamarin.iOS
// can create instances of your type from Objective-C code.
public NSEnumerator(IntPtr handle)
    : base(handle)
{
}
```

Cinquième, fournissez des méthodes pour chacun des sélecteurs déclarés à l’étape 3. Celles-ci `objc_msgSend()` utilisent pour appeler le sélecteur sur l’objet natif. Notez l’utilisation de [Runtime. GetNSObject ()](xref:ObjCRuntime.Runtime.GetNSObject*) pour convertir un `IntPtr` en type (sous- `NSObject` ) typé approprié. Si vous souhaitez que la méthode soit appelée à partir du code Objective-C, le membre *doit* être **virtuel**.

```csharp
[Export("nextObject")]
public virtual NSObject NextObject()
{
    return Runtime.GetNSObject(
        Messaging.IntPtr_objc_msgSend(this.Handle, selNextObject.Handle));
}
```

```csharp
// Note that for properties, [Export] goes on the get/set method:
public virtual NSArray AllObjects {
    [Export("allObjects")]
    get {
        return (NSArray) Runtime.GetNSObject(
            Messaging.IntPtr_objc_msgSend(this.Handle, selAllObjects.Handle));
    }
}
```

Ensemble :

```csharp
using System;
using Foundation;
using ObjCRuntime;

namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        static Selector selInit       = new Selector("init");
        static Selector selAllObjects = new Selector("allObjects");
        static Selector selNextObject = new Selector("nextObject");

        [Export("init")]
        public NSEnumerator()
            : base(NSObjectFlag.Empty)
        {
            Handle = Messaging.IntPtr_objc_msgSend(this.Handle,
                selInit.Handle);
        }

        public NSEnumerator(IntPtr handle)
            : base(handle)
        {
        }

        [Export("nextObject")]
        public virtual NSObject NextObject()
        {
            return Runtime.GetNSObject(
                Messaging.IntPtr_objc_msgSend(this.Handle,
                    selNextObject.Handle));
        }

        // Note that for properties, [Export] goes on the get/set method:
        public virtual NSArray AllObjects {
            [Export("allObjects")]
            get {
                return (NSArray) Runtime.GetNSObject(
                    Messaging.IntPtr_objc_msgSend(this.Handle,
                        selAllObjects.Handle));
            }
        }
    }
}
```
