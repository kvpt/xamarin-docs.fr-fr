---
title: C#6 présentation des nouvelles fonctionnalités
description: La version 6 du C# langage continue à évoluer le langage pour avoir moins de code réutilisable, une meilleure clarté et une plus grande cohérence. La syntaxe d’initialisation du nettoyeur, la possibilité d’utiliser await dans des blocs catch/finally et le conditionnel null? les opérateurs sont particulièrement utiles.
ms.prod: xamarin
ms.assetid: 4B4E41A8-68BA-4E2B-9539-881AC19971B
ms.custom: xamu-video
author: asb3993
ms.author: amburns
ms.date: 03/22/2017
ms.openlocfilehash: 5f51332f84d2f91adbdc8c46e509774f59a8ed9e
ms.sourcegitcommit: 5f972a757030a1f17f99177127b4b853816a1173
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69887434"
---
# <a name="c-6-new-features-overview"></a>C#6 présentation des nouvelles fonctionnalités

_La version 6 du C# langage continue à évoluer le langage pour avoir moins de code réutilisable, une meilleure clarté et une plus grande cohérence. La syntaxe d’initialisation du nettoyeur, la possibilité d’utiliser await dans des blocs catch/finally et le conditionnel null? les opérateurs sont particulièrement utiles._

> [!NOTE]
> Pour plus d’informations sur la version la C# plus récente du langage (version 7), reportez-vous à l’article [Nouveautés de C# 7,0](/dotnet/csharp/whats-new/csharp-7)

Ce document présente les nouvelles fonctionnalités de C# 6. Elle est entièrement prise en charge par le compilateur mono et les développeurs peuvent commencer à utiliser les nouvelles fonctionnalités sur toutes les plateformes cibles Xamarin.

