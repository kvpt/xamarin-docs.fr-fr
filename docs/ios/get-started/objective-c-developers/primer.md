---
title: Abécédaire du langage C# pour les développeurs Objective-C
description: Ce document décrit C# pour les développeurs Objective-C. Il compare et contraste les deux langages en examinant les protocoles et les interfaces, les catégories et les méthodes d’extension, les frameworks et les assemblys, les sélecteurs et les paramètres nommés, et bien plus encore.
ms.prod: xamarin
ms.assetid: 00285CBD-AE5E-4126-8F22-6B231B9467EA
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2017
ms.openlocfilehash: 8b84d959ce7976fc51cbdfee99cbceec560e8e8e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73022431"
---
# <a name="c-primer-for-objective-c-developers"></a>Abécédaire du langage C# pour les développeurs Objective-C

_Xamarin. iOS permet de partager du code de plateforme C# agnostique écrit dans plusieurs plateformes. Toutefois, les applications iOS existantes peuvent souhaiter tirer parti du code Objective-C qui a déjà été créé. Cet article sert de brève introduction pour les développeurs objective-C qui souhaitent passer à Xamarin et au C# langage._

Les applications iOS et OS X développées en Objective-C peuvent bénéficier de Xamarin en utilisant C# là où aucun code spécifique de la plateforme n’est pas nécessaire, ce qui permet d’utiliser ce code sur des appareils autres qu’Apple. Les éléments tels que les services web, l’analyse JSON et XML, ainsi que les algorithmes personnalisés, peuvent ensuite être utilisés à travers les différentes plateformes.

Pour tirer parti de Xamarin tout en conservant les ressources Objective-C existantes, ce premier peut être exposé à C# via une technologie de Xamarin connue sous le nom de liaisons, qui surfacent le code Objective-C pour l’environnement C# managé. Le cas échéant, le code peut également être transféré ligne par ligne vers C#. Néanmoins, quelle que soit l’approche adoptée (liaison ou transfert), un minimum de connaissances d’Objective-C et de C# sont nécessaires pour pouvoir tirer efficacement parti du code Objective-C existant avec Xamarin.iOS.

## <a name="objective-c-interop"></a>Interopérabilité d’Objective-C

Il n’existe actuellement aucun mécanisme pris en charge pour créer à l’aide de Xamarin.iOS une bibliothèque en C# qui puisse être appelée à partir d’Objective-C. Cela est principalement dû au fait que le runtime Mono est également nécessaire en plus de la liaison. Toutefois, vous pouvez toujours créer la majorité de votre logique en Objective-C, y compris les interfaces utilisateur. Pour ce faire, encapsulez le code Objective-C dans une bibliothèque et créez-y une liaison. Xamarin.iOS est nécessaire pour démarrer l’application (il doit créer le point d’entrée `Main`). Toute autre logique en Objective-C peut ensuite être exposée à C# via la liaison (ou via P/Invoke). Vous pouvez ainsi conserver la logique spécifique de la plateforme en Objective-C et développer des parties indépendantes de la plateforme en C#.

Cet article met en avant certaines ressemblances clés et différences entre les deux langages. Il peut servir de manuel d’introduction durant le passage à C# avec Xamarin.iOS, que ce soit par liaison à du code Objective-C existant ou par transfert vers C#.

Pour plus d’informations sur la création de liaisons, consultez les autres documents disponibles dans [Liaison Objective-C](~/ios/platform/binding-objective-c/index.md).

## <a name="language-comparison"></a>Comparaison des langages

Objective-C et C# sont deux langages très différents d’un point de vue de la syntaxe et du runtime. Objective-C est un langage dynamique qui utilise un schéma de transmission de messages, tandis que C# est typé statiquement. Au niveau de la syntaxe, Objective-C est similaire à Smalltalk, tandis que C# tire la plupart de sa syntaxe de base de Java, même si au cours de ces dernières années, elle a évolué et inclut désormais de nombreuses fonctionnalités au-delà de Java.

