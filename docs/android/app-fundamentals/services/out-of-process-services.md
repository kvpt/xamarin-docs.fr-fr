---
title: Exécution des services Android dans des processus distants
description: En règle générale, tous les composants d’une application Android s’exécutent dans le même processus. Les services Android sont une exception notable dans la mesure où ils peuvent être configurés pour s’exécuter dans leurs propres processus et partagés avec d’autres applications, y compris ceux d’autres développeurs Android. Ce guide explique comment créer et utiliser un service distant Android à l’aide de Xamarin.
ms.prod: xamarin
ms.assetid: 27A2E972-A690-480B-B31D-5EF1F74F673C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 360ea18de0c9d30988d63602ba3c17c3d00ed83a
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644094"
---
# <a name="running-android-services-in-remote-processes"></a>Exécution des services Android dans des processus distants

_En règle générale, tous les composants d’une application Android s’exécutent dans le même processus. Les services Android sont une exception notable dans la mesure où ils peuvent être configurés pour s’exécuter dans leurs propres processus et partagés avec d’autres applications, y compris ceux d’autres développeurs Android. Ce guide explique comment créer et utiliser un service distant Android à l’aide de Xamarin._

## <a name="out-of-process-services-overview"></a>Vue d’ensemble des services hors processus

Lors du démarrage d’une application, Android crée un processus dans lequel l’application doit être exécutée. En règle générale, tous les composants de l’application s’exécutent dans ce processus. Les services Android sont une exception notable dans la mesure où ils peuvent être configurés pour s’exécuter dans leurs propres processus et partagés avec d’autres applications, y compris ceux d’autres développeurs Android. Ces types de services sont appelés _services_ distants ou _services hors processus_. Le code de ces services sera contenu dans le même APK que l’application principale; Toutefois, lorsque le service est démarré, Android crée un nouveau processus uniquement pour ce service. En revanche, un service qui s’exécute dans le même processus que le reste de l’application est parfois appelé _service local_.

En général, il n’est pas nécessaire qu’une application implémente un service distant. Un service local est suffisant (et souhaitable) pour les exigences d’une application dans de nombreux cas. Un hors processus possède son propre espace mémoire qui doit être géré par Android. Bien que cela entraîne une surcharge supplémentaire pour l’application globale, il existe des scénarios où il peut être avantageux d’exécuter un service dans son propre processus:

1. **Fonctionnalités de partage** &ndash; Certains développeurs d’applications peuvent avoir plusieurs applications et fonctionnalités partagées entre toutes les applications. L’empaquetage de cette fonctionnalité dans un service Android qui s’exécute dans son propre processus peut simplifier la maintenance des applications. Il est également possible d’empaqueter le service dans son propre APK autonome et de le déployer séparément du reste de l’application.
2. **Amélioration de l’expérience utilisateur** &ndash; Il existe deux façons de faire en sorte qu’un service hors processus puisse améliorer l’expérience utilisateur de l’application. La première concerne la gestion de la mémoire. Lorsqu’un cycle de garbage collection (GC) se produit, Android interrompt toutes les activités du processus jusqu’à ce que le GC soit terminé. L’utilisateur peut percevoir cette pause sous la forme d’une «interruption» ou d’un «Jank». Lorsqu’un service est en cours d’exécution dans son propre processus, il s’agit du processus de service qui est suspendu, et non du processus de l’application. Cette pause est bien moins perceptible pour l’utilisateur, car le processus d’application (et par conséquent l’interface utilisateur) n’est pas suspendu.

    Deuxièmement, si les besoins en mémoire d’un processus deviennent trop importants, Android peut arrêter ce processus pour libérer des ressources pour l’appareil. Si un service a un grand encombrement mémoire et qu’il s’exécute dans le même processus que l’interface utilisateur, lorsque Android force la récupération de ces ressources, l’interface utilisateur est arrêtée, ce qui force l’utilisateur à démarrer l’application. Toutefois, si un service qui s’exécute dans son propre processus est arrêté par Android, le processus d’interface utilisateur reste inchangé. L’interface utilisateur peut lier (et redémarrer) le service, transparente à l’utilisateur, et reprendre son fonctionnement normal.

3. **Amélioration des performances des applications** &ndash; Le processus d’interface utilisateur peut être arrêté ou arrêté indépendamment du processus de service. En déplaçant des tâches de démarrage de longue durée vers un service hors processus, il est possible que la durée de démarrage de l’interface utilisateur soit améliorée (en supposant que le processus de service reste actif entre le moment où l’interface utilisateur est lancée).