> [!VIDEO https://youtube.com/embed/7UdV7zGPfMU]

**Nouveautés de C# la vidéo 6**

## <a name="using-c-6"></a>En C# utilisant 6

Le C# compilateur 6 est utilisé dans toutes les versions récentes de Visual Studio pour Mac.
Ceux qui utilisent des compilateurs de ligne de commande doivent `mcs --version` confirmer que retourne 4,0 ou une version ultérieure.
Visual Studio pour Mac les utilisateurs peuvent vérifier s’ils disposent de mono 4 (ou plus récent) en vous référant à à **propos de Visual Studio pour Mac > Visual Studio pour Mac > afficher les détails**.

## <a name="less-boilerplate"></a>Moins réutilisable
### <a name="using-static"></a>using static
Les énumérations, et certaines classes telles `System.Math`que, sont principalement des détenteurs des valeurs statiques et des fonctions. Dans C# 6, vous pouvez importer tous les membres statiques d’un type à `using static` l’aide d’une instruction unique. Comparer une fonction trigonométrique classique dans C# les 5 et C# 6:

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

`using static`ne rend pas les `const` champs publics, tels `Math.PI` que `Math.E`et, directement accessibles:

```csharp
for (var angle = 0.0; angle <= Math.PI * 2.0; angle += Math.PI / 8) ... 
//PI is const, not static, so requires Math.PI
```

### <a name="using-static-with-extension-methods"></a>utilisation de static avec les méthodes d’extension

La `using static` fonctionnalité fonctionne un peu différemment avec les méthodes d’extension. Bien que les méthodes d’extension `static`soient écrites à l’aide de, elles n’ont aucun sens sans une instance sur laquelle fonctionner. Ainsi, `using static` quand est utilisé avec un type qui définit des méthodes d’extension, les méthodes d’extension deviennent disponibles sur leur type cible `this` (le type de la méthode). Par exemple, `using static System.Linq.Enumerable` peut être utilisé pour étendre l’API d' `IEnumerable<T>` objets sans obtenir tous les types LINQ:

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

L’exemple précédent montre la différence de comportement: la méthode `Enumerable.Where` d’extension est associée au tableau, tandis que la méthode `String.Join` statique peut être `String` appelée sans référence au type.

### <a name="nameof-expressions"></a>Expressions nameof
Parfois, vous souhaitez faire référence au nom que vous avez donné à une variable ou un champ. Dans C# 6, `nameof(someVariableOrFieldOrType)` retourne la chaîne. `"someVariableOrFieldOrType"` Par exemple, lors de la `ArgumentException` levée d’un, il est très probable que vous souhaitiez nommer l’argument qui n’est pas valide:

```csharp
throw new ArgumentException ("Problem with " + nameof(myInvalidArgument))
```

Le principal avantage des `nameof` expressions est qu’elles sont de type vérifié et sont compatibles avec la refactorisation pilotée par les outils. Le contrôle de type des `nameof` expressions est particulièrement utile dans les situations où `string` un est utilisé pour associer de manière dynamique des types. Par exemple, dans iOS a `string` est utilisé pour spécifier le type utilisé pour prototyper `UITableViewCell` des objets `UITableView`dans un. `nameof`peut s’assurer que cette association n’échoue pas en raison d’une erreur d’orthographe ou de refactorisation mal écrit:

```csharp
public override UITableViewCell GetCell (UITableView tableView, NSIndexPath indexPath)
{
    var cell = tableView.DequeueReusableCell (nameof(CellTypeA), indexPath);
    cell.TextLabel.Text = objects [indexPath.Row].ToString ();
    return cell;
}
```

Bien que vous puissiez passer un nom qualifié `nameof`à, seul l’élément final (après le `.`dernier) est retourné. Par exemple, vous pouvez ajouter une liaison de données dans Xamarin. Forms:

```csharp
var myReactiveInstance = new ReactiveType ();
var myLabelOld.BindingContext = myReactiveInstance;
var myLabelNew.BindingContext = myReactiveInstance;
var myLabelOld.SetBinding (Label.TextProperty, "StringField");
var myLabelNew.SetBinding (Label.TextProperty, nameof(ReactiveType.StringField));
```

Les deux appels à `SetBinding` passent des valeurs identiques: `nameof(ReactiveType.StringField)` est `"StringField"`, pas `"ReactiveType.StringField"` comme vous pourriez l’attendre initialement.

## <a name="null-conditional-operator"></a>Opérateur conditionnel null
Les mises à jour antérieures C# ont introduit les concepts des types Nullable et l’opérateur `??` de fusion Null pour réduire la quantité de code réutilisable lors de la gestion de valeurs Nullable. C#6 poursuit ce thème avec l' «opérateur conditionnel null» `?.`. Lorsqu’il est utilisé sur un objet sur le côté droit d’une expression, l’opérateur conditionnel null retourne la valeur du membre si l’objet n’est `null` pas `null` et dans le cas contraire:

```csharp
var ss = new string[] { "Foo", null };
var length0 = ss [0]?.Length; // 3
var length1 = ss [1]?.Length; // null
var lengths = ss.Select (s => s?.Length ?? 0); //[3, 0]
```

(Et `length0` sont `length1` déduits en tant que type `int?`)

La dernière ligne de l’exemple précédent montre l' `?` opérateur conditionnel null en association avec l' `??` opérateur de fusion Null. Le nouvel C# opérateur conditionnel null est retourné `null` sur le deuxième élément du tableau, auquel cas l’opérateur de fusion Null démarre et fournit `lengths` un 0 au tableau (qu’il soit approprié ou non, bien sûr). spécifique au problème).

L’opérateur conditionnel null doit considérablement réduire la quantité de réutilisabilité des valeurs NULL nécessaires dans de nombreuses applications.

Il existe certaines limitations sur l’opérateur conditionnel null en raison d’ambiguïtés. Vous ne pouvez pas suivre `?` immédiatement un avec une liste d’arguments entre parenthèses, comme vous pouvez l’espérer pour un délégué:

```csharp
SomeDelegate?("Some Argument") // Not allowed
```

Toutefois, `Invoke` peut être utilisé pour séparer le `?` de la liste d’arguments et est toujours `null`une amélioration marquée du bloc de contrôle de l’utilisation:

```csharp
public event EventHandler HandoffOccurred;
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    HandoffOccurred?.Invoke (this, userActivity.UserInfo);
    return true;
}
```

## <a name="string-interpolation"></a>Interpolation de chaîne
La `String.Format` fonction a traditionnellement utilisé des index comme espaces réservés dans la chaîne de format, par exemple, `String.Format("Expected: {0} Received: {1}.", expected, received`). Bien entendu, l’ajout d’une nouvelle valeur a toujours impliqué une petite tâche ennuyeux pour le comptage des arguments, la renumérotation des espaces réservés et l’insertion du nouvel argument dans la séquence droite de la liste d’arguments.

C#la nouvelle fonctionnalité d’interpolation de chaîne de 6 s' `String.Format`améliore considérablement sur. À présent, vous pouvez nommer directement des variables dans une chaîne avec un `$`préfixe. Par exemple :

```csharp
$"Expected: {expected} Received: {received}."
```

Les variables sont, bien entendu, vérifiées et une variable mal orthographiée ou non disponible provoque une erreur du compilateur.

Les espaces réservés n’ont pas besoin d’être des variables simples, il peut s’agir de n’importe quelle expression. Au sein de ces espaces réservés, vous pouvez utiliser des guillemets *sans* les placer dans une séquence d’échappement. Par exemple, notez les `"s"` éléments suivants:

```csharp
var s = $"Timestamp: {DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}"
```

L’interpolation de chaîne prend en charge l’alignement et la `String.Format`syntaxe de mise en forme de. Tout comme vous l’avez `{index, alignment:format}`écrit précédemment C# , dans 6 `{placeholder, alignment:format}`, vous écrivez:

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

résultats dans:

```
The value is       1.00.
The value is       2.00.
The value is       3.00.
The value is       4.00.
The value is      12.00.
The value is 123,456.00.
Minimum is 1.00.
```

L’interpolation de chaîne est un sucre `String.Format`syntaxique pour: elle ne peut `@""` pas être utilisée avec des littéraux de `const`chaîne et n’est pas compatible avec, même si aucun espace réservé n’est utilisé:

```csharp
const string s = $"Foo"; //Error : const requires value
```

