---
title: Vues HTML de génération à l’aide de modèles Razor
description: " À l’aide d’une page Web plein écran pour le rendu HTML peut être un moyen simple et efficace pour restituer la mise en forme complexe de manière inter-plateformes, surtout si vous avez déjà HTML, JavaScript et CSS à partir d’un projet de site Web."
ms.prod: xamarin
ms.assetid: D8B87C4F-178E-48D9-BE43-85066C46F05C
author: asb3993
ms.author: amburns
ms.date: 07/24/2018
ms.openlocfilehash: 539f59b9835cab6281327bcd1a37482ef82b62cc
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650177"
---
# <a name="building-html-views-using-razor-templates"></a>Vues HTML de génération à l’aide de modèles Razor

Dans le monde du développement mobile la terme « application hybride » fait généralement référence à une application qui présente certaines de ses écrans (ou all) en tant que pages HTML à l’intérieur d’un contrôle de visionneuse web hébergé.

Il existe certains environnements de développement qui vous permettent de générer votre application de mobile entièrement en HTML et JavaScript, toutefois, ces applications peuvent subir des problèmes de performances lorsque vous tentez d’effectuer un traitement complexe ou les effets d’interface utilisateur et sont également limité dans la plateforme fonctionnalités auxquelles qu'ils peuvent accéder.

Xamarin offre le meilleur des deux mondes, en particulier lorsque vous utilisez le moteur de création de modèles Razor HTML. Avec Xamarin, vous avez la possibilité de créer des vues HTML basé sur un modèle multiplateforme qui utilisent JavaScript et CSS, mais également un accès complet à l’API de la plateforme sous-jacente et rapidement de traitement à l’aide C#.

Ce document explique comment utiliser le moteur de création de modèles Razor pour générer des vues HTML + JavaScript + CSS qui peuvent être utilisées sur les plateformes mobiles à l’aide de Xamarin.

## <a name="using-web-views-programmatically"></a>À l’aide de vues Web par programme

Avant de nous Découvrez Razor cette section couvre l’utilisation des vues web pour afficher le contenu HTML directement – en particulier le contenu HTML est généré au sein d’une application.

Xamarin fournit un accès complet à la plateforme sous-jacente API sur iOS et Android, il est facile de créer et afficher le code HTML à l’aide de c#. Vous trouverez ci-dessous la syntaxe de base pour chaque plateforme.

### <a name="ios"></a>iOS

Affichage HTML dans un contrôle UIWebView dans Xamarin.iOS prend également quelques lignes de code :

```csharp
var webView = new UIWebView (View.Bounds);
View.AddSubview(webView);
string contentDirectoryPath = Path.Combine (NSBundle.MainBundle.BundlePath, "Content/");
var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadHtmlString(html, NSBundle.MainBundle.BundleUrl);
```

