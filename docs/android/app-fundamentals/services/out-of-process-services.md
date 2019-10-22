---
title: Exécution des services Android dans des processus distants
description: En règle générale, tous les composants d’une application Android s’exécutent dans le même processus. Les services Android sont une exception notable dans la mesure où ils peuvent être configurés pour s’exécuter dans leurs propres processus et partagés avec d’autres applications, y compris ceux d’autres développeurs Android. Ce guide explique comment créer et utiliser un service distant Android à l’aide de Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 5429f260399602b7ef15e8263bc74cb8ae940f4f
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70754885"
---
# <a name="running-android-services-in-remote-processes"></a>Exécution des services Android dans des processus distants

_En règle générale, tous les composants d’une application Android s’exécutent dans le même processus. Les services Android sont une exception notable dans la mesure où ils peuvent être configurés pour s’exécuter dans leurs propres processus et partagés avec d’autres applications, y compris ceux d’autres développeurs Android. Ce guide explique comment créer et utiliser un service distant Android à l’aide de Xamarin._

## <a name="out-of-process-services-overview"></a>Vue d’ensemble des services hors processus

Lors du démarrage d’une application, Android crée un processus dans lequel l’application doit être exécutée. En règle générale, tous les composants de l’application s’exécutent dans ce processus. Les services Android sont une exception notable dans la mesure où ils peuvent être configurés pour s’exécuter dans leurs propres processus et partagés avec d’autres applications, y compris ceux d’autres développeurs Android. Ces types de services sont appelés _services distants_ ou _services hors processus_. Le code de ces services sera contenu dans le même APK que l’application principale ; Toutefois, lorsque le service est démarré, Android crée un nouveau processus uniquement pour ce service. En revanche, un service qui s’exécute dans le même processus que le reste de l’application est parfois appelé _service local_.

En général, il n’est pas nécessaire qu’une application implémente un service distant. Un service local est suffisant (et souhaitable) pour les exigences d’une application dans de nombreux cas. Un hors processus possède son propre espace mémoire qui doit être géré par Android. Bien que cela entraîne une surcharge supplémentaire pour l’application globale, il existe des scénarios où il peut être avantageux d’exécuter un service dans son propre processus :

1. La **fonctionnalité de partage** &ndash; certains développeurs d’applications peuvent avoir plusieurs applications et fonctionnalités partagées entre toutes les applications. L’empaquetage de cette fonctionnalité dans un service Android qui s’exécute dans son propre processus peut simplifier la maintenance des applications. Il est également possible d’empaqueter le service dans son propre APK autonome et de le déployer séparément du reste de l’application.
2. **L’amélioration de l’expérience utilisateur** &ndash; il existe deux façons de faire en sorte qu’un service hors processus puisse améliorer l’expérience utilisateur de l’application. La première concerne la gestion de la mémoire. Lorsqu’un cycle de garbage collection (GC) se produit, Android interrompt toutes les activités du processus jusqu’à ce que le GC soit terminé. L’utilisateur peut percevoir cette pause sous la forme d’une « interruption » ou d’un « Jank ». Lorsqu’un service est en cours d’exécution dans son propre processus, il s’agit du processus de service qui est suspendu, et non du processus de l’application. Cette pause est bien moins perceptible pour l’utilisateur, car le processus d’application (et par conséquent l’interface utilisateur) n’est pas suspendu.

    Deuxièmement, si les besoins en mémoire d’un processus deviennent trop importants, Android peut arrêter ce processus pour libérer des ressources pour l’appareil. Si un service a un grand encombrement mémoire et qu’il s’exécute dans le même processus que l’interface utilisateur, lorsque Android force la récupération de ces ressources, l’interface utilisateur est arrêtée, ce qui force l’utilisateur à démarrer l’application. Toutefois, si un service qui s’exécute dans son propre processus est arrêté par Android, le processus d’interface utilisateur reste inchangé. L’interface utilisateur peut lier (et redémarrer) le service, transparente à l’utilisateur, et reprendre son fonctionnement normal.

3. L' **amélioration des performances des applications** &ndash; le processus de l’interface utilisateur peut être interrompu ou arrêté indépendamment du processus de service. En déplaçant des tâches de démarrage de longue durée vers un service hors processus, il est possible que la durée de démarrage de l’interface utilisateur soit améliorée (en supposant que le processus de service reste actif entre le moment où l’interface utilisateur est lancée).