À de nombreux égards, la liaison à un service qui s’exécute dans un autre processus est identique [à la liaison à un service local](~/android/app-fundamentals/services/creating-a-service/bound-services.md). Le client `BindService` appellera pour lier (et démarrer, si nécessaire) le service. Un `Android.OS.IServiceConnection` objet sera créé pour gérer la connexion entre le client et le service. Si le client se connecte correctement au service, Android retourne un objet via le `IServiceConnection` qui peut être utilisé pour appeler des méthodes sur le service. Le client interagit ensuite avec le service à l’aide de cet objet. Pour passer en revue, Voici les étapes à suivre pour établir une liaison à un service:

* **Créer une intention** &ndash; Une intention explicite doit être utilisée pour la liaison au service.
* **Implémenter et instancier `IServiceConnection` un objet** &ndash; l' `IServiceConnection` objet agit comme un intermédiaire entre le client et le service.  Il est responsable de la surveillance de la connexion entre le client et le serveur.
* **Appeler l' `BindService`**  &ndash; appel de`BindService` la méthode permet de distribuer l’intention et la connexion de service créées dans les étapes précédentes à Android, ce qui se charge de démarrer le service et d’établir la communication entre client et service.

La nécessité de franchir les limites d’un processus introduit une complexité supplémentaire: la communication est unidirectionnelle (client à serveur) et le client ne peut pas appeler directement des méthodes sur la classe de service. Rappelez-vous que lorsqu’un service exécute le même processus que le client, Android `IBinder` fournit un objet qui peut permettre une communication bidirectionnelle. Ce n’est pas le cas avec le service qui s’exécute dans son propre processus. Un client communique avec un service distant avec l’aide de la `Android.OS.Messenger` classe.

Lorsqu’un client demande à établir une liaison avec le service distant, Android appellera `Service.OnBind` la méthode Lifecycle, qui retournera `IBinder` l’objet interne encapsulé par le `Messenger`. Le `Messenger` est un wrapper léger sur une implémentation `IBinder` spéciale fournie par le Android SDK. Le `Messenger` s’occupe de la communication entre les deux processus. Le développeur n’est pas préoccupé par les détails de la sérialisation d’un message, en marshalant le message à travers la limite du processus, puis en le désérialisant sur le client. Ce travail est géré par l' `Messenger` objet. Ce diagramme illustre les composants Android côté client qui sont impliqués lorsqu’un client initie une liaison à un service hors processus:

![Diagramme qui montre les étapes et les composants d’une liaison de client à un service](out-of-process-services-images/ipc-01.png "Diagramme qui montre les étapes et les composants d’une liaison de client à un service.")

La `Service` classe dans le processus distant passera par les mêmes rappels de cycle de vie qu’un service lié dans un processus local, et la plupart des API impliquées sont les mêmes. `Service.OnCreate`est utilisé pour initialiser un `Handler` et l’injecter `Messenger` dans l’objet. De même `OnBind` , est substitué, mais au lieu de retourner `IBinder` un objet, `Messenger`le service retourne.  Ce diagramme illustre ce qui se produit dans le service lorsqu’un client y est lié:

![Diagramme qui montre les étapes et les composants qu’un service traverse lorsqu’il est lié par un client distant](out-of-process-services-images/ipc-02.png "Diagramme qui montre les étapes et les composants qu’un service traverse lorsqu’il est lié par un client distant.")

Lorsqu’un `Message` service est reçu par un service, il est traité par dans l' `Android.OS.Handler`instance de. Le service implémente sa propre `Handler` sous-classe qui doit substituer `HandleMessage` la méthode. Cette méthode est appelée par `Messenger` et reçoit le `Message` en tant que paramètre. Le `Handler` inspecte les `Message` métadonnées et utilise ces informations pour appeler des méthodes sur le service.

Une communication unidirectionnelle se produit lorsqu’un client crée un `Message` objet et le distribue au service à l’aide `Messenger.Send` de la méthode. `Messenger.Send`sérialise la main et `Message` les données sérialisées sur Android, qui acheminent le message entre les limites du processus et le service.  Le `Messenger` qui est hébergé par le service crée un `Message` objet à partir des données entrantes. Il `Message` est placé dans une file d’attente, où les messages sont envoyés un à la `Handler`fois à. Inspecte les métadonnées contenues `Message` dans et appelle les méthodes appropriées sur le `Service`. `Handler` Le diagramme suivant illustre ces différents concepts en action:

