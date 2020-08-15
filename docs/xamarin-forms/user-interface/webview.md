---
title: Xamarin.Forms WebView
description: Cet article explique comment utiliser la Xamarin.Forms classe WebView pour présenter des documents et du contenu Web locaux ou réseau aux utilisateurs.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e0fbdc91c2661732e3afdbde44ce9891f89c5f62
ms.sourcegitcommit: 14d67a2db82e67471584b1749e0d5b9ec0c0c09b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88228636"
---
# <a name="no-locxamarinforms-webview"></a>Xamarin.Forms WebView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) est une vue permettant d’afficher du contenu Web et HTML dans votre application :

![Dans l’Explorateur d’applications](webview-images/in-app-browser.png)

## <a name="content"></a>Contenu

`WebView` prend en charge les types de contenu suivants :

- La &ndash; prise en charge des sites Web html & CSS WebView est complète pour les sites Web écrits en html & CSS, y compris la prise en charge de JavaScript.
- Documents &ndash; étant donné que WebView est implémenté à l’aide de composants natifs sur chaque plateforme, WebView est capable d’afficher des documents dans les formats pris en charge par la plateforme sous-jacente.
- Les chaînes HTML &ndash; WebView peuvent afficher des chaînes HTML à partir de la mémoire.
- La WebView des fichiers locaux &ndash; peut présenter l’un des types de contenu ci-dessus incorporés dans l’application.

> [!NOTE]
> `WebView` sur Windows ne prend pas en charge Silverlight, Flash ou les contrôles ActiveX, même s’ils sont pris en charge par Internet Explorer sur cette plateforme.

### <a name="websites"></a>Sites web

