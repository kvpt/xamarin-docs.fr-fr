---
title: Services limités dans Xamarin.Android
description: Services liés sont Android qui fournissent une interface client-serveur interagissant avec un client (par exemple, une activité Android). Ce guide explique les composants clés liés à la création d’un service lié et comment l’utiliser dans une application Xamarin.Android.
ms.prod: xamarin
ms.assetid: 809ECE88-EF08-4E9A-B389-A2DC08C51A6E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 05/04/2018
ms.openlocfilehash: 490331663d94a1e3130fc794a11a52acdacca014
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67829744"
---
# <a name="bound-services-in-xamarinandroid"></a>Services limités dans Xamarin.Android

_Services liés sont Android qui fournissent une interface client-serveur interagissant avec un client (par exemple, une activité Android). Ce guide explique les composants clés liés à la création d’un service lié et comment l’utiliser dans une application Xamarin.Android._

## <a name="bound-services-overview"></a>Vue d’ensemble des Services liés

Les services qui fournissent une interface client-serveur pour les clients d’interagir directement avec le service sont désignés en tant que _lié services_.  Il peut y avoir plusieurs clients connectés à une seule instance d’un service en même temps. Le service lié et le client sont isolés les uns des autres. Au lieu de cela, Android fournit une série d’objets intermédiaires qui gèrent l’état de la connexion entre les deux. Cet état est géré par un objet qui implémente le [ `Android.Content.IServiceConnection` ](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/) interface.  Cet objet est créé par le client et transmis en tant que paramètre à la [ `BindService` ](https://developer.xamarin.com/api/member/Android.Content.Context.BindService/) (méthode). Le `BindService` est disponible sur n’importe quel [ `Android.Content.Context` ](https://developer.xamarin.com/api/type/Android.Content.Context) objet (par exemple, une activité). Il s’agit une demande au système d’exploitation Android pour démarrer le service et le lier un client. Il existe trois façons pour un client peut lier à un service à l’aide de la `BindService` méthode :

* **Un classeur de service** &ndash; un classeur de service est une classe qui implémente le [ `Android.OS.IBinder` ](https://developer.xamarin.com/api/type/Android.OS.IBinder) interface. La plupart des applications n’implémentez pas cette interface directement, au lieu de cela, elles s’étendent le [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe. Ceci est l’approche la plus courante et convient lorsque le client et le service existent dans le même processus.
* **À l’aide d’un Messenger** &ndash; cette technique est adaptée lorsque le service peut exister dans un processus distinct. Au lieu de cela, les demandes de service sont marshalés entre le client et le service via un [ `Android.OS.Messenger` ](https://developer.xamarin.com/api/type/Android.OS.Messenger). Un [ `Android.OS.Handler` ](https://developer.xamarin.com/api/type/Android.OS.Handler) est créé dans le service qui gère la `Messenger` demandes. Ce point sera abordé dans un autre guide.
* **À l’aide du langage de définition d’Interface Android (AIDL)** &ndash; [AIDL](https://developer.android.com/guide/components/aidl) est une technique avancée qui n’est pas traitée dans ce guide.

Une fois qu’un client a été lié à un service, la communication entre les deux est se produit `Android.OS.IBinder` objet.  Cet objet est responsable de l’interface qui permet au client d’interagir avec le service. Il n’est pas nécessaire pour chaque application Xamarin.Android implémenter cette interface à partir de zéro, le SDK Android fournit le [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder) classe qui prend en charge de la plupart du code requis avec l’objet entre de marshaling le client et le service.

Quand un client est terminé avec le service, il doit supprimer la liaison à partir de celui-ci en appelant le `UnbindService` (méthode). Une fois que le dernier client a indépendant à partir d’un service, Android arrête et supprime du service lié.

Ce diagramme illustre comment l’activité, connexion de service, liant et service liés entre eux :

![Un diagramme montrant comment les composants de service sont liés entre eux](bound-services-images/bound-services-02.png "un diagramme montrant comment les composants de service sont liés entre eux.")

Ce guide explique comment étendre le `Service` classe pour implémenter un service lié. Il couvre également implémenter `IServiceConnection` et l’extension `Binder` pour permettre à un client communiquer avec le service. Un exemple d’application de ce guide, qui contiennent une solution avec un seul projet Xamarin.Android appelé **[BoundServiceDemo](https://github.com/xamarin/monodroid-samples/tree/master/ApplicationFundamentals/ServiceSamples/BoundServiceDemo)** . Il s’agit d’une application très simple qui montre comment implémenter un service et comment lier une activité. Le service lié a une API très simple avec une seule méthode, `GetFormattedTimestamp`, qui retourne une chaîne qui indique à l’utilisateur lorsque le service a démarré et la durée pendant laquelle il a été exécuté. L’application permet également à l’utilisateur à annuler la liaison et la liaison avec le service manuellement.

[![Capture d’écran de l’application en cours d’exécution sur un téléphone Android](bound-services-images/bound-services-03-sml.png)](bound-services-images/bound-services-03.png#lightbox)

## <a name="implementing-and-consuming-a-bound-service"></a>Implémentation et utilisation d’un Service lié

Il existe trois composants qui doivent être implémentées dans l’ordre pour une application Android utiliser un service lié :

1. **Étendre la `Service` classe et implémenter les méthodes de rappel du cycle de vie** &ndash; cette classe contiendra le code qui effectue le travail qui est demandé du service. Ce point sera abordé plus en détail ci-dessous.
2. **Créer une classe qui implémente `IServiceConnection`**  &ndash; cette interface fournit des méthodes de rappel seront appelée par Android pour informer le client lorsque la connexion au service a été modifiée, par exemple, le client a connecté ou déconnecté à la service. La connexion de service fournit également une référence à un objet que le client peut utiliser pour interagir directement avec le service. Cette référence est connue comme le _binder_.
3. **Créer une classe qui implémente `IBinder`**  &ndash; A _Binder_ implémentation fournit l’API un client utilise pour communiquer avec le service. Le Binder peut fournir une référence au service lié, autorisant des méthodes à appeler directement ou le classeur peut fournir une API qui encapsule et masque le service lié à partir de l’application cliente. Un `IBinder` doit fournir le code nécessaire pour les appels de procédure distante. Il n’est pas nécessaire (ou recommandé) pour implémenter le `IBinder` interface directement. À la place des applications doivent étendre la `Binder` type qui fournit la plupart des fonctionnalités de base requises par une `IBinder`.
4. **Démarrage et la liaison à un Service** &ndash; une fois la connexion de service, le classeur et le service ont été créés l’application Android est responsable du démarrage du service et la liaison à ce dernier.

Chacune de ces étapes est abordée dans les sections suivantes plus en détail.

### <a name="extend-the-service-class"></a>Étendre la `Service` classe

Pour créer un service à l’aide de Xamarin.Android, il est nécessaire de sous-classe `Service` et orner la classe avec le [ `ServiceAttribute` ](https://developer.xamarin.com/api/type/Android.App.ServiceAttribute). L’attribut est utilisé par les outils de génération de Xamarin.Android pour enregistrer correctement le service de l’application **AndroidManifest.xml** comme une activité de fichiers, un service lié a son propre cycle de vie et de rappel méthodes associées à la événements importants dans son cycle de vie. La liste suivante est un exemple de certaines des méthodes de rappel plus courantes qui implémentera d’un service :

* `OnCreate` &ndash; Cette méthode est appelée par Android comme il consiste à instancier le service. Il est utilisé pour initialiser des variables ou des objets qui sont requis par le service pendant sa durée de vie. Cette méthode est facultative.
* `OnBind` &ndash; Cette méthode doit être implémentée par tous les services liés. Elle est appelée lorsque le premier client essaie de se connecter au service. Il retourne une instance de `IBinder` afin que le client peut interagir avec le service. Tant que le service est en cours d’exécution, le `IBinder` objet sera utilisé pour répondre à toutes les demandes ultérieures du client pour lier le service.
* `OnUnbind` &ndash; Cette méthode est appelée lorsque tous les clients liés ont indépendant. En retournant `true` à partir de cette méthode, le service appelle ultérieurement `OnRebind` avec l’intention passée à `OnUnbind` lorsque les clients de nouveau le lier. Pour cela, vous devez utiliser lorsqu’un service poursuit en cours d’exécution après que qu’elle a été détachée. Cela se produit si le service récemment indépendant ont été également un service démarré, et `StopService` ou `StopSelf` n’avaient pas été appelée. Dans ce scénario, `OnRebind` permet à l’intention à récupérer. Retourne la valeur par défaut `false` , qui ne fait rien. facultatif.
* `OnDestroy` &ndash; Cette méthode est appelée lors de la Android est destruction du service. Les nettoyages nécessaires, telles que la libération des ressources, doivent être effectuées dans cette méthode. facultatif.

Les événements de cycle de vie d’un service lié sont affichés dans ce diagramme :

![Un diagramme indiquant l’ordre dans lequel les méthodes de cycle de vie sont appelées](bound-services-images/bound-services-01.png "un diagramme indiquant l’ordre dans lequel les méthodes de cycle de vie sont appelées.")

L’extrait de code suivant, à partir de l’application d’accompagnement qui accompagne ce guide, montre comment implémenter un service lié dans Xamarin.Android :

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

Dans l’exemple, le `OnCreate` méthode initialise un objet qui conserve la logique pour la récupération et de mise en forme d’un horodatage qui est demandé par un client. Lorsque le premier client essaie de lier au service, Android appellera le `OnBind` (méthode). Ce service instanciera un `TimestampBinder` objet qui autorise les clients pour accéder à cette instance du service en cours d’exécution. Le `TimestampBinder` classe est décrite dans la section suivante.

### <a name="implementing-ibinder"></a>Implémentation IBinder

Comme mentionné, un `IBinder` objet fournit le canal de communication entre un client et un service. Les applications Android ne doivent pas implémenter le `IBinder` interface, le [ `Android.OS.Binder` ](https://developer.xamarin.com/api/type/Android.OS.Binder/) doit être étendu. Le `Binder` classe fournit une grande partie de l’infrastructure nécessaire qui est nécessaire marshaler l’objet de classeur à partir du service (qui peut être en cours d’exécution dans un processus distinct) au client. Dans la plupart des cas, le `Binder` sous-classe est seulement quelques lignes de code et encapsule une référence au service. Dans cet exemple, `TimestampBinder` a une propriété qui expose le `TimestampService` au client :

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

Cela `Binder` rend possible d’appeler les méthodes publiques sur le service ; par exemple :

```csharp
string currentTimestamp = serviceConnection.Binder.Service.GetFormattedTimestamp()
```

Une fois `Binder` a été étendu, il est nécessaire d’implémenter `IServiceConnection` interconnecter tous les éléments.

### <a name="creating-the-service-connection"></a>Création de la connexion de Service

Le `IServiceConnection` présentera | introduire | exposer | connecter le `Binder` objet au client. En plus d’implémenter le `IServiceConnection` interface, la classe doit étendre `Java.Lang.Object`. La connexion de service doit également fournir une façon qui le client peut accéder à la `Binder` (et donc communiquer avec le service lié).

Ce code est tiré l’accompagnant exemple de projet est une manière possible d’implémenter `IServiceConnection`:

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

Dans le cadre du processus de liaison, Android appellera le `OnServiceConnected` (méthode), fournissant le `name` du service qui est lié et le `binder` qui contient une référence au service proprement dit. Dans cet exemple, la connexion de service a deux propriétés, qui contient une référence pour le classeur et un indicateur booléen de si le client est connecté au service ou non.

Le `OnServiceDisconnected` méthode est appelée uniquement lorsque la connexion entre un client et un service est perdue ou répartie de façon inattendue. Cette méthode permet au client une chance de répondre à l’interruption de service.  

## <a name="starting-and-binding-to-a-service-with-an-explicit-intent"></a>Démarrage et la liaison à un Service avec l’intention explicite

Pour utiliser un service lié, un client (par exemple, une activité) doit instancier un objet qui implémente `Android.Content.IServiceConnection` et appeler le `BindService` (méthode). `BindService` retournera `true` si le service est lié, `false` si elle n’est pas. La méthode `BindService` accepte trois paramètres :

* **Un `Intent`**  &ndash; l’intention doit identifier de manière explicite quel service pour se connecter à.
* **Un `IServiceConnection` objet** &ndash; cet objet est un intermédiaire qui fournit des méthodes de rappel pour avertir le client du service lié est démarré et arrêté.
* **[`Android.Content.Bind`](https://developer.xamarin.com/api/type/Android.Content.Bind/) enum** &ndash; ce paramètre est un jeu d’indicateurs utilisées par le système pour la liaison lorsque l’objet. La valeur la plus couramment utilisée est [ `Bind.AutoCreate` ](https://developer.xamarin.com/api/field/Android.Content.Bind.AutoCreate/), qui démarre automatiquement le service si elle n’est pas déjà en cours d’exécution.

L’extrait de Code suivant est un exemple montrant comment démarrer un service lié dans une activité à l’aide d’une intention explicite :

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
> En commençant avec Android 5.0 (niveau 21 d’API), il n’est possible de lier à un service avec l’intention explicite.

## <a name="architectural-notes-about-the-service-connection-and-the-binder"></a>Notes de publication architecturales sur la connexion de Service et le classeur.

Certains puristes de la programmation orientée objet peuvent désapprouver de l’implémentation précédente de la `TimestampBinder` classe car il s’agit d’une violation de la [loi de Déméter](https://en.wikipedia.org/wiki/Law_of_Demeter) , dans sa forme la plus simple indiquant « ne parlez pas aux personnes étrangères ; uniquement communiquer avec vos amis ». Cette implémentation particulière expose le concret `TimestampService` classe à tous les clients.

À proprement parler, il n’est pas nécessaire pour le client connaître le `TimestampService` et exposer cette classe concrète aux clients peut rendre une application plus fragile et plus difficile à gérer pendant sa durée de vie. Une autre approche consiste à utiliser une interface qui expose le `GetFormattedTimestamp()` (méthode) et les appels de proxy au service via le `Binder` (ou possible de la classe de connexion de service) :  

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

Cet exemple particulier autorisent une activité à appeler des méthodes sur le service proprement dit :

```csharp
// In this example the Activity is only talking to a friend, i.e. the IGetTimestamp interface provided by the Binder.
string currentTimestamp = serviceConnection.Binder.GetFormattedTimestamp()
```


## <a name="related-links"></a>Liens associés

- [Android.App.Service](https://developer.xamarin.com/api/type/Android.App.Service/)
- [Android.Content.Bind](https://developer.xamarin.com/api/type/Android.Content.Bind/)
- [Android.Content.Context](https://developer.xamarin.com/api/type/Android.Content.Context/)
- [Android.Content.IServiceConnection](https://developer.xamarin.com/api/type/Android.Content.IServiceConnection/)
- [Android.OS.Binder](https://developer.xamarin.com/api/type/Android.OS.Binder/)
- [Android.OS.IBinder](https://developer.xamarin.com/api/type/Android.OS.IBinder)
- [BoundServiceDemo (sample)](https://developer.xamarin.com/samples/monodroid/ApplicationFundamentals/ServiceSamples/BoundServiceDemo/)
