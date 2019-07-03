---
title: Services de premier plan
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: b1cc043ac94fb48e3e7fe6b7ba647cfb19a4c7eb
ms.sourcegitcommit: 450106d5f05b4473bf7f5b9100b2eaf18c9110de
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67522936"
---
# <a name="foreground-services"></a>Services de premier plan

Un service de premier plan est un type spécial d’un service lié ou d’un service démarré. Occasionnellement services effectue les tâches que les utilisateurs doivent connaître activement, ces services sont appelés _des services de premier plan_. Un exemple d’un service de premier plan est une application qui fournit l’utilisateur directions lors de la voiture ou à pied. Même si l’application est en arrière-plan, il est toujours important que le service a suffisamment de ressources pour fonctionner correctement et que l’utilisateur dispose d’un moyen rapide et pratique pour accéder à l’application. Pour une application Android, cela signifie qu’un service de premier plan ne doit recevoir une priorité plus élevée qu’un service « standard » et un service de premier plan doit fournir un `Notification` que Android affichera tant que le service est en cours d’exécution.
 
Pour démarrer un service de premier plan, l’application doit distribuer une intention qui indique à Android pour démarrer le service. Le service doit ensuite s’auto-enregistrer comme un service de premier plan avec Android. Applications qui s’exécutent sur Android 8.0 (ou version ultérieure) doivent utiliser le `Context.StartForegroundService` pour démarrer le service, tandis que les applications qui s’exécutent sur des appareils avec une version antérieure d’Android doivent utiliser (méthode) `Context.StartService`

Cette méthode d’extension c# est un exemple montrant comment démarrer un service de premier plan. Sur Android 8.0 et versions ultérieures, il utilise le `StartForegroundService` (méthode), sinon l’ancien `StartService` méthode sera utilisée.  

```csharp
public static void StartForegroundServiceCompat<T>(this Context context, Bundle args = null) where T : Service
{
    var intent = new Intent(context, typeof(T));
    if (args != null) 
    {
        intent.PutExtras(args);
    }

    if (Android.OS.Build.VERSION.SdkInt >= Android.OS.BuildVersionCodes.O)
    {
        context.StartForegroundService(intent);
    }
    else
    {
        context.StartService(intent);
    }
}
```

## <a name="registering-as-a-foreground-service"></a>L’inscription d’un Service de premier plan

Une fois un service de premier plan a démarré, il doit s’inscrire auprès d’Android en appelant le [ `StartForeground` ](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/). Si le service est démarré avec le `Service.StartForegroundService` méthode mais ne pas s’inscrire, puis Android arrête le service et marquer l’application comme non réactif.

`StartForeground` accepte deux paramètres, qui sont tous deux obligatoires :
 
* Valeur entière qui est unique au sein de l’application pour identifier le service.
* Un `Notification` objet affiche dans la barre d’état pour Android, que le service est en cours d’exécution.

Android la notification s’affiche dans la barre d’état pour tant que le service est en cours d’exécution. La notification, au minimum, fournira une aide visuelle à l’utilisateur que le service est en cours d’exécution. Dans l’idéal, la notification doit fournir à l’utilisateur d’un raccourci vers l’application ou éventuellement des boutons d’action pour contrôler l’application. Un exemple de ceci est un lecteur de musique &ndash; la notification affichée peut-être comporter des boutons à pause/lecture de musique, de rembobiner la chanson précédente, ou ignorer le morceau suivant. 

Cet extrait de code est un exemple de l’inscription d’un service comme un service de premier plan :   

```csharp
// This is any integer value unique to the application.
public const int SERVICE_RUNNING_NOTIFICATION_ID = 10000;

public override StartCommandResult OnStartCommand(Intent intent, StartCommandFlags flags, int startId)
{
    // Code not directly related to publishing the notification has been omitted for clarity.
    // Normally, this method would hold the code to be run when the service is started.
    
    var notification = new Notification.Builder(this)
        .SetContentTitle(Resources.GetString(Resource.String.app_name))
        .SetContentText(Resources.GetString(Resource.String.notification_text))
        .SetSmallIcon(Resource.Drawable.ic_stat_name)
        .SetContentIntent(BuildIntentToShowMainActivity())
        .SetOngoing(true)
        .AddAction(BuildRestartTimerAction())
        .AddAction(BuildStopServiceAction())
        .Build();

    // Enlist this instance of the service as a foreground service
    StartForeground(SERVICE_RUNNING_NOTIFICATION_ID, notification);
}
```

La notification précédente affiche une notification de barre d’état qui est similaire à ce qui suit :

![Image montrant la notification dans la barre d’état](foreground-services-images/foreground-services-01.png "Image montrant la notification dans la barre d’état")

Cette capture d’écran montrant la notification développée dans la barre d’état de notification avec deux actions qui lui permet de contrôler le service :

![Image montrant la notification développée](foreground-services-images/foreground-services-02.png "Image montrant la notification développée.")

Plus d’informations sur les notifications sont disponibles dans le [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md) section de la [Notifications Android](~/android/app-fundamentals/notifications/index.md) guide.

## <a name="unregistering-as-a-foreground-service"></a>Annulation de l’inscription en tant qu’un Service de premier plan

Un service peut retirer répertorié comme un service de premier plan en appelant la méthode `StopForeground`. `StopForeground` n’arrêtera pas le service, mais il supprime l’icône de notification et des signaux Android ce service peut être arrêtée si nécessaire.

La notification de barre d’état qui s’affiche peut également être supprimée en passant `true` à la méthode : 

```csharp
StopForeground(true);
```

Si le service est interrompu par un appel à `StopSelf` ou `StopService`, la notification de barre d’état va être supprimée.

## <a name="related-links"></a>Liens associés

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.App.Service.StartForeground](https://developer.xamarin.com/api/member/Android.App.Service.StartForeground/p/System.Int32/Android.App.Notification/)
- [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
