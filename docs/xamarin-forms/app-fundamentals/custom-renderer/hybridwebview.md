---
title: Implémentation d’un HybridWebView
description: Cet article montre comment créer un renderer personnalisé pour un contrôle personnalisé HybridWebView, qui montre comment améliorer les contrôles web propres à la plateforme web pour permettre d’appeler du code C# à partir de JavaScript.
ms.prod: xamarin
ms.assetid: 58DFFA52-4057-49A8-8682-50A58C7E842C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/07/2019
ms.openlocfilehash: 66e26704f22a73559eef04608726ab3b4a935385
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771922"
---
# <a name="implementing-a-hybridwebview"></a>Implémentation d’un HybridWebView

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)

_Les contrôles d’interface utilisateur personnalisés Xamarin.Forms doivent dériver de la classe View, qui est utilisée pour placer des dispositions et des contrôles sur l’écran. Cet article montre comment créer un renderer personnalisé pour un contrôle personnalisé HybridWebView, qui montre comment améliorer les contrôles web propres à la plateforme web pour permettre d’appeler du code C# à partir de JavaScript._

Chaque vue Xamarin.Forms est accompagnée d’un renderer pour chaque plateforme qui crée une instance d’un contrôle natif. Quand un élément [`View`](xref:Xamarin.Forms.View) est restitué par une application Xamarin.Forms, dans iOS, la classe `ViewRenderer` est instanciée, entraînant à son tour l’instanciation d’un contrôle `UIView` natif. Sur la plateforme Android, la classe `ViewRenderer` instancie un contrôle `View`. Sur la plateforme Windows universelle (UWP), la classe `ViewRenderer` instancie un contrôle `FrameworkElement` natif. Pour plus d’informations sur le renderer et les classes de contrôle natif auxquels les contrôles Xamarin.Forms sont mappés, consultez [Classes de base de renderer et contrôles natifs](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Le diagramme suivant illustre la relation entre l’élément [`View`](xref:Xamarin.Forms.View) et les contrôles natifs correspondants qui l’implémentent :

![](hybridwebview-images/view-classes.png "Relation entre la classe View et son implémentation de classes natives")

Il est possible d’utiliser le processus de rendu pour implémenter des personnalisations spécifiques à la plateforme en créant un renderer personnalisé pour un élément [`View`](xref:Xamarin.Forms.View) sur chaque plateforme. Le processus pour y parvenir est le suivant :

1. [Créez](#Creating_the_HybridWebView) le contrôle personnalisé `HybridWebView`.
1. [Consommez](#Consuming_the_HybridWebView) le `HybridWebView` à partir de Xamarin.Forms.
1. [Créez](#creating-the-custom-renderer-on-each-platform) le renderer personnalisé pour le `HybridWebView` sur chaque plateforme.

Nous allons maintenant présenter chaque élément à tour de rôle pour implémenter un renderer `HybridWebView` qui améliore les contrôles web spécifiques à la plateforme afin de permettre à du code C# d’être appelé à partir de JavaScript. L’instance `HybridWebView` est utilisée pour afficher une page HTML qui invite l’utilisateur à entrer son nom. Ensuite, quand l’utilisateur clique sur un bouton HTML, une fonction JavaScript appelle une `Action` C# qui affiche une fenêtre contextuelle contenant le nom de l’utilisateur.

Pour plus d’informations sur le processus permettant d’appeler C# à partir de JavaScript, consultez [Appel de C# à partir de JavaScript](#Invoking_C_from_JavaScript). Pour plus d’informations sur la page HTML, consultez [Création de la page web](#Creating_the_Web_Page).

<a name="Creating_the_HybridWebView" />

## <a name="creating-the-hybridwebview"></a>Création du HybridWebView

Vous pouvez créer le contrôle personnalisé `HybridWebView` en utilisant une sous-classe de la classe [`View`](xref:Xamarin.Forms.View), comme indiqué dans l’exemple de code suivant :

```csharp
public class HybridWebView : View
{
  Action<string> action;
  public static readonly BindableProperty UriProperty = BindableProperty.Create (
    propertyName: "Uri",
    returnType: typeof(string),
    declaringType: typeof(HybridWebView),
    defaultValue: default(string));

  public string Uri {
    get { return (string)GetValue (UriProperty); }
    set { SetValue (UriProperty, value); }
  }

  public void RegisterAction (Action<string> callback)
  {
    action = callback;
  }

  public void Cleanup ()
  {
    action = null;
  }

  public void InvokeAction (string data)
  {
    if (action == null || data == null) {
      return;
    }
    action.Invoke (data);
  }
}
```

Le contrôle personnalisé `HybridWebView` est créé dans le projet de bibliothèque .NET Standard et définit l’API suivante :

- Une propriété `Uri` qui spécifie l’adresse de la page web à charger.
- Une méthode `RegisterAction` qui inscrit une `Action` auprès du contrôle. L’action inscrite est appelée à partir d’un code JavaScript contenu dans le fichier HTML référencé par le biais de la propriété `Uri`.
- Une méthode `CleanUp`qui supprime la référence à l’`Action` inscrite.
- Une méthode `InvokeAction` qui appelle l’`Action` inscrite. Cette méthode est appelée à partir d’un renderer personnalisé dans chaque projet spécifique à la plateforme.

<a name="Consuming_the_HybridWebView" />

## <a name="consuming-the-hybridwebview"></a>Consommation du HybridWebView

Vous pouvez référencer le contrôle personnalisé `HybridWebView` en XAML dans le projet de bibliothèque .NET Standard en déclarant un espace de noms pour son emplacement et en utilisant le préfixe d’espace de noms sur le contrôle personnalisé. L’exemple de code suivant montre comment le contrôle personnalisé `HybridWebView` peut être consommé par une page XAML :

```xaml
<ContentPage ...
             xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
             x:Class="CustomRenderer.HybridWebViewPage"
             Padding="0,20,0,0">
    <ContentPage.Content>
        <local:HybridWebView x:Name="hybridWebView" Uri="index.html"
          HorizontalOptions="FillAndExpand" VerticalOptions="FillAndExpand" />
    </ContentPage.Content>
</ContentPage>
```

Le préfixe d’espace de noms `local` peut porter n’importe quel nom. Toutefois, les valeurs `clr-namespace` et `assembly` doivent correspondre aux détails du contrôle personnalisé. Une fois l’espace de noms déclaré, le préfixe est utilisé pour référencer le contrôle personnalisé.

L’exemple de code suivant montre comment le contrôle personnalisé `HybridWebView` peut être consommé par une page C# :

```csharp
public class HybridWebViewPageCS : ContentPage
{
  public HybridWebViewPageCS ()
  {
    var hybridWebView = new HybridWebView {
      Uri = "index.html",
      HorizontalOptions = LayoutOptions.FillAndExpand,
      VerticalOptions = LayoutOptions.FillAndExpand
    };
    ...
    Padding = new Thickness (0, 20, 0, 0);
    Content = hybridWebView;
  }
}
```

L’instance `HybridWebView` est utilisée pour afficher un contrôle web natif sur chaque plateforme. Sa propriété `Uri` est définie sur un fichier HTML qui est stocké dans chaque projet spécifique à la plateforme et qui est affiché par le contrôle web natif. Le code HTML restitué invite l’utilisateur à entrer son nom, une fonction JavaScript appelant une `Action` C# en réponse à un clic de bouton HTML.

La `HybridWebViewPage` enregistre l’action à appeler à partir de JavaScript, comme illustré dans l’exemple de code suivant :

```csharp
public partial class HybridWebViewPage : ContentPage
{
  public HybridWebViewPage ()
  {
    ...
    hybridWebView.RegisterAction (data => DisplayAlert ("Alert", "Hello " + data, "OK"));
  }
}
```

Cette action appelle la méthode [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert(System.String,System.String,System.String))pour afficher une fenêtre contextuelle modale qui présente le nom entré dans la page HTML affichée par l’instance `HybridWebView`.

Un renderer personnalisé peut maintenant être ajouté à chaque projet d’application pour améliorer les contrôles spécifiques à la plateforme web en permettant à du code C# d’être appelé à partir de JavaScript.

## <a name="creating-the-custom-renderer-on-each-platform"></a>Création du renderer personnalisé sur chaque plateforme

Le processus de création de la classe de renderer personnalisé est le suivant :

1. Créez une sous-classe de la classe `ViewRenderer<T1,T2>` qui restitue le contrôle personnalisé. Le premier argument de type doit être le contrôle personnalisé auquel le renderer est destiné ; dans le cas présent, `HybridWebView`. Le deuxième argument de type doit être le contrôle natif destiné à implémenter la vue personnalisée.
1. Remplacez la méthode `OnElementChanged` qui restitue le contrôle personnalisé et écrivez une logique pour le personnaliser. Cette méthode est appelée durant la création du contrôle personnalisé Xamarin.Forms correspondant.
1. Ajoutez un attribut `ExportRenderer` à la classe de renderer personnalisé afin de spécifier qu’il sera utilisé pour restituer le contrôle personnalisé Xamarin.Forms. Cet attribut est utilisé pour inscrire le renderer personnalisé auprès de Xamarin.Forms.

> [!NOTE]
> Pour la plupart des éléments Xamarin.Forms, il est facultatif de fournir un renderer personnalisé dans chaque projet de plateforme. Si un renderer personnalisé n’est pas inscrit, le renderer par défaut de la classe de base du contrôle est utilisé. Toutefois, les renderers personnalisés sont nécessaires dans chaque projet de plateforme lors du rendu d’un élément [View](xref:Xamarin.Forms.View).

Le diagramme suivant illustre les responsabilités de chaque projet dans l’exemple d’application ainsi que les relations qu’ils entretiennent les uns avec les autres :

![](hybridwebview-images/solution-structure.png "Responsabilités du projet de renderer personnalisé HybridWebView")

Le contrôle personnalisé `HybridWebView` est restitué par des classes de renderer spécifiques à la plateforme qui dérivent toutes de la classe `ViewRenderer` pour chaque plateforme. Il en résulte la restitution de chaque contrôle personnalisé `HybridWebView` avec des contrôles web spécifiques à la plateforme, comme le montrent les captures d’écran suivantes :

![](hybridwebview-images/screenshots.png "HybridWebView sur chaque plateforme")

La classe `ViewRenderer` expose la méthode `OnElementChanged`, qui est appelée quand le contrôle personnalisé Xamarin.Forms est créé pour restituer le contrôle web natif correspondant. Cette méthode prend un paramètre `ElementChangedEventArgs` qui contient les propriétés `OldElement` et `NewElement`. Ces propriétés représentent respectivement l’élément Xamarin.Forms auquel le renderer *était* attaché et l’élément Xamarin.Forms auquel le renderer *est* attaché. Dans l’exemple d’application, la propriété `OldElement` sera `null` et la propriété `NewElement` contiendra une référence à l’instance `HybridWebView`.

Une version substituée de la méthode `OnElementChanged`, dans chaque classe de renderer spécifique à la plateforme, est l’emplacement où effectuer l’instanciation et la personnalisation du contrôle web natif. La méthode `SetNativeControl` doit être utilisée pour instancier le contrôle web natif, et cette méthode affecte également la référence de contrôle à la propriété `Control`. De plus, une référence au contrôle Xamarin.Forms qui est restitué peut être obtenue par le biais de la propriété `Element`.

Dans certains cas, la méthode `OnElementChanged` peut être appelée plusieurs fois. Ainsi, pour éviter les fuites de mémoire, vous devez être vigilant au moment de l’instanciation d’un nouveau contrôle natif. L’approche à utiliser lors de l’instanciation d’un nouveau contrôle natif dans un renderer personnalisé est indiquée dans l’exemple de code suivant :

```csharp
protected override void OnElementChanged (ElementChangedEventArgs<NativeListView> e)
{
  base.OnElementChanged (e);

  if (e.OldElement != null) {
    // Unsubscribe from event handlers and cleanup any resources
  }

  if (e.NewElement != null) {
    if (Control == null) {
      // Instantiate the native control and assign it to the Control property with
      // the SetNativeControl method
    }
    // Configure the control and subscribe to event handlers
  }
}
```

Un nouveau contrôle natif doit uniquement être instancié une seule fois, quand la propriété `Control` a la valeur `null`.  Par ailleurs, vous devez uniquement créer et configurer le contrôle et vous abonner aux gestionnaires d’événements quand le renderer personnalisé est attaché à un nouvel élément Xamarin.Forms. De même, vous devez vous désabonner des gestionnaires d’événements auxquels vous vous êtes abonné uniquement quand l’élément auquel le renderer est attaché change. Le choix de cette approche permet de créer un renderer personnalisé performant qui ne subit pas de fuites de mémoire.

> [!IMPORTANT]
> La méthode `SetNativeControl` doit uniquement être appelée si `e.NewElement` n’est pas `null`.

Chaque classe de renderer personnalisé est décorée avec un attribut `ExportRenderer` qui inscrit le renderer auprès de Xamarin.Forms. L’attribut accepte deux paramètres : le nom de type du contrôle personnalisé Xamarin.Forms en cours de restitution et le nom de type du renderer personnalisé. Le préfixe `assembly` de l’attribut spécifie que l’attribut s’applique à la totalité de l’assembly.

Les sections suivantes décrivent la structure de la page web chargée par chaque contrôle web natif, le processus permettant d’appeler C# à partir de JavaScript et son implémentation dans chaque classe de renderer personnalisé spécifique à la plateforme.

<a name="Creating_the_Web_Page" />

### <a name="creating-the-web-page"></a>Création de la page web

L’exemple de code suivant montre la page web affichée par le contrôle personnalisé `HybridWebView` :

```html
<html>
<body>
<script src="http://code.jquery.com/jquery-1.11.0.min.js"></script>
<h1>HybridWebView Test</h1>
<br/>
Enter name: <input type="text" id="name">
<br/>
<br/>
<button type="button" onclick="javascript:invokeCSCode($('#name').val());">Invoke C# Code</button>
<br/>
<p id="result">Result:</p>
<script type="text/javascript">
function log(str)
{
    $('#result').text($('#result').text() + " " + str);
}

function invokeCSCode(data) {
    try {
        log("Sending Data:" + data);
        invokeCSharpAction(data);
    }
    catch (err){
          log(err);
    }
}
</script>
</body>
</html>
```

La page web permet à un utilisateur d’entrer son nom dans un élément `input` et fournit un élément `button` qui appelle du code C# quand l’utilisateur clique cet élément. Le processus pour y parvenir est le suivant :

- Quand l’utilisateur clique sur l’élément `button`, la fonction JavaScript `invokeCSCode` est appelée et reçoit la valeur de l’élément `input`.
- La fonction `invokeCSCode` appelle la fonction `log` pour afficher les données qu’elle envoie à l’`Action` C#. Elle appelle ensuite la méthode `invokeCSharpAction` pour appeler l’`Action` C#, en transmettant le paramètre reçu de l’élément `input`.

La fonction JavaScript `invokeCSharpAction`n’est pas définie dans la page web ; elle y est injectée par chaque renderer personnalisé.

Sur iOS, ce fichier HTML réside dans le dossier Contenu du projet de plateforme, avec une action de build de **BundleResource**. Sur Android, ce fichier HTML réside dans le dossier Ressources/Contenu du projet de plateforme, avec une action de build de **AndroidAsset**.

<a name="Invoking_C_from_JavaScript" />

### <a name="invoking-c-from-javascript"></a>Appel de C# à partir de JavaScript

Le processus permettant d’appeler C# à partir de JavaScript est identique sur chaque plateforme :

- Le renderer personnalisé crée un contrôle web natif et charge le fichier HTML spécifié par la propriété `HybridWebView.Uri`.
- Une fois la page web chargée, le renderer personnalisé injecte la fonction JavaScript `invokeCSharpAction` dans la page web.
- Quand l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction `invokeCSCode` est appelée, qui appelle à son tour la fonction `invokeCSharpAction`.
- La fonction `invokeCSharpAction` appelle une méthode dans le renderer personnalisé, qui à son tour appelle la méthode `HybridWebView.InvokeAction`.
- La méthode `HybridWebView.InvokeAction` appelle l’`Action` inscrite.

Les sections suivantes expliquent comment ce processus est implémenté sur chaque plateforme.

### <a name="creating-the-custom-renderer-on-ios"></a>Création du renderer personnalisé sur iOS

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme iOS :

```csharp
[assembly: ExportRenderer (typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.iOS
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, WKWebView>, IWKScriptMessageHandler
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.webkit.messageHandlers.invokeAction.postMessage(data);}";
        WKUserContentController userController;

        protected override void OnElementChanged (ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged (e);

            if (e.OldElement != null) {
                userController.RemoveAllUserScripts ();
                userController.RemoveScriptMessageHandler ("invokeAction");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup ();
            }
            if (e.NewElement != null) {
                if (Control == null) {
                    userController = new WKUserContentController ();
                    var script = new WKUserScript (new NSString (JavaScriptFunction), WKUserScriptInjectionTime.AtDocumentEnd, false);
                    userController.AddUserScript (script);
                    userController.AddScriptMessageHandler (this, "invokeAction");

                    var config = new WKWebViewConfiguration { UserContentController = userController };
                    var webView = new WKWebView (Frame, config);
                    SetNativeControl (webView);
                }
                string fileName = Path.Combine (NSBundle.MainBundle.BundlePath, string.Format ("Content/{0}", Element.Uri));
                Control.LoadRequest (new NSUrlRequest (new NSUrl (fileName, false)));
            }
        }

        public void DidReceiveScriptMessage (WKUserContentController userContentController, WKScriptMessage message)
        {
            Element.InvokeAction (message.Body.ToString ());
        }
    }
}
```

La classe `HybridWebViewRenderer` charge la page web spécifiée dans la propriété `HybridWebView.Uri` dans un contrôle [`WKWebView`](xref:WebKit.WKWebView) natif, puis la fonction JavaScript `invokeCSharpAction` est injectée dans la page web. Une fois que l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction JavaScript `invokeCSharpAction` est exécutée, puis la méthode `DidReceiveScriptMessage` est appelée après la réception d’un message de la page web. À son tour, cette méthode appelle la méthode `HybridWebView.InvokeAction`, qui appelle l’action inscrite pour afficher la fenêtre contextuelle.

Cette fonctionnalité est obtenue comme suit :

- Sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :
  - Sous réserve que la propriété `Control` soit `null`, les opérations suivantes sont effectuées :
    - Une instance [`WKUserContentController`](xref:WebKit.WKUserContentController) est créée, ce qui permet de publier des messages et d’injecter des scripts utilisateur dans une page web.
    - Une instance [`WKUserScript`](xref:WebKit.WKUserScript) est créée pour injecter la fonction JavaScript `invokeCSharpAction` dans la page web une fois la page web chargée.
    - La méthode [`WKUserContentController.AddUserScript`](xref:WebKit.WKUserContentController.AddUserScript(WebKit.WKUserScript)) ajoute l’instance [`WKUserScript`](xref:WebKit.WKUserScript) au contrôleur de contenu.
    - La méthode [`WKUserContentController.AddScriptMessageHandler`](xref:WebKit.WKUserContentController.AddScriptMessageHandler(WebKit.IWKScriptMessageHandler,System.String)) ajoute un gestionnaire de messages de script nommé `invokeAction` à l’instance [`WKUserContentController`](xref:WebKit.WKUserContentController) ; ainsi, la fonction JavaScript `window.webkit.messageHandlers.invokeAction.postMessage(data)` est définie dans tous les cadres de toutes les vues web appelées à utiliser l’instance `WKUserContentController`.
    - Une instance [`WKWebViewConfiguration`](xref:WebKit.WKWebViewConfiguration) est créée, avec l’instance [`WKUserContentController`](xref:WebKit.WKUserContentController) définie en tant que contrôleur de contenu.
    - Un contrôle [`WKWebView`](xref:WebKit.WKWebView) est instancié, puis la méthode `SetNativeControl` est appelée pour affecter une référence au contrôle `WKWebView` à la propriété `Control`.
  - La méthode [`WKWebView.LoadRequest`](xref:WebKit.WKWebView.LoadRequest(Foundation.NSUrlRequest)) charge le fichier HTML qui est spécifié par la propriété `HybridWebView.Uri`. Le code spécifie que le fichier est stocké dans le dossier `Content` du projet. Une fois la page web affichée, la fonction JavaScript `invokeCSharpAction` y est injectée.
- Quand l’élément auquel le renderer est attaché change :
  - Les ressources sont libérées.

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

### <a name="creating-the-custom-renderer-on-android"></a>Création du renderer personnalisé sur Android

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme Android :

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.Droid
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Android.Webkit.WebView>
    {
        const string JavascriptFunction = "function invokeCSharpAction(data){jsBridge.invokeAction(data);}";
        Context _context;

        public HybridWebViewRenderer(Context context) : base(context)
        {
            _context = context;
        }

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.RemoveJavascriptInterface("jsBridge");
                var hybridWebView = e.OldElement as HybridWebView;
                hybridWebView.Cleanup();
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    var webView = new Android.Webkit.WebView(_context);
                    webView.Settings.JavaScriptEnabled = true;
                    webView.SetWebViewClient(new JavascriptWebViewClient($"javascript: {JavascriptFunction}"));
                    SetNativeControl(webView);
                }
                Control.AddJavascriptInterface(new JSBridge(this), "jsBridge");
                Control.LoadUrl($"file:///android_asset/Content/{Element.Uri}");
            }
        }
    }
}
```

La classe `HybridWebViewRenderer` charge la page web spécifiée dans la propriété `HybridWebView.Uri` dans un contrôle [`WebView`](xref:Android.Webkit.WebView) natif, puis la fonction JavaScript `invokeCSharpAction` est injectée dans la page web, avec la substitution de `OnPageFinished` dans la classe `JavascriptWebViewClient` :

```csharp
public class JavascriptWebViewClient : WebViewClient
{
    string _javascript;

