---
title: Bibliothèques de liaison Objective-C
description: Ce document fournit une vue d’ensemble de la création C# liaisons au code Objective-C, qui décrit comment lier des événements, des méthodes, des contrôles personnalisés et bien plus encore.
ms.prod: xamarin
ms.assetid: 8A832A76-A770-1A7C-24BA-B3E6F57617A0
author: conceptdev
ms.author: crdun
ms.date: 03/06/2018
ms.openlocfilehash: 306cce581eb1506e770222ea10e160c4fdbe1b29
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827486"
---
# <a name="binding-objective-c-libraries"></a>Bibliothèques de liaison Objective-C

Lorsque vous travaillez avec Xamarin.iOS ou Xamarin.Mac, vous pouvez rencontrer des cas où vous souhaitez utiliser une bibliothèque Objective-C de fournisseurs tiers. Dans ces situations, vous pouvez utiliser des projets de liaison Xamarin pour créer un C# liaison aux bibliothèques Objective-C natifs. Le projet utilise les mêmes outils que nous utilisons pour ramener l’iOS et les API Mac à C#.

Ce document décrit comment lier les API Objective-C, si vous liez simplement les API C, vous devez utiliser le mécanisme de .NET standard pour ce faire, [l’infrastructure de P/Invoke](https://www.mono-project.com/docs/advanced/pinvoke/).
Pour plus d’informations sur la façon de lier statiquement une bibliothèque C sont disponibles sur le [liaison de bibliothèques natives](~/ios/platform/native-interop.md) page.

Voir le Guide de notre [Guide de référence des Types de liaison](~/cross-platform/macios/binding/binding-types-reference.md).
En outre, si vous souhaitez en savoir plus sur ce qui se passe sous le capot, vérifier notre [vue d’ensemble de la liaison](~/cross-platform/macios/binding/overview.md) page.

Liaisons peuvent être générées pour les bibliothèques iOS et Mac.
Cette page explique comment travailler sur un iOS de liaison, toutefois, les liaisons Mac sont très similaires.

**Exemple de Code pour iOS**

Vous pouvez utiliser la [iOS de liaison, exemple](https://github.com/xamarin/monotouch-samples/tree/master/BindingSample) projet à faire des essais avec des liaisons.

<a name="Getting_Started" />

## <a name="getting-started"></a>Bien démarrer

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Le moyen le plus simple de créer une liaison consiste à créer un projet de liaison Xamarin.iOS.
Vous pouvez cela à partir de Visual Studio pour Mac, sélectionnez le type de projet **iOS > bibliothèque > bibliothèque de liaisons**:

[![](objective-c-libraries-images/00-sml.png "Cela à partir de Visual Studio pour Mac, sélectionnez le type de projet bibliothèque de liaisons de bibliothèque iOS")](objective-c-libraries-images/00.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Le moyen le plus simple de créer une liaison consiste à créer un projet de liaison Xamarin.iOS.
Cela en sélectionnant le type de projet à partir de Visual Studio sur Windows **Visual C# > iOS > bibliothèque de liaisons (iOS)** :

[![](objective-c-libraries-images/00vs-sml.png "iOS iOS de la bibliothèque de liaisons")](objective-c-libraries-images/00vs.png#lightbox)

> [!IMPORTANT]
> Remarque : Liaison de projets pour **Xamarin.Mac** sont uniquement pris en charge dans Visual Studio pour Mac.

-----

Le projet généré contient un modèle petit que vous pouvez modifier, il contient deux fichiers : `ApiDefinition.cs` et `StructsAndEnums.cs`.

Le `ApiDefinition.cs` est où vous allez définir le contrat d’API, c’est le fichier qui décrit comment l’API Objective-C sous-jacente est projeté dans C#. La syntaxe et le contenu de ce fichier est le sujet principal de la discussion de ce document et le contenu de celui-ci est limité à C# interfaces et C# déclarations delegate. Le `StructsAndEnums.cs` est le fichier dans lequel vous entrerez toutes les définitions qui sont requis par les interfaces et les délégués. Cela inclut les valeurs d’énumération et structures que votre code peut utiliser.

<a name="Binding_an_API" />

## <a name="binding-an-api"></a>Liaison d’une API

Pour effectuer une liaison complète, vous devez comprendre la définition d’API de Objective-C et de vous familiariser avec les instructions de conception de .NET Framework.

Pour lier votre bibliothèque, vous commencerez généralement avec un fichier de définition d’API. Un fichier de définition d’API est simplement un C# fichier source qui contient C# interfaces qui ont été annotées avec un certain nombre d’attributs qui aident le lecteur de la liaison.  Ce fichier est ce qui définit le contrat entre C# et Objective-C est.

Par exemple, il s’agit d’un fichier d’api simple pour une bibliothèque :

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

L’exemple ci-dessus définit une classe appelée `Cocos2D.Camera` qui dérive de la `NSObject` type de base (ce type provient `Foundation.NSObject`) et qui définit une propriété statique (`ZEye`), deux méthodes qui acceptent aucun argument et une méthode qui prend trois arguments.

Une présentation détaillée du format du fichier API et les attributs que vous pouvez utiliser est couvert dans le [fichier de définition d’API](~/cross-platform/macios/binding/objective-c-libraries.md#The_API_definition_file) section ci-dessous.

Pour produire une liaison terminée, vous serez traitent généralement les quatre composants :

-  Le fichier de définition d’API (`ApiDefinition.cs` dans le modèle).
-  Facultatif : les énumérations, les types, structs requis par le fichier de définition d’API (`StructsAndEnums.cs` dans le modèle).
-  Facultatif : les sources supplémentaires qui peuvent développer la liaison générée, ou fournir plus C# API convivial (n’importe quel C# fichiers que vous ajoutez au projet).
-  La bibliothèque native que vous liez.

Ce graphique montre la relation entre les fichiers :

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png "Ce graphique montre la relation entre les fichiers")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.33.07-pm.png#lightbox)

Le fichier de définition d’API ne contienne que les définitions d’espaces de noms et d’interface (avec tous les membres contenant une interface) et ne doit pas contenir les classes, énumérations, délégués ou les structs. Le fichier de définition d’API est simplement le contrat qui doit être utilisé pour générer l’API.

Tout code supplémentaire que vous avez besoin comme énumérations ou prenant en charge les classes doit être hébergée sur un fichier distinct, dans l’exemple ci-dessus la « CameraMode » est une valeur d’énumération qui n’existe pas dans le fichier CS et doit être hébergée dans un fichier distinct, par exemple `StructsAndEnums.cs` :

```csharp
public enum CameraMode {
    FlyOver, Back, Follow
}
```

Le `APIDefinition.cs` fichier est associé le `StructsAndEnum` classe et sont utilisées pour générer la liaison de base de la bibliothèque. Vous pouvez utiliser la bibliothèque résultant en tant que-est, mais en règle générale, vous pouvez paramétrer la bibliothèque résultante pour ajouter certaines C# fonctionnalités au profit de vos utilisateurs. Certains exemples incluent la mise en œuvre un `ToString()` (méthode), fournir C# indexeurs, ajouter des conversions implicites vers et à partir de certains types natifs ou fournir des versions fortement typées de certaines méthodes. Ces améliorations sont stockées dans extra C# fichiers. Ajoutez simplement le C# fichiers à votre projet et ils ne seront inclus dans ce processus de génération.

Cela montre comment implémenter le code dans votre `Extra.cs` fichier. Notez que vous utilisez des classes partielles comme ils augmentent les classes partielles qui sont générés à partir de la combinaison de la `ApiDefinition.cs` et `StructsAndEnums.cs` liaison de base :

```csharp
public partial class Camera {
    // Provide a ToString method
    public override string ToString ()
    {
         return String.Format ("ZEye: {0}", ZEye);
    }
}
```

Création de la bibliothèque produira votre liaison natif.

Pour effectuer cette liaison, vous devez ajouter la bibliothèque native au projet.  Procéder en ajoutant la bibliothèque native à votre projet, soit en faisant glisser la bibliothèque native du Finder sur le projet dans l’Explorateur de solutions, soit en double-cliquant sur le projet et en choisissant **ajouter**  >  **Ajouter des fichiers** pour sélectionner la bibliothèque native.
Les bibliothèques natives par convention commençant par le mot « lib » et se terminent par l’extension « .a ». Lorsque vous effectuez cette opération, Visual Studio pour Mac ajoute deux fichiers : le fichier .a et remplies automatiquement C# fichier qui contient des informations sur ce que contient la bibliothèque native :

 [![](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png "Les bibliothèques natives par convention démarrer avec la bibliothèque de word et se terminent par l’extension .a")](objective-c-libraries-images/screen-shot-2012-02-08-at-3.45.06-pm.png#lightbox)

Le contenu de la `libMagicChord.linkwith.cs` fichier contient des informations sur la façon dont cette bibliothèque peut être utilisée et indique à votre IDE pour empaqueter ce binaire dans le fichier résultant de la DLL :

```csharp
using System;
using ObjCRuntime;

[assembly: LinkWith ("libMagicChord.a", SmartLink = true, ForceLoad = true)]
```

Plus de détails sur la façon d’utiliser la [`[LinkWith]`](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute) 
attribut sont documentées dans le [Guide de référence des Types de liaison](~/cross-platform/macios/binding/binding-types-reference.md).

Maintenant lorsque vous générez le projet vous finirez avec un `MagicChords.dll` fichier qui contient la liaison et la bibliothèque native. Vous pouvez distribuer ce projet, ou utiliser la DLL résultante à d’autres développeurs pour leurs propres.

Parfois, vous constaterez peut-être que vous avez besoin de plusieurs valeurs d’énumération, les définitions de délégué ou d’autres types. Ne placez pas ceux du fichier de définitions d’API, car il s’agit simplement d’un contrat

<a name="The_API_definition_file" />

## <a name="the-api-definition-file"></a>Le fichier de définition d’API

Le fichier de définition d’API se compose d’un nombre d’interfaces. Les interfaces dans la définition d’API seront activés dans une déclaration de classe, et elles doivent être décorées avec le [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) attribut pour spécifier la classe de base pour la classe.

Vous vous demandez peut-être pourquoi nous n’avez pas utilisé les classes au lieu d’interfaces pour la définition du contrat. Nous avons choisi d’interfaces, car elle nous a permis d’écrire le contrat pour une méthode sans avoir à fournir un corps de méthode dans le fichier de définition d’API, ou avoir à fournir un corps qui avait pour lever une exception ou retourne une valeur significative.

Mais étant donné que nous utilisons l’interface comme un squelette pour générer une classe que nous avons dû recourir à décorer les différentes parties du contrat avec des attributs pour piloter la liaison.

<a name="Binding_Methods" />

### <a name="binding-methods"></a>Méthodes de liaison

La liaison la plus simple, que vous pouvez faire consiste à lier une méthode. Déclarez une méthode dans l’interface avec le C# conventions de nommage et complétez la méthode avec le [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attribut. Le [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut fait le lien votre C# nom avec le nom Objective-C dans le runtime Xamarin.iOS. Le paramètre de la [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribut est le nom du sélecteur Objective-C. Voici quelques exemples :

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

Les exemples ci-dessus montrent comment vous pouvez lier les méthodes d’instance. Pour lier des méthodes statiques, vous devez utiliser le [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attribut, comme suit :

```csharp
// A static method, that takes no arguments
[Static, Export ("refresh")]
void Beep ();
```

Cela est nécessaire, car le contrat fait partie d’une interface et interfaces n’ont aucune notion de déclarations d’instance statique de Visual Studio, il est donc nécessaire une fois encore de recourir à des attributs. Si vous souhaitez masquer une méthode particulière de la liaison, vous pouvez décorer la méthode avec le [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attribut.

Le `btouch-native` commande introduira des contrôles pour les paramètres de référence ne pas être null. Si vous souhaitez autoriser les valeurs null pour un paramètre particulier, utilisez le [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
attribut sur le paramètre, comme ceci :

```csharp
[Export ("setText:")]
string SetText ([NullAllowed] string text);
```

Lors de l’exportation d’un type référence, avec le [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) mot clé que vous pouvez également spécifier la sémantique d’allocation. Cela est nécessaire pour s’assurer qu’aucune donnée n’est divulguée.

<a name="Binding_Properties" />

### <a name="binding-properties"></a>Propriétés de liaison

Tout comme les méthodes, les propriétés de Objective-C sont liées à l’aide de la [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute)
attribut et mappées directement à C# propriétés. Tout comme les méthodes, les propriétés peuvent être décorées avec le [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute)
et le [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
Attributs.

Lorsque vous utilisez le [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut sur une propriété sous btouch natif de l’arrière-plan lie en fait deux méthodes : la méthode getter et setter. Le nom que vous fournissez à exporter est le **basename** et l’accesseur Set est calculée en ajoutant le mot « jeu », l’activation de la première lettre de la **basename** en majuscules et rendre le sélecteur de prendre un argument. Cela signifie que `[Export ("label")]` appliquée sur une propriété lie plutôt le « label » et « setLabel : » Méthodes objective-C.

Parfois, les propriétés Objective-C ne suivent pas le modèle décrit ci-dessus et le nom est remplacé manuellement. Dans ce cas, vous pouvez contrôler la façon dont que la liaison est générée à l’aide de la [`[Bind]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAttribute) 
attribut sur l’accesseur Get ou Set, par exemple :

```csharp
[Export ("menuVisible")]
bool MenuVisible { [Bind ("isMenuVisible")] get; set; }
```

Alors lie « isMenuVisible » et « setMenuVisible : ». Si vous le souhaitez, une propriété peut être liée à l’aide de la syntaxe suivante :

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

Où les méthodes getter et setter définis explicitement en tant que dans le `name` et `setName` liaisons ci-dessus.

Outre la prise en charge pour les propriétés statiques à l’aide de [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute), vous pouvez décorer les propriétés statiques de thread avec [ `[IsThreadStatic]` ](~/cross-platform/macios/binding/binding-types-reference.md#IsThreadStaticAttribute), par exemple :

```csharp
[Export ("currentRunLoop")][Static][IsThreadStatic]
NSRunLoop Current { get; }
```

Tout comme les méthodes permettent de certains paramètres être marquée avec [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute), vous pouvez appliquer [`[NullAllowed]`](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute)
à une propriété pour indiquer que la valeur null est une valeur valide pour la propriété, par exemple :

```csharp
[Export ("text"), NullAllowed]
string Text { get; set; }
```

Le [ `[NullAllowed]` ](~/cross-platform/macios/binding/binding-types-reference.md#NullAllowedAttribute) paramètre peut également être spécifié directement sur l’accesseur Set :

```csharp
[Export ("text")]
string Text { get; [NullAllowed] set; }
```

#### <a name="caveats-of-binding-custom-controls"></a>Mises en garde de liaison de contrôles personnalisés

Les remarques suivantes doivent être considérés lors de la configuration de la liaison pour un contrôle personnalisé :

1. **Propriétés de liaison doivent être statiques** - lors de la définition de la liaison des propriétés, la [ `[Static]` ](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute) attribut doit être utilisé.
 2. **Noms de propriété doivent correspondre exactement à** -le nom utilisé pour lier la propriété doit correspondre au nom de la propriété dans le contrôle personnalisé exactement.
3. **Types de propriété doivent correspondre exactement** -le type de variable utilisé pour lier la propriété doit correspondre au type de la propriété dans le contrôle personnalisé exactement.
4. **Points d’arrêt et la méthode getter/setter** : points d’arrêt sont placés dans l’accesseur Get ou les méthodes d’accesseur Set de la propriété ne seront jamais atteint.
5. **Observez les rappels** -vous devez utiliser des rappels d’observation pour être averti des modifications dans les valeurs de propriété des contrôles personnalisés.

Pour observer les avertissements répertoriés ci-dessus peut entraîner la liaison échoue silencieusement lors de l’exécution.

<a name="MutablePattern" />

#### <a name="objective-c-mutable-pattern-and-properties"></a>Propriétés et modèle mutable objective-C

Les infrastructures objective-C utilisent un idiome où certaines classes sont immuables avec une sous-classe mutable. Par exemple `NSString` est la version immuable, tandis que `NSMutableString` est la sous-classe qui permet la mutation.

Dans ces classes, il est courant de voir la classe de base immuable contiennent des propriétés avec un accesseur Get, mais pas d’accesseur Set. Et pour la version mutable introduire la méthode setter. Dans la mesure où cela n’est pas vraiment possible avec C#, nous avions mapper cet idiome dans un idiome qui fonctionnerait avec C#.

La façon dont il est mappé vers C# par l’ajout de la méthode getter et setter sur la classe de base, mais en marquant la méthode setter avec un [`[NotImplemented]`](~/cross-platform/macios/binding/binding-types-reference.md#NotImplementedAttribute)
attribut.

Puis, dans la sous-classe mutable, vous utilisez le [`[Override]`](~/cross-platform/macios/binding/binding-types-reference.md#OverrideAttribute) 
attribut sur la propriété pour vous assurer que la propriété est en fait substitution du comportement du parent.

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

<a name="Binding_Constructors" />

### <a name="binding-constructors"></a>Constructeurs de liaison

Le `btouch-native` outil génère automatiquement des constructeurs d’épreuve dans votre classe, pour une classe donnée `Foo`, il génère :

-  `Foo ()`: le constructeur par défaut (qui correspond à un constructeur de « init » d’Objective-C)
-  `Foo (NSCoder)`: le constructeur utilisé pendant la désérialisation des fichiers NIB (mappe à Objective-C » initWithCoder : « constructeur).
-  `Foo (IntPtr handle)`: le constructeur pour la création de handle, elle est appelée par le runtime lorsque le runtime doit exposer un objet managé à partir d’un objet non managé.
-  `Foo (NSEmptyFlag)`: il est utilisé par les classes dérivées pour empêcher l’initialisation double.

Pour les constructeurs que vous définissez, ils doivent être déclarés à l’aide de la signature suivante à l’intérieur de la définition d’Interface : elles doivent retourner une `IntPtr` valeur et le nom de la méthode doivent être de constructeur. Par exemple pour lier le `initWithFrame:` constructeur, voici ce que vous utilisez :

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);
```

<a name="Binding_Protocols" />

### <a name="binding-protocols"></a>Protocoles de liaison

Comme décrit dans le document de conception d’API, dans la section [traitant des modèles et des protocoles](~/ios/internals/api-design/index.md#Models), Xamarin.iOS mappe les protocoles Objective-C dans les classes qui ont été marquées avec le [`[Model]`](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute)
attribut. Cela est généralement utilisé lors de l’implémentation des classes de délégué Objective-C.

La principale différence entre une classe de liée normale et une classe déléguée est que la classe de délégué peut avoir une ou plusieurs méthodes facultatives.

Considérez par exemple le `UIKit` classe `UIAccelerometerDelegate`, voici comment elle est liée dans Xamarin.iOS :

```csharp
[BaseType (typeof (NSObject))]
[Model][Protocol]
interface UIAccelerometerDelegate {
        [Export ("accelerometer:didAccelerate:")]
        void DidAccelerate (UIAccelerometer accelerometer, UIAcceleration acceleration);
}
```

Dans la mesure où il s’agit d’une méthode facultative sur la définition de `UIAccelerometerDelegate` rien faire d’autre à faire. Mais si une méthode requise s’est produite sur le protocole, vous devez ajouter le [`[Abstract]`](~/cross-platform/macios/binding/binding-types-reference.md#AbstractAttribute)
attribut à la méthode. Cela forcera l’utilisateur de l’implémentation pour fournir un corps pour la méthode.

En général, les protocoles sont utilisés dans les classes qui répondent aux messages. Cela est généralement effectuée en Objective-C, affectez à la propriété « delegate » d’une instance d’un objet qui répond aux méthodes dans le protocole.

La convention dans Xamarin.iOS est pour prendre en charge les deux le Objective-C faiblement couplées style où une instance d’un `NSObject` peut avoir pour le délégué et expose également une version fortement typée. Pour cette raison, nous fournissons généralement à la fois un `Delegate` propriété qui est fortement typée et un `WeakDelegate` faiblement typée. Nous lions généralement la version faiblement typé avec [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute), et nous utilisons le [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attribut pour fournir la version fortement typée.

Cet exemple montre comment nous liée la `UIAccelerometer` classe :

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

**Nouveautés de MonoTouch 7.0**

À partir de MonoTouch 7.0, un fonctionnalité de liaison de protocole de nouvel et amélioré incorporée.  Cette nouvelle prise en charge rend plus simple d’utiliser les idiomes Objective-C pour arrêter un ou plusieurs protocoles dans une classe donnée.

Pour chaque définition de protocole `MyProtocol` en Objective-C, il existe désormais une `IMyProtocol` interface qui répertorie toutes les méthodes requises du protocole, ainsi que d’une classe d’extension qui fournit toutes les méthodes facultatives.  L’exemple ci-dessus, combinée avec la nouvelle prise en charge dans l’éditeur permet aux développeurs d’implémenter des méthodes de protocole sans avoir à utiliser les sous-classes distincts des classes de modèle abstrait précédente Xamarin Studio.

Toute définition qui contient le [ `[Protocol]` ](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) attribut génère en fait trois classes de prise en charge qui améliorent considérablement la façon que vous consommez des protocoles :

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

Le **implémentation de la classe** fournit une classe abstraite complète que vous pouvez substituer des méthodes individuelles d’et obtenir la sécurité de type complet.  Mais en raison C# prenant ne pas en charge l’héritage multiple, il existe des scénarios où vous pouvez doit avoir une autre classe de base, mais vous souhaitez toujours implémenter une interface, ce qui est l’emplacement où le

Le texte généré **définition d’interface** arrive.  Il est une interface qui possède toutes les méthodes requises du protocole.  Cela permet aux développeurs qui souhaitent implémenter votre protocole pour simplement implémenter l’interface.  Le runtime s’inscriront automatiquement le type en tant que l’adoption du protocole.

Notez que l’interface répertorie les méthodes requises uniquement et n’expose pas les méthodes facultatives.  Cela signifie que les classes qui adoptent le protocole obtiendra complète vérification de type pour les méthodes requises, mais ont recours au typage faible (manuellement à l’aide [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attributs et correspondre à la signature) pour les méthodes de protocole facultatifs.

Pour faciliter le consommer une API qui utilise des protocoles, l’outil de liaison également produira une classe de méthode d’extensions qui expose toutes les méthodes facultatives.  Cela signifie que tant que vous consommez une API, vous serez en mesure de traiter des protocoles comme ayant toutes les méthodes.

Si vous souhaitez utiliser les définitions de protocole dans votre API, vous devez écrire des interfaces vides squelettes dans votre définition d’API.  Si vous souhaitez utiliser le MyProtocol dans une API, vous devez le faire :

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

La méthode ci-dessus est nécessaire, car au moment de la liaison la `IMyProtocol` n’existerait pas, autrement dit pourquoi vous devez fournir une interface vide.

#### <a name="adopting-protocol-generated-interfaces"></a>Adoption d’interfaces générées par le protocole

Chaque fois que vous implémentez une des interfaces générées pour les protocoles, comme suit :

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

L’implémentation pour les méthodes d’interface obtient automatiquement exportée avec le nom approprié, par conséquent, il est équivalent à cet :

```csharp
class MyDelegate : NSObject, IUITableViewDelegate {
    [Export ("getRowHeight:")]
    nint IUITableViewDelegate.GetRowHeight (nint row) {
        return 1;
    }
}
```

Si l’interface est implémentée de manière implicite ou explicite n’a pas d’importance.

<a name="Binding_Class_Extensions" />

### <a name="binding-class-extensions"></a>Extensions de la classe de liaison

En Objective-C, il est possible d’étendre les classes avec de nouvelles méthodes, même esprit à C#de méthodes d’extension. Lorsqu’une de ces méthodes est présente, vous pouvez utiliser la [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attribut pour marquer la méthode comme étant le récepteur du message Objective-C.

Par exemple, dans Xamarin.iOS nous lié les méthodes d’extension qui sont définis sur `NSString` lorsque `UIKit` est importé en tant que méthodes dans le `NSStringDrawingExtensions`, comme suit :

```csharp
[Category, BaseType (typeof (NSString))]
interface NSStringDrawingExtensions {
    [Export ("drawAtPoint:withFont:")]
    CGSize DrawString (CGPoint point, UIFont font);
}
```

<a name="Binding_Objective-C_Argument_Lists" />

### <a name="binding-objective-c-argument-lists"></a>Liaison des listes d’arguments Objective-C

Objective-C prend en charge les arguments de variadiques. Exemple :

```objc
- (void) appendWorkers:(XWorker *) firstWorker, ...
  NS_REQUIRES_NIL_TERMINATION ;
```

Pour appeler cette méthode à partir de C# vous souhaitez créer une signature comme suit :

```csharp
[Export ("appendWorkers"), Internal]
void AppendWorkers (Worker firstWorker, IntPtr workersPtr)
```

Cela déclare la méthode comme internes, masquage de l’API ci-dessus à partir d’utilisateurs, mais les exposer à la bibliothèque. Ensuite, vous pouvez écrire une méthode comme suit :

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

Parfois, vous devez accéder à des champs publics qui ont été déclarés dans une bibliothèque.

Ces champs contiennent généralement des valeurs de chaînes ou des entiers qui doivent être référencées. Ils sont souvent utilisés en tant que chaîne qui représente une notification spécifique et en tant que clés dans les dictionnaires.

Pour lier un champ, ajouter une propriété à votre fichier de définition d’interface et décorer la propriété avec le [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attribut. Cet attribut prend un seul paramètre : le nom de C du symbole à rechercher. Exemple :

```csharp
[Field ("NSSomeEventNotification")]
NSString NSSomeEventNotification { get; }
```

Si vous voulez encapsuler les différents champs dans une classe statique qui ne dérive pas de `NSObject`, vous pouvez utiliser la [`[Static]`](~/cross-platform/macios/binding/binding-types-reference.md#StaticAttribute_Class) 
attribut de la classe, comme suit :

```csharp
[Static]
interface LonelyClass {
    [Field ("NSSomeEventNotification")]
    NSString NSSomeEventNotification { get; }
}
```

La méthode ci-dessus générera un `LonelyClass` qui ne dérive pas de `NSObject` et contiendra une liaison à la `NSSomeEventNotification` 
 `NSString` exposées en tant qu’un `NSString`.

Le [ `[Field]` ](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute) attribut peut être appliqué aux types de données suivants :

-  `NSString` références (uniquement pour les propriétés en lecture seule)
-  `NSArray` références (uniquement pour les propriétés en lecture seule)
-  les entiers 32 bits (`System.Int32`)
-  les entiers 64 bits (`System.Int64`)
-  valeurs en virgule flottante 32 bits (`System.Single`)
-  valeurs en virgule flottante 64 bits (`System.Double`)
-  `System.Drawing.SizeF`
-  `CGSize`

Outre le nom du champ natif, vous pouvez spécifier le nom de la bibliothèque où se trouve le champ, en passant le nom de la bibliothèque :

```csharp
[Static]
interface LonelyClass {
    [Field ("SomeSharedLibrarySymbol", "SomeSharedLibrary")]
    NSString SomeSharedLibrarySymbol { get; }
}
```

Si vous effectuez une liaison statique, aucune bibliothèque n’est à lier, vous devez donc utiliser le `__Internal` nom :

```csharp
[Static]
interface LonelyClass {
    [Field ("MyFieldFromALibrary", "__Internal")]
    NSString MyFieldFromALibrary { get; }
}
```

<a name="Binding_Enums" />

### <a name="binding-enums"></a>Énumérations de liaison

Vous pouvez ajouter `enum` directement dans votre liaison de fichiers à facilite les utiliser à l’intérieur des définitions d’API - sans utiliser un autre fichier source (devant être compilé dans les liaisons et le projet final).

Exemple :

```csharp
[Native] // needed for enums defined as NSInteger in ObjC
enum MyEnum {}

interface MyType {
    [Export ("initWithEnum:")]
    IntPtr Constructor (MyEnum value);
}
```

Il est également possible de créer vos propres enums pour remplacer `NSString` constantes. Dans ce cas le générateur est **automatiquement** créer les méthodes pour convertir les valeurs des énumérations et des constantes de chaîne NSString pour vous.

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

Dans l’exemple ci-dessus, vous pouvez décider décorer `void Perform (NSString mode);` avec un [ `[Internal]` ](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute) attribut. Cela va **masquer** l’API basée sur une constante à partir de vos consommateurs de liaison.

Toutefois, cela limiterait sous-classement le type en tant que l’API mieux autre utilise un [ `[Wrap]` ](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute) attribut. Ces méthodes générées ne sont pas `virtual`, par exemple, vous ne sont pas être en mesure de les remplacer - qui peuvent, ou non, être un bon choix.

Une alternative consiste à marquer l’original, `NSString`-basé, définition en tant que `[Protected]`. Cela permettra de sous-classement de fonctionner, si nécessaire, et la version wrap'ed toujours travaillera et appelez la méthode substituée.

### <a name="binding-nsvalue-nsnumber-and-nsstring-to-a-better-type"></a>Liaison `NSValue`, `NSNumber`, et `NSString` à un meilleur type

Le [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) attribut autorise la liaison `NSNumber`, `NSValue` et `NSString`(énumérations) en plus précises C# types. L’attribut peut être utilisé pour créer la meilleure et plus précis, les API .NET sur l’API native.

Vous pouvez décorer les méthodes (sur la valeur de retour), les paramètres et les propriétés avec [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute). La seule restriction est que votre membre **ne doit pas** être à l’intérieur d’un [`[Protocol]`](~/cross-platform/macios/binding/binding-types-reference.md#ProtocolAttribute) 
ou [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) interface.

Exemple :

```csharp
[return: BindAs (typeof (bool?))]
[Export ("shouldDrawAt:")]
NSNumber ShouldDraw ([BindAs (typeof (CGRect))] NSValue rect);
```

Devrait produire :

```csharp
[Export ("shouldDrawAt:")]
bool? ShouldDraw (CGRect rect) { ... }
```

En interne, nous ferons le `bool?`  <->  `NSNumber` et `CGRect`  <->  `NSValue` conversions.

[`[BindAs]`](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) prend également en charge les tableaux de `NSNumber` `NSValue` et `NSString`(énumérations).

Exemple :

```csharp
[BindAs (typeof (CAScroll []))]
[Export ("supportedScrollModes")]
NSString [] SupportedScrollModes { get; set; }
```

Devrait produire :

```csharp
[Export ("supportedScrollModes")]
CAScroll [] SupportedScrollModes { get; set; }
```

`CAScroll` est un `NSString` soutenu enum, nous avons extrait droite `NSString` valeur et de gérer la conversion de type.

Veuillez consulter la [ `[BindAs]` ](~/cross-platform/macios/binding/binding-types-reference.md#BindAsAttribute) documentation pour voir les types de conversion prises en charge.

<a name="Binding_Notifications" />

### <a name="binding-notifications"></a>Notifications de liaison

Les notifications sont des messages qui sont publiés sur le `NSNotificationCenter.DefaultCenter` et sont utilisés comme un mécanisme pour diffuser des messages à partir d’une partie de l’application vers un autre. Les développeurs s’abonner aux notifications en général à l’aide de la [NSNotificationCenter](xref:Foundation.NSNotificationCenter)de [AddObserver](xref:Foundation.NSNotificationCenter.AddObserver(Foundation.NSString,System.Action{Foundation.NSNotification})) (méthode). Lorsqu’une application publie un message vers le centre de notification, il contient généralement une charge utile stockée dans le [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dictionnaire. Ce dictionnaire est faiblement typé et l’obtention des informations hors il est sujet aux erreurs, comme les utilisateurs doivent généralement lire dans la documentation, les clés sont disponibles sur le dictionnaire et les types des valeurs qui peuvent être stockées dans le dictionnaire. La présence de clés parfois est utilisée comme une valeur booléenne également.

Le Générateur de liaison Xamarin.iOS prend en charge pour les développeurs à lier des notifications. Pour ce faire, vous définissez le [`[Notification]`](~/cross-platform/macios/binding/binding-types-reference.md#NotificationAttribute)
attribut sur une propriété qui a été également été marqués avec un [`[Field]`](~/cross-platform/macios/binding/binding-types-reference.md#FieldAttribute)
propriété (il peut être public ou privé).

Cet attribut peut être utilisé sans arguments pour les notifications qui ne transmettent aucune charge utile, ou vous pouvez spécifier un `System.Type` qui fait référence à une autre interface dans la définition d’API, généralement avec le nom se terminant par « EventArgs ». Le générateur activera l’interface dans une classe qui sous-classe `EventArgs` et inclut toutes les propriétés qui y figurent. Le [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut doit être utilisé dans la classe EventArgs pour répertorier le nom de la clé utilisée pour rechercher le dictionnaire Objective-C pour extraire la valeur.

Exemple :

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

Les utilisateurs de votre code peuvent ensuite facilement s’abonner aux notifications publiées sur le [NSDefaultCenter](xref:Foundation.NSNotificationCenter.DefaultCenter) à l’aide de code similaire à celui-ci :

```csharp
var token = MyClass.Notifications.ObserverDidStart ((notification) => {
    Console.WriteLine ("Observed the 'DidStart' event!");
});
```

La valeur retournée à partir de `ObserveDidStart` peut être utilisé pour facilement cesser de recevoir des notifications, comme suit :

```csharp
token.Dispose ();
```

Ou vous pouvez appeler [NSNotification.DefaultCenter.RemoveObserver](xref:Foundation.NSNotificationCenter.RemoveObserver(Foundation.NSObject)) et passer le jeton. Si votre notification contient des paramètres, vous devez spécifier un programme d’assistance `EventArgs` interface, comme suit :

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

La méthode ci-dessus génère un `MyScreenChangedEventArgs` classe avec le `ScreenX` et `ScreenY` propriétés qui permettent d’extraire les données à partir de la [NSNotification.UserInfo](xref:Foundation.NSNotification.UserInfo) dictionnaire à l’aide de clés de « ScreenXKey » et « ScreenYKey » respectivement et appliquer les conversions appropriées. Le `[ProbePresence]` attribut est utilisé pour le générateur pour détecter si la clé est définie le `UserInfo`, au lieu de tenter d’extraire la valeur. Cela est utilisé pour les cas où la présence de la clé est la valeur (en général pour les valeurs booléennes).

Cela vous permet d’écrire du code comme suit :

```csharp
var token = MyClass.NotificationsObserveScreenChanged ((notification) => {
    Console.WriteLine ("The new screen dimensions are {0},{1}", notification.ScreenX, notification.ScreenY);
});
```

<a name="Binding_Categories" />

### <a name="binding-categories"></a>Catégories de liaison

Les catégories sont un mécanisme Objective-C permet d’étendre l’ensemble des méthodes et propriétés disponibles dans une classe.   Dans la pratique, ils sont utilisés pour étendre les fonctionnalités d’une classe de base (par exemple `NSObject`) quand un framework spécifique est lié dans (par exemple `UIKit`), rendre leurs méthodes, mais uniquement si la nouvelle infrastructure est liée dans.   Dans d’autres cas, ils sont utilisés pour organiser des fonctionnalités dans une classe par fonctionnalité.   Elles sont similaires à l’esprit pour C# méthodes d’extension. Voici à quoi ressemblerait une catégorie dans Objective-c :

```csharp
@interface UIView (MyUIViewExtension)
-(void) makeBackgroundRed;
@end
```

L’exemple ci-dessus si trouvée sur une bibliothèque s’étendrait des instances de `UIView` avec la méthode `makeBackgroundRed`.

Pour lier les, vous pouvez utiliser la [ `[Category]` ](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute) attribut sur une définition d’interface.  Lorsque vous utilisez le [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attribut, la signification de la [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) 
attribut change d’être utilisé pour spécifier la classe de base pour étendre, soit le type à étendre.

Le suivant montre comment la `UIView` extensions sont liées et transformées C# méthodes d’extension :

```csharp
[BaseType (typeof (UIView))]
[Category]
interface MyUIViewExtension {
    [Export ("makeBackgroundRed")]
    void MakeBackgroundRed ();
}
```

La méthode ci-dessus créera un `MyUIViewExtension` une classe qui contient le `MakeBackgroundRed` méthode d’extension.  Cela signifie que vous pouvez maintenant appeler « MakeBackgroundRed » sur n’importe quel `UIView` sous-classe, ce qui vous donne les mêmes fonctionnalités que vous obtiendriez sur Objective-C. Dans d’autres cas, les catégories sont utilisées ne pas étendre une classe système, mais pour organiser des fonctionnalités purement à des fins de décoration.  Comme ceci :

```csharp
@interface SocialNetworking (Twitter)
- (void) postToTwitter:(Message *) message;
@end

@interface SocialNetworking (Facebook)
- (void) postToFacebook:(Message *) message andPicture: (UIImage*)
picture;
@end
```

Bien que vous pouvez utiliser la [`[Category]`](~/cross-platform/macios/binding/binding-types-reference.md#CategoryAttribute)
attribut également pour ce style de la décoration de déclarations, vous pouvez également simplement ajouter les tous à la définition de classe.  Ces deux types d’atteindre le même :

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

Elle est simplement plus courte dans ce cas de fusionner les catégories :

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

Les blocs sont une nouvelle construction introduite par Apple pour mettre l’équivalent fonctionnel de C# méthodes anonymes pour Objective-C. Par exemple, le `NSSet` classe expose maintenant cette méthode :

```csharp
- (void) enumerateObjectsUsingBlock:(void (^)(id obj, BOOL *stop) block
```

La description ci-dessus déclare une méthode appelée `enumerateObjectsUsingBlock:` qui prend un argument nommé `block`. Ce bloc est similaire à un C# méthode anonyme dans qu’il a prise en charge pour la capture de l’environnement actuel (le pointeur « this », accès aux variables locales et paramètres). La méthode ci-dessus dans `NSSet` appelle le bloc avec deux paramètres un `NSObject` (le `id obj` partie) et un pointeur vers une valeur booléenne (la `BOOL *stop`) partie.

Pour lier ce type d’API avec btouch, vous devez d’abord déclarer la signature de type de bloc en tant qu’un C# déléguer et de le référencer à partir d’un point d’entrée d’API, comme suit :

```csharp
// This declares the callback signature for the block:
delegate void NSSetEnumerator (NSObject obj, ref bool stop)

// Later, inside your definition, do this:
[Export ("enumerateObjectUsingBlock:")]
void Enumerate (NSSetEnumerator enum)
```

Et maintenant votre code peut appeler votre fonction à partir de C#:

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

Le Générateur de liaison peut transformer une certaine classe de méthodes async conviviale méthodes (méthodes qui retournent une tâche ou une tâche&lt;T&gt;).

Vous pouvez utiliser la [`[Async]`](~/cross-platform/macios/binding/binding-types-reference.md#AsyncAttribute) 
attribut sur les méthodes qui retournent void et dont le dernier argument est un rappel.  Lorsque vous appliquez ce à une méthode, le Générateur de liaison générera une version de cette méthode avec le suffixe `Async`.  Si le rappel ne prend aucun paramètre, la valeur de retour sera un `Task`, si le rappel prend un paramètre, le résultat sera un `Task<T>`.  Si le rappel prend plusieurs paramètres, vous devez définir le `ResultType` ou `ResultTypeName` pour spécifier le nom de votre choix du type généré qui contiendra toutes les propriétés.

Exemple :

```csharp
[Export ("loadfile:completed:")]
[Async]
void LoadFile (string file, Action<string> completed);
```

Le code ci-dessus génère les deux la méthode LoadFile, ainsi que :

```csharp
[Export ("loadfile:completed:")]
Task<string> LoadFileAsync (string file);
```

<a name="Surfacing_Strong_Types" />

### <a name="surfacing-strong-types-for-weak-nsdictionary-parameters"></a>En exposant des types forts pour les paramètres NSDictionary faibles

Dans de nombreux endroits dans l’API Objective-C, les paramètres sont passés comme faiblement typée `NSDictionary` API avec des clés spécifiques et des valeurs, mais elles sont sujettes à des (vous pouvez transmettre les clés non valides et n’obtenir aucun avertissement ; vous pouvez passer des valeurs non valides et n’obtenir aucun avertissement) et frustrante à utiliser car elles requièrent plusieurs allers-retours à la documentation pour rechercher les noms de clés possibles et les valeurs.

La solution consiste à fournir une version fortement typée qui fournit que la version fortement typée de l’API et dans les coulisses mappe les sous-jacent clés et les valeurs.

Ainsi par exemple, si l’API Objective-C accepté une `NSDictionary` et elle est documentée comme prenant la clé `XyzVolumeKey` qui accepte un `NSNumber` avec une valeur de volume entre 0.0 et 1.0 et un `XyzCaptionKey` qui prend une chaîne, vous souhaiteriez vos utilisateurs disposent d’une API intéressante qui ressemble à ceci :

```csharp
public class  XyzOptions {
    public nfloat? Volume { get; set; }
    public string Caption { get; set; }
}
```

Le `Volume` propriété est définie comme nullable float, car la convention en Objective-C ne nécessite pas ces dictionnaires pour avoir la valeur, donc il existe des scénarios où la valeur ne peut pas être définie.

Pour ce faire, vous devez effectuer quelques opérations :

* Créer une classe fortement typée, qui sous-classe [DictionaryContainer](xref:Foundation.DictionaryContainer) et fournit les diverses méthodes getter et setter pour chaque propriété.
* Déclarer des surcharges pour les méthodes qui prennent des `NSDictionary` à prendre la nouvelle version fortement typée.

Vous pouvez créer la classe fortement typée soit manuellement ou utiliser le générateur pour effectuer le travail pour vous.  Tout d’abord, nous explorons comment effectuer cette opération manuellement afin de comprendre ce qui se passe, puis sur l’approche automatique.

Vous devez créer un fichier de support pour cela, il n’aborde pas votre contrat API.  C’est ce que vous devez écrire pour créer votre classe XyzOptions :

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

Vous devez ensuite fournir une méthode de wrapper qui expose l’API de haut niveau par-dessus l’API de bas niveau.

```csharp
[BaseType (typeof (NSObject))]
interface XyzPanel {
    [Export ("playback:withOptions:")]
    void Playback (string fileName, [NullAllowed] NSDictionary options);

    [Wrap ("Playback (fileName, options == null ? null : options.Dictionary")]
    void Playback (string fileName, XyzOptions options);
}
```

Si votre API sans devoir être remplacées, vous pouvez masquer en toute sécurité l’API NSDictionary à l’aide de la [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attribut.

Comme vous pouvez le voir, nous utilisons le [`[Wrap]`](~/cross-platform/macios/binding/binding-types-reference.md#WrapAttribute)
attribut utilisé pour faire apparaître un nouveau point d’entrée API, et nous présenterons à l’aide de notre fortement typée `XyzOptions` classe.  Il permet également la méthode de wrapper pour la valeur null à passer.

À présent, une chose que nous ne pas mentionner est là le `XyzOptionsKeys` provient de valeurs.  Vous devez généralement regrouper les clés qui met en évidence une API dans une classe statique comme `XyzOptionsKeys`, comme suit :

```csharp
[Static]
class XyzOptionKeys {
    [Field ("kXyzVolumeKey")]
    NSString VolumeKey { get; }

    [Field ("kXyzCaptionKey")]
    NSString CaptionKey { get; }
}
```

Nous examinons la prise en charge automatique pour la création de ces dictionnaires fortement typée.  Cela évite beaucoup de code, et vous pouvez définir le dictionnaire directement dans votre contrat d’API, au lieu d’utiliser un fichier externe.

Pour créer un dictionnaire fortement typé, introduire une interface dans votre API et la décorer avec le [StrongDictionary](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attribut.  Cela indique le générateur qu’il doit créer une classe avec le même nom que votre interface dérive `DictionaryContainer` et fournira des accesseurs typés forts pour celui-ci.

Le [ `[StrongDictionary]` ](~/cross-platform/macios/binding/binding-types-reference.md#StrongDictionary) attribut prend un paramètre, qui est le nom de la classe statique qui contient vos clés de dictionnaire.  Chaque propriété de l’interface deviennent alors un accesseur fortement typé.  Par défaut, le code utilise le nom de la propriété avec le suffixe « Key » dans la classe statique pour créer l’accesseur.

Cela signifie que la création de votre accesseur fortement typées ne requiert plus un fichier externe, ni avoir à créer manuellement des accesseurs Get et Set pour chaque propriété, ni avoir à rechercher les clés manuellement vous-même.

Voici à quoi ressemblera votre ensemble de la liaison :

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

Au cas où vous devez référencer dans votre `XyzOption` membres un autre champ (autrement dit pas le nom de la propriété avec le suffixe `Key`), vous pouvez décorer la propriété avec un [`[Export]`](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) 
attribut avec le nom que vous souhaitez utiliser.

<a name="Type_mappings" />

## <a name="type-mappings"></a>Mappages de types

Cette section décrit comment les types d’Objective-C sont mappés aux C# types.

<a name="Simple_Types" />

### <a name="simple-types"></a>Types simples

Le tableau suivant montre comment vous devez mapper les types à partir du Objective-C et le monde de CocoaTouch au monde Xamarin.iOS :

|Nom du type d’objective-C|Type d’API unifiée Xamarin.iOS|
|---|---|
|`BOOL`, `GLboolean`|`bool`|
|`NSInteger`|`nint`|
|`NSUInteger`|`nuint`|
|`CFTimeInterval` / `NSTimeInterval`|`double`|
|`NSString` ([plus sur la liaison de chaîne NSString](~/ios/internals/api-design/nsstring.md))|`string`|
|`char *`|`string` (voir aussi : [ `[PlainString]` ](~/cross-platform/macios/binding/binding-types-reference.md#plainstring))|
|`CGRect`|`CGRect`|
|`CGPoint`|`CGPoint`|
|`CGSize`|`CGSize`|
|`CGFloat`, `GLfloat`|`nfloat`|
|Types de CoreFoundation (`CF*`)|`CoreFoundation.CF*`|
|`GLint`|`nint`|
|`GLfloat`|`nfloat`|
|Types de base (`NS*`)|`Foundation.NS*`|
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

Le runtime Xamarin.iOS prend automatiquement en charge de la conversion C# tableaux `NSArrays` et effectuer la conversion, par exemple la méthode Objective-C imaginaire qui retourne un `NSArray` de `UIViews`:

```csharp
// Get the peer views - untyped
- (NSArray *)getPeerViews ();

// Set the views for this container
- (void) setViews:(NSArray *) views
```

Est lié à ceci :

```csharp
[Export ("getPeerViews")]
UIView [] GetPeerViews ();

[Export ("setViews:")]
void SetViews (UIView [] views);
```

L’idée consiste à utiliser un fortement typée C# tableau car cela permettra l’IDE pour fournir la complétion de code approprié avec le type réel sans forcer l’utilisateur à deviner ou rechercher la documentation pour connaître le type réel des objets contenus dans le tableau.

Dans les cas où vous ne pouvez pas suivre vers le bas le type réel de la plus dérivé contenu dans le tableau, vous pouvez utiliser `NSObject []` comme valeur de retour.

<a name="Selectors" />

### <a name="selectors"></a>Sélecteurs

Sélecteurs apparaissent sur l’API Objective-C en tant que le type spécial `SEL`. Lors de la liaison d’un sélecteur, vous devez mapper le type à `ObjCRuntime.Selector`.  En général, les sélecteurs sont exposés dans une API avec un objet, l’objet cible et un sélecteur pour appeler dans l’objet cible. Ces deux fournissant essentiellement correspond à la C# déléguer : quelque chose qui encapsule la méthode à appeler ainsi que l’objet pour appeler la méthode dans.

Voici à quoi ressemble la liaison :

```csharp
interface Button {
   [Export ("setTarget:selector:")]
   void SetTarget (NSObject target, Selector sel);
}
```

Et c’est la façon dont la méthode doit généralement être utilisée dans une application :

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

Pour rendre la liaison mieux à C# les développeurs, vous en général fournissent une méthode qui prend un `NSAction` paramètre, ce qui permet à C# délégués et expressions lambda à être utilisée au lieu du `Target+Selector`. Pour cela est généralement conseillé de masquer le `SetTarget` méthode en lui ajoutant un indicateur avec un [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attribut, puis vous expose une nouvelle méthode d’assistance, comme suit :

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

À présent, votre code utilisateur peut être écrit comme suit :

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

Lorsque vous liez une méthode qui prend un `NSString`, vous pouvez remplacer qu’avec un C# type, à la fois sur les types de retour et les paramètres de chaîne.

Le seul cas lorsque vous souhaiterez peut-être utiliser un `NSString` est directement lors de la chaîne est utilisée comme un jeton. Pour plus d’informations sur les chaînes et `NSString`, lisez le [conception d’API sur chaîne NSString](~/ios/internals/api-design/nsstring.md) document.

Dans certains cas rares, une API peut exposer une chaîne de type C (`char *`) au lieu d’une chaîne Objective-C (`NSString *`). Dans ce cas, vous pouvez annoter le paramètre avec le [`[PlainString]`](~/cross-platform/macios/binding/binding-types-reference.md#plainstring)
attribut.

<a name="outref_parameters" />

### <a name="outref-parameters"></a>out / paramètres ref

Certaines API retournent des valeurs dans leurs paramètres, ou transmettre des paramètres par référence.

En général, la signature ressemble à ceci :

```csharp
- (void) someting:(int) foo withError:(NSError **) retError
- (void) someString:(NSObject **)byref
```

Le premier exemple montre un idiome courant de Objective-C pour retourner des codes d’erreur, un pointeur vers un `NSError` pointeur est passé, et au retour, la valeur est définie.   La deuxième méthode montre comment une méthode Objective-C peut prendre un objet et modifier son contenu.   Il s’agit d’un passage par référence, plutôt qu’une valeur de sortie pure.

Votre liaison ressemblerait à ceci :

```csharp
[Export ("something:withError:")]
void Something (nint foo, out NSError error);
[Export ("someString:")]
void SomeString (ref NSObject byref);
```

<a name="Memory_management_attributes" />

### <a name="memory-management-attributes"></a>Attributs de gestion de mémoire

Lorsque vous utilisez le [ `[Export]` ](~/cross-platform/macios/binding/binding-types-reference.md#ExportAttribute) attribut et que vous transmettez des données qui seront conservées par la méthode appelée, vous pouvez spécifier la sémantique de l’argument en lui passant comme second paramètre, par exemple :

```csharp
[Export ("method", ArgumentSemantic.Retain)]
```

La méthode ci-dessus est indicateur la valeur comme ayant la sémantique de « Durée de stockage ». La sémantique disponible est :

-  Assigner
-  Copier
-  Conserver

<a name="Style_Guidelines" />

### <a name="style-guidelines"></a>Règles de style

<a name="Using_[Internal]" />

#### <a name="using-internal"></a>À l’aide de [interne]

Vous pouvez utiliser la [`[Internal]`](~/cross-platform/macios/binding/binding-types-reference.md#InternalAttribute)
attribut pour masquer une méthode à partir de l’API publique. Vous souhaiterez peut-être effectuer cette opération dans le cas où l’API exposée est trop bas niveau et que vous souhaitez fournir une implémentation de haut niveau dans un fichier distinct, en fonction de cette méthode.

Vous pouvez également utiliser lorsque vous rencontrez des limitations dans le Générateur de liaison, par exemple certains scénarios avancés peuvent exposer des types qui ne sont pas liés et vous souhaitez lier votre propre façon vous souhaitez encapsuler ces types dans votre propre façon.

<a name="Event_Handlers_and_Callbacks" />

## <a name="event-handlers-and-callbacks"></a>Gestionnaires d’événements et rappels

En général, les classes objective-C diffuser des notifications ou demander des informations en envoyant un message sur une classe déléguée (délégué Objective-C).

Ce modèle, tandis qu’entièrement pris en charge et affichées par Xamarin.iOS peut parfois être fastidieuse. Xamarin.iOS expose le C# modèle d’événement et un système de rappel de méthode sur la classe qui peut être utilisé dans ces situations. Cela permet au code comme suit pour exécuter :

```csharp
button.Clicked += delegate {
    Console.WriteLine ("I was clicked");
};
```

Le Générateur de liaison est capable de réduire la quantité de frappe requise pour mapper le modèle Objective-C dans le C# modèle.

En commençant avec Xamarin.iOS 1.4, il sera possible d’également demander au générateur pour produire des liaisons pour un spécifique délégués Objective-C et exposer le délégué en tant que C# événements et propriétés sur le type d’hôte.

Il existe deux classes impliquées dans ce processus, la classe hôte qui sera est celui qui émet des événements et envoie ces informations dans le `Delegate` ou `WeakDelegate` et la classe delegate réelle.

Considérant la configuration suivante :

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

-  Dans votre classe d’hôte, ajoutez à votre [`[BaseType]`](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute)  
   déclaration de type qui agit comme son délégué et le C# nom que vous avez exposée. Dans notre exemple ci-dessus, ceux-ci sont `typeof (MyClassDelegate)` et `WeakDelegate` respectivement.
-  Dans votre classe de délégué, sur chaque méthode qui a plus de deux paramètres, vous devez spécifier le type que vous souhaitez utiliser pour la classe EventArgs générée automatiquement.

Le Générateur de liaison n’est pas limité d’habillage uniquement une destination de l’événement unique, il est possible que certaines classes Objective-C à émettre des messages à plus d’un délégué, donc vous devrez fournir des tableaux pour prendre en charge cette configuration. La plupart des configurations ne le n'aurez pas besoin, mais le générateur est prêt à prendre en charge de ces cas.

Le code résultant sera :

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

Le `EventArgs` est utilisé pour spécifier le nom de la `EventArgs` classe à générer. Vous devez utiliser un par signature (dans cet exemple, le `EventArgs` contiendra un `With` propriété de type nint).

Avec les définitions ci-dessus, le générateur produit l’événement suivant dans la MyClass générée :

```csharp
public MyClassLoadedEventArgs : EventArgs {
        public MyClassLoadedEventArgs (nint bytes);
        public nint Bytes { get; set; }
}

public event EventHandler<MyClassLoadedEventArgs> Loaded {
        add; remove;
}
```

Par conséquent, vous pouvez maintenant utiliser le code comme ceci :

```csharp
MyClass c = new MyClass ();
c.Loaded += delegate (sender, args){
        Console.WriteLine ("Loaded event with {0} bytes", args.Bytes);
};
```

Rappels ressemblent aux appels d’événements, la différence est que, au lieu d’avoir plusieurs abonnés potentiels (par exemple, plusieurs méthodes peuvent raccorder un `Clicked` événement ou un `DownloadFinished` événement) rappels ne peuvent avoir qu’un seul abonné.

Le processus est identique, la seule différence est que, au lieu d’exposer le nom de la `EventArgs` classe qui doit être généré, EventArgs réellement est utilisé pour nommer résultant C# nom du délégué.

Si la méthode dans la classe déléguée retourne une valeur, le Générateur de liaison sera le mapper à une méthode de délégué dans la classe parente au lieu d’un événement. Dans ces cas, vous devez fournir la valeur par défaut qui doit être retournée par la méthode si l’utilisateur ne pas raccorder au délégué. Procéder à l’aide de la [`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute)
ou [ `[DefaultValueFromArgument]` ](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute) attributs.

[`[DefaultValue]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueAttribute) est de coder en dur une valeur de retour, alors que [`[DefaultValueFromArgument]`](~/cross-platform/macios/binding/binding-types-reference.md#DefaultValueFromArgumentAttribute)
permet de spécifier quel argument d’entrée sera retourné.

<a name="Enumerations_and_Base_Types" />

## <a name="enumerations-and-base-types"></a>Énumérations et les types de base

Vous pouvez également référencer des énumérations ou des types de base qui ne sont pas directement pris en charge par le système de définition d’interface btouch. Pour ce faire, placez votre énumérations et les types de base dans un fichier distinct et les inclure dans le cadre d’un des fichiers supplémentaires que vous fournissez à btouch.

<a name="Linking_the_Dependencies" />

## <a name="linking-the-dependencies"></a>Lier les dépendances

Si vous liez des API qui ne font pas partie de votre application, vous devez vous assurer que votre fichier exécutable est lié par rapport à ces bibliothèques.

Vous devez informer Xamarin.iOS comment lier vos bibliothèques, cela peut être effectué en modifiant votre configuration de build pour appeler le `mtouch` commande avec certains extra générer des arguments qui spécifient comment effectuer une liaison avec les nouvelles bibliothèques à l’aide de la «-gcc_flags » option, suivi d’une chaîne entre guillemets qui contient toutes les bibliothèques supplémentaires sont nécessaires pour votre programme, comme suit :

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load -lSystemLibrary -framework CFNetwork -ObjC"
```

L’exemple ci-dessus sera liée `libMyLibrary.a`, `libSystemLibrary.dylib` et `CFNetwork` bibliothèque framework dans votre fichier exécutable final.

Ou vous pouvez tirer parti de niveau de l’assembly [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), que vous pouvez incorporer dans vos fichiers de contrat (tel que `AssemblyInfo.cs`).
Lorsque vous utilisez le [ `[LinkWithAttribute]` ](~/cross-platform/macios/binding/binding-types-reference.md#LinkWithAttribute), vous devez avoir votre bibliothèque native disponible au moment que vous prenez votre liaison, lorsque cette action incorpore la bibliothèque native avec votre application. Exemple :

```csharp
// Specify only the library name as a constructor argument and specify everything else with properties:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget = LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]

// Or you can specify library name *and* link target as constructor arguments:
[assembly: LinkWith ("libMyLibrary.a", LinkTarget.ArmV6 | LinkTarget.ArmV7 | LinkTarget.Simulator, ForceLoad = true, IsCxx = true)]
```

Vous vous demandez peut-être, pourquoi devez-vous `-force_load` commande et la raison est que le ObjC - indicateur bien qu’il compile le code, il ne conserve pas les métadonnées nécessaires pour prendre en charge les catégories (l’élimination de code mort de l’éditeur de liens/le compilateur supprime il) que vous besoin lors de l’exécution pour Xamarin.iOS.

<a name="Assisted_References" />

## <a name="assisted-references"></a>Références assistées

Certains objets temporaires tels que les feuilles de l’action et les zones de l’alerte sont difficiles à suivre pour les développeurs et le Générateur de liaison peut aider un peu ici.

Par exemple, si vous aviez une classe qui vous a montré un message et puis généré un `Done` événement, la méthode traditionnelle de gérer cela serait :

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

Dans le scénario ci-dessus, le développeur doit conserver la référence à l’objet lui-même et soit fuite ou activement effacer la référence pour sa propre zone.  Prend en charge du code de liaison, le Générateur de suivi de la référence pour vous et clair, le cas d’une méthode spéciale est appelée, le code ci-dessus puis deviendrait :

```csharp
class Demo {
    void ShowError (string msg)
    {
        var box = new MessageBox (msg);
        box.Done += { ... };
    }
}
```

Notez la façon dont il n’est plus nécessaire de conserver la variable dans une instance, qu’elle fonctionne avec une variable locale et qu’il n’est pas nécessaire effacer la référence lors de l’objet meurt.

Pour tirer parti de cela, votre classe doit avoir une propriété d’événements définie le [ `[BaseType]` ](~/cross-platform/macios/binding/binding-types-reference.md#BaseTypeAttribute) déclaration et également le `KeepUntilRef` variable définie sur le nom de la méthode est appelée lorsque l’objet a terminé son travail, comme Cela :

```csharp
[BaseType (typeof (NSObject), KeepUntilRef="Dismiss"), Delegates=new string [] { "WeakDelegate" }, Events=new Type [] { typeof (SomeDelegate) }) ]
class Demo {
    [Export ("show")]
    void Show (string message);
}
```

<a name="Inheriting_Protocols" />

## <a name="inheriting-protocols"></a>Hériter des protocoles

Depuis Xamarin.iOS v3.2, nous prenons en charge héritant de protocoles qui ont été marqués avec le [ `[Model]` ](~/cross-platform/macios/binding/binding-types-reference.md#ModelAttribute) propriété. Cela est utile dans certains modèles d’API, comme dans `MapKit` où le `MKOverlay` de protocole, hérite de la `MKAnnotation` de protocole et est adopté par un nombre de classes qui héritent de `NSObject`.

Nous nécessitaient copie le protocole à chaque implémentation, mais dans ce cas maintenant nous pouvons avoir le `MKShape` classe hériter la `MKOverlay` protocole et il génère toutes les méthodes requises automatiquement.

## <a name="related-links"></a>Liens connexes

- [Exemple de liaison](https://developer.xamarin.com/samples/monotouch/BindingSample/)
