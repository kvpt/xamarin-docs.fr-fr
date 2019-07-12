---
title: Vue d’ensemble des liaisons Objective-C
description: Ce document fournit une vue d’ensemble de différentes façons de créer C# liaisons pour le code Objective-C, notamment les liaisons de ligne de commande, les projets de liaison et les Sharpie d’objectif. Il aborde également le fonctionne de la liaison.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: asb3993
ms.author: amburns
ms.date: 11/25/2015
ms.openlocfilehash: 93f90d2dad140f021c7e575afed44da1c5351eda
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830517"
---
# <a name="overview-of-objective-c-bindings"></a>Vue d’ensemble des liaisons Objective-C

_Détails sur le fonctionnement du processus de liaison_

Liaison d’une bibliothèque Objective-C pour une utilisation avec Xamarin prend trois étapes :

1. Écrire un C# « Définition API » pour décrire comment l’API native est exposée dans .NET, et comment il est mappé à sous-jacent Objective-C. Cette opération est effectuée à l’aide de la norme C# constructions telles que `interface` et liaison divers **attributs** (consultez ce [exemple simple](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Une fois que vous avez écrit la « définition API » C#, compilez-le pour produire un assembly de « liaison ». Cela peut être fait sur le [ **ligne de commande** ](#commandline) ou en utilisant un [ **projet de liaison** ](#bindingproject) dans Visual Studio pour Mac ou Visual Studio.

3. Cet assembly « binding » est ensuite ajouté à votre projet d’application Xamarin, donc vous pouvez accéder à la fonctionnalité native à l’aide de l’API que vous avez défini.
   Le projet de liaison est entièrement distinct de vos projets d’application.

   > [!NOTE]
   > Étape 1 peut être automatisé avec l’assistance de [ **objectif Sharpie**](#objectivesharpie). Il examine les API Objective-C et génère un proposé C# « Définition API ». Vous pouvez personnaliser les fichiers créés par objectif Sharpie et les utiliser dans un projet de liaison (ou sur la ligne de commande) pour créer votre assembly de liaison. Objectif Sharpie ne crée pas de liaisons par lui-même, il est simplement une partie facultative de processus plus importants.

Vous pouvez également lire plus de détails techniques de [son fonctionnement](#howitworks), ce qui vous aideront à écrire vos liaisons.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Liaisons de la ligne de commande

Vous pouvez utiliser la `btouch-native` pour Xamarin.iOS (ou `bmac-native` si vous utilisez Xamarin.Mac) pour créer des liaisons directement. Il fonctionne en passant le C# API définitions que vous avez créée manuellement (ou à l’aide de Objective Sharpie) à l’outil de ligne de commande (`btouch-native` pour iOS ou `bmac-native` pour Mac).


La syntaxe générale pour l’appel de ces outils est :

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

La commande ci-dessus génère le fichier `cocos2d.dll` dans le répertoire actif, et il contiendra la bibliothèque totalement liée que vous pouvez utiliser dans votre projet. Ceci est l’outil Visual Studio pour Mac utilise pour créer vos liaisons si vous utilisez un projet de liaison (décrit [ci-dessous](#bindingproject)).


<a name="bindingproject" />

## <a name="binding-project"></a>Projet de liaison

Un projet de liaison peut être créé dans Visual Studio pour Mac ou Visual Studio (Visual Studio prend uniquement en charge les liaisons iOS) et le rend plus facile modifier et générer des définitions d’API pour la liaison (par opposition à l’aide de la ligne de commande).

Suivez ce [guide Mise en route](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) pour voir comment créer et utiliser un projet de liaison pour produire une liaison.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Objectif Sharpie est un autre outil de ligne commande qui permet des étapes initiales de la création d’une liaison. Il ne crée pas une liaison par lui-même, au lieu de cela, il automatise la première étape de génération d’une définition d’API de la bibliothèque native cible.

Lire le [docs d’objectif Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) pour apprendre à analyser des bibliothèques natives, d’infrastructures natives et CocoaPods dans les définitions d’API qui peuvent être générées dans des liaisons.

<a name="howitworks" />

## <a name="how-binding-works"></a>Fonctionne de la liaison

Il est possible d’utiliser le [[inscrire]](xref:Foundation.RegisterAttribute) attribut, [[Exporter]](xref:Foundation.ExportAttribute) attribut, et [appel de sélecteur manuel Objective-C](~/ios/internals/objective-c-selectors.md) ensemble à lier manuellement new (précédemment types Objective-C non liés).

Tout d’abord, trouver un type que vous souhaitez lier. Pour la discussion à des fins (et la simplicité), nous allons lier la [NSEnumerator](https://developer.apple.com/iphone/library/documentation/Cocoa/Reference/Foundation/Classes/NSEnumerator_Class/Reference/Reference.html) type (qui a déjà été lié dans [Foundation.NSEnumerator](xref:Foundation.NSEnumerator); l’implémentation ci-après est simplement par exemple à des fins).

Ensuite, nous devons créer la C# type. Nous devrons probablement placer ces données dans un espace de noms ; dans la mesure où Objective-C ne prend pas en charge les espaces de noms, nous devons utiliser le `[Register]` attribut pour modifier le nom de type Xamarin.iOS s’inscrivent auprès du runtime Objective-C. Le C# type doit également hériter [Foundation.NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Troisièmement, passez en revue la documentation Objective-C et créer [ObjCRuntime.Selector](xref:ObjCRuntime.Selector) instances pour chaque sélecteur que vous souhaitez utiliser. Placez ceux-ci dans le corps de la classe :

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quatrièmement, votre type devrez fournir des constructeurs. Vous *doit* chaîner votre appel de constructeur pour le constructeur de classe de base. Le `[Export]` attributs permettent au code Objective-C pour appeler les constructeurs avec le nom de la sélection spécifiée :

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

Cinquièmement, fournissent des méthodes pour chacun des sélecteurs déclarées à l’étape 3. Ces filtres utilisent `objc_msgSend()` pour appeler le sélecteur sur l’objet natif. Notez l’utilisation de [Runtime.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*) pour convertir un `IntPtr` dans correctement typée `NSObject` (sub) type. Si vous souhaitez que la méthode puisse être appelée à partir du code Objective-C, le membre *doit* être **virtuel**.

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

Vue d’ensemble :

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