    public JavascriptWebViewClient(string javascript)
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
  - Sous réserve que la propriété `Control` soit `null`, les opérations suivantes sont effectuées :
    - L’instance [`WebView`](xref:Android.Webkit.WebView) native est créée, JavaScript est activé dans le contrôle et une instance `JavascriptWebViewClient` est définie en tant qu’implémentation de `WebViewClient`.
    - La méthode `SetNativeControl` est appelée pour affecter une référence au contrôle [`WebView`](xref:Android.Webkit.WebView) natif à la propriété `Control`.
  - La méthode [`WebView.AddJavascriptInterface`](xref:Android.Webkit.WebView.AddJavascriptInterface*) injecte une nouvelle instance `JSBridge`, qu’elle nomme `jsBridge`, dans le cadre principal du contexte JavaScript de l’affichage web. Ainsi, les méthodes dans la classe `JSBridge` sont accessibles à partir de JavaScript.
  - La méthode [`WebView.LoadUrl`](xref:Android.Webkit.WebView.LoadUrl*) charge le fichier HTML qui est spécifié par la propriété `HybridWebView.Uri`. Le code spécifie que le fichier est stocké dans le dossier `Content` du projet.
  - Dans la classe `JavascriptWebViewClient`, la fonction JavaScript `invokeCSharpAction` est injectée dans la page web une fois celle-ci chargée.
- Quand l’élément auquel le renderer est attaché change :
  - Les ressources sont libérées.

Quand la fonction JavaScript `invokeCSharpAction` est exécutée, elle appelle à son tour la méthode `JSBridge.InvokeAction`, comme l’illustre l’exemple de code suivant :

```csharp
public class JSBridge : Java.Lang.Object
{
  readonly WeakReference<HybridWebViewRenderer> hybridWebViewRenderer;

  public JSBridge (HybridWebViewRenderer hybridRenderer)
  {
    hybridWebViewRenderer = new WeakReference <HybridWebViewRenderer> (hybridRenderer);
  }

  [JavascriptInterface]
  [Export ("invokeAction")]
  public void InvokeAction (string data)
  {
    HybridWebViewRenderer hybridRenderer;

    if (hybridWebViewRenderer != null && hybridWebViewRenderer.TryGetTarget (out hybridRenderer))
    {
      hybridRenderer.Element.InvokeAction (data);
    }
  }
}
```

La classe doit dériver de `Java.Lang.Object`, tandis que les méthodes qui sont exposées à JavaScript doivent être décorées avec les attributs `[JavascriptInterface]` et `[Export]`. Ainsi, quand la fonction JavaScript `invokeCSharpAction` est injectée dans la page web et est exécutée, elle appelle la méthode `JSBridge.InvokeAction`, car celle-ci est décorée avec les attributs `[JavascriptInterface]` et `[Export("invokeAction")]`. À son tour, la méthode `InvokeAction` appelle la méthode `HybridWebView.InvokeAction`, qui appelle l’action inscrite pour afficher la fenêtre contextuelle.

> [!NOTE]
> Les projets qui utilisent l’attribut `[Export]` doivent inclure une référence à `Mono.Android.Export`, sinon une erreur de compilation se produit.

Notez que la classe `JSBridge` conserve une `WeakReference` à la classe `HybridWebViewRenderer`, afin d’éviter la création d’une référence circulaire entre les deux classes. Pour plus d’informations, consultez [Références faibles](https://msdn.microsoft.com/library/ms404247(v=vs.110).aspx) sur MSDN.

### <a name="creating-the-custom-renderer-on-uwp"></a>Création du renderer personnalisé sur UWP

L’exemple de code suivant illustre le renderer personnalisé pour la plateforme UWP :

```csharp
[assembly: ExportRenderer(typeof(HybridWebView), typeof(HybridWebViewRenderer))]
namespace CustomRenderer.UWP
{
    public class HybridWebViewRenderer : ViewRenderer<HybridWebView, Windows.UI.Xaml.Controls.WebView>
    {
        const string JavaScriptFunction = "function invokeCSharpAction(data){window.external.notify(data);}";

        protected override void OnElementChanged(ElementChangedEventArgs<HybridWebView> e)
        {
            base.OnElementChanged(e);

            if (e.OldElement != null)
            {
                Control.NavigationCompleted -= OnWebViewNavigationCompleted;
                Control.ScriptNotify -= OnWebViewScriptNotify;
            }
            if (e.NewElement != null)
            {
                if (Control == null)
                {
                    SetNativeControl(new Windows.UI.Xaml.Controls.WebView());
                }
                Control.NavigationCompleted += OnWebViewNavigationCompleted;
                Control.ScriptNotify += OnWebViewScriptNotify;
                Control.Source = new Uri(string.Format("ms-appx-web:///Content//{0}", Element.Uri));
            }
        }

        async void OnWebViewNavigationCompleted(WebView sender, WebViewNavigationCompletedEventArgs args)
        {
            if (args.IsSuccess)
            {
                // Inject JS script
                await Control.InvokeScriptAsync("eval", new[] { JavaScriptFunction });
            }
        }

        void OnWebViewScriptNotify(object sender, NotifyEventArgs e)
        {
            Element.InvokeAction(e.Value);
        }
    }
}
```

La classe `HybridWebViewRenderer` charge la page web spécifiée dans la propriété `HybridWebView.Uri` dans un contrôle `WebView` natif, puis la fonction JavaScript `invokeCSharpAction` est injectée dans la page web, avec la méthode `WebView.InvokeScriptAsync`. Une fois que l’utilisateur entre son nom et clique sur l’élément `button` HTML, la fonction JavaScript `invokeCSharpAction` est exécutée, puis la méthode `OnWebViewScriptNotify` est appelée après la réception d’une notification de la page web. À son tour, cette méthode appelle la méthode `HybridWebView.InvokeAction`, qui appelle l’action inscrite pour afficher la fenêtre contextuelle.

Cette fonctionnalité est obtenue comme suit :

- Sous réserve que le renderer personnalisé soit attaché à un nouvel élément Xamarin.Forms :
  - Sous réserve que la propriété `Control` soit `null`, les opérations suivantes sont effectuées :
    - La méthode `SetNativeControl` est appelée pour instancier un nouveau contrôle `WebView` natif et affecter une référence à ce dernier à la propriété `Control`.
  - Des gestionnaires d’événements pour les événements `NavigationCompleted` et `ScriptNotify` sont inscrits. L’événement `NavigationCompleted` est déclenché quand le contrôle `WebView` natif a terminé de charger le contenu actuel ou si la navigation a échoué. L’événement `ScriptNotify` est déclenché quand le contenu du contrôle `WebView` natif utilise JavaScript pour passer une chaîne à l’application. La page web déclenche l’événement `ScriptNotify` en appelant `window.external.notify` tout en passant un paramètre `string`.
  - La propriété `WebView.Source` est définie sur l’URI du fichier HTML qui est spécifié par la propriété `HybridWebView.Uri`. Le code suppose que le fichier est stocké dans le dossier `Content` du projet. Une fois la page web affichée, l’événement `NavigationCompleted` est déclenché et la méthode `OnWebViewNavigationCompleted` est appelée. La fonction JavaScript `invokeCSharpAction` est ensuite injectée dans la page web avec la méthode `WebView.InvokeScriptAsync`, à condition que la navigation se soit déroulée correctement.
- Quand l’élément auquel le renderer est attaché change :
  - Les événements font l’objet d’un désabonnement.

## <a name="summary"></a>Récapitulatif

Cet article vous a montré comment créer un renderer personnalisé pour un contrôle personnalisé `HybridWebView`, qui montre comment améliorer les contrôles web propres à la plateforme web pour permettre d’appeler du code C# à partir de JavaScript.

## <a name="related-links"></a>Liens associés

- [CustomRendererHybridWebView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-hybridwebview)
- [Appeler C# à partir de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
