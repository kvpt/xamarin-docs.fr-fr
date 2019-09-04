---
title: Xamarin.Forms WebView
description: Cet article explique comment utiliser la classe WebView de Xamarin.Forms pour présenter local ou réseau de contenu web et des documents pour les utilisateurs.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2019
ms.openlocfilehash: 9113ff728c382a26d0f0b5c0a6e40575cb8e776f
ms.sourcegitcommit: c9651cad80c2865bc628349d30e82721c01ddb4a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2019
ms.locfileid: "70227950"
---
# <a name="xamarinforms-webview"></a>Xamarin.Forms WebView

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) est une vue pour l’affichage web et le contenu HTML dans votre application. Contrairement aux `OpenUri`, qui dirige l’utilisateur vers le navigateur web sur l’appareil, `WebView` affiche le contenu HTML à l’intérieur de votre application.

![Dans l’Explorateur d’applications](webview-images/in-app-browser.png)

## <a name="content"></a>Contenu

`WebView` prend en charge les types de contenu suivants :

- Sites Web HTML et CSS &ndash; WebView a prise en charge complète pour les sites Web écrits à l’aide de HTML et CSS, y compris la prise en charge JavaScript.
- Documents &ndash; WebView étant implémenté à l’aide des composants natifs sur chaque plateforme, WebView est capable d’afficher des documents qui sont visibles sur chaque plateforme. Cela signifie que les fichiers PDF fonctionnent sur iOS et Android.
- Chaînes HTML &ndash; WebView peut afficher les chaînes de HTML à partir de la mémoire.
- Fichiers locaux &ndash; WebView peut présenter un des types de contenu ci-dessus incorporé dans l’application.

> [!NOTE]
> `WebView` sur Windows ne prend pas en charge Silverlight, Flash ou tous les contrôles ActiveX, même si elles sont prises en charge par Internet Explorer sur cette plateforme.

### <a name="websites"></a>Sites Web

