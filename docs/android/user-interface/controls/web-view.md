---
title: Affichage Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/06/2018
ms.openlocfilehash: c1fcb16bd40b818b27b57b877534e051a789a6c9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029019"
---
# <a name="xamarinandroid-web-view"></a>Vue Web Xamarin. Android

[`WebView`](xref:Android.Webkit.WebView) vous permet de créer votre propre fenêtre pour afficher des pages Web (ou même développer un navigateur complet). Dans ce didacticiel, vous allez créer un [`Activity`](xref:Android.App.Activity) simple
qui peut afficher et parcourir des pages Web.

Créez un nouveau projet nommé **HelloWebView**.

Ouvrez **ressources/mise en page/main. AXML** et insérez ce qui suit :

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Étant donné que cette application va accéder à Internet, vous devez ajouter les autorisations appropriées au fichier manifeste Android. Ouvrez les propriétés de votre projet pour spécifier les autorisations dont votre application a besoin pour fonctionner. Activez l’autorisation `INTERNET` comme indiqué ci-dessous :

![Définition de l’autorisation INTERNET dans le manifeste Android](web-view-images/01-set-internet-permissions.png)

À présent, ouvrez **MainActivity.cs** et ajoutez une directive using pour WebKit :

```csharp
using Android.Webkit;
```

En haut de la classe `MainActivity`, déclarez un objet [`WebView`](xref:Android.Webkit.WebView) :

```csharp
WebView web_view;
```

Quand la **WebView** est invitée à charger une URL, elle délègue par défaut la demande au navigateur par défaut. Pour que la **WebView** charge l’URL (plutôt que le navigateur par défaut), vous devez sous-classe `Android.Webkit.WebViewClient` et remplacer la méthode `ShouldOverriderUrlLoading`. Une instance de cette `WebViewClient` personnalisée est fournie au `WebView`. Pour ce faire, ajoutez la classe de `HelloWebViewClient` imbriquée suivante dans `MainActivity`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    public override bool ShouldOverrideUrlLoading (WebView view, string url)
    {
        view.LoadUrl(url);
        return false;
    }
}
```

Lorsque `ShouldOverrideUrlLoading` retourne `false`, il signale à Android que l’instance `WebView` actuelle a géré la demande et qu’aucune action supplémentaire n’est nécessaire. 

Si vous ciblez le niveau d’API 24 ou ultérieur, utilisez la surcharge de `ShouldOverrideUrlLoading` qui prend un `IWebResourceRequest` pour le deuxième argument au lieu d’un `string`:

```csharp
public class HelloWebViewClient : WebViewClient
{
    // For API level 24 and later
    public override bool ShouldOverrideUrlLoading (WebView view, IWebResourceRequest request)
    {
        view.LoadUrl(request.Url.ToString());
        return false;
    }
}
```

Ensuite, utilisez le code suivant pour la méthode [`OnCreate()`](xref:Android.App.Activity.OnCreate*)) :

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    // Set our view from the "main" layout resource
    SetContentView (Resource.Layout.Main);

    web_view = FindViewById<WebView> (Resource.Id.webview);
    web_view.Settings.JavaScriptEnabled = true;
    web_view.SetWebViewClient(new HelloWebViewClient());
    web_view.LoadUrl ("https://www.xamarin.com/university");
}
```

Cela initialise le membre [`WebView`](xref:Android.Webkit.WebView) avec celui de la disposition [`Activity`](xref:Android.App.Activity) et active JavaScript pour le [`WebView`](xref:Android.Webkit.WebView) avec [`JavaScriptEnabled`](xref:Android.Webkit.WebSettings.JavaScriptEnabled)
`= true` (consultez l' [appel C\# from JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) Recipe pour informations sur l’appel des fonctions C\# à partir de JavaScript). Enfin, une page Web initiale est chargée avec [`LoadUrl(String)`](xref:Android.Webkit.WebView).

Générez et exécutez l’application. Vous devez voir une application Web page Viewer simple comme celle illustrée dans la capture d’écran suivante :

[![exemple d’application affichant une WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Pour gérer l’appui sur la touche du bouton **précédent** , ajoutez l’instruction using suivante :

```csharp
using Android.Views;
```

Ensuite, ajoutez la méthode suivante à l’intérieur de l’activité `HelloWebView` :

```csharp
public override bool OnKeyDown (Android.Views.Keycode keyCode, Android.Views.KeyEvent e)
{
    if (keyCode == Keycode.Back && web_view.CanGoBack ())
    {
        web_view.GoBack ();
        return true;
    }
    return base.OnKeyDown (keyCode, e);
}
```

Ce [`OnKeyDown(int, KeyEvent)`](xref:Android.App.Activity.OnKeyDown*)
la méthode de rappel est appelée chaque fois qu’un bouton est enfoncé pendant que l’activité est en cours d’exécution. La condition à l’intérieur de utilise la [`KeyEvent`](xref:Android.Views.KeyEvent) pour vérifier si la touche enfoncée est le bouton **précédent** et si le [`WebView`](xref:Android.Webkit.WebView) est effectivement en capacité de naviguer vers l’arrière (s’il possède un historique). Si les deux ont la valeur true, la méthode [`GoBack()`](xref:Android.Webkit.WebView.GoBack) est appelée, ce qui permet d’accéder en arrière-plan d’une étape dans l’historique des [`WebView`](xref:Android.Webkit.WebView) . Le retour de `true` indique que l’événement a été géré. Si cette condition n’est pas remplie, l’événement est renvoyé au système.

Exécutez de nouveau l'application. Vous devez maintenant être en mesure de suivre les liens et de revenir en arrière dans l’historique des pages :

[![des exemples de captures d’écran du bouton précédent en action](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](https://creativecommons.org/licenses/by/2.5/).

## <a name="related-links"></a>Liens associés

- [Appeler C# à partir de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Android. WebKit. WebView](xref:Android.Webkit.WebView)
- [KeyEvent](xref:Android.Webkit.WebView)
