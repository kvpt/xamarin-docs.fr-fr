---
title: Liaison des bibliothèques objective-C
description: Ce document fournit une vue d’ensemble de haut niveau de la C# création de liaisons au code Objective-C, en expliquant comment lier des événements, des méthodes, des contrôles personnalisés, etc.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: 6841e94ad13357c51e6ccf59e35c659dfb9954aa
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016293"
---
# <a name="binding-objective-c-libraries"></a>Liaison des bibliothèques objective-C

Lorsque vous utilisez Xamarin. iOS ou Xamarin. Mac, vous pouvez rencontrer des cas où vous souhaitez consommer une bibliothèque objective-C tierce. Dans ces situations, vous pouvez utiliser des projets de liaison Xamarin pour C# créer une liaison aux bibliothèques objective-C natives. Le projet utilise les mêmes outils que ceux que nous utilisons pour intégrer les API iOS et C#Mac à.

Ce document décrit comment lier des API objective-C, si vous liez uniquement des API C, vous devez utiliser le mécanisme .NET standard pour cela, [l’infrastructure P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Vous trouverez plus d’informations sur la liaison statique d’une bibliothèque C sur la page [liaison de bibliothèques natives](~/ios/platform/native-interop.md) .

Consultez notre [Guide de référence des types de liaison](~/cross-platform/macios/binding/binding-types-reference.md)compagnons.
En outre, si vous souhaitez en savoir plus sur ce qui se passe en coulisses, consultez notre page de présentation de la [liaison](~/cross-platform/macios/binding/overview.md) .

Les liaisons peuvent être générées pour les bibliothèques iOS et Mac.
Cette page explique comment travailler sur une liaison iOS, mais les liaisons Mac sont très similaires.

**Exemple de code pour iOS**

Vous pouvez utiliser l’exemple de projet de [liaison iOS](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) pour expérimenter des liaisons.

<a name="Getting_Started" />

## <a name="getting-started"></a>Bien démarrer

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Le moyen le plus simple de créer une liaison consiste à créer un projet de liaison Xamarin. iOS.
Pour ce faire, vous pouvez Visual Studio pour Mac en sélectionnant le type de projet, bibliothèque de **> iOS > bibliothèques de liaisons**:

[![](objective-c-libraries-images/00-sml.png "Do this from Visual Studio for Mac by selecting the project type, iOS Library Bindings Library")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le moyen le plus simple de créer une liaison consiste à créer un projet de liaison Xamarin. iOS.
Pour ce faire, vous pouvez utiliser Visual Studio sur Windows en sélectionnant le type de projet, **visual C# > iOS > bibliothèques de liaisons (IOS)** :

[![](objective-c-libraries-images/00vs-sml.png "iOS Bindings Library iOS")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Remarque : les projets de liaison pour **Xamarin. Mac** sont uniquement pris en charge dans les Visual Studio pour Mac.

-----

Le projet généré contient un modèle de petite taille que vous pouvez modifier, il contient deux fichiers : `ApiDefinition.cs` et `StructsAndEnums.cs`.

Le `ApiDefinition.cs` est l’emplacement où vous allez définir le contrat d’API. il s’agit du fichier qui décrit comment l’API objective- C#C sous-jacente est projetée dans. La syntaxe et le contenu de ce fichier sont le sujet principal de la discussion sur ce document et son contenu est limité aux C# interfaces et C# aux déclarations de délégué. Le fichier `StructsAndEnums.cs` est le fichier dans lequel vous allez entrer les définitions requises par les interfaces et les délégués. Cela comprend les valeurs d’énumération et les structures que votre code peut utiliser.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Liaison d’une API

Pour effectuer une liaison complète, vous devez comprendre la définition de l’API objective-C et vous familiariser avec les instructions de conception de .NET Framework.

Pour lier votre bibliothèque, vous démarrez généralement avec un fichier de définition d’API. Un fichier de définition d’API est C# simplement un fichier source C# qui contient des interfaces qui ont été annotées avec quelques attributs permettant de piloter la liaison.  Ce fichier définit le contrat entre C# et objective-C.

Par exemple, il s’agit d’un fichier d’API trivial pour une bibliothèque :

```csharp
using Foundation;

namespace Cocos2D {
  [BaseType (typeof (NSObject))]
  interface Camera {
    [Static, Export ("getZEye")]
    nfloat ZEye { get; }

    [Export ("restore")]
    void Restore ();

    [Export ("locate")]
    void Locate ();

    [Export ("setEyeX:eyeY:eyeZ:")]
    void SetEyeXYZ (nfloat x, nfloat y, nfloat z);

    [Export ("setMode:")]
    void SetMode (CameraMode mode);
  }
}
```

L’exemple ci-dessus définit une classe appelée `Cocos2D.Camera` qui dérive du type de base `NSObject` (ce type provient d' `Foundation.NSObject`) et qui définit une propriété statique (`ZEye`), deux méthodes qui ne prennent pas d’arguments et une méthode qui accepte trois arguments.

Une présentation détaillée du format du fichier API et des attributs que vous pouvez utiliser est décrite dans la section [fichier de définition d’API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) ci-dessous.

Pour produire une liaison complète, vous devez généralement gérer quatre composants :

- Fichier de définition de l’API (`ApiDefinition.cs` dans le modèle).
- Facultatif : tous les enums, types et structs requis par le fichier de définition d’API (`StructsAndEnums.cs` dans le modèle).
- Facultatif : sources supplémentaires qui peuvent développer la liaison générée ou fournir une API plus C# conviviale (tous C# les fichiers que vous ajoutez au projet).
- Bibliothèque Native que vous liez.

Ce graphique montre la relation entre les fichiers :

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "This chart shows the relationship between the files")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Le fichier de définition d’API ne contient que des espaces de noms et des définitions d’interface (avec tous les membres qu’une interface peut contenir) et ne doit pas contenir des classes, des énumérations, des délégués ou des structs. Le fichier de définition d’API est simplement le contrat qui sera utilisé pour générer l’API.

Tout code supplémentaire dont vous avez besoin, comme les énumérations ou les classes de prise en charge, doit être hébergé sur un fichier distinct. dans l’exemple ci-dessus, « CameraMode » est une valeur d’énumération qui n’existe pas dans le fichier CS et qui doit être hébergée dans un fichier séparé, par exemple `StructsAndEnums.cs`:

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

Le fichier `APIDefinition.cs` est associé à la classe `StructsAndEnum` et sont utilisés pour générer la liaison principale de la bibliothèque. Vous pouvez utiliser la bibliothèque résultante telle quelle, mais en général, vous souhaiterez paramétrer la bibliothèque résultante pour C# ajouter des fonctionnalités pour l’avantage de vos utilisateurs. Il peut s’agir, par exemple, de C# l’implémentation d’une méthode `ToString()`, de fournir des indexeurs, d’ajouter des conversions implicites vers et à partir de certains types natifs ou de fournir des versions fortement typées de certaines méthodes. Ces améliorations sont stockées dans des C# fichiers supplémentaires. Ajoutez simplement les C# fichiers à votre projet et ils seront inclus dans ce processus de génération.

Cela montre comment implémenter le code dans votre fichier `Extra.cs`. Notez que vous utiliserez des classes partielles, car elles augmentent les classes partielles générées à partir de la combinaison de la `ApiDefinition.cs` et de la liaison de noyau `StructsAndEnums.cs` :

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

La génération de la bibliothèque produira votre liaison native.

Pour terminer cette liaison, vous devez ajouter la bibliothèque Native au projet.  Pour ce faire, vous pouvez ajouter la bibliothèque native à votre projet, soit en faisant glisser la bibliothèque native de Finder vers le projet dans l’Explorateur de solutions, soit en cliquant avec le bouton droit sur le projet et en sélectionnant **ajouter** > **Ajouter des fichiers** pour sélectionner la bibliothèque native.
Les bibliothèques natives par convention commencent par le mot « lib » et se terminent par l’extension « . a ». Dans ce cas, Visual Studio pour Mac ajoute deux fichiers : le fichier. a et un fichier rempli C# automatiquement qui contient des informations sur ce que contient la bibliothèque Native :

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Native libraries by convention start with the word lib and end with the extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Le contenu du fichier `libMagicChord.linkwith.cs` contient des informations sur la façon dont cette bibliothèque peut être utilisée et indique à votre IDE d’empaqueter ce binaire dans le fichier DLL résultant :

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Informations complètes sur l’utilisation de l' [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
les attributs sont documentés dans le [Guide de référence des types de liaison](~/cross-platform/macios/binding/binding-types-reference.md).

À présent, lorsque vous générez le projet, vous obtenez un fichier `MagicChords.dll` qui contient à la fois la liaison et la bibliothèque native. Vous pouvez distribuer ce projet ou la DLL résultante à d’autres développeurs pour leur propre usage.

Il peut arriver que vous ayez besoin de quelques valeurs d’énumération, de définitions déléguées ou d’autres types. Ne les placez pas dans le fichier de définitions d’API, car il s’agit simplement d’un contrat

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>Fichier de définition de l’API

Le fichier de définition d’API se compose d’un certain nombre d’interfaces. Les interfaces dans la définition de l’API sont transformées en une déclaration de classe et doivent être décorées avec l’attribut [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) pour spécifier la classe de base de la classe.

Vous vous demandez peut-être pourquoi nous n’avons pas utilisé des classes au lieu d’interfaces pour la définition de contrat. Nous avons choisi interfaces, car cela nous permettait d’écrire le contrat pour une méthode sans avoir à fournir un corps de méthode dans le fichier de définition d’API, ou à fournir un corps qui devait lever une exception ou retourner une valeur significative.

Mais étant donné que nous utilisons l’interface comme squelette pour générer une classe, nous devions recourir à la décoration de différentes parties du contrat avec des attributs pour piloter la liaison.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Méthodes de liaison

La liaison la plus simple consiste à lier une méthode. Déclarez simplement une méthode dans l’interface avec C# les conventions de nommage et décorez la méthode avec la [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
. L’attribut [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) est ce qui lie C# votre nom au nom objective-C dans le runtime Xamarin. iOS. Le paramètre de l' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribut est le nom du sélecteur objective-C. Voici quelques exemples :

```csharp
// A method, that takes no arguments
[Export ("refresh")]
void Refresh ();

// A method that takes two arguments and return the result
[Export ("add:and:")]
nint Add (nint a, nint b);

// A method that takes a string
[Export ("draw:atColumn:andRow:")]
void Draw (string text, nint column, nint row);
```

Les exemples ci-dessus montrent comment vous pouvez lier des méthodes d’instance. Pour lier des méthodes statiques, vous devez utiliser l’attribut [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) , comme suit :

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Cela est nécessaire, car le contrat fait partie d’une interface, et les interfaces n’ont aucune notion de déclarations statiques vs instance. il est donc nécessaire de réutiliser les attributs. Si vous souhaitez masquer une méthode particulière de la liaison, vous pouvez décorer la méthode avec l’attribut [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) .

La commande `btouch-native` présente des vérifications de paramètres de référence qui ne sont pas null. Si vous souhaitez autoriser les valeurs NULL pour un paramètre particulier, utilisez l' [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
sur le paramètre, comme suit :

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Lors de l’exportation d’un type référence, avec le mot clé [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) vous pouvez également spécifier la sémantique d’allocation. Cela est nécessaire pour s’assurer qu’aucune donnée n’est divulguée.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propriétés de liaison

Tout comme les méthodes, les propriétés objective-C sont liées à l’aide de l' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attribute et mappent C# directement aux propriétés. Tout comme les méthodes, les propriétés peuvent être décorées avec le [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
et le [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
.

Lorsque vous utilisez l’attribut [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) sur une propriété en coulisses, btouch-Native lie en fait deux méthodes : l’accesseur get et l’accesseur Set. Le nom que vous fournissez pour l’exportation est le **BaseName** et l’accesseur Set est calculé en ajoutant le mot « set » en préfixe, en mettant la première lettre du **BaseName** en majuscules et en faisant en sorte que le sélecteur prenne un argument. Cela signifie que `[Export ("label")]` appliquée sur une propriété lie en fait les méthodes objective-C « label » et « setLabel : ».

Parfois, les propriétés objective-C ne suivent pas le modèle décrit ci-dessus et le nom est remplacé manuellement. Dans ce cas, vous pouvez contrôler la façon dont la liaison est générée à l’aide de l' [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
attribut sur la méthode Getter ou Setter, par exemple :

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Cela lie ensuite « isMenuVisible » et « setMenuVisible : ». Si vous le souhaitez, une propriété peut être liée à l’aide de la syntaxe suivante :

```csharp
[Category, BaseType(typeof(UIView))]
interface UIView_MyIn
{
  [Export ("name")]
  string Name();

  [Export("setName:")]
  void SetName(string name);
}
```

Où les accesseurs get et setter sont définis explicitement comme dans les liaisons `name` et `setName` ci-dessus.

En plus de la prise en charge des propriétés statiques à l’aide de [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), vous pouvez décorer des propriétés thread-static avec [`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), par exemple :

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Tout comme les méthodes autorisant l’indicateur de certains paramètres avec [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), vous pouvez appliquer [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
à une propriété pour indiquer que la valeur null est une valeur valide pour la propriété, par exemple :

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Le paramètre [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) peut également être spécifié directement sur la méthode setter :

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Avertissements relatifs à la liaison de contrôles personnalisés

Les avertissements suivants doivent être pris en compte lors de la configuration de la liaison pour un contrôle personnalisé :

1. Les **Propriétés de liaison doivent être statiques** : lors de la définition de la liaison de propriétés, l’attribut [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) doit être utilisé.
2. Les **noms de propriété doivent correspondre exactement** : le nom utilisé pour lier la propriété doit correspondre exactement au nom de la propriété dans le contrôle personnalisé.
3. Les **types de propriété doivent correspondre exactement** -le type de variable utilisé pour lier la propriété doit correspondre exactement au type de la propriété dans le contrôle personnalisé.
4. **Les points d’arrêt et les points d’arrêt Getter/Setter** placés dans les méthodes getter ou Setter de la propriété ne seront jamais atteints.
5. **Observer les rappels** : vous devrez utiliser des rappels d’observation pour être averti des modifications apportées aux valeurs de propriété des contrôles personnalisés.

Si vous ne respectez pas l’un des avertissements mentionnés ci-dessus, la liaison échoue en silence au moment de l’exécution.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Propriétés et modèle mutable objective-C

Les frameworks objective-C utilisent un idiome dans lequel certaines classes sont immuables avec une sous-classe mutable. Par exemple `NSString` est la version immuable, tandis que `NSMutableString` est la sous-classe qui autorise la mutation.

Dans ces classes, il est courant de voir que la classe de base immuable contient des propriétés avec un accesseur Get, mais pas d’accesseur Set. Et pour la version mutable pour introduire l’accesseur Set. Dans la mesure où cela n’est C#pas vraiment possible avec, nous avons dû mapper cet idiome dans un idiome C#qui fonctionnait avec.

La façon dont ce est mappé C# est l’ajout de l’accesseur get et de l’accesseur Set à la classe de base, mais le marquage de la méthode setter avec un [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
.

Ensuite, sur la sous-classe mutable, vous utilisez la [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
attribut sur la propriété pour garantir que la propriété substitue réellement le comportement du parent.

Exemple :

```csharp
[BaseType (typeof (NSObject))]
interface MyTree {
    string Name { get; [NotImplemented] set; }
}

[BaseType (typeof (MyTree))]
interface MyMutableTree {
    [Override]
    string Name { get; set; }
}
```

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Constructeurs de liaison

L’outil `btouch-native` génère automatiquement quatre constructeurs dans votre classe, pour une classe donnée `Foo`, il génère :

- `Foo ()`: le constructeur par défaut (est mappé au constructeur « init » de Objective-C)
- `Foo (NSCoder)`: le constructeur utilisé pendant la désérialisation des fichiers de plume (est mappé au constructeur « initWithCoder : » de Objective-C).
- `Foo (IntPtr handle)`: le constructeur pour la création basée sur des handles, qui est appelé par le runtime quand le runtime doit exposer un objet managé à partir d’un objet non managé.
- `Foo (NSEmptyFlag)`: utilisé par les classes dérivées pour empêcher une double initialisation.

Pour les constructeurs que vous définissez, ils doivent être déclarés à l’aide de la signature suivante à l’intérieur de la définition de l’interface : ils doivent retourner une valeur `IntPtr` et le nom de la méthode doit être le constructeur. Par exemple, pour lier le constructeur `initWithFrame:`, c’est ce que vous utiliseriez :

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocoles de liaison

Comme décrit dans le document de conception d’API, dans la section discussion sur les [modèles et les protocoles](~/ios/internals/api-design/index.md#models), Xamarin. iOS mappe les protocoles objective-C dans des classes qui ont été marquées avec le [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
. Cela est généralement utilisé lors de l’implémentation de classes déléguées objective-C.

La grande différence entre une classe liée normale et une classe déléguée est que la classe déléguée peut avoir une ou plusieurs méthodes facultatives.

Par exemple, considérons la classe `UIKit` `UIAccelerometerDelegate`, c’est la manière dont elle est liée dans Xamarin. iOS :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Étant donné qu’il s’agit d’une méthode facultative sur la définition de `UIAccelerometerDelegate` il n’y a rien d’autre à faire. Toutefois, si une méthode est nécessaire sur le protocole, vous devez ajouter le [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
attribut à la méthode. Cela forcera l’utilisateur de l’implémentation à fournir en fait un corps pour la méthode.

En général, les protocoles sont utilisés dans les classes qui répondent aux messages. Cette opération est généralement effectuée en Objective-C en affectant à la propriété « Delegate » une instance d’un objet qui répond aux méthodes dans le protocole.

La Convention dans Xamarin. iOS consiste à prendre en charge à la fois le style objective-C, faiblement couplé, où une instance d’un `NSObject` peut être assignée au délégué et pour exposer également une version fortement typée de celui-ci. Pour cette raison, nous fournissons généralement une propriété `Delegate` qui est fortement typée et une `WeakDelegate` faiblement typée. Nous lions généralement la version faiblement typée avec [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), et nous utilisons l’attribut [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) pour fournir la version fortement typée.

Cela montre comment nous avons lié la classe `UIAccelerometer` :

```csharp
[BaseType (typeof (NSObject))]
interface UIAccelerometer {
        [Static] [Export ("sharedAccelerometer")]
        UIAccelerometer SharedAccelerometer { get; }

        [Export ("updateInterval")]
        double UpdateInterval { get; set; }

        [Wrap ("WeakDelegate")]
        UIAccelerometerDelegate Delegate { get; set; }

        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }
}
```

<a name="iOS7ProtocolSupport" />

**Nouveautés de MonoTouch 7,0**

À partir de MonoTouch 7,0, une nouvelle fonctionnalité de liaison de protocole améliorée a été incorporée.  Grâce à cette nouvelle prise en charge, il est plus simple d’utiliser des idiomes objective-C pour adopter un ou plusieurs protocoles dans une classe donnée.

Pour chaque définition de protocole `MyProtocol` en Objective-C, il existe désormais une interface `IMyProtocol` qui répertorie toutes les méthodes requises du protocole, ainsi qu’une classe d’extension qui fournit toutes les méthodes facultatives.  Ce qui précède, combiné à la nouvelle prise en charge de l’éditeur de Xamarin Studio, permet aux développeurs d’implémenter des méthodes de protocole sans avoir à utiliser les sous-classes distinctes des classes de modèle abstrait précédentes.

Toute définition contenant l’attribut [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) génère en fait trois classes de prise en charge qui améliorent considérablement la façon dont vous consommez les protocoles :

```csharp
// Full method implementation, contains all methods
class MyProtocol : IMyProtocol {
    public void Say (string msg);
    public void Listen (string msg);
}

// Interface that contains only the required methods
interface IMyProtocol: INativeObject, IDisposable {
    [Export ("say:")]
    void Say (string msg);
}

// Extension methods
static class IMyProtocol_Extensions {
    public static void Optional (this IMyProtocol this, string msg);
    }
}
```

L' **implémentation de classe** fournit une classe abstraite complète qui vous permet de substituer des méthodes individuelles de et d’obtenir une sécurité de type complète.  Toutefois, en C# raison de la non-prise en charge de l’héritage multiple, il existe des scénarios dans lesquels vous devrez peut-être avoir une classe de base différente, mais vous souhaitez toujours implémenter une interface, c’est là que le

La **définition d’interface** générée entre en entrée.  Il s’agit d’une interface qui contient toutes les méthodes requises du protocole.  Cela permet aux développeurs qui souhaitent implémenter votre protocole d’implémenter simplement l’interface.  Le runtime inscrit automatiquement le type comme adoptant le protocole.

Notez que l’interface répertorie uniquement les méthodes requises et expose les méthodes facultatives.  Cela signifie que les classes qui adoptent le protocole obtiennent une vérification de type complète pour les méthodes requises, mais doivent recourir à un typage faible (manuellement à l’aide de [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attributs et correspondant à la signature) pour les méthodes de protocole facultatives.

Pour faciliter l’utilisation d’une API qui utilise des protocoles, l’outil de liaison produira également une classe de méthode extensions qui expose toutes les méthodes facultatives.  Cela signifie que tant que vous consommez une API, vous pouvez traiter les protocoles comme ayant toutes les méthodes.

Si vous souhaitez utiliser les définitions de protocole dans votre API, vous devrez écrire le squelette des interfaces vides dans la définition de votre API.  Si vous souhaitez utiliser MyProtocol dans une API, vous devez effectuer cette opération :

```csharp
[BaseType (typeof (NSObject))]
[Model, Protocol]
interface MyProtocol {
    // Use [Abstract] when the method is defined in the @required section
    // of the protocol definition in Objective-C
    [Abstract]
    [Export ("say:")]
    void Say (string msg);

    [Export ("listen")]
    void Listen ();
}

interface IMyProtocol {}

[BaseType (typeof(NSObject))]
interface MyTool {
    [Export ("getProtocol")]
    IMyProtocol GetProtocol ();
}
```

L’expression ci-dessus est nécessaire, car au moment de la liaison, le `IMyProtocol` n’existe pas, c’est pourquoi vous devez fournir une interface vide.

#### <a name="adopting-protocol-generated-interfaces"></a>Adoption d’interfaces générées par le protocole

Chaque fois que vous implémentez l’une des interfaces générées pour les protocoles, comme suit :

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L’implémentation pour les méthodes d’interface est automatiquement exportée avec le nom approprié. il est donc équivalent à ce qui suit :

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Peu importe si l’interface est implémentée implicitement ou explicitement.

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Extensions de classe de liaison

En Objective-C, il est possible d’étendre les classes avec de nouvelles méthodes, C#similaires dans l’esprit pour les méthodes d’extension de. Quand l’une de ces méthodes est présente, vous pouvez utiliser la [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attribut pour marquer la méthode comme étant le récepteur du message objective-C.

Par exemple, dans Xamarin. iOS, nous avons lié les méthodes d’extension définies sur `NSString` lorsque `UIKit` est importé en tant que méthodes dans le `NSStringDrawingExtensions`, comme suit :

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Liaison des listes d’arguments objective-C

Objective-C prend en charge les arguments variadiques. Exemple :

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Pour appeler cette méthode à C# partir de, vous devez créer une signature semblable à celle-ci :

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Cela déclare la méthode comme étant interne, en masquant l’API ci-dessus aux utilisateurs, mais en l’exposant à la bibliothèque. Vous pouvez ensuite écrire une méthode comme celle-ci :

```csharp
public void AppendWorkers(params Worker[] workers)
{
    if (workers == null)
         throw new ArgumentNullException ("workers");

    var pNativeArr = Marshal.AllocHGlobal(workers.Length * IntPtr.Size);
    for (int i = 1; i < workers.Length; ++i)
        Marshal.WriteIntPtr (pNativeArr, (i - 1) * IntPtr.Size, workers[i].Handle);

    // Null termination
    Marshal.WriteIntPtr (pNativeArr, (workers.Length - 1) * IntPtr.Size, IntPtr.Zero);

    // the signature for this method has gone from (IntPtr, IntPtr) to (Worker, IntPtr)
    WorkerManager.AppendWorkers(workers[0], pNativeArr);
    Marshal.FreeHGlobal(pNativeArr);
}
```

<a name="Binding_Fields" />

### <a name="binding-fields"></a>Champs de liaison

Parfois, vous souhaiterez accéder aux champs publics qui ont été déclarés dans une bibliothèque.

En général, ces champs contiennent des chaînes ou des valeurs entières qui doivent être référencées. Ils sont couramment utilisés comme chaîne représentant une notification spécifique et en tant que clés dans les dictionnaires.

Pour lier un champ, ajoutez une propriété à votre fichier de définition d’interface et décorez la propriété avec l’attribut [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) . Cet attribut accepte un paramètre : le nom C du symbole à rechercher. Exemple :

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Si vous souhaitez encapsuler différents champs dans une classe statique qui ne dérive pas de `NSObject`, vous pouvez utiliser l' [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
sur la classe, comme suit :

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

L’exemple ci-dessus génère une `LonelyClass` qui ne dérive pas de `NSObject` et qui contient une liaison au `NSSomeEventNotification`
`NSString` exposée en tant que `NSString`.

L’attribut [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) peut être appliqué aux types de données suivants :

- références `NSString` (propriétés en lecture seule)
- références `NSArray` (propriétés en lecture seule)
- 32 bits int (`System.Int32`)
- 64 bits int (`System.Int64`)
- virgule flottante 32 bits (`System.Single`)
- virgule flottante 64 bits (`System.Double`)
- `System.Drawing.SizeF`
- `CGSize`

En plus du nom de champ natif, vous pouvez spécifier le nom de la bibliothèque dans laquelle se trouve le champ, en passant le nom de la bibliothèque :

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Si vous effectuez une liaison statique, il n’y a aucune bibliothèque à lier. vous devez donc utiliser le nom de la `__Internal` :

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Liaisons, énumérations

Vous pouvez ajouter des `enum` directement dans vos fichiers de liaison pour faciliter leur utilisation dans les définitions d’API, sans utiliser un fichier source différent (qui doit être compilé à la fois dans les liaisons et le projet final).

Exemple :

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Il est également possible de créer vos propres énumérations pour remplacer `NSString` constantes. Dans ce cas, le Générateur créera **automatiquement** les méthodes pour convertir des valeurs enums et des constantes chaîne NSString pour vous.

Exemple :

```csharp
enum NSRunLoopMode {

    [DefaultEnumValue]
    [Field ("NSDefaultRunLoopMode")]
    Default,

    [Field ("NSRunLoopCommonModes")]
    Common,

    [Field (null)]
    Other = 1000
}

interface MyType {
    [Export ("performForMode:")]
    void Perform (NSString mode);

    [Wrap ("Perform (mode.GetConstant ())")]
    void Perform (NSRunLoopMode mode);
}
```

Dans l’exemple ci-dessus, vous pouvez choisir de décorer `void Perform (NSString mode);` avec un attribut [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) . Cela va **Masquer** l’API basée sur des constantes de vos consommateurs de liaison.

Toutefois, cela limiterait la sous-classe du type, car l’API plus attrayante utilise un attribut [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) . Ces méthodes générées ne sont pas `virtual`, c’est-à-dire que vous ne pouvez pas les remplacer, ce qui peut être un bon choix.

Une alternative consiste à marquer la définition d’origine, basée sur `NSString`, comme `[Protected]`. Cela permettra au sous-classe de fonctionner, si nécessaire, et la version de wrap’ed continuera de fonctionner et d’appeler la méthode substituée.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Liaison de `NSValue`, `NSNumber`et `NSString` à un meilleur type

L’attribut [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) permet de lier des `NSNumber`, `NSValue` et `NSString`(enums) à C# des types plus précis. L’attribut peut être utilisé pour créer une API .NET plus précise et plus précise sur l’API native.

Vous pouvez décorer des méthodes (sur une valeur de retour), des paramètres et des propriétés avec [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). La seule restriction est que votre membre **ne doit pas** se trouver à l’intérieur d’un [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
ou [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interface.

Exemple :

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Sortie :

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

En interne, nous allons effectuer les conversions de `bool?` <-> `NSNumber` et `CGRect` <-> .`NSValue`

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) prend également en charge les tableaux de `NSNumber` `NSValue` et `NSString`(enums).

Exemple :

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Sortie :

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` est un `NSString` enum sauvegardé, nous allons extraire la valeur de `NSString` appropriée et gérer la conversion de type.

Pour voir les types de conversion pris en charge, consultez la documentation [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) .

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notifications de liaison

Les notifications sont des messages qui sont publiés dans le `NSNotificationCenter.DefaultCenter` et sont utilisés comme mécanisme pour diffuser des messages d’une partie de l’application vers une autre. Les développeurs s’abonnent aux notifications généralement à l’aide de la méthode [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) de [NSNotificationCenter](xref:Foundation.NSNotificationCenter). Lorsqu’une application publie un message dans le centre de notifications, elle contient généralement une charge utile stockée dans le dictionnaire [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) . Ce dictionnaire est faiblement typé et l’obtention d’informations à partir de celui-ci est sujette aux erreurs, car les utilisateurs doivent généralement lire la documentation qui contient les clés disponibles sur le dictionnaire et les types de valeurs qui peuvent être stockées dans le dictionnaire. La présence de clés est parfois utilisée comme une valeur booléenne.

Le générateur de liaisons Xamarin. iOS offre aux développeurs la prise en charge de la liaison des notifications. Pour ce faire, vous devez définir le [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
attribut sur une propriété qui a été également marquée avec un [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
Property (elle peut être publique ou privée).

Cet attribut peut être utilisé sans arguments pour les notifications qui ne contiennent pas de charge utile, ou vous pouvez spécifier un `System.Type` qui référence une autre interface dans la définition de l’API, en général avec le nom se terminant par « EventArgs ». Le générateur transformera l’interface en une classe qui sous-classe `EventArgs` et inclura toutes les propriétés qui y sont listées. L’attribut [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) doit être utilisé dans la classe EventArgs pour répertorier le nom de la clé utilisée pour rechercher le dictionnaire objective-C afin d’extraire la valeur.

Exemple :

```csharp
interface MyClass {
    [Notification]
    [Field ("MyClassDidStartNotification")]
    NSString DidStartNotification { get; }
}
```

Le code ci-dessus génère une classe imbriquée `MyClass.Notifications` avec les méthodes suivantes :

```csharp
public class MyClass {
   [..]
   public Notifications {
      public static NSObject ObserveDidStart (EventHandler<NSNotificationEventArgs> handler)
   }
}
```

Les utilisateurs de votre code peuvent alors facilement s’abonner aux notifications publiées dans le [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) à l’aide d’un code tel que celui-ci :

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

La valeur renvoyée par `ObserveDidStart` peut être utilisée pour arrêter facilement la réception de notifications, comme suit :

```csharp
token.Dispose ();
```

Vous pouvez appeler [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) et passer le jeton. Si votre notification contient des paramètres, vous devez spécifier une interface d’assistance `EventArgs`, comme suit :

```csharp
interface MyClass {
    [Notification (typeof (MyScreenChangedEventArgs)]
    [Field ("MyClassScreenChangedNotification")]
    NSString ScreenChangedNotification { get; }
}

// The helper EventArgs declaration
interface MyScreenChangedEventArgs {
    [Export ("ScreenXKey")]
    nint ScreenX { get; set; }

    [Export ("ScreenYKey")]
    nint ScreenY { get; set; }

    [Export ("DidGoOffKey")]
    [ProbePresence]
    bool DidGoOff { get; }
}
```

L’opération ci-dessus génère une classe `MyScreenChangedEventArgs` avec les propriétés `ScreenX` et `ScreenY` qui extraient les données du dictionnaire [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) en utilisant respectivement les clés « ScreenXKey » et « ScreenYKey » et appliquent les conversions appropriées. L’attribut `[ProbePresence]` est utilisé pour que le générateur sonde si la clé est définie dans le `UserInfo`, au lieu d’essayer d’extraire la valeur. Cela est utilisé dans les cas où la présence de la clé est la valeur (généralement pour les valeurs booléennes).

Cela vous permet d’écrire du code tel que celui-ci :

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Catégories de liaison

Les catégories sont un mécanisme objective-C utilisé pour étendre l’ensemble de méthodes et de propriétés disponibles dans une classe.   Dans la pratique, ils sont utilisés pour étendre les fonctionnalités d’une classe de base (par exemple `NSObject`) quand un Framework spécifique est lié (par exemple `UIKit`), ce qui rend leurs méthodes disponibles, mais uniquement si la nouvelle infrastructure est liée.   Dans d’autres cas, ils sont utilisés pour organiser les fonctionnalités dans une classe par fonctionnalité.   Elles sont similaires à l’esprit C# pour les méthodes d’extension. Voici à quoi ressemble une catégorie dans objective-C :

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

L’exemple ci-dessus, s’il est trouvé sur une bibliothèque, étend les instances de `UIView` avec la méthode `makeBackgroundRed`.

Pour les lier, vous pouvez utiliser l’attribut [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) sur une définition d’interface.  Lors de l’utilisation de l' [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attribut, la signification de l' [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
l’attribut change d’être utilisé pour spécifier la classe de base à étendre, en tant que type à étendre.

L’exemple suivant montre comment les extensions `UIView` sont liées et converties en C# méthodes d’extension :

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

L’opération ci-dessus crée une `MyUIViewExtension` une classe qui contient la méthode d’extension `MakeBackgroundRed`.  Cela signifie que vous pouvez maintenant appeler « MakeBackgroundRed » sur n’importe quelle sous-classe de `UIView`, ce qui vous donne les mêmes fonctionnalités que celles que vous obtiendriez sur objective-C. Dans d’autres cas, les catégories sont utilisées pour étendre une classe système, mais pour organiser les fonctionnalités, uniquement à des fins de décoration.  Comme ceci :

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Bien que vous puissiez utiliser les [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attribut pour ce style de décoration de déclarations, vous pouvez également les ajouter à la définition de classe.  Les deux peuvent obtenir le même :

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Twitter {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);
}

[Category]
[BaseType (typeof (SocialNetworking))]
interface Facebook {
    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

Dans ce cas, il est plus simple de fusionner les catégories :

```csharp
[BaseType (typeof (NSObject))]
interface SocialNetworking {
    [Export ("postToTwitter:")]
    void PostToTwitter (Message message);

    [Export ("postToFacebook:andPicture:")]
    void PostToFacebook (Message message, UIImage picture);
}
```

<a name="Binding_Blocks" />

### <a name="binding-blocks"></a>Blocs de liaison

Les blocs sont une nouvelle construction introduite par Apple pour amener l’équivalent C# fonctionnel des méthodes anonymes à Objective-C. Par exemple, la classe `NSSet` expose désormais cette méthode :

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La description ci-dessus déclare une méthode appelée `enumerateObjectsUsingBlock:` qui accepte un argument nommé `block`. Ce bloc est semblable à une C# méthode anonyme en ce qu’il prend en charge la capture de l’environnement actuel (le pointeur « this », l’accès aux variables et paramètres locaux). La méthode ci-dessus dans `NSSet` appelle le bloc avec deux paramètres, un `NSObject` (la partie `id obj`) et un pointeur vers une partie booléenne (la `BOOL *stop`).

Pour lier ce type d’API à btouch, vous devez d’abord déclarer la signature de type de bloc C# en tant que délégué, puis la référencer à partir d’un point d’entrée d’API, comme ceci :

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

Désormais, votre code peut appeler votre fonction à C#partir de :

```csharp
var myset = new NSMutableSet ();
myset.Add (new NSString ("Foo"));

s.Enumerate (delegate (NSObject obj, ref bool stop){
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

Vous pouvez également utiliser des expressions lambda si vous préférez :

```csharp
var myset = new NSMutableSet ();
mySet.Add (new NSString ("Foo"));

s.Enumerate ((obj, stop) => {
    Console.WriteLine ("The first object is: {0} and stop is: {1}", obj, stop);
});
```

<a name="GeneratingAsync" />

### <a name="asynchronous-methods"></a>Méthodes asynchrones

Le générateur de liaisons peut transformer une certaine classe de méthodes en méthodes asynchrones (méthodes qui retournent une tâche ou une tâche&lt;T&gt;).

Vous pouvez utiliser les [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
attribut sur les méthodes qui retournent void et dont le dernier argument est un rappel.  Lorsque vous appliquez ce à une méthode, le générateur de liaisons génère une version de cette méthode avec le suffixe `Async`.  Si le rappel n’accepte aucun paramètre, la valeur de retour est un `Task`, si le rappel prend un paramètre, le résultat est un `Task<T>`.  Si le rappel prend plusieurs paramètres, vous devez définir la `ResultType` ou `ResultTypeName` pour spécifier le nom souhaité du type généré qui contiendra toutes les propriétés.

Exemple :

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

Le code ci-dessus génère la méthode LoadFile, ainsi que :

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Types forts de surface pour les paramètres NSDictionary faibles

À de nombreux endroits de l’API objective-C, les paramètres sont passés comme des API `NSDictionary` faiblement typées avec des clés et des valeurs spécifiques, mais celles-ci sont sujettes aux erreurs (vous pouvez passer des clés non valides et ne pas recevoir d’avertissements. vous pouvez transmettre des valeurs non valides et ne pas recevoir d’avertissements) et frustrantes pour les utiliser, car

La solution consiste à fournir une version fortement typée qui fournit la version fortement typée de l’API et en arrière-plan met en correspondance les différentes clés et valeurs sous-jacentes.

Par exemple, si l’API objective-C accepte une `NSDictionary` et qu’elle est documentée comme prenant la clé `XyzVolumeKey` qui prend une `NSNumber` avec une valeur de volume comprise entre 0,0 et 1,0 et une `XyzCaptionKey` qui prend une chaîne, vous souhaitez que vos utilisateurs aient une API intéressante qui ressemble à ceci :

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

La propriété `Volume` est définie en tant que valeur float Nullable, étant donné que la Convention en Objective-C ne requiert pas que ces dictionnaires aient la valeur, il existe donc des scénarios où la valeur ne peut pas être définie.

Pour ce faire, vous devez effectuer quelques opérations :

- Créez une classe fortement typée, qui sous-classe [DictionaryContainer](xref:Foundation.DictionaryContainer) et fournit les différents getters et setters pour chaque propriété.
- Déclarez des surcharges pour les méthodes qui prennent `NSDictionary` pour prendre la nouvelle version fortement typée.

Vous pouvez créer la classe fortement typée soit manuellement, soit à l’aide du générateur pour effectuer le travail pour vous.  Nous allons tout d’abord apprendre à effectuer cette opération manuellement pour comprendre ce qui se passe, puis l’approche automatique.

Pour ce faire, vous devez créer un fichier de prise en charge qui ne passe pas à votre API de contrat.  C’est ce que vous devez écrire pour créer votre classe XyzOptions :

```csharp
public class XyzOptions : DictionaryContainer {
# if !COREBUILD
    public XyzOptions () : base (new NSMutableDictionary ()) {}
    public XyzOptions (NSDictionary dictionary) : base (dictionary){}

    public nfloat? Volume {
       get { return GetFloatValue (XyzOptionsKeys.VolumeKey); }
       set { SetNumberValue (XyzOptionsKeys.VolumeKey, value); }
    }
    public string Caption {
       get { return GetStringValue (XyzOptionsKeys.CaptionKey); }
       set { SetStringValue (XyzOptionsKeys.CaptionKey, value); }
    }
# endif
}
```

Vous devez ensuite fournir une méthode wrapper qui couvre l’API de haut niveau, en plus de l’API de bas niveau.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Si votre API n’a pas besoin d’être remplacée, vous pouvez masquer en toute sécurité l’API basée sur NSDictionary à l’aide de l' [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
.

Comme vous pouvez le voir, nous utilisons le [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
attribut pour surfacer un nouveau point d’entrée d’API, et nous l’avons vu à l’aide de notre classe de `XyzOptions` fortement typée.  La méthode wrapper autorise également la réussite de la valeur null.

Maintenant, nous n’avons pas mentionné à l’origine des valeurs de `XyzOptionsKeys`.  En règle générale, vous regroupez les clés qu’une API a surfaces dans une classe statique comme `XyzOptionsKeys`, comme suit :

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Examinons la prise en charge automatique de la création de ces dictionnaires fortement typés.  Cela permet d’éviter une grande quantité de fichiers, et vous pouvez définir le dictionnaire directement dans votre contrat d’API, au lieu d’utiliser un fichier externe.

Pour créer un dictionnaire fortement typé, introduisez une interface dans votre API et décorez-la avec l’attribut [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) .  Cela indique au générateur qu’il doit créer une classe portant le même nom que l’interface qui dérivera de `DictionaryContainer` et fournira des accesseurs fortement typés pour celui-ci.

L’attribut [`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) prend un paramètre, qui est le nom de la classe statique qui contient vos clés de dictionnaire.  Ensuite, chaque propriété de l’interface devient un accesseur fortement typé.  Par défaut, le code utilise le nom de la propriété avec le suffixe « Key » dans la classe statique pour créer l’accesseur.

Cela signifie que la création de votre accesseur fortement typé n’a plus besoin d’un fichier externe, ni de la création manuelle des accesseurs get et setters pour chaque propriété, ni de la recherche manuelle des clés.

Voici à quoi ressemble la totalité de votre liaison :

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
[StrongDictionary ("XyzOptionKeys")]
interface XyzOptions {
    nfloat Volume { get; set; }
    string Caption { get; set; }
}

[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Si vous devez référencer dans votre `XyzOption` membres un autre champ (qui n’est pas le nom de la propriété avec le suffixe `Key`), vous pouvez décorer la propriété avec un [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribut portant le nom que vous souhaitez utiliser.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mappages de types

Cette section décrit comment les types objective-C sont mappés aux C# types.

<a name="Simple_Types" />

### <a name="simple-types"></a>Types simples

Le tableau suivant montre comment mapper des types de l’environnement objective-C et CocoaTouch au monde Xamarin. iOS :

|Nom du type objective-C|Type de API unifiée Xamarin. iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([plus sur la liaison de chaîne NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (voir aussi : [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Types CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Types Foundation (`NS*`)|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays" />

### <a name="arrays"></a>Tableaux

Le runtime Xamarin. iOS se charge automatiquement de la C# conversion des tableaux en `NSArrays` et de la reconversion, par exemple la méthode imaginaire-C imaginaire qui retourne une `NSArray` de `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

Est lié comme suit :

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

L’idée est d’utiliser un tableau fortement typé C# , car cela permet à l’IDE de fournir la saisie semi-automatique du code avec le type réel sans forcer l’utilisateur à deviner ou rechercher la documentation pour déterminer le type réel des objets contenus dans le tableau.

Dans les cas où vous ne pouvez pas effectuer le suivi du type le plus dérivé réel contenu dans le tableau, vous pouvez utiliser `NSObject []` comme valeur de retour.

<a name="Selectors" />

### <a name="selectors"></a>Sélecteurs

Les sélecteurs apparaissent sur l’API objective-C comme type spécial `SEL`. Lors de la liaison d’un sélecteur, vous devez mapper le type à `ObjCRuntime.Selector`.  En général, les sélecteurs sont exposés dans une API avec un objet, l’objet cible et un sélecteur à appeler dans l’objet cible. Le fait de fournir ces deux éléments correspond C# essentiellement au délégué : un objet qui encapsule la méthode à appeler, ainsi que l’objet pour appeler la méthode dans.

Voici à quoi ressemble la liaison :

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

C’est ainsi que la méthode est généralement utilisée dans une application :

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        b.SetTarget (this, new Selector ("print"));
    }

    [Export ("print")]
    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

Pour rendre la liaison plus attrayante C# pour les développeurs, vous fournissez généralement une méthode qui prend un paramètre `NSAction`, C# qui permet d’utiliser des délégués et des expressions lambda à la place de l' `Target+Selector`. Pour ce faire, vous devez généralement masquer la méthode `SetTarget` en la marquant avec un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
puis, vous exposez une nouvelle méthode d’assistance, comme ceci :

```csharp
// API.cs
interface Button {
   [Export ("setTarget:selector:"), Internal]
   void SetTarget (NSObject target, Selector sel);
}

// Extensions.cs
public partial class Button {
     public void SetTarget (NSAction callback)
     {
         SetTarget (new NSActionDispatcher (callback), NSActionDispatcher.Selector);
     }
}
```

Maintenant, votre code utilisateur peut être écrit comme suit :

```csharp
class DialogPrint : UIViewController {
    void HookPrintButton (Button b)
    {
        // First Style
        b.SetTarget (ThePrintMethod);

        // Lambda style
        b.SetTarget (() => {  /* print here */ });
    }

    void ThePrintMethod ()
    {
       // This does the printing
    }
}
```

<a name="Strings" />

### <a name="strings"></a>Chaînes

Lorsque vous liez une méthode qui prend un `NSString`, vous pouvez remplacer celle-ci par C# un type chaîne, à la fois sur les types de retour et les paramètres.

Le seul cas où vous souhaiterez peut-être utiliser un `NSString` directement est lorsque la chaîne est utilisée comme jeton. Pour plus d’informations sur les chaînes et les `NSString`, consultez le document [conception d’API sur chaîne NSString](~/ios/internals/api-design/nsstring.md) .

Dans certains cas rares, une API peut exposer une chaîne de type C (`char *`) au lieu d’une chaîne objective-C (`NSString *`). Dans ce cas, vous pouvez annoter le paramètre avec le [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>paramètres out/Ref

Certaines API retournent des valeurs dans leurs paramètres, ou passent des paramètres par référence.

En général, la signature se présente comme suit :

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

Le premier exemple montre un idiome objective-C commun pour retourner des codes d’erreur, un pointeur vers un pointeur `NSError` est passé et, à la suite du retour, la valeur est définie.   La deuxième méthode montre comment une méthode objective-C peut prendre un objet et modifier son contenu.   Il s’agit d’un passage par référence, plutôt que d’une valeur de sortie pure.

Votre liaison ressemble à ceci :

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Attributs de gestion de la mémoire

Lorsque vous utilisez l’attribut [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) et que vous transmettez des données qui seront conservées par la méthode appelée, vous pouvez spécifier la sémantique des arguments en la passant en tant que second paramètre, par exemple :

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

L’exemple ci-dessus marque la valeur comme ayant la sémantique « retain ». Les sémantiques disponibles sont les suivantes :

- Attribuer
- Copier
- Conserver

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Règles de style

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>Utilisation de [Internal]

Vous pouvez utiliser les [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attribut pour masquer une méthode de l’API publique. Vous pouvez le faire dans les cas où l’API exposée est trop faible et que vous souhaitez fournir une implémentation de haut niveau dans un fichier distinct basé sur cette méthode.

Vous pouvez également l’utiliser lorsque vous rencontrez des restrictions dans le générateur de liaisons, par exemple certains scénarios avancés peuvent exposer des types qui ne sont pas liés et que vous souhaitez lier de votre propre manière, et que vous souhaitez encapsuler ces types vous-même de votre propre manière.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Gestionnaires d’événements et rappels

Les classes objective-C diffusent généralement des notifications ou demandent des informations en envoyant un message sur une classe déléguée (délégué objective-C).

Ce modèle, bien qu’il soit entièrement pris en charge et exposé par Xamarin. iOS peut parfois être fastidieux. Xamarin. iOS expose le C# modèle d’événement et un système de rappel de méthode sur la classe qui peut être utilisé dans ces situations. Cela permet au code comme celui-ci de s’exécuter :

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Le générateur de liaisons peut réduire la quantité de frappe requise pour mapper le modèle objective-C dans le C# modèle.

À compter de Xamarin. iOS 1,4, il est possible de demander également au générateur de générer des liaisons pour un délégué objective-C spécifique et d’exposer C# le délégué en tant qu’événements et propriétés sur le type d’hôte.

Deux classes sont impliquées dans ce processus, la classe hôte qui est celle qui émet actuellement des événements et les envoie au `Delegate` ou `WeakDelegate` et à la classe déléguée réelle.

En considération de la configuration suivante :

```csharp
[BaseType (typeof (NSObject))]
interface MyClass {
    [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
    NSObject WeakDelegate { get; set; }

    [Wrap ("WeakDelegate")][NullAllowed]
    MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
    [Export ("loaded:bytes:")]
    void Loaded (MyClass sender, int bytes);
}
```

Pour encapsuler la classe, vous devez :

- Dans votre classe hôte, ajoutez à votre [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   déclaration le type qui agit comme son délégué et le C# nom que vous avez exposé. Dans notre exemple ci-dessus, ceux-ci sont `typeof (MyClassDelegate)` et `WeakDelegate` respectivement.
- Dans votre classe déléguée, sur chaque méthode ayant plus de deux paramètres, vous devez spécifier le type que vous souhaitez utiliser pour la classe EventArgs générée automatiquement.

Le générateur de liaison n’est pas limité à l’encapsulation d’une seule destination d’événement, il est possible que certaines classes objective-C émettent des messages vers plusieurs délégués. vous devez donc fournir des tableaux pour prendre en charge cette installation. La plupart des configurations n’en ont pas besoin, mais le générateur est prêt à prendre en charge ces cas de figure.

Le code obtenu est le suivant :

```csharp
[BaseType (typeof (NSObject),
    Delegates=new string [] {"WeakDelegate"},
    Events=new Type [] { typeof (MyClassDelegate) })]
interface MyClass {
        [Export ("delegate", ArgumentSemantic.Assign)][NullAllowed]
        NSObject WeakDelegate { get; set; }

        [Wrap ("WeakDelegate")][NullAllowed]
        MyClassDelegate Delegate { get; set; }
}

[BaseType (typeof (NSObject))]
interface MyClassDelegate {
        [Export ("loaded:bytes:"), EventArgs ("MyClassLoaded")]
        void Loaded (MyClass sender, int bytes);
}
```

Le `EventArgs` est utilisé pour spécifier le nom de la classe `EventArgs` à générer. Vous devez utiliser un par signature (dans cet exemple, le `EventArgs` contient une propriété `With` de type nint).

Avec les définitions ci-dessus, le générateur produira l’événement suivant dans le MyClass généré :

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Vous pouvez maintenant utiliser le code comme suit :

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Les rappels sont tout comme les appels d’événement, la différence est que, au lieu d’avoir plusieurs abonnés potentiels (par exemple, plusieurs méthodes peuvent se raccorder à un événement `Clicked` ou à un événement `DownloadFinished`), les rappels peuvent uniquement avoir un seul abonné.

Le processus est identique, la seule différence est que, au lieu d’exposer le nom de la classe `EventArgs` qui sera générée, EventArgs est utilisé en fait pour nommer le nom C# du délégué résultant.

Si la méthode dans la classe déléguée retourne une valeur, le générateur de liaisons le mappe à une méthode déléguée dans la classe parente au lieu d’un événement. Dans ce cas, vous devez fournir la valeur par défaut qui doit être retournée par la méthode si l’utilisateur n’est pas raccordé au délégué. Pour ce faire, utilisez le [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
ou [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) attributs.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) configurera en dur une valeur de retour, tandis que [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
est utilisé pour spécifier l’argument d’entrée qui sera retourné.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Énumérations et types de base

Vous pouvez également référencer des énumérations ou des types de base qui ne sont pas directement pris en charge par le système de définition d’interface btouch. Pour ce faire, placez vos énumérations et types principaux dans un fichier distinct et incluez-les dans l’un des fichiers supplémentaires que vous fournissez à btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Liaison des dépendances

Si vous liez des API qui ne font pas partie de votre application, vous devez vous assurer que votre exécutable est lié à ces bibliothèques.

Vous devez informer Xamarin. iOS comment lier vos bibliothèques. pour ce faire, vous pouvez modifier votre configuration de build pour appeler la commande `mtouch` avec des arguments de build supplémentaires qui spécifient comment établir une liaison avec les nouvelles bibliothèques à l’aide de l’option « -gcc_flags », suivie d’une chaîne entre guillemets qui contient toutes les bibliothèques supplémentaires requises pour votre programme, comme suit :

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

L’exemple ci-dessus permet de lier `libMyLibrary.a`, `libSystemLibrary.dylib` et la bibliothèque `CFNetwork` Framework à votre exécutable final.

Vous pouvez aussi tirer parti de la [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute)au niveau de l’assembly, que vous pouvez incorporer dans vos fichiers de contrat (tels que `AssemblyInfo.cs`).
Lorsque vous utilisez la [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), votre bibliothèque Native doit être disponible au moment où vous effectuez votre liaison, car cette opération incorpore la bibliothèque native à votre application. Exemple :

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Vous vous demandez peut-être pourquoi vous avez besoin de `-force_load` commande, et la raison en est que l’indicateur-ObjC bien qu’il compile le code dans, il ne conserve pas les métadonnées requises pour prendre en charge les catégories (l’élimination de l’éditeur de liens/du compilateur) dont vous avez besoin au moment de l’exécution pour Xamarin. iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Références assistées

Certains objets temporaires, tels que les feuilles d’action et les zones d’alerte, sont fastidieux à suivre pour les développeurs et le générateur de liaisons peut vous aider un peu ici.

Par exemple, si vous avez une classe qui affichait un message, puis générait un événement `Done`, la méthode classique de traitement de ce serait la suivante :

```csharp
class Demo {
    MessageBox box;

    void ShowError (string msg)
    {
        box = new MessageBox (msg);
        box.Done += { box = null; ... };
    }
}
```

Dans le scénario ci-dessus, le développeur doit conserver la référence à l’objet lui-même et l’une ou l’autre des fuites ou effacer activement la référence pour Box seul.  Lors de la liaison de code, le générateur prend en charge le suivi de la référence pour vous et l’efface quand une méthode spéciale est appelée, le code ci-dessus devient alors :

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Notez qu’il n’est plus nécessaire de conserver la variable dans une instance, qu’elle fonctionne avec une variable locale et qu’il n’est pas nécessaire d’effacer la référence lorsque l’objet meurt.

Pour tirer parti de cela, votre classe doit avoir une propriété Events définie dans la Déclaration [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) et également la variable `KeepUntilRef` définie sur le nom de la méthode appelée lorsque l’objet a terminé son travail, comme suit :

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Héritage de protocoles

À compter de Xamarin. iOS v 3.2, nous prenons en charge l’héritage des protocoles qui ont été marqués avec la propriété [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) . Cela est utile dans certains modèles d’API, par exemple dans `MapKit` où le protocole `MKOverlay`, hérite du protocole `MKAnnotation` et est adopté par un certain nombre de classes qui héritent de `NSObject`.

Historiquement, nous avons nécessité de copier le protocole à chaque implémentation, mais dans ce cas, nous pouvons maintenant faire en sorte que la classe `MKShape` hérite du protocole `MKOverlay` et génère automatiquement toutes les méthodes requises.

## <a name="related-links"></a>Liens connexes

- [Exemple de liaison](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