Pour afficher un site Web à partir d’Internet, définissez la `WebView` [`Source`](xref:Xamarin.Forms.WebViewSource) propriété de sur une URL de chaîne :

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> Les URL doivent être entièrement formées avec le protocole spécifié (par exemple, « http:// » ou « https:// » doivent être ajoutés).

#### <a name="ios-and-ats"></a>iOS et ATS

Depuis la version 9, iOS permet uniquement à votre application de communiquer avec les serveurs qui implémentent par défaut la sécurité des meilleures pratiques. Les valeurs doivent être définies dans `Info.plist` pour permettre la communication avec les serveurs non sécurisés.

> [!NOTE]
> Si votre application nécessite une connexion à un site Web non sécurisé, vous devez toujours entrer le domaine en tant qu’exception `NSExceptionDomains` au lieu de désactiver complètement l’ATS à l’aide de `NSAllowsArbitraryLoads` . `NSAllowsArbitraryLoads` ne doit être utilisé qu’en cas d’urgence extrême.

L’exemple suivant montre comment activer un domaine spécifique (dans ce cas xamarin.com) pour ignorer les exigences de l’ATS :

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSExceptionDomains</key>
        <dict>
            <key>xamarin.com</key>
            <dict>
                <key>NSIncludesSubdomains</key>
                <true/>
                <key>NSTemporaryExceptionAllowsInsecureHTTPLoads</key>
                <true/>
                <key>NSTemporaryExceptionMinimumTLSVersion</key>
                <string>TLSv1.1</string>
            </dict>
        </dict>
    </dict>
    ...
</key>
```

Il est recommandé d’autoriser uniquement certains domaines à contourner l’ATS, ce qui vous permet d’utiliser des sites de confiance tout en bénéficiant de la sécurité supplémentaire sur les domaines non approuvés. L’exemple suivant illustre la méthode la moins sécurisée pour désactiver ATS pour l’application :

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Pour plus d’informations sur cette nouvelle fonctionnalité d’iOS 9, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md) .

### <a name="html-strings"></a>Chaînes HTML

Si vous souhaitez présenter une chaîne de code HTML définie dynamiquement dans le code, vous devez créer une instance de [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource) :

```csharp
var browser = new WebView();
var htmlSource = new HtmlWebViewSource();
htmlSource.Html = @"<html><body>
  <h1>Xamarin.Forms</h1>
  <p>Welcome to WebView.</p>
  </body></html>";
browser.Source = htmlSource;
```

![WebView affichant une chaîne HTML](webview-images/html-string.png)

Dans le code ci-dessus, `@` est utilisé pour marquer le code html comme [littéral de chaîne Verbatim](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals), ce qui signifie que la plupart des caractères d’échappement sont ignorés.

> [!NOTE]
> Il peut être nécessaire de définir les `WidthRequest` `HeightRequest` Propriétés et de [`WebView`](xref:Xamarin.Forms.WebView) pour afficher le contenu HTML, en fonction de la disposition du `WebView` est un enfant de. Par exemple, cela est requis dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout) .

### <a name="local-html-content"></a>Contenu HTML local

WebView peut afficher du contenu à partir de code HTML, CSS et JavaScript incorporé dans l’application. Par exemple :

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamarin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

FORMAT

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Notez que les polices spécifiées dans le code CSS ci-dessus doivent être personnalisées pour chaque plateforme, car toutes les plateformes ne possèdent pas les mêmes polices.

Pour afficher le contenu local à l’aide d’un `WebView` , vous devez ouvrir le fichier html comme n’importe quel autre, puis charger le contenu sous la forme d’une chaîne dans la `Html` propriété d’un `HtmlWebViewSource` . Pour plus d’informations sur l’ouverture des fichiers, consultez [utilisation des fichiers](~/xamarin-forms/data-cloud/data/files.md).

Les captures d’écran suivantes montrent le résultat de l’affichage du contenu local sur chaque plateforme :

![WebView affichant du contenu local](webview-images/local-content.png)

Bien que la première page ait été chargée, le `WebView` n’a aucune connaissance de l’origine du code html. Il s’agit d’un problème lors du traitement des pages qui font référence à des ressources locales. Voici des exemples de cas où cela peut se produire lorsque des pages locales sont liées les unes aux autres, qu’une page utilise un fichier JavaScript distinct ou qu’une page est liée à une feuille de style CSS.  

Pour résoudre ce cas, vous devez indiquer `WebView` où trouver les fichiers sur le système de fichiers. Pour ce faire, définissez la `BaseUrl` propriété sur le `HtmlWebViewSource` utilisé par le `WebView` .

Étant donné que le système de fichiers sur chaque système d’exploitation est différent, vous devez déterminer cette URL sur chaque plateforme. Xamarin.Forms expose le `DependencyService` pour la résolution des dépendances au moment de l’exécution sur chaque plateforme.

Pour utiliser le `DependencyService` , définissez d’abord une interface qui peut être implémentée sur chaque plateforme :

```csharp
public interface IBaseUrl { string Get(); }
```

Notez que jusqu’à ce que l’interface soit implémentée sur chaque plateforme, l’application ne s’exécute pas. Dans le projet commun, assurez-vous de n’oubliez pas de définir à l' `BaseUrl` aide du `DependencyService` :

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Les implémentations de l’interface pour chaque plateforme doivent ensuite être fournies.

#### <a name="ios"></a>iOS

Sur iOS, le contenu Web doit se trouver dans le répertoire racine ou le répertoire de **ressources** du projet à l’aide de l’action de génération *BundleResource*, comme illustré ci-dessous :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Fichiers locaux sur iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers locaux sur iOS](webview-images/ios-xs.png)

-----

`BaseUrl`Doit être défini sur le chemin d’accès du bundle principal :

```csharp
[assembly: Dependency (typeof (BaseUrl_iOS))]
namespace WorkingWithWebview.iOS
{
  public class BaseUrl_iOS : IBaseUrl
  {
    public string Get()
    {
      return NSBundle.MainBundle.BundlePath;
    }
  }
}
```

#### <a name="android"></a>Android

Sur Android, placez les images HTML, CSS et dans le dossier composants avec l’action de génération *AndroidAsset* , comme illustré ci-dessous :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Fichiers locaux sur Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers locaux sur Android](webview-images/android-xs.png)

-----

Sur Android, `BaseUrl` doit avoir la valeur `"file:///android_asset/"` suivante :

```csharp
[assembly: Dependency (typeof(BaseUrl_Android))]
namespace WorkingWithWebview.Android
{
  public class BaseUrl_Android : IBaseUrl
  {
    public string Get()
    {
      return "file:///android_asset/";
    }
  }
}
```

Sur Android, les fichiers du dossier **ressources** sont également accessibles via le contexte Android actuel, qui est exposé par la `MainActivity.Instance` propriété :

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Sur les projets plateforme Windows universelle (UWP), placez les images HTML, CSS et dans la racine du projet avec l’action de génération définie sur *contenu*.

`BaseUrl`Doit avoir la valeur `"ms-appx-web:///"` :

```csharp
[assembly: Dependency(typeof(BaseUrl))]
namespace WorkingWithWebview.UWP
{
    public class BaseUrl : IBaseUrl
    {
        public string Get()
        {
            return "ms-appx-web:///";
        }
    }
}
```

## <a name="navigation"></a>Navigation

WebView prend en charge la navigation parmi plusieurs méthodes et propriétés qu’il rend disponibles :

- **GoForward ()** &ndash; Si `CanGoForward` a la valeur true, `GoForward` l’appel de accède à la page visitée suivante.
- **GoBack ()** &ndash; Si `CanGoBack` a la valeur true, `GoBack` l’appel à accède à la dernière page visitée.
- **CanGoBack** &ndash; `true` s’il existe des pages vers lesquelles naviguer en arrière, `false` si le navigateur est à l’URL de départ.
- **CanGoForward** &ndash; `true` si l’utilisateur a navigué vers l’arrière et peut avancer jusqu’à une page déjà visitée.

Dans les pages, `WebView` ne prend pas en charge les gestes tactiles multiples. Il est important de s’assurer que le contenu est optimisé pour les appareils mobiles et qu’il apparaît sans nécessiter de zoom.

Il est courant pour les applications d’afficher un lien dans un `WebView` , plutôt que dans le navigateur de l’appareil. Dans ces situations, il est utile d’autoriser la navigation normale, mais lorsque l’utilisateur accède à nouveau pendant qu’il se trouve sur le lien de départ, l’application doit revenir à la vue d’application normale.

Utilisez les méthodes et les propriétés de navigation intégrées pour activer ce scénario.

Commencez par créer la page pour la vue du navigateur :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.InAppBrowserXaml"
             Title="Browser">
    <StackLayout Margin="20">
        <StackLayout Orientation="Horizontal">
            <Button Text="Back" HorizontalOptions="StartAndExpand" Clicked="OnBackButtonClicked" />
            <Button Text="Forward" HorizontalOptions="EndAndExpand" Clicked="OnForwardButtonClicked" />
        </StackLayout>
        <!-- WebView needs to be given height and width request within layouts to render. -->
        <WebView x:Name="webView" WidthRequest="1000" HeightRequest="1000" />
    </StackLayout>
</ContentPage>
```

Dans le code-behind :

```csharp
public partial class InAppBrowserXaml : ContentPage
{
    public InAppBrowserXaml(string URL)
    {
        InitializeComponent();
        webView.Source = URL;
    }

    async void OnBackButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoBack)
        {
            webView.GoBack();
        }
        else
        {
            await Navigation.PopAsync();
        }
    }

    void OnForwardButtonClicked(object sender, EventArgs e)
    {
        if (webView.CanGoForward)
        {
            webView.GoForward();
        }
    }
}
```

C’est tout !

![Boutons de navigation WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Événements

WebView génère les événements suivants pour vous aider à répondre aux changements d’État :

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) : événement déclenché lorsque la WebView commence à charger une nouvelle page.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) : événement déclenché lorsque la page est chargée et que la navigation s’est arrêtée.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) : événement déclenché lors de la création d’une demande de rechargement du contenu actuel.

