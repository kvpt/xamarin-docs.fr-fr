---
title: Services liés dans Xamarin. Android
description: Les services liés sont des services Android qui fournissent une interface client-serveur qui peut interagir avec un client (par exemple, une activité Android). Ce guide aborde les principaux composants impliqués dans la création d’un service lié et son utilisation dans une application Xamarin. Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: ae1e8332f1d62a4690863a97f63c0c1bef1ee127
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70119087"
---
# <a name="bound-services-in-xamarinandroid"></a>Services liés dans Xamarin. Android

_Les services liés sont des services Android qui fournissent une interface client-serveur qui peut interagir avec un client (par exemple, une activité Android). Ce guide aborde les principaux composants impliqués dans la création d’un service lié et son utilisation dans une application Xamarin. Android._

## <a name="bound-services-overview"></a>Vue d’ensemble des services liés

Les services qui fournissent une interface client-serveur pour les clients qui interagissent directement avec le service sont appelés _services liés_.  Plusieurs clients peuvent être connectés à une instance unique d’un service en même temps. Le service lié et le client sont isolés les uns des autres. Au lieu de cela, Android fournit une série d’objets intermédiaires qui gèrent l’état de la connexion entre les deux. Cet État est géré par un objet qui implémente l' [`Android.Content.IServiceConnection`](xref:Android.Content.IServiceConnection) interface.  Cet objet est créé par le client et passé en tant que paramètre à [`BindService`](xref:Android.Content.Context.BindService*) la méthode. Est disponible sur n’importe [`Android.Content.Context`](xref:Android.Content.Context) quel objet (par exemple, une activité). `BindService` Il s’agit d’une demande adressée au système d’exploitation Android pour démarrer le service et y lier un client. Il existe trois façons pour un client de se lier à un service à `BindService` l’aide de la méthode:

