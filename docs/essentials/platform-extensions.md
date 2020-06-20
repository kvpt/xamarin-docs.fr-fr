---
title: Xamarin.EssentialsExtensions de plateforme
description: Xamarin.Essentialsfournit plusieurs méthodes d’extension de plateforme lorsqu’il est nécessaire d’utiliser des types de plateforme tels que Rect, size et point.
ms.assetid: AB4D198A-4FD7-479E-8627-01F887A6D056
author: jamesmontemagno
ms.author: jamont
ms.date: 03/13/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 56cb9619a4132f6568cee8fbf590965934024639
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801907"
---
# <a name="xamarinessentials-platform-extensions"></a>Xamarin.Essentials: Extensions de plateforme

Xamarin.Essentialsfournit plusieurs méthodes d’extension de plateforme lorsqu’il est nécessaire d’utiliser des types de plateforme tels que Rect, size et point. Cela signifie que vous pouvez convertir la version `System` de ces types en types spécifiques iOS, Android et UWP.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-platform-extensions"></a>Utilisation d’extensions de plateforme

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Toutes les extensions de plateforme peuvent uniquement être appelées depuis le projet iOS, Android ou UWP.

## <a name="android-extensions"></a>Extensions Android

Ces extensions sont accessibles uniquement à partir d’un projet Android.

### <a name="application-context--activity"></a>Activité & contexte de l’application

À l’aide des extensions de plateforme de la `Platform` classe, vous pouvez accéder au en cours `Context` ou à `Activity` l’application en cours d’exécution.

```csharp

var context = Platform.AppContext;

// Current Activity or null if not initialized or not started.
var activity = Platform.CurrentActivity;
```

S’il existe une situation où le `Activity` est nécessaire, mais que l’application n’a pas démarré complètement, la `WaitForActivityAsync` méthode doit être utilisée.

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

Les États d’activité sont les suivants :

* Date de création
* Repris
* Suspendu
* Détruit
* SaveInstanceState
* Démarré
* Arrêté

Pour en savoir plus, consultez la documentation relative au [cycle de vie](https://docs.microsoft.com/xamarin/android/app-fundamentals/activity-lifecycle/) de l’activité.

## <a name="ios-extensions"></a>Extensions iOS

Ces extensions sont accessibles uniquement à partir d’un projet iOS.

### <a name="current-uiviewcontroller"></a>UIViewController actuel

Accédez à la vue actuellement visible `UIViewController` :

```csharp
var vc = Platform.GetCurrentUIViewController();
```

Cette méthode retournera s’il est `null` Impossible de détecter un `UIViewController` .

## <a name="cross-platform-extensions"></a>Extensions multiplateforme

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

- [Code source des convertisseurs](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Types/PlatformExtensions)
- [Documentation sur les API de convertisseurs de points](xref:Xamarin.Essentials.PointExtensions)
- [Documentation de l’API de convertisseurs de rectangles](xref:Xamarin.Essentials.RectangleExtensions)
- [Documentation sur les API de convertisseurs de tailles](xref:Xamarin.Essentials.SizeExtensions)