Pour afficher un site Web à partir d’internet, définissez le `WebView`de [ `Source` ](xref:Xamarin.Forms.WebViewSource) propriété vers une URL de chaîne :

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URL doivent être entièrement formées avec le protocole spécifié (autrement dit, elle doit avoir « http:// » ou « https:// » ajouté à ce dernier).

#### <a name="ios-and-ats"></a>iOS et ATS

Depuis la version 9, iOS autorise uniquement l’application de communiquer avec les serveurs qui implémentent les meilleures pratiques sécurité par défaut. Valeurs doivent être définies `Info.plist` pour permettre la communication avec des serveurs non sécurisés.

> [!NOTE]
> Si votre application nécessite une connexion à un site Web non sécurisé, vous devez toujours Entrez le domaine une exception à l’aide `NSExceptionDomains` au lieu de la désactivation ATS complètement à l’aide de `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` doit uniquement être utilisé dans les situations d’urgence extrêmes.

Ce qui suit montre comment activer un domaine spécifique (dans ce cas xamarin.com) pour ignorer les exigences d’ATS :

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

Il est recommandé d’activer uniquement certains domaines de contourner ATS, ce qui vous permet d’utiliser des sites approuvés tout en bénéficiant de la sécurité supplémentaire sur des domaines non approuvés. Ce qui suit illustre la méthode la moins sécurisée de désactiver ATS pour l’application :

```xml
<key>NSAppTransportSecurity</key>
    <dict>
        <key>NSAllowsArbitraryLoads </key>
        <true/>
    </dict>
    ...
</key>
```

Consultez [App Transport Security](~/ios/app-fundamentals/ats.md) pour plus d’informations sur cette nouvelle fonctionnalité dans iOS 9.

### <a name="html-strings"></a>Chaînes HTML

Si vous souhaitez présenter une chaîne de code HTML défini dynamiquement dans le code, vous devez créer une instance de [ `HtmlWebViewSource` ](xref:Xamarin.Forms.HtmlWebViewSource):

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

Dans le code ci-dessus, `@` est utilisé pour marquer le code HTML en tant que chaîne littérale, ce qui signifie que tous les caractères d’échappement habituels sont ignorés.

> [!NOTE]
> Il peut être nécessaire de définir les `WidthRequest` propriétés `HeightRequest` et de [`WebView`](xref:Xamarin.Forms.WebView) pour afficher le contenu HTML, en fonction de la disposition du `WebView` est un enfant de. Par exemple, cela est requis dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Contenu HTML local

WebView peut afficher le contenu à partir de HTML, CSS et Javascript incorporés dans l’application. Exemple :

```html
<html>
  <head>
    <title>Xamarin Forms</title>
  </head>
  <body>
    <h1>Xamrin.Forms</h1>
    <p>This is an iOS web page.</p>
    <img src="XamarinLogo.png" />
  </body>
</html>
```

CSS :

```css
html,body {
  margin:0;
  padding:10;
}
body,p,h1 {
  font-family: Chalkduster;
}
```

Notez que les polices spécifiés dans le code CSS ci-dessus doivent être personnalisés pour chaque plateforme, comme pas chaque plateforme a les mêmes polices.

À l’affichage local contenu à l’aide un `WebView`, vous aurez besoin ouvrir le fichier HTML comme n’importe quel autre, puis charger le contenu sous forme de chaîne dans le `Html` propriété d’un `HtmlWebViewSource`. Pour plus d’informations sur l’ouverture de fichiers, consultez [utilisation des fichiers](~/xamarin-forms/data-cloud/data/files.md).

Les captures d’écran suivantes montrent le résultat de l’affichage du contenu local sur chaque plateforme :

![WebView affichant du contenu local](webview-images/local-content.png)

Bien que la première page a été chargée, le `WebView` n’a aucune connaissance de d'où provenance le code HTML. C’est un problème lors du traitement de pages qui référencent des ressources locales. Lorsque le lien local pages uns des autres, une page permet de recourir à un fichier JavaScript séparé, ou une page établit un lien vers une feuille de style CSS sont des exemples de lorsque cela peut se produire.  

Pour résoudre ce problème, vous devez indiquer le `WebView` où se trouvent les fichiers sur le système de fichiers. Faire en définissant le `BaseUrl` propriété sur le `HtmlWebViewSource` utilisé par le `WebView`.

Étant donné que le système de fichiers sur chacun des systèmes d’exploitation est différent, vous devez déterminer cette URL sur chaque plateforme. Xamarin.Forms expose le `DependencyService` pour la résolution des dépendances lors de l’exécution sur chaque plateforme.

Pour utiliser le `DependencyService`, tout d’abord définir une interface qui peut être implémentée sur chaque plateforme :

```csharp
public interface IBaseUrl { string Get(); }
```

Notez que jusqu'à ce que l’interface est implémentée sur chaque plateforme, l’application s’exécutera pas. Dans le projet common, assurez-vous que vous n’oubliez pas de définir la `BaseUrl` à l’aide de la `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Implémentations de l’interface pour chaque plateforme puis doivent être fournies.

#### <a name="ios"></a>iOS

Sur iOS, le contenu web doit se trouver dans le répertoire du projet racine ou **ressources** répertoire avec l’action de génération *BundleResource*, comme illustré ci-dessous :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Fichiers locaux sur iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers locaux sur iOS](webview-images/ios-xs.png)

-----

Le `BaseUrl` doit être défini sur le chemin d’accès du regroupement principal :

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

Sur Android, placez le HTML, CSS et images dans le dossier de ressources avec l’action de génération *AndroidAsset* comme illustré ci-dessous :

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![Fichiers locaux sur Android](webview-images/android-vs.png)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers locaux sur Android](webview-images/android-xs.png)

-----

Sur Android, le `BaseUrl` doit être défini sur `"file:///android_asset/"`:

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