À de nombreux égards, la liaison à un service qui s’exécute dans un autre processus est identique [à la liaison à un service local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). Le client appellera `BindService` pour lier (et démarrer, si nécessaire) le service. Un objet `Android.OS.IServiceConnection` sera créé pour gérer la connexion entre le client et le service. Si le client se connecte correctement au service, Android retourne un objet via le `IServiceConnection` qui peut être utilisé pour appeler des méthodes sur le service. Le client interagit ensuite avec le service à l’aide de cet objet. Pour passer en revue, Voici les étapes à suivre pour établir une liaison à un service :

- **Créez une intention** &ndash; une intention explicite doit être utilisée pour la liaison au service.
- **Implémenter et instancier un objet `IServiceConnection`** &ndash; l’objet `IServiceConnection` agit comme un intermédiaire entre le client et le service.  Il est responsable de la surveillance de la connexion entre le client et le serveur.
- **Appelez la méthode `BindService`** &ndash; l’appel de `BindService` distribuera l’intention et la connexion de service créées dans les étapes précédentes à Android, ce qui prendra soin de démarrer le service et d’établir la communication entre le client et le service.

La nécessité de franchir les limites d’un processus introduit une complexité supplémentaire : la communication est unidirectionnelle (client à serveur) et le client ne peut pas appeler directement des méthodes sur la classe de service. Rappelez-vous que lorsqu’un service exécute le même processus que le client, Android fournit un objet `IBinder` qui peut permettre une communication bidirectionnelle. Ce n’est pas le cas avec le service qui s’exécute dans son propre processus. Un client communique avec un service distant à l’aide de la classe `Android.OS.Messenger`.

Lorsqu’un client demande à établir une liaison avec le service distant, Android appelle la méthode de cycle de vie `Service.OnBind`, qui retourne l’objet `IBinder` interne encapsulé par le `Messenger`. Le `Messenger` est un wrapper léger sur une implémentation de `IBinder` spéciale fournie par le Android SDK. Le `Messenger` s’occupe de la communication entre les deux processus. Le développeur n’est pas préoccupé par les détails de la sérialisation d’un message, en marshalant le message à travers la limite du processus, puis en le désérialisant sur le client. Ce travail est géré par l’objet `Messenger`. Ce diagramme illustre les composants Android côté client qui sont impliqués lorsqu’un client initie une liaison à un service hors processus :

![Diagramme qui montre les étapes et les composants d’une liaison de client à un service](out-of-process-services-images/ipc-01.png "Diagramme qui montre les étapes et les composants d’une liaison de client à un service.")

La classe `Service` dans le processus distant passera par les mêmes rappels de cycle de vie qu’un service lié dans un processus local, et la plupart des API impliquées sont les mêmes. `Service.OnCreate` est utilisé pour initialiser une `Handler` et l’injecter dans `Messenger` objet. De même, `OnBind` est substitué, mais au lieu de retourner un objet `IBinder`, le service retourne le `Messenger`.  Ce diagramme illustre ce qui se produit dans le service lorsqu’un client y est lié :

![Diagramme qui montre les étapes et les composants qu’un service traverse lorsqu’il est lié par un client distant](out-of-process-services-images/ipc-02.png "Diagramme qui montre les étapes et les composants qu’un service traverse lorsqu’il est lié par un client distant.")

Lorsqu’un `Message` est reçu par un service, il est traité par dans l’instance de `Android.OS.Handler`. Le service implémente sa propre `Handler` sous-classe qui doit substituer la méthode `HandleMessage`. Cette méthode est appelée par la `Messenger` et reçoit le `Message` en tant que paramètre. Le `Handler` inspecte les métadonnées `Message` et utilise ces informations pour appeler des méthodes sur le service.

Une communication unidirectionnelle se produit lorsqu’un client crée un objet `Message` et le distribue au service à l’aide de la méthode `Messenger.Send`. `Messenger.Send` sérialise le `Message` et la main des données sérialisées sur Android, qui acheminent le message entre les limites du processus et le service.  La `Messenger` hébergée par le service crée un objet `Message` à partir des données entrantes. Cette `Message` est placée dans une file d’attente, où les messages sont envoyés un par un au `Handler`. Le `Handler` inspecte les métadonnées contenues dans le `Message` et appelle les méthodes appropriées sur le `Service`. Le diagramme suivant illustre ces différents concepts en action :

![Diagramme montrant le mode de transmission des messages entre les processus](out-of-process-services-images/ipc-03.png "Diagramme montrant le mode de transmission des messages entre les processus.")

