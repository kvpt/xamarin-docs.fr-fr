---
title: C#6 nouvelles fonctionnalités vue d’ensemble
description: La version 6 de la C# langage continue d’évoluer de la langue pour avoir moins de code réutilisable, améliorer la clarté et la cohérence plus. Syntaxe d’initialisation plus propre, la possibilité d’utiliser await dans les blocs catch/finally et l’instruction conditionnelle la valeur null ? opérateur sont particulièrement utiles.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 0fd7f4e9972f7bf762a44c9edf30fa13f9d989bc
ms.sourcegitcommit: 0845ed2daa65468b6fe12ac4e9386f3315d72f4d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850951"
---
# <a name="c-6-new-features-overview"></a>C#6 nouvelles fonctionnalités vue d’ensemble

_La version 6 de la C# langage continue d’évoluer de la langue pour avoir moins de code réutilisable, améliorer la clarté et la cohérence plus. Syntaxe d’initialisation plus propre, la possibilité d’utiliser await dans les blocs catch/finally et l’instruction conditionnelle la valeur null ? opérateur sont particulièrement utiles._

> [!NOTE]
> Pour plus d’informations sur la dernière version de la C# langue – version 7 – consultez l’article [Nouveautés C# 7.0](/dotnet/csharp/whats-new/csharp-7)

Ce document présente les nouvelles fonctionnalités de C# 6. Il est entièrement pris en charge par le compilateur mono et les développeurs peuvent démarrer à l’aide des nouvelles fonctionnalités sur toutes les plateformes cibles de Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Quelles sont les nouveautés C# 6 vidéo**

## <a name="using-c-6"></a>À l’aide de C# 6

Le C# compilateur 6 est utilisé dans toutes les versions récentes de Visual Studio pour Mac.
Si vous utilisez des compilateurs de ligne de commande doivent confirmer que `mcs --version` retourne 4.0 ou version ultérieure.
Visual Studio pour les utilisateurs Mac permettre vérifier s’ils ont Mono 4 (ou version ultérieure) installé en vous reportant aux **sur Visual Studio pour Mac > Visual Studio pour Mac > Afficher les détails**.

## <a name="less-boilerplate"></a>Moins réutilisable
### <a name="using-static"></a>using static
Énumérations et certaines classes comme `System.Math`, sont principalement les titulaires de fonctions et des valeurs statiques. Dans C# 6, vous pouvez importer tous les membres statiques d’un type avec un seul `using static` instruction. Comparer une fonction trigonométriques typique dans C# 5 et C# 6 :

```csharp
// Classic C#
class MyClass
{
    public static Tuple<double,double> SolarAngleOld(double latitude, double declination, double hourAngle)
    {
        var tmp = Math.Sin (latitude) * Math.Sin (declination) + Math.Cos (latitude) * Math.Cos (declination) * Math.Cos (hourAngle);
        return Tuple.Create (Math.Asin (tmp), Math.Acos (tmp));
    }
}

// C# 6
using static System.Math;

class MyClass
{
    public static Tuple<double, double> SolarAngleNew(double latitude, double declination, double hourAngle)
    {
        var tmp = Asin (latitude) * Sin (declination) + Cos (latitude) * Cos (declination) * Cos (hourAngle);
        return Tuple.Create (Asin (tmp), Acos (tmp));
    }
}
```

`using static` ne pas rendre public `const` les champs, tels que `Math.PI` et `Math.E`, directement accessibles :

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>à l’aide de statiques avec les méthodes d’Extension

Le `using static` installation fonctionne un peu différemment avec les méthodes d’extension. Bien que les méthodes d’extension sont écrits à l’aide de `static`, ils incompréhensible sans une instance à utiliser. Par conséquent, lorsque `using static` est utilisé avec un type qui définit les méthodes d’extension, les méthodes d’extension sont disponibles sur leur type de cible (la méthode `this` type). Par exemple, `using static System.Linq.Enumerable` peuvent être utilisées pour étendre l’API de `IEnumerable<T>` objets sans ralentir votre dans tous les types LINQ :

```csharp
using static System.Linq.Enumerable;
using static System.String;

class Program
{
    static void Main()
    {
        var values = new int[] { 1, 2, 3, 4 };
        var evenValues = values.Where (i => i % 2 == 0);
        System.Console.WriteLine (Join(",", evenValues));
    }
}
```

L’exemple précédent montre la différence de comportement : la méthode d’extension `Enumerable.Where` est associé avec le tableau, lors de la méthode statique `String.Join` peut être appelée sans référence à la `String` type.

### <a name="nameof-expressions"></a>Expressions nameof
Parfois, vous souhaitez faire référence au nom que vous avez fournies une variable ou un champ. Dans C# 6, `nameof(someVariableOrFieldOrType)` retourne la chaîne `"someVariableOrFieldOrType"`. Par exemple, lorsque vous levez une `ArgumentException` vous verrez très probablement à nommer l’argument qui n’est pas valide :

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

