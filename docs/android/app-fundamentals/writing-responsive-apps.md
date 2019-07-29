---
title: Écriture d’applications réactives
ms.prod: xamarin
ms.assetid: 452DF940-6331-55F0-D130-002822BBED55
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/15/2018
ms.openlocfilehash: e3f7d788e71718f4ca1336b7906cf3d63bf07f32
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509200"
---
# <a name="writing-responsive-applications"></a>Écriture d’applications réactives

L’une des clés de la gestion d’une interface graphique réactive consiste à effectuer des tâches de longue durée sur un thread d’arrière-plan pour que l’interface utilisateur ne soit pas bloquée. Supposons que nous voulons calculer une valeur à afficher à l’utilisateur, mais cette valeur prend 5 secondes pour calculer:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        SlowMethod ();
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Cela fonctionne, mais l’application se bloque pendant 5 secondes, tandis que la valeur est calculée. Pendant ce temps, l’application ne répond à aucune intervention de l’utilisateur. Pour contourner ce processus, nous voulons effectuer nos calculs sur un thread d’arrière-plan:

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        textview.Text = "Method Complete";
    }
}
```

Nous calculons à présent la valeur sur un thread d’arrière-plan afin que notre interface utilisateur reste réactive pendant le calcul. Toutefois, lorsque le calcul est effectué, notre application se bloque, laissant cela dans le journal:

```shell
E/mono    (11207): EXCEPTION handling: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):
E/mono    (11207): Unhandled Exception: Android.Util.AndroidRuntimeException: Exception of type 'Android.Util.AndroidRuntimeException' was thrown.
E/mono    (11207):   at Android.Runtime.JNIEnv.CallVoidMethod (IntPtr jobject, IntPtr jmethod, Android.Runtime.JValue[] parms)
E/mono    (11207):   at Android.Widget.TextView.set_Text (IEnumerable`1 value)
E/mono    (11207):   at MonoDroidDebugging.Activity1.SlowMethod ()
```

Cela est dû au fait que vous devez mettre à jour l’interface utilisateur graphique à partir du thread d’interface utilisateur. Notre code met à jour l’interface utilisateur graphique à partir du thread ThreadPool, provoquant l’arrêt de l’application. Nous devons calculer notre valeur sur le thread d’arrière-plan, puis effectuer notre mise à jour sur le thread d’interface utilisateur, qui est géré avec [Activity. RunOnUIThread](xref:Android.App.Activity.RunOnUiThread*):

```csharp
public class ThreadDemo : Activity
{
    TextView textview;

    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        // Create a new TextView and set it as our view
        textview = new TextView (this);
        textview.Text = "Working..";

        SetContentView (textview);

        ThreadPool.QueueUserWorkItem (o => SlowMethod ());
    }

    private void SlowMethod ()
    {
        Thread.Sleep (5000);
        RunOnUiThread (() => textview.Text = "Method Complete");
    }
}
```

Ce code fonctionne comme prévu. Cette interface GUI reste réactive et est correctement mise à jour une fois que le calcul est comple.

Notez que cette technique n’est pas utilisée uniquement pour calculer une valeur coûteuse. Il peut être utilisé pour toute tâche de longue durée qui peut être effectuée en arrière-plan, comme un appel de service Web ou le téléchargement de données Internet.
