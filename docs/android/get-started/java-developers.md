---
title: Xamarin pour les développeurs Java
description: Si vous êtes un développeur Java, vous allez pouvoir tirer parti de vos compétences et de votre code existant sur la plateforme Xamarin tout en bénéficiant des avantages de réutilisation de code de C#. Vous constaterez que la syntaxe C# est très similaire à la syntaxe de Java, et que les deux langages fournissent des fonctionnalités similaires. En outre, vous allez découvrir des fonctionnalités propres à C# qui facilitent le développement.
ms.prod: xamarin
ms.assetid: A3B6C041-4052-4E7D-999C-C4FA10BE3D67
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/13/2018
ms.openlocfilehash: f4c8c8b19d7738478cdc2c8f83c6fc8d1f361466
ms.sourcegitcommit: cf56d2bae34dc0f8e94c2d3d28d5f460d59807bf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985658"
---
# <a name="xamarin-for-java-developers"></a>Xamarin pour les développeurs Java

_Si vous êtes un développeur Java, vous allez pouvoir tirer parti de vos compétences et de votre code existant sur la plateforme Xamarin tout en bénéficiant des avantages de réutilisation de code de C#. Vous constaterez que la syntaxe C# est très similaire à la syntaxe de Java, et que les deux langages fournissent des fonctionnalités similaires. Vous constaterez que la syntaxe C# est très similaire à la syntaxe de Java, et que les deux langages fournissent des fonctionnalités similaires._

## <a name="overview"></a>Présentation

Cet article constitue une introduction à la programmation C# pour les développeurs Java, qui met principalement l’accent sur les fonctionnalités du langage C# que vous rencontrerez en développant des applications Xamarin.Android. En outre, cet article explique en quoi ces fonctionnalités diffèrent de leurs équivalents Java et C#, et présente des fonctionnalités importantes (pertinentes pour Xamarin.Android) qui ne sont pas disponibles dans Java. Des liens vers des références supplémentaires sont inclus. Vous pouvez donc utiliser cet article comme point de départ pour approfondir vos connaissances sur C# et .NET.

Si vous êtes familiarisé avec Java, vous n’aurez aucune difficulté à aborder la syntaxe du langage C#. La syntaxe C# est très similaire à la syntaxe Java &ndash; C# est un langage à « accolades », comme Java, C et C++. À bien des égards, la syntaxe C# peut être vue comme un sur-ensemble de la syntaxe Java, mais avec quelques mots-clés renommés et ajoutés.

Vous trouverez de nombreuses caractéristiques clés de Java dans C# :

- Programmation orientée objet basée sur les classes

- Typage fort

- Prise en charge des interfaces

- Génériques

- Garbage collection

- Compilation du runtime

Java et C# sont compilés dans un langage intermédiaire qui est exécuté dans un environnement d’exécution géré. C# et Java sont typés de façon statique, et les deux langages traitent les chaînes en tant que types immuables.
Les deux langages utilisent une hiérarchie de classes à racine unique. Comme Java, C# prend uniquement en charge l’héritage unique et n’autorise pas les méthodes globales.
Dans les deux langages, les objets sont créés sur le tas en utilisant le mot-clé `new` et les objets sont récupérés par le garbage collector lorsqu’ils ne sont plus utilisés. Les deux langages fournissent la prise en charge des exceptions formelles avec une sémantique `try`/`catch`. Les deux prennent en charge la gestion et la synchronisation des threads.

Toutefois, il existe de nombreuses différences entre les Java et C#. Par exemple :