Dans le cas d’utilisation courante de la création d’arguments de fonction avec l’interpolation de chaîne, vous devez faire attention à l’échappement, l’encodage et les problèmes de culture. Les requêtes SQL et URL sont, bien sûr, critiques à assainir. Comme avec `String.Format`, l' `CultureInfo.CurrentCulture`interpolation de chaîne utilise. L' `CultureInfo.InvariantCulture` utilisation de est un peu plus simple:

```csharp
Thread.CurrentThread.CurrentCulture  = new CultureInfo ("de");
Console.WriteLine ($"Today is: {DateTime.Now}"); //"21.05.2015 13:52:51"
Console.WriteLine ($"Today is: {DateTime.Now.ToString(CultureInfo.InvariantCulture)}"); //"05/21/2015 13:52:51"
```

## <a name="initialization"></a>Initialisation

C#6 fournit un certain nombre de méthodes concises pour spécifier des propriétés, des champs et des membres.

### <a name="auto-property-initialization"></a>Initialisation automatique des propriétés

Les propriétés automatiques peuvent désormais être initialisées de la même façon concise que les champs. Les propriétés auto immuables peuvent être écrites uniquement avec un accesseur Get:

```csharp
class ToDo
{
    public DateTime Due { get; set; } = DateTime.Now.AddDays(1);
    public DateTime Created { get; } = DateTime.Now;
```

Dans le constructeur, vous pouvez définir la valeur d’une propriété automatique Getter-only:

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

Cette initialisation des propriétés automatiques est à la fois une fonctionnalité d’économie d’espace générale et une aubaine pour les développeurs souhaitant mettre en évidence l’immuabilité dans leurs objets.

### <a name="index-initializers"></a>Initialiseurs d’index.

C#6 introduit des initialiseurs d’index, qui vous permettent de définir à la fois la clé et la valeur dans les types qui ont un indexeur. En règle générale, il `Dictionary`s’agit des structures de données de style:

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

### <a name="expression-bodied-function-members"></a>Membres de fonctions de caractères corporels

Les fonctions lambda présentent plusieurs avantages, dont l’un est simplement l’économie d’espace. De même, les membres de classe de type «expression» permettent aux petites fonctions d’être exprimées un peu plus succinctement que dans C# les versions précédentes de 6.

Les fonctions membres de fonction par expression utilisent la syntaxe de flèche lambda plutôt que la syntaxe de bloc traditionnelle:

```csharp
public override string ToString () => $"{FirstName} {LastName}";
```

Notez que la syntaxe de la flèche lambda n’utilise pas de `return`Explicit. Pour les fonctions qui `void`retournent, l’expression doit également être une instruction:

```csharp
public void Log(string message) => System.Console.WriteLine($"{DateTime.Now.ToString ("s", System.Globalization.CultureInfo.InvariantCulture )}: {message}");
```

Les membres de l’expression sont toujours soumis à la règle prise `async` en charge pour les méthodes, mais pas pour les propriétés:

```csharp
//A method, so async is valid
public async Task DelayInSeconds(int seconds) => await Task.Delay(seconds * 1000);
//The following property will not compile
public async Task<int> LeisureHours => await Task.FromResult<char> (DateTime.Now.DayOfWeek.ToString().First()) == 'S' ? 16 : 5;
```

## <a name="exceptions"></a>Exceptions

Il n’y a pas deux façons de le faire: la gestion des exceptions est difficile à obtenir. Les nouvelles fonctionnalités C# de 6 rendent la gestion des exceptions plus flexible et plus cohérente.

### <a name="exception-filters"></a>Filtres d’exception

Par définition, les exceptions se produisent dans des circonstances inhabituelles, et elles peuvent être très difficiles à mettre en cause et du code sur *toutes* les façons dont une exception d’un type particulier peut se produire. C#6 introduit la possibilité de protéger un gestionnaire d’exécution à l’aide d’un filtre évalué par le Runtime. Pour ce faire, ajoutez un `when (bool)` modèle après la Déclaration `catch(ExceptionType)` normale. Dans l’exemple suivant, un filtre distingue une erreur d' `date` analyse relative au paramètre par opposition à d’autres erreurs d’analyse.

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

### <a name="await-in-catchfinally"></a>attendre dans catch... Enfin...

Les `async` fonctionnalités introduites C# dans 5 ont été un changeur de jeu pour le langage. Dans C# 5, `await` n’était pas autorisé `catch` dans `finally` les blocs et, une gêne en raison de `async/await` la valeur de la fonctionnalité. C#6 supprime cette limitation, en permettant aux résultats asynchrones d’être attendus de manière cohérente via le programme, comme indiqué dans l’extrait de code suivant:

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

Le C# langage continue d’évoluer pour rendre les développeurs plus productifs tout en promouvant les bonnes pratiques et les outils de prise en charge. Ce document a donné une vue d’ensemble des nouvelles fonctionnalités de C# langage dans 6 et a brièvement démontré comment elles sont utilisées.

## <a name="related-links"></a>Liens associés

- [Nouvelles fonctionnalités de langage C# dans 6](https://github.com/dotnet/roslyn/wiki/New-Language-Features-in-C%23-6)

