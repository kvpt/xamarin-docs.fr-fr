---
title: Génération de vues HTML à l’aide de modèles Razor
description: " L’utilisation d’une page Web en plein écran pour le rendu HTML peut être un moyen simple et efficace de restituer une mise en forme complexe de manière multiplateforme, en particulier si vous disposez déjà du code HTML, JavaScript et CSS d’un projet de site Web."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: davidortinau
ms.author: daortin
ms.date: 07/24/2018
ms.openlocfilehash: 5f1b1345f9abbf891cfbea6e45a8ed2abd7c0dac
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73014602"
---
# <a name="building-html-views-using-razor-templates"></a>Génération de vues HTML à l’aide de modèles Razor

Dans le monde du développement mobile, le terme « application hybride » fait généralement référence à une application qui présente certains (ou la totalité) de ses écrans sous forme de pages HTML dans un contrôle de visionneuse Web hébergé.

Certains environnements de développement vous permettent de générer votre application mobile entièrement en HTML et JavaScript, mais ces applications peuvent être sujettes à des problèmes de performances lorsque vous essayez d’effectuer un traitement complexe ou des effets d’interface utilisateur, et elles sont également limitées dans la plateforme. fonctionnalités auxquelles ils peuvent accéder.

Xamarin offre le meilleur des deux mondes, en particulier lors de l’utilisation du moteur de création de modèles HTML Razor. Avec Xamarin, vous avez la possibilité de créer des vues HTML multiplateformes basées sur des modèles qui utilisent JavaScript et CSS, mais également d’avoir un accès complet aux API de C#la plateforme sous-jacente et un traitement rapide à l’aide de.

Ce document explique comment utiliser le moteur de création de modèles Razor pour générer des vues HTML + JavaScript + CSS qui peuvent être utilisées sur des plateformes mobiles à l’aide de Xamarin.

## <a name="using-web-views-programmatically"></a>Utilisation de vues Web par programmation

Avant de découvrir Razor, cette section explique comment utiliser des affichages Web pour afficher directement le contenu HTML, en particulier le contenu HTML généré au sein d’une application.

Xamarin fournit un accès complet aux API de la plateforme sous-jacente sur iOS et Android. il est donc facile de créer et C#d’afficher du code HTML à l’aide de. La syntaxe de base pour chaque plateforme est illustrée ci-dessous.

### <a name="ios"></a>iOS