Ceci dit, plusieurs fonctionnalités de langage d’Objective-C et de C# sont similaires au niveau du fonctionnement. Durant la création d’une liaison au code Objective-C à partir de C# ou quand vous transférez Objective-C vers C#, comprendre ces ressemblances s’avère utile.

### <a name="protocols-vs-interfaces"></a>Protocoles et interfaces

Objective-C et C# sont tous deux des langages à héritage simple. Toutefois, les deux langages prennent en charge l’implémentation d’interfaces multiples dans une classe donnée. En Objective-C, ces interfaces logiques sont appelées des *protocoles* tandis qu’en C#, elles sont appelées des *interfaces*. Au niveau de l’implémentation, la principale différence entre une interface C# et un protocole Objective-C est que ce dernier peut avoir des méthodes facultatives. Pour plus d’informations, consultez l’article [Événements, délégués et protocoles](~/ios/app-fundamentals/delegates-protocols-and-events.md).

### <a name="categories-vs-extension-methods"></a>Catégories et méthodes d’extension

Objective-C permet d’ajouter des méthodes à une classe pour laquelle vous n’avez peut-être pas le code d’implémentation à l’aide de *catégories*. En C#, un concept similaire est disponible à travers ce que l'on appelle des *méthodes d’extension*.

Les méthodes d’extension vous permettent d’ajouter des méthodes statiques à une classe, où les méthodes statiques en C# sont analogues aux méthodes de classe en Objective-C. Par exemple, le code suivant ajoute une méthode nommée `ScrollToBottom` à la classe `UITextView`, qui est, à son tour, une classe managée liée à la classe `UITextView` Objective-C à partir de UIKit :

```csharp
public static class UITextViewExtensions
{
    public static void ScrollToBottom (this UITextView textView)
    {
        // code to scroll textView
    }
}
```

Ainsi, quand une instance d’un argument `UITextView` sera créée dans le code, la méthode sera disponible dans la liste de saisie semi-automatique, comme indiqué ci-dessous :

 ![](primer-images/01-extensionmethodintellisense.png "The method available in the autocomplete")

Quand la méthode d’extension est appelée, l’instance est passée à l’argument, tel que `textView` dans cet exemple.

### <a name="frameworks-vs-assemblies"></a>Frameworks et assemblys

Objective-C empaquette des classes associées dans des répertoires spéciaux appelés des frameworks. Toutefois, en C# et en .NET, les assemblys sont utilisés pour fournir des bits réutilisables de code précompilé. Dans les environnements en dehors d’iOS, les assemblys contiennent le code de langage intermédiaire (IL) qui est compilé « juste-à-temps » (JIT) au moment de l’exécution. Toutefois, Apple n’autorise pas JIT dans les applications iOS. Par conséquent, le code C# ciblant iOS avec Xamarin est compilé en avance (AOT), produisant ainsi un seul exécutable Unix et les fichiers de métadonnées qui sont inclus dans l’ensemble d’applications.

### <a name="selectors-vs-named-parameters"></a>Sélecteurs et paramètres nommés

Les méthodes Objective-C incluent par nature les noms des paramètres dans les sélecteurs. Par exemple, un sélecteur tel que `AddCrayon:WithColor:` indique clairement ce que signifie chaque paramètre quand il est utilisé dans le code. C# peut éventuellement prendre aussi en charge les arguments nommés.

Par exemple, un code similaire en C# utilisant des arguments nommés pourrait se présenter comme suit :

```csharp
AddCrayon (crayon: myCrayon, color: UIColor.Blue);
```

Bien que C# ait ajouté cette prise en charge dans la version 4.0 du langage, dans la pratique, elle est rarement utilisée. Toutefois, si vous souhaitez être explicite dans votre code, il existe bien une prise en charge.

### <a name="headers-and-namespaces"></a>En-têtes et espaces de noms

En tant que surensemble de C, Objective-C utilise des en-têtes pour les déclarations publiques, qui sont séparés du fichier d’implémentation. C# n’utilise pas de fichiers d’en-tête. Contrairement à Objective-C, le code C# est contenu dans des espaces de noms. Si vous souhaitez inclure du code disponible dans un espace de noms, vous devez ajouter une directive using au début du fichier d’implémentation ou qualifier le type avec l’espace de noms complet.

