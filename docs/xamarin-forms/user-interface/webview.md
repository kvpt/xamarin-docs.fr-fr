---
title: Xamarin.Forms WebView
description: Cet article explique comment utiliser la classe WebView de Xamarin.Forms pour présenter local ou réseau de contenu web et des documents pour les utilisateurs.
ms.prod: xamarin
ms.assetid: E44F5D0F-DB8E-46C7-8789-114F1652A6C5
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/04/2019
ms.openlocfilehash: 26fbe6af639c67a94408605ba456bb3a100d2355
ms.sourcegitcommit: 3d39bafe4c56b15cbb695b1f7f02b926e1033f58
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78292975"
---
# <a name="xamarinforms-webview"></a>Xamarin.Forms WebView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)

[`WebView`](xref:Xamarin.Forms.WebView) est une vue permettant d’afficher du contenu Web et HTML dans votre application :

![Dans l’Explorateur d’applications](webview-images/in-app-browser.png)

## <a name="content"></a>Contenu

`WebView` prend en charge les types de contenu suivants :

- Les sites Web HTML & CSS &ndash; WebView prennent entièrement en charge les sites Web écrits à l’aide de HTML & CSS, y compris la prise en charge de JavaScript.
- Les documents &ndash;, car WebView est implémenté à l’aide de composants natifs sur chaque plateforme, WebView est capable d’afficher des documents affichables sur chaque plateforme. Cela signifie que les fichiers PDF fonctionnent sur iOS et Android.
- Les chaînes HTML &ndash; WebView peuvent afficher des chaînes HTML à partir de la mémoire.
- Les fichiers locaux &ndash; WebView peuvent présenter l’un des types de contenu ci-dessus incorporés dans l’application.

> [!NOTE]
> `WebView` sur Windows ne prend pas en charge Silverlight, Flash ou des contrôles ActiveX, même s’ils sont pris en charge par Internet Explorer sur cette plateforme.

### <a name="websites"></a>Sites web

Pour afficher un site Web à partir d’Internet, définissez la propriété [`Source`](xref:Xamarin.Forms.WebViewSource) de l' `WebView`sur une URL de chaîne :

```csharp
var browser = new WebView
{
  Source = "http://xamarin.com"
};
```