L’affichage de code HTML dans un contrôle UIWebView dans Xamarin. iOS ne prend que quelques lignes de code :

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Pour plus d’informations sur l’utilisation du contrôle UIWebView, consultez les recettes [UIWebView iOS](https://github.com/xamarin/docs-archive/tree/master/Recipes/ios/content_controls/web_view) .

### <a name="android"></a>Android

L’affichage de code HTML dans un contrôle WebView à l’aide de Xamarin. Android s’effectue en quelques lignes de code :

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Pour plus d’informations sur l’utilisation du contrôle WebView, consultez les recettes d' [Android WebView](https://github.com/xamarin/docs-archive/tree/master/Recipes/android/controls/webview) .

### <a name="specifying-the-base-directory"></a>Spécification du répertoire de base

Sur les deux plateformes, il existe un paramètre qui spécifie le répertoire de base pour la page HTML. Il s’agit de l’emplacement sur le système de fichiers de l’appareil qui est utilisé pour résoudre des références relatives à des ressources telles que des images et des fichiers CSS. Par exemple, des balises telles que

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

reportez-vous à ces fichiers : **style. CSS**, **singe. jpg** et **JScript. js**. Le paramètre répertoire de base indique à l’affichage Web où se trouvent ces fichiers afin qu’ils puissent être chargés dans la page.

#### <a name="ios"></a>iOS

La sortie du modèle est rendue dans iOS avec le C# code suivant :

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

Le répertoire de base est spécifié en tant que `NSBundle.MainBundle.BundleUrl` qui fait référence au répertoire dans lequel l’application est installée. Tous les fichiers du dossier **Resources** sont copiés à cet emplacement, par exemple le fichier **style. CSS** présenté ici :

 ![solution iPhoneHybrid](images/image1_240x163.png)

L’action de génération pour tous les fichiers de contenu statiques doit être **BundleResource**:

 ![action de génération de projet iOS : BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android requiert également qu’un répertoire de base soit passé en tant que paramètre lorsque les chaînes HTML sont affichées dans une vue Web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La chaîne spéciale **file:///android_asset/** fait référence au dossier des ressources Android dans votre application, illustré ici contenant le fichier **style. CSS** .

 ![Solution AndroidHybrid](images/image3_240x167.png)

L’action de génération pour tous les fichiers de contenu statique doit être **AndroidAsset**.

 ![Action de génération de projet Android : AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Appeler C# à partir de HTML et JavaScript

Lorsqu’une page HTML est chargée dans une vue Web, elle traite les liens et les formulaires comme si la page était chargée à partir d’un serveur. Cela signifie que si l’utilisateur clique sur un lien ou envoie un formulaire, l’affichage Web tente de naviguer jusqu’à la cible spécifiée.

Si le lien est vers un serveur externe (par exemple, google.com), l’affichage Web tente de charger le site Web externe (en supposant qu’il existe une connexion Internet).

```html
<a href="http://google.com/">Google</a>
```

Si le lien est relatif, l’affichage Web tente de charger ce contenu à partir du répertoire de base. Évidemment, aucune connexion réseau n’est nécessaire pour que cela fonctionne, car le contenu est stocké dans l’application sur l’appareil.

```html
<a href="somepage.html">Local content</a>
```

Les actions de formulaire suivent la même règle.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Vous n’allez pas héberger un serveur Web sur le client. Toutefois, vous pouvez utiliser les mêmes techniques de communication serveur que celles utilisées dans les modèles de conception réactifs d’aujourd’hui pour appeler des services via HTTP et gérer les réponses de façon asynchrone en émettant du code JavaScript (ou en appelant JavaScript déjà hébergé dans la vue Web). Cela vous permet de passer facilement les données du code HTML dans C# le code à des fins de traitement, puis d’afficher à nouveau les résultats sur la page html.

IOS et Android fournissent un mécanisme permettant au code d’application d’intercepter ces événements de navigation afin que le code d’application puisse répondre (si nécessaire). Cette fonctionnalité est essentielle à la création d’applications hybrides, car elle permet au code natif d’interagir avec la vue Web.

#### <a name="ios"></a>iOS

L’événement ShouldStartLoad sur la vue Web dans iOS peut être substitué pour permettre au code de l’application de gérer une requête de navigation (par exemple, un clic sur un lien). Les paramètres de la méthode fournissent toutes les informations

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

puis attribuez le gestionnaire d’événements :

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

Sur Android, il vous suffit de sous-classe WebViewClient, puis d’implémenter le code pour répondre à la demande de navigation.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

puis définissez le client sur l’affichage Web :

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Appel de JavaScript à partir de C\#

En plus d’indiquer à une vue Web de charger une nouvelle page HTML C# , le code peut également exécuter JavaScript dans la page actuellement affichée. Vous pouvez créer des blocs de code JavaScript C# entiers à l’aide de chaînes et les exécuter, ou vous pouvez élaborer des appels de méthode vers JavaScript déjà disponibles sur la page via des balises`script`.

#### <a name="android"></a>Android

Créez le code JavaScript à exécuter, puis faites-le précéder de « JavaScript : » et demandez à l’affichage Web de charger cette chaîne :

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

les vues Web iOS fournissent une méthode spécifique pour appeler JavaScript :

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Récapitulatif

Cette section a introduit les fonctionnalités des contrôles d’affichage Web sur Android et iOS qui nous permettent de créer des applications hybrides avec Xamarin, notamment :

- La possibilité de charger du code HTML à partir de chaînes générées dans le code,
- La possibilité de référencer des fichiers locaux (CSS, JavaScript, images ou autres fichiers HTML);
- La possibilité d’intercepter des demandes C# de navigation dans le code,
- La possibilité d’appeler JavaScript à C# partir du code.

La section suivante présente Razor, qui vous permet de créer facilement le code HTML à utiliser dans les applications hybrides.

## <a name="what-is-razor"></a>Qu’est-ce que Razor ?

Razor est un moteur de création de modèles qui a été introduit avec ASP.NET MVC, à l’origine pour s’exécuter sur le serveur et pour générer du code HTML à fournir aux navigateurs Web.

Le moteur de création de modèles Razor étend la C# syntaxe HTML standard avec pour vous permettre d’exprimer la disposition et d’incorporer facilement les feuilles de style CSS et JavaScript. Le modèle peut faire référence à une classe de modèle, qui peut être n’importe quel type personnalisé et dont les propriétés sont accessibles directement à partir du modèle. L’un de ses principaux avantages est la possibilité de mélanger facilement C# le HTML et la syntaxe.

Les modèles Razor ne sont pas limités à une utilisation côté serveur, ils peuvent également être inclus dans les applications Xamarin. L’utilisation de modèles Razor, ainsi que la possibilité d’utiliser des vues Web par programme, permettent la génération d’applications hybrides multiplateforme sophistiquées avec Xamarin.

### <a name="razor-template-basics"></a>Concepts de base du modèle Razor

Les fichiers de modèle Razor ont une extension de fichier **. cshtml** . Ils peuvent être ajoutés à un projet Xamarin à partir de la section Création de modèles de texte de la boîte de dialogue **nouveau fichier** :

 ![Nouveau fichier-modèle Razor](images/image5_400x201.png)

Un modèle Razor simple ( **RazorView. cshtml**) est illustré ci-dessous.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Notez les différences suivantes par rapport à un fichier HTML standard :

- Le symbole `@` a une signification particulière dans les modèles Razor : il indique que l’expression C# suivante doit être évaluée.
- `@model` directive apparaît toujours en tant que première ligne d’un fichier de modèle Razor.
- La directive `@model` doit être suivie d’un type. Dans cet exemple, une simple chaîne est passée au modèle, mais il peut s’agir de n’importe quelle classe personnalisée.
- Lorsque `@Model` est référencé dans tout le modèle, il fournit une référence à l’objet passé au modèle lors de sa génération (dans cet exemple, il s’agit d’une chaîne).
- L’IDE génère automatiquement une classe partielle pour les modèles (fichiers avec l’extension **. cshtml** ). Vous pouvez afficher ce code, mais il ne doit pas être modifié.
 ![RazorView. cshtml](images/image6_125x34.png) la classe partielle est nommée RazorView pour correspondre au nom du fichier de modèle. cshtml. Il s’agit du nom utilisé pour faire référence au modèle dans C# le code.
- les instructions `@using` peuvent également être incluses en haut d’un modèle Razor pour inclure des espaces de noms supplémentaires.

La sortie HTML finale peut ensuite être générée à l’aide C# du code suivant. Notez que nous spécifions que le modèle doit être une chaîne « Hello World » qui sera incorporée dans la sortie du modèle rendu.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Voici la sortie affichée dans une vue Web sur le simulateur iOS et Émulateur Android :

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Plus de syntaxe Razor

Dans cette section, nous allons présenter des syntaxe Razor de base pour vous aider à commencer à l’utiliser. Les exemples de cette section remplissent la classe suivante avec des données et l’affichent à l’aide de Razor :

```csharp
public class Monkey {
    public string Name { get; set; }
    public DateTime Birthday { get; set; }
    public List<string> FavoriteFoods { get; set; }
}
```

Tous les exemples utilisent le code d’initialisation de données suivant

```csharp
var animal = new Monkey {
    Name = "Rupert",
    Birthday=new DateTime(2011, 04, 01),
    FavoriteFoods = new List<string>
        {"Bananas", "Banana Split", "Banana Smoothie"}
};
```

#### <a name="displaying-model-properties"></a>Affichage des propriétés de modèle

Quand le modèle est une classe avec des propriétés, il est facilement référencé dans le modèle Razor, comme illustré dans cet exemple de modèle :

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Cela peut être rendu dans une chaîne à l’aide du code suivant :

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

La sortie finale est présentée ici dans une vue Web sur le simulateur iOS et Émulateur Android :

 ![Rupert](images/image8_516x160.png)

#### <a name="c-statements"></a>C#publication

Un modèle C# plus complexe peut être inclus dans le modèle, par exemple les mises à jour des propriétés du modèle et le calcul de l’âge dans cet exemple :

```html
@model Monkey
<html>
    <body>
    @{
        Model.Name = "Rupert X. Monkey";
        Model.Birthday = new DateTime(2011,3,1);
    }
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    </body>
</html>
```

Vous pouvez écrire des expressions à ligne C# simple complexes (telles que la mise en forme de l’âge) en entourant le code avec `@()`.

Plusieurs C# instructions peuvent être écrites en les entourant de `@{}`.

#### <a name="if-else-statements"></a>Instructions if-else

Les branches de code peuvent être exprimées avec `@if` comme indiqué dans cet exemple de modèle.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <p>@Model.FavoriteFoods.Count favorites</p>
    }
    </body>
</html>
```

#### <a name="loops"></a>Boucles

Les constructions de boucle comme `foreach` peuvent également être ajoutées. Le préfixe `@` peut être utilisé sur la variable de boucle (`@food` dans ce cas) pour le rendre en HTML.

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @Model.Birthday.ToString("d MMMM yyyy")</p>
    <p>Age: @(Math.Floor(DateTime.Now.Date.Subtract (Model.Birthday.Date).TotalDays/365)) years old</p>
    <p>Favorite Foods:</p>
    @if (Model.FavoriteFoods.Count == 0) {
        <p>No favorites</p>
    } else {
        <ul>
            @foreach (var food in @Model.FavoriteFoods) {
                <li>@food</li>
            }
        </ul>
    }
    </body>
</html>
```

La sortie du modèle ci-dessus est indiquée en cours d’exécution sur le simulateur iOS et Émulateur Android :

 ![Singe Rupert X](images/image9_520x277.png)

Cette section a abordé les principes de base de l’utilisation de modèles Razor pour afficher des vues en lecture seule simples. La section suivante explique comment créer des applications plus complètes à l’aide de Razor qui peuvent accepter les entrées utilisateur et interagir entre JavaScript en mode HTML C#et.

## <a name="using-razor-templates-with-xamarin"></a>Utilisation de modèles Razor avec Xamarin

Cette section explique comment utiliser créer votre propre application hybride à l’aide des modèles de solution dans Visual Studio pour Mac. Trois modèles sont disponibles à partir du **fichier > nouvelle > solution...** :

- **Application > Android > application Android WebView**
- **Application > iOS > application WebView**
- **Projet MVC ASP.NET**

La **nouvelle** fenêtre de solution ressemble à ceci pour les projets iPhone et Android : la description de la solution sur la droite met en évidence la prise en charge du moteur de création de modèles Razor.

 ![Création de solutions iPhone et Android](images/image13_1139x959.png)

Notez que vous pouvez facilement ajouter un modèle Razor **. cshtml** à *n’importe quel* projet Xamarin existant ; il n’est pas nécessaire d’utiliser ces modèles de solution. les projets iOS n’ont pas besoin d’une table de montage séquentiel pour utiliser Razor. Ajoutez simplement un contrôle UIWebView à toute vue par programmation et vous pouvez restituer les modèles Razor dans C# le code.

Le contenu par défaut de la solution de modèle pour les projets iPhone et Android est illustré ci-dessous :

 ![modèles iPhone et Android](images/image10_428x310.png)

Les modèles vous offrent une infrastructure d’application prête à l’emploi pour charger un modèle Razor avec un objet de modèle de données, traiter l’entrée d’utilisateur et communiquer avec l’utilisateur via JavaScript.

Les éléments importants de la solution sont les suivants :

- Contenu statique, tel que le fichier **style. CSS** .
- Les fichiers de modèle Razor. cshtml comme **RazorView. cshtml** .
- Classes de modèle référencées dans les modèles Razor tels que **ExampleModel.cs** .
- Classe spécifique à la plateforme qui crée l’affichage Web et restitue le modèle, comme le `MainActivity` sur Android et le `iPhoneHybridViewController` sur iOS.

La section suivante explique le fonctionnement des projets.

### <a name="static-content"></a>Contenu statique

Le contenu statique comprend des feuilles de style CSS, des images, des fichiers JavaScript ou d’autres contenus qui peuvent être liés ou référencés par un fichier HTML affiché dans une vue Web.

Les projets de modèle incluent une feuille de style minimale pour montrer comment inclure du contenu statique dans votre application hybride. La feuille de style CSS est référencée dans le modèle de la façon suivante :

```html
<link rel="stylesheet" href="style.css" />
```

Vous pouvez ajouter n’importe quel fichier de feuille de style et JavaScript dont vous avez besoin, y compris des infrastructures telles que JQuery.

### <a name="razor-cshtml-templates"></a>Modèles Razor cshtml

Le modèle inclut un fichier Razor **. cshtml** contenant du code pré-écrit pour faciliter la communication des données entre le HTML/ C#JavaScript et. Cela vous permet de créer des applications hybrides sophistiquées qui n’affichent pas uniquement les données en lecture seule à partir du modèle, mais acceptent également l’entrée utilisateur dans C# le code HTML et la repasse au code pour le traitement ou le stockage.

#### <a name="rendering-the-template"></a>Rendu du modèle

L’appel de la `GenerateString` sur un modèle rend le HTML prêt pour l’affichage dans une vue Web. Si le modèle utilise un modèle, il doit être fourni avant le rendu. Ce diagramme illustre le fonctionnement du rendu, et non que les ressources statiques sont résolues par l’affichage Web lors de l’exécution, à l’aide du répertoire de base fourni pour rechercher les fichiers spécifiés.

 ![Diagramme de flux Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Appel C# de code à partir du modèle

La communication à partir d’une vue Web rendue C# qui rappelle à est effectuée en définissant l’URL de la vue Web, puis en interceptant la requête dans C# pour gérer la demande native sans recharger l’affichage Web.

Vous pouvez voir un exemple dans la manière dont le bouton RazorView est géré. Le bouton a le code HTML suivant :

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

La fonction JavaScript `InvokeCSharpWithFormValues` lit toutes les valeurs du formulaire HTML et définit la `location.href` pour l’affichage Web :

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Cela tente de naviguer dans la vue Web vers une URL avec un schéma personnalisé (par exemple, `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Lorsque l’affichage Web natif traite cette requête de navigation, nous avons la possibilité de l’intercepter. Dans iOS, cette opération est effectuée en gérant l’événement HandleShouldStartLoad de UIWebView. Dans Android, nous avons simplement sous-classe les WebViewClient utilisés dans le formulaire et remplaçons ShouldOverrideUrlLoading.

Les éléments internes de ces deux intercepteurs de navigation sont fondamentalement les mêmes.

Tout d’abord, vérifiez l’URL que l’affichage Web tente de charger et, si elle ne commence pas par le schéma personnalisé (`hybrid:`), laissez la navigation se produire normalement.

Pour le modèle d’URL personnalisée, tout ce qui se trouve dans l’URL entre le schéma et le «  ? » nom de la méthode à gérer (dans ce cas, « UpdateLabel »). Tout dans la chaîne de requête sera traité comme les paramètres de l’appel de la méthode :

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` dans cet exemple effectue une quantité minime de manipulations de chaînes sur le paramètre de zone deC# texte (en attente « dit » à la chaîne), puis rappelle la vue Web.

Après avoir géré l’URL, la méthode abandonne la navigation afin que l’affichage Web ne tente pas de terminer la navigation vers l’URL personnalisée.

#### <a name="manipulating-the-template-from-c"></a>Manipulation du modèle à partir de C\#

La communication à une vue Web HTML rendue C# à partir de est effectuée en appelant JavaScript dans l’affichage Web. Sur iOS, cette opération s’effectue en appelant `EvaluateJavascript` sur le UIWebView :

```csharp
webView.EvaluateJavascript (js);
```

Sur Android, JavaScript peut être appelé dans l’affichage Web en chargeant le JavaScript en tant qu’URL en utilisant le modèle d’URL `"javascript:"` :

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Rendre une application véritablement hybride

Ces modèles n’utilisent pas de contrôles natifs sur chaque plateforme : la totalité de l’écran est remplie avec une seule vue Web.

Le langage HTML peut être très utile pour le prototypage et l’affichage des types d’éléments sur lesquels le Web est le plus adapté, comme le texte enrichi et la disposition réactive. Toutefois, toutes les tâches ne sont pas adaptées au HTML et au JavaScript, par exemple, en parcourant des listes de données longues, par exemple, il est préférable d’utiliser des contrôles d’interface utilisateur natifs (tels que UITableView sur iOS ou ListView sur Android).

Les vues Web du modèle peuvent facilement être augmentées à l’aide de contrôles spécifiques à la plateforme. modifiez simplement **fichier mainstoryboard. Storyboard** dans le concepteur iOS ou **ressources/mise en page/main. AXML** sur Android.

### <a name="razortodo-sample"></a>Exemple RazorTodo

Le référentiel [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) contient deux solutions distinctes pour montrer les différences entre une application entièrement pilotée par HTML et une application qui combine du code HTML avec les contrôles natifs :

- **RazorTodo** -application entièrement pilotée par HTML à l’aide de modèles Razor.
- **RazorNativeTodo** : utilise des contrôles d’affichage de liste natifs pour iOS et Android, mais affiche l’écran de modification avec HTML et Razor.

Ces applications Xamarin s’exécutent à la fois sur iOS et Android, en utilisant les bibliothèques de classes portables (classes portables) pour partager du code commun, comme les classes de base de données et de modèle. Les modèles Razor **. cshtml** peuvent également être inclus dans la bibliothèque de classes portable, afin qu’ils soient facilement partagés entre les plateformes.

Les deux exemples d’applications intègrent le partage Twitter et les API Text-to-Speech de la plateforme native, ce qui démontre que les applications hybrides avec Xamarin ont toujours accès à toutes les fonctionnalités sous-jacentes à partir de vues HTML Razor basées sur des modèles.

L’application **RazorTodo** utilise des modèles Razor html pour les affichages de liste et de modification. Cela signifie que nous pouvons créer l’application presque complètement dans une bibliothèque de classes portable partagée (y compris la base de données et les modèles Razor **. cshtml** ). Les captures d’écran ci-dessous montrent les applications iOS et Android.

 ![RazorTodo](images/Both_700x290.png)

L’application **RazorNativeTodo** utilise un modèle Razor html pour la vue Edit, mais implémente une liste de défilement native sur chaque plateforme. Cela offre un certain nombre d’avantages, notamment :

- Performances : les contrôles de défilement natifs utilisent la virtualisation pour garantir un défilement rapide et sans heurts, même avec de très longues listes de données.
- Expérience Native : les éléments d’interface utilisateur spécifiques à la plateforme sont facilement activés, tels que la prise en charge de l’index de défilement rapide dans iOS et Android.

L’un des principaux avantages de la création d’applications hybrides avec Xamarin est que vous pouvez commencer avec une interface utilisateur entièrement pilotée par HTML (comme le premier exemple), puis ajouter des fonctionnalités spécifiques à la plateforme lorsque cela est nécessaire (comme le deuxième exemple le montre). Les écrans de liste natifs et les écrans de modification Razor HTML sur iOS et Android sont affichés ci-dessous.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Récapitulatif

Cet article a expliqué les fonctionnalités des contrôles d’affichage Web disponibles sur iOS et Android qui facilitent la création d’applications hybrides.

Il a ensuite abordé le moteur de création de modèles Razor et la syntaxe qui peut être utilisée pour générer facilement du code HTML dans les applications Xamarin à l’aide de. **cshtml** Fichiers de modèle Razor. Il décrit également les modèles de solution Visual Studio pour Mac qui vous permettent de commencer rapidement à créer des applications hybrides avec Xamarin.

Enfin, il a introduit les exemples RazorTodo qui montrent comment combiner des vues Web avec des interfaces utilisateur et des API natives.

### <a name="related-links"></a>Liens associés

- [Exemple RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3-moteur d’affichage Razor (Microsoft)](https://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Présentation de la programmation Web ASP.NET à l’aide de la syntaxe Razor (Microsoft)](https://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
