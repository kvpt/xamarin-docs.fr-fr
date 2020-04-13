---
title: Personnalisation d’une WebView
description: Un WebView Xamarin.Forms est une vue qui affiche le contenu Web et HTML de votre application. Cet article explique comment créer un rendu personnalisé qui étend le WebView pour permettre d’invoquer le code CMD à partir de JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/31/2020
ms.openlocfilehash: c736c083d4a8c424d3e017dae3cc30e35ad4fa3b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "80419068"
---
# <a name="customizing-a-webview"></a>Personnalisation d’une WebView

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Un Xamarin.Forms `WebView` est une vue qui affiche le contenu Web et HTML de votre application. Cet article explique comment créer un rendu `WebView` personnalisé qui étend le pour permettre le code C ' d’être invoqué à partir de JavaScript._

Chaque vue Xamarin.Forms est accompagnée d’un renderer pour chaque plateforme qui crée une instance d’un contrôle natif. Lorsqu’une [`WebView`](xref:Xamarin.Forms.WebView) application Xamarin.Forms est rendue sur `WkWebViewRenderer` iOS, la classe est instantanée, ce `WkWebView` qui instantanément un contrôle natif. Sur la plateforme Android, la classe `WebViewRenderer` instancie un contrôle `WebView` natif. Sur la plateforme Windows universelle (UWP), la classe `WebViewRenderer` instancie un contrôle `WebView` natif. Pour plus d’informations sur le renderer et les classes de contrôle natif auxquels les contrôles Xamarin.Forms sont mappés, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la [`View`](xref:Xamarin.Forms.View) relation entre les contrôles natifs correspondants qui la mettent en œuvre :

![](hybridwebview-images/webview-classes.png "Relationship Between the WebView Class and its Implementing Native Classes")

Le processus de rendu peut être utilisé pour implémenter [`WebView`](xref:Xamarin.Forms.WebView) des personnalisations de plate-forme en créant un rendu personnalisé pour un sur chaque plate-forme. Le processus pour y parvenir est le suivant :