> [!NOTE]
> URL doivent être entièrement formées avec le protocole spécifié (autrement dit, elle doit avoir « http:// » ou « https:// » ajouté à ce dernier).

#### <a name="ios-and-ats"></a>iOS et ATS

Depuis la version 9, iOS autorise uniquement l’application de communiquer avec les serveurs qui implémentent les meilleures pratiques sécurité par défaut. Les valeurs doivent être définies dans `Info.plist` pour permettre la communication avec les serveurs non sécurisés.

> [!NOTE]
> Si votre application nécessite une connexion à un site Web non sécurisé, vous devez toujours entrer le domaine en tant qu’exception à l’aide de `NSExceptionDomains` au lieu de désactiver complètement l’ATS à l’aide de `NSAllowsArbitraryLoads`. `NSAllowsArbitraryLoads` ne doit être utilisé qu’en cas d’urgence extrême.

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

Pour plus d’informations sur cette nouvelle fonctionnalité d’iOS 9, consultez [sécurité du transport d’application](~/ios/app-fundamentals/ats.md) .

### <a name="html-strings"></a>Chaînes HTML

Si vous souhaitez présenter une chaîne de code HTML définie dynamiquement dans le code, vous devez créer une instance de [`HtmlWebViewSource`](xref:Xamarin.Forms.HtmlWebViewSource):

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

Dans le code ci-dessus, `@` est utilisé pour marquer le code HTML comme [littéral de chaîne textuelle](/dotnet/csharp/programming-guide/strings/#regular-and-verbatim-string-literals), ce qui signifie que la plupart des caractères d’échappement sont ignorés.

> [!NOTE]
> Il peut être nécessaire de définir les propriétés `WidthRequest` et `HeightRequest` du [`WebView`](xref:Xamarin.Forms.WebView) pour afficher le contenu HTML, en fonction de la disposition du `WebView` est un enfant de. Par exemple, cela est nécessaire dans un [`StackLayout`](xref:Xamarin.Forms.StackLayout).

### <a name="local-html-content"></a>Contenu HTML local

WebView peut afficher le contenu à partir de HTML, CSS et Javascript incorporés dans l’application. Par exemple :

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

Pour afficher le contenu local à l’aide d’un `WebView`, vous devez ouvrir le fichier HTML comme n’importe quel autre, puis charger le contenu sous la forme d’une chaîne dans la propriété `Html` d’un `HtmlWebViewSource`. Pour plus d’informations sur l’ouverture des fichiers, consultez [utilisation des fichiers](~/xamarin-forms/data-cloud/data/files.md).

Les captures d’écran suivantes montrent le résultat de l’affichage du contenu local sur chaque plateforme :

![WebView affichant du contenu local](webview-images/local-content.png)

Bien que la première page ait été chargée, le `WebView` n’a aucune connaissance de l’origine du code HTML. C’est un problème lors du traitement de pages qui référencent des ressources locales. Lorsque le lien local pages uns des autres, une page permet de recourir à un fichier JavaScript séparé, ou une page établit un lien vers une feuille de style CSS sont des exemples de lorsque cela peut se produire.  

Pour résoudre ce cas, vous devez indiquer au `WebView` où trouver les fichiers sur le système de fichiers. Pour ce faire, définissez la propriété `BaseUrl` sur le `HtmlWebViewSource` utilisé par le `WebView`.

Étant donné que le système de fichiers sur chacun des systèmes d’exploitation est différent, vous devez déterminer cette URL sur chaque plateforme. Xamarin. Forms expose les `DependencyService` pour la résolution de dépendances au moment de l’exécution sur chaque plateforme.

Pour utiliser le `DependencyService`, commencez par définir une interface qui peut être implémentée sur chaque plateforme :

```csharp
public interface IBaseUrl { string Get(); }
```

Notez que jusqu'à ce que l’interface est implémentée sur chaque plateforme, l’application s’exécutera pas. Dans le projet commun, veillez à définir le `BaseUrl` à l’aide de la `DependencyService`:

```csharp
var source = new HtmlWebViewSource();
source.BaseUrl = DependencyService.Get<IBaseUrl>().Get();
```

Implémentations de l’interface pour chaque plateforme puis doivent être fournies.

#### <a name="ios"></a>iOS

Sur iOS, le contenu Web doit se trouver dans le répertoire racine ou le répertoire de **ressources** du projet à l’aide de l’action de génération *BundleResource*, comme illustré ci-dessous :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Fichiers locaux sur iOS](webview-images/ios-vs.png)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers locaux sur iOS](webview-images/ios-xs.png)

-----

Le `BaseUrl` doit être défini sur le chemin d’accès du bundle principal :

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

Sur Android, les fichiers du dossier **ressources** sont également accessibles via le contexte Android actuel, qui est exposé par la propriété `MainActivity.Instance` :

```csharp
var assetManager = MainActivity.Instance.Assets;
using (var streamReader = new StreamReader (assetManager.Open ("local.html")))
{
  var html = streamReader.ReadToEnd ();
}
```

#### <a name="universal-windows-platform"></a>Plateforme Windows universelle

Sur les projets plateforme Windows universelle (UWP), placez les images HTML, CSS et dans la racine du projet avec l’action de génération définie sur *contenu*.

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

- **GoForward ()** &ndash; si `CanGoForward` a la valeur true, l’appel de `GoForward` accède à la page visitée suivante.
- **GoBack ()** &ndash; si `CanGoBack` a la valeur true, l’appel de `GoBack` accède à la dernière page visitée.
- **CanGoBack** &ndash; `true` s’il existe des pages vers lesquelles naviguer en arrière, `false` si le navigateur est à l’URL de départ.
- **CanGoForward** &ndash; `true` si l’utilisateur a navigué vers l’arrière et peut avancer jusqu’à une page déjà visitée.

Dans les pages, `WebView` ne prend pas en charge les gestes tactiles multiples. Il est important pour vous assurer que le contenu est optimisé pour les mobiles et s’affiche sans avoir besoin du zoom.