![Diagramme montrant le mode de transmission des messages entre les processus](out-of-process-services-images/ipc-03.png "Diagramme montrant le mode de transmission des messages entre les processus.")

Ce guide aborde les détails de l’implémentation d’un service hors processus. Il explique comment implémenter un service destiné à s’exécuter dans son propre processus et comment un client peut communiquer avec ce service à l’aide de `Messenger` l’infrastructure. Elle abordera également brièvement la communication bidirectionnelle: le client envoyant un message à un service et le service renvoyant un message au client. Étant donné que les services peuvent être partagés entre différentes applications, ce guide aborde également une technique permettant de limiter l’accès client au service à l’aide des autorisations Android.

> [!IMPORTANT]
> [Bugzilla 51940/GitHub 1950-les services avec des processus isolés et une classe d’application personnalisée ne parviennent pas à résoudre](https://github.com/xamarin/xamarin-android/issues/1950) les surcharges signalent qu’un service Xamarin. `IsolatedProcess` Android ne démarrera pas correctement quand a la valeur. `true` Ce guide est fourni à des fins de référence. Une application Xamarin. Android doit toujours être en mesure de communiquer avec un service hors processus écrit en Java.

## <a name="requirements"></a>Configuration requise

Ce guide suppose que vous êtes familiarisé avec la création de services.

Bien qu’il soit possible d’utiliser des intentions implicites avec des applications qui ciblent des API Android plus anciennes, ce guide se concentre uniquement sur l’utilisation d’intentions explicites. Les applications ciblant Android 5,0 (niveau d’API 21) ou une version ultérieure doivent utiliser une intention explicite de liaison avec un service. Il s’agit de la technique qui sera illustrée dans ce guide.

## <a name="create-a-service-that-runs-in-a-separate-process"></a>Créer un service qui s’exécute dans un processus distinct

Comme décrit ci-dessus, le fait qu’un service s’exécute dans son propre processus signifie que certaines API différentes sont impliquées. Pour une vue d’ensemble rapide, Voici les étapes à suivre pour établir une liaison avec un service distant et l’utiliser:  

* **Créez la `Service`**  `Service` sous-classe de sous-classe du type et implémentez les méthodes de cycle de vie pour un service lié. &ndash; Il est également nécessaire de définir des métadonnées qui informent Android que le service doit s’exécuter dans son propre processus.
* **Implémenter `Handler` un** estchargé&ndash; d’analyser les demandes des clients, d’extraire tous les paramètres qui ont été transmis à partir du client et d’appeler les méthodes appropriées sur le service. `Handler`
* **Instanciez `Messenger` un** `Service` `Messenger` `Handler` comme décrit ci-dessus, chaque doit conserver une instance de la classe qui acheminera les demandes des clients vers le qui a été créé à l’étape précédente. &ndash;

Un service destiné à s’exécuter dans son propre processus est fondamentalement un service lié. La classe de service étendra la `Service` classe de base et sera décorée avec le `ServiceAttribute` contenant les métadonnées qu’Android doit regrouper dans le manifeste Android. Pour commencer, les propriétés suivantes du `ServiceAttribute` qui sont importantes pour un service hors processus:

1. `Exported`Cette propriété doit avoir la `true` valeur pour permettre à d’autres applications d’interagir avec le service. &ndash; La valeur par défaut de cette propriété est `false`.
2. `Process`&ndash; Cette propriété doit être définie. Il est utilisé pour spécifier le nom du processus dans lequel le service s’exécutera.
3. `IsolatedProcess`&ndash; Cette propriété permet une sécurité supplémentaire, indiquant à Android d’exécuter le service dans un bac à sable (sandbox) isolé avec une autorisation minimale pour interagir avec le reste du système. Consultez [Bugzilla 51940-les services avec des processus isolés et la classe d’application personnalisée ne peuvent pas résoudre correctement les](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)surcharges.
4. `Permission`&ndash; Il est possible de contrôler l’accès client au service en spécifiant une autorisation que les clients doivent demander (et être accordées).

Pour exécuter un service avec son propre processus, `Process` la propriété `ServiceAttribute` sur doit être définie sur le nom du service. Pour interagir avec les applications externes, `Exported` la propriété doit avoir la `true`valeur. Si `Exported` a `false`la valeur, seuls les clients du même apk (c’est-à-dire la même application) et s’exécutant dans le même processus pourront interagir avec le service.

Le type de processus dans lequel le service s’exécutera dépend de la valeur `Process` de la propriété. Android identifie trois types différents de processus:

-   **Processus privé** &ndash; Un processus privé est un processus qui est uniquement disponible pour l’application qui l’a démarré. Pour identifier un processus comme privé, son nom doit commencer par un **:** (point-virgule). Le service décrit dans l’extrait de code précédent et la capture d’écran est un processus privé. L’extrait de code suivant est un exemple de `ServiceAttribute`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process=":timestampservice_process",
             Exported=true)]
    ```

-   **Processus global** &ndash; Un service exécuté dans un processus global est accessible à toutes les applications qui s’exécutent sur l’appareil. Un processus global doit être un nom de classe complet qui commence par un caractère minuscule.
    (Sauf si des étapes sont prises pour sécuriser le service, d’autres applications peuvent les lier et interagir avec lui. La sécurisation du service contre toute utilisation non autorisée sera abordée plus loin dans ce guide.)

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

-   **Processus isolé** &ndash; Un processus isolé est un processus qui s’exécute dans son propre bac à sable (sandbox), isolé du reste du système et sans autorisations spéciales. Pour exécuter un service dans un processus isolé, la `IsolatedProcess` propriété `ServiceAttribute` du est définie sur `true` comme indiqué dans cet extrait de code:
    
    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             IsolatedProcess= true,
             Process="com.xamarin.xample.messengerservice.timestampservice_process",
             Exported=true)]
    ```

