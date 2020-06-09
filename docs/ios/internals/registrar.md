---
title: Entrer le Bureau d’enregistrement pour Xamarin. iOS
description: Ce document décrit le Bureau d’enregistrement de types Xamarin. iOS, qui rend les classes C# disponibles pour le runtime objective-C.
ms.prod: xamarin
ms.assetid: 610A0834-1141-4D09-A05E-B7ADF99462C5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 1a664b197b9e1a28f19ef9fd90a6f1dd26c159b4
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84565055"
---
# <a name="type-registrar-for-xamarinios"></a>Entrer le Bureau d’enregistrement pour Xamarin. iOS

Ce document décrit le système d’enregistrement de type utilisé par Xamarin. iOS.

## <a name="registration-of-managed-classes-and-methods"></a>Inscription des classes et des méthodes managées

Au démarrage, Xamarin. iOS s’inscrit :

- Classes avec un attribut [[register]](xref:Foundation.RegisterAttribute) en tant que classes objective-C.
- Classes avec un attribut [[category]](xref:ObjCRuntime.CategoryAttribute) en tant que catégories objective-C.
- Interfaces avec un attribut [[Protocole]](xref:Foundation.ProtocolAttribute) en tant que protocoles objective-C.
- Les membres avec un [[Export]](xref:Foundation.ExportAttribute), ce qui permet à Objective-C d’y accéder.

Par exemple, considérez la `Main` méthode managée commune dans les applications Xamarin. iOS :

```csharp
UIApplication.Main (args, null, "AppDelegate");
```

Ce code indique au runtime objective-C d’utiliser le type appelé `AppDelegate` comme classe déléguée de l’application. Pour que le runtime objective-C puisse créer une instance de la classe C# `AppDelegate` , cette classe doit être inscrite.

Xamarin. iOS effectue l’inscription automatiquement, soit au moment de l’exécution (inscription dynamique), soit au moment de la compilation (inscription statique).

L’inscription dynamique utilise la réflexion au démarrage pour rechercher toutes les classes et méthodes à inscrire, en les passant au runtime objective-C. L’inscription dynamique est utilisée par défaut pour les builds de simulateur.

L’inscription statique inspecte, au moment de la compilation, les assemblys utilisés par l’application. Elle détermine les classes et les méthodes à inscrire avec Objective-C et génère un mappage, qui est incorporé dans votre fichier binaire.
Ensuite, au démarrage, il inscrit le mappage avec le runtime objective-C. L’inscription statique est utilisée pour les builds d’appareil.

### <a name="categories"></a>Catégories

À compter de Xamarin. iOS 8,10, il est possible de créer des catégories objective-C à l’aide de la syntaxe C#.

Pour créer une catégorie, utilisez l' `[Category]` attribut et spécifiez le type à étendre. Par exemple, le code suivant étend `NSString` :

```csharp
[Category (typeof (NSString))]
```

Chacune des méthodes d’une catégorie a un `[Export]` attribut, ce qui la rend disponible pour le runtime objective-C :

```csharp
[Export ("today")]
public static string Today ()
{
    return "Today";
}
```

Toutes les méthodes d’extension managées doivent être statiques, mais il est possible de créer des méthodes d’instance objective-C à l’aide de la syntaxe C# standard pour les méthodes d’extension :

```csharp
[Export ("toUpper")]
public static string ToUpper (this NSString self)
{
    return self.ToString ().ToUpper ();
}
```

Le premier argument de la méthode d’extension est l’instance sur laquelle la méthode a été appelée :

```csharp
[Category (typeof (NSString))]
public static class MyStringCategory
{
    [Export ("toUpper")]
    static string ToUpper (this NSString self)
    {
        return self.ToString ().ToUpper ();
    }
 }
 ```

Cet exemple ajoute une `toUpper` méthode d’instance native à la `NSString` classe. Cette méthode peut être appelée à partir de Objective-C :

```csharp
[Category (typeof (UIViewController))]
public static class MyViewControllerCategory
{
    [Export ("shouldAutoRotate")]
    static bool GlobalRotate ()
    {
        return true;
    }
}
```

### <a name="protocols"></a>Protocoles