L' [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) objet qui accompagne l' [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) événement a quatre propriétés :

- `Cancel` : indique s’il faut ou non annuler la navigation.
- `NavigationEvent` : événement de navigation qui a été déclenché.
- `Source` : élément qui a effectué la navigation.
- `Url` : destination de navigation.

L' [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) objet qui accompagne l' [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) événement a quatre propriétés :

- `NavigationEvent` : événement de navigation qui a été déclenché.
- `Result` : décrit le résultat de la navigation, à l’aide d’un membre de l' [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) énumération. Les valeurs valides sont `Cancel`, `Failure`, `Success` et `Timeout`.
- `Source` : élément qui a effectué la navigation.
- `Url` : destination de navigation.

Si vous prévoyez d’utiliser des pages Web dont le chargement prend beaucoup de temps, envisagez d’utiliser les [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) événements et pour implémenter un indicateur d’État. Par exemple :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="WebViewSample.LoadingLabelXaml"
             Title="Loading Demo">
    <StackLayout>
        <!--Loading label should not render by default.-->
        <Label x:Name="labelLoading" Text="Loading..." IsVisible="false" />
        <WebView HeightRequest="1000" WidthRequest="1000" Source="http://www.xamarin.com" Navigated="webviewNavigated" Navigating="webviewNavigating" />
    </StackLayout>
