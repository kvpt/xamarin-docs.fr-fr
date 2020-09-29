---
title: Liaison des bibliothèques objective-C
description: Ce document fournit une vue d’ensemble de haut niveau de la création de liaisons C# au code Objective-C, qui décrivent comment lier des événements, des méthodes, des contrôles personnalisés et bien plus encore.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: davidortinau
ms.author: daortin
ms.date: 03/06/2018
ms.openlocfilehash: ebb9baf7bb1a6da96615eac65d5384cb7a05a9d6
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91457599"
---
# <a name="binding-objective-c-libraries"></a>Liaison des bibliothèques objective-C

Lorsque vous utilisez Xamarin. iOS ou Xamarin. Mac, vous pouvez rencontrer des cas où vous souhaitez consommer une bibliothèque objective-C tierce. Dans ces situations, vous pouvez utiliser des projets de liaison Xamarin pour créer une liaison C# avec les bibliothèques objective-C natives. Le projet utilise les mêmes outils que ceux que nous utilisons pour intégrer les API iOS et Mac à C#.

Ce document décrit comment lier des API objective-C, si vous liez uniquement des API C, vous devez utiliser le mécanisme .NET standard pour cela, [l’infrastructure P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Vous trouverez plus d’informations sur la liaison statique d’une bibliothèque C sur la page [liaison de bibliothèques natives](~/ios/platform/native-interop.md) .

Consultez notre [Guide de référence des types de liaison](~/cross-platform/macios/binding/binding-types-reference.md)compagnons.
En outre, si vous souhaitez en savoir plus sur ce qui se passe en coulisses, consultez notre page de présentation de la [liaison](~/cross-platform/macios/binding/overview.md) .

Les liaisons peuvent être générées pour les bibliothèques iOS et Mac.
Cette page explique comment travailler sur une liaison iOS, mais les liaisons Mac sont très similaires.

**Exemple de code pour iOS**

Vous pouvez utiliser l’exemple de projet de [liaison iOS](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) pour expérimenter des liaisons.

<a name="Getting_Started"></a>

## <a name="getting-started"></a>Prise en main

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Le moyen le plus simple de créer une liaison consiste à créer un projet de liaison Xamarin. iOS.
Pour ce faire, vous pouvez Visual Studio pour Mac en sélectionnant le type de projet, bibliothèque de **> iOS > bibliothèques de liaisons**:

[![Pour ce faire, à partir de Visual Studio pour Mac, sélectionnez le type de projet Bibliothèque de liaisons de bibliothèque iOS](objective-c-libraries-images/00-sml.png)](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Le moyen le plus simple de créer une liaison consiste à créer un projet de liaison Xamarin. iOS.
Pour ce faire, vous pouvez utiliser Visual Studio sur Windows en sélectionnant le type de projet, **Visual C# > ios > bibliothèques de liaisons (IOS)**:

[![Bibliothèque de liaisons iOS iOS](objective-c-libraries-images/00vs-sml.png)](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Remarque : les projets de liaison pour **Xamarin. Mac** sont uniquement pris en charge dans les Visual Studio pour Mac.

-----

Le projet généré contient un modèle de petite taille que vous pouvez modifier, il contient deux fichiers : `ApiDefinition.cs` et `StructsAndEnums.cs` .

`ApiDefinition.cs`Est l’emplacement où vous allez définir le contrat d’API. il s’agit du fichier qui décrit comment l’API objective-C sous-jacente est projetée en C#. La syntaxe et le contenu de ce fichier sont le sujet principal de la discussion sur ce document et son contenu est limité aux interfaces C# et aux déclarations de délégué C#. Le `StructsAndEnums.cs` fichier est le fichier dans lequel vous allez entrer les définitions requises par les interfaces et les délégués. Cela comprend les valeurs d’énumération et les structures que votre code peut utiliser.

<a name="Binding_an_API"></a>

## <a name="binding-an-api"></a>Liaison d’une API

Pour effectuer une liaison complète, vous devez comprendre la définition de l’API objective-C et vous familiariser avec les instructions de conception de .NET Framework.

Pour lier votre bibliothèque, vous démarrez généralement avec un fichier de définition d’API. Un fichier de définition d’API est simplement un fichier source C# qui contient des interfaces C# qui ont été annotées avec quelques attributs permettant de piloter la liaison.  Ce fichier définit le contrat entre C# et objective-C.

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

L’exemple ci-dessus définit une classe appelée `Cocos2D.Camera` qui dérive du `NSObject` type de base (ce type provient de `Foundation.NSObject` ) et qui définit une propriété statique ( `ZEye` ), deux méthodes qui ne prennent pas d’arguments et une méthode qui accepte trois arguments.

Une présentation détaillée du format du fichier API et des attributs que vous pouvez utiliser est décrite dans la section [fichier de définition d’API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) ci-dessous.

Pour produire une liaison complète, vous devez généralement gérer quatre composants :

- Fichier de définition de l’API ( `ApiDefinition.cs` dans le modèle).
- Facultatif : tous les enums, types et structs requis par le fichier de définition `StructsAndEnums.cs` d’API (dans le modèle).
- Facultatif : sources supplémentaires qui peuvent développer la liaison générée ou fournir une API plus conviviale C# (tous les fichiers C# que vous ajoutez au projet).
- Bibliothèque Native que vous liez.

Ce graphique montre la relation entre les fichiers :

 [![Ce graphique montre la relation entre les fichiers.](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png)](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Le fichier de définition d’API ne contient que des espaces de noms et des définitions d’interface (avec tous les membres qu’une interface peut contenir) et ne doit pas contenir des classes, des énumérations, des délégués ou des structs. Le fichier de définition d’API est simplement le contrat qui sera utilisé pour générer l’API.

Tout code supplémentaire dont vous avez besoin, comme les énumérations ou les classes de prise en charge, doit être hébergé sur un fichier distinct. dans l’exemple ci-dessus, « CameraMode » est une valeur d’énumération qui n’existe pas dans le fichier CS et qui doit être hébergée dans un fichier séparé, par exemple `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

Le `APIDefinition.cs` fichier est combiné avec la `StructsAndEnum` classe et sont utilisés pour générer la liaison principale de la bibliothèque. Vous pouvez utiliser la bibliothèque résultante telle quelle, mais en général, vous souhaiterez adapter la bibliothèque résultante pour ajouter des fonctionnalités C# pour l’avantage de vos utilisateurs. Il peut s’agir, par exemple, d’implémenter une `ToString()` méthode, de fournir des indexeurs C#, d’ajouter des conversions implicites vers et à partir de certains types natifs ou de fournir des versions fortement typées de certaines méthodes. Ces améliorations sont stockées dans des fichiers C# supplémentaires. Ajoutez simplement les fichiers C# à votre projet et ils seront inclus dans ce processus de génération.

Cela montre comment implémenter le code dans votre `Extra.cs` fichier. Notez que vous utiliserez des classes partielles, car elles augmentent les classes partielles générées à partir de la combinaison de la `ApiDefinition.cs` et de la `StructsAndEnums.cs` liaison principale :

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

Pour terminer cette liaison, vous devez ajouter la bibliothèque Native au projet.  Pour ce faire, vous pouvez ajouter la bibliothèque native à votre projet, soit en faisant glisser la bibliothèque native de Finder vers le projet dans l’Explorateur de solutions, soit en cliquant avec le bouton droit sur le projet et en choisissant **Ajouter**  >  **Ajouter des fichiers** pour sélectionner la bibliothèque native.
Les bibliothèques natives par convention commencent par le mot « lib » et se terminent par l’extension « . a ». Visual Studio pour Mac ajoutera alors deux fichiers : le fichier. a et un fichier C# rempli automatiquement qui contient des informations sur ce que contient la bibliothèque Native :

 [![Les bibliothèques natives par convention commencent par le mot lib et se terminent par l’extension. a](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png)](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Le contenu du `libMagicChord.linkwith.cs` fichier contient des informations sur la façon dont cette bibliothèque peut être utilisée et indique à votre IDE d’empaqueter ce binaire dans le fichier dll résultant :

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Informations complètes sur l’utilisation de la [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
les attributs sont documentés dans le [Guide de référence des types de liaison](~/cross-platform/macios/binding/binding-types-reference.md).

À présent, lorsque vous générez le projet, vous obtenez un `MagicChords.dll` fichier qui contient à la fois la liaison et la bibliothèque native. Vous pouvez distribuer ce projet ou la DLL résultante à d’autres développeurs pour leur propre usage.

Il peut arriver que vous ayez besoin de quelques valeurs d’énumération, de définitions déléguées ou d’autres types. Ne les placez pas dans le fichier de définitions d’API, car il s’agit simplement d’un contrat

<a name="The_API_definition_file"></a>

## <a name="the-api-definition-file"></a>Fichier de définition de l’API

Le fichier de définition d’API se compose d’un certain nombre d’interfaces. Les interfaces dans la définition de l’API sont transformées en une déclaration de classe et doivent être décorées avec l' [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) attribut pour spécifier la classe de base pour la classe.

Vous vous demandez peut-être pourquoi nous n’avons pas utilisé des classes au lieu d’interfaces pour la définition de contrat. Nous avons choisi interfaces, car cela nous permettait d’écrire le contrat pour une méthode sans avoir à fournir un corps de méthode dans le fichier de définition d’API, ou à fournir un corps qui devait lever une exception ou retourner une valeur significative.

Mais étant donné que nous utilisons l’interface comme squelette pour générer une classe, nous devions recourir à la décoration de différentes parties du contrat avec des attributs pour piloter la liaison.

<a name="Binding_Methods"></a>

### <a name="binding-methods"></a>Méthodes de liaison

La liaison la plus simple consiste à lier une méthode. Déclarez simplement une méthode dans l’interface avec les conventions de nommage C# et décorez la méthode avec l’instruction [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
. L' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut est celui qui lie votre nom C# au nom objective-C dans le runtime Xamarin. iOS. Le paramètre de la [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribut est le nom du sélecteur objective-C. Exemples :

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

Les exemples ci-dessus montrent comment vous pouvez lier des méthodes d’instance. Pour lier des méthodes statiques, vous devez utiliser l' [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attribut, comme suit :

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Cela est nécessaire, car le contrat fait partie d’une interface, et les interfaces n’ont aucune notion de déclarations statiques vs instance. il est donc nécessaire de réutiliser les attributs. Si vous souhaitez masquer une méthode particulière de la liaison, vous pouvez décorer la méthode avec l' [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attribut.

La `btouch-native` commande introduira des vérifications des paramètres de référence à ne pas avoir la valeur null. Si vous souhaitez autoriser les valeurs NULL pour un paramètre particulier, utilisez l' [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
sur le paramètre, comme suit :

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Lors de l’exportation d’un type référence, avec le [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) mot clé, vous pouvez également spécifier la sémantique d’allocation. Cela est nécessaire pour s’assurer qu’aucune donnée n’est divulguée.

<a name="Binding_Properties"></a>

### <a name="binding-properties"></a>Propriétés de liaison

Tout comme les méthodes, les propriétés objective-C sont liées à l’aide de l' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attribute et mappez directement aux propriétés C#. Tout comme les méthodes, les propriétés peuvent être décorées avec l' [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
et le [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
.

Lorsque vous utilisez l' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut sur une propriété en coulisses, btouch-Native lie en fait deux méthodes : l’accesseur get et l’accesseur Set. Le nom que vous fournissez pour l’exportation est le **BaseName** et l’accesseur Set est calculé en ajoutant le mot « set » en préfixe, en mettant la première lettre du **BaseName** en majuscules et en faisant en sorte que le sélecteur prenne un argument. Cela signifie que `[Export ("label")]` appliquée sur une propriété lie en fait les méthodes objective-C « label » et « setLabel : ».

Parfois, les propriétés objective-C ne suivent pas le modèle décrit ci-dessus et le nom est remplacé manuellement. Dans ce cas, vous pouvez contrôler la façon dont la liaison est générée à l’aide de l’outil [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
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

Où les accesseurs get et setter sont définis explicitement comme dans les `name` `setName` liaisons et ci-dessus.

En plus de la prise en charge des propriétés statiques à l’aide de [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) , vous pouvez décorer des propriétés thread-static avec [`[IsThreadStatic]`](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute) , par exemple :

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Tout comme les méthodes autorisant l’indicateur de certains paramètres avec [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) , vous pouvez appliquer [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
à une propriété pour indiquer que la valeur null est une valeur valide pour la propriété, par exemple :

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Le [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) paramètre peut également être spécifié directement sur la méthode setter :

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Avertissements relatifs à la liaison de contrôles personnalisés

Les avertissements suivants doivent être pris en compte lors de la configuration de la liaison pour un contrôle personnalisé :

1. Les **Propriétés de liaison doivent être statiques** : lors de la définition de la liaison de propriétés, l' [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attribut doit être utilisé.
2. Les **noms de propriété doivent correspondre exactement** : le nom utilisé pour lier la propriété doit correspondre exactement au nom de la propriété dans le contrôle personnalisé.
3. Les **types de propriété doivent correspondre exactement** -le type de variable utilisé pour lier la propriété doit correspondre exactement au type de la propriété dans le contrôle personnalisé.
4. **Les points d’arrêt et les points d’arrêt Getter/Setter** placés dans les méthodes getter ou Setter de la propriété ne seront jamais atteints.
5. **Observer les rappels** : vous devrez utiliser des rappels d’observation pour être averti des modifications apportées aux valeurs de propriété des contrôles personnalisés.

Si vous ne respectez pas l’un des avertissements mentionnés ci-dessus, la liaison échoue en silence au moment de l’exécution.

<a name="MutablePattern"></a>

#### <a name="objective-c-mutable-pattern-and-properties"></a>Propriétés et modèle mutable objective-C

Les frameworks objective-C utilisent un idiome dans lequel certaines classes sont immuables avec une sous-classe mutable. Par exemple, il s’agit de la `NSString` version immuable, tandis `NSMutableString` que est la sous-classe qui autorise la mutation.

Dans ces classes, il est courant de voir que la classe de base immuable contient des propriétés avec un accesseur Get, mais pas d’accesseur Set. Et pour la version mutable pour introduire l’accesseur Set. Dans la mesure où cela n’est pas vraiment possible avec C#, nous avons dû mapper cet idiome dans un idiome qui fonctionnait avec C#.

La façon dont ce est mappé à C# consiste à ajouter l’accesseur get et l’accesseur Set à la classe de base, mais à marquer la méthode setter avec un [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
.

Ensuite, sur la sous-classe mutable, vous utilisez la [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
attribut sur la propriété pour garantir que la propriété substitue réellement le comportement du parent.

Exemple :

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

<a name="Binding_Constructors"></a>

### <a name="binding-constructors"></a>Constructeurs de liaison

L' `btouch-native` outil génère automatiquement quatre constructeurs dans votre classe, pour une classe donnée `Foo` , il génère :

- `Foo ()`: le constructeur par défaut (est mappé au constructeur « init » de Objective-C)
- `Foo (NSCoder)`: le constructeur utilisé pendant la désérialisation des fichiers de plume (est mappé au constructeur « initWithCoder : » de Objective-C).
- `Foo (IntPtr handle)`: le constructeur pour la création basée sur des handles, appelé par le runtime quand le runtime doit exposer un objet managé à partir d’un objet non managé.
- `Foo (NSEmptyFlag)`: utilisé par les classes dérivées pour empêcher une double initialisation.

Pour les constructeurs que vous définissez, ils doivent être déclarés à l’aide de la signature suivante à l’intérieur de la définition de l’interface : ils doivent retourner une `IntPtr` valeur et le nom de la méthode doit être Constructor. Par exemple, pour lier le `initWithFrame:` constructeur, c’est ce que vous utiliseriez :

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols"></a>

### <a name="binding-protocols"></a>Protocoles de liaison

Comme décrit dans le document de conception d’API, dans la section discussion sur les [modèles et les protocoles](~/ios/internals/api-design/index.md#models), Xamarin. iOS mappe les protocoles objective-C dans des classes qui ont été marquées avec l’interface [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
. Cela est généralement utilisé lors de l’implémentation de classes déléguées objective-C.

La grande différence entre une classe liée normale et une classe déléguée est que la classe déléguée peut avoir une ou plusieurs méthodes facultatives.

Par exemple, considérons la `UIKit` classe `UIAccelerometerDelegate` . c’est ainsi qu’elle est liée dans Xamarin. iOS :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Étant donné qu’il s’agit d’une méthode facultative sur la définition `UIAccelerometerDelegate` , il n’y a rien d’autre à faire. Toutefois, si une méthode est nécessaire sur le protocole, vous devez ajouter la [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
attribut à la méthode. Cela forcera l’utilisateur de l’implémentation à fournir en fait un corps pour la méthode.

En général, les protocoles sont utilisés dans les classes qui répondent aux messages. Cette opération est généralement effectuée en Objective-C en affectant à la propriété « Delegate » une instance d’un objet qui répond aux méthodes dans le protocole.

La Convention dans Xamarin. iOS consiste à prendre en charge à la fois le style objective-C faiblement couplé dans lequel une instance de `NSObject` peut être assignée au délégué et également exposer une version fortement typée de celui-ci. Pour cette raison, nous fournissons généralement à la fois une `Delegate` propriété fortement typée et une `WeakDelegate` qui est faiblement typée. Nous lions généralement la version faiblement typée avec [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) , et nous utilisons l' [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attribut pour fournir la version fortement typée.

Cela montre comment nous avons lié la `UIAccelerometer` classe :

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

<a name="iOS7ProtocolSupport"></a>

**Nouveautés de MonoTouch 7,0**

À partir de MonoTouch 7,0, une nouvelle fonctionnalité de liaison de protocole améliorée a été incorporée.  Grâce à cette nouvelle prise en charge, il est plus simple d’utiliser des idiomes objective-C pour adopter un ou plusieurs protocoles dans une classe donnée.

Pour chaque définition `MyProtocol` de protocole en Objective-C, il existe désormais une `IMyProtocol` interface qui répertorie toutes les méthodes requises du protocole, ainsi qu’une classe d’extension qui fournit toutes les méthodes facultatives.  Ce qui précède, combiné à la nouvelle prise en charge de l’éditeur de Xamarin Studio, permet aux développeurs d’implémenter des méthodes de protocole sans avoir à utiliser les sous-classes distinctes des classes de modèle abstrait précédentes.

Toute définition contenant l' [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) attribut génère en fait trois classes de prise en charge qui améliorent considérablement la façon dont vous consommez les protocoles :

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

L' **implémentation de classe** fournit une classe abstraite complète qui vous permet de substituer des méthodes individuelles de et d’obtenir une sécurité de type complète.  Mais en raison du fait que C# ne prend pas en charge l’héritage multiple, il existe des scénarios dans lesquels vous devrez peut-être avoir une classe de base différente, mais vous souhaitez toujours implémenter une interface, c’est là que le

La **définition d’interface** générée entre en entrée.  Il s’agit d’une interface qui contient toutes les méthodes requises du protocole.  Cela permet aux développeurs qui souhaitent implémenter votre protocole d’implémenter simplement l’interface.  Le runtime inscrit automatiquement le type comme adoptant le protocole.

Notez que l’interface répertorie uniquement les méthodes requises et expose les méthodes facultatives.  Cela signifie que les classes qui adoptent le protocole obtiendront une vérification de type complète pour les méthodes requises, mais auront recours à un typage faible (à l’aide de [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
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

L’expression ci-dessus est nécessaire car, au moment de la liaison, le n' `IMyProtocol` existe pas, c’est pourquoi vous devez fournir une interface vide.

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

<a name="Binding_Class_Extensions"></a>

### <a name="binding-class-extensions"></a>Extensions de classe de liaison

En Objective-C, il est possible d’étendre les classes avec de nouvelles méthodes, comme dans l’esprit des méthodes d’extension de C#. Quand l’une de ces méthodes est présente, vous pouvez utiliser la [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attribut pour marquer la méthode comme étant le récepteur du message objective-C.

Par exemple, dans Xamarin. iOS, nous avons lié les méthodes d’extension qui sont définies sur `NSString` lorsque `UIKit` est importé en tant que méthodes dans le `NSStringDrawingExtensions` , comme suit :

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists"></a>

### <a name="binding-objective-c-argument-lists"></a>Liaison des listes d’arguments objective-C

Objective-C prend en charge les arguments variadiques. Par exemple :

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Pour appeler cette méthode à partir de C#, vous devez créer une signature semblable à celle-ci :

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

<a name="Binding_Fields"></a>

### <a name="binding-fields"></a>Champs de liaison

Parfois, vous souhaiterez accéder aux champs publics qui ont été déclarés dans une bibliothèque.

En général, ces champs contiennent des chaînes ou des valeurs entières qui doivent être référencées. Ils sont couramment utilisés comme chaîne représentant une notification spécifique et en tant que clés dans les dictionnaires.

Pour lier un champ, ajoutez une propriété à votre fichier de définition d’interface et décorez la propriété avec l' [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attribut. Cet attribut accepte un paramètre : le nom C du symbole à rechercher. Par exemple :

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Si vous souhaitez encapsuler différents champs dans une classe statique qui ne dérive pas de `NSObject` , vous pouvez utiliser l' [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
sur la classe, comme suit :

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

L’exemple ci-dessus génère un `LonelyClass` qui ne dérive pas de `NSObject` et contient une liaison au `NSSomeEventNotification` 
 `NSString` exposé en tant que `NSString` .

L' [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attribut peut être appliqué aux types de données suivants :

- `NSString` références (propriétés en lecture seule)
- `NSArray` références (propriétés en lecture seule)
- 32 bits ints ( `System.Int32` )
- 64 bits ints ( `System.Int64` )
- 32 bits float ( `System.Single` )
- 64 bits float ( `System.Double` )
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

Si vous effectuez une liaison statique, il n’y a aucune bibliothèque à lier. vous devez donc utiliser le `__Internal` nom :

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums"></a>

### <a name="binding-enums"></a>Liaisons, énumérations

Vous pouvez ajouter `enum` directement dans vos fichiers de liaison pour faciliter leur utilisation dans les définitions d’API, sans utiliser un fichier source différent (qui doit être compilé à la fois dans les liaisons et le projet final).

Exemple :

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Il est également possible de créer vos propres énumérations pour remplacer des `NSString` constantes. Dans ce cas, le Générateur créera **automatiquement** les méthodes pour convertir des valeurs enums et des constantes chaîne NSString pour vous.

Exemple :

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

Dans l’exemple ci-dessus, vous pouvez décider de décorer `void Perform (NSString mode);` avec un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attribut. Cela va **Masquer** l’API basée sur des constantes de vos consommateurs de liaison.

Toutefois, cela limiterait la sous-classe du type, car l’API plus attrayante utilise un [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attribut. Ces méthodes générées ne le sont pas `virtual` . par exemple, vous ne pourrez pas les remplacer, ce qui peut être un bon choix.

Une alternative consiste à marquer la définition d’origine à l' `NSString` aide de `[Protected]` . Cela permettra au sous-classe de fonctionner, si nécessaire, et la version de wrap’ed continuera de fonctionner et d’appeler la méthode substituée.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Liaison `NSValue` , `NSNumber` et `NSString` à un meilleur type

L' [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) attribut autorise la `NSNumber` liaison `NSValue` et `NSString` (enums) en types C# plus précis. L’attribut peut être utilisé pour créer une API .NET plus précise et plus précise sur l’API native.

Vous pouvez décorer des méthodes (sur une valeur de retour), des paramètres et des propriétés avec [`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) . La seule restriction est que votre membre **ne doit pas** se trouver à l’intérieur d’un [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
ou [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interface.

Par exemple :

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

En interne, nous allons effectuer les `bool?`  <->  `NSNumber` `CGRect`  <->  `NSValue` conversions et.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) prend également en charge les tableaux de `NSNumber` `NSValue` et `NSString` (enums).

Par exemple :

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

`CAScroll` est une `NSString` énumération sauvegardée, nous allons extraire la `NSString` valeur appropriée et gérer la conversion de type.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute)Pour plus d’informations sur les types de conversion pris en charge, consultez la documentation.

<a name="Binding_Notifications"></a>

### <a name="binding-notifications"></a>Notifications de liaison

Les notifications sont des messages qui sont publiés dans le `NSNotificationCenter.DefaultCenter` et qui sont utilisés comme mécanisme pour diffuser des messages d’une partie de l’application vers une autre. Les développeurs s’abonnent aux notifications généralement à l’aide de la méthode [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) de [NSNotificationCenter](xref:Foundation.NSNotificationCenter). Lorsqu’une application publie un message dans le centre de notifications, elle contient généralement une charge utile stockée dans le dictionnaire [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) . Ce dictionnaire est faiblement typé et l’obtention d’informations à partir de celui-ci est sujette aux erreurs, car les utilisateurs doivent généralement lire la documentation qui contient les clés disponibles sur le dictionnaire et les types de valeurs qui peuvent être stockées dans le dictionnaire. La présence de clés est parfois utilisée comme une valeur booléenne.

Le générateur de liaisons Xamarin. iOS offre aux développeurs la prise en charge de la liaison des notifications. Pour ce faire, vous devez définir le [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
attribut sur une propriété qui a été également marquée avec un [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
Property (elle peut être publique ou privée).

Cet attribut peut être utilisé sans arguments pour les notifications qui ne contiennent pas de charge utile, ou vous pouvez spécifier un `System.Type` qui fait référence à une autre interface dans la définition de l’API, généralement avec le nom se terminant par « EventArgs ». Le générateur transformera l’interface en une classe qui sous-classe `EventArgs` et inclura toutes les propriétés qui y sont listées. L' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut doit être utilisé dans la classe EventArgs pour répertorier le nom de la clé utilisée pour rechercher le dictionnaire objective-C afin d’extraire la valeur.

Par exemple :

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

La valeur retournée par `ObserveDidStart` peut être utilisée pour arrêter facilement la réception de notifications, comme suit :

```csharp
token.Dispose ();
```

Vous pouvez appeler [NSNotification. DefaultCenter. RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) et passer le jeton. Si votre notification contient des paramètres, vous devez spécifier une interface d’assistance `EventArgs` , comme suit :

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

La version ci-dessus génère une `MyScreenChangedEventArgs` classe avec les `ScreenX` `ScreenY` Propriétés et qui récupèrent les données du dictionnaire [NSNotification. UserInfo](xref:Foundation.NSNotification.UserInfo) en utilisant respectivement les clés « ScreenXKey » et « ScreenYKey » et appliquent les conversions appropriées. L' `[ProbePresence]` attribut est utilisé pour que le générateur sonde si la clé est définie dans le `UserInfo` , au lieu d’essayer d’extraire la valeur. Cela est utilisé dans les cas où la présence de la clé est la valeur (généralement pour les valeurs booléennes).

Cela vous permet d’écrire du code tel que celui-ci :

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories"></a>

### <a name="binding-categories"></a>Catégories de liaison

Les catégories sont un mécanisme objective-C utilisé pour étendre l’ensemble de méthodes et de propriétés disponibles dans une classe.   Dans la pratique, ils sont utilisés pour étendre les fonctionnalités d’une classe de base (par exemple `NSObject` ) lorsqu’un Framework spécifique est lié (par exemple `UIKit` ), en rendant leurs méthodes disponibles, mais uniquement si la nouvelle infrastructure est liée.   Dans d’autres cas, ils sont utilisés pour organiser les fonctionnalités dans une classe par fonctionnalité.   Elles sont similaires dans l’esprit des méthodes d’extension C#. Voici à quoi ressemble une catégorie dans objective-C :

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

L’exemple ci-dessus, s’il est trouvé sur une bibliothèque, étend les instances de `UIView` avec la méthode `makeBackgroundRed` .

Pour les lier, vous pouvez utiliser l' [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) attribut sur une définition d’interface.  Lors de l’utilisation de [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attribut, la signification de l’attribut [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
l’attribut change d’être utilisé pour spécifier la classe de base à étendre, en tant que type à étendre.

L’exemple suivant montre comment les `UIView` extensions sont liées et converties en méthodes d’extension C# :

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

L’opération ci-dessus crée une `MyUIViewExtension` classe qui contient la `MakeBackgroundRed` méthode d’extension.  Cela signifie que vous pouvez maintenant appeler « MakeBackgroundRed » sur n’importe quelle sous-classe, ce qui vous `UIView` donne les mêmes fonctionnalités que celles que vous obtiendriez sur objective-C. Dans d’autres cas, les catégories sont utilisées pour étendre une classe système, mais pour organiser les fonctionnalités, uniquement à des fins de décoration.  Comme ceci :

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Bien que vous puissiez utiliser la [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
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

<a name="Binding_Blocks"></a>

### <a name="binding-blocks"></a>Blocs de liaison

Les blocs sont une nouvelle construction introduite par Apple pour amener l’équivalent fonctionnel des méthodes anonymes C# à Objective-C. Par exemple, la `NSSet` classe expose maintenant cette méthode :

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La description ci-dessus déclare une méthode appelée `enumerateObjectsUsingBlock:` qui accepte un argument nommé `block` . Ce bloc est similaire à une méthode anonyme C# en ce qu’il prend en charge la capture de l’environnement actuel (le pointeur « this », l’accès aux variables et paramètres locaux). La méthode ci-dessus `NSSet` appelle le bloc avec deux paramètres `NSObject` (le `id obj` composant) et un pointeur vers une partie booléenne (la `BOOL *stop` ).

Pour lier ce type d’API à btouch, vous devez d’abord déclarer la signature de type de bloc en tant que délégué C#, puis la référencer à partir d’un point d’entrée d’API, comme ceci :

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

Maintenant, votre code peut appeler votre fonction à partir de C# :

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

<a name="GeneratingAsync"></a>

### <a name="asynchronous-methods"></a>Méthodes asynchrones

Le générateur de liaisons peut transformer une certaine classe de méthodes en méthodes asynchrones (méthodes qui retournent une tâche ou une tâche &lt; T &gt; ).

Vous pouvez utiliser la [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
attribut sur les méthodes qui retournent void et dont le dernier argument est un rappel.  Lorsque vous appliquez ce à une méthode, le générateur de liaisons génère une version de cette méthode avec le suffixe `Async` .  Si le rappel n’accepte aucun paramètre, la valeur de retour est `Task` , si le rappel prend un paramètre, le résultat est `Task<T>` .  Si le rappel prend plusieurs paramètres, vous devez définir `ResultType` ou `ResultTypeName` pour spécifier le nom souhaité du type généré qui contiendra toutes les propriétés.

Exemple :

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

<a name="Surfacing_Strong_Types"></a>

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>Types forts de surface pour les paramètres NSDictionary faibles

À de nombreux endroits de l’API objective-C, les paramètres sont passés comme des API faiblement typées `NSDictionary` avec des clés et des valeurs spécifiques, mais elles sont sujettes aux erreurs (vous pouvez passer des clés non valides et ne pas recevoir d’avertissements. vous pouvez transmettre des valeurs non valides et ne pas recevoir d’avertissements) et frustrantes pour une utilisation de plusieurs fois.

La solution consiste à fournir une version fortement typée qui fournit la version fortement typée de l’API et en arrière-plan met en correspondance les différentes clés et valeurs sous-jacentes.

Par exemple, si l’API objective-C a accepté un `NSDictionary` et qu’elle est documentée comme prenant la clé `XyzVolumeKey` qui accepte un `NSNumber` avec une valeur de volume comprise entre 0,0 et 1,0 et une `XyzCaptionKey` qui prend une chaîne, vous souhaitez que vos utilisateurs aient une API intéressante ressemblant à ceci :

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

La `Volume` propriété est définie en tant que valeur float Nullable, étant donné que la Convention en Objective-C ne requiert pas que ces dictionnaires aient la valeur, il existe donc des scénarios où la valeur ne peut pas être définie.

Pour ce faire, vous devez effectuer quelques opérations :

- Créez une classe fortement typée, qui sous-classe [DictionaryContainer](xref:Foundation.DictionaryContainer) et fournit les différents getters et setters pour chaque propriété.
- Déclarez des surcharges pour les méthodes `NSDictionary` qui prennent la nouvelle version fortement typée.

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

Si votre API n’a pas besoin d’être remplacée, vous pouvez masquer en toute sécurité l’API basée sur NSDictionary à l’aide de la [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
.

Comme vous pouvez le voir, nous utilisons la [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
attribut pour la surface d’un nouveau point d’entrée d’API, et nous l’avons vu à l’aide de notre classe fortement typée `XyzOptions` .  La méthode wrapper autorise également la réussite de la valeur null.

Maintenant, nous n’avons pas mentionné que les `XyzOptionsKeys` valeurs proviennent.  En général, vous regroupez les clés qu’une API a surfaces dans une classe statique comme `XyzOptionsKeys` , comme suit :

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

Pour créer un dictionnaire fortement typé, introduisez une interface dans votre API et décorez-la avec l’attribut [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) .  Cela indique au générateur qu’il doit créer une classe portant le même nom que votre interface `DictionaryContainer` et qui fournira des accesseurs fortement typés pour celui-ci.

L' [`[StrongDictionary]`](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attribut accepte un paramètre, qui est le nom de la classe statique qui contient vos clés de dictionnaire.  Ensuite, chaque propriété de l’interface devient un accesseur fortement typé.  Par défaut, le code utilise le nom de la propriété avec le suffixe « Key » dans la classe statique pour créer l’accesseur.

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

Si vous devez faire référence à `XyzOption` un champ différent dans vos membres (qui n’est pas le nom de la propriété avec le suffixe `Key` ), vous pouvez décorer la propriété avec un [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribut portant le nom que vous souhaitez utiliser.

<a name="Type_mappings"></a>

## <a name="type-mappings"></a>Mappages de types

Cette section décrit comment les types objective-C sont mappés aux types C#.

<a name="Simple_Types"></a>

### <a name="simple-types"></a>Types simples

Le tableau suivant montre comment mapper des types de l’environnement objective-C et CocoaTouch au monde Xamarin. iOS :

|Nom du type objective-C|Type de API unifiée Xamarin. iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([plus d’informations sur la liaison chaîne NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (voir aussi : [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring) )|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Types CoreFoundation ( `CF*` )|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Types de base ( `NS*` )|`Foundation.NS*`|
|`id`|`Foundation`.`NSObject`|
|`NSGlyph`|`nint`|
|`NSSize`|`CGSize`|
|`NSTextAlignment`|`UITextAlignment`|
|`SEL`|`ObjCRuntime.Selector`|
|`dispatch_queue_t`|`CoreFoundation.DispatchQueue`|
|`CFTimeInterval`|`double`|
|`CFIndex`|`nint`|
|`NSGlyph`|`nuint`|

<a name="Arrays"></a>

### <a name="arrays"></a>Tableaux

Le runtime Xamarin. iOS se charge automatiquement de la conversion des tableaux C# en `NSArrays` et de la reconversion, par exemple la méthode imaginaire objective-C qui retourne un `NSArray` de `UIViews` :

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

L’idée est d’utiliser un tableau C# fortement typé, car cela permet à l’IDE de fournir la saisie semi-automatique du code avec le type réel sans forcer l’utilisateur à deviner, ou de rechercher la documentation pour déterminer le type réel des objets contenus dans le tableau.

Dans les cas où vous ne pouvez pas effectuer le suivi du type le plus dérivé réel contenu dans le tableau, vous pouvez utiliser `NSObject []` comme valeur de retour.

<a name="Selectors"></a>

### <a name="selectors"></a>Sélecteurs

Les sélecteurs apparaissent sur l’API objective-C comme type spécial `SEL` . Lors de la liaison d’un sélecteur, vous devez mapper le type à `ObjCRuntime.Selector` .  En général, les sélecteurs sont exposés dans une API avec un objet, l’objet cible et un sélecteur à appeler dans l’objet cible. Le fait de fournir ces deux éléments correspond essentiellement au délégué C# : un objet qui encapsule la méthode à appeler, ainsi que l’objet pour appeler la méthode dans.

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

Pour rendre la liaison plus attrayante pour les développeurs C#, vous fournissez généralement une méthode qui prend un `NSAction` paramètre, ce qui permet d’utiliser des délégués et des expressions lambda C# à la place de `Target+Selector` . Pour ce faire, vous devez généralement masquer la `SetTarget` méthode en la marquant avec un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
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

<a name="Strings"></a>

### <a name="strings"></a>Chaînes

Lorsque vous liez une méthode qui prend un `NSString` , vous pouvez remplacer celle-ci par un type de chaîne C#, à la fois sur les types de retour et les paramètres.

Le seul cas où vous souhaiterez peut-être utiliser un `NSString` directement est lorsque la chaîne est utilisée en tant que jeton. Pour plus d’informations sur les chaînes et `NSString` , consultez le document [conception d’API sur chaîne NSString](~/ios/internals/api-design/nsstring.md) .

Dans certains cas rares, une API peut exposer une chaîne de type C ( `char *` ) au lieu d’une chaîne objective-c ( `NSString *` ). Dans ce cas, vous pouvez annoter le paramètre avec l' [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
.

<a name="outref_parameters"></a>

### <a name="outref-parameters"></a>paramètres out/Ref

Certaines API retournent des valeurs dans leurs paramètres, ou passent des paramètres par référence.

En général, la signature se présente comme suit :

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

Le premier exemple montre un idiome objective-C commun pour retourner des codes d’erreur, un pointeur vers un `NSError` pointeur est passé et, à la suite du retour, la valeur est définie.   La deuxième méthode montre comment une méthode objective-C peut prendre un objet et modifier son contenu.   Il s’agit d’un passage par référence, plutôt que d’une valeur de sortie pure.

Votre liaison ressemble à ceci :

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes"></a>

### <a name="memory-management-attributes"></a>Attributs de gestion de la mémoire

Lorsque vous utilisez l' [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut et que vous passez des données qui seront conservées par la méthode appelée, vous pouvez spécifier la sémantique d’argument en la passant comme deuxième paramètre, par exemple :

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

L’exemple ci-dessus marque la valeur comme ayant la sémantique « retain ». Les sémantiques disponibles sont les suivantes :

- Affecter
- Copier
- Conserver

<a name="Style_Guidelines"></a>

### <a name="style-guidelines"></a>Règles de style

<a name="Using_[Internal]"></a>

#### <a name="using-internal"></a>Utilisation de [Internal]

Vous pouvez utiliser la [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attribut pour masquer une méthode de l’API publique. Vous pouvez le faire dans les cas où l’API exposée est trop faible et que vous souhaitez fournir une implémentation de haut niveau dans un fichier distinct basé sur cette méthode.

Vous pouvez également l’utiliser lorsque vous rencontrez des restrictions dans le générateur de liaisons, par exemple certains scénarios avancés peuvent exposer des types qui ne sont pas liés et que vous souhaitez lier de votre propre manière, et que vous souhaitez encapsuler ces types vous-même de votre propre manière.

<a name="Event_Handlers_and_Callbacks"></a>

## <a name="event-handlers-and-callbacks"></a>Gestionnaires d’événements et rappels

Les classes objective-C diffusent généralement des notifications ou demandent des informations en envoyant un message sur une classe déléguée (délégué objective-C).

Ce modèle, bien qu’il soit entièrement pris en charge et exposé par Xamarin. iOS peut parfois être fastidieux. Xamarin. iOS expose le modèle d’événement C# et un système de rappel de méthode sur la classe qui peut être utilisé dans ces situations. Cela permet au code comme celui-ci de s’exécuter :

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Le générateur de liaisons peut réduire la quantité de saisie requise pour mapper le modèle objective-C dans le modèle C#.

À compter de Xamarin. iOS 1,4, il est possible de demander également au générateur de générer des liaisons pour un délégué objective-C spécifique et d’exposer le délégué en tant qu’événements et propriétés C# sur le type d’hôte.

Deux classes sont impliquées dans ce processus, la classe hôte qui est celle qui émet actuellement des événements et envoie celles-ci dans `Delegate` ou `WeakDelegate` et la classe déléguée réelle.

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
   déclaration le type qui agit comme son délégué et le nom C# que vous avez exposé. Dans notre exemple ci-dessus, il s’agit de `typeof (MyClassDelegate)` et `WeakDelegate` respectivement.
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

`EventArgs`Est utilisé pour spécifier le nom de la `EventArgs` classe à générer. Vous devez utiliser un par signature (dans cet exemple, le `EventArgs` contient une `With` propriété de type nint).

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

Les rappels sont tout comme les appels d’événement, la différence est que, au lieu d’avoir plusieurs abonnés potentiels (par exemple, plusieurs méthodes peuvent se raccorder à un `Clicked` événement ou à un `DownloadFinished` événement), les rappels ne peuvent avoir qu’un seul abonné.

Le processus est identique, la seule différence est que, au lieu d’exposer le nom de la `EventArgs` classe qui sera générée, EventArgs est utilisé en fait pour nommer le nom de délégué C# résultant.

Si la méthode dans la classe déléguée retourne une valeur, le générateur de liaisons le mappe à une méthode déléguée dans la classe parente au lieu d’un événement. Dans ce cas, vous devez fournir la valeur par défaut qui doit être retournée par la méthode si l’utilisateur n’est pas raccordé au délégué. Pour ce faire, utilisez l' [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
[`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)attributs ou.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) coder en dur une valeur de retour, alors que [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
est utilisé pour spécifier l’argument d’entrée qui sera retourné.

<a name="Enumerations_and_Base_Types"></a>

## <a name="enumerations-and-base-types"></a>Énumérations et types de base

Vous pouvez également référencer des énumérations ou des types de base qui ne sont pas directement pris en charge par le système de définition d’interface btouch. Pour ce faire, placez vos énumérations et types principaux dans un fichier distinct et incluez-les dans l’un des fichiers supplémentaires que vous fournissez à btouch.

<a name="Linking_the_Dependencies"></a>

## <a name="linking-the-dependencies"></a>Liaison des dépendances

Si vous liez des API qui ne font pas partie de votre application, vous devez vous assurer que votre exécutable est lié à ces bibliothèques.

Vous devez informer Xamarin. iOS comment lier vos bibliothèques. pour ce faire, vous pouvez modifier votre configuration de build pour appeler la `mtouch` commande avec des arguments de build supplémentaires qui spécifient comment établir une liaison avec les nouvelles bibliothèques à l’aide de l’option « -gcc_flags », suivie d’une chaîne entre guillemets qui contient toutes les bibliothèques supplémentaires requises pour votre programme, comme suit :

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

L’exemple ci-dessus crée un lien `libMyLibrary.a` `libSystemLibrary.dylib` et la `CFNetwork` bibliothèque d’infrastructure dans votre exécutable final.

Vous pouvez aussi tirer parti du niveau assembly [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) , que vous pouvez incorporer dans vos fichiers de contrat (tels que `AssemblyInfo.cs` ).
Lorsque vous utilisez l' [`[LinkWithAttribute]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) , vous devez disposer de la bibliothèque Native disponible au moment où vous effectuez la liaison, car cela incorporera la bibliothèque native avec votre application. Par exemple :

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Vous vous demandez peut-être pourquoi vous avez besoin de `-force_load` la commande, et la raison en est que l’indicateur-objc bien qu’il compile le code dans, il ne conserve pas les métadonnées requises pour prendre en charge les catégories (l’élimination de l’éditeur de liens/du compilateur) dont vous avez besoin au moment de l’exécution pour Xamarin. iOS.

<a name="Assisted_References"></a>

## <a name="assisted-references"></a>Références assistées

Certains objets temporaires, tels que les feuilles d’action et les zones d’alerte, sont fastidieux à suivre pour les développeurs et le générateur de liaisons peut vous aider un peu ici.

Par exemple, si vous avez une classe qui affichait un message, puis générait un `Done` événement, la méthode classique de traitement de ce serait la suivante :

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

Pour tirer parti de cela, votre classe doit avoir une propriété Events définie dans la [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) déclaration et également la `KeepUntilRef` variable définie sur le nom de la méthode appelée lorsque l’objet a terminé son travail, comme suit :

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols"></a>

## <a name="inheriting-protocols"></a>Héritage de protocoles

À compter de Xamarin. iOS v 3.2, nous prenons en charge l’héritage des protocoles qui ont été marqués avec la [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) propriété. Cela est utile dans certains modèles d’API, tels que dans, `MapKit` où le `MKOverlay` protocole hérite du `MKAnnotation` protocole et est adopté par un certain nombre de classes qui héritent de `NSObject` .

Historiquement, nous avons nécessité de copier le protocole à chaque implémentation, mais dans ce cas, nous pouvons maintenant faire en sorte que la `MKShape` classe hérite du `MKOverlay` protocole et qu’elle génère automatiquement toutes les méthodes requises.

## <a name="related-links"></a>Liens connexes

- [Exemple de liaison](/samples/xamarin/ios-samples/bindingsample/)