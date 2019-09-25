---
title: Services démarrés avec Xamarin. Android
ms.prod: xamarin
ms.assetid: 8CC3A850-4CD2-4F93-98EE-AF3470794000
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 1b7bed0fc6dba1d9f80524ac3429b7fdcb751ab9
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "70755068"
---
# <a name="started-services-with-xamarinandroid"></a>Services démarrés avec Xamarin. Android

## <a name="started-services-overview"></a>Présentation des services démarrée

Les services démarrés exécutent généralement une unité de travail sans fournir de commentaires directs ou de résultats au client. Un exemple d’unité de travail est un service qui charge un fichier sur un serveur. Le client envoie une demande à un service pour télécharger un fichier à partir de l’appareil vers un site Web. Le service charge le fichier en mode silencieux (même si l’application n’a pas d’activités au premier plan) et se termine une fois le téléchargement terminé. Il est important de comprendre qu’un service démarré s’exécutera sur le thread d’interface utilisateur d’une application. Cela signifie que si un service doit effectuer un travail qui bloquera le thread d’interface utilisateur, il doit créer et supprimer des threads si nécessaire.

Contrairement à un service lié, aucun canal de communication n’est présent entre un service « pur » démarré et ses clients. Cela signifie qu’un service démarré implémente des méthodes de cycle de vie différentes par rapport à un service lié. La liste suivante met en évidence les méthodes de cycle de vie courantes dans un service démarré :

- `OnCreate`&ndash; Appelée une fois lorsque le service est démarré pour la première fois. C’est là que le code d’initialisation doit être implémenté.
- `OnBind`&ndash; Cette méthode doit être implémentée par toutes les classes de service, mais un service démarré n’a généralement pas de client associé. Pour cette raison, un service démarré retourne `null`simplement. En revanche, un service hybride (qui est la combinaison d’un service lié et d’un service démarré) doit implémenter et retourner `Binder` un pour le client.
- `OnStartCommand`Appelé pour chaque demande de démarrage du service, en réponse à un appel à `StartService` ou à un redémarrage par le système. &ndash; C’est là que le service peut commencer toute tâche de longue durée. La méthode retourne une `StartCommandResult` valeur qui indique comment ou si le système doit gérer le redémarrage du service après un arrêt en raison d’une mémoire insuffisante. Cet appel a lieu sur le thread principal. Cette méthode est décrite plus en détail ci-dessous.
- `OnDestroy`&ndash; Cette méthode est appelée lorsque le service est en cours de destruction. Il est utilisé pour exécuter tout nettoyage final requis.

La méthode la plus importante pour un service démarré `OnStartCommand` est la méthode. Elle est appelée chaque fois que le service reçoit une demande de travail. L’extrait de code suivant est un exemple `OnStartCommand`de : 

```csharp
public override StartCommandResult OnStartCommand (Android.Content.Intent intent, StartCommandFlags flags, int startId)
{
    // This method executes on the main thread of the application.
    Log.Debug ("DemoService", "DemoService started");
    ...
    return StartCommandResult.Sticky;
}
```

Le premier paramètre est un `Intent` objet contenant les métadonnées relatives au travail à effectuer. Le deuxième paramètre contient une `StartCommandFlags` valeur qui fournit des informations sur l’appel de la méthode. Ce paramètre a l’une des deux valeurs possibles :

- `StartCommandFlag.Redelivery`Cela signifie que est une nouvelle remise d’un précédent `Intent`. `Intent` &ndash; Cette valeur est fournie lorsque le service a été `StartCommandResult.RedeliverIntent` retourné mais a été arrêté avant d’être correctement arrêté.
-`StartCommandFlag.Retry`Cette valeur est reçue lorsqu’un appel `OnStartCommand` précédent a échoué et que Android tente de redémarrer le service avec le même objectif que la tentative précédente qui a échoué. &dash;

