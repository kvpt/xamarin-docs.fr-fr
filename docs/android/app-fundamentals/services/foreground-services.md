---
title: Services de premier plan
ms.prod: xamarin
ms.assetid: C10FD999-7A91-4708-B642-0C1B0901BD24
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/19/2018
ms.openlocfilehash: d8230973c76aad4ae5ef4db105d2562d34c27489
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68509237"
---
# <a name="foreground-services"></a>Services de premier plan

Un service de premier plan est un type spécial d’un service lié ou d’un service démarré. Parfois, les services effectuent des tâches que les utilisateurs doivent connaître activement, ces services sont appelés _services de premier plan_. Un exemple de service de premier plan est une application qui fournit à l’utilisateur des instructions pour la conduite ou la marche. Même si l’application est en arrière-plan, il est toujours important que le service dispose de suffisamment de ressources pour fonctionner correctement et que l’utilisateur dispose d’un moyen rapide et pratique d’accéder à l’application. Pour une application Android, cela signifie qu’un service de premier plan doit recevoir une priorité plus élevée qu’un service «normal» et un service `Notification` de premier plan doit fournir un que Android affichera tant que le service est en cours d’exécution.

Pour démarrer un service de premier plan, l’application doit distribuer un objectif qui indique à Android de démarrer le service. Ensuite, le service doit s’inscrire lui-même en tant que service de premier plan avec Android. Les applications qui s’exécutent sur Android 8,0 (ou version ultérieure) `Context.StartForegroundService` doivent utiliser la méthode pour démarrer le service, tandis que les applications qui s’exécutent sur des appareils avec une version antérieure d’Android doivent utiliser`Context.StartService`

Cette C# méthode d’extension est un exemple de démarrage d’un service de premier plan. Sur Android 8,0 et versions ultérieures, il `StartForegroundService` utilisera la méthode; `StartService` sinon, l’ancienne méthode sera utilisée.

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

## <a name="registering-as-a-foreground-service"></a>Inscription en tant que service de premier plan

Une fois qu’un service de premier plan a démarré, il doit s’inscrire auprès d' [`StartForeground`](xref:Android.App.Service.StartForeground*)Android en appelant le. Si le service est démarré avec la `Service.StartForegroundService` méthode mais ne s’inscrit pas, Android arrête le service et marque l’application comme non réactive.

`StartForeground`prend deux paramètres, tous deux obligatoires:

- Valeur entière qui est unique dans l’application pour identifier le service.
- `Notification` Objet qu’Android affiche dans la barre d’état aussi longtemps que le service est en cours d’exécution.

Android affiche la notification dans la barre d’État pour tant que le service est en cours d’exécution. La notification, au minimum, fournira à l’utilisateur un signal visuel indiquant que le service est en cours d’exécution. Dans l’idéal, la notification doit fournir à l’utilisateur un raccourci vers l’application ou éventuellement des boutons d’action pour contrôler l’application. Par exemple, un lecteur &ndash; de musique, la notification qui s’affiche peut comporter des boutons pour suspendre/écouter de la musique, pour revenir à la chanson précédente ou pour passer à la chanson suivante. 

Cet extrait de code est un exemple d’inscription d’un service en tant que service de premier plan:   

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

La notification précédente affiche une notification de barre d’état semblable à la suivante:

![Image représentant la notification dans la barre d’État](foreground-services-images/foreground-services-01.png "Image représentant la notification dans la barre d’État")

Cette capture d’écran montre la notification étendue dans le tiroir de notification avec deux actions qui permettent à l’utilisateur de contrôler le service:

![Image représentant la notification développée](foreground-services-images/foreground-services-02.png "Image représentant la notification développée.")

Vous trouverez plus d’informations sur les notifications dans la section [notifications locales](~/android/app-fundamentals/notifications/local-notifications.md) du Guide des [notifications Android](~/android/app-fundamentals/notifications/index.md) .

## <a name="unregistering-as-a-foreground-service"></a>Annulation de l’inscription en tant que service de premier plan

Un service peut se délister lui-même en tant que service de `StopForeground`premier plan en appelant la méthode. `StopForeground`n’arrête pas le service, mais supprime l’icône de notification et signale à Android que ce service peut être arrêté si nécessaire.

La notification de la barre d’État qui s’affiche peut également être `true` supprimée en passant à la méthode: 

```csharp
StopForeground(true);
```

Si le service est arrêté à l’aide d’un `StopSelf` appel `StopService`à ou, la notification de la barre d’État sera supprimée.

## <a name="related-links"></a>Liens associés

- [Android.App.Service](xref:Android.App.Service)
- [Android.App.Service.StartForeground](xref:Android.App.Service.StartForeground*)
- [Notifications locales](~/android/app-fundamentals/notifications/local-notifications.md)
- [ForegroundServiceDemo (exemple)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/ForegroundServiceDemo/)