1. [Créez](#create-the-hybridwebview) le contrôle personnalisé `HybridWebView`.
1. [Consommez](#consume-the-hybridwebview) le `HybridWebView` à partir de Xamarin.Forms.
1. [Créez](#create-the-custom-renderer-on-each-platform) le renderer personnalisé pour le `HybridWebView` sur chaque plateforme.

Chaque élément sera maintenant discuté à `HybridWebView` son tour pour implémenter [`WebView`](xref:Xamarin.Forms.WebView) un rendu qui améliore les Xamarin.Forms pour permettre d’invoquer le code CMD à partir de JavaScript. L’instance `HybridWebView` est utilisée pour afficher une page HTML qui invite l’utilisateur à entrer son nom. Ensuite, quand l’utilisateur clique sur un bouton HTML, une fonction JavaScript appelle une `Action` C# qui affiche une fenêtre contextuelle contenant le nom de l’utilisateur.

Pour plus d’informations sur le processus d’invocation de C à partir de JavaScript, voir [Invoke C de JavaScript](#invoke-c-from-javascript). Pour plus d’informations sur la page HTML, voir [Créer la page Web](#create-the-web-page).

> [!NOTE]
> A [`WebView`](xref:Xamarin.Forms.WebView) peut invoquer une fonction JavaScript à partir de C, et retourner n’importe quel résultat au code D’appel C. Pour plus d’informations, voir [Invoquer JavaScript](~/xamarin-forms/user-interface/webview.md#invoking-javascript).

## <a name="create-the-hybridwebview"></a>Créez l’HybridWebView

Le `HybridWebView` contrôle personnalisé peut être créé [`WebView`](xref:Xamarin.Forms.WebView) en sous-classant la classe :

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
- Une méthode `InvokeAction` qui appelle l’`Action` inscrite. Cette méthode sera appelée à partir d’un rendu personnalisé dans chaque projet de plate-forme.

## <a name="consume-the-hybridwebview"></a>Consommer l’HybridWebView

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

L’instance `HybridWebView` est utilisée pour afficher un contrôle web natif sur chaque plateforme. Sa `Uri` propriété est définie sur un fichier HTML qui est stocké dans chaque projet de plate-forme, et qui sera affiché par le contrôle web natif. Le code HTML restitué invite l’utilisateur à entrer son nom, une fonction JavaScript appelant une `Action` C# en réponse à un clic de bouton HTML.

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

Cette action [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String)) appelle la méthode pour afficher un pop-up modal qui `HybridWebView` présente le nom entré dans la page HTML affichée par l’instance.

Un rendu personnalisé peut désormais être ajouté à chaque projet d’application pour améliorer les contrôles Web de la plate-forme en permettant d’invoquer le code CMD à partir de JavaScript.

## <a name="create-the-custom-renderer-on-each-platform"></a>Créez le rendu personnalisé sur chaque plate-forme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la `WkWebViewRenderer` classe `WebViewRenderer` sur iOS, et la classe sur Android et UWP, qui rend le contrôle personnalisé.
1. Remplacer la `OnElementChanged` méthode qui [`WebView`](xref:Xamarin.Forms.WebView) rend la logique et l’écrire pour la personnaliser. Cette méthode est `HybridWebView` appelée lorsqu’un objet est créé.
1. Ajoutez `ExportRenderer` un attribut à la classe de rendu personnalisé ou *AssemblyInfo.cs*, pour spécifier qu’il sera utilisé pour rendre le contrôle personnalisé Xamarin.Forms. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Pour la plupart des éléments Xamarin.Forms, il est facultatif de fournir un renderer personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé. Toutefois, les renderers personnalisés sont nécessaires dans chaque projet de plateforme lors du rendu d’un élément [View](xref:Xamarin.Forms.View).

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](hybridwebview-images/solution-structure.png "HybridWebView Custom Renderer Project Responsibilities")

Le `HybridWebView` contrôle personnalisé est rendu par les classes `WkWebViewRenderer` de rendu de plate-forme, qui dérivent de la classe sur iOS, et de la `WebViewRenderer` classe sur Android et UWP. Il en `HybridWebView` résulte que chaque contrôle personnalisé est rendu avec des contrôles Web natifs, comme le montrent les captures d’écran suivantes:

![](hybridwebview-images/screenshots.png "HybridWebView on each Platform")

Les `WkWebViewRenderer` `WebViewRenderer` classes et `OnElementChanged` les classes exposent la méthode, qui est appelée lorsque le contrôle personnalisé Xamarin.Forms est créé pour rendre le contrôle web natif correspondant. Cette méthode `VisualElementChangedEventArgs` prend un `OldElement` `NewElement` paramètre qui contient et les propriétés. Ces propriétés représentent respectivement l’élément Xamarin.Forms auquel le renderer *était* attaché et l’élément Xamarin.Forms auquel le renderer *est* attaché. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence à l’instance `HybridWebView`.

Une version dépassée `OnElementChanged` de la méthode, dans chaque classe de rendu de plate-forme, est l’endroit pour effectuer la personnalisation de contrôle web indigène. Une référence au contrôle Xamarin.Forms qui est rendu peut `Element` être obtenue par la propriété.

Chaque classe de renderer personnalisé est décorée avec un attribut `ExportRenderer` qui inscrit le renderer auprès de Xamarin.Forms. L’attribut accepte deux paramètres : le nom de type du contrôle personnalisé Xamarin.Forms en cours de restitution et le nom de type du renderer personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes discutent de la structure de la page Web chargée par chaque contrôle Web natif, du processus d’invocation de C de JavaScript, et de la mise en œuvre de cette plate-forme classe de rendu personnalisé.

### <a name="create-the-web-page"></a>Créer la page web

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

### <a name="invoke-c-from-javascript"></a>InvoqueZ C de JavaScript

Le processus permettant d’appeler C# à partir de JavaScript est identique sur chaque plateforme :

- Le renderer personnalisé crée un contrôle web natif et charge le fichier HTML spécifié par la propriété `HybridWebView.Uri`.
- Une fois la page web chargée, le renderer personnalisé injecte la fonction JavaScript `invokeCSharpAction` dans la page web.
- Quand l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction `invokeCSCode` est appelée, qui appelle à son tour la fonction `invokeCSharpAction`.
- La fonction `invokeCSharpAction` appelle une méthode dans le renderer personnalisé, qui à son tour appelle la méthode `HybridWebView.InvokeAction`.
- La méthode `HybridWebView.InvokeAction` appelle l’`Action` inscrite.

Les sections suivantes expliquent comment ce processus est implémenté sur chaque plateforme.

### <a name="create-the-custom-renderer-on-ios"></a>Créez le rendu personnalisé sur iOS

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

La `HybridWebViewRenderer` classe charge la page `HybridWebView.Uri` Web spécifiée [`WKWebView`](xref:WebKit.WKWebView) dans la `invokeCSharpAction` propriété dans un contrôle natif, et la fonction JavaScript est injectée dans la page Web. Une fois que l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction JavaScript `invokeCSharpAction` est exécutée, puis la méthode `DidReceiveScriptMessage` est appelée après la réception d’un message de la page web. À son tour, cette méthode appelle la méthode `HybridWebView.InvokeAction`, qui appelle l’action inscrite pour afficher la fenêtre contextuelle.

Cette fonctionnalité est obtenue comme suit :

- Le constructeur de rendu `WkWebViewConfiguration` crée un objet [`WKUserContentController`](xref:WebKit.WKUserContentController) et récupère son objet. L’objet `WkUserContentController` permet d’afficher des messages et d’injecter des scripts d’utilisateur dans une page Web.
- Le constructeur de rendu [`WKUserScript`](xref:WebKit.WKUserScript) crée un objet, qui injecte la `invokeCSharpAction` fonction JavaScript dans la page Web après le chargement de la page Web.
- Le constructeur de rendu [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) appelle la [`WKUserScript`](xref:WebKit.WKUserScript) méthode pour ajouter l’objet au contrôleur de contenu.
- Le constructeur de rendu [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) appelle la méthode pour `invokeAction` ajouter [`WKUserContentController`](xref:WebKit.WKUserContentController) un gestionnaire de message `window.webkit.messageHandlers.invokeAction.postMessage(data)` de script nommé à l’objet, ce qui fera définir la fonction JavaScript dans toutes les images dans tous les `WebView` cas qui utilisent l’objet. `WKUserContentController`
- Sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :
  - La [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) méthode charge le fichier HTML spécifié `HybridWebView.Uri` par la propriété. Le code spécifie que le fichier est stocké dans le dossier `Content` du projet. Une fois la page web affichée, la fonction JavaScript `invokeCSharpAction` y est injectée.
- Les ressources sont libérées lorsque l’élément du rendu est attaché aux modifications.
- L’élément Xamarin.Forms est nettoyé lorsque le rendu est éliminé.

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

### <a name="create-the-custom-renderer-on-android"></a>Créez le rendu personnalisé sur android

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

La `HybridWebViewRenderer` classe charge la page `HybridWebView.Uri` Web spécifiée [`WebView`](xref:Android.Webkit.WebView) dans la `invokeCSharpAction` propriété dans un contrôle natif, et la fonction JavaScript `OnPageFinished` est injectée dans la page Web, après que la page Web a terminé le chargement, avec le remplacement dans la `JavascriptWebViewClient` classe:

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

- Sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :
  - La `SetWebViewClient` méthode définit `JavascriptWebViewClient` un nouvel `WebViewClient`objet comme la mise en œuvre de .
  - La [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) méthode injecte `JSBridge` une nouvelle instance dans le cadre principal du `jsBridge`contexte JavaScript de la WebView, le nommant . Ainsi, les méthodes dans la classe `JSBridge` sont accessibles à partir de JavaScript.
  - La [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) méthode charge le fichier HTML spécifié `HybridWebView.Uri` par la propriété. Le code spécifie que le fichier est stocké dans le dossier `Content` du projet.
  - Dans la classe `JavascriptWebViewClient`, la fonction JavaScript `invokeCSharpAction` est injectée dans la page web une fois celle-ci chargée.
- Les ressources sont libérées lorsque l’élément du rendu est attaché aux modifications.
- L’élément Xamarin.Forms est nettoyé lorsque le rendu est éliminé.

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

La classe doit dériver de `Java.Lang.Object`, tandis que les méthodes qui sont exposées à JavaScript doivent être décorées avec les attributs `[JavascriptInterface]` et `[Export]`. Ainsi, quand la fonction JavaScript `invokeCSharpAction` est injectée dans la page web et est exécutée, elle appelle la méthode `JSBridge.InvokeAction`, car celle-ci est décorée avec les attributs `[JavascriptInterface]` et `[Export("invokeAction")]`. À son `InvokeAction` tour, la `HybridWebView.InvokeAction` méthode invoque la méthode, qui invoquera l’action enregistrée pour afficher le pop-up.

> [!IMPORTANT]
> Les projets `[Export]` Android qui utilisent `Mono.Android.Export`l’attribut doivent inclure une référence à , ou une erreur de compilateur en résultera.

Notez que la classe `JSBridge` conserve une `WeakReference` à la classe `HybridWebViewRenderer`, afin d’éviter la création d’une référence circulaire entre les deux classes. Pour plus d’informations voir [Références faibles](/en-us/dotnet/standard/garbage-collection/weak-references).

### <a name="create-the-custom-renderer-on-uwp"></a>Créez le rendu personnalisé sur UWP

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

- Sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :
  - Des gestionnaires d’événements pour les événements `NavigationCompleted` et `ScriptNotify` sont inscrits. L’événement `NavigationCompleted` est déclenché quand le contrôle `WebView` natif a terminé de charger le contenu actuel ou si la navigation a échoué. L’événement `ScriptNotify` est déclenché quand le contenu du contrôle `WebView` natif utilise JavaScript pour passer une chaîne à l’application. La page web déclenche l’événement `ScriptNotify` en appelant `window.external.notify` tout en passant un paramètre `string`.
  - La propriété `WebView.Source` est définie sur l’URI du fichier HTML qui est spécifié par la propriété `HybridWebView.Uri`. Le code suppose que le fichier est stocké dans le dossier `Content` du projet. Une fois la page web affichée, l’événement `NavigationCompleted` est déclenché et la méthode `OnWebViewNavigationCompleted` est appelée. La fonction JavaScript `invokeCSharpAction` est ensuite injectée dans la page web avec la méthode `WebView.InvokeScriptAsync`, à condition que la navigation se soit déroulée correctement.
- L’événement est désabonné à partir du moment où l’élément du rendu est attaché aux modifications.
- L’élément Xamarin.Forms est nettoyé lorsque le rendu est éliminé.

## <a name="related-links"></a>Liens connexes

- [HybridWebView (échantillon)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