À compter de Xamarin. iOS 8,10, les interfaces avec l' `[Protocol]` attribut seront exportées vers objective-C en tant que protocoles :

```csharp
[Protocol ("MyProtocol")]
interface IMyProtocol
{
    [Export ("method")]
    void Method ();
}

class MyClass : IMyProtocol
{
    void Method ()
    {
    }
}
```

Ce code exporte `IMyProtocol` vers objective-C en tant que protocole appelé `MyProtocol` et une classe appelée `MyClass` qui implémente le protocole.

## <a name="new-registration-system"></a>Nouveau système d’inscription

À partir de la version stable de 6.2.6 et de la version bêta 6.3.4, nous avons ajouté un nouveau bureau d’enregistrement statique. Dans la version 7.2.1, nous avons fait du nouveau bureau d’enregistrement la valeur par défaut.

Ce nouveau système d’inscription offre les nouvelles fonctionnalités suivantes :

- Détection des erreurs du programmeur au moment de la compilation :
  - Deux classes en cours d’inscription avec le même nom.
  - Plusieurs méthodes exportées pour répondre au même sélecteur
- Suppression du code natif inutilisé :
  - Le nouveau système d’inscription ajoute des références fortes au code utilisé dans les bibliothèques statiques, ce qui permet à l’éditeur de liens natif de supprimer le code natif inutilisé du fichier binaire résultant. Sur les exemples de liaisons de Xamarin, la plupart des applications deviennent au moins 300 000 plus petites.

- Prise en charge des sous-classes génériques de `NSObject` ; pour plus d’informations, consultez [génériques NSObject](~/ios/internals/api-design/nsobject-generics.md) . En outre, le nouveau système d’inscription intercepte les constructions génériques non prises en charge qui auraient précédemment provoqué un comportement aléatoire au moment de l’exécution.

### <a name="errors-caught-by-the-new-registrar"></a>Erreurs détectées par le nouveau bureau d’enregistrement

Voici quelques exemples d’erreurs détectées par le nouveau bureau d’enregistrement.