- **Un classeur de services** Un Binder de service est une classe qui implémente l' [`Android.OS.IBinder`](xref:Android.OS.IBinder) interface. &ndash; La plupart des applications n’implémentent pas cette interface directement, mais elles [`Android.OS.Binder`](xref:Android.OS.Binder) étendent la classe. Il s’agit de l’approche la plus courante, qui convient lorsque le service et le client existent dans le même processus.
- **Utilisation d’un Messenger** &ndash; Cette technique convient lorsque le service peut exister dans un processus séparé. Au lieu de cela, les demandes de service sont marshalées entre le [`Android.OS.Messenger`](xref:Android.OS.Messenger)client et le service via un. Un [`Android.OS.Handler`](xref:Android.OS.Handler) est créé dans le service qui gérera les `Messenger` demandes. Ce sujet sera abordé dans un autre guide.
- **Utilisation du langage de définition d’interface Android (AIDL)** &ndash; [ AIDL](https://developer.android.com/guide/components/aidl) est une technique avancée qui n’est pas abordée dans ce guide.

Une fois qu’un client a été lié à un service, la communication entre les deux `Android.OS.IBinder` est effectuée via l’objet.  Cet objet est responsable de l’interface qui permettra au client d’interagir avec le service. Pour chaque application Xamarin. Android, il n’est pas nécessaire d’implémenter cette interface à partir de zéro [`Android.OS.Binder`](xref:Android.OS.Binder) , le Android SDK fournit la classe qui prend en charge la majeure partie du code nécessaire au marshaling de l’objet entre le client et le service.

Lorsqu’un client est effectué avec le service, il doit le séparer en appelant la `UnbindService` méthode. Une fois que le dernier client a indépendant d’un service, Android s’arrête et supprime le service lié.

Ce diagramme illustre comment l’activité, la connexion de service, le Binder et le service sont tous liés:

![Diagramme montrant la relation entre les composants de service et les uns avec les autres](bound-services-images/bound-services-02.png "Diagramme montrant la relation entre les composants de service et les uns avec les autres.")

Ce guide explique comment étendre la `Service` classe pour implémenter un service lié. Il couvre également l’implémentation `IServiceConnection` et l' `Binder` extension de pour permettre à un client de communiquer avec le service. Un exemple d’application accompagne ce guide, qui contient une solution avec un seul projet Xamarin. Android appelé **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Il s’agit d’une application de base qui montre comment implémenter un service et comment y lier une activité. Le service lié a une API très simple avec une seule méthode, `GetFormattedTimestamp`, qui retourne une chaîne qui indique à l’utilisateur quand le service a démarré et combien de temps il a été exécuté. L’application permet également à l’utilisateur de se dissocier manuellement et de se lier au service.

[![Capture d’écran de l’application en cours d’exécution sur un téléphone Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implémentation et utilisation d’un service lié

Il existe trois composants qui doivent être implémentés pour qu’une application Android utilise un service lié:

1. **Étendez `Service` la classe et implémentez les méthodes** &ndash; de rappel de cycle de vie. cette classe contiendra le code qui exécutera le travail qui sera demandé par le service. Ce sujet sera traité plus en détail ci-dessous.
2. **Créer une classe qui implémente `IServiceConnection`**  &ndash; cette interface fournit des méthodes de rappel appelées par Android pour notifier le client lorsque la connexion au service a changé, c.-à-d. que le client s’est connecté ou déconnecté du services. La connexion de service fournira également une référence à un objet que le client peut utiliser pour interagir directement avec le service. Cette référence est connue sous le nom de _Binder_.
3. **Créer une classe qui implémente `IBinder`**  &ndash; une implémentation de _Binder_ fournit l’API qu’un client utilise pour communiquer avec le service. Le Binder peut fournir une référence au service lié, ce qui permet aux méthodes d’être appelées directement ou le Binder peut fournir une API cliente qui encapsule et masque le service lié à partir de l’application. Un `IBinder` doit fournir le code nécessaire pour les appels de procédure distante. Il n’est pas nécessaire (ou recommandé) d’implémenter l' `IBinder` interface directement. Au lieu de cela, `Binder` les applications doivent étendre le type qui fournit la plupart des `IBinder`fonctionnalités de base requises par un.
4. **Démarrage et liaison à un service** &ndash; Une fois la connexion au service, le Binder et le service créés, l’application Android est responsable du démarrage du service et de sa liaison.

Chacune de ces étapes sera abordée plus en détail dans les sections suivantes.

### <a name="extend-the-service-class"></a>Étendre la `Service` classe

Pour créer un service à l’aide de Xamarin. Android, il est nécessaire `Service` de sous-classe et d’ornementer la classe [`ServiceAttribute`](xref:Android.App.ServiceAttribute)avec. L’attribut est utilisé par les outils de génération Xamarin. Android pour inscrire correctement le service dans le fichier **fichier AndroidManifest. xml** de l’application, à l’instar d’une activité, un service lié a ses propres méthodes de cycle de vie et de rappel associées aux événements significatifs dans son cycle de vie. La liste suivante est un exemple de certaines des méthodes de rappel les plus courantes qu’un service doit implémenter:

- `OnCreate`&ndash; Cette méthode est appelée par Android au fur et à mesure qu’elle instancie le service. Elle est utilisée pour initialiser les variables ou objets requis par le service pendant sa durée de vie. Cette méthode est facultative.
- `OnBind`&ndash; Cette méthode doit être implémentée par tous les services liés. Elle est appelée lorsque le premier client tente de se connecter au service. Elle renverra une instance de `IBinder` afin que le client puisse interagir avec le service. Tant que le service est en cours d’exécution `IBinder` , l’objet est utilisé pour accomplir toutes les demandes futures du client à lier au service.
- `OnUnbind`&ndash; Cette méthode est appelée lorsque tous les clients liés ont une liaison détachée. En retournant `true` à partir de cette méthode, le service `OnRebind` appellera ultérieurement avec l’intention `OnUnbind` passée à quand de nouveaux clients s’y lient. Cela se produit lorsqu’un service continue à s’exécuter après qu’il a été indépendant. Cela se produit si le service récemment non lié était également un service démarré et `StopService` ou `StopSelf` n’a pas été appelé. Dans ce type de scénario `OnRebind` , autorise la récupération de l’intention. La valeur par `false` défaut retourne, ce qui ne fait rien. facultatif.
- `OnDestroy`&ndash; Cette méthode est appelée quand Android détruit le service. Tout nettoyage nécessaire, tel que la libération de ressources, doit être effectué dans cette méthode. facultatif.

Les événements de cycle de vie des clés d’un service lié sont présentés dans ce diagramme:

![Diagramme montrant l’ordre dans lequel les méthodes de cycle de vie sont appelées](bound-services-images/bound-services-01.png "Diagramme montrant l’ordre dans lequel les méthodes de cycle de vie sont appelées.")

L’extrait de code suivant, issu de l’application associée qui accompagne ce guide, montre comment implémenter un service lié dans Xamarin. Android:

```csharp
using Android.App;
using Android.Util;
using Android.Content;
using Android.OS;

namespace BoundServiceDemo
{
    [Service(Name="com.xamarin.ServicesDemo1")]
    public class TimestampService : Service, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampService).FullName;
        IGetTimestamp timestamper;

        public IBinder Binder { get; private set; }

        public override void OnCreate()
        {
            // This method is optional to implement
            base.OnCreate();
            Log.Debug(TAG, "OnCreate");
            timestamper = new UtcTimestamper();
        }

        public override IBinder OnBind(Intent intent)
        {
            // This method must always be implemented
            Log.Debug(TAG, "OnBind");
            this.Binder = new TimestampBinder(this);
            return this.Binder;
        }

        public override bool OnUnbind(Intent intent)
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnUnbind");
            return base.OnUnbind(intent);
        }

        public override void OnDestroy()
        {
            // This method is optional to implement
            Log.Debug(TAG, "OnDestroy");
            Binder = null;
            timestamper = null;
            base.OnDestroy();
        }

        /// <summary>
        /// This method will return a formatted timestamp to the client.
        /// </summary>
        /// <returns>A string that details what time the service started and how long it has been running.</returns>
        public string GetFormattedTimestamp()
        {
            return timestamper?.GetFormattedTimestamp();
        }
    }
}
```

Dans l’exemple, la `OnCreate` méthode initialise un objet qui contient la logique de récupération et de mise en forme d’un horodatage qui est demandé par un client. Lorsque le premier client tente d’établir une liaison avec le service, Android appellera `OnBind` la méthode. Ce service instancie un `TimestampBinder` objet qui permettra aux clients d’accéder à cette instance du service en cours d’exécution. La `TimestampBinder` classe est décrite dans la section suivante.

### <a name="implementing-ibinder"></a>Implémentation de IBinder

Comme mentionné, un `IBinder` objet fournit le canal de communication entre un client et un service. Les applications Android ne doivent pas `IBinder` implémenter l' [`Android.OS.Binder`](xref:Android.OS.Binder) interface, le doit être étendu. La `Binder` classe fournit la majeure partie de l’infrastructure nécessaire, qui est nécessaire pour marshaler l’objet Binder à partir du service (qui peut s’exécuter dans un processus distinct) au client. Dans la plupart des cas `Binder` , la sous-classe n’est qu’un petit nombre de lignes de code et encapsule une référence au service. Dans cet exemple, `TimestampBinder` a une propriété qui expose `TimestampService` au client:

```csharp
public class TimestampBinder : Binder
{
    public TimestampBinder(TimestampService service)
    {
        this.Service = service;
    }

    public TimestampService Service { get; private set; }
}
```

Cela `Binder` permet d’appeler les méthodes publiques sur le service, par exemple:

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Une `Binder` fois l’extension terminée, il est nécessaire d' `IServiceConnection` implémenter pour connecter tout ensemble.

### <a name="creating-the-service-connection"></a>Création de la connexion de service

Le `IServiceConnection` sera présent | introduire | exposer | connecter l' `Binder` objet au client. En plus d’implémenter `IServiceConnection` l’interface, la classe doit `Java.Lang.Object`étendre. La connexion de service doit également fournir une méthode permettant au client d’accéder `Binder` au (et donc de communiquer avec le service lié).

Ce code provient de l’exemple de projet d’accompagnement est un moyen possible `IServiceConnection`d’implémenter:

```csharp
using Android.Util;
using Android.OS;
using Android.Content;

namespace BoundServiceDemo
{
    public class TimestampServiceConnection : Java.Lang.Object, IServiceConnection, IGetTimestamp
    {
        static readonly string TAG = typeof(TimestampServiceConnection).FullName;

        MainActivity mainActivity;
        public TimestampServiceConnection(MainActivity activity)
        {
            IsConnected = false;
            Binder = null;
            mainActivity = activity;
        }

        public bool IsConnected { get; private set; }
        public TimestampBinder Binder { get; private set; }

        public void OnServiceConnected(ComponentName name, IBinder service)
        {
            Binder = service as TimestampBinder;
            IsConnected = this.Binder != null;

            string message = "onServiceConnected - ";
            Log.Debug(TAG, $"OnServiceConnected {name.ClassName}");

            if (IsConnected)
            {
                message = message + " bound to service " + name.ClassName;
                mainActivity.UpdateUiForBoundService();
            }
            else
            {
                message = message + " not bound to service " + name.ClassName;
                mainActivity.UpdateUiForUnboundService();
            }

            Log.Info(TAG, message);
            mainActivity.timestampMessageTextView.Text = message;

        }

        public void OnServiceDisconnected(ComponentName name)
        {
            Log.Debug(TAG, $"OnServiceDisconnected {name.ClassName}");
            IsConnected = false;
            Binder = null;
            mainActivity.UpdateUiForUnboundService();
        }

        public string GetFormattedTimestamp()
        {
            if (!IsConnected)
            {
                return null;
            }

            return Binder?.GetFormattedTimestamp();
        }
    }
}

```

Dans le cadre du processus de liaison, Android appellera la `OnServiceConnected` méthode, en fournissant le `name` du service qui est lié et le `binder` qui contient une référence au service lui-même. Dans cet exemple, la connexion de service possède deux propriétés: une qui contient une référence au Binder et un indicateur booléen pour si le client est connecté au service ou non.

La `OnServiceDisconnected` méthode est appelée uniquement lorsque la connexion entre un client et un service est perdue ou interrompue de manière inattendue. Cette méthode permet au client de répondre à l’interruption du service.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Démarrage et liaison à un service avec une intention explicite

Pour utiliser un service lié, un client (tel qu’une activité) doit instancier un objet qui implémente `Android.Content.IServiceConnection` et appelle la `BindService` méthode. `BindService`retourne `true` si le service est lié à, `false` si ce n’est pas le cas. La méthode `BindService` accepte trois paramètres :

- L’intention doit identifier explicitement le service auquel se connecter. **`Intent`** &ndash;
- **Objet`IServiceConnection`**  cetobjetestunintermédiairequifournitdesméthodesderappelpournotifierleclientlorsqueleservice&ndash; lié est démarré et arrêté.
- enum ce paramètre est un ensemble d’indicateurs utilisés par le système pour la liaison de l’objet. **[`Android.Content.Bind`](xref:Android.Content.Bind)** &ndash; La valeur la plus couramment utilisée [`Bind.AutoCreate`](xref:Android.Content.Bind.AutoCreate)est, qui démarre automatiquement le service s’il n’est pas déjà en cours d’exécution.

L’extrait de code suivant est un exemple de démarrage d’un service lié dans une activité à l’aide d’une intention explicite:

```csharp
protected override void OnStart ()
{
    base.OnStart ();

    if (serviceConnection == null)
    {
        this.serviceConnection = new TimestampServiceConnection(this);
    }

    Intent serviceToStart = new Intent(this, typeof(TimestampService));
    BindService(serviceToStart, this.serviceConnection, Bind.AutoCreate);

}
```

> [!IMPORTANT]
> À compter d’Android 5,0 (niveau d’API 21), il n’est possible de lier à un service qu’avec une intention explicite.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Remarques architecturales sur la connexion de service et le Binder.

Certains puristes OOP peuvent désapprouver l’implémentation précédente de la `TimestampBinder` classe, car il s’agit d’une violation de la [Loi de Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter) qui, dans les États de forme les plus simples, ne communique pas avec des inconnus. Communiquez avec vos amis uniquement». Cette implémentation particulière expose la classe `TimestampService` concrète à tous les clients.

Strictement parlant, le client n’a pas besoin de connaître le `TimestampService` et d’exposer que la classe concrète aux clients peut rendre une application plus fragile et plus difficile à gérer au fil de sa durée de vie. Une autre approche consiste à utiliser une interface qui expose la `GetFormattedTimestamp()` méthode, et les appels de proxy au service `Binder` via (ou la classe de connexion de service):  

```csharp
public class TimestampBinder : Binder, IGetTimestamp
{
    TimestampService service;
    public TimestampBinder(TimestampService service)
    {
        this.service = service;
    }

    public string GetFormattedTimestamp()
    {
        return service?.GetFormattedTimestamp();
    }
}
```

Cet exemple particulier permet à une activité d’appeler des méthodes sur le service lui-même:

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>Liens associés

- [Android.App.Service](xref:Android.App.Service)
- [Android.Content.Bind](xref:Android.Content.Bind)
- [Android.Content.Context](xref:Android.Content.Context)
- [Android.Content.IServiceConnection](xref:Android.Content.IServiceConnection)
- [Android.OS.Binder](xref:Android.OS.Binder)
- [Android.OS.IBinder](xref:Android.OS.IBinder)
- [BoundServiceDemo (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/applicationfundamentals-servicesamples-boundservicedemo)