</ContentPage>
```

Les deux gestionnaires d’événements :

```csharp
void webviewNavigating(object sender, WebNavigatingEventArgs e)
{
    labelLoading.IsVisible = true;
}

void webviewNavigated(object sender, WebNavigatedEventArgs e)
{
    labelLoading.IsVisible = false;
}
```

Cela entraîne la sortie suivante (chargement) :

![Exemple d’événement de navigation WebView](webview-images/loading-start.png)

Fin du chargement :

![Exemple d’événement de navigation WebView](webview-images/loading-end.png)

## <a name="reloading-content"></a>Rechargement du contenu

[`WebView`](xref:Xamarin.Forms.WebView) a une `Reload` méthode qui peut être utilisée pour recharger le contenu actuel :

```csharp
var webView = new WebView();
...
webView.Reload();
```

Lorsque la `Reload` méthode est appelée, l' `ReloadRequested` événement est déclenché, indiquant qu’une demande a été effectuée pour recharger le contenu actuel.

## <a name="performance"></a>Performances

Les navigateurs Web populaires adoptent des technologies telles que le rendu à accélération matérielle et la compilation JavaScript. Avant le Xamarin.Forms 4,4, le Xamarin.Forms `WebView` a été implémenté sur iOS par la `UIWebView` classe. Toutefois, un grand nombre de ces technologies n’étaient pas disponibles dans cette implémentation. Par conséquent, depuis Xamarin.Forms 4,4, le Xamarin.Forms `WebView` est implémenté sur iOS par la `WkWebView` classe, qui prend en charge la navigation plus rapide.

> [!NOTE]
> Sur iOS, le `WkWebViewRenderer` a une surcharge de constructeur qui accepte un `WkWebViewConfiguration` argument. Cela permet de configurer le convertisseur à la création.

Une application peut revenir à l’utilisation de la `UIWebView` classe IOS pour implémenter le Xamarin.Forms `WebView` , pour des raisons de compatibilité. Pour ce faire, vous pouvez ajouter le code suivant au fichier **AssemblyInfo.cs** dans le projet de plateforme IOS pour l’application :

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView` sur Android, par défaut, est environ aussi rapide que le navigateur intégré.

La [WebView UWP](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) utilise le moteur de rendu Microsoft Edge. Les appareils de bureau et tablettes doivent avoir les mêmes performances que l’utilisation du navigateur Edge lui-même.

## <a name="permissions"></a>Autorisations

Pour que `WebView` fonctionne, vous devez vous assurer que les autorisations sont définies pour chaque plateforme. Notez que sur certaines plateformes, `WebView` fonctionnera en mode débogage, mais pas en cas de génération pour la version finale. Cela est dû au fait que certaines autorisations, comme celles pour l’accès Internet sur Android, sont définies par défaut par Visual Studio pour Mac en mode débogage.

- **UWP** &ndash; nécessite la fonctionnalité Internet (client & Server) lors de l’affichage du contenu réseau.
- **Android** &ndash; nécessite `INTERNET`  uniquement lors de l’affichage du contenu à partir du réseau. Le contenu local ne requiert pas d’autorisations spéciales.
- **iOS** &ndash; ne requiert pas d’autorisations spéciales.

## <a name="layout"></a>Layout

Contrairement à la plupart des autres Xamarin.Forms vues, `WebView` exige que `HeightRequest` et `WidthRequest` soient spécifiés lorsqu’ils sont contenus dans StackLayout ou RelativeLayout. Si vous ne parvenez pas à spécifier ces propriétés, le `WebView` n’est pas restitué.

Les exemples suivants illustrent des dispositions qui aboutissent à un travail, rendu `WebView` s :

StackLayout avec WidthRequest & HeightRequest :

```xaml
<StackLayout>
    <Label Text="test" />
    <WebView Source="http://www.xamarin.com/"
        HeightRequest="1000"
        WidthRequest="1000" />
</StackLayout>
```

RelativeLayout avec WidthRequest & HeightRequest :

```xaml
<RelativeLayout>
    <Label Text="test"
        RelativeLayout.XConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=10}"
        RelativeLayout.YConstraint= "{ConstraintExpression
                                      Type=Constant, Constant=20}" />
    <WebView Source="http://www.xamarin.com/"
        RelativeLayout.XConstraint="{ConstraintExpression Type=Constant,
                                     Constant=10}"
        RelativeLayout.YConstraint="{ConstraintExpression Type=Constant,
                                     Constant=50}"
        WidthRequest="1000" HeightRequest="1000" />
</RelativeLayout>
```