Il est courant pour les applications d’afficher un lien dans un `WebView`, plutôt que dans le navigateur de l’appareil. Dans ces situations, il est utile permettre la navigation normale, mais lors de l’accès utilisateur lorsqu’ils sont sur le lien de départ, l’application doit retourner à la vue de l’application normale.

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

Et voilà !

![Boutons de navigation WebView](webview-images/in-app-browser.png)

## <a name="events"></a>Événements

WebView déclenche les événements suivants pour vous aider à répondre aux modifications d’état :

- [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) : événement déclenché lorsque la WebView commence à charger une nouvelle page.
- [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) : événement déclenché lorsque la page est chargée et que la navigation s’est arrêtée.
- [`ReloadRequested`](xref:Xamarin.Forms.WebView.ReloadRequested) : événement déclenché lors de la création d’une demande de rechargement du contenu actuel.

L’objet [`WebNavigatingEventArgs`](xref:Xamarin.Forms.WebNavigatingEventArgs) qui accompagne l’événement [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) a quatre propriétés :

- `Cancel` : indique s’il faut ou non annuler la navigation.
- `NavigationEvent` : événement de navigation qui a été déclenché.
- `Source` : élément qui a effectué la navigation.
- `Url` : destination de navigation.

L’objet [`WebNavigatedEventArgs`](xref:Xamarin.Forms.WebNavigatedEventArgs) qui accompagne l’événement [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) a quatre propriétés :

- `NavigationEvent` : événement de navigation qui a été déclenché.
- `Result` : décrit le résultat de la navigation, à l’aide d’un membre de l’énumération [`WebNavigationResult`](xref:Xamarin.Forms.WebNavigationResult) . Les valeurs valides sont `Cancel`, `Failure`, `Success` et `Timeout`.
- `Source` : élément qui a effectué la navigation.
- `Url` : destination de navigation.

Si vous prévoyez d’utiliser des pages Web dont le chargement prend beaucoup de temps, envisagez d’utiliser les événements [`Navigating`](xref:Xamarin.Forms.WebView.Navigating) et [`Navigated`](xref:Xamarin.Forms.WebView.Navigated) pour implémenter un indicateur d’État. Par exemple :

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

[`WebView`](xref:Xamarin.Forms.WebView) a une méthode `Reload` qui peut être utilisée pour recharger le contenu actuel :

```csharp
var webView = new WebView();
...
webView.Reload();
```

Lorsque la méthode `Reload` est appelée, l’événement `ReloadRequested` est déclenché, indiquant qu’une demande a été effectuée pour recharger le contenu actuel.

## <a name="performance"></a>Performances

Les navigateurs Web populaires adoptent des technologies telles que le rendu à accélération matérielle et la compilation JavaScript. Avant Xamarin. Forms 4,4, le `WebView` Xamarin. Forms a été implémenté sur iOS par la classe `UIWebView`. Toutefois, un grand nombre de ces technologies n’étaient pas disponibles dans cette implémentation. Par conséquent, étant donné que Xamarin. Forms 4,4, Xamarin. Forms `WebView` est implémenté sur iOS par la classe `WkWebView`, qui prend en charge la navigation plus rapide.

> [!NOTE]
> Sur iOS, la `WkWebViewRenderer` a une surcharge de constructeur qui accepte un argument `WkWebViewConfiguration`. Cela permet de configurer le convertisseur à la création.

Une application peut revenir à l’utilisation de la classe `UIWebView` iOS pour implémenter la `WebView`Xamarin. Forms, pour des raisons de compatibilité. Pour ce faire, vous pouvez ajouter le code suivant au fichier **AssemblyInfo.cs** dans le projet de plateforme IOS pour l’application :

```csharp
// Opt-in to using UIWebView instead of WkWebView.
[assembly: ExportRenderer(typeof(Xamarin.Forms.WebView), typeof(Xamarin.Forms.Platform.iOS.WebViewRenderer))]
```

`WebView` sur Android est par défaut aussi rapide que le navigateur intégré.

La [WebView UWP](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/web-view) utilise le moteur de rendu Microsoft Edge. Les appareils de bureau et des tablettes devraient s’afficher les mêmes performances en utilisant le navigateur Edge proprement dit.

