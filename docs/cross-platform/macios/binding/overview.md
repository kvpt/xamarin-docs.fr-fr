---
title: Aperçu des liaisons Objective-C
description: Ce document donne un aperçu des différentes façons de créer des liaisons C pour le code Objectif-C, y compris les liaisons de ligne de commande, les projets contraignants et Objectif Sharpie. Il traite également du fonctionnement de la liaison.
ms.prod: xamarin
ms.assetid: 9EE288C5-8952-C5A9-E542-0BD847300EC6
author: davidortinau
ms.author: daortin
ms.date: 11/25/2015
ms.openlocfilehash: be2f7f555b76d472f7a66d95e661bb2f5884c58f
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78292421"
---
# <a name="overview-of-objective-c-bindings"></a>Aperçu des liaisons Objective-C

_Détails du fonctionnement du processus de liaison_

Lier une bibliothèque Objective-C pour une utilisation avec Xamarin fait trois étapes :

1. Écrivez une définition de l’API C POUR décrire comment l’API indigène est exposée en .NET, et comment elle s’adresse à l’Objectif-C sous-jacent. Ceci est fait en utilisant `interface` des constructions standard de C’comme et divers **attributs** de liaison (voir cet [exemple simple](~/cross-platform/macios/binding/objective-c-libraries.md#Binding_an_API)).

2. Une fois que vous avez écrit la « définition de l’API » en C, vous la compilez pour produire un assemblage « contraignant ». Cela peut être fait sur la [**ligne de commande**](#commandline) ou en utilisant un projet de [**liaison**](#bindingproject) dans Visual Studio pour Mac ou Visual Studio.

3. Cet assemblage "contraignant" est ensuite ajouté à votre projet d’application Xamarin, de sorte que vous pouvez accéder à la fonctionnalité native à l’aide de l’API que vous avez défini.
   Le projet contraignant est complètement distinct de vos projets d’application.

   > [!NOTE]
   > L’étape 1 peut être automatisée avec l’aide [**d’Objectif Sharpie**](#objectivesharpie). Il examine l’API Objective-C et génère une définition proposée de l’API. Vous pouvez personnaliser les fichiers créés par Objectif Sharpie et les utiliser dans un projet contraignant (ou sur la ligne de commande) pour créer votre assemblage contraignant. Objectif Sharpie ne crée pas de liaisons par lui-même, c’est simplement une partie facultative du processus plus large.

Vous pouvez également lire plus de détails techniques sur [son fonctionnement, ce](#howitworks)qui vous aidera à écrire vos fixations.

<a name="Command_Line_Bindings" /><a name="commandline" />

## <a name="command-line-bindings"></a>Liaisons de ligne de commandement

Vous pouvez `btouch-native` utiliser le pour Xamarin.iOS (ou `bmac-native` si vous utilisez Xamarin.Mac) pour construire des fixations directement. Il fonctionne en passant les définitions de l’API CD que vous avez créées`btouch-native` à la `bmac-native` main (ou en utilisant Objectif Sharpie) à l’outil de ligne de commande (pour iOS ou pour Mac).

La syntaxe générale pour invoquer ces outils est la suivante:

```csharp
# Use this for Xamarin.iOS:
bash$ /Developer/MonoTouch/usr/bin/btouch-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

```csharp
# Use this for Xamarin.Mac:
bash$ bmac-native -e cocos2d.cs -s:enums.cs -x:extensions.cs
```

La commande ci-dessus `cocos2d.dll` générera le fichier dans l’annuaire actuel, et il contiendra la bibliothèque entièrement liée que vous pouvez utiliser dans votre projet. C’est l’outil que Visual Studio pour Mac utilise pour créer vos liaisons si vous utilisez un projet de liaison (décrit [ci-dessous](#bindingproject)).

<a name="bindingproject" />

## <a name="binding-project"></a>Projet contraignant

Un projet contraignant peut être créé dans Visual Studio pour Mac ou Visual Studio (Visual Studio ne prend en charge que les fixations iOS) et facilite l’édition et la construction de définitions API pour la liaison (par rapport à l’utilisation de la ligne de commande).

Suivez ce [guide de démarrage](~/cross-platform/macios/binding/objective-c-libraries.md#Getting_Started) pour voir comment créer et utiliser un projet de liaison pour produire une liaison.

<a name="objectivesharpie" />

## <a name="objective-sharpie"></a>Objective Sharpie

Objectif Sharpie est un autre outil de ligne de commande distinct qui aide avec les étapes initiales de la création d’une liaison. Il ne crée pas de liaison par lui-même, il automatise plutôt l’étape initiale de générer une définition API pour la bibliothèque autochtone cible.

Lisez les [documents Objectif Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md) pour apprendre à analyser les bibliothèques indigènes, les cadres autochtones et les CocoaPods dans des définitions d’API qui peuvent être intégrées dans des liaisons.

<a name="howitworks" />

## <a name="how-binding-works"></a>Fonctionnement de la liaison

Il est possible d’utiliser [l’attribut [Register],](xref:Foundation.RegisterAttribute) [l’attribut [Export]](xref:Foundation.ExportAttribute) et [l’invocation manuelle du sélecteur Objectif-C](~/ios/internals/objective-c-selectors.md) ensemble pour lier manuellement de nouveaux types Objectif-C (auparavant non liés).

Tout d’abord, trouver un type que vous souhaitez lier. À des fins de discussion (et de simplicité), nous lierons le type [NSEnumerator](https://developer.apple.com/documentation/foundation/nsenumerator) (qui a déjà été lié à [Foundation.NSEnumerator](xref:Foundation.NSEnumerator); la mise en œuvre ci-dessous est juste à des fins par exemple).

Deuxièmement, nous devons créer le type C. Nous allons probablement vouloir placer cela dans un espace de nom; étant donné qu’Objectif-C ne prend pas en charge `[Register]` les espaces de noms, nous devrons utiliser l’attribut pour changer le nom de type que Xamarin.iOS s’enregistrera avec l’objectif-C runtime. Le type C’doit également hériter de [Foundation.NSObject](xref:Foundation.NSObject):

```csharp
namespace Example.Binding {
    [Register("NSEnumerator")]
    class NSEnumerator : NSObject
    {
        // see steps 3-5
    }
}
```

Troisièmement, examinez la documentation Objective-C et créez des instances [ObjCRuntime.Selector](xref:ObjCRuntime.Selector) pour chaque sélecteur que vous souhaitez utiliser. Placez-les dans le corps de classe :

```csharp
static Selector selInit       = new Selector("init");
static Selector selAllObjects = new Selector("allObjects");
static Selector selNextObject = new Selector("nextObject");
```

Quatrièmement, votre type devra fournir des constructeurs. Vous *devez* enchaîner votre invocation constructeur au constructeur de classe de base. Les `[Export]` attributs permettent au code Objectif-C d’appeler les constructeurs avec le nom spécifié du sélecteur :

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

Cinquièmement, fournir des méthodes pour chacun des sélecteurs déclarés dans l’étape 3. Ceux-ci `objc_msgSend()` utiliseront pour invoquer le sélecteur sur l’objet indigène. Notez l’utilisation de [Runtime.GetNSObject()](xref:ObjCRuntime.Runtime.GetNSObject*) pour convertir un `IntPtr` type (sous-type) bien tapé. `NSObject` Si vous voulez que la méthode soit callable à partir du code Objectif-C, le membre *doit* être **virtuel**.

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

Mettre tout cela ensemble:

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