L’avantage principal de `nameof` expressions est qu’ils sont la vérification de type et sont compatibles avec alimenté par l’outil de refactorisation. La vérification de type de `nameof` expressions est particulièrement Bienvenue dans les situations où un `string` est utilisé pour associer des types de manière dynamique. Par exemple, dans iOS un `string` est utilisé pour spécifier le type utilisé pour le prototype `UITableViewCell` des objets dans un `UITableView`. `nameof` vous assurer que cette association n’échoue pas en raison d’une faute d’orthographe ou de la refactorisation mal écrit :

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Bien que vous pouvez transmettre un nom qualifié à `nameof`, uniquement le dernier élément (après le dernier `.`) est retournée. Par exemple, vous pouvez ajouter une liaison de données dans Xamarin.Forms :

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Les deux appels à `SetBinding` transmettez des valeurs identiques : `nameof(ReactiveType.StringField)` est `"StringField"`, et non pas `"ReactiveType.StringField"` comme prévu initialement.

## <a name="null-conditional-operator"></a>Opérateur conditionnel null
Précédemment mises à jour à C# a introduit les concepts de types nullable et l’opérateur de fusion null `??` pour réduire la quantité de code réutilisable lors de la gestion des valeurs NULL. C#6 est toujours ce thème avec « opérateur de condition null » `?.`. Lorsqu’il est utilisé sur un objet dans la partie droite d’une expression, l’opérateur conditionnel null retourne la valeur du membre si l’objet n’est pas `null` et `null` sinon :

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Les deux `length0` et `length1` sont déduits comme étant de type `int?`)

La dernière ligne dans l’exemple précédent montre la `?` opérateur conditionnel null en combinaison avec le `??` opérateur de fusion null. La nouvelle C# 6 opérateur conditionnel null retourne `null` sur l’élément 2nd dans le tableau, le moment où l’opérateur de fusion null intervient et fournit un 0 pour le `lengths` (si qui est approprié ou ne figure pas, bien sûr, de tableau problème spécifique).

L’opérateur conditionnel null doit réduire considérablement la quantité nécessaire de vérification de valeur null réutilisable dans plusieurs, plusieurs applications.

Il existe certaines limitations sur l’opérateur conditionnel null en raison d’ambiguïtés. Vous ne pouvez pas suivre immédiatement un `?` avec une liste d’arguments entre parenthèses, comme vous peut espère faire avec un délégué :

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Toutefois, `Invoke` peut être utilisé pour séparer les `?` à partir de la liste d’arguments et est toujours une amélioration marquée sur un `null`-vérification de bloc de code réutilisable :

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolation de chaîne
Le `String.Format` (fonction) a utilisé traditionnellement les indices comme espaces réservés dans la chaîne de format, par exemple, `String.Format("Expected: {0} Received: {1}.", expected, received`). Bien sûr, l’ajout d’une nouvelle valeur a toujours impliqué une tâche peu ennuyeux allant des arguments, renumérotation des espaces réservés, et insérer le nouvel argument dans la bonne séquence dans la liste d’arguments.

C#6 de la nouvelle fonctionnalité d’interpolation de chaîne améliore considérablement `String.Format`. Maintenant, vous pouvez nommer directement de variables dans une chaîne de préfixe avec un `$`. Par exemple :

```csharp
$"Expected: {expected} Received: {received}."
```

Les variables sont, bien sûr, activés et une variable mal orthographiée ou non disponibles entraîne une erreur du compilateur.

Les espaces réservés n’êtes pas obligé d’être de simples variables, ils peuvent être n’importe quelle expression. Dans ces espaces réservés, vous pouvez utiliser des guillemets *sans* ces soumissions d’échappement. Par exemple, notez le `"s"` dans l’exemple suivant :

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

Interpolation de chaîne prend en charge l’alignement et la syntaxe de mise en forme de `String.Format`. Tout comme vous déjà rédigé un article `{index, alignment:format}`, dans C# 6 que vous écrivez `{placeholder, alignment:format}`:

```csharp
using static System.Linq.Enumerable;
using System;

class Program
{
    static void Main ()
    {
        var values = new int[] { 1, 2, 3, 4, 12, 123456 };
        foreach (var s in values.Select (i => $"The value is { i,10:N2}.")) {
            Console.WriteLine (s);
        }
    Console.WriteLine ($"Minimum is { values.Min(i => i):N2}.");
    }
}
```
résultats dans :

    The value is       1.00.
    The value is       2.00.
    The value is       3.00.
    The value is       4.00.
    The value is      12.00.
    The value is 123,456.00.
    Minimum is 1.00.

Interpolation de chaîne est de liant syntaxique pour `String.Format`: il ne peut pas être utilisé avec `@""` littéraux de chaîne et n’est pas compatible avec `const`, même si aucun des espaces réservés ne sont utilisés :

```csharp
const string s = $"Foo"; //Error : const requires value
```