AbsoluteLayout *sans* WidthRequest & HeightRequest :

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Grid *sans* WidthRequest & HeightRequest. La grille est l’une des quelques dispositions qui ne nécessitent pas la spécification des hauteurs et des largeurs demandés. :

```xaml
<Grid>
    <Grid.RowDefinitions>
        <RowDefinition Height="100" />
        <RowDefinition Height="*" />
    </Grid.RowDefinitions>
    <Label Text="test" Grid.Row="0" />
    <WebView Source="http://www.xamarin.com/" Grid.Row="1" />
</Grid>
```

## <a name="invoking-javascript"></a>Appeler JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) offre la possibilité d’appeler une fonction JavaScript à partir de C# et de retourner tout résultat au code C# appelant. Pour ce faire, vous devez utiliser la [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) méthode, qui est illustrée dans l’exemple suivant de l’exemple [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

La [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) méthode évalue le JavaScript qui est spécifié comme argument et retourne tout résultat sous la forme d’un `string` . Dans cet exemple, la `factorial` fonction JavaScript est appelée, ce qui retourne la factorielle de `number` en conséquence. Cette fonction JavaScript est définie dans le fichier HTML local que le [`WebView`](xref:Xamarin.Forms.WebView) charge, et est illustrée dans l’exemple suivant :

```html
<html>
<body>
<script type="text/javascript">
function factorial(num) {
        if (num === 0 || num === 1)
            return 1;
        for (var i = num - 1; i >= 1; i--) {
            num *= i;
        }
        return num;
}
</script>
</body>
</html>
```

## <a name="cookies"></a>Cookies

Les cookies peuvent être définis sur un [`WebView`](xref:Xamarin.Forms.WebView) , qui sont ensuite envoyés avec la demande Web à l’URL spécifiée. Pour ce faire `Cookie` , vous devez ajouter des objets à un `CookieContainer` , qui est ensuite défini comme valeur de la `WebView.Cookies` propriété pouvant être liée. Le code suivant en est un exemple :

```csharp
using System.Net;
using Xamarin.Forms;
// ...

CookieContainer cookieContainer = new CookieContainer();
Uri uri = new Uri("https://dotnet.microsoft.com/apps/xamarin", UriKind.RelativeOrAbsolute);

Cookie cookie = new Cookie
{
    Name = "XamarinCookie",
    Expires = DateTime.Now.AddDays(1),
    Value = "My cookie",
    Domain = uri.Host,
    Path = "/"
};
cookieContainer.Add(uri, cookie);
webView.Cookies = cookieContainer;
webView.Source = new UrlWebViewSource { Url = uri.ToString() };
```

Dans cet exemple, un seul `Cookie` est ajouté à l' `CookieContainer` objet, qui est ensuite défini comme valeur de la `WebView.Cookies` propriété. Lorsque  [`WebView`](xref:Xamarin.Forms.WebView) envoie une requête Web à l’URL spécifiée, le cookie est envoyé avec la demande.

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>Désapprobation UIWebView et rejet de l’App Store (ITMS-90809)

