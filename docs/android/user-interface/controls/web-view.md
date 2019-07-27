---
title: Affichage Web
ms.prod: xamarin
ms.assetid: 807F214A-166D-B342-0BBA-525517577F6B
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/06/2018
ms.openlocfilehash: 2718953c9e5628374c45fa3741d1ad3be3125dd9
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510147"
---
# <a name="xamarinandroid-web-view"></a>Vue Web Xamarin. Android

[`WebView`](xref:Android.Webkit.WebView)vous permet de créer votre propre fenêtre pour afficher des pages Web (ou même développer un navigateur complet). Dans ce didacticiel, vous allez créer un simple[`Activity`](xref:Android.App.Activity)
qui peut afficher et parcourir des pages Web.

Créez un nouveau projet nommé **HelloWebView**.

Ouvrez **ressources/mise en page/main. AXML** et insérez ce qui suit:

```xml
<?xml version="1.0" encoding="utf-8"?>
<WebView  xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/webview"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent" />
```

Étant donné que cette application va accéder à Internet, vous devez ajouter les autorisations appropriées au fichier manifeste Android. Ouvrez les propriétés de votre projet pour spécifier les autorisations dont votre application a besoin pour fonctionner. Activez l' `INTERNET` autorisation, comme indiqué ci-dessous:

![Définition de l’autorisation INTERNET dans le manifeste Android](web-view-images/01-set-internet-permissions.png)

À présent, ouvrez **MainActivity.cs** et ajoutez une directive using pour WebKit:

```csharp
using Android.Webkit;
```

En haut de la `MainActivity` classe, déclarez un [`WebView`](xref:Android.Webkit.WebView) objet:

```csharp
WebView web_view;
```

Quand la **WebView** est invitée à charger une URL, elle délègue par défaut la demande au navigateur par défaut. Pour que la **WebView** charge l’URL (plutôt que le navigateur par défaut), vous devez effectuer `Android.Webkit.WebViewClient` une sous-classe `ShouldOverriderUrlLoading` et substituer la méthode. Une instance de ce personnalisé `WebViewClient` est fournie `WebView`à. Pour ce faire, ajoutez la `HelloWebViewClient` classe imbriquée suivante à l’intérieur `MainActivity`de:

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

Lorsque `ShouldOverrideUrlLoading` `WebView` retourne `false`, il signale à Android que l’instance actuelle a géré la demande et qu’aucune action supplémentaire n’est nécessaire. 

Si vous ciblez le niveau d’API 24 ou ultérieur, utilisez la `ShouldOverrideUrlLoading` surcharge de qui `IWebResourceRequest` accepte un pour le deuxième argument au `string`lieu d’un:

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

Ensuite, utilisez le code suivant pour la [`OnCreate()`](xref:Android.App.Activity.OnCreate*)méthode):

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

[`WebView`](xref:Android.Webkit.WebView) Cela Initialise le membre avec celui de la [`Activity`](xref:Android.App.Activity) disposition et active JavaScript pour le [`WebView`](xref:Android.Webkit.WebView) avec `= true` [`JavaScriptEnabled`](xref:Android.Webkit.WebSettings.JavaScriptEnabled) 
 (consultez l' [appel C\# à partir de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript) recette pour obtenir des informations sur la façon\# d’appeler des fonctions C à partir de JavaScript). Enfin, une page Web initiale est chargée avec [`LoadUrl(String)`](xref:Android.Webkit.WebView).

Générez et exécutez l’application. Vous devez voir une application Web page Viewer simple comme celle illustrée dans la capture d’écran suivante:

[![Exemple d’application affichant une WebView](web-view-images/02-simple-webview-app-sml.png)](web-view-images/02-simple-webview-app.png#lightbox)

Pour gérer l’appui sur la touche du bouton **précédent** , ajoutez l’instruction using suivante:

```csharp
using Android.Views;
```

Ensuite, ajoutez la méthode suivante à l' `HelloWebView` intérieur de l’activité:

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

Voici[`OnKeyDown(int, KeyEvent)`](xref:Android.App.Activity.OnKeyDown*)
la méthode de rappel est appelée chaque fois qu’un bouton est enfoncé pendant que l’activité est en cours d’exécution. La condition à l’intérieur [`KeyEvent`](xref:Android.Views.KeyEvent) de utilise le pour vérifier si la touche enfoncée est le [`WebView`](xref:Android.Webkit.WebView) bouton **précédent** et si le peut effectivement naviguer vers l’arrière (s’il possède un historique). Si les deux ont la valeur true [`GoBack()`](xref:Android.Webkit.WebView.GoBack) , la méthode est appelée, ce qui permet de remonter d' [`WebView`](xref:Android.Webkit.WebView) une étape dans l’historique. Le `true` retour de indique que l’événement a été géré. Si cette condition n’est pas remplie, l’événement est renvoyé au système.

Exécutez de nouveau l'application. Vous devez maintenant être en mesure de suivre les liens et de revenir en arrière dans l’historique des pages:

[![Exemples de captures d’écran du bouton précédent en action](web-view-images/03-back-button-sml.png)](web-view-images/03-back-button.png#lightbox)

*Certaines parties de cette page sont des modifications basées sur le travail créé et partagé par le projet open source Android et utilisées conformément aux termes décrits dans la*
[*licence d’attribution de Creative-2,5*](http://creativecommons.org/licenses/by/2.5/).

## <a name="related-links"></a>Liens associés

- [Appeler C# à partir de JavaScript](https://github.com/xamarin/recipes/tree/master/Recipes/android/controls/webview/call_csharp_from_javascript)
- [Android.Webkit.WebView](xref:Android.Webkit.WebView)
- [KeyEvent](xref:Android.Webkit.WebView)
