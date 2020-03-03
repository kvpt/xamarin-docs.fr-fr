---
title: Extensions de plateforme Xamarin.Essentials
description: Xamarin.Essentials fournit plusieurs méthodes d’extension de plateforme quand vous devez utiliser des types de plateforme comme Rect, Size et Point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
ms.openlocfilehash: 4e43159fb9cae6646be54d8efc24c334bc071477
ms.sourcegitcommit: fec87846fcb262fc8b79774a395908c8c8fc8f5b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77545148"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Extensions de plateforme

Xamarin.Essentials fournit plusieurs méthodes d’extension de plateforme quand vous devez utiliser des types de plateforme comme Rect, Size et Point. Cela signifie que vous pouvez convertir la version `System` de ces types en types spécifiques iOS, Android et UWP. 

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Utilisation d’extensions de plateforme

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Toutes les extensions de plateforme peuvent uniquement être appelées depuis le projet iOS, Android ou UWP.

## <a name="android-extensions"></a>Extensions Android

Ces extensions sont accessibles uniquement à partir d’un projet Android.

### <a name="application-context--activity"></a>Contexte et activité de l’application

À l’aide des extensions de plateforme de la classe `Platform`, vous pouvez accéder à `Context` ou à `Activity` actif pour l’application en cours d’exécution.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

Si `Activity` est nécessaire, mais que l’application n’a pas été entièrement démarrée, la méthode `WaitForActivityAsync` doit être utilisée.

```csharp
var activity = await Platform.WaitForActivityAsync();
```

### <a name="activity-lifecycle"></a>Cycle de vie des activités

Outre l’obtention de l’activité en cours, vous pouvez également vous inscrire aux événements de cycle de vie.

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

Les états d’activité sont les suivants :

* Création
* Repris
* Suspendu
* Détruit
* SaveInstanceState
* Démarré
* Arrêté

Pour en savoir plus, consultez la documentation relative au [cycle de vie de l’activité](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/).

## <a name="ios-extensions"></a>Extensions iOS

Ces extensions sont accessibles uniquement à partir d’un projet iOS.

### <a name="current-uiviewcontroller"></a>Current UIViewController

Accédez au `UIViewController` actuellement visible :

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Cette méthode retourne `null` s’il n’est pas possible de détecter un `UIViewController`.

## <a name="cross-platform-extensions"></a>Extensions multiplateforme

Ces extensions existent sur toutes les plateformes.

### <a name="point"></a>Point

```csharp
var system = new System.Drawing.Point(x, y);

// Convert to CoreGraphics.CGPoint, Android.Graphics.Point, and Windows.Foundation.Point
var platform = system.ToPlatformPoint();

// Back to System.Drawing.Point
var system2 = platform.ToSystemPoint();
```

### <a name="size"></a>Size

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
