---
title: 'Xamarin.Essentials: Actions de l’application'
description: La classe accéléromètre de vous Xamarin.Essentials permet de créer et de répondre aux raccourcis d’application à partir de l’icône de l’application.
ms.assetid: 5edf9bc5-b721-448c-a8a2-0a9d4d0c792c
author: jamesmontemagno
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8b1da7d07c042ff10b48948c4f411d65c6c05002
ms.sourcegitcommit: d4d293174a8324ce82b8f961ae6eadce294cafd7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98194885"
---
# <a name="no-locxamarinessentials-app-actions"></a>Xamarin.Essentials: Actions de l’application

La classe **AppActions** vous permet de créer et de répondre aux raccourcis d’application à partir de l’icône de l’application.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **AppActions** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

Ajoutez le filtre d’intention à votre `MainActivity` classe :

```csharp
[IntentFilter(
        new[] { Xamarin.Essentials.Platform.Intent.ActionAppAction },
        Categories = new[] { Android.Content.Intent.CategoryDefault })]
public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
{
    ...
```

Ajoutez ensuite la logique suivante pour gérer les actions :

```csharp
protected override void OnResume()
{
    base.OnResume();

    Xamarin.Essentials.Platform.OnResume(this);
}

protected override void OnNewIntent(Android.Content.Intent intent)
{
    base.OnNewIntent(intent);

    Xamarin.Essentials.Platform.OnNewIntent(intent);
}
```

# <a name="ios"></a>[iOS](#tab/ios)

Dans la, `AppDelegate.cs` Ajoutez la logique suivante pour gérer les actions :

```csharp
public override void PerformActionForShortcutItem(UIApplication application, UIApplicationShortcutItem shortcutItem, UIOperationHandler completionHandler)
    => Xamarin.Essentials.Platform.PerformActionForShortcutItem(application, shortcutItem, completionHandler);
```

# <a name="uwp"></a>[UWP](#tab/uwp)

Dans le `App.xaml.cs` fichier de la `OnLaunched` méthode, ajoutez la logique suivante au bas de la méthode :

```csharp
Xamarin.Essentials.Platform.OnLaunched(e);
```

-----

## <a name="create-actions"></a>Créer des actions

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```
Les actions d’application peuvent être créées à tout moment, mais elles sont souvent créées au démarrage d’une application. Appelez la `SetAsync` méthode pour créer la liste des actions pour votre application.


```csharp
try
{
    await AppActions.SetAsync(
        new AppAction("app_info", "App Info", icon: "app_info_action_icon"),
        new AppAction("battery_info", "Battery Info"));
}
catch (FeatureNotSupportedException ex)
{
    Debug.WriteLine("App Actions not supported");
}
```

Si les actions de l’application ne sont pas prises en charge sur la version spécifique du système d’exploitation `FeatureNotSupportedException` , une est levée. 

Les propriétés suivantes peuvent être définies sur un `AppAction` :

* ID : identificateur unique utilisé pour répondre au TAP de l’action.
* Title : titre visible à afficher.
* Sous-titre : si prend en charge un sous-titre à afficher sous le titre.
* Icon : doit correspondre aux icônes dans le répertoire Resources correspondant sur chaque plateforme.

![Actions de l’application sur homescreen](images/appactions.png)

## <a name="responding-to-actions"></a>Réponse aux actions

Lorsque votre application démarre, inscrivez-vous à l' `OnAppAction` événement. Quand vous sélectionnez une action d’application, l’événement est envoyé avec les informations relatives à l’action sélectionnée.

```csharp
public App()
{
    //...
    AppActions.OnAppAction += AppActions_OnAppAction;
}

void AppActions_OnAppAction(object sender, AppActionEventArgs e)
{
    // Don't handle events fired for old application instances
    // and cleanup the old instance's event handler
    if (Application.Current != this && Application.Current is App app)
    {
        AppActions.OnAppAction -= app.AppActions_OnAppAction;
        return;
    }
    MainThread.BeginInvokeOnMainThread(async () =>
    {
        await Shell.Current.GoToAsync($"//{e.AppAction.Id}");
    });
}
```

## <a name="getactions"></a>GetActions
Vous pouvez récupérer la liste actuelle des actions de l’application en appelant `AppActions.GetAsync()` .

## <a name="api"></a>API

- [Code source AppActions](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/AppActions)
- [Documentation de l’API AppActions](xref:Xamarin.Essentials.AppActions)