> [!IMPORTANT]
> Consultez [Bugzilla 51940-les services avec des processus isolés et la classe d’application personnalisée ne parviennent pas à résoudre](https://bugzilla.xamarin.com/show_bug.cgi?id=51940) les surcharges correctement

Un service isolé est un moyen simple de sécuriser une application et l’appareil contre du code non fiable. Par exemple, une application peut télécharger et exécuter un script à partir d’un site Web. Dans ce cas, l’exécution de cette opération dans un processus isolé fournit une couche supplémentaire de sécurité contre le code non approuvé compromet l’appareil Android.

> [!IMPORTANT]
> Une fois qu’un service a été exporté, le nom du service ne doit pas changer. La modification du nom du service peut interrompre les autres applications qui utilisent le service.

Pour voir l’effet de la `Process` propriété, la capture d’écran suivante montre un service qui s’exécute dans son propre processus privé:

![Capture d’écran montrant un service s’exécutant dans un processus privé](out-of-process-services-images/ipc-04.png "Capture d’écran montrant un service s’exécutant dans un processus privé.")

La capture d’écran `Process="com.xamarin.xample.messengerservice.timestampservice_process"` suivante montre et le service s’exécutant dans un processus global:

![Capture d’écran d’un service s’exécutant dans un processus global](out-of-process-services-images/ipc-05.png "Capture d’écran d’un service s’exécutant dans un processus global.")

Une fois `ServiceAttribute` que le a été défini, le service doit implémenter un. `Handler`

### <a name="implementing-a-handler"></a>Implémentation d’un gestionnaire

Pour traiter les demandes des clients, le service doit `Handler` implémenter un et `HandleMessage` substituer le methodThis est la `Message` méthode prend une instance qui encapsule l’appel de méthode à partir du client et convertit cet appel en une action ou une tâche que le service exécutera. L' `Message` objet expose une propriété appelée `What` qui est une valeur entière, dont la signification est partagée entre le client et le service et se réfère à une tâche que le service doit effectuer pour le client.

L’extrait de code suivant de l’exemple d’application montre un `HandleMessage`exemple de. Dans cet exemple, il existe deux actions qu’un client peut demander au service:

* La première action est un message _Hello, World_ , le client a envoyé un message simple au service.
* La deuxième action appelle une méthode sur le service et récupère une chaîne. dans ce cas, la chaîne est un message qui retourne l’heure à laquelle le service a démarré et la durée d’exécution de ce dernier:

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

Il est également possible d’empaqueter des paramètres pour le `Message`service dans le. Ce sujet sera abordé plus loin dans ce guide. La rubrique suivante à prendre en compte est `Messenger` la création de l’objet `Message`pour traiter les s entrants.

### <a name="instantiating-the-messenger"></a>Instanciation de la messagerie

Comme indiqué précédemment, la désérialisation de `Message` l’objet et l' `Handler.HandleMessage` appel de sont la responsabilité `Messenger` de l’objet. La `Messenger` classe fournit également un `IBinder` objet que le client utilisera pour envoyer des messages au service.  

Lorsque le service démarre, il instancie `Messenger` et `Handler`injecte. Un bon emplacement pour effectuer cette initialisation se trouve sur la `OnCreate` méthode du service. Cet extrait de code est un exemple de service qui initialise ses propres `Handler` et: `Messenger`

```csharp
private Messenger messenger; // Instance variable for the Messenger

public override void OnCreate()
{
    base.OnCreate();
    messenger = new Messenger(new TimestampRequestHandler(this));
    Log.Info(TAG, $"TimestampService is running in process id {Android.OS.Process.MyPid()}.");
}
```

À ce stade, la dernière étape consiste `Service` à substituer. `OnBind`

### <a name="implementing-serviceonbind"></a>Implémentation de service. OnBind

Tous les services liés, qu’ils s’exécutent dans leur propre processus ou non, `OnBind` doivent implémenter la méthode. La valeur de retour de cette méthode est un objet que le client peut utiliser pour interagir avec le service. Exactement ce que fait l’objet dépend du fait que le service est un service local ou un service distant. Lorsqu’un service local retourne une implémentation personnalisée `IBinder` , un service distant retourne le `IBinder` qui est encapsulé, mais le `Messenger` qui a été créé dans la section précédente:

```csharp
public override IBinder OnBind(Intent intent)
{
    Log.Debug(TAG, "OnBind");
    return messenger.Binder;
}
```

Une fois ces trois étapes effectuées, le service distant peut être considéré comme terminé.

## <a name="consuming-the-service"></a>Consommation du service

Tous les clients doivent implémenter du code pour être en mesure de lier et de consommer le service distant. D’un point de vue conceptuel, du point de vue du client, il existe très peu de différences entre la liaison à un service local ou à un service distant. Le client appelle la `BindService` méthode, en passant une intention explicite pour identifier le service et un `IServiceConnection` qui permet de gérer la connexion entre le client et le service.

Cet extrait de code est un exemple de création d’une **intention explicite** pour la liaison à un service distant. L’intention doit identifier le package qui contient le service et le nom du service. Une façon de définir ces informations consiste à utiliser un `Android.Content.ComponentName` objet et à le fournir à l’intention. Cet extrait de code est un exemple:  

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

Lorsque le service est lié, la `IServiceConnection.OnServiceConnected` méthode est appelée et fournit un `IBinder` à un client. Toutefois, le client n’utilisera pas directement `IBinder`le. Au lieu de cela, il `Messenger` instancie un `IBinder`objet à partir de ce. Il s’agit `Messenger` du que le client utilisera pour interagir avec le service distant.

L’exemple suivant illustre une implémentation de base `IServiceConnection` qui montre comment un client gère la connexion et la déconnexion d’un service. Notez que la `OnServiceConnected` méthode reçoit et `IBinder`, et que le client crée `Messenger` un à `IBinder`partir de ce qui suit:

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

Une fois la connexion au service et l’intention créées, le client peut appeler `BindService` et initier le processus de liaison:

```csharp
IServiceConnection serviceConnection = new TimestampServiceConnection(this);
BindActivity(serviceToStart, serviceConnection, Bind.AutoCreate);
```

Une fois que le client a été lié au service et `Messenger` que le est disponible, il est possible que le client `Messages` envoie au service.

## <a name="sending-messages-to-the-service"></a>Envoi de messages au service

Une fois que le client est connecté et `Messenger` qu’il dispose d’un objet, il est possible de communiquer avec le `Message` service en distribuant des objets via le `Messenger`. Cette communication est unidirectionnelle, le client envoie le message, mais il n’y a pas de message de retour du service au client. À cet égard, `Message` est un mécanisme de déclenchement et d’oubli.

La meilleure façon de créer un `Message` objet consiste à utiliser la [`Message.Obtain`](xref:Android.OS.Message) méthode de fabrique. Cette méthode permet d’extraire `Message` un objet d’un pool global qui est géré par Android. `Message.Obtain`comporte également des méthodes surchargées qui permettent d' `Message` initialiser l’objet avec les valeurs et les paramètres requis par le service.  Une fois `Message` la est instanciée, elle est distribuée au service en appelant `Messenger.Send`. Cet extrait de code est un exemple de création et de distribution `Message` d’un au processus de service:

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

Il existe plusieurs formes différentes de la `Message.Obtain` méthode. L’exemple précédent utilise [`Message.Obtain(Handler h, Int32 what)`](xref:Android.OS.Message.Obtain). Parce qu’il s’agit d’une demande asynchrone à un service hors processus; Il n’y aura pas de réponse du service, donc `Handler` la valeur de `null`est. Le deuxième paramètre, `Int32 what`, est stocké dans la `.What` propriété de l' `Message` objet. La `.What` propriété est utilisée par le code dans le processus de service pour appeler des méthodes sur le service.

La `Message` classe expose également deux propriétés supplémentaires qui peuvent être utilisées par le destinataire: `Arg1` et `Arg2`. Ces deux propriétés sont des valeurs entières qui peuvent avoir des valeurs spéciales accordées sur des valeurs qui ont une signification entre le client et le service. Par exemple, `Arg1` peut contenir un ID client et `Arg2` peut contenir un numéro de bon de commande pour ce client. Le [`Method.Obtain(Handler h, Int32 what, Int32 arg1, Int32 arg2)`](xref:Android.OS.Message.Obtain) peut être utilisé pour définir les deux propriétés lors de `Message` la création de. Une autre façon de remplir ces deux valeurs consiste à définir `.Arg` les `.Arg2` propriétés et directement sur `Message` l’objet après qu’il a été créé.

### <a name="passing-additional-values-to-the-service"></a>Passage de valeurs supplémentaires au service

Il est possible de transmettre des données plus complexes au service à l’aide `Bundle`d’un. Dans ce cas, les valeurs supplémentaires peuvent être placées `Bundle` dans un et envoyées avec `Message` le en définissant la [ `.Data` ](xref:Android.OS.Message.Data) propriété de propriété avant l’envoi.

```csharp
Bundle serviceParameters = new Bundle();
serviceParameters.

var msg = Message.Obtain(null, Constants.SERVICE_TASK_TO_PERFORM);
msg.Data = serviceParameters;

messenger.Send(msg);
```


> [!NOTE]
> En général, un `Message` ne doit pas avoir une charge utile supérieure à 1 Mo. La limite de taille peut varier selon la version d’Android et pour toute modification propriétaire apportée par le fournisseur à son implémentation du projet open source Android (AOSP) fourni avec l’appareil.

## <a name="returning-values-from-the-service"></a>Retour de valeurs à partir du service

L’architecture de messagerie qui a été évoquée à ce stade est unidirectionnelle, le client envoie un message au service. S’il est nécessaire que le service retourne une valeur à un client, tout ce qui a été abordé à ce stade est inversé. Le service doit créer un `Message`, empaqueter toutes les valeurs de retour `Message` et distribuer `Messenger` le via un au client. Toutefois, le service ne crée pas son propre `Messenger`. il s’appuie plutôt sur le client qui instancie et le `Messenger` package a dans le cadre de la requête initiale. Le service effectue `Send` le message à l’aide de ce `Messenger`fourni par le client.  

La séquence d’événements pour la communication bidirectionnelle est la suivante:

1. Le client est lié au service. Lorsque le service et le client se connectent `IServiceConnection` , le qui est géré par le client aura une référence à `Messenger` un objet qui est utilisé pour `Message`transmettre des s au service. Pour éviter toute confusion, cette opération est appelée Messenger de _service_.
2. Le client instancie `Handler` un (appelé _Gestionnaire client_) et l’utilise pour initialiser son propre `Messenger` (le _client Messenger_). Notez que le service Messenger et le client Messenger sont deux objets différents qui gèrent le trafic dans deux directions différentes. Le service Messenger gère les messages du client au service, tandis que le client Messenger gère les messages du service vers le client.
3. Le client crée un `Message` objet et définit la `ReplyTo` propriété avec le client Messenger. Le message est ensuite envoyé au service à l’aide du service Messenger.
4. Le service reçoit le message du client et effectue le travail demandé.
5. Lorsqu’il est temps que le service envoie la réponse au client, il utilise `Message.Obtain` pour créer un nouvel `Message` objet.
6. Pour envoyer ce message au client, le service extrait le client Messenger à partir de la `.ReplyTo` propriété du message client et l’utilise `Message` pour `.Send` revenir au client.
7. Lorsque la réponse est reçue par le client, elle a son propre `Handler` `Message` traitement en inspectant la `.What` propriété (et, si nécessaire, en extrayant les paramètres contenus dans le `Message`).

Cet exemple de code montre comment le client instancie `Message` le et le `Messenger` package a que le service doit utiliser pour sa réponse:

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

Le service doit apporter ses propres `Handler` modifications pour extraire le `Messenger` et l’utiliser pour envoyer des réponses au client. Cet extrait de code est un exemple de la façon dont `Handler` le service crée `Message` un et le renvoie au client:  

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

Notez que dans les exemples de code ci- `Messenger` dessus, l’instance qui est créée par le client n’est *pas* le même objet que celui reçu par le service. Il s’agit de `Messenger` deux objets différents qui s’exécutent dans deux processus distincts qui représentent le canal de communication.

## <a name="securing-the-service-with-android-permissions"></a>Sécurisation du service avec des autorisations Android

Un service qui s’exécute dans un processus global est accessible par toutes les applications qui s’exécutent sur cet appareil Android. Dans certains cas, cette ouverture et cette disponibilité ne sont pas souhaitables, et il est nécessaire de sécuriser le service contre tout accès à partir de clients non autorisés. Pour limiter l’accès au service distant, il est possible d’utiliser des autorisations Android.

Les autorisations peuvent être identifiées `Permission` par la propriété `ServiceAttribute` du qui décore `Service` la sous-classe. Cela permet de nommer une autorisation que le client doit recevoir lors de la liaison au service. Si le client ne dispose pas des autorisations appropriées, Android lève une exception lorsque `Java.Lang.SecurityException` le client tente d’établir une liaison avec le service.

Android offre quatre niveaux d’autorisation:

* **normal** &ndash; Il s’agit du niveau d’autorisation par défaut. Il est utilisé pour identifier les autorisations à faible risque qui peuvent être accordées automatiquement par Android aux clients qui le demandent. L’utilisateur n’a pas besoin d’accorder explicitement ces autorisations, mais les autorisations peuvent être affichées dans les paramètres de l’application.
* **signature** &ndash; Il s’agit d’une catégorie spéciale d’autorisation qui sera accordée automatiquement par Android aux applications qui sont toutes signées avec le même certificat. Cette autorisation est conçue pour permettre à un développeur d’applications de partager facilement des composants ou des données entre leurs applications sans avoir à passer l’utilisateur à des approbations constantes.
* **signatureOrSystem** Cela est très similaire aux autorisations de signature décrites ci-dessus. &ndash; En plus d’être accordé automatiquement aux applications qui sont signées par le même certificat, cette autorisation est également accordée aux applications qui sont signées avec le même certificat que celui utilisé pour signer les applications installées avec l’image système Android. En général, cette autorisation est utilisée uniquement par les développeurs de la ROM Android pour permettre à leurs applications de fonctionner avec des applications tierces. Elle n’est généralement pas utilisée par les applications qui sont destinées à la distribution générale pour le grand public.
* **dangereux** &ndash; Les autorisations dangereuses sont celles qui peuvent entraîner des problèmes pour l’utilisateur. Pour cette raison, les autorisations **dangereuses** doivent être approuvées explicitement par l’utilisateur.

Étant `signature` donné `normal` que les autorisations et sont accordées automatiquement au moment de l’installation par Android, il est essentiel que apk hébergeant le service soit installé **avant** le apk contenant le client. Si le client est installé en premier, Android n’accorde pas les autorisations. Dans ce cas, il est nécessaire de désinstaller le client APK, d’installer le service APK, puis de réinstaller le APK client.

Il existe deux façons courantes de sécuriser un service avec des autorisations Android:

1.  **Implémenter la sécurité au niveau des signatures** &ndash; La sécurité au niveau de la signature signifie que l’autorisation est accordée automatiquement aux applications qui sont signées avec la même clé que celle qui a été utilisée pour signer le apk qui détient le service. C’est un moyen simple pour les développeurs de sécuriser leur service tout en les protégeant de leurs propres applications. Les autorisations au niveau de la signature sont `Permission` déclarées en `ServiceAttribute` affectant à la propriété de la valeur `signature`:

    ```csharp
    [Service(Name = "com.xamarin.TimestampService",
             Process="com.xamarin.TimestampService.timestampservice_process",
             Permission="signature")]
    public class TimestampService : Service
    {
    }
    ```

2.  **Créer une autorisation personnalisée** &ndash; Il est possible pour le développeur du service de créer une autorisation personnalisée pour le service. C’est le plus adapté quand un développeur souhaite partager son service avec des applications d’autres développeurs. Une autorisation personnalisée nécessite un peu plus d’effort pour implémenter et sera traitée ci-dessous.

Un exemple simplifié de la création d' `normal` une autorisation personnalisée est décrit dans la section suivante. Pour plus d’informations sur les autorisations Android, consultez la documentation de Google concernant les [meilleures pratiques & la sécurité](https://developer.android.com/training/articles/security-tips.html). Pour plus d’informations sur les autorisations Android, consultez la [section autorisations](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms) de la documentation Android pour le manifeste d’application pour plus d’informations sur les autorisations Android.

> [!NOTE]
> En général, [Google déconseille l’utilisation des autorisations personnalisées](https://developer.android.com/training/articles/security-tips.html#RequestingPermissions) , car elles peuvent s’avérer confuses pour les utilisateurs.

### <a name="creating-a-custom-permission"></a>Création d’une autorisation personnalisée

Pour utiliser une autorisation personnalisée, elle est déclarée par le service alors que le client demande explicitement cette autorisation.

Pour créer une autorisation dans le service apk, un `permission` élément est ajouté à l' `manifest` élément dans **fichier AndroidManifest. xml**. Les `name`attributs, `protectionLevel`et `label` doivent être définis pour cette autorisation. L' `name` attribut doit être défini sur une chaîne qui identifie de façon unique l’autorisation. Le nom s’affiche dans la vue **informations** sur l’application des **paramètres Android** (comme indiqué dans la section suivante).

L' `protectionLevel` attribut doit être défini sur l’une des quatre valeurs de chaîne décrites ci-dessus.  `label` Et`description` doivent faire référence à des ressources de type chaîne et sont utilisés pour fournir un nom convivial et une description à l’utilisateur.

Cet extrait de code est un exemple de déclaration d' `permission` un attribut personnalisé dans **fichier AndroidManifest. xml** du apk qui contient le service:

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

Ensuite, **fichier AndroidManifest. xml** du client apk doit demander explicitement cette nouvelle autorisation. Pour ce faire, ajoutez l' `users-permission` attribut à **fichier AndroidManifest. xml**:

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

Pour afficher les autorisations accordées à une application, ouvrez l’application paramètres Android, puis sélectionnez **applications**. Recherchez et sélectionnez l’application dans la liste. Dans l’écran informations sur l' **application** , appuyez sur **autorisations** pour afficher une vue qui affiche toutes les autorisations accordées à l’application:

[![Captures d’écran d’un appareil Android montrant comment trouver les autorisations accordées à une application](out-of-process-services-images/ipc-06-sml.png)](out-of-process-services-images/ipc-06.png#lightbox)

## <a name="summary"></a>Récapitulatif

Ce guide a été une discussion avancée sur l’exécution d’un service Android dans un processus distant. Les différences entre un service local et un service distant ont été expliquées, ainsi que les raisons pour lesquelles un service distant peut être utile pour la stabilité et les performances d’une application Android. Une fois que vous avez expliqué comment implémenter un service distant et comment un client peut communiquer avec le service, le guide a été mis en place pour fournir une méthode permettant de limiter l’accès au service à partir de clients autorisés uniquement.


## <a name="related-links"></a>Liens associés

- [D](xref:Android.OS.Handler)
- [Message](xref:Android.OS.Message)
- [Messenger](xref:Android.OS.Messenger)
- [ServiceAttribute](xref:Android.App.ServiceAttribute)
- [Attribut exporté](https://developer.android.com/guide/topics/manifest/service-element.html#exported)
- [Les services avec des processus isolés et une classe d’application personnalisée ne parviennent pas à résoudre les surcharges correctement](https://bugzilla.xamarin.com/show_bug.cgi?id=51940)
- [Processus et threads](https://developer.android.com/guide/components/processes-and-threads.html)
- [Manifeste Android-autorisations](https://developer.android.com/guide/topics/manifest/manifest-intro.html#perms)
- [Conseils de sécurité](https://developer.android.com/training/articles/security-tips.html)
- [MessengerServiceDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-messengerservicedemo)