Par exemple, le code suivant inclut l’espace de noms `UIKit`, qui rend disponible chaque classe de cet espace de noms pour l’implémentation :

```csharp
using UIKit
namespace MyAppNamespace
{
    // implementation of classes
}
```

En outre, le mot clé de l’espace de noms dans le code ci-dessus définit l’espace de noms utilisé pour le fichier d’implémentation lui-même. Si plusieurs fichiers d’implémentation partagent le même espace de noms, il est inutile d’inclure l’espace de noms dans une directive using, car cela est implicite.

### <a name="properties"></a>Propriétés

Objective-C et C# incluent tous deux le concept de propriétés pour fournir une abstraction de haut niveau autour des méthodes d’accesseur. En Objective-C, la directive du compilateur @property est utilisée pour générer efficacement des méthodes d’accesseur. En revanche, C# prend en charge les propriétés dans le langage même. Une propriété C# peut être implémentée soit à l’aide d’un style plus long qui accède à un champ de stockage, soit à l’aide d’une syntaxe de propriété automatique plus courte, comme indiqué dans les exemples suivants :

```csharp
// automatic property syntax
public string Name { get; set; }

// property implemented with a backing field
string address;
public string Address {
    get {
        // could add additional code here
        return address;
    }
    set {
        address = value;
    }
}
```

### <a name="static-keyword"></a>Mot clé Static

Le mot clé *Static* a une signification très différente en Objective-C et en C#. En Objective-C, les fonctions statiques servent à limiter l’étendue d’une fonction au fichier actuel. En revanche, en C#, l’étendue est gérée à travers les mots clés *publics*, *privés* et *internes*.

Quand le mot clé static est appliqué à une variable en Objective-C, la variable conserve sa valeur entre les appels de fonction.

C# possède également un mot clé static. Quand il est appliqué à une méthode, il effectue efficacement la même chose que le modificateur `+` en Objectif-C, à savoir qu’il crée une méthode de classe. De même, quand il est appliqué à d’autres constructions telles que des champs, des propriétés ou des événements, il inclut ces derniers dans le type dans lequel ils sont déclarés plutôt qu’avec une instance quelconque de ce type. Vous pouvez également rendre une classe statique, dans laquelle toutes les méthodes définies doivent également être statiques.

### <a name="nsarray-vs-list-initialization"></a>NSArray et initialisation de liste

Objective-C inclut désormais la syntaxe de littéral à utiliser avec `NSArray`, ce qui simplifie son initialisation. C# a toutefois un type plus riche appelé un `List`, qui est *générique*, ce qui signifie que le type contenu par la liste peut être fourni par le code qui crée la liste (comme les modèles en C++). En outre, les listes prennent en charge la syntaxe d’initialisation d’automatique comme indiqué ci-dessous :

```csharp
MyClass object1 = new MyClass ();
MyClass object2 = new MyClass ();
List<MyClass> myList = new List<MyClass>{ object1, object2 };
```

### <a name="blocks-vs-lambda-expressions"></a>Blocs et expressions lambda

Objective-C utilise des *blocs* pour créer des fermetures qui vous permettent de créer une fonction inline pouvant utiliser l’état dans lequel elle est confinée. C# a un concept similaire à travers l’utilisation d’expressions lambda. En C#, les expressions lambda sont créées avec l’opérateur `=>`, comme indiqué ci-dessous :

```csharp
(args) => {
    //  implementation code
};
```

Pour plus d’informations sur les expressions lambda, consultez le [Guide de programmation C#](https://msdn.microsoft.com/library/vstudio/bb397687.aspx) de Microsoft.

## <a name="summary"></a>Récapitulatif

Cet article a comparé plusieurs fonctionnalités de langage entre Objective-C et C#. Dans certains cas, il a évoqué des fonctionnalités analogues qui existent dans les deux langages, telles que les blocs pour les expressions lambda et les catégories pour les méthodes d’extension. Il a également souligné certaines différences entre les langages, par exemple au niveau des espaces de noms en C# et de la signification du mot clé static.