Dans le cas d’utilisation courant de la création d’arguments de fonction avec l’interpolation de chaîne, vous devez toujours faire attention à la séquence d’échappement, de codage et de problèmes de la culture. Requêtes SQL et les URL sont bien entendu, critiques d’expurgation. Comme avec `String.Format`, chaîne de l’interpolation utilise le `CultureInfo.CurrentCulture`. À l’aide de `CultureInfo.InvariantCulture` est un peu plus longue :

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Initialisation

C#6 fournit plusieurs façons concise pour spécifier les propriétés, les champs et les membres.

### <a name="auto-property-initialization"></a>Initialisation d’auto-property

Les auto-properties peuvent désormais être initialisés dans la même manière concise en tant que champs. Les auto-properties immuables peuvent être écrites avec uniquement un accesseur get :

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

Dans le constructeur, vous pouvez définir la valeur d’une propriété d’automatique getter uniquement :

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
    public string Description { get; }

    public ToDo (string description)
    {
        this.Description = description; //Can assign (only in constructor!)
    }
```

Cette initialisation d’auto-properties est une fonctionnalité générale de l’enregistrement de l’espace et une aubaine pour les développeurs souhaitant mettre en évidence l’immuabilité dans leurs objets.

### <a name="index-initializers"></a>Initialiseurs d’index.

C#6 introduit des initialiseurs d’index, ce qui vous permet de définir la clé et la valeur de type ayant un indexeur. En règle générale, il s’agit de `Dictionary`-style de structures de données :

```csharp
partial void ActivateHandoffClicked (WatchKit.WKInterfaceButton sender)
{
    var userInfo = new NSMutableDictionary {
        ["Created"] = NSDate.Now,
        ["Due"] = NSDate.Now.AddSeconds(60 * 60 * 24),
        ["Task"] = Description
    };
    UpdateUserActivity ("com.xamarin.ToDo.edit", userInfo, null);
    statusLabel.SetText ("Check phone");
}
```

### <a name="expression-bodied-function-members"></a>Membres expression-bodied (fonction)

Les fonctions lambda ont plusieurs avantages, dont est simplement l’économie d’espace. De même, les membres de classe expression-bodied autorisent les petites fonctions un peu plus succinctement était possible dans les versions précédentes d’exprimer C# 6.

Les membres de fonction expression-bodied utiliser la syntaxe de flèche lambda, plutôt que la syntaxe de bloc traditionnel :

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Notez que la syntaxe de la flèche vers le lambda n’utilise pas explicite `return`. Pour les fonctions qui retournent `void`, l’expression doit être également une instruction :

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Membres expression-bodied sont toujours soumis à la règle qui `async` est pris en charge pour les méthodes, mais pas les propriétés :

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Exceptions

Il n’existe aucune deux façon à son sujet : gestion des exceptions sont difficiles à gérer. Nouvelles fonctionnalités dans C# 6 rendre la gestion des exceptions plus flexible et cohérente.

### <a name="exception-filters"></a>Filtres d’exception

Par définition, les exceptions se produisent dans des circonstances inhabituelles, et il peut être très difficile de raison et le code sur *tous les* les façons d’une exception d’un type particulier peut-être se produire. C#6 introduit la possibilité de protéger un gestionnaire d’exécution avec un filtre évalué par le runtime. Cela s’effectue en ajoutant un `when (bool)` modèle après la normale `catch(ExceptionType)` déclaration. Dans l’exemple suivant, un filtre permet de distinguer une erreur d’analyse relatives à la `date` paramètre plutôt que d’autres erreurs d’analyse.

```csharp
public void ExceptionFilters(string aFloat, string date, string anInt)
{
    try
    {
        var f = Double.Parse(aFloat);
        var d = DateTime.Parse(date);
        var n = Int32.Parse(anInt);
    } catch (FormatException e) when (e.Message.IndexOf("DateTime") > -1) {
        Console.WriteLine ($"Problem parsing \"{nameof(date)}\" argument");
    } catch (FormatException x) {
        Console.WriteLine ("Problem parsing some other argument");
    }
}
```

### <a name="await-in-catchfinally"></a>await catch... finally...

Le `async` fonctions introduites dans C# 5 ont été une change la donne pour la langue. Dans C# 5, `await` n’était pas autorisée dans `catch` et `finally` bloque, ennuyeux étant donné la valeur de la `async/await` fonctionnalité. C#6 supprime cette limitation, ce qui permet des résultats asynchrones d’être attendue systématiquement via le programme comme indiqué dans l’extrait suivant :

```csharp
async void SomeMethod()
{
    try {
        //...etc...
    } catch (Exception x) {
        var diagnosticData = await GenerateDiagnosticsAsync (x);
        Logger.log (diagnosticData);
    } finally {
        await someObject.FinalizeAsync ();
    }
}
```

## <a name="summary"></a>Récapitulatif

Le C# langage continue d’évoluer pour améliorer la productivité des développeurs tout en prenant en charge des outils et promouvoir de bonnes pratiques. Ce document a offert une vue d’ensemble des nouvelles fonctionnalités de langage dans C# 6 et a démontré brièvement la façon dont ils sont utilisés.

## <a name="related-links"></a>Liens associés

- [Des fonctionnalités du nouveau langage dans C# 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

