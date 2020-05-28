---
title: ''
description: Une vue Xamarin.Forms WebView est une vue qui affiche du contenu Web et HTML dans votre application. Cet article explique comment créer un convertisseur personnalisé qui étend la WebView pour permettre au code C# d’être appelé à partir de JavaScript.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8c83742896af4a22bcff327df82c1b14ff983bb2
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138968"
---
# <a name="customizing-a-webview"></a>Personnalisation d’une WebView

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Un Xamarin.Forms `WebView` est une vue qui affiche du contenu Web et HTML dans votre application. Cet article explique comment créer un convertisseur personnalisé qui étend le `WebView` pour permettre au code C# d’être appelé à partir de JavaScript._

Chaque Xamarin.Forms vue possède un convertisseur associé pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’un [`WebView`](xref:Xamarin.Forms.WebView) est rendu par une Xamarin.Forms application sur iOS, la `WkWebViewRenderer` classe est instanciée, qui à son tour instancie un `WkWebView` contrôle natif. Sur la plateforme Android, la classe `WebViewRenderer` instancie un contrôle `WebView` natif. Sur la plateforme Windows universelle (UWP), la classe `WebViewRenderer` instancie un contrôle `WebView` natif. Pour plus d’informations sur le convertisseur et les classes de contrôle natifs sur lesquelles les Xamarin.Forms contrôles sont mappés, consultez [classes de base de convertisseur et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre le [`View`](xref:Xamarin.Forms.View) et les contrôles natifs correspondants qui l’implémentent :

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

Le processus de rendu peut être utilisé pour implémenter des personnalisations de plateforme en créant un convertisseur personnalisé pour un [`WebView`](xref:Xamarin.Forms.WebView) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#create-the-hybridwebview) le contrôle personnalisé `HybridWebView`.
1. [Consommez](#consume-the-hybridwebview) `HybridWebView` à partir de Xamarin.Forms .
1. [Créez](#create-the-custom-renderer-on-each-platform) le renderer personnalisé pour le `HybridWebView` sur chaque plateforme.

Chaque élément sera maintenant abordé à son tour pour implémenter un `HybridWebView` convertisseur qui améliore le Xamarin.Forms [`WebView`](xref:Xamarin.Forms.WebView) pour permettre l’appel de code C# à partir de JavaScript. L’instance `HybridWebView` est utilisée pour afficher une page HTML qui invite l’utilisateur à entrer son nom. Ensuite, quand l’utilisateur clique sur un bouton HTML, une fonction JavaScript appelle une `Action` C# qui affiche une fenêtre contextuelle contenant le nom de l’utilisateur.

Pour plus d’informations sur le processus d’appel de C# à partir de JavaScript, consultez [Invoke c# from JavaScript](#invoke-c-from-javascript)(en anglais). Pour plus d’informations sur la page HTML, consultez [créer la page Web](#create-the-web-page).

> [!NOTE]
> Un [`WebView`](xref:Xamarin.Forms.WebView) peut appeler une fonction JavaScript à partir de C# et retourner tout résultat au code c# appelant. Pour plus d’informations, consultez [appel de JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript).

## <a name="create-the-hybridwebview"></a>Créer le HybridWebView

Le `HybridWebView` contrôle personnalisé peut être créé en sous-classant la [`WebView`](xref:Xamarin.Forms.WebView) classe :

```csharp
public class HybridWebView : WebView
{
    Action<string> action;

    public static readonly BindableProperty UriProperty = BindableProperty.Create(
        propertyName: "Uri",
        returnType: typeof(string),
        declaringType: typeof(HybridWebView),
        defaultValue: default(string));

    public string Uri
    {
        get { return (string)GetValue(UriProperty); }
        set { SetValue(UriProperty, value); }
    }

    public void RegisterAction(Action<string> callback)
    {
        action = callback;
    }

    public void Cleanup()
    {
        action = null;
    }

    public void InvokeAction(string data)
    {
        if (action == null || data == null)
        {
            return;
        }
        action.Invoke(data);
    }
}
```

Le contrôle personnalisé `HybridWebView` est créé dans le projet de bibliothèque .NET Standard et définit l’API suivante :

- Une propriété `Uri` qui spécifie l’adresse de la page web à charger.
- Une méthode `RegisterAction` qui inscrit une `Action` auprès du contrôle. L’action inscrite est appelée à partir d’un code JavaScript contenu dans le fichier HTML référencé par le biais de la propriété `Uri`.
- Une méthode `CleanUp`qui supprime la référence à l’`Action` inscrite.
- Une méthode `InvokeAction` qui appelle l’`Action` inscrite. Cette méthode sera appelée à partir d’un convertisseur personnalisé dans chaque projet de plateforme.

## <a name="consume-the-hybridwebview"></a>Utilisation du HybridWebView

Vous pouvez référencer le contrôle personnalisé `HybridWebView` en XAML dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur le contrôle personnalisé. L’exemple de code suivant montre comment le contrôle personnalisé `HybridWebView` peut être consommé par une page XAML :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,40,0,0">
    <local:HybridWebView x:Name="hybridWebView"
                         Uri="index.html" />
</ContentPage>

```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer le contrôle personnalisé.

L’exemple de code suivant montre comment le contrôle personnalisé `HybridWebView` peut être consommé par une page C# :

```csharp
public HybridWebViewPageCS()
{
    var hybridWebView = new HybridWebView
    {
        Uri = "index.html"
    };
    // ...
    Padding = new Thickness(0, 40, 0, 0);
    Content = hybridWebView;
}
```

L’instance `HybridWebView` est utilisée pour afficher un contrôle web natif sur chaque plateforme. Sa `Uri` propriété est définie sur un fichier HTML qui est stocké dans chaque projet de plateforme et qui sera affiché par le contrôle Web natif. Le code HTML restitué invite l’utilisateur à entrer son nom, une fonction JavaScript appelant une `Action` C# en réponse à un clic de bouton HTML.

La `HybridWebViewPage` enregistre l’action à appeler à partir de JavaScript, comme illustré dans l’exemple de code suivant :

```csharp
public partial class HybridWebViewPage : ContentPage
{
    public HybridWebViewPage()
    {
        // ...
        hybridWebView.RegisterAction(data => DisplayAlert("Alert", "Hello " + data, "OK"));
    }
}
```

Cette action appelle la [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) méthode pour afficher une fenêtre contextuelle modale qui présente le nom entré dans la page HTML affichée par l' `HybridWebView` instance.

Un convertisseur personnalisé peut maintenant être ajouté à chaque projet d’application pour améliorer les contrôles Web de plateforme en autorisant l’appel de code C# à partir de JavaScript.

## <a name="create-the-custom-renderer-on-each-platform"></a>Créer le convertisseur personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la `WkWebViewRenderer` classe sur iOS, et la `WebViewRenderer` classe sur Android et UWP, qui restitue le contrôle personnalisé.
1. Substituez la `OnElementChanged` méthode qui restitue le [`WebView`](xref:Xamarin.Forms.WebView) et la logique d’écriture pour le personnaliser. Cette méthode est appelée lors de la création d’un `HybridWebView` objet.
1. Ajoutez un `ExportRenderer` attribut à la classe de convertisseur personnalisé ou à *AssemblyInfo.cs*pour spécifier qu’il sera utilisé pour restituer le Xamarin.Forms contrôle personnalisé. Cet attribut est utilisé pour inscrire le convertisseur personnalisé avec Xamarin.Forms .

> [!NOTE]
> Pour la plupart des Xamarin.Forms éléments, il est facultatif de fournir un convertisseur personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé. Toutefois, les renderers personnalisés sont nécessaires dans chaque projet de plateforme lors du rendu d’un élément [View](xref:Xamarin.Forms.View).

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

Le `HybridWebView` contrôle personnalisé est rendu par les classes de convertisseur de plateforme, qui dérivent de la `WkWebViewRenderer` classe sur iOS et de la `WebViewRenderer` classe sur Android et UWP. Cela entraîne `HybridWebView` le rendu de chaque contrôle personnalisé avec les contrôles Web natifs, comme illustré dans les captures d’écran suivantes :

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

Les `WkWebViewRenderer` `WebViewRenderer` classes et exposent la `OnElementChanged` méthode, qui est appelée lorsque le Xamarin.Forms contrôle personnalisé est créé pour restituer le contrôle Web natif correspondant. Cette méthode prend un `VisualElementChangedEventArgs` paramètre qui contient `OldElement` les `NewElement` Propriétés et. Ces propriétés représentent l' Xamarin.Forms élément auquel le convertisseur *a été* attaché, et l' Xamarin.Forms élément auquel le convertisseur *est* attaché, respectivement. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence à l’instance `HybridWebView`.

Une version substituée de la `OnElementChanged` méthode, dans chaque classe de convertisseur de plateforme, est l’endroit où effectuer la personnalisation de contrôle Web native. Une référence au Xamarin.Forms contrôle qui est rendu peut être obtenue via la `Element` propriété.

Chaque classe de convertisseur personnalisé est décorée avec un `ExportRenderer` attribut qui inscrit le convertisseur auprès de Xamarin.Forms . L’attribut accepte deux paramètres : le nom de type du Xamarin.Forms contrôle personnalisé rendu et le nom de type du convertisseur personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent la structure de la page Web chargée par chaque contrôle Web natif, le processus d’appel de code C# à partir de JavaScript et l’implémentation de ce dans chaque classe de convertisseur personnalisée de plateforme.

### <a name="create-the-web-page"></a>Créer la page Web

L’exemple de code suivant montre la page web affichée par le contrôle personnalisé `HybridWebView` :

```html
<html>
<body>
    <script src="http://code.jquery.com/jquery-2.1.4.min.js"></script>
    <h1>HybridWebView Test</h1>
    <br />
    Enter name: <input type="text" id="name">
    <br />
    <br />
    <button type="button" onclick="javascript: invokeCSCode($('#name').val());">Invoke C# Code</button>
    <br />
    <p id="result">Result:</p>
    <script type="text/javascript">function log(str) {
            $('#result').text($('#result').text() + " " + str);
        }

        function invokeCSCode(data) {
            try {
                log("Sending Data:" + data);
                invokeCSharpAction(data);
            }
            catch (err) {
                log(err);
            }
        }</script>
</body>
</html>
```

La page web permet à un utilisateur d’entrer son nom dans un élément `input` et fournit un élément `button` qui appelle du code C# quand l’utilisateur clique cet élément. Le processus pour y parvenir est le suivant :

- Quand l’utilisateur clique sur l’élément `button`, la fonction JavaScript `invokeCSCode` est appelée et reçoit la valeur de l’élément `input`.
- La fonction `invokeCSCode` appelle la fonction `log` pour afficher les données qu’elle envoie à l’`Action` C#. Elle appelle ensuite la méthode `invokeCSharpAction` pour appeler l’`Action` C#, en transmettant le paramètre reçu de l’élément `input`.

La fonction JavaScript `invokeCSharpAction`n’est pas définie dans la page web ; elle y est injectée par chaque renderer personnalisé.

Sur iOS, ce fichier HTML réside dans le dossier Contenu du projet de plateforme, avec une action de build de **BundleResource**. Sur Android, ce fichier HTML réside dans le dossier Ressources/Contenu du projet de plateforme, avec une action de build de **AndroidAsset**.

### <a name="invoke-c-from-javascript"></a>Appeler C# à partir de JavaScript

Le processus permettant d’appeler C# à partir de JavaScript est identique sur chaque plateforme :

- Le renderer personnalisé crée un contrôle web natif et charge le fichier HTML spécifié par la propriété `HybridWebView.Uri`.
- Une fois la page web chargée, le renderer personnalisé injecte la fonction JavaScript `invokeCSharpAction` dans la page web.
- Quand l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction `invokeCSCode` est appelée, qui appelle à son tour la fonction `invokeCSharpAction`.
- La fonction `invokeCSharpAction` appelle une méthode dans le renderer personnalisé, qui à son tour appelle la méthode `HybridWebView.InvokeAction`.
- La méthode `HybridWebView.InvokeAction` appelle l’`Action` inscrite.

Les sections suivantes expliquent comment ce processus est implémenté sur chaque plateforme.

### <a name="create-the-custom-renderer-on-ios"></a>Créer le convertisseur personnalisé sur iOS

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : WkWebViewRenderer, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        public HybridWebViewRenderer() : this(new WKWebViewConfiguration())
        {
        }

        public HybridWebViewRenderer(WKWebViewConfiguration config) : base(config)
        {
            userController = config.UserContentController;
            var script = new WKUserScript(new NSString(JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
            userController.AddUserScript(script);
            userController.AddScriptMessageHandler(this, "invokeAction");
        }

        protected override void OnElementChanged(VisualElementChangedEventArgs e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                userController.RemoveAllUserScripts();
                userController.RemoveScriptMessageHandler("invokeAction");
                HybridWebView hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }

            if (e.NewElement != null)
            {
                string filename = Path.Combine(NSBundle.MainBundle.BundlePath, $"Content/{((HybridWebView)Element).Uri}");
                LoadRequest(new NSUrlRequest(new NSUrl(filename, false)));
            }
        }

        public void DidReceiveScriptMessage(WKUserContentController userContentController, WKScriptMessage message)
        {
            ((HybridWebView)Element).InvokeAction(message.Body.ToString());
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

La `HybridWebViewRenderer` classe charge la page Web spécifiée dans la `HybridWebView.Uri` propriété dans un [`WKWebView`](xref:WebKit.WKWebView) contrôle natif, et la `invokeCSharpAction` fonction JavaScript est injectée dans la page Web. Une fois que l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction JavaScript `invokeCSharpAction` est exécutée, puis la méthode `DidReceiveScriptMessage` est appelée après la réception d’un message de la page web. À son tour, cette méthode appelle la méthode `HybridWebView.InvokeAction`, qui appelle l’action inscrite pour afficher la fenêtre contextuelle.

Cette fonctionnalité est obtenue comme suit :

- Le constructeur de convertisseur crée un `WkWebViewConfiguration` objet et récupère son [`WKUserContentController`](xref:WebKit.WKUserContentController) objet. L' `WkUserContentController` objet permet de publier des messages et d’injecter des scripts utilisateur dans une page Web.
- Le constructeur de convertisseur crée un [`WKUserScript`](xref:WebKit.WKUserScript) objet, qui injecte la `invokeCSharpAction` fonction JavaScript dans la page Web après le chargement de la page Web.
- Le constructeur de convertisseur appelle la [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) méthode pour ajouter l' [`WKUserScript`](xref:WebKit.WKUserScript) objet au contrôleur de contenu.
- Le constructeur de convertisseur appelle la [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) méthode pour ajouter un gestionnaire de messages de script nommé `invokeAction` à l' [`WKUserContentController`](xref:WebKit.WKUserContentController) objet, ce qui entraîne la `window.webkit.messageHandlers.invokeAction.postMessage(data)` définition de la fonction JavaScript dans tous les frames de toutes les `WebView` instances qui utilisent l' `WKUserContentController` objet.
- Si le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément :
  - La [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) méthode charge le fichier HTML spécifié par la `HybridWebView.Uri` propriété. Le code spécifie que le fichier est stocké dans le dossier `Content` du projet. Une fois la page web affichée, la fonction JavaScript `invokeCSharpAction` y est injectée.
- Les ressources sont libérées lorsque l’élément auquel le convertisseur est attaché les modifications.
- L' Xamarin.Forms élément est nettoyé lorsque le convertisseur est supprimé.

> [!NOTE]
> La classe `WKWebView` est uniquement prise en charge dans iOS versions 8 et ultérieures.

De plus, **Info.plist** doit être mis à jour pour inclure les valeurs suivantes :

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <true/>
</dict>
```

### <a name="create-the-custom-renderer-on-android"></a>Créer le convertisseur personnalisé sur Android

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                ((HybridWebView)Element).Cleanup();
            }
            if (e.NewElement != null)
            {
                Control.SetWebViewClient(new JavascriptWebViewClient(this, $"javascript: {JavascriptFunction}"));
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{((HybridWebView)Element).Uri}");
            }
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

La `HybridWebViewRenderer` classe charge la page Web spécifiée dans la `HybridWebView.Uri` propriété dans un [`WebView`](xref:Android.Webkit.WebView) contrôle natif, et la `invokeCSharpAction` fonction JavaScript est injectée dans la page Web, une fois le chargement de la page Web terminé, avec la `OnPageFinished` substitution dans la `JavascriptWebViewClient` classe :

```csharp
public class JavascriptWebViewClient : FormsWebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(HybridWebViewRenderer renderer, string javascript) : base(renderer)
    {
        _javascript = javascript;
    }

    public override void OnPageFinished(WebView view, string url)
    {
        base.OnPageFinished(view, url);
        view.EvaluateJavascript(_javascript, null);
    }
}
```

Une fois que l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction JavaScript `invokeCSharpAction` est exécutée. Cette fonctionnalité est obtenue comme suit :

- Si le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément :
  - La `SetWebViewClient` méthode définit un nouvel `JavascriptWebViewClient` objet en tant qu’implémentation de `WebViewClient` .
  - La [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) méthode injecte une nouvelle `JSBridge` instance dans le frame principal du contexte JavaScript de WebView, en la nommant `jsBridge` . Ainsi, les méthodes dans la classe `JSBridge` sont accessibles à partir de JavaScript.
  - La [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) méthode charge le fichier HTML spécifié par la `HybridWebView.Uri` propriété. Le code spécifie que le fichier est stocké dans le dossier `Content` du projet.
  - Dans la classe `JavascriptWebViewClient`, la fonction JavaScript `invokeCSharpAction` est injectée dans la page web une fois celle-ci chargée.
- Les ressources sont libérées lorsque l’élément auquel le convertisseur est attaché les modifications.
- L' Xamarin.Forms élément est nettoyé lorsque le convertisseur est supprimé.

Quand la fonction JavaScript `invokeCSharpAction` est exécutée, elle appelle à son tour la méthode `JSBridge.InvokeAction`, comme l’illustre l’exemple de code suivant :

```csharp
public class JSBridge : Java.Lang.Object
{
    readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

    public JSBridge(HybridWebViewRenderer hybridRenderer)
    {
        hybridWebViewRenderer = new WeakReference<HybridWebViewRenderer>(hybridRenderer);
    }

    [JavascriptInterface]
    [Export("invokeAction")]
    public void InvokeAction(string data)
    {
        HybridWebViewRenderer hybridRenderer;

        if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget(out hybridRenderer))
        {
            ((HybridWebView)hybridRenderer.Element).InvokeAction(data);
        }
    }
}
```

La classe doit dériver de `Java.Lang.Object`, tandis que les méthodes qui sont exposées à JavaScript doivent être décorées avec les attributs `[JavascriptInterface]` et `[Export]`. Ainsi, quand la fonction JavaScript `invokeCSharpAction` est injectée dans la page web et est exécutée, elle appelle la méthode `JSBridge.InvokeAction`, car celle-ci est décorée avec les attributs `[JavascriptInterface]` et `[Export("invokeAction")]`. À son tour, la `InvokeAction` méthode appelle la `HybridWebView.InvokeAction` méthode, qui appellera l’action inscrite pour afficher la fenêtre contextuelle.

> [!IMPORTANT]
> Les projets Android qui utilisent l' `[Export]` attribut doivent inclure une référence à `Mono.Android.Export` , sinon une erreur de compilateur se produit.

Notez que la classe `JSBridge` conserve une `WeakReference` à la classe `HybridWebViewRenderer`, afin d’éviter la création d’une référence circulaire entre les deux classes. Pour plus d’informations, consultez [références faibles](/en-us/dotnet/standard/garbage-collection/weak-references).

### <a name="create-the-custom-renderer-on-uwp"></a>Créer le convertisseur personnalisé sur UWP

L’exemple de code suivant illustre le renderer personnalisé pour UWP :

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : WebViewRenderer
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<Xamarin.Forms.WebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri($"ms-appx-web:///Content//{((HybridWebView)Element).Uri}");
            }
        }

        async void OnWebViewNavigationCompleted(Windows.UI.Xaml.Controls.WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            ((HybridWebView)Element).InvokeAction(e.Value);
        }

        protected override void Dispose(bool disposing)
        {
            if (disposing)
            {
                ((HybridWebView)Element).Cleanup();
            }
            base.Dispose(disposing);
        }        
    }
}
```

La classe `HybridWebViewRenderer` charge la page web spécifiée dans la propriété `HybridWebView.Uri` dans un contrôle `WebView` natif, puis la fonction JavaScript `invokeCSharpAction` est injectée dans la page web, avec la méthode `WebView.InvokeScriptAsync`. Une fois que l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction JavaScript `invokeCSharpAction` est exécutée, puis la méthode `OnWebViewScriptNotify` est appelée après la réception d’une notification de la page web. À son tour, cette méthode appelle la méthode `HybridWebView.InvokeAction`, qui appelle l’action inscrite pour afficher la fenêtre contextuelle.

Cette fonctionnalité est obtenue comme suit :

- Si le convertisseur personnalisé est attaché à un nouvel Xamarin.Forms élément :
  - Des gestionnaires d’événements pour les événements `NavigationCompleted` et `ScriptNotify` sont inscrits. L’événement `NavigationCompleted` est déclenché quand le contrôle `WebView` natif a terminé de charger le contenu actuel ou si la navigation a échoué. L’événement `ScriptNotify` est déclenché quand le contenu du contrôle `WebView` natif utilise JavaScript pour passer une chaîne à l’application. La page web déclenche l’événement `ScriptNotify` en appelant `window.external.notify` tout en passant un paramètre `string`.
  - La propriété `WebView.Source` est définie sur l’URI du fichier HTML qui est spécifié par la propriété `HybridWebView.Uri`. Le code suppose que le fichier est stocké dans le dossier `Content` du projet. Une fois la page web affichée, l’événement `NavigationCompleted` est déclenché et la méthode `OnWebViewNavigationCompleted` est appelée. La fonction JavaScript `invokeCSharpAction` est ensuite injectée dans la page web avec la méthode `WebView.InvokeScriptAsync`, à condition que la navigation se soit déroulée correctement.
- L’inscription de l’événement est annulée lorsque l’élément auquel le convertisseur est attaché est modifié.
- L' Xamarin.Forms élément est nettoyé lorsque le convertisseur est supprimé.

## <a name="related-links"></a>Liens connexes

- [HybridWebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
