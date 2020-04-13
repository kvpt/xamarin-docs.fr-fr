---
title: Extensions de plateforme Xamarin.Essentials
description: Xamarin.Essentials fournit plusieurs méthodes d’extension de plateforme quand vous devez utiliser des types de plateforme comme Rect, Size et Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77545148"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Extensions de la plate-forme

Xamarin.Essentials fournit plusieurs méthodes d’extension de plateforme quand vous devez utiliser des types de plateforme comme Rect, Size et Point. Cela signifie que vous pouvez convertir la version `System` de ces types en types spécifiques iOS, Android et UWP. 

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Utilisation d’extensions de plateforme

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Toutes les extensions de plateforme peuvent uniquement être appelées depuis le projet iOS, Android ou UWP.

## <a name="android-extensions"></a>Android Extensions

Ces extensions ne peuvent être consultées qu’à partir d’un projet Android.

### <a name="application-context--activity"></a>Contexte d’application & activité

En utilisant les extensions de la plate-forme dans la `Platform` classe, vous pouvez accéder au courant `Context` ou `Activity` à l’application en cours d’exécution.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

S’il y a `Activity` une situation où l’application est `WaitForActivityAsync` nécessaire, mais l’application n’a pas entièrement commencé alors la méthode doit être utilisée.

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>Cycle de vie des activités

En plus d’obtenir l’activité actuelle, vous pouvez également vous inscrire à des événements du cycle de vie.

```csharp
protected override void OnCreate(Bundle bundle)
{
    base.OnCreate(bundle);

    Xamarin.Essentials.Platform.Init(this, bundle);

    Xamarin.Essentials.Platform.ActivityStateChanged += Platform_ActivityStateChanged;
}

protected override void OnDestroy()
{
    base.OnDestroy();
    Xamarin.Essentials.Platform.ActivityStateChanged -= Platform_ActivityStateChanged;
}

void Platform_ActivityStateChanged(object sender, Xamarin.Essentials.ActivityStateChangedEventArgs e) =>
    Toast.MakeText(this, e.State.ToString(), ToastLength.Short).Show();
```

Les états d’activité sont les suivants :

* Date de création
* Repris
* Suspendu
* Détruit
* SaveInstanceState (en)
* Démarré
* Arrêté

Lisez la documentation sur le [cycle de vie](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) de l’activité pour en savoir plus.

## <a name="ios-extensions"></a>Extensions iOS

Ces extensions ne peuvent être accessibles qu’à partir d’un projet iOS.

### <a name="current-uiviewcontroller"></a>Current UIViewController

Accédez à l’actuel visible `UIViewController`:

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Cette méthode `null` sera de retour `UIViewController`si elle n’est pas en mesure de détecter un .

## <a name="cross-platform-extensions"></a>Extensions multiplateformes

Ces extensions existent dans toutes les plateformes.

### <a name="point"></a>Point

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>Taille

```csharp
var system = new System.Drawing.Size(width, height);

// Convert to CoreGraphics.CGSize, Android.Util.Size, and Windows.Foundation.Size
var platform = system.ToPlatformSize();

// Back to System.Drawing.Size
var system2 = platform.ToSystemSize();
```

### <a name="rectangle"></a>Rectangle

```csharp
var system = new System.Drawing.Rectangle(x, y, width, height);

// Convert to CoreGraphics.CGRect, Android.Graphics.Rect, and Windows.Foundation.Rect
var platform = system.ToPlatformRectangle();

// Back to System.Drawing.Rectangle
var system2 = platform.ToSystemRectangle();
```

## <a name="api"></a>API

- [Code source des convertisseurs](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentation sur les API de convertisseurs de points](xref:Xamarin.Essentials.PointExtensions)
- [Documentation de l’API de convertisseurs de rectangles](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentation sur les API de convertisseurs de tailles](xref:Xamarin.Essentials.SizeExtensions)
