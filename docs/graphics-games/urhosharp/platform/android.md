---
title: Prise en charge de UrhoSharp Android
description: Ce document décrit le programme d’installation spécifique à Android et les informations liées à la fonctionnalité de UrhoSharp. En particulier, il aborde les architectures prises en charge, la création d’un projet, en configurant et en lançant Urho et l’incorporation personnalisé de Urho.
ms.prod: xamarin
ms.assetid: 8409BD81-B1A6-4F5D-AE11-6BBD3F7C6327
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 3377602b8d793be8274ed150ebbd124c9d165e82
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67832493"
---
# <a name="urhosharp-android-support"></a>Prise en charge de UrhoSharp Android

_Fonctionnalités et le programme d’installation spécifique android_

Bien que Urho est une bibliothèque de classes portable, et permet à la même API pour être utilisé sur la plateforme différents pour votre logique de jeu, vous devez toujours initialiser Urho dans votre pilote de plateforme spécifique et dans certains cas, vous pouvez tirer parti des fonctionnalités spécifiques de plateforme .

Dans les pages ci-dessous, supposons que `MyGame` est une sous-classe de la `Application` classe.

## <a name="architectures"></a>Architectures

**Architectures prises en charge**: x86, armeabi, armeabi-v7a

## <a name="create-a-project"></a>Création d’un projet

Créez un projet Android et ajoutez le package NuGet de UrhoSharp.

Ajouter des données qui contient vos éléments multimédias à la **actifs** directory et assurez-vous que tous les fichiers ont **AndroidAsset** en tant que le **Action de génération**.

![Le programme d’installation de projet](android-images/image-3.png "ajouter des données qui contient les ressources dans le répertoire de ressources")

## <a name="configure-and-launching-urho"></a>Configurer et de lancement Urho

Ajouter à l’aide des instructions pour la `Urho` et `Urho.Android` espaces de noms, puis ajoutez ce code pour l’initialisation Urho, ainsi que le lancement de votre application.

La façon la plus simple d’exécuter un jeu, tel qu’implémenté dans la classe MyGame consiste à appeler

```csharp
UrhoSurface.RunInActivity<MyGame>();
```

Une activité en plein écran s’ouvre avec le jeu en tant qu’un contenu.

## <a name="custom-embedding-of-urho"></a>Incorporation personnalisé de Urho

Vous peut également avoir Urho reprendre l’écran de l’application entière, et pour l’utiliser en tant que composant de votre application, vous pouvez créer un `SurfaceView` via :

```csharp
var surface = UrhoSurface.CreateSurface<MyGame>(activity)
```

Vous devrez également transmettre les quelques événements de votre activité à UrhoSharp, par exemple :

```csharp
protected override void OnPause()
{
    UrhoSurface.OnPause();
    base.OnPause();
}
```

Vous devez faire de même : `OnResume`, `OnPause`, `OnLowMemory`, `OnDestroy`, `DispatchKeyEvent` et `OnWindowFocusChanged`.

Cet exemple montre une activité typique qui lance le jeu :

```csharp
[Activity(Label = "MyUrhoApp", MainLauncher = true,
    Icon = "@drawable/icon", Theme = "@android:style/Theme.NoTitleBar.Fullscreen",
    ConfigurationChanges = ConfigChanges.KeyboardHidden | ConfigChanges.Orientation,
    ScreenOrientation = ScreenOrientation.Portrait)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle bundle)
    {
        base.OnCreate(bundle);
        var mLayout = new AbsoluteLayout(this);
        var surface = UrhoSurface.CreateSurface<MyUrhoApp>(this);
        mLayout.AddView(surface);
        SetContentView(mLayout);
    }

    protected override void OnResume()
    {
        UrhoSurface.OnResume();
        base.OnResume();
    }

    protected override void OnPause()
    {
        UrhoSurface.OnPause();
        base.OnPause();
    }

    public override void OnLowMemory()
    {
        UrhoSurface.OnLowMemory();
        base.OnLowMemory();
    }

    protected override void OnDestroy()
    {
        UrhoSurface.OnDestroy();
        base.OnDestroy();
    }

    public override bool DispatchKeyEvent(KeyEvent e)
    {
        if (!UrhoSurface.DispatchKeyEvent(e))
            return false;
        return base.DispatchKeyEvent(e);
    }

    public override void OnWindowFocusChanged(bool hasFocus)
    {
        UrhoSurface.OnWindowFocusChanged(hasFocus);
        base.OnWindowFocusChanged(hasFocus);
    }
}
```