Consultez le [iOS UIWebView](http://docs.xamarin.com/recipes/ios/content_controls/web_view/) recettes pour plus d’informations sur l’utilisation du contrôle UIWebView.

### <a name="android"></a>Android

Affichage HTML dans un contrôle WebView à l’aide de Xamarin.Android s’effectue en quelques lignes de code :

```csharp
// webView is declared in an AXML layout file
var webView = FindViewById<WebView> (Resource.Id.webView);

// enable JavaScript execution in your html view so you can provide "alerts" and other js
webView.SetWebChromeClient(new WebChromeClient());

var html = "<html><h1>Hello</h1><p>World</p></html>";
webView.LoadDataWithBaseURL("file:///android_asset/", html, "text/html", "UTF-8", null);
```

Consultez le [Android WebView](http://docs.xamarin.com/recipes/android/controls/webview/) recettes pour plus d’informations sur l’utilisation du contrôle WebView.

### <a name="specifying-the-base-directory"></a>Spécification du répertoire de Base

Sur les deux plateformes, il existe un paramètre qui spécifie le répertoire de base pour la page HTML. Il s’agit de l’emplacement sur le système de fichiers de l’appareil est utilisé pour résoudre des références relatives aux ressources telles que des images et des fichiers CSS. Par exemple, les balises telles que

```html
<link rel="stylesheet" href="style.css" />
<img src="monkey.jpg" />
<script type="text/javascript" src="jscript.js">
```

reportez-vous à ces fichiers : **style.css**, **monkey.jpg** et **jscript.js**. Le paramètre de répertoire de base indique à la vue web où ces fichiers se trouvent afin qu’ils peuvent être chargés dans la page.

#### <a name="ios"></a>iOS

La sortie de modèle est restituée dans iOS par le code c# suivant :

```csharp
webView.LoadHtmlString (page, NSBundle.MainBundle.BundleUrl);
```

Le répertoire de base est spécifié en tant que `NSBundle.MainBundle.BundleUrl` qui fait référence à l’application est installée dans le répertoire. Tous les fichiers dans le **ressources** dossier sont copiés vers cet emplacement, tel que le **style.css** fichier présenté ici :

 ![solution d’iPhoneHybrid](images/image1_240x163.png)

L’Action de génération pour tous les fichiers de contenu statiques doit être **BundleResource**:

 ![action de génération de projet iOS : BundleResource](images/image2_250x131.png)

#### <a name="android"></a>Android

Android requiert également un répertoire de base à passer en tant que paramètre lorsque les chaînes html sont affichés dans une vue web.

```csharp
webView.LoadDataWithBaseURL("file:///android_asset/", page, "text/html", "UTF-8", null);
```

La chaîne spéciale **file:///android_asset/** fait référence au dossier des ressources Android dans votre application, illustré ici contenant le **style.css** fichier.

 ![Solution de AndroidHybrid](images/image3_240x167.png)

L’Action de génération pour tous les fichiers de contenu statiques doit être **AndroidAsset**.

 ![Action de génération de projet Android : AndroidAsset](images/image4_250x71.png)

### <a name="calling-c-from-html-and-javascript"></a>Appel C# à partir de HTML et JavaScript

Lorsqu’une page html est chargée dans un affichage web, il traite les liens et les formulaires comme il le ferait si la page a été chargée à partir d’un serveur. Cela signifie que si l’utilisateur clique sur un lien ou soumet un formulaire l’affichage web tente de naviguer vers la cible spécifiée.

Si le lien pointe vers un serveur externe (par exemple, google.com) l’affichage web tente de charger le site Web externe (en supposant qu’il existe une connexion internet).

```html
<a href="http://google.com/">Google</a>
```

Si le lien est relatif au mode web tente de charger ce contenu à partir du répertoire de base. Évidemment, aucune connexion réseau n’est requise pour ce faire, comme le contenu est stocké dans l’application sur l’appareil.

```html
<a href="somepage.html">Local content</a>
```

Actions de formulaire suivent la même règle.

```html
<form method="get" action="http://google.com/"></form>
<form method="get" action="somepage.html"></form>
```

Vous n’allez pas héberger un serveur web sur le client ; Toutefois, vous pouvez utiliser les mêmes techniques de communication de serveur utilisés dans les modèles de conception réactive aujourd'hui permet d’appeler des services via HTTP GET et gérer les réponses de façon asynchrone par émission de JavaScript (ou JavaScript appelant déjà hébergée sur la vue web). Cela vous permet de passer facilement des données du code HTML au code c# pour traitement, puis afficher que les résultats de la sauvegarde sur la page HTML.

IOS et Android fournissent un mécanisme pour le code d’application d’intercepter ces événements de navigation afin que le code d’application peut répondre (si nécessaire). Cette fonctionnalité est essentielle pour créer des applications hybrides, car elle permet en code natif pour interagir avec la vue web.

#### <a name="ios"></a>iOS

L’événement ShouldStartLoad sur la vue web dans iOS peut être substituée pour autoriser le code d’application gérer une requête de navigation (par exemple, un clic de lien). Les paramètres de méthode fournissent toutes les informations

```csharp
bool HandleShouldStartLoad (UIWebView webView, NSUrlRequest request, UIWebViewNavigationType navigationType) {
    // return true if handled in code
    // return false to let the web view follow the link
}
```

puis attribuez le Gestionnaire d’événements :

```csharp
webView.ShouldStartLoad += HandleShouldStartLoad;
```

#### <a name="android"></a>Android

Sur Android simplement sous-classe WebViewClient, puis implémenter de code pour répondre à la demande de navigation.

```csharp
class HybridWebViewClient : WebViewClient {
    public override bool ShouldOverrideUrlLoading (WebView webView, IWebResourceRequest request) {
        // return true if handled in code
        // return false to let the web view follow the link
    }
}
```

Paramétrez ensuite le client sur l’affichage web :

```csharp
webView.SetWebViewClient (new HybridWebViewClient ());
```

### <a name="calling-javascript-from-c"></a>Appel de JavaScript à partir deC#

En plus d’indiquer un affichage web pour charger une nouvelle page HTML, C# code peut également s’exécuter JavaScript au sein de la page actuellement affichée. Les blocs de code JavaScript entiers peuvent être créés à l’aide de C# des chaînes et exécutée, ou vous pouvez créer des appels de méthode JavaScript déjà disponible sur la page via `script` balises.

#### <a name="android"></a>Android

Créer le code JavaScript pour être exécutés et puis ajouter le préfixe « javascript : » et demander à l’affichage web pour charger cette chaîne :

```csharp
var js = "alert('test');";
webView.LoadUrl ("javascript:" + js);
```

#### <a name="ios"></a>iOS

les vues web iOS fournissent une méthode spécifiquement pour appeler JavaScript :

```csharp
var js = "alert('test');";
webView.EvaluateJavascript (js);
```

### <a name="summary"></a>Récapitulatif

Cette section a introduit les fonctionnalités des contrôles d’affichage web sur Android et iOS qui nous permettent de créer des applications hybrides avec Xamarin, notamment :

-  La capacité à charger HTML à partir de chaînes générés dans le code,
-  La capacité de référencer les fichiers locaux (CSS, JavaScript, Images ou autres fichiers HTML)
-  La possibilité d’intercepter les demandes de navigation dans le code c#,
-  La possibilité d’appeler JavaScript à partir de C# code.


La section suivante présente Razor, ce qui le rend facile de créer le code HTML à utiliser dans des applications hybrides.

## <a name="what-is-razor"></a>Nouveautés de Razor ?

Razor est un moteur de création de modèles qui a été introduit avec ASP.NET MVC, à l’origine pour s’exécuter sur le serveur et de générer du code HTML à servir aux navigateurs web.

Le moteur de création de modèles Razor étend la syntaxe HTML standard avec C# afin que vous pouvez exprimer la disposition et incorporer facilement des feuilles de style CSS et JavaScript. Le modèle peut faire référence à une classe de modèle, ce qui peut être tout type personnalisé et dont les propriétés sont accessibles directement à partir du modèle. Un de ses principaux avantages est la capacité à combiner facilement de syntaxe HTML et c#.

Modèles Razor ne sont pas limités à utilisation côté serveur, ils peuvent également être inclus dans les applications Xamarin. À l’aide de modèles Razor, ainsi que la possibilité de travailler par programmation avec des vues web permet d’applications sophistiquées inter-plateformes hybrides sont créées avec Xamarin.

### <a name="razor-template-basics"></a>Principes fondamentaux du modèle Razor

Fichiers modèles Razor ont une **.cshtml** extension de fichier. Ils peuvent être ajoutés à un projet Xamarin à partir de la section Création de modèles de texte dans le **nouveau fichier** boîte de dialogue :

 ![Nouveau fichier - modèle Razor](images/image5_400x201.png)

Un simple modèle Razor ( **RazorView.cshtml**) est indiqué ci-dessous.

```html
@model string
<html>
    <body>
    <h1>@Model</h1>
    </body>
</html>
```

Notez les différences suivantes à partir d’un fichier HTML normal :

-  Le `@` symbole a une signification spéciale dans les modèles Razor : il indique que l’expression suivante est c# doit être évaluée.
- `@model` la directive apparaît toujours comme la première ligne d’un fichier de modèle Razor.
-  Le `@model` directive doit être suivie d’un Type. Dans cet exemple une chaîne simple est passée au modèle, mais cela peut être n’importe quelle classe personnalisé.
-  Lorsque `@Model` est référencé dans le modèle, il fournit une référence à l’objet passé au modèle lorsqu’il est généré (dans cet exemple, il sera une chaîne).
-  L’IDE génère automatiquement une classe partielle pour les modèles (fichiers avec le **.cshtml** extension). Vous pouvez afficher ce code, mais il ne doit pas être modifié.
 ![RazorView.cshtml](images/image6_125x34.png) la classe partielle est nommée RazorView doit correspondre au nom de fichier de modèle .cshtml. C’est ce nom est utilisé pour faire référence au modèle en code c#.
- `@using` instructions peuvent également être incluses en haut d’un modèle Razor pour inclure des espaces de noms supplémentaires.


La sortie HTML finale peut ensuite être générée avec le code c# suivant. Notez que nous spécifions le modèle comme une chaîne « Hello World » qui sera incorporée dans la sortie de modèle de rendu.

```csharp
var template = new RazorView () { Model = "Hello World" };
var page = template.GenerateString ();
```

Voici la sortie affichée dans une vue web sur l’iOS Simulator et émulateur Android :

 ![Hello World](images/image7_523x135.png)

### <a name="more-razor-syntax"></a>Plus la syntaxe Razor

Dans cette section, que nous allons introduire certains syntaxe Razor de base pour vous aider à commencer à l’utiliser. Les exemples de cette section remplissent la classe suivante avec des données et l’affichent à l’aide de Razor :

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

Lorsque le modèle est une classe avec des propriétés, ils sont facilement référencés dans le modèle Razor comme indiqué dans cet exemple de modèle :

```html
@model Monkey
<html>
    <body>
    <h1>@Model.Name</h1>
    <p>Birthday: @(Model.Birthday.ToString("d MMMM yyyy"))</p>
    </body>
</html>
```

Cela peut être rendu en une chaîne en utilisant le code suivant :

```csharp
var template = new RazorView () { Model = animal };
var page = template.GenerateString ();
```

Le résultat final est illustré ici dans une vue web sur l’iOS Simulator et émulateur Android :

 ![RUPERT](images/image8_516x160.png)

#### <a name="c-statements"></a>Instructions c#

Plus complexe c# peut être inclus dans le modèle, telles que les mises à jour des propriétés de modèle et le calcul d’âge dans cet exemple :

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

Vous pouvez écrire une ligne c# des expressions complexes (par exemple, l’âge de la mise en forme) en mettant le code avec `@()`.

Plusieurs instructions c# peuvent être écrits en les entourant avec `@{}`.

#### <a name="if-else-statements"></a>Instructions if-else

Branches de code peuvent être exprimées avec `@if` comme indiqué dans cet exemple de modèle.

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

Bouclage des constructions, telles que `foreach` peuvent également être ajoutés. Le `@` préfixe peut être utilisé sur la variable de boucle ( `@food` dans ce cas) pour effectuer le rendu au format HTML.

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

La sortie du modèle ci-dessus est présentée en cours d’exécution sur iOS Simulator et émulateur Android :

 ![Rupert X Monkey](images/image9_520x277.png)

Cette section a couvert les principes fondamentaux de l’utilisation de modèles Razor pour effectuer le rendu des vues en lecture seule simples. La section suivante explique comment créer des applications plus complètes à l’aide de Razor qui peuvent accepter l’entrée d’utilisateur et interagir entre JavaScript dans la vue HTML et C#.

## <a name="using-razor-templates-with-xamarin"></a>À l’aide de modèles Razor avec Xamarin

Cette section explique comment utiliser build votre propre application hybride avec les modèles de solution dans Visual Studio pour Mac. Il existe trois modèles disponibles dans le **fichier > Nouveau > Solution...**  fenêtre :

- **Android > application > Application WebView Android**
- **iOS > application > Application WebView**
- **Projet ASP.NET MVC**



Le **nouvelle Solution** fenêtre ressemble à ceci pour iPhone et les projets Android - la description de la solution sur la droite met en évidence la prise en charge pour le moteur de création de modèles Razor.

 ![Création d’iPhone et Android solutions](images/image13_1139x959.png)

Notez que vous pouvez facilement ajouter un **.cshtml** modèle Razor à *n’importe quel* existant projet Xamarin, il n’est pas nécessaire d’utiliser ces modèles de solution. les projets iOS ne nécessitent pas une table de montage séquentiel à utiliser Razor soit ; Ajoutez simplement un contrôle UIWebView à n’importe quelle vue par programmation et vous pouvez afficher les modèles Razor entière en code c#.

Le contenu de solution de modèle par défaut pour iPhone et les projets Android est présenté ci-dessous :

 ![iPhone et Android modèles](images/image10_428x310.png)

Les modèles vous donnent l’infrastructure d’application de prêts à l’emploi pour charger un modèle Razor avec un objet de modèle de données, de traiter l’entrée d’utilisateur et de communiquer avec l’utilisateur via JavaScript.

Les parties importantes de la solution sont :

-  Contenu statique comme le **style.css** fichier.
-  Comme des fichiers modèles Razor .cshtml **RazorView.cshtml** .
-  Les classes qui sont référencés dans les modèles Razor comme modèle **ExampleModel.cs** .
-  La classe spécifique à la plateforme qui crée la vue web et restitue le modèle, telles que la `MainActivity` sur Android et le `iPhoneHybridViewController` sur iOS.


La section suivante explique comment les projets fonctionnent.

### <a name="static-content"></a>Contenu statique

Contenu statique inclut des feuilles de style CSS, images, fichiers JavaScript ou un autre contenu qui peut être lié à partir d’ou référencé par un fichier HTML affiché dans une vue web.

Les projets de modèle incluent une feuille de style minimal pour illustrer comment inclure du contenu statique dans votre application hybride. La feuille de style CSS est référencé dans le modèle comme suit :

```html
<link rel="stylesheet" href="style.css" />
```

Vous pouvez ajouter toute feuille de style et les fichiers JavaScript que vous avez besoin, y compris les infrastructures telles que JQuery.

### <a name="razor-cshtml-templates"></a>Cshtml de Razor modèles

Le modèle inclut un Razor **.cshtml** fichier qui a déjà écrit du code pour aider à communiquer des données entre le code HTML/JavaScript et C#. Ainsi, vous pouvez générer des applications hybrides sophistiqués qui ne simplement afficher les données en lecture seule à partir du modèle, mais également acceptent les entrées d’utilisateur dans le code HTML et passez-le au code c# pour le traitement ou de stockage.

#### <a name="rendering-the-template"></a>Le modèle de rendu

Appelant le `GenerateString` sur un modèle génère le rendu HTML prêt pour l’affichage dans une vue web. Si le modèle utilise un modèle qu’il doit être fourni avant le rendu. Ce diagramme illustre le rendu fonctionne – pas les ressources statiques sont résolus par la vue web lors de l’exécution, en utilisant le répertoire de base fourni pour rechercher les fichiers spécifiés.

 ![Organigramme de Razor](images/image12_700x421.png)

#### <a name="calling-c-code-from-the-template"></a>Appel de code c# à partir du modèle

Communication à partir d’une vue web rendu rappel c# s’effectue en définissant l’URL pour l’affichage web et l’interception puis la demande en c# pour gérer la demande native sans recharger l’affichage web.

Un exemple peut être constaté dans modalités de bouton de RazorView. Le bouton présente le code HTML suivant :

```html
<input type="button" name="UpdateLabel" value="Click" onclick="InvokeCSharpWithFormValues(this)" />
```

Le `InvokeCSharpWithFormValues` fonction JavaScript lit toutes les valeurs à partir du formulaire HTML et les jeux les `location.href` pour l’affichage web :

```javascript
location.href = "hybrid:" + elm.name + "?" + qs;
```

Il tente de naviguer dans l’affichage web à une URL avec un schéma personnalisé (par exemple). `hybrid:`)

```
hybrid:UpdateLabel?textbox=SomeValue&UpdateLabel=Click
```

Lorsque la vue web natifs traite cette demande de navigation, nous avons la possibilité d’intercepter. Dans iOS, cela en gérant HandleShouldStartLoad événement de l’UIWebView. Dans Android, nous avons simplement sous-classe la WebViewClient utilisé dans le formulaire et remplacer ShouldOverrideUrlLoading.

Les mécanismes internes de ces deux intercepteurs de navigation sont essentiellement la même.

Tout d’abord, vérifiez l’URL de l’affichage web tente de charger, et si elle ne commence pas par le schéma personnalisé (`hybrid:`), autoriser la navigation se produit comme d’habitude.

Pour le schéma d’URL personnalisé, tous les éléments de l’URL entre le schéma et le « ? » est le nom de la méthode d’être géré (dans ce cas, « UpdateLabel »). Tous les éléments de la chaîne de requête seront traitée comme paramètres à l’appel de méthode :

```csharp
var resources = url.Substring(scheme.Length).Split('?');
var method = resources [0];
var parameters = System.Web.HttpUtility.ParseQueryString(resources[1]);
```

`UpdateLabel` Dans cet exemple effectue une quantité minimale de manipulation de chaînes sur le paramètre de la zone de texte (ajoutant le préfixe « C# indique « à la chaîne) et rappelle ensuite à la vue web.

Après avoir géré l’URL, la méthode abandonne le volet de navigation afin que la vue web ne tente pas de terminer la navigation vers l’URL personnalisée.

#### <a name="manipulating-the-template-from-c"></a>Manipuler le modèle à partir de c#

Communication à un rendu HTML web à partir de C# s’effectue en appelant JavaScript dans l’affichage web. Sur iOS, cela s’effectue en appelant `EvaluateJavascript` sur le UIWebView :

```csharp
webView.EvaluateJavascript (js);
```

Sur Android, JavaScript peut être appelée dans l’affichage web en chargeant le code JavaScript comme une URL à l’aide de la `"javascript:"` schéma d’URL :

```csharp
webView.LoadUrl ("javascript:" + js);
```

## <a name="making-an-app-truly-hybrid"></a>Une application véritablement hybride

Ces modèles ne faites pas utiliser de contrôles natifs sur chaque plateforme : tout l’écran est rempli avec une vue web unique.

HTML peut être intéressante pour le prototypage, et affichage des types d’opérations web convient mieux à telles que le texte enrichi et la disposition dynamique. Cependant pas toutes les tâches sont adaptés au format HTML et JavaScript : le défilement de longues listes de données, par exemple, effectue à l’aide de contrôles d’interface utilisateur natives comme (UITableView sur iOS) ou de ListView sur Android.

Les vues web dans le modèle peuvent facilement être augmentées avec contrôles spécifiques à la plateforme : il vous suffit de modifier le **MainStoryboard.storyboard** dans le concepteur iOS ou **Resources/layout/Main.axml** sur Android.

### <a name="razortodo-sample"></a>RazorTodo Sample

Le [RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo) référentiel contient deux solutions distinctes pour afficher les différences entre une application entièrement basée sur HTML et une application qui combine HTML avec des contrôles natifs :

-  **RazorTodo** -application basée sur HTML complètement à l’aide de modèles Razor.
-  **RazorNativeTodo** : utilise les contrôles d’affichage de liste natif pour iOS et Android, mais affiche l’écran de modification avec HTML et Razor.


Ces applications Xamarin s’exécutent sur iOS et Android, en utilisant les bibliothèques de classes portables (PCL) de partager du code courants tels que les classes de base de données et le modèle. Razor **.cshtml** modèles peuvent également être inclus dans la bibliothèque PCL afin qu’ils sont facilement partagées entre les plateformes.

Les deux exemples d’applications basées sur Twitter partage et API synthèse vocale à partir de la plateforme native, démontrer qu’applications hybrides avec Xamarin ont toujours accès à toutes les fonctionnalités sous-jacentes à partir des vues de basés sur les modèles HTML Razor.

Le **RazorTodo** application utilise des modèles HTML Razor pour les affichages de listes et les modifier. Cela signifie que nous pouvons créer l’application presque entièrement dans une bibliothèque de classes Portable partagée (y compris la base de données et **.cshtml** modèles Razor). Les captures d’écran ci-dessous montrent les applications iOS et Android.

 ![RazorTodo](images/Both_700x290.png)

Le **RazorNativeTodo** application utilise un modèle HTML Razor pour le mode édition, mais implémente une zone de liste natif sur chaque plateforme. Cela fournit de nombreux avantages, notamment :

-  Performances - les contrôles de défilement natifs utilisent la virtualisation pour garantir le défilement rapide et sans heurts même avec très longues listes de données.
-  Expérience native - éléments d’interface utilisateur spécifiques à la plateforme sont facilement activés, telles que la prise en charge du index défilement rapide dans iOS et Android.


Des principaux avantages de la création d’applications hybrides avec Xamarin sont que vous pouvez démarrer avec une interface utilisateur entièrement piloté par HTML (par exemple, le premier exemple) et ajouter des fonctionnalités spécifiques à la plateforme à la demande (comme le deuxième exemple s’affiche). Les écrans de liste natif et HTML Razor modifier les écrans sur iOS et Android sont présentées ci-dessous.

 ![RazorNativeTodo](images/BothNative_700x290.png)

## <a name="summary"></a>Récapitulatif

Cet article a décrit les fonctionnalités des contrôles de vue web disponibles sur iOS et Android qui facilitent la création des applications hybrides.

Il décrit ensuite le moteur de création de modèles Razor et la syntaxe qui permet de générer facilement du code HTML dans les applications Xamarin à l’aide. **cshtml** des fichiers modèles Razor. Il décrit également le Visual Studio pour Mac, les modèles de solution qui vous permettent de rapidement commencer la création d’applications hybrides avec Xamarin.

Enfin, il introduit les exemples RazorTodo qui illustrent comment combiner des vues web avec les API et les interfaces utilisateur natives.

### <a name="related-links"></a>Liens associés

- [Exemple de RazorTodo](https://github.com/xamarin/mobile-samples/tree/master/RazorTodo)
- [MVC 3 - moteur d’affichage Razor (Microsoft)](http://www.asp.net/mvc/videos/mvc-3/mvc-3-razor-view-engine)
- [Introduction à la programmation Web ASP.NET à l’aide de la syntaxe Razor (Microsoft)](http://www.asp.net/web-pages/tutorials/basics/2-introduction-to-asp-net-web-programming-using-the-razor-syntax)