À compter du 2020 avril, [Apple rejette les applications](https://developer.apple.com/news/?id=12232019b) qui utilisent toujours l’API déconseillée `UIWebView` . Si Xamarin.Forms a été remplacé `WKWebView` par par défaut, il existe toujours une référence à l’ancien SDK dans les Xamarin.Forms fichiers binaires. Le comportement actuel de l' [éditeur de liens iOS](~/ios/deploy-test/linker.md) ne supprime pas cela et, par conséquent, l’API déconseillée `UIWebView` semblera toujours être référencée à partir de votre application lorsque vous la soumettez à l’App Store.

Une version préliminaire de l’éditeur de liens est disponible pour résoudre ce problème. Pour activer la préversion, vous devrez fournir un argument supplémentaire `--optimize=experimental-xforms-product-type` à l’éditeur de liens.

Les conditions préalables à ce fonctionnement sont les suivantes :

- ** Xamarin.Forms 4,5 ou version ultérieure**. Xamarin.Forms 4,6, ou une version ultérieure, est requis si votre application utilise un élément visuel Material.
- **Xamarin. iOS 13.10.0.17 ou version ultérieure**. Vérifiez votre version de Xamarin. iOS [dans Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#version-information). Cette version de Xamarin. iOS est incluse avec Visual Studio pour Mac 8.4.1 et Visual Studio 16.4.3.
- **Supprimez les `UIWebView` références à **. Votre code ne doit pas contenir de références à `UIWebView` ou à des classes qui utilisent `UIWebView` .

Pour plus d’informations sur la détection et la suppression de `UIWebView` références, consultez [dépréciation de UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation).

### <a name="configure-the-linker"></a>Configurer l’éditeur de liens

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Pour supprimer des références, procédez comme suit pour l’éditeur de liens `UIWebView` :

1. **Ouvrir les propriétés** &ndash; d’un projet iOS Cliquez avec le bouton droit sur votre projet iOS, puis choisissez **Propriétés**.
1. **Accéder à la section** &ndash; de génération iOS Sélectionnez la section **Build iOS** .
1. **Mettre à jour les arguments** &ndash; mTouch supplémentaires Dans les **arguments mTouch supplémentaires** , ajoutez cet indicateur `--optimize=experimental-xforms-product-type` (en plus de toute valeur qui peut déjà y figurer). Remarque : cet indicateur fonctionne conjointement avec le **comportement** de l’éditeur de liens défini sur **Kit de développement logiciel (SDK) uniquement** ou **lier tout**. Si, pour une raison quelconque, vous voyez des erreurs lors de la définition du comportement de l’éditeur de liens sur tout, il s’agit probablement d’un problème dans le code de l’application ou dans une bibliothèque tierce qui n’est pas sécurisée pour l’éditeur de liens. Pour plus d’informations sur l’éditeur de liens, consultez [liaison d’applications Xamarin. iOS](~/ios/deploy-test/linker.md).
1. **Mettre à jour toutes les configurations** &ndash; de build Utilisez les listes **configuration** et **plateforme** en haut de la fenêtre pour mettre à jour toutes les configurations de Build. La configuration la plus importante à mettre à jour est la configuration de la **version ou** de l’iPhone, car elle est généralement utilisée pour créer des builds pour l’envoi de l’App Store.

Vous pouvez voir la fenêtre avec le nouvel indicateur en place dans cette capture d’écran :

[![Définition de l’indicateur dans la section de génération iOS](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Pour supprimer des références, procédez comme suit pour l’éditeur de liens `UIWebView` :

1. **Ouvrir les options** &ndash; de projet iOS Cliquez avec le bouton droit sur votre projet iOS, puis choisissez **options**.
1. **Accéder à la section** &ndash; de génération iOS Sélectionnez la section **Build iOS** .
1. **Mettez à jour les arguments _mTouch_ supplémentaires** &ndash; dans les **arguments _mTouch_ supplémentaires** ajouter cet indicateur `--optimize=experimental-xforms-product-type` (en plus de toute valeur qui peut déjà y figurer). Remarque : cet indicateur fonctionne conjointement avec le **comportement** de l’éditeur de liens défini sur **Kit de développement logiciel (SDK) uniquement** ou **lier tout**. Si, pour une raison quelconque, vous voyez des erreurs lors de la définition du comportement de l’éditeur de liens sur tout, il s’agit probablement d’un problème dans le code de l’application ou dans une bibliothèque tierce qui n’est pas sécurisée pour l’éditeur de liens. Pour plus d’informations sur l’éditeur de liens, consultez [liaison d’applications Xamarin. iOS](~/ios/deploy-test/linker.md).
1. **Mettre à jour toutes les configurations** &ndash; de build Utilisez les listes **configuration** et **plateforme** en haut de la fenêtre pour mettre à jour toutes les configurations de Build. La configuration la plus importante à mettre à jour est la configuration de la **version ou** de l’iPhone, car elle est généralement utilisée pour créer des builds pour l’envoi de l’App Store.

Vous pouvez voir la fenêtre avec le nouvel indicateur en place dans cette capture d’écran :

[![Définition de l’indicateur dans la section de génération iOS](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

Désormais, lorsque vous créez une nouvelle build (version Release) et l’envoyez à l’App Store, aucun avertissement ne doit s’poser sur l’API déconseillée.

## <a name="related-links"></a>Liens associés

- [Utilisation de WebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
- [Désapprobation UIWebView](~/ios/user-interface/controls/webview.md#uiwebview-deprecation)