- Java ne prend pas en charge les variables locales implicitement typées (C# prend en charge le mot clé `var`).

- En Java, vous pouvez passer des paramètres uniquement par valeur, tandis qu’en C#, vous pouvez passer des références ou des valeurs. (C# fournit les mots-clés `ref` et `out` pour le passage de paramètres par référencent ; il n’existe pas d’équivalent en Java).

- Java ne prend pas en charge les directives de préprocesseur comme `#define`.

- Java ne prend pas en charge les types d’entiers non signés, tandis que C# fournit des types d’entiers non signés comme `ulong`, `uint`, `ushort` et `byte`.

- Java ne prend pas en charge la surcharge d’opérateur. En C#, vous pouvez surcharger les opérateurs et conversions.

- Dans une instruction `switch` Java, le code peut passer dans la section switch suivante, mais en C#, la fin de chaque section `switch` doit arrêter le switch (la fin de chaque section doit se fermer avec une instruction `break`).

- En Java, vous spécifiez les exceptions levées par une méthode avec le mot-clé `throws`, mais C# n’a aucun concept des exceptions vérifiées &ndash; le mot clé `throws` n’est pas pris en charge en C#.

- C# prend en charge Language-Integrated Query (LINQ), qui vous permet d’utiliser les mots réservés `from`, `select`, et `where` pour écrire des requêtes sur des collections d’une manière similaire aux requêtes de base de données.

Bien entendu, il existe de nombreuses différences supplémentaires entre C# et Java qui ne peuvent être traitées dans cet article. En outre, Java et C# continuent à évoluer (par exemple, Java 8, ce qui n’est pas encore dans la chaîne d’outils Android, prend en charge les expressions lambda de style C#), aussi ces différences évoluent au fil du temps. Les principales différences actuellement rencontrées par les développeurs Java qui découvrent Xamarin.Android sont décrites ici.

- [Passer du développement Java à C#](#fundamentals) présente les différences fondamentales entre C# et Java.

- [Fonctionnalités programmation orientée objet](#oopfeatures) présente les différences de programmation orientée objet les plus importantes entre les deux langages.

- [Différences de mots clés](#keywords) fournit un tableau des équivalents utile, les mots clés propres à C# et des liens vers les définitions des mots clés C#.

C# propose plusieurs fonctionnalités essentielles dans Xamarin.Android qui ne sont pas actuellement disponibles pour les développeurs Java sur Android. Ces fonctionnalités peuvent vous aider à mieux écrire votre code, en moins de temps :

- [Propriétés](#properties) &ndash; Avec le système de propriétés de C#, vous pouvez accéder à des variables de membre directement et en toute sécurité sans avoir à écrire des méthodes getter et setter.

- [Expressions lambda](#lambdas) &ndash; En C#, vous pouvez utiliser des méthodes anonymes (également appelées *expressions lambda*) pour exprimer vos fonctionnalités plus succinctement et plus efficacement. Vous pouvez éviter d’avoir à écrire des objets à usage unique, et passer l’état local à une méthode sans avoir à ajouter des paramètres.

- [Gestion des événements](#events) &ndash; C# fournit la prise en charge, au niveau du langage, de la *programmation pilotée par les événements*, qui permet d’enregistrer un objet pour qu’il reçoive des notifications quand un événement d’intérêt se produit. Le mot-clé `event` définit un mécanisme de diffusion multidiffusion, qu’une classe d’éditeur peut utiliser pour notifier les abonnés aux événements.

- [Programmation asynchrone](#async) &ndash; Les fonctionnalités de programmation asynchrones du langage C# (`async`/`await`) assurent la réactivité des applications.
    La prise en charge au niveau du langage de cette fonctionnalité rend la programmation asynchrone facile à implémenter et moins sujette aux erreurs.

Enfin, Xamarin vous permet de [tirer parti des ressources Java existantes](#interop) via une technologie appelée *liaison*. Vous pouvez appeler votre code, vos infrastructures et vos bibliothèques Java dans C# en utilisant les générateurs de liaison automatiques de Xamarin. Pour ce faire, vous créez simplement une bibliothèque statique dans Java et l’exposez à C# via une liaison.

<a name="fundamentals" />

## <a name="going-from-java-to-c-development"></a>Passer du développement Java à C#

Les sections suivantes décrivent les principales différences de prise en main entre les langages C# et Java ; une section ultérieure décrit les différences de programmation orientée objet entre ces langages.

### <a name="libraries-vs-assemblies"></a>Bibliothèques vs. Assemblys

Java empaquète généralement les classes connexes dans des fichiers **.jar**. En C# et en .NET, toutefois, les parties réutilisables de code précompilé sont empaquetées dans des *assemblys*, qui sont généralement fournis sous forme de fichiers *.dll*. Un assembly est une unité de déploiement pour le code C#/.NET, et chaque assembly est généralement associé à un projet C#. Les assemblys contiennent le code intermédiaire (IL) qui est compilé juste-à-temps lors de l’exécution.

Pour plus d’informations sur les assemblys, consultez la rubrique [Assemblys et le Global Assembly Cache](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/assemblies-gac/).

### <a name="packages-vs-namespaces"></a>Packages vs. Espaces de noms

C# utilise le mot-clé `namespace` pour grouper les types associés ensemble ; Cela s’apparente au mot-clé `package` de Java. En règle générale, une application Xamarin.Android réside dans un espace de noms créé pour cette application. Par exemple, le code C# suivant déclare le wrapper d’espace de noms `WeatherApp` pour une application météorologique :

```csharp
namespace WeatherApp
{
    ...
```

### <a name="importing-types"></a>Importation de types

Lorsque vous utilisez des types définis dans des espaces de noms externes, vous importez ces types avec une instruction `using` (qui est très similaire à l’instruction `import` de Java). En Java, vous pouvez importer un seul type avec une instruction comme suit :

```java
import javax.swing.JButton
```

Vous pouvez importer un package Java entier avec une instruction comme celle-ci :

```java
import javax.swing.*
```

L’instruction `using` de C# fonctionne de façon très similaire, mais elle vous permet d’importer un package entier sans spécifier de caractère générique. Par exemple, vous verrez souvent une série d’instructions `using` au début des fichiers source Xamarin.Android, comme illustré dans cet exemple :

```csharp
using System;
using Android.App;
using Android.Content;
using Android.Runtime;
using Android.Views;
using Android.Widget;
using Android.OS;
using System.Net;
using System.IO;
using System.Json;
using System.Threading.Tasks;
```

Ces instructions importent les fonctionnalités des espaces de noms `System`, `Android.App`, `Android.Content`, etc.

### <a name="generics"></a>Génériques

Java et C# prennent en charge les *génériques*, qui sont des espaces réservés qui vous permettent de connecter différents types au moment de la compilation. Toutefois, les génériques fonctionnent différemment en C#. En Java, [l’effacement de type](https://docs.oracle.com/javase/tutorial/java/generics/erasure.html) rend les informations de type disponibles uniquement au moment de la compilation, mais pas au moment de l’exécution. En revanche, le common language runtime (CLR) .NET fournit une prise en charge explicite des types génériques, ce qui signifie que C# a accès aux informations de type lors de l’exécution. Pour le développement Xamarin.Android au quotidien, l’importance de cette distinction n’est pas souvent évidente, mais si vous utilisez la [réflexion](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/reflection), cette fonctionnalité est cruciale pour accéder aux informations de type au moment de l’exécution.

Dans Xamarin.Android, vous verrez souvent la méthode générique `FindViewById` utilisée pour obtenir une référence à un contrôle de disposition. Cette méthode accepte un paramètre de type générique qui spécifie le type de contrôle à rechercher. Par exemple :

```csharp
TextView label = FindViewById<TextView> (Resource.Id.Label);
```

Dans cet exemple de code, `FindViewById` obtient une référence au contrôle `TextView` qui est défini dans la disposition en tant que **Label**, puis la renvoie en tant que type `TextView`.

Pour plus d’informations sur les génériques, consultez la rubrique [Génériques](https://docs.microsoft.com/dotnet/csharp/programming-guide/generics/index).
Notez qu’il existe certaines limitations de prise en charge des classes génériques C# par Xamarin.Android ; Pour plus d’informations, consultez [Limitations](~/android/internals/limitations.md).

<a name="oopfeatures" />

## <a name="object-oriented-programming-features"></a>Fonctionnalités de programmation orientée objet

Java et C# utilisent des idiomes de programmation orientée objet très similaires :

- Toutes les classes sont au final dérivées d’un objet racine unique &ndash; Tous les objets Java dérivent de `java.lang.Object`, tandis que tous les objets C# dérivent de `System.Object`.

- Les instances de classe sont des types référence.

- Lorsque vous accédez à des propriétés et méthodes d’une instance, vous utilisez l’opérateur « `.` ».

- Toutes les instances de classe sont créées sur le tas via l’opérateur `new`.

- Étant donné que les deux langages utilisent le garbage collection, il n’existe aucun moyen de libérer explicitement les objets non utilisés (il n’y a pas de mot clé `delete` comme en C++).

- Vous pouvez étendre les classes par héritage, et les deux langages autorisent une seule classe de base par type.

- Vous pouvez définir des interfaces, et une classe peut hériter (autrement dit, implémenter) plusieurs définitions d’interface.

Toutefois, il y a des différences importantes :

- Java a deux fonctionnalités puissantes que C# ne prend pas en charge : les classes anonymes et les classes internes. (Toutefois, C# autorise l’imbrication de définitions de classe &ndash; Les classes imbriquées de C# sont semblables aux classes imbriquées statiques de Java.)

- C# prend en charge les types de structure de style C (`struct`), ce que Java ne fait pas.

- En C#, vous pouvez implémenter une définition de classe dans des fichiers sources distincts à l’aide du mot clé `partial`.

- Les interfaces C# ne peuvent pas déclarer de types.

- C# utilise une syntaxe de destructeur de style C++ pour exprimer les finaliseurs. La syntaxe est différente de la méthode `finalize` de Java, mais la sémantique est quasiment identique. (Notez qu’en C#, les destructeurs appellent automatiquement le destructeur de classe de base &ndash; contrairement à Java où un appel explicite à `super.finalize` est utilisé.)

### <a name="class-inheritance"></a>Héritage de classe

Pour étendre une classe Java, vous utilisez le mot-clé `extends`. Pour étendre une classe en C#, vous utilisez un signe deux-points (`:`) pour indiquer la dérivation. Par exemple, dans les applications Xamarin.Android, vous verrez souvent des dérivations de classe qui ressemblent au fragment de code suivant :

```csharp
public class MainActivity : Activity
{
    ...
```

Dans cet exemple, `MainActivity` hérite de la classe `Activity`.

Pour déclarer la prise en charge pour une interface en Java, vous utilisez le mot-clé `implements`. Toutefois, en C#, vous ajoutez simplement les noms d’interface à la liste des classes à partir desquelles hériter, comme indiqué dans ce fragment de code :

```csharp
public class SensorsActivity : Activity, ISensorEventListener
{
    ...
```

Dans cet exemple, `SensorsActivity` hérite de `Activity` et implémente les fonctionnalités déclarées dans l’interface `ISensorEventListener`. Notez que la liste des interfaces doit se trouver après la classe de base (ou vous aurez une erreur de compilation). Par convention, les noms d’interface C# se finissent par une majuscule « I » ; Il est ainsi possible de déterminer les classes qui sont des interfaces sans nécessiter de mot-clé `implements`.

Lorsque vous souhaitez empêcher une classe d’être sous-classée davantage en C#, vous faites précéder le nom de classe avec `sealed` &ndash; en Java, vous faites précéder le nom de classe avec `final`.

Pour plus d’informations sur les définitions de classe C#, consultez les rubriques [Classes](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/classes) et [Héritage](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/inheritance).

<a name="properties" />

### <a name="properties"></a>Properties

En Java, les méthodes de mutateurs (setters) et d’inspecteurs (getters) sont souvent utilisées pour contrôler la façon dont les modifications sont apportées aux membres de classe tout en masquant et protégeant ces membres à partir de l’extérieur du code. Par exemple, la classe `TextView` d’Android fournit les méthodes `getText` et `setText`. C# fournit un mécanisme similaire, mais plus direct appelé *propriétés*.
Les utilisateurs d’une classe C# peuvent accéder à une propriété de la même façon qu’ils accèdent à un champ, mais chaque accès entraîne en fait un appel de méthode qui est transparent pour l’appelant. Cette méthode « en coulisses » peut implémenter des effets secondaires, tels que la définition d’autres valeurs, la réalisation de conversions ou la modification d’état des objets.

Les propriétés sont souvent utilisées pour l’accès et modification des membres de l’objet d’interface utilisateur. Par exemple :

```csharp
int width = rulerView.MeasuredWidth;
int height = rulerView.MeasuredHeight;
...
rulerView.DrawingCacheEnabled = true;
```

Dans cet exemple, les valeurs de largeur et de hauteur sont lues à partir de l’objet `rulerView` en accédant à ses propriétés `MeasuredWidth` et `MeasuredHeight`. Lorsque ces propriétés sont lues, les valeurs de leurs champs associés (mais masqués) sont extraites en arrière-plan et retournées à l’appelant. L’objet `rulerView` peut stocker des valeurs de largeur et de hauteur dans une unité de mesure (par exemple, en pixels) et convertir ces valeurs à la volée en une autre unité de mesure (par exemple, en millimètres) lorsque les propriétés `MeasuredWidth` et `MeasuredHeight` sont accessibles.

L’objet `rulerView` possède également une propriété nommée `DrawingCacheEnabled` &ndash; l’exemple de code définit cette propriété sur `true` pour activer le cache de dessin dans `rulerView`. En arrière-plan, un champ masqué associé est mis à jour avec la nouvelle valeur et d’autres aspects de l’état `rulerView` sont éventuellement modifiés. Par exemple, lorsque `DrawingCacheEnabled` est défini sur `false`, `rulerView` peut également supprimer toutes les informations de cache de dessin déjà accumulées dans l’objet.

L’accès aux propriétés peut être en lecture/écriture, en lecture seule ou en écriture seule. En outre, vous pouvez utiliser des modificateurs d’accès différents pour lire et écrire. Par exemple, vous pouvez définir une propriété qui a un accès en lecture public mais un accès en écriture privé.

Pour plus d’informations sur les propriétés C#, consultez la rubrique [Propriétés](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/properties).

### <a name="calling-base-class-methods"></a>Appel de méthodes de classe de base

Pour appeler un constructeur de classe de base en C#, vous utilisez un signe deux-points (`:`) suivi du mot-clé `base` et d’une liste d’initialiseurs ; cet appel de constructeur `base` est placé immédiatement après la liste de paramètres du constructeur dérivé. Le constructeur de classe de base est appelé sur entrée pour le constructeur dérivé ; le compilateur insère l’appel au constructeur de base au début du corps de la méthode. Le fragment de code suivant illustre un constructeur de base appelé à partir d’un constructeur dérivé dans une application Xamarin.Android :

```csharp
public class PictureLayout : ViewGroup
{
    ...
    public PictureLayout (Context context)
           : base (context)
    {
        ...
    }
    ...
}

```

Dans cet exemple, la classe `PictureLayout` est dérivée de `ViewGroup`. Le constructeur `PictureLayout` illustré dans cet exemple de constructeur accepte un argument `context` et le passe au constructeur `ViewGroup` via l’appel `base(context)`.

Pour appeler une méthode de classe de base en C#, utilisez le mot-clé `base`. Par exemple, les applications Xamarin.Android appellent souvent des méthodes de base, comme illustré ici :

```csharp
public class MainActivity : Activity
{
    ...
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);
```

Dans ce cas, la méthode `OnCreate` définie par la classe dérivée (`MainActivity`) appelle la méthode `OnCreate` de la classe de base (`Activity`).

### <a name="access-modifiers"></a>Modificateurs d’accès

Java et C# prennent en charge les modificateurs d’accès `public`, `private` et `protected`. Toutefois, C# prend en charge deux modificateurs d’accès supplémentaires :

- **`internal`** &ndash; Le membre de classe est uniquement accessible dans l’assembly actuel.

- **`protected internal`** &ndash; Le membre de classe est accessible dans l’assembly de définition, la classe de définition et les classes dérivées (les classes dérivées aussi bien à l’intérieur qu’à l’extérieur de l’assembly ont accès).

Pour plus d’informations sur les modificateurs d’accès C#, consultez la rubrique [Modificateurs d’accès](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/access-modifiers).

### <a name="virtual-and-override-methods"></a>Méthodes virtuelles et de substitution

Java et C# prennent en charge *le polymorphisme*, la possibilité de traiter les objets connexes de la même manière. Dans les deux langages, vous pouvez utiliser une référence de classe de base pour faire référence à un objet de classe dérivée, et les méthodes d’une classe dérivée peuvent substituer les méthodes de ses classes de base. Les deux langages ont le concept de méthode *virtuelle*, une méthode dans une classe de base qui est conçue pour être remplacée par une méthode dans une classe dérivée.
Comme Java, C# prend en charge les classes et méthodes `abstract`.

Toutefois, il existe certaines différences entre Java et C# dans la façon de déclarer des méthodes virtuelles et les remplacer :

- Par défaut, les méthodes ne sont pas virtuelles. Les classes parentes doivent libeller explicitement les méthodes à remplacer en utilisant le mot-clé `virtual`. En revanche, toutes les méthodes dans Java sont des méthodes virtuelles par défaut.

- Pour empêcher une méthode d’être substituée en C#, vous laissez simplement le mot-clé `virtual`. Par contraste, Java utilise le mot-clé `final` pour marquer une méthode avec l’instruction « le remplacement n’est pas autorisé. »

- Les classes dérivées C# doivent utiliser le mot clé `override` pour indiquer explicitement qu’une méthode de classe de base virtuelle est substituée.

Pour plus d’informations sur la prise en charge du polymorphisme par C#, consultez la rubrique [Polymorphisme](https://docs.microsoft.com/dotnet/csharp/programming-guide/classes-and-structs/polymorphism).

<a name="lambdas" />

## <a name="lambda-expressions"></a>Expressions lambda

C# permet de créer des *fermetures*: des méthodes anonymes intégrées qui peuvent accéder à l’état de la méthode dans laquelle elles sont placées.
Avec les expressions lambda, vous pouvez écrire moins de lignes de code pour implémenter la même fonctionnalité que vous auriez implémentée dans Java avec nombreuses lignes de code supplémentaires.

Les expressions lambda permettent d’ignorer les étapes supplémentaires impliquées dans la création d’une classe à usage unique ou anonyme comme vous le feriez dans Java &ndash; au lieu de cela, vous pouvez écrire uniquement la logique métier de votre code de méthode de façon intégrée. En outre, étant donné que les expressions lambda ont accès aux variables dans la méthode environnante, il est inutile de créer une longue liste de paramètres pour passer l’état au code de votre méthode.

En C#, les expressions lambda sont créées avec l’opérateur `=>`, comme indiqué ici :

```csharp
(arg1, arg2, ...) => {
    // implementation code
};
```

Dans Xamarin.Android, les expressions lambda sont souvent utilisées pour définir des gestionnaires d’événements. Par exemple :

```csharp
button.Click += (sender, args) => {
    clickCount += 1;    // access variable in surrounding code
    button.Text = string.Format ("Clicked {0} times.", clickCount);
};
```

Dans cet exemple, le code d’expression lambda (le code entre les accolades) incrémente un nombre de clics et met à jour le texte `button` pour afficher le nombre de clics. Cette expression lambda est inscrite avec l’objet `button` comme un gestionnaire d’événements à appeler lorsque l’utilisateur clique sur le bouton. (Ces fichiers sont expliqués de façon plus détaillée ci-dessous.) Dans cet exemple simple, les paramètres `sender` et `args` ne sont pas utilisés par le code d’expression lambda, mais ils sont requis dans l’expression lambda pour satisfaire les exigences de signature de méthode d’inscription d’événement. Dans la pratique, le compilateur C# convertit l’expression lambda en une méthode anonyme qui est appelée chaque fois que des événements de clic de bouton se produisent.

Pour plus d’informations sur C# et les expressions lambda, consultez la rubrique [Expressions lambda](https://docs.microsoft.com/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).

<a name="events" />

## <a name="event-handling"></a>Gestion des événements

Un *événement* est le moyen pour un objet de notifier les abonnés inscrits lorsque quelque chose d’intéressant se produit sur cet objet. Contrairement à Java, dans lequel un abonné implémente généralement une interface `Listener` qui contient une méthode de rappel, C# fournit la prise en charge au niveau du langage pour la gestion des événements via des *délégués*. Un *délégué* est similaire à un pointeur de fonction de type sécurisé et orienté objet &ndash; il encapsule une référence d’objet et un jeton de méthode. Si un objet client veut s’abonner à un événement, il crée un délégué et passe le délégué à l’objet de notification.
Lorsque l’événement se produit, l’objet de notification appelle la méthode représentée par l’objet délégué, informant ainsi l’objet de client abonnement de l’événement. En C#, les gestionnaires d'événements sont surtout de simples méthodes appelées par le biais de délégués.

Pour plus d’informations sur les délégués, consultez la rubrique [Délégués](https://docs.microsoft.com/dotnet/csharp/programming-guide/delegates/index).

En C#, les événements sont *multidiffusion* ; Autrement dit, plusieurs écouteurs peuvent être avertis lorsqu’un événement se produit. Cette différence est notable lorsque vous envisagez les différences syntaxiques entre les inscriptions aux événements en Java et en C#. En Java, vous appelez `SetXXXListener` pour enregistrer les notifications d’événements ; En C# vous utilisez l’opérateur `+=` pour inscrire pour les notifications d’événement en « ajoutant » votre délégué à la liste des écouteurs d’événements.
En Java, vous appelez `SetXXXListener` pour annuler l’inscription, tandis qu’en C# vous utilisez `-=` pour soustraire votre délégué de la liste des écouteurs.

Dans Xamarin.Android, les événements sont souvent utilisés pour informer les objets lorsqu’un utilisateur fait quelque chose sur un contrôle d’interface utilisateur. En règle générale, un contrôle d’interface utilisateur aura des membres qui sont définis à l’aide du mot-clé `event`. Vous attachez vos délégués à ces membres pour vous abonner aux événements de contrôle d’interface utilisateur.

Pour vous abonner à un événement :

1. Créez un objet de délégué qui fait référence à la méthode à appeler lorsque l’événement se produit.

2. Utilisez l’opérateur `+=` pour attacher votre délégué à l’événement auquel vous vous abonnez.

L’exemple suivant définit un délégué (avec l’utilisation explicite du mot-clé `delegate`) pour vous abonner aux clics de bouton.
Ce gestionnaire de clics de bouton démarre une nouvelle activité :

```csharp
startActivityButton.Click += delegate {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

Toutefois, vous pouvez également utiliser une expression lambda pour enregistrer les événements, et ignorer le mot-clé `delegate` entièrement. Par exemple :

```csharp
startActivityButton.Click += (sender, e) => {
    Intent intent = new Intent (this, typeof (MyActivity));
    StartActivity (intent);
};

```

Dans cet exemple, l’objet `startActivityButton` dispose d’un événement qui attend un délégué avec une certaine signature de méthode : une qui prend les arguments de l’expéditeur et de l’événement et retourne void. Toutefois, étant donné que nous ne souhaitez pas vous embêter à définir explicitement un délégué de ce type ou sa méthode, nous déclarons la signature de la méthode avec `(sender, e)` et utilisons une expression lambda pour implémenter le corps du gestionnaire d’événements.
Notez que nous devons déclarer cette liste de paramètres, même si nous n’utilisons pas les paramètres `sender` et `e`.

Il est important de vous rappeler que vous pouvez vous désabonner à un délégué (via l’opérateur `-=`), mais vous ne pouvez pas vous désabonner d’une expression lambda &ndash; tenter de le faire peut provoquer des fuites de mémoire. Utilisez la forme lambda de l’inscription d’événement uniquement lorsque votre gestionnaire n’annule pas l’abonnement depuis l’événement.

En règle générale, les expressions lambda sont utilisées pour déclarer les gestionnaires d’événements dans le code Xamarin.Android. Ce moyen plus rapide de déclarer les gestionnaires d’événements peut sembler difficile à déchiffrer au départ, mais il vous fait gagner énormément de temps lors de l’écriture et de la lecture du code. Avec le temps, vous vous habituerez à reconnaître ce modèle (qui revient fréquemment dans le code Xamarin.Android), et vous consacrerez plus de temps à réfléchir à la logique métier de votre application et moins de temps à gérer une syntaxe surchargée.

<a name="async" />

## <a name="asynchronous-programming"></a>Programmation asynchrone

La *Programmation asynchrone* est un moyen d’améliorer la réactivité de votre application. Les fonctionnalités de programmation asynchrone permettent au reste du code de votre application de continuer à s’exécuter pendant que certaines parties de votre application sont bloquées par une opération de longue durée.
L’accès web, le traitement des images et la lecture/écriture de fichiers sont des exemples d’opérations qui peuvent provoquer le blocage d’une application entière si elle n’est pas écrite de façon asynchrone.

C# inclut la prise en charge, au niveau du langage, de la programmation asynchrone via les mots-clés `async` et `await`. Ces fonctionnalités de langage facilitent grandement l’écriture de code qui effectue des tâches longues sans bloquer le thread principal de votre application. En résumé, vous utilisez le mot-clé `async` dans une méthode pour indiquer que le code dans la méthode doit être exécuté de façon asynchrone et ne pas bloquer pas le thread de l’appelant. Vous utilisez le mot-clé `await` lorsque vous appelez des méthodes qui sont marquées avec `async`. Le compilateur interprète `await` comme le moment où l’exécution de votre méthode doit être déplacée vers un thread d’arrière-plan (une tâche est retournée à l’appelant). Lorsque cette tâche est terminée, l’exécution du code se poursuit sur le thread de l’appelant au niveau du point `await` dans votre code, en retournant les résultats de l’appel `async`. Par convention, les méthodes qui s’exécutent de façon asynchrone ont le suffixe `Async` joint à leur nom.

Dans les applications Xamarin.Android, `async` et `await` sont généralement utilisés pour libérer le thread de l’interface utilisateur afin qu’il puisse répondre aux entrées de l’utilisateur (par exemple s’il appuie sur un bouton **Annuler**) pendant une opération longue en arrière-plan.

Dans l’exemple suivant, un gestionnaire d’événements pour les clics de bouton provoque une opération asynchrone pour télécharger une image à partir du web :

```csharp
downloadButton.Click += downloadAsync;
...
async void downloadAsync(object sender, System.EventArgs e)
{
    webClient = new WebClient ();
    var url = new Uri ("http://photojournal.jpl.nasa.gov/jpeg/PIA15416.jpg");
    byte[] bytes = null;

    bytes = await webClient.DownloadDataTaskAsync(url);

    // display the downloaded image ...
```

Dans cet exemple, lorsque l’utilisateur clique sur le contrôle `downloadButton`, le `downloadAsync`Gestionnaire d’événements crée un objet `WebClient` et un objet `Uri` pour extraire une image à partir de l’URL spécifiée. Ensuite, il appelle la méthode `DownloadDataTaskAsync` de l’objet `WebClient` avec cette URL pour extraire l’image.

Notez que la déclaration de méthode de `downloadAsync` est précédée du mot-clé `async` pour indiquer qu’elle sera exécutée de façon asynchrone et retournera une tâche. Notez également que l’appel à `DownloadDataTaskAsync` est précédé du mot-clé `await`. L’application déplace l’exécution du gestionnaire d’événements (en commençant par le point où `await` apparaît) dans un thread d’arrière-plan jusqu'à ce que `DownloadDataTaskAsync` se termine et retourne.
Pendant ce temps, le thread d’interface utilisateur de l’application peut toujours répondre aux entrées de l’utilisateur et déclencher des gestionnaires d’événements pour les autres contrôles. Lorsque `DownloadDataTaskAsync` se termine (ce qui peut prendre plusieurs secondes), l’exécution reprend où la variable `bytes` est définie sur le résultat de l’appel à `DownloadDataTaskAsync`, et le reste du code du gestionnaire d’événements affiche l’image téléchargée sur le thread de l’appelant (interface utilisateur).

Pour une introduction à `async`/`await` en C#, consultez la rubrique [Programmation asynchrone avec Async et Await](https://docs.microsoft.com/dotnet/csharp/async).
Pour plus d’informations sur la prise en charge des fonctionnalités de programmation asynchrones dans Xamarin, consultez [Présentation de la prise en charge asynchrone](~/cross-platform/platform/async.md).

<a name="keywords" />

## <a name="keyword-differences"></a>Différences de mots-clés

Plusieurs mots-clés de langage utilisés en Java sont également utilisés en C#. Il existe également des mots-clés Java qui ont un équivalent différemment nommé en C#, comme indiqué dans ce tableau :

|Java|C#|Description|
|---|---|---|
|`boolean`|[bool](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/bool)|Utilisé pour déclarer les valeurs booléennes true et false.|
|`extends`|`:`|Précède la classe et les interfaces héritées.|
|`implements`|`:`|Précède la classe et les interfaces héritées.|
|`import`|[using](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/using)|Importe les types à partir d’un espace de noms, également utilisé pour la création d’un alias d’espace de noms.|
|`final`|[sealed](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/sealed)|Empêche la dérivation de classe ; empêche les méthodes et propriétés d’être substituées dans les classes dérivées.|
|`instanceof`|[is](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/is)|Détermine si un objet est compatible avec un type donné.|
|`native`|[extern](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/extern)|Déclare une méthode qui est implémentée en externe.|
|`package`|[namespace](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/namespace)|Déclare une portée pour un ensemble d’objets liés.|
|`T...`|[params T](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/params)|Spécifie un paramètre de méthode qui accepte un nombre variable d’arguments.|
|`super`|[base](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/base)|Sert à accéder aux membres de la classe parent à partir d’une classe dérivée.|
|`synchronized`|[lock](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/lock-statement)|Encapsule une section critique du code avec acquisition et libération de verrou.|

Il existe également plusieurs mots-clés qui sont propres à C# et n’ont aucun équivalent en Java. Le code Xamarin.Android utilise souvent les mots-clés C# suivants (cette table est utile comme référence lors de la lecture de [l’exemple de code](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Android) Xamarin.Android) :

|C#|Description|
|---|---|
|[as](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/as)|Effectue des conversions entre des types référence compatibles ou des types nullable.|
|[async](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async)|Spécifie qu’une méthode ou une expression lambda est asynchrone.|
|[await](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/await)|Suspend l’exécution d’une méthode jusqu'à ce qu’une tâche se termine.|
|[byte](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/byte)|Type entier 8 bits non signé.|
|[delegate](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/delegate)|Utilisé pour encapsuler une méthode ou une méthode anonyme.|
|[enum](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/enum)|Déclare une énumération, un ensemble de constantes nommées.|
|[event](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/event)|Déclare un événement dans une classe d’éditeur.|
|[fixed](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/fixed-statement)|Empêche une variable d’être réadressée.|
|`get`|Définit une méthode d’accesseur qui récupère la valeur d’une propriété.|
|[in](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/in-generic-modifier)|Permet à un paramètre d’accepter un type moins dérivé dans une interface générique.|
|[object](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/object)|Alias du type Object de .NET Framework.|
|[out](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/out)|Déclaration de modificateur de paramètre ou de paramètre de type générique.|
|[override](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/override)|Étend ou modifie l’implémentation d’un membre hérité.|
|[partial](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/partial-method)|Déclare une définition qui doit être fractionnée en plusieurs fichiers ou fractionne une définition de méthode à partir de son implémentation.|
|[readonly](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/readonly)|Déclare qu’un membre de classe ne peut être assigné au moment de la déclaration ou par le constructeur de classe.|
|[ref](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/ref)|Entraîne le passage d'un argument par référence, et non par valeur.|
|[set](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/set)|Définit une méthode d’accesseur qui définit la valeur d’une propriété.|
|[string](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/string)|Alias du type String de .NET Framework.|
|[struct](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/struct)|Un type valeur qui encapsule un groupe de variables liées.|
|[typeof](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/typeof)|Obtient le type d’un objet.|
|[var](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/var)|Définit une variable locale implicitement typée.|
|[value](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/value)|Fait référence à la valeur que le code client souhaite affecter à une propriété.|
|[virtual](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/virtual)|Autorise la substitution d'une méthode dans une classe dérivée.|

<a name="interop" />

## <a name="interoperating-with-existing-java-code"></a>Interopération avec du code Java existant

Si vous disposez de fonctionnalités Java existantes que vous ne souhaitez pas convertir en C#, vous pouvez réutiliser vos bibliothèques Java existantes dans les applications Xamarin.Android via deux techniques :

- **Créer une bibliothèque de liaison Java** &ndash; Pour cette approche, vous utilisez des outils Xamarin pour générer des wrappers C# autour de types Java. Ces wrappers sont appelés *liaisons*. Par conséquent, votre application Xamarin.Android peut utiliser votre fichier *.jar* en appelant ces wrappers.

- **Java Native Interface** &ndash;*Java Native Interface* (JNI) est un framework qui permet aux applications C# d’appeler ou d’être appelées par du code Java.

Pour plus d’informations sur ces techniques, consultez [Présentation de l’intégration Java](~/android/platform/java-integration/index.md).

## <a name="for-further-reading"></a>Pour obtenir des informations supplémentaires

Le [Guide de programmation C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/) de MSDN est un excellent moyen de commencer à apprendre le langage de programmation C#, et vous pouvez utiliser la [Référence c#](https://docs.microsoft.com/dotnet/csharp/language-reference/) pour rechercher des fonctionnalités spécifiques du langage C#.

De la même façon que les connaissances de Java impliquent autant la familiarisation avec les bibliothèques de classes Java que le fait de connaître le langage Java, une connaissance pratique de C# nécessite d’être familiarisé avec le .NET framework. Le paquet d’apprentissage [Passage à C# et .NET Framework pour les développeurs Java](https://www.microsoft.com/download/details.aspx?id=6073) de Microsoft est une bonne solution pour en savoir plus sur .NET Framework à partir d’une perspective Java (tout en obtenant une meilleure compréhension du langage C#).

Lorsque vous êtes prêt à gérer votre premier projet Xamarin.Android en C#, notre série [Hello, Android](~/android/get-started/hello-android/index.md) peut vous aider à créer votre première application Xamarin.Android, et approfondir votre compréhension des principes fondamentaux du développement d’applications Android avec Xamarin.

## <a name="summary"></a>Récapitulatif

Cet article propose une introduction à l’environnement de programmation C# Xamarin.Android du point de vue du développeur Java. Il souligne les similitudes entre C# et Java tout en expliquant leurs différences pratiques. Il présente les assemblys et les espaces de noms, explique comment importer les types externes et fournit une vue d’ensemble des différences en matière de modificateurs d’accès, génériques, dérivation de classe, appels aux méthodes de classe de base, substitutions de méthodes et gestion des événements. Il présente les fonctionnalités C# qui ne sont pas disponibles dans Java, dont les propriétés, la programmation asynchrone `async`/`await`, les expressions lambda, les délégués C# et le système de gestion des événements C#. Il comprend des tables des mots-clés C# importants, explique comment interagir avec les bibliothèques Java existantes et fournit des liens vers la documentation associée pour approfondir le sujet.

## <a name="related-links"></a>Liens associés

- [Présentation de l’intégration Java](~/android/platform/java-integration/index.md)
- [Guide de programmation C#](https://docs.microsoft.com/dotnet/csharp/programming-guide/)
- [Référence C#](https://docs.microsoft.com/dotnet/csharp/language-reference/index)
- [Passage à C# et .NET Framework pour les développeurs Java](https://www.microsoft.com/download/details.aspx?id=6073)