Sur Android, les fichiers dans le **actifs** dossier est également accessible via le contexte actuel Android, ce qui est exposé par le `MainActivity.Instance` propriété :

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Sur les projets de plateforme universelle Windows (UWP), placez HTML, CSS et des images dans la racine du projet avec l’action de génération la valeur *contenu*.

Le `BaseUrl` doit être défini sur `"ms-appx-web:///"`:

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

WebView prend en charge la navigation via plusieurs méthodes et propriétés disponibles :

- **GoForward()** &ndash; si `CanGoForward` a la valeur true, l’appel `GoForward` accède à la page visitée suivante.
- **GoBack()** &ndash; si `CanGoBack` a la valeur true, l’appel `GoBack` permet d’accéder à la dernière page visitée.
- **CanGoBack** &ndash; `true` s’il existe des pages pour accéder à, `false` si le navigateur est à l’adresse de départ.
- **CanGoForward** &ndash; `true` si l’utilisateur a navigué vers l’arrière et peut accéder à une page qui a été déjà visitée.

Dans les pages, `WebView` ne prend pas en charge les entrées tactiles multipoints. Il est important pour vous assurer que le contenu est optimisé pour les mobiles et s’affiche sans avoir besoin du zoom.

Il est courant pour les applications afficher un lien dans un `WebView`, plutôt que le navigateur du périphérique. Dans ces situations, il est utile permettre la navigation normale, mais lors de l’accès utilisateur lorsqu’ils sont sur le lien de départ, l’application doit retourner à la vue de l’application normale.

Pour activer ce scénario, utilisez les méthodes de navigation intégrées et les propriétés.

Commencez par créer la page de la vue du navigateur :

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

C’est tout !

![Boutons de navigation WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Events

WebView déclenche les événements suivants pour vous aider à répondre aux modifications d’état :

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating): événement déclenché lorsque la WebView commence à charger une nouvelle page.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated): événement déclenché lorsque la page est chargée et que la navigation s’est arrêtée.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested): événement déclenché lors de la création d’une demande de rechargement du contenu actuel.

L' [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) objet qui accompagne l' [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) événement a quatre propriétés:

- `Cancel`: indique s’il faut ou non annuler la navigation.
- `NavigationEvent`: événement de navigation qui a été déclenché.
- `Source`: élément qui a effectué la navigation.
- `Url`: destination de navigation.

L' [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) objet qui accompagne l' [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) événement a quatre propriétés:

- `NavigationEvent`: événement de navigation qui a été déclenché.
- `Result`: décrit le résultat de la navigation, à l' [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) aide d’un membre de l’énumération. Les valeurs valides sont `Cancel`, `Failure`, `Success` et `Timeout`.
- `Source`: élément qui a effectué la navigation.
- `Url`: destination de navigation.

Si vous prévoyez d’utiliser des pages Web dont le chargement prend beaucoup de temps, envisagez [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) d’utiliser les [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) événements et pour implémenter un indicateur d’État. Par exemple :

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

Il en résulte dans la sortie suivante (chargement) :

![Exemple d’événement de navigation WebView](webview-images/loading-start.png)

Chargement terminé :

![Exemple d’événement de navigation WebView](webview-images/loading-end.png)

## <a name="reloading-content"></a>Rechargement du contenu

[`WebView`](xref:Xamarin.Forms.WebView) a un `Reload` méthode qui peut être utilisée pour recharger le contenu actuel :

```csharp
var webView = new WebView();
...
webView.Reload();
```

Lorsque le `Reload` méthode est appelée le `ReloadRequested` événement est déclenché, indiquant qu’une demande a été apportée à recharger le contenu actuel.

## <a name="performance"></a>Performances