Ce guide aborde les détails de l’implémentation d’un service hors processus. Il explique comment implémenter un service destiné à s’exécuter dans son propre processus et comment un client peut communiquer avec ce service à l’aide de l’infrastructure `Messenger`. Elle abordera également brièvement la communication bidirectionnelle : le client envoyant un message à un service et le service renvoyant un message au client. Étant donné que les services peuvent être partagés entre différentes applications, ce guide aborde également une technique permettant de limiter l’accès client au service à l’aide des autorisations Android.

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-les services avec des processus isolés et une classe d’application personnalisée ne parviennent pas à résoudre les surcharges](https://github.com/xamarin/xamarin-android/issues/1950) signalent qu’un service Xamarin. Android ne démarre pas correctement quand le `IsolatedProcess` est défini sur `true`. Ce guide est fourni à des fins de référence. Une application Xamarin. Android doit toujours être en mesure de communiquer avec un service hors processus écrit en Java.

## <a name="requirements"></a>spécifications

Ce guide suppose que vous êtes familiarisé avec la création de services.

Bien qu’il soit possible d’utiliser des intentions implicites avec des applications qui ciblent des API Android plus anciennes, ce guide se concentre uniquement sur l’utilisation d’intentions explicites. Les applications ciblant Android 5,0 (niveau d’API 21) ou une version ultérieure doivent utiliser une intention explicite de liaison avec un service. Il s’agit de la technique qui sera illustrée dans ce guide.

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Créer un service qui s’exécute dans un processus distinct

Comme décrit ci-dessus, le fait qu’un service s’exécute dans son propre processus signifie que certaines API différentes sont impliquées. Pour une vue d’ensemble rapide, Voici les étapes à suivre pour établir une liaison avec un service distant et l’utiliser :  

- **Créez la sous-classe `Service`** &ndash; sous-classe le type de `Service` et implémentez les méthodes de cycle de vie d’un service lié. Il est également nécessaire de définir des métadonnées qui informent Android que le service doit s’exécuter dans son propre processus.
- **Implémentez un `Handler`** &ndash; l' `Handler` est chargé d’analyser les demandes des clients, d’extraire tous les paramètres qui ont été transmis à partir du client et d’appeler les méthodes appropriées sur le service.
- **Instanciez un `Messenger`** &ndash; comme décrit ci-dessus, chaque `Service` doit conserver une instance de la classe `Messenger` qui achemine les demandes des clients vers le `Handler` qui a été créé à l’étape précédente.

Un service destiné à s’exécuter dans son propre processus est fondamentalement un service lié. La classe de service étend la classe de `Service` de base et est décorée avec le `ServiceAttribute` contenant les métadonnées que Android doit regrouper dans le manifeste Android. Pour commencer, les propriétés suivantes de la `ServiceAttribute` sont importantes pour un service hors processus :

1. `Exported` &ndash; cette propriété doit avoir la valeur `true` pour permettre à d’autres applications d’interagir avec le service. La valeur par défaut de cette propriété est `false`.
2. `Process` &ndash; cette propriété doit être définie. Il est utilisé pour spécifier le nom du processus dans lequel le service s’exécutera.
3. `IsolatedProcess` &ndash; cette propriété permet une sécurité supplémentaire, indiquant à Android d’exécuter le service dans un bac à sable (sandbox) isolé avec une autorisation minimale pour interagir avec le reste du système. Consultez [Bugzilla 51940-les services avec des processus isolés et la classe d’application personnalisée ne peuvent pas résoudre correctement les surcharges](https://bugzilla.xamarin.com/show_bug.cgi?id=51940).
4. `Permission` &ndash; il est possible de contrôler l’accès client au service en spécifiant une autorisation que les clients doivent demander (et être accordées).

Pour exécuter un service avec son propre processus, la propriété `Process` sur le `ServiceAttribute` doit être définie sur le nom du service. Pour interagir avec les applications externes, la propriété `Exported` doit être définie sur `true`. Si `Exported` est `false`, seuls les clients du même APK (c’est-à-dire la même application) et qui s’exécutent dans le même processus pourront interagir avec le service.

Le type de processus dans lequel le service s’exécutera dépend de la valeur de la propriété `Process`. Android identifie trois types différents de processus :

- Le **processus privé** &ndash; un processus privé est un processus qui est uniquement disponible pour l’application qui l’a démarré. Pour identifier un processus comme privé, son nom doit commencer par un **:** (point-virgule). Le service décrit dans l’extrait de code précédent et la capture d’écran est un processus privé. L’extrait de code suivant est un exemple de l' `ServiceAttribute` :

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

- Le **processus global** &ndash; un service exécuté dans un processus global est accessible à toutes les applications en cours d’exécution sur l’appareil. Un processus global doit être un nom de classe complet qui commence par un caractère minuscule.
    (Sauf si des étapes sont prises pour sécuriser le service, d’autres applications peuvent les lier et interagir avec lui. La sécurisation du service contre toute utilisation non autorisée sera abordée plus loin dans ce guide.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

- Le processus **isolé** &ndash; un processus isolé est un processus qui s’exécute dans son propre bac à sable (sandbox), isolé du reste du système et sans autorisations spéciales. Pour exécuter un service dans un processus isolé, la propriété `IsolatedProcess` du `ServiceAttribute` est définie sur `true` comme indiqué dans cet extrait de code :
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consultez [Bugzilla 51940-les services avec des processus isolés et la classe d’application personnalisée ne parviennent pas à résoudre les surcharges correctement](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)

Un service isolé est un moyen simple de sécuriser une application et l’appareil contre du code non fiable. Par exemple, une application peut télécharger et exécuter un script à partir d’un site Web. Dans ce cas, l’exécution de cette opération dans un processus isolé fournit une couche supplémentaire de sécurité contre le code non approuvé compromet l’appareil Android.

> [!IMPORTANT]
> Une fois qu’un service a été exporté, le nom du service ne doit pas changer. La modification du nom du service peut interrompre les autres applications qui utilisent le service.

Pour voir l’effet de la propriété `Process`, la capture d’écran suivante montre un service qui s’exécute dans son propre processus privé :

![Capture d’écran montrant un service s’exécutant dans un processus privé](out-of-process-services-images/ipc-04.png "Capture d’écran montrant un service s’exécutant dans un processus privé.")

La capture d’écran suivante montre `Process="com.xamarin.xample.messengerservice.timestampservice_process"` et le service s’exécutant dans un processus global :

![Capture d’écran d’un service s’exécutant dans un processus global](out-of-process-services-images/ipc-05.png "Capture d’écran d’un service s’exécutant dans un processus global.")

Une fois le `ServiceAttribute` défini, le service doit implémenter une `Handler`.

### <a name="implementing-a-handler"></a>Implémentation d’un gestionnaire

Pour traiter les demandes des clients, le service doit implémenter une `Handler` et remplacer le `HandleMessage` methodThis est la méthode prend une instance `Message` qui encapsule l’appel de méthode à partir du client et convertit cet appel en action ou tâche que le service va assurer. L’objet `Message` expose une propriété appelée `What` qui est une valeur entière, dont la signification est partagée entre le client et le service et qui est associée à une tâche que le service doit effectuer pour le client.

L’extrait de code suivant de l’exemple d’application montre un exemple de `HandleMessage`. Dans cet exemple, il existe deux actions qu’un client peut demander au service :

- La première action est un message _Hello, World_ , le client a envoyé un message simple au service.
- La deuxième action appelle une méthode sur le service et récupère une chaîne. dans ce cas, la chaîne est un message qui retourne l’heure à laquelle le service a démarré et la durée d’exécution de ce dernier :

```csharp
public class TimestampRequestHandler : Android.OS.Handler
{
    // other code omitted for clarity

    public override void HandleMessage(Message msg)
    {
        int messageType = msg.What;
        Log.Debug(TAG, $"Message type: {messageType}.");

        switch (messageType)
        {
            case Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE:
                // The client as sent a simple Hello, say in the Android Log.
                break;

            case Constants.GET_UTC_TIMESTAMP:
                // Call methods on the service to retrieve a timestamp message.
                break;
            default:
                Log.Warn(TAG, $"Unknown messageType, ignoring the value {messageType}.");
                base.HandleMessage(msg);
                break;
        }
    }
}
```

Il est également possible d’empaqueter des paramètres pour le service dans le `Message`. Ce sujet sera abordé plus loin dans ce guide. La rubrique suivante à prendre en compte est la création de l’objet `Messenger` pour traiter le `Message`s entrant.

### <a name="instantiating-the-messenger"></a>Instanciation de la messagerie

Comme indiqué précédemment, la désérialisation de l’objet `Message` et l’appel de `Handler.HandleMessage` sont la responsabilité de l’objet `Messenger`. La classe `Messenger` fournit également un objet `IBinder` que le client utilisera pour envoyer des messages au service.  

Lorsque le service démarre, il instancie le `Messenger` et injecte le `Handler`. Un bon emplacement pour effectuer cette initialisation se trouve sur la méthode `OnCreate` du service. Cet extrait de code est un exemple de service qui initialise ses propres `Handler` et `Messenger` :

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

À ce stade, la dernière étape consiste à ce que le `Service` Substitue `OnBind`.

### <a name="implementing-serviceonbind"></a>Implémentation de service. OnBind

Tous les services liés, qu’ils s’exécutent dans leur propre processus ou non, doivent implémenter la méthode `OnBind`. La valeur de retour de cette méthode est un objet que le client peut utiliser pour interagir avec le service. Exactement ce que fait l’objet dépend du fait que le service est un service local ou un service distant. Lorsqu’un service local retourne une implémentation de `IBinder` personnalisée, un service distant retourne le `IBinder` encapsulé, mais le `Messenger` créé dans la section précédente :

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Une fois ces trois étapes effectuées, le service distant peut être considéré comme terminé.

## <a name="consuming-the-service"></a>Consommation du service

Tous les clients doivent implémenter du code pour être en mesure de lier et de consommer le service distant. D’un point de vue conceptuel, du point de vue du client, il existe très peu de différences entre la liaison à un service local ou à un service distant. Le client appelle la méthode `BindService`, en passant une intention explicite pour identifier le service et un `IServiceConnection` qui permet de gérer la connexion entre le client et le service.

Cet extrait de code est un exemple de création d’une **intention explicite** pour la liaison à un service distant. L’intention doit identifier le package qui contient le service et le nom du service. Une façon de définir ces informations consiste à utiliser un objet `Android.Content.ComponentName` et à le fournir à l’intention. Cet extrait de code est un exemple :  

```csharp
// This is the package name of the APK, set in the Android manifest
const string REMOTE_SERVICE_COMPONENT_NAME = "com.xamarin.TimestampService";
// This is the name of the service, according the value of ServiceAttribute.Name
const string REMOTE_SERVICE_PACKAGE_NAME   = "com.xamarin.xample.messengerservice";

// Provide the package name and the name of the service with a ComponentName object.
ComponentName cn = new ComponentName(REMOTE_SERVICE_PACKAGE_NAME, REMOTE_SERVICE_COMPONENT_NAME);
Intent serviceToStart = new Intent();
serviceToStart.SetComponent(cn);
```

Lorsque le service est lié, la méthode `IServiceConnection.OnServiceConnected` est appelée et fournit un `IBinder` à un client. Toutefois, le client n’utilisera pas directement le `IBinder`. Au lieu de cela, il instancie un objet `Messenger` à partir de ce `IBinder`. Il s’agit de la `Messenger` que le client utilisera pour interagir avec le service distant.

L’exemple suivant illustre une implémentation de base `IServiceConnection` qui montre comment un client gère la connexion et la déconnexion d’un service. Notez que la méthode `OnServiceConnected` reçoit et `IBinder`, et que le client crée une `Messenger` à partir de cette `IBinder` :

```csharp
public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection
{
    static readonly string TAG = typeof(TimestampServiceConnection).FullName;

    MainActivity mainActivity;
    Messenger messenger;

    public TimestampServiceConnection(MainActivity activity)
    {
        IsConnected = false;
        mainActivity = activity;
    }

    public bool IsConnected { get; private set; }
    public Messenger Messenger { get; private set; }

    public void OnServiceConnected(ComponentName name, IBinder service)
    {
        Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

        IsConnected = service != null
        Messenger = new Messenger(service);

        if (IsConnected)
        {
            // things to do when the connection is successful. perhaps notify the client? enable UI features?
        }
        else
        {
            // things to do when the connection isn't successful.
        }
    }

    public void OnServiceDisconnected(ComponentName name)
    {
        Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
        IsConnected = false;
        Messenger = null;

        // Things to do when the service disconnects. perhaps notify the client? disable UI features?

    }
}
```

Une fois la connexion au service et l’intention créées, le client peut appeler `BindService` et initier le processus de liaison :

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Une fois que le client a été lié au service et que le `Messenger` est disponible, il est possible que le client envoie des `Messages` au service.

## <a name="sending-messages-to-the-service"></a>Envoi de messages au service

Une fois que le client est connecté et qu’il dispose d’un objet `Messenger`, il est possible de communiquer avec le service en distribuant les objets `Message` via le `Messenger`. Cette communication est unidirectionnelle, le client envoie le message, mais il n’y a pas de message de retour du service au client. À cet égard, le `Message` est un mécanisme d’incendie et d’oubli.

La meilleure façon de créer un objet `Message` est d’utiliser la méthode de fabrique [`Message.Obtain`](xref:Android.OS.Message) . Cette méthode extrait un objet `Message` à partir d’un pool global qui est géré par Android. `Message.Obtain` possède également des méthodes surchargées qui permettent d’initialiser l’objet `Message` à l’aide des valeurs et des paramètres requis par le service.  Une fois le `Message` instancié, il est distribué au service en appelant `Messenger.Send`. Cet extrait de code est un exemple de création et de distribution d’un `Message` au processus de service :

```csharp
Message msg = Message.Obtain(null, Constants.SAY_HELLO_TO_TIMESTAMP_SERVICE);
try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a error trying to send the message.");
}
```

Il existe plusieurs formes différentes de la méthode `Message.Obtain`. L’exemple précédent utilise la [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain). Parce qu’il s’agit d’une demande asynchrone à un service hors processus ; Il n’y aura pas de réponse du service. par conséquent, le `Handler` est défini sur `null`. Le deuxième paramètre, `Int32 what`, est stocké dans la propriété `.What` de l’objet `Message`. La propriété `.What` est utilisée par le code dans le processus de service pour appeler des méthodes sur le service.

La classe `Message` expose également deux propriétés supplémentaires qui peuvent être utilisées par le destinataire : `Arg1` et `Arg2`. Ces deux propriétés sont des valeurs entières qui peuvent avoir des valeurs spéciales accordées sur des valeurs qui ont une signification entre le client et le service. Par exemple, `Arg1` peut contenir un ID client et `Arg2` peut contenir un numéro de bon de commande pour ce client. La [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain) peut être utilisée pour définir les deux propriétés lors de la création du `Message`. Une autre façon de remplir ces deux valeurs consiste à définir les propriétés `.Arg` et `.Arg2` directement sur l’objet `Message` après qu’il a été créé.

### <a name="passing-additional-values-to-the-service"></a>Passage de valeurs supplémentaires au service

Il est possible de transmettre des données plus complexes au service à l’aide d’un `Bundle`. Dans ce cas, les valeurs supplémentaires peuvent être placées dans une `Bundle` et envoyées avec la `Message` en définissant la propriété de [propriété `.Data`](xref:Android.OS.Message.Data) avant l’envoi.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```

> [!NOTE]
> En général, une `Message` ne doit pas avoir une charge utile supérieure à 1 Mo. La limite de taille peut varier selon la version d’Android et pour toute modification propriétaire apportée par le fournisseur à son implémentation du projet open source Android (AOSP) fourni avec l’appareil.

## <a name="returning-values-from-the-service"></a>Retour de valeurs à partir du service

L’architecture de messagerie qui a été évoquée à ce stade est unidirectionnelle, le client envoie un message au service. S’il est nécessaire que le service retourne une valeur à un client, tout ce qui a été abordé à ce stade est inversé. Le service doit créer un `Message`, empaqueter toutes les valeurs de retour et distribuer le `Message` via un `Messenger` au client. Toutefois, le service ne crée pas son propre `Messenger` ; au lieu de cela, il s’appuie sur le client qui instancie et conditionne un `Messenger` dans le cadre de la requête initiale. Le service `Send` le message à l’aide de ce `Messenger` fourni par le client.  

La séquence d’événements pour la communication bidirectionnelle est la suivante :

1. Le client est lié au service. Lorsque le service et le client se connectent, le `IServiceConnection` qui est géré par le client aura une référence à un objet `Messenger` utilisé pour transmettre `Message`s au service. Pour éviter toute confusion, cette opération est appelée Messenger de _service_.
2. Le client instancie un `Handler` (appelé _Gestionnaire client_) et l’utilise pour initialiser sa propre `Messenger` (le _client Messenger_). Notez que le service Messenger et le client Messenger sont deux objets différents qui gèrent le trafic dans deux directions différentes. Le service Messenger gère les messages du client au service, tandis que le client Messenger gère les messages du service vers le client.
3. Le client crée un objet `Message` et définit la propriété `ReplyTo` avec le client Messenger. Le message est ensuite envoyé au service à l’aide du service Messenger.
4. Le service reçoit le message du client et effectue le travail demandé.
5. Lorsqu’il est temps pour le service d’envoyer la réponse au client, il utilise `Message.Obtain` pour créer un nouvel objet `Message`.
6. Pour envoyer ce message au client, le service extrait le client Messenger à partir de la propriété `.ReplyTo` du message client et l’utilise pour `.Send` le `Message` au client.
7. Lorsque la réponse est reçue par le client, elle a sa propre `Handler` qui traitera la `Message` en inspectant la propriété `.What` (et, si nécessaire, en extrayant les paramètres contenus dans le `Message`).

Cet exemple de code montre comment le client instancie le `Message` et conditionne un `Messenger` que le service doit utiliser pour sa réponse :

```csharp
Handler clientHandler = new ActivityHandler();
Messenger clientMessenger = new Messenger(activityHandler);

Message msg = Message.Obtain(null, Constants.GET_UTC_TIMESTAMP);
msg.ReplyTo = clientMessenger;

try
{
    serviceConnection.Messenger.Send(msg);
}
catch (RemoteException ex)
{
    Log.Error(TAG, ex, "There was a problem sending the message.");
}
```

Le service doit apporter des modifications à son propre `Handler` pour extraire le `Messenger` et l’utiliser pour envoyer des réponses au client. Cet extrait de code est un exemple de la façon dont le `Handler` du service crée un `Message` et le renvoie au client :  

```csharp
// This is the message that the service will send to the client.
Message responseMessage = Message.Obtain(null, Constants.RESPONSE_TO_SERVICE);
Bundle dataToReturn = new Bundle();
dataToReturn.PutString(Constants.RESPONSE_MESSAGE_KEY, "This is the result from the service.");
responseMessage.Data = dataToReturn;

// The msg object here is the message that was received by the service. The service will not instantiate a client,
// It will use the client that is encapsulated by the message from the client.
Messenger clientMessenger = msg.ReplyTo;
if (clientMessenger!= null)
{
    try
    {
        clientMessenger.Send(responseMessage);
    }
    catch (Exception ex)
    {
        Log.Error(TAG, ex, "There was a problem sending the message.");
    }
}
```

Notez que dans les exemples de code ci-dessus, l’instance de `Messenger` créée par le client n’est *pas* le même objet que celui reçu par le service. Il s’agit de deux objets `Messenger` différents qui s’exécutent dans deux processus distincts qui représentent le canal de communication.

## <a name="securing-the-service-with-android-permissions"></a>Sécurisation du service avec des autorisations Android

Un service qui s’exécute dans un processus global est accessible par toutes les applications qui s’exécutent sur cet appareil Android. Dans certains cas, cette ouverture et cette disponibilité ne sont pas souhaitables, et il est nécessaire de sécuriser le service contre tout accès à partir de clients non autorisés. Pour limiter l’accès au service distant, il est possible d’utiliser des autorisations Android.

Les autorisations peuvent être identifiées par la propriété `Permission` du `ServiceAttribute` qui décore la sous-classe `Service`. Cela permet de nommer une autorisation que le client doit recevoir lors de la liaison au service. Si le client ne dispose pas des autorisations appropriées, Android lève une `Java.Lang.SecurityException` lorsque le client tente d’établir une liaison avec le service.

Android offre quatre niveaux d’autorisation :

- **normal** &ndash; il s’agit du niveau d’autorisation par défaut. Il est utilisé pour identifier les autorisations à faible risque qui peuvent être accordées automatiquement par Android aux clients qui le demandent. L’utilisateur n’a pas besoin d’accorder explicitement ces autorisations, mais les autorisations peuvent être affichées dans les paramètres de l’application.
- **signature** &ndash; il s’agit d’une catégorie spéciale d’autorisation qui sera accordée automatiquement par Android aux applications qui sont toutes signées avec le même certificat. Cette autorisation est conçue pour permettre à un développeur d’applications de partager facilement des composants ou des données entre leurs applications sans avoir à passer l’utilisateur à des approbations constantes.
- **signatureOrSystem** &ndash; cela est très similaire aux autorisations de **signature** décrites ci-dessus. En plus d’être accordé automatiquement aux applications qui sont signées par le même certificat, cette autorisation est également accordée aux applications qui sont signées avec le même certificat que celui utilisé pour signer les applications installées avec l’image système Android. En général, cette autorisation est utilisée uniquement par les développeurs de la ROM Android pour permettre à leurs applications de fonctionner avec des applications tierces. Elle n’est généralement pas utilisée par les applications qui sont destinées à la distribution générale pour le grand public.
- les autorisations **dangereuses &ndash; dangereuses** sont celles qui peuvent entraîner des problèmes pour l’utilisateur. Pour cette raison, les autorisations **dangereuses** doivent être approuvées explicitement par l’utilisateur.

Étant donné que les autorisations `signature` et `normal` sont accordées automatiquement au moment de l’installation par Android, il est essentiel que APK hébergeant le service soit installé **avant** le apk contenant le client. Si le client est installé en premier, Android n’accorde pas les autorisations. Dans ce cas, il est nécessaire de désinstaller le client APK, d’installer le service APK, puis de réinstaller le APK client.

Il existe deux façons courantes de sécuriser un service avec des autorisations Android :

1. **Implémenter la sécurité au niveau** de la signature &ndash; la sécurité au niveau des signatures signifie que l’autorisation est accordée automatiquement aux applications qui sont signées avec la même clé que celle utilisée pour signer le apk qui détient le service. C’est un moyen simple pour les développeurs de sécuriser leur service tout en les protégeant de leurs propres applications. Les autorisations au niveau de la signature sont déclarées en affectant à la propriété `Permission` du `ServiceAttribute` la valeur `signature` :

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2. **Créez une autorisation personnalisée** &ndash; il est possible pour le développeur du service de créer une autorisation personnalisée pour le service. C’est le plus adapté quand un développeur souhaite partager son service avec des applications d’autres développeurs. Une autorisation personnalisée nécessite un peu plus d’effort pour implémenter et sera traitée ci-dessous.

Un exemple simplifié de création d’une autorisation de `normal` personnalisée est décrit dans la section suivante. Pour plus d’informations sur les autorisations Android, consultez la documentation de Google concernant les [meilleures pratiques & la sécurité](https://developer.android.com/training/articles/security-tips.html). Pour plus d’informations sur les autorisations Android, consultez la [section autorisations](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) de la documentation Android pour le manifeste d’application pour plus d’informations sur les autorisations Android.

> [!NOTE]
> En général, [Google déconseille l’utilisation des autorisations personnalisées](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) , car elles peuvent s’avérer confuses pour les utilisateurs.

### <a name="creating-a-custom-permission"></a>Création d’une autorisation personnalisée

Pour utiliser une autorisation personnalisée, elle est déclarée par le service alors que le client demande explicitement cette autorisation.

Pour créer une autorisation dans le service APK, un élément `permission` est ajouté à l’élément `manifest` dans **fichier AndroidManifest. xml**. Cette autorisation doit avoir les attributs `name`, `protectionLevel` et `label` définis. L’attribut `name` doit avoir la valeur d’une chaîne qui identifie de façon unique l’autorisation. Le nom s’affiche dans la vue **informations** sur l’application des **paramètres Android** (comme indiqué dans la section suivante).

L’attribut `protectionLevel` doit être défini sur l’une des quatre valeurs de chaîne décrites ci-dessus.  Les `label` et `description` doivent faire référence aux ressources de type chaîne et sont utilisés pour fournir un nom convivial et une description à l’utilisateur.

Cet extrait de code est un exemple de déclaration d’un attribut de `permission` personnalisé dans **fichier AndroidManifest. xml** du apk qui contient le service :

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerservice">

    <uses-sdk android:minSdkVersion="21" />

    <permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP"
                android:protectionLevel="signature"
                android:label="@string/permission_label"
                android:description="@string/permission_description"
                />

    <application android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">

    </application>
</manifest>
```

Ensuite, **fichier AndroidManifest. xml** du client apk doit demander explicitement cette nouvelle autorisation. Pour ce faire, ajoutez l’attribut `users-permission` à **fichier AndroidManifest. xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
          android:versionCode="1"
          android:versionName="1.0"
          package="com.xamarin.xample.messengerclient">

    <uses-sdk android:minSdkVersion="21" />

    <uses-permission android:name="com.xamarin.xample.messengerservice.REQUEST_TIMESTAMP" />

    <application
            android:allowBackup="true"
            android:icon="@mipmap/icon"
            android:label="@string/app_name"
            android:theme="@style/AppTheme">
    </application>
    </manifest>
```

### <a name="view-the-permissions-granted-to-an-app"></a>Afficher les autorisations accordées à une application

Pour afficher les autorisations accordées à une application, ouvrez l’application paramètres Android, puis sélectionnez **applications**. Recherchez et sélectionnez l’application dans la liste. Dans l’écran informations sur l' **application** , appuyez sur **autorisations** pour afficher une vue qui affiche toutes les autorisations accordées à l’application :

[![Screenshots à partir d’un appareil Android qui montre comment trouver les autorisations accordées à une application](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Récapitulatif

Ce guide a été une discussion avancée sur l’exécution d’un service Android dans un processus distant. Les différences entre un service local et un service distant ont été expliquées, ainsi que les raisons pour lesquelles un service distant peut être utile pour la stabilité et les performances d’une application Android. Une fois que vous avez expliqué comment implémenter un service distant et comment un client peut communiquer avec le service, le guide a été mis en place pour fournir une méthode permettant de limiter l’accès au service à partir de clients autorisés uniquement.

## <a name="related-links"></a>Liens associés

- [D](xref:Android.OS.Handler)
- [Message](xref:Android.OS.Message)
- [Messagerie](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Attribut exporté](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Les services avec des processus isolés et une classe d’application personnalisée ne parviennent pas à résoudre les surcharges correctement](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processus et threads](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifeste Android-autorisations](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Conseils de sécurité](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