## <a name="permissions"></a>Autorisations

Pour que `WebView` fonctionne, vous devez vous assurer que les autorisations sont définies pour chaque plateforme. Notez que sur certaines plateformes, `WebView` fonctionnera en mode débogage, mais pas en cas de génération pour la mise en version. C’est parce que certaines autorisations, comme ceux utilisés pour l’accès à internet sur Android, sont définies par défaut par Visual Studio pour Mac en mode de débogage.

- La &ndash; **UWP** requiert la fonctionnalité Internet (client & Server) lors de l’affichage du contenu réseau.
- **Android** &ndash; nécessite `INTERNET` uniquement lors de l’affichage du contenu à partir du réseau. Le contenu local ne nécessite aucune autorisation spéciale.
- **iOS** &ndash; ne requiert pas d’autorisations spéciales.

## <a name="layout"></a>Disposition

Contrairement à la plupart des autres vues Xamarin. Forms, `WebView` requiert que `HeightRequest` et `WidthRequest` soient spécifiés lorsqu’ils sont contenus dans StackLayout ou RelativeLayout. Si vous ne parvenez pas à spécifier ces propriétés, le `WebView` ne sera pas restitué.

Les exemples suivants illustrent des dispositions qui aboutissent à un travail, un rendu `WebView`s :

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

Grid *sans* WidthRequest & HeightRequest. Grille est une des dispositions quelques qui ne nécessite pas de spécification hauteurs demandés et les largeurs. :

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

[`WebView`](xref:Xamarin.Forms.WebView) permet d’appeler une fonction JavaScript à partir de C#et de retourner tout résultat au code appelant C# . Pour ce faire, vous devez utiliser la méthode [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) , qui est illustrée dans l’exemple suivant de l’exemple [WebView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview) :

```csharp
var numberEntry = new Entry { Text = "5" };
var resultLabel = new Label();
var webView = new WebView();
...

int number = int.Parse(numberEntry.Text);
string result = await webView.EvaluateJavaScriptAsync($"factorial({number})");
resultLabel.Text = $"Factorial of {number} is {result}.";
```

La méthode [`WebView.EvaluateJavaScriptAsync`](xref:Xamarin.Forms.WebView.EvaluateJavaScriptAsync*) évalue le JavaScript spécifié comme argument et retourne tout résultat sous forme de `string`. Dans cet exemple, la fonction JavaScript `factorial` est appelée, ce qui retourne la factorielle de `number` en conséquence. Cette fonction JavaScript est définie dans le fichier HTML local que le [`WebView`](xref:Xamarin.Forms.WebView) charge, et est illustré dans l’exemple suivant :

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

## <a name="uiwebview-deprecation-and-app-store-rejection-itms-90809"></a>Désapprobation UIWebView et rejet de l’App Store (ITMS-90809)