- Exportation du même sélecteur plusieurs fois dans la même classe :

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo:")]
      void Foo (NSString str);
      [Export ("foo:")]
      void Foo (string str)
  }
  ```

- Exportation de plusieurs classes managées avec le même nom objective-C :

  ```csharp
  [Register ("Class")]
  class MyClass : NSObject {}

  [Register ("Class")]
  class YourClass : NSObject {}
  ```

- Exportation des méthodes génériques :

  ```csharp
  [Register]
  class MyDemo : NSObject
  {
      [Export ("foo")]
      void Foo<T> () {}
  }
  ```

### <a name="limitations-of-the-new-registrar"></a>Limitations du nouveau bureau d’enregistrement

Voici quelques éléments à prendre en compte sur le nouveau bureau d’enregistrement :

- Certaines bibliothèques tierces doivent être mises à jour pour fonctionner avec le nouveau système d’inscription. Pour plus d’informations, consultez les [modifications requises](#required-modifications) ci-dessous.

- Un inconvénient à terme est également que Clang doit être utilisé si le Framework de comptes est utilisé (cela est dû au fait que l’en-tête **Accounts. h** d’Apple ne peut être compilé que par Clang). Ajoutez `--compiler:clang` aux arguments mTouch supplémentaires pour utiliser Clang si vous utilisez xcode 4,6 ou une version antérieure (Xamarin. iOS sélectionne automatiquement Clang dans xcode 5,0 ou version ultérieure).

- Si Xcode 4,6 (ou version antérieure) est utilisé, vous devez sélectionner GCC/G + + si les noms de type exportés contiennent des caractères non-ASCII (cela est dû au fait que la version de Clang fournie avec Xcode 4,6 ne prend pas en charge les caractères non-ASCII dans les identificateurs dans le code Objective-C). Ajoutez `--compiler:gcc` aux arguments mTouch supplémentaires pour utiliser GCC.

## <a name="selecting-a-registrar"></a>Sélection d’un bureau d’enregistrement

Vous pouvez sélectionner un autre bureau d’enregistrement en ajoutant l’une des options suivantes aux arguments mTouch supplémentaires dans les paramètres de **génération iOS** du projet :

- `--registrar:static`– valeur par défaut pour les builds d’appareils
- `--registrar:dynamic`– valeur par défaut pour les builds du simulateur

> [!NOTE]
> Le API classique de Xamarin prenait en charge d’autres options telles que `--registrar:legacystatic` et `--registrar:legacydynamic` . Toutefois, ces options ne sont pas prises en charge par le API unifiée.

## <a name="shortcomings-in-the-old-registration-system"></a>Lacunes dans l’ancien système d’enregistrement

L’ancien système d’inscription présente les inconvénients suivants :

- Il n’existait aucune référence statique (Native) aux classes et méthodes objective-C dans les bibliothèques natives tierces, ce qui signifiait que nous n’aurions pas pu demander à l’éditeur de liens natif de supprimer le code natif tiers qui n’était pas réellement utilisé (car tout serait supprimé). C’est la raison pour `-force_load libNative.a` laquelle chaque liaison tierce devait faire (ou l’équivalent `ForceLoad=true` dans l' `[LinkWith]` attribut).
- Vous pouvez exporter deux types managés avec le même nom objective-C sans avertissement. Un scénario rare consistait à se retrouver avec deux `AppDelegate` classes dans des espaces de noms différents. Au moment de l’exécution, il serait tout à fait aléatoire qu’il était possible de le prélever (en fait, il variait entre les exécutions d’une application qui n’étaient pas encore régénérées, ce qui a été effectué pour une expérience de débogage très curieuse et frustrante).
- Vous pouvez exporter deux méthodes avec la même signature objective-C. Encore une fois, l’appel d’Objective-C était aléatoire (mais ce problème n’était pas aussi courant que le précédent, principalement parce que la seule façon d’expérimenter ce bogue était de remplacer la méthode managée Unlucky).
- L’ensemble des méthodes qui ont été exportées était légèrement différent entre les builds dynamiques et statiques.
- Il ne fonctionne pas correctement lors de l’exportation de classes génériques (l’implémentation générique exacte exécutée au moment de l’exécution est aléatoire, ce qui entraîne un comportement indéterminé).

<a name="required-modifications"></a>

## <a name="new-registrar-required-changes-to-bindings"></a>Nouveau bureau d’enregistrement : modifications requises pour les liaisons

Cette section décrit les modifications de liaison qui doivent être effectuées pour fonctionner avec le nouveau bureau d’enregistrement.

### <a name="protocols-must-have-the-protocol-attribute"></a>Les protocoles doivent avoir l’attribut [Protocole]

Les protocoles doivent maintenant avoir l' `[Protocol]` attribut. Si vous ne le faites pas, vous obtiendrez une erreur native de l’éditeur de liens, par exemple :

```console
Undefined symbols for architecture i386: "_OBJC_CLASS_$_ProtocolName", referenced from: ...
```

### <a name="selectors-must-have-a-valid-number-of-parameters"></a>Les sélecteurs doivent avoir un nombre valide de paramètres

Tous les sélecteurs doivent indiquer correctement le nombre de paramètres. Auparavant, ces erreurs étaient ignorées et pouvaient provoquer des problèmes d’exécution.

En bref, le nombre de signes deux-points doit correspondre au nombre de paramètres :

- Aucun paramètre :`foo`
- Un paramètre :`foo:`
- Deux paramètres :`foo:parameterName2:`

Les utilisations incorrectes sont les suivantes :

```csharp
// Invalid: export takes no arguments, but function expects one
[Export ("apply")]
void Apply (NSObject target);

// Invalid: exported as taking an argument, but the managed version does not have one:
[Export ("display:")]
void Display ();
```

### <a name="use-isvariadic-parameter-in-export"></a>Utiliser le paramètre IsVariadic dans l’exportation

Les fonctions variadiques doivent utiliser l' `IsVariadic` argument de l' `[Export]` attribut :

```csharp
[Export ("variadicMethod:", IsVariadic = true)]
void VariadicMethod (NSObject first, IntPtr subsequent);
```

### <a name="must-link-to-existing-symbols"></a>Doit être lié à des symboles existants

Il est impossible de lier des classes qui n’existent pas dans la bibliothèque native.
Si une classe a été supprimée ou renommée dans la bibliothèque native, veillez à mettre à jour les liaisons pour qu’elles correspondent.