Enfin, le troisième paramètre est une valeur entière qui est unique pour l’application qui identifie la demande. Il est possible que plusieurs appelants puissent appeler le même objet de service. Cette valeur est utilisée pour associer une demande d’arrêt d’un service avec une demande donnée pour démarrer un service. Il sera abordé plus en détail dans la section [arrêt du service](#Stopping_the_Service). 

La valeur `StartCommandResult` est retournée par le service sous forme de suggestion à Android sur la marche à suivre si le service est arrêté en raison de contraintes de ressources. Il existe trois valeurs possibles pour `StartCommandResult`:

- **[StartCommandResult. NotSticky](xref:Android.App.StartCommandResult.NotSticky)** &ndash; cette valeur indique à Android qu’il n’est pas nécessaire de redémarrer le service qu’il a supprimé. À titre d’exemple, imaginez un service qui génère des miniatures pour une galerie dans une application. Si le service est arrêté, il n’est pas essentiel de recréer la &ndash; miniature immédiatement. la miniature peut être recréée lors de la prochaine exécution de l’application.
- **[StartCommandResult. collant](xref:Android.App.StartCommandResult.Sticky)** &ndash; cela indique à Android de redémarrer le service, mais pas de fournir la dernière intention qui a démarré le service. S’il n’y a pas d’intentions en attente à gérer, `null` un est fourni pour le paramètre d’intention. Il peut s’agir, par exemple, d’une application de lecteur de musique. le service redémarre prêt à écouter de la musique, mais il lira la dernière chanson.
- **[StartCommandResult. RedeliverIntent](xref:Android.App.StartCommandResult.RedeliverIntent)** &ndash; cette valeur indique à Android de redémarrer le service et de remettre à nouveau le dernier `Intent`. Par exemple, il s’agit d’un service qui télécharge un fichier de données pour une application. Si le service est arrêté, le fichier de données doit encore être téléchargé. En retournant `StartCommandResult.RedeliverIntent`, lorsque Android redémarre le service, il fournit également l’intention (qui contient l’URL du fichier à télécharger) au service. Cela permet au téléchargement de redémarrer ou de reprendre (en fonction de l’implémentation exacte du code).

Il existe une quatrième valeur pour `StartCommandResult`. &ndash; `StartCommandResult.ContinuationMask` Cette valeur est retournée `OnStartCommand` par et elle décrit comment Android va continuer le service qu’il a supprimé. Cette valeur n’est généralement pas utilisée pour démarrer un service.

Les événements de cycle de vie des clés d’un service démarré sont présentés dans ce diagramme : 

![Diagramme montrant l’ordre dans lequel les méthodes de cycle de vie sont appelées](started-services-images/started-service-01.png "Diagramme montrant l’ordre dans lequel les méthodes de cycle de vie sont appelées.")

<a name="Stopping_the_Service" />

## <a name="stopping-the-service"></a>Arrêt du service

Un service démarré continue de s’exécuter indéfiniment. Android conservera le service s’exécuter tant qu’il y a suffisamment de ressources système. Soit le client doit arrêter le service, soit le service peut s’arrêter lorsqu’il a terminé son travail. Il existe deux façons d’arrêter un service : 

- **[Android. Content. Context. StopService ()](xref:Android.Content.Context.StopService*)** Un client (tel qu’une activité) peut demander l’arrêt d’un service en `StopService` appelant la méthode : &ndash;

    ```csharp
    StopService(new Intent(this, typeof(DemoService));
    ```

- **[Android. app. service. StopSelf ()](xref:Android.App.Service.StopSelf*)** Un service peut s’arrêter de lui-même en `StopSelf`appelant le : &ndash;

    ```csharp
    StopSelf();
    ```

### <a name="using-startid-to-stop-a-service"></a>Utilisation de startId pour arrêter un service

Plusieurs appelants peuvent demander qu’un service soit démarré. S’il existe une demande de démarrage en suspens, le service `startId` peut utiliser le qui `OnStartCommand` est passé dans pour empêcher l’arrêt prématuré du service. Le `startId` correspond au dernier appel à `StartService`, et est incrémenté chaque fois qu’il est appelé. Par conséquent, si une `StartService` requête suivante n’a pas encore abouti à un appel à `OnStartCommand`, le service peut `StopSelfResult`appeler, en lui transmettant la `startId` dernière valeur de qu’il a reçue ( `StopSelf`au lieu d’appeler simplement). Si un appel à `StartService` n’a pas encore abouti à un appel correspondant `OnStartCommand`à, le système n’arrête pas le service, car `startId` le utilisé dans `StopSelf` l’appel ne correspond pas à l' `StartService` appel le plus récent.

## <a name="related-links"></a>Liens associés

- [StartedServicesDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-startedservicesdemo)
- [Android.App.Service](xref:Android.App.Service)
- [Android.App.StartCommandFlags](xref:Android.App.StartCommandFlags)
- [Android.App.StartCommandResult](xref:Android.App.StartCommandResult)
- [Android.Content.BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [Android.Content.Intent](xref:Android.Content.Intent)
- [Android.OS.Handler](xref:Android.OS.Handler)
- [Android.Widget.Toast](xref:Android.Widget.Toast)
- [Icônes de la barre d’État](https://developer.android.com/guide/practices/ui_guidelines/icon_design_status_bar.html)