À compter du 2020 avril, [Apple rejette les applications](https://developer.apple.com/news/?id=12232019b) qui utilisent toujours l’API de `UIWebView` déconseillée. Alors que Xamarin. Forms est passé à `WKWebView` comme valeur par défaut, il existe toujours une référence à l’ancien SDK dans les fichiers binaires Xamarin. Forms. Le comportement actuel de l' [éditeur de liens iOS](~/ios/deploy-test/linker.md) ne supprime pas cela et, par conséquent, l’API dépréciée `UIWebView` semblera toujours être référencée à partir de votre application lorsque vous la soumettez à l’App Store.

Une version préliminaire de l’éditeur de liens est disponible pour résoudre ce problème. Pour activer la préversion, vous devrez fournir un argument supplémentaire `--optimize=experimental-xforms-product-type` à l’éditeur de liens. 

Les conditions préalables à ce fonctionnement sont les suivantes :

- **Xamarin. forms 4,5 ou une version ultérieure** &ndash; les versions préliminaires de Xamarin. forms 4,5 peuvent être utilisées.
- **Xamarin. iOS 13.10.0.17 ou version ultérieure** &ndash; Vérifiez votre version de Xamarin. iOS [dans Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#version-information). Cette version de Xamarin. iOS est incluse avec Visual Studio pour Mac 8.4.1 et Visual Studio 16.4.3.
- **Supprimez les références à `UIWebView`** &ndash; votre code ne doit pas contenir de références à des `UIWebView` ou à des classes qui utilisent `UIWebView`.

### <a name="configure-the-linker-preview"></a>Configurer la version préliminaire de l’éditeur de liens

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Procédez comme suit pour que l’éditeur de liens supprime `UIWebView` références :

1. **Ouvrez les propriétés du projet ios** &ndash; cliquez avec le bouton droit sur votre projet iOS, puis choisissez **Propriétés**.
1. **Accédez à la section Build ios** &ndash; sélectionnez la section **Build iOS** .
1. **Mettez à jour les arguments mTouch supplémentaires** &ndash; dans les **arguments mTouch supplémentaires** ajouter cet indicateur `--optimize=experimental-xforms-product-type` (en plus de toute valeur qui peut déjà y figurer). Remarque : cet indicateur fonctionne conjointement avec le **comportement** de l’éditeur de liens défini sur **Kit de développement logiciel (SDK) uniquement** ou **lier tout**. Si, pour une raison quelconque, vous voyez des erreurs lors de la définition du comportement de l’éditeur de liens sur tout, il s’agit probablement d’un problème dans le code de l’application ou dans une bibliothèque tierce qui n’est pas sécurisée pour l’éditeur de liens. Pour plus d’informations sur l’éditeur de liens, consultez [liaison d’applications Xamarin. iOS](~/ios/deploy-test/linker.md).
1. **Mettez à jour toutes les configurations de build** &ndash; utilisez les listes **configuration** et **plateforme** en haut de la fenêtre pour mettre à jour toutes les configurations de Build. La configuration la plus importante à mettre à jour est la configuration de la **version ou** de l’iPhone, car elle est généralement utilisée pour créer des builds pour l’envoi de l’App Store.

Vous pouvez voir la fenêtre avec le nouvel indicateur en place dans cette capture d’écran :

[![de la définition de l’indicateur dans la section de génération iOS](webview-images/iosbuildblade-vs-sml.png)](webview-images/iosbuildblade-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Suivez ces étapes pour que l’éditeur de liens supprime les références `UIWebView`

1. **Ouvrez Options de projet ios** &ndash; cliquez avec le bouton droit sur votre projet iOS, puis choisissez **options**.
1. **Accédez à la section Build ios** &ndash; sélectionnez la section **Build iOS** .
1. **Mettez à jour les arguments _mTouch_ supplémentaires** &ndash; dans les **arguments _mtouch_ supplémentaires** ajouter cet indicateur `--optimize=experimental-xforms-product-type` (en plus de toute valeur qui peut déjà y figurer). Remarque : cet indicateur fonctionne conjointement avec le **comportement** de l’éditeur de liens défini sur **Kit de développement logiciel (SDK) uniquement** ou **lier tout**. Si, pour une raison quelconque, vous voyez des erreurs lors de la définition du comportement de l’éditeur de liens sur tout, il s’agit probablement d’un problème dans le code de l’application ou dans une bibliothèque tierce qui n’est pas sécurisée pour l’éditeur de liens. Pour plus d’informations sur l’éditeur de liens, consultez [liaison d’applications Xamarin. iOS](~/ios/deploy-test/linker.md).
1. **Mettez à jour toutes les configurations de build** &ndash; utilisez les listes **configuration** et **plateforme** en haut de la fenêtre pour mettre à jour toutes les configurations de Build. La configuration la plus importante à mettre à jour est la configuration de la **version ou** de l’iPhone, car elle est généralement utilisée pour créer des builds pour l’envoi de l’App Store.

Vous pouvez voir la fenêtre avec le nouvel indicateur en place dans cette capture d’écran :

[![de la définition de l’indicateur dans la section de génération iOS](webview-images/iosbuildblade-xs-sml.png)](webview-images/iosbuildblade-xs.png#lightbox)

-----

Désormais, lorsque vous créez une nouvelle build (version Release) et l’envoyez à l’App Store, aucun avertissement ne doit s’poser sur l’API déconseillée.

## <a name="related-links"></a>Liens connexes

- [Utilisation de WebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithwebview)
- [WebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-webview)