Les navigateurs web les plus courants désormais adoptent les technologies telles que la compilation de JavaScript et de rendu à accélération matérielle. Sur iOS, par défaut, le Xamarin.Forms `WebView` est implémentée par le `UIWebView` classe et la plupart de ces technologies ne sont pas disponibles dans cette implémentation. Toutefois, une application peut participer à l’utilisation du iOS `WkWebView` classe pour implémenter le Xamarin.Forms `WebView`, qui prend en charge la navigation plus rapide. Cela est possible en ajoutant le code suivant à la **AssemblyInfo.cs** fichier dans le projet de plateforme iOS pour l’application :

```csharp
// Opt-in to using WkWebView instead of UIWebView.
[assembly: ExportRenderer(typeof(WebView), typeof(Xamarin.Forms.Platform.iOS.WkWebViewRenderer))]
```

> [!NOTE]
> Sur iOS, le `WkWebViewRenderer` a une surcharge de constructeur qui accepte `WkWebViewConfiguration` un argument. Cela permet de configurer le convertisseur à la création.

`WebView` sur Android par défaut est presque aussi rapide que le navigateur intégré.

Le [UWP WebView](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) utilise le moteur de rendu Microsoft Edge. Les appareils de bureau et des tablettes devraient s’afficher les mêmes performances en utilisant le navigateur Edge proprement dit.

## <a name="permissions"></a>Autorisations

Dans l’ordre pour `WebView` pour travailler, vous devez vérifier que les autorisations sont définies pour chaque plateforme. Notez que sur certaines plateformes, `WebView` fonctionne en mode débogage, mais ne pas lors de la génération de version. C’est parce que certaines autorisations, comme ceux utilisés pour l’accès à internet sur Android, sont définies par défaut par Visual Studio pour Mac en mode de débogage.

- **UWP** &ndash; nécessite la fonctionnalité Internet (Client & serveur) lors de l’affichage du contenu du réseau.
- **Android** &ndash; requiert `INTERNET` uniquement lors de l’affichage de contenu à partir du réseau. Le contenu local ne nécessite aucune autorisation spéciale.
- **iOS** &ndash; ne requiert aucune autorisation spéciale.

## <a name="layout"></a>Mise en page

Contrairement à la plupart des autres vues Xamarin.Forms `WebView` requiert que `HeightRequest` et `WidthRequest` sont spécifiés lorsque contenue dans StackLayout ou RelativeLayout. Si vous ne parvenez pas à spécifier ces propriétés, le `WebView` ne sera pas restitué.

Les exemples suivants montrent des dispositions de mener à un travail, rendu `WebView`%s :

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

DispositionAbsolue *sans* WidthRequest & HeightRequest :

```xaml
<AbsoluteLayout>
    <Label Text="test" AbsoluteLayout.LayoutBounds="0,0,100,100" />
    <WebView Source="http://www.xamarin.com/"
      AbsoluteLayout.LayoutBounds="0,150,500,500" />
</AbsoluteLayout>
```

Grille *sans* WidthRequest & HeightRequest. Grille est une des dispositions quelques qui ne nécessite pas de spécification hauteurs demandés et les largeurs. :

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

## <a name="invoking-javascript"></a>Appel de JavaScript

[`WebView`](xref:Xamarin.Forms.WebView) inclut la possibilité d’appeler une fonction JavaScript à partir de C#et retourner des résultats à l’appeler C# code. Pour cela, avec la [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) (méthode), qui est indiqué dans l’exemple suivant tiré le [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) exemple :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

Le [ `WebView.EvaluateJavaScriptAsync` ](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) méthode évalue le code JavaScript qui est spécifié comme argument et retourne un résultat comme un `string`. Dans cet exemple, le `factorial` fonction JavaScript est appelée, qui retourne la factorielle de `number` en conséquence. Cet fonction est définie dans le code HTML local de JavaScript de fichiers qui le [ `WebView` ](xref:Xamarin.Forms.WebView) charge et est illustré dans l’exemple suivant :

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

## <a name="related-links"></a>Liens associés

- [Utilisation de WebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
