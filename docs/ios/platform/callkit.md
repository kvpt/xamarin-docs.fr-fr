---
title: CallKit dans Xamarin. iOS
description: Cet article décrit la nouvelle API CallKit qu’Apple a publiée dans iOS 10 et comment l’implémenter dans les applications VOIP Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 738A142D-FFD2-4738-B3ED-57C273179848
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/15/2017
ms.openlocfilehash: aa57ae8a0f8254a715893b155d34e20297ec5c73
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68653013"
---
# <a name="callkit-in-xamarinios"></a>CallKit dans Xamarin. iOS

La nouvelle API CallKit dans iOS 10 permet aux applications VOIP de s’intégrer à l’interface utilisateur iPhone et de fournir une interface et une expérience familières à l’utilisateur final. Avec cette API, les utilisateurs peuvent afficher et interagir avec les appels VOIP à partir de l’écran de verrouillage de l’appareil iOS et gérer les contacts à l’aide des **favoris** de l’application téléphonique et des vues **récentes** .

## <a name="about-callkit"></a>À propos de CallKit

D’après Apple, CallKit est une nouvelle infrastructure qui élève les applications VOIP (voix sur IP) tierces à une expérience de premier tiers sur iOS 10. L’API CallKit permet aux applications VOIP de s’intégrer à l’interface utilisateur iPhone et de fournir une interface et une expérience familières à l’utilisateur final. Tout comme l’application téléphonique intégrée, un utilisateur peut afficher et interagir avec les appels VOIP à partir de l’écran de verrouillage de l’appareil iOS et gérer les contacts à l’aide des **favoris** de l’application téléphonique et des vues **récentes** .

En outre, l’API CallKit offre la possibilité de créer des extensions d’application qui peuvent associer un numéro de téléphone à un nom (ID d’appelant) ou indiquer au système quand un nombre doit être bloqué (blocage d’appel).

### <a name="the-existing-voip-app-experience"></a>L’expérience d’application VOIP existante

Avant de discuter de la nouvelle API CallKit et de ses capacités, jetez un coup d’œil sur l’expérience utilisateur actuelle avec une application VOIP tierce dans iOS 9 (et plus faible) à l’aide d’une application VOIP fictive appelée MonkeyCall. MonkeyCall est une application simple qui permet à l’utilisateur d’envoyer et de recevoir des appels VOIP à l’aide des API iOS existantes.

Actuellement, si l’utilisateur reçoit un appel entrant sur MonkeyCall et que son iPhone est verrouillé, la notification reçue sur l’écran de verrouillage ne peut pas être différenciée de tout autre type de notification (comme celles des messages ou des applications de messagerie, par exemple).

Si l’utilisateur souhaite répondre à l’appel, il doit faire glisser la notification MonkeyCall pour ouvrir l’application et entrer son code secret (ou l’ID utilisateur Touch) pour déverrouiller le téléphone avant qu’il puisse accepter l’appel et démarrer la conversation.

L’expérience est également lourde si le téléphone est déverrouillé. Là encore, l’appel MonkeyCall entrant est affiché sous la forme d’une bannière de notification standard qui glisse à partir du haut de l’écran. Étant donné que la notification est temporaire, elle peut être facilement manquée par l’utilisateur qui la force à ouvrir le centre de notifications et à trouver la notification spécifique à répondre, puis à appeler ou à rechercher et à lancer l’application MonkeyCall manuellement.

### <a name="the-callkit-voip-app-experience"></a>Expérience de l’application VOIP CallKit

En implémentant les nouvelles API CallKit dans l’application MonkeyCall, l’expérience de l’utilisateur avec un appel VOIP entrant peut être très améliorée dans iOS 10. Prenons l’exemple de l’utilisateur recevant un appel VOIP quand son téléphone est verrouillé par-dessus. En implémentant CallKit, l’appel s’affiche sur l’écran de verrouillage de l’iPhone, comme c’est le cas si l’appel a été reçu à partir de l’application téléphonique intégrée, avec l’interface utilisateur native, l’interface utilisateur native et la fonctionnalité de glissement-réponse standard.

Là encore, si l’iPhone est déverrouillé lors de la réception d’un appel VOIP MonkeyCall, les mêmes fonctionnalités d’interface utilisateur native et de prise en compte de type «glisser-à-réponse» et «TAP-to-déclin» de l’application téléphonique intégrée sont présentées et MonkeyCall a la possibilité de jouer une sonnerie personnalisée .

CallKit fournit des fonctionnalités supplémentaires à MonkeyCall, ce qui permet à ses appels VOIP d’interagir avec d’autres types d’appels, de s’afficher dans les listes de favoris et les listes de favoris intégrées, pour utiliser les fonctionnalités ne pas déranger et bloquer, démarrer les appels MonkeyCall à partir de Siri et offre aux utilisateurs la possibilité d’affecter des appels MonkeyCall à des personnes de l’application contacts.

Les sections suivantes décrivent en détail l’architecture CallKit, les flux d’appels entrants et sortants et l’API CallKit.

## <a name="the-callkit-architecture"></a>Architecture CallKit

Dans iOS 10, Apple a adopté CallKit dans tous les services système de telle sorte que les appels effectués sur CarPlay, par exemple, soient connus de l’interface utilisateur du système via CallKit. Dans l’exemple ci-dessous, étant donné que MonkeyCall adopte CallKit, il est connu du système de la même façon que ces services système intégrés et obtient toutes les mêmes fonctionnalités:

[![](callkit-images/callkit01.png "La pile de service CallKit")](callkit-images/callkit01.png#lightbox)

Examinez de plus près l’application MonkeyCall à partir du diagramme ci-dessus. L’application contient tout son code pour communiquer avec son propre réseau et contient ses propres interfaces utilisateur. Il lie CallKit pour communiquer avec le système:

[![](callkit-images/callkit02.png "Architecture d’application MonkeyCall")](callkit-images/callkit02.png#lightbox)

Il existe deux interfaces principales dans CallKit que l’application utilise:

- `CXProvider`-Cela permet à l’application MonkeyCall d’informer le système des notifications hors bande susceptibles de se produire.
- `CXCallController`-Autorise l’application MonkeyCall à informer le système des actions de l’utilisateur local.

### <a name="the-cxprovider"></a>CXProvider

Comme indiqué ci- `CXProvider` dessus, permet à une application d’informer le système des notifications hors bande susceptibles de se produire. Il s’agit d’une notification qui ne se produit pas en raison d’actions de l’utilisateur local, mais qui se produisent en raison d’événements externes tels que les appels entrants.

Une application doit utiliser le `CXProvider` pour les éléments suivants:

- Signaler un appel entrant au système.
- Signale que l’appel sortant s’est connecté au système.
- Signaler l’utilisateur distant qui termine l’appel au système.

Lorsque l’application souhaite communiquer avec le système, elle utilise la `CXCallUpdate` classe et, lorsque le système doit communiquer avec l’application, elle utilise la `CXAction` classe:

[![](callkit-images/callkit03.png "Communication avec le système via un CXProvider")](callkit-images/callkit03.png#lightbox)

### <a name="the-cxcallcontroller"></a>CXCallController

`CXCallController` Permet à une application d’informer le système des actions des utilisateurs locaux, tels que l’utilisateur qui démarre un appel VoIP. En implémentant `CXCallController` une application, l’application obtient l’interaction avec d’autres types d’appels dans le système. Par exemple, si un appel téléphonique actif est déjà en cours, `CXCallController` peut permettre à l’application VoIP de placer cet appel en attente et de démarrer ou de répondre à un appel VoIP.

Une application doit utiliser le `CXCallController` pour les éléments suivants:

- Signaler quand l’utilisateur a démarré un appel sortant au système.
- Signale quand l’utilisateur répond à un appel entrant au système.
- Signale quand l’utilisateur met fin à un appel au système.

Lorsque l’application souhaite communiquer des actions de l’utilisateur local au système, elle utilise `CXTransaction` la classe:

[![](callkit-images/callkit04.png "Signalement au système à l’aide d’un CXCallController")](callkit-images/callkit04.png#lightbox)

## <a name="implementing-callkit"></a>Implémentation de CallKit

Les sections suivantes montrent comment implémenter CallKit dans une application VOIP Xamarin. iOS. Par exemple, ce document utilisera le code de l’application MonkeyCall VOIP fictive. Le code présenté ici représente plusieurs classes de prise en charge, les parties spécifiques de CallKit sont traitées en détail dans les sections suivantes.

### <a name="the-activecall-class"></a>La classe ActiveCall

La `ActiveCall` classe est utilisée par l’application MonkeyCall pour contenir toutes les informations relatives à un appel VoIP actuellement actif, comme suit:

```csharp
using System;
using CoreFoundation;
using Foundation;

namespace MonkeyCall
{
    public class ActiveCall
    {
        #region Private Variables
        private bool isConnecting;
        private bool isConnected;
        private bool isOnhold;
        #endregion

        #region Computed Properties
        public NSUuid UUID { get; set; }
        public bool isOutgoing { get; set; }
        public string Handle { get; set; }
        public DateTime StartedConnectingOn { get; set;}
        public DateTime ConnectedOn { get; set;}
        public DateTime EndedOn { get; set; }

        public bool IsConnecting {
            get { return isConnecting; }
            set {
                isConnecting = value;
                if (isConnecting) StartedConnectingOn = DateTime.Now;
                RaiseStartingConnectionChanged ();
            }
        }

        public bool IsConnected {
            get { return isConnected; }
            set {
                isConnected = value;
                if (isConnected) {
                    ConnectedOn = DateTime.Now;
                } else {
                    EndedOn = DateTime.Now;
                }
                RaiseConnectedChanged ();
            }
        }

        public bool IsOnHold {
            get { return isOnhold; }
            set {
                isOnhold = value;
            }
        }
        #endregion

        #region Constructors
        public ActiveCall ()
        {
        }

        public ActiveCall (NSUuid uuid, string handle, bool outgoing)
        {
            // Initialize
            this.UUID = uuid;
            this.Handle = handle;
            this.isOutgoing = outgoing;
        }
        #endregion

        #region Public Methods
        public void StartCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call starting successfully
            completionHandler (true);

            // Simulate making a starting and completing a connection
            DispatchQueue.MainQueue.DispatchAfter (new DispatchTime(DispatchTime.Now, 3000), () => {
                // Note that the call is starting
                IsConnecting = true;

                // Simulate pause before connecting
                DispatchQueue.MainQueue.DispatchAfter (new DispatchTime (DispatchTime.Now, 1500), () => {
                    // Note that the call has connected
                    IsConnecting = false;
                    IsConnected = true;
                });
            });
        }

        public void AnswerCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call being answered
            IsConnected = true;
            completionHandler (true);
        }

        public void EndCall (ActiveCallbackDelegate completionHandler)
        {
            // Simulate the call ending
            IsConnected = false;
            completionHandler (true);
        }
        #endregion

        #region Events
        public delegate void ActiveCallbackDelegate (bool successful);
        public delegate void ActiveCallStateChangedDelegate (ActiveCall call);

        public event ActiveCallStateChangedDelegate StartingConnectionChanged;
        internal void RaiseStartingConnectionChanged ()
        {
            if (this.StartingConnectionChanged != null) this.StartingConnectionChanged (this);
        }

        public event ActiveCallStateChangedDelegate ConnectedChanged;
        internal void RaiseConnectedChanged ()
        {
            if (this.ConnectedChanged != null) this.ConnectedChanged (this);
        }
        #endregion
    }
}
```

`ActiveCall`contient plusieurs propriétés qui définissent l’état de l’appel et deux événements qui peuvent être déclenchés lorsque l’état de l’appel change. Étant donné qu’il s’agit d’un exemple uniquement, il existe trois méthodes utilisées pour simuler le démarrage, la réponse et la fin d’un appel.

### <a name="the-startcallrequest-class"></a>La classe StartCallRequest

La `StartCallRequest` classe statique fournit quelques méthodes d’assistance qui seront utilisées lors de l’utilisation des appels sortants:

```csharp
using System;
using Foundation;
using Intents;

namespace MonkeyCall
{
    public static class StartCallRequest
    {
        public static string URLScheme {
            get { return "monkeycall"; }
        }

        public static string ActivityType {
            get { return INIntentIdentifier.StartAudioCall.GetConstant ().ToString (); }
        }

        public static string CallHandleFromURL (NSUrl url)
        {
            // Is this a MonkeyCall handle?
            if (url.Scheme == URLScheme) {
                // Yes, return host
                return url.Host;
            } else {
                // Not handled
                return null;
            }
        }

        public static string CallHandleFromActivity (NSUserActivity activity)
        {
            // Is this a start call activity?
            if (activity.ActivityType == ActivityType) {
                // Yes, trap any errors
                try {
                    // Get first contact
                    var interaction = activity.GetInteraction ();
                    var startAudioCallIntent = interaction.Intent as INStartAudioCallIntent;
                    var contact = startAudioCallIntent.Contacts [0];

                    // Get the person handle
                    return contact.PersonHandle.Value;
                } catch {
                    // Error, report null
                    return null;
                }
            } else {
                // Not handled
                return null;
            }
        }
    }
}
```

Les `CallHandleFromURL` classes `CallHandleFromActivity` et sont utilisées dans AppDelegate pour recevoir le handle de contact de la personne qui est appelée dans un appel sortant. Pour plus d’informations, consultez la section [gestion des appels sortants](#handling-outgoing-calls) ci-dessous.

### <a name="the-activecallmanager-class"></a>La classe ActiveCallManager

La `ActiveCallManager` classe gère tous les appels ouverts dans l’application MonkeyCall.

```csharp
using System;
using System.Collections.Generic;
using Foundation;
using CallKit;

namespace MonkeyCall
{
    public class ActiveCallManager
    {
        #region Private Variables
        private CXCallController CallController = new CXCallController ();
        #endregion

        #region Computed Properties
        public List<ActiveCall> Calls { get; set; }
        #endregion

        #region Constructors
        public ActiveCallManager ()
        {
            // Initialize
            this.Calls = new List<ActiveCall> ();
        }
        #endregion

        #region Private Methods
        private void SendTransactionRequest (CXTransaction transaction)
        {
            // Send request to call controller
            CallController.RequestTransaction (transaction, (error) => {
                // Was there an error?
                if (error == null) {
                    // No, report success
                    Console.WriteLine ("Transaction request sent successfully.");
                } else {
                    // Yes, report error
                    Console.WriteLine ("Error requesting transaction: {0}", error);
                }
            });
        }
        #endregion

        #region Public Methods
        public ActiveCall FindCall (NSUuid uuid)
        {
            // Scan for requested call
            foreach (ActiveCall call in Calls) {
                if (call.UUID.Equals(uuid)) return call;
            }

            // Not found
            return null;
        }

        public void StartCall (string contact)
        {
            // Build call action
            var handle = new CXHandle (CXHandleType.Generic, contact);
            var startCallAction = new CXStartCallAction (new NSUuid (), handle);

            // Create transaction
            var transaction = new CXTransaction (startCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void EndCall (ActiveCall call)
        {
            // Build action
            var endCallAction = new CXEndCallAction (call.UUID);

            // Create transaction
            var transaction = new CXTransaction (endCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void PlaceCallOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, true);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }

        public void RemoveCallFromOnHold (ActiveCall call)
        {
            // Build action
            var holdCallAction = new CXSetHeldCallAction (call.UUID, false);

            // Create transaction
            var transaction = new CXTransaction (holdCallAction);

            // Inform system of call request
            SendTransactionRequest (transaction);
        }
        #endregion
    }
}
```

Là encore, étant donné qu’il s’agit uniquement `ActiveCallManager` d’une simulation, le `ActiveCall` seul gère une collection d’objets et a une routine pour rechercher `UUID` un appel donné par sa propriété. Il comprend également des méthodes pour démarrer, terminer et modifier l’état de suspension d’un appel sortant. Pour plus d’informations, consultez la section [gestion des appels sortants](#handling-outgoing-calls) ci-dessous.

### <a name="the-providerdelegate-class"></a>La classe ProviderDelegate

Comme indiqué ci-dessus `CXProvider` , un offre une communication bidirectionnelle entre l’application et le système pour les notifications hors bande. Le développeur doit fournir un personnalisé `CXProviderDelegate` et l’attacher `CXProvider` au pour que l’application gère les événements CallKit hors bande. MonkeyCall utilise les éléments `CXProviderDelegate`suivants:

```csharp
using System;
using Foundation;
using CallKit;
using UIKit;

namespace MonkeyCall
{
    public class ProviderDelegate : CXProviderDelegate
    {
        #region Computed Properties
        public ActiveCallManager CallManager { get; set;}
        public CXProviderConfiguration Configuration { get; set; }
        public CXProvider Provider { get; set; }
        #endregion

        #region Constructors
        public ProviderDelegate (ActiveCallManager callManager)
        {
            // Save connection to call manager
            CallManager = callManager;

            // Define handle types
            var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };

            // Get Image Template
            var templateImage = UIImage.FromFile ("telephone_receiver.png");

            // Setup the initial configurations
            Configuration = new CXProviderConfiguration ("MonkeyCall") {
                MaximumCallsPerCallGroup = 1,
                SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
                IconTemplateImageData = templateImage.AsPNG(),
                RingtoneSound = "musicloop01.wav"
            };

            // Create a new provider
            Provider = new CXProvider (Configuration);

            // Attach this delegate
            Provider.SetDelegate (this, null);

        }
        #endregion

        #region Override Methods
        public override void DidReset (CXProvider provider)
        {
            // Remove all calls
            CallManager.Calls.Clear ();
        }

        public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
        {
            // Create new call record
            var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

            // Monitor state changes
            activeCall.StartingConnectionChanged += (call) => {
                if (call.isConnecting) {
                    // Inform system that the call is starting
                    Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
                }
            };

            activeCall.ConnectedChanged += (call) => {
                if (call.isConnected) {
                    // Inform system that the call has connected
                    provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
                }
            };

            // Start call
            activeCall.StartCall ((successful) => {
                // Was the call able to be started?
                if (successful) {
                    // Yes, inform the system
                    action.Fulfill ();

                    // Add call to manager
                    CallManager.Calls.Add (activeCall);
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.AnswerCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Attempt to answer call
            call.EndCall ((successful) => {
                // Was the call successfully answered?
                if (successful) {
                    // Remove call from manager's queue
                    CallManager.Calls.Remove (call);

                    // Yes, inform system
                    action.Fulfill ();
                } else {
                    // No, inform system
                    action.Fail ();
                }
            });
        }

        public override void PerformSetHeldCallAction (CXProvider provider, CXSetHeldCallAction action)
        {
            // Find requested call
            var call = CallManager.FindCall (action.CallUuid);

            // Found?
            if (call == null) {
                // No, inform system and exit
                action.Fail ();
                return;
            }

            // Update hold status
            call.isOnHold = action.OnHold;

            // Inform system of success
            action.Fulfill ();
        }

        public override void TimedOutPerformingAction (CXProvider provider, CXAction action)
        {
            // Inform user that the action has timed out
        }

        public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // Start the calls audio session here
        }

        public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
        {
            // End the calls audio session and restart any non-call
            // related audio
        }
        #endregion

        #region Public Methods
        public void ReportIncomingCall (NSUuid uuid, string handle)
        {
            // Create update to describe the incoming call and caller
            var update = new CXCallUpdate ();
            update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

            // Report incoming call to system
            Provider.ReportNewIncomingCall (uuid, update, (error) => {
                // Was the call accepted
                if (error == null) {
                    // Yes, report to call manager
                    CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
                } else {
                    // Report error to user here
                    Console.WriteLine ("Error: {0}", error);
                }
            });
        }
        #endregion
    }
}
```

Lorsqu’une instance de ce délégué est créée, elle est passée `ActiveCallManager` à qui est utilisé pour gérer toute activité d’appel. Ensuite, il définit les types de handles (`CXHandleType`) `CXProvider` auxquels répondra:

```csharp
// Define handle types
var handleTypes = new [] { (NSNumber)(int)CXHandleType.PhoneNumber };
```

Elle obtient l’image de modèle qui sera appliquée à l’icône de l’application lorsqu’un appel est en cours:

```csharp
// Get Image Template
var templateImage = UIImage.FromFile ("telephone_receiver.png");
```

Ces valeurs sont regroupées dans `CXProviderConfiguration` un qui sera utilisé pour configurer les `CXProvider`éléments suivants:

```csharp
// Setup the initial configurations
Configuration = new CXProviderConfiguration ("MonkeyCall") {
    MaximumCallsPerCallGroup = 1,
    SupportedHandleTypes = new NSSet<NSNumber> (handleTypes),
    IconTemplateImageData = templateImage.AsPNG(),
    RingtoneSound = "musicloop01.wav"
};
```

Le délégué crée ensuite un nouveau `CXProvider` avec ces configurations et s’attache à lui-même:

```csharp
// Create a new provider
Provider = new CXProvider (Configuration);

// Attach this delegate
Provider.SetDelegate (this, null);
```

Lorsque vous utilisez CallKit, l’application ne crée plus et ne gère pas ses propres sessions audio. au lieu de cela, elle doit configurer et utiliser une session audio que le système créera et traitera pour elle. 

S’il s’agissait d’une application réelle `DidActivateAudioSession` , la méthode est utilisée pour démarrer l’appel avec un préconfiguré `AVAudioSession` que le système a fourni:

```csharp
public override void DidActivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // Start the call's audio session here...
}
```

Elle utilise également la `DidDeactivateAudioSession` méthode pour finaliser et libérer sa connexion à la session audio fournie par le système:

```csharp
public override void DidDeactivateAudioSession (CXProvider provider, AVFoundation.AVAudioSession audioSession)
{
    // End the calls audio session and restart any non-call
    // releated audio
}
```

Le reste du code est abordé en détail dans les sections qui suivent.

### <a name="the-appdelegate-class"></a>La classe AppDelegate

MonkeyCall utilise le AppDelegate pour contenir des instances du `ActiveCallManager` et `CXProviderDelegate` qui seront utilisées dans l’application:

```csharp
using Foundation;
using UIKit;
using Intents;
using System;

namespace MonkeyCall
{
    [Register ("AppDelegate")]
    public class AppDelegate : UIApplicationDelegate
    {
        #region Constructors
        public override UIWindow Window { get; set; }
        public ActiveCallManager CallManager { get; set; }
        public ProviderDelegate CallProviderDelegate { get; set; }
        #endregion

        #region Override Methods
        public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
        {
            // Initialize the call handlers
            CallManager = new ActiveCallManager ();
            CallProviderDelegate = new ProviderDelegate (CallManager);

            return true;
        }

        public override bool OpenUrl (UIApplication app, NSUrl url, NSDictionary options)
        {
            // Get handle from url
            var handle = StartCallRequest.CallHandleFromURL (url);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from URL: {0}", url); 
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
        {
            var handle = StartCallRequest.CallHandleFromActivity (userActivity);

            // Found?
            if (handle == null) {
                // No, report to system
                Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
                return false;
            } else {
                // Yes, start call and inform system
                CallManager.StartCall (handle);
                return true;
            }
        }

        ...
        #endregion
    }
}
```

Les `OpenUrl` méthodes `ContinueUserActivity` de substitution et sont utilisées lorsque l’application traite un appel sortant. Pour plus d’informations, consultez la section [gestion des appels sortants](#handling-outgoing-calls) ci-dessous.

## <a name="handling-incoming-calls"></a>Gestion des appels entrants

Il existe plusieurs États et processus qu’un appel VOIP entrant peut traverser pendant un flux de travail d’appel entrant classique, par exemple:

- Informant l’utilisateur (et le système) qu’un appel entrant existe.
- Réception d’une notification lorsque l’utilisateur souhaite répondre à l’appel et initialiser l’appel avec l’autre utilisateur.
- Informez le système et le réseau de communication lorsque l’utilisateur souhaite mettre fin à l’appel en cours.

Les sections suivantes décrivent en détail comment une application peut utiliser CallKit pour gérer le flux de travail des appels entrants, à nouveau à l’aide de l’application VOIP MonkeyCall comme exemple.

### <a name="informing-user-of-incoming-call"></a>Informant l’utilisateur de l’appel entrant

Lorsqu’un utilisateur distant a démarré une conversation VOIP avec l’utilisateur local, les éléments suivants se produisent:

[![](callkit-images/callkit05.png "Un utilisateur distant a démarré une conversation VOIP")](callkit-images/callkit05.png#lightbox)

1. L’application reçoit une notification de son réseau de communication indiquant qu’il y a un appel VOIP entrant.
2. L’application utilise `CXProvider` pour envoyer un `CXCallUpdate` au système, l’informant de l’appel.
3. Le système publie l’appel à l’interface utilisateur système, aux services système et à toute autre application VOIP utilisant CallKit.

Par exemple, dans le `CXProviderDelegate`:

```csharp
public void ReportIncomingCall (NSUuid uuid, string handle)
{
    // Create update to describe the incoming call and caller
    var update = new CXCallUpdate ();
    update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);

    // Report incoming call to system
    Provider.ReportNewIncomingCall (uuid, update, (error) => {
        // Was the call accepted
        if (error == null) {
            // Yes, report to call manager
            CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
        } else {
            // Report error to user here
            Console.WriteLine ("Error: {0}", error);
        }
    });
}
```

Ce code crée une nouvelle `CXCallUpdate` instance et y attache un handle qui identifie l’appelant. Ensuite, elle utilise la `ReportNewIncomingCall` méthode de la `CXProvider` classe pour informer le système de l’appel. En cas de réussite, l’appel est ajouté à la collection d’appels actifs de l’application, si ce n’est pas le cas, l’erreur doit être signalée à l’utilisateur.

### <a name="user-answering-incoming-call"></a>Réponse de l’utilisateur à l’appel entrant

Si l’utilisateur souhaite répondre à l’appel VOIP entrant, voici ce qui se produit:

[![](callkit-images/callkit06.png "L’utilisateur répond à l’appel VOIP entrant.")](callkit-images/callkit06.png#lightbox)

1. L’interface utilisateur système informe le système que l’utilisateur souhaite répondre à l’appel VOIP.
2. Le système envoie `CXAnswerCallAction` à l' `CXProvider` application l’informant de l’intention de réponse.
3. L’application informe son réseau de communication que l’utilisateur répond à l’appel et l’appel VOIP se poursuit normalement.

Par exemple, dans le `CXProviderDelegate`:

```csharp
public override void PerformAnswerCallAction (CXProvider provider, CXAnswerCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.AnswerCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Ce code recherche d’abord l’appel donné dans sa liste d’appels actifs. Si l’appel est introuvable, le système est notifié et la méthode se termine. S’il est trouvé, la `AnswerCall` méthode de la `ActiveCall` classe est appelée pour démarrer l’appel et le système est des informations si elle réussit ou échoue.

### <a name="user-ending-incoming-call"></a>Fin de l’appel entrant par l’utilisateur

Si l’utilisateur souhaite terminer l’appel à partir de l’interface utilisateur de l’application, voici ce qui se produit:

[![](callkit-images/callkit07.png "L’utilisateur met fin à l’appel à partir de l’interface utilisateur de l’application")](callkit-images/callkit07.png#lightbox)

1. L’application crée `CXEndCallAction` qui est regroupée dans `CXTransaction` un qui est envoyé au système pour l’informer que l’appel se termine.
2. Le système vérifie l’intention de l’appel de fin et `CXEndCallAction` le renvoie à l’application via `CXProvider`le.
3. L’application informe ensuite son réseau de communication que l’appel se termine.

Par exemple, dans le `CXProviderDelegate`:

```csharp
public override void PerformEndCallAction (CXProvider provider, CXEndCallAction action)
{
    // Find requested call
    var call = CallManager.FindCall (action.CallUuid);

    // Found?
    if (call == null) {
        // No, inform system and exit
        action.Fail ();
        return;
    }

    // Attempt to answer call
    call.EndCall ((successful) => {
        // Was the call successfully answered?
        if (successful) {
            // Remove call from manager's queue
            CallManager.Calls.Remove (call);

            // Yes, inform system
            action.Fulfill ();
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Ce code recherche d’abord l’appel donné dans sa liste d’appels actifs. Si l’appel est introuvable, le système est notifié et la méthode se termine. S’il est trouvé, la `EndCall` méthode de la `ActiveCall` classe est appelée pour terminer l’appel et le système est des informations si elle réussit ou échoue. En cas de réussite, l’appel est supprimé de la collection d’appels actifs.

## <a name="managing-multiple-calls"></a>Gestion de plusieurs appels

La plupart des applications VOIP peuvent gérer plusieurs appels à la fois. Par exemple, s’il existe actuellement un appel VOIP actif et que l’application reçoit une notification indiquant qu’un nouvel appel entrant est en cours, l’utilisateur peut suspendre ou raccrocher au premier appel pour répondre à la seconde.

Dans la situation qui précède, le système envoie un `CXTransaction` à l’application qui inclut une liste de plusieurs actions (telles `CXEndCallAction` que et `CXAnswerCallAction`). Toutes ces actions devront être traitées individuellement, afin que le système puisse mettre à jour l’interface utilisateur de manière appropriée.

## <a name="handling-outgoing-calls"></a>Gestion des appels sortants

Si l’utilisateur appuie sur une entrée de la liste récents (dans l’application téléphonique), par exemple, à partir d’un appel appartenant à l’application, un _appel de démarrage_ est envoyé par le système:

[![](callkit-images/callkit08.png "Réception d’une intention d’appel de démarrage")](callkit-images/callkit08.png#lightbox)

1. L’application crée une _action démarrer l’appel_ en fonction de l’intention de l’appel de démarrage reçue du système. 
2. L’application utilisera `CXCallController` pour demander l’action de démarrage à partir du système.
3. Si le système accepte l’action, elle est retournée à l’application via le `XCProvider` délégué.
4. L’application démarre l’appel sortant avec son réseau de communication.

Pour plus d’informations sur les intentions, consultez notre documentation sur [les intentions et les extensions d’interface utilisateur](~/ios/platform/sirikit/understanding-sirikit.md) des intentions. 

### <a name="the-outgoing-call-lifecycle"></a>Cycle de vie des appels sortants

Lorsque vous utilisez CallKit et un appel sortant, l’application doit informer le système des événements de cycle de vie suivants:

1. **Démarrage** : informe le système qu’un appel sortant est sur le le début.
2. **Démarré** : informe le système qu’un appel sortant a démarré.
3. **Connexion** : informer le système que l’appel sortant se connecte.
4. **Connecté** : informe l’appel sortant s’est connecté et que les deux parties peuvent parler maintenant.

Par exemple, le code suivant démarre un appel sortant:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void StartCall (string contact)
{
    // Build call action
    var handle = new CXHandle (CXHandleType.Generic, contact);
    var startCallAction = new CXStartCallAction (new NSUuid (), handle);

    // Create transaction
    var transaction = new CXTransaction (startCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Il crée un `CXHandle` et l’utilise pour configurer un `CXStartCallAction` qui est regroupé dans `CXTransaction` un qui est envoyé au système à l' `RequestTransaction` aide de la `CXCallController` méthode de la classe. En appelant la `RequestTransaction` méthode, le système peut placer tout appel existant en attente, quelle que soit la source (application téléphonique, FaceTime, VoIP, etc.) avant le début du nouvel appel.

La demande de démarrage d’un appel VOIP sortant peut provenir de différentes sources, telles que Siri, d’une entrée sur une carte de visite (dans l’application contacts) ou de la liste des récents (dans l’application téléphonique). Dans ce cas, l’application reçoit un appel de démarrage à l’intérieur d' `NSUserActivity` un et le AppDelegate doit la gérer:

```csharp
public override bool ContinueUserActivity (UIApplication application, NSUserActivity userActivity, UIApplicationRestorationHandler completionHandler)
{
    var handle = StartCallRequest.CallHandleFromActivity (userActivity);

    // Found?
    if (handle == null) {
        // No, report to system
        Console.WriteLine ("Unable to get call handle from User Activity: {0}", userActivity);
        return false;
    } else {
        // Yes, start call and inform system
        CallManager.StartCall (handle);
        return true;
    }
}
```

Ici, `CallHandleFromActivity` la méthode de la classe `StartCallRequest` d’assistance est utilisée pour obtenir le handle de la personne appelée (voir [la classe StartCallRequest](#the-startcallrequest-class) ci-dessus).

La `PerformStartCallAction` méthode de la [classe ProviderDelegate](#the-providerdelegate-class) est utilisée pour terminer l’appel sortant réel et informer le système de son cycle de vie:

```csharp
public override void PerformStartCallAction (CXProvider provider, CXStartCallAction action)
{
    // Create new call record
    var activeCall = new ActiveCall (action.CallUuid, action.CallHandle.Value, true);

    // Monitor state changes
    activeCall.StartingConnectionChanged += (call) => {
        if (call.IsConnecting) {
            // Inform system that the call is starting
            Provider.ReportConnectingOutgoingCall (call.UUID, call.StartedConnectingOn.ToNSDate());
        }
    };

    activeCall.ConnectedChanged += (call) => {
        if (call.IsConnected) {
            // Inform system that the call has connected
            Provider.ReportConnectedOutgoingCall (call.UUID, call.ConnectedOn.ToNSDate ());
        }
    };

    // Start call
    activeCall.StartCall ((successful) => {
        // Was the call able to be started?
        if (successful) {
            // Yes, inform the system
            action.Fulfill ();

            // Add call to manager
            CallManager.Calls.Add (activeCall);
        } else {
            // No, inform system
            action.Fail ();
        }
    });
}
```

Elle crée une instance de la `ActiveCall` classe (pour stocker des informations sur l’appel en cours) et se remplit avec la personne appelée. Les `StartingConnectionChanged` événements `ConnectedChanged` et sont utilisés pour surveiller et signaler le cycle de vie des appels sortants. L’appel est lancé et le système est informé que l’action a été exécutée.

### <a name="ending-an-outgoing-call"></a>Fin d’un appel sortant

Lorsque l’utilisateur a terminé un appel sortant et souhaite l’arrêter, le code suivant peut être utilisé:

```csharp
private CXCallController CallController = new CXCallController ();
...

private void SendTransactionRequest (CXTransaction transaction)
{
    // Send request to call controller
    CallController.RequestTransaction (transaction, (error) => {
        // Was there an error?
        if (error == null) {
            // No, report success
            Console.WriteLine ("Transaction request sent successfully.");
        } else {
            // Yes, report error
            Console.WriteLine ("Error requesting transaction: {0}", error);
        }
    });
}

public void EndCall (ActiveCall call)
{
    // Build action
    var endCallAction = new CXEndCallAction (call.UUID);

    // Create transaction
    var transaction = new CXTransaction (endCallAction);

    // Inform system of call request
    SendTransactionRequest (transaction);
}
```

Si crée un `CXEndCallAction` avec l’UUID de l’appel à end, le regroupe dans un `CXTransaction` qui est envoyé au système à l’aide `RequestTransaction` de la méthode `CXCallController` de la classe. 

## <a name="additional-callkit-details"></a>Détails supplémentaires sur CallKit

Cette section aborde quelques détails supplémentaires que le développeur devra prendre en compte lors de l’utilisation de CallKit, par exemple:

- Configuration du fournisseur
- Erreurs d’action
- Restrictions système
- Audio VOIP

### <a name="provider-configuration"></a>Configuration du fournisseur

La configuration du fournisseur permet à une application VOIP iOS 10 de personnaliser l’expérience utilisateur (à l’intérieur de l’interface utilisateur d’appel native) lors de l’utilisation de CallKit.

Une application peut effectuer les types de personnalisations suivants:

- Affichez un nom localisé.
- Activer la prise en charge de l’appel vidéo.
- Personnalisez les boutons sur l’interface utilisateur d’appel en présentant sa propre icône d’image de modèle. L’interaction de l’utilisateur avec des boutons personnalisés est envoyée directement à l’application à traiter. 

### <a name="action-errors"></a>Erreurs d’action

les applications VOIP iOS 10 utilisant CallKit doivent gérer les actions qui échouent correctement et garder l’utilisateur informé de l’état d’action à tout moment. 

Prenez en considération l’exemple suivant:

1. L’application a reçu une action de démarrage et a commencé le processus d’initialisation d’un nouvel appel VOIP avec son réseau de communication.
2. En raison de la capacité de communication réseau limitée ou inexistante, cette connexion échoue.
3. L’application *doit* renvoyer le message d' **échec** à l’action de démarrage (`Action.Fail()`) pour informer le système de la défaillance.
4. Cela permet au système d’informer l’utilisateur de l’état de l’appel. Par exemple, pour afficher l’interface utilisateur de l’échec de l’appel.

En outre, une application VOIP iOS 10 doit répondre aux erreurs de _délai d’attente_ qui peuvent se produire lorsqu’une action attendue ne peut pas être traitée dans un laps de temps donné. Chaque type d’action fourni par CallKit a une valeur de délai d’attente maximale qui lui est associée. Ces valeurs de délai d’attente garantissent que toute action CallKit demandée par l’utilisateur est gérée de manière réactive, ce qui permet également de conserver les fluides du système d’exploitation et de les répartir.

Il existe plusieurs méthodes sur le délégué de fournisseur`CXProviderDelegate`() qui doivent être remplacées pour gérer correctement ces situations de délai d’attente.

### <a name="system-restrictions"></a>Restrictions système

En fonction de l’état actuel de l’appareil iOS exécutant l’application VOIP iOS 10, certaines restrictions système peuvent être appliquées.

Par exemple, un appel VOIP entrant peut être limité par le système dans les cas suivants:

1. La personne qui appelle est sur la liste des appelants bloqués de l’utilisateur.
2. L’appareil iOS de l’utilisateur est en mode do-not-bafaire.

Si un appel VOIP est limité par l’une de ces situations, utilisez le code suivant pour le gérer:

```csharp
public class ProviderDelegate : CXProviderDelegate
{
...

    public void ReportIncomingCall (NSUuid uuid, string handle)
    {
        // Create update to describe the incoming call and caller
        var update = new CXCallUpdate ();
        update.RemoteHandle = new CXHandle (CXHandleType.Generic, handle);
    
        // Report incoming call to system
        Provider.ReportNewIncomingCall (uuid, update, (error) => {
            // Was the call accepted
            if (error == null) {
                // Yes, report to call manager
                CallManager.Calls.Add (new ActiveCall (uuid, handle, false));
            } else {
                // Report error to user here
                if (error.Code == (int)CXErrorCodeIncomingCallError.CallUuidAlreadyExists) {
                    // Handle duplicate call ID
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByBlockList) {
                    // Handle call from blocked user
                } else if (error.Code == (int)CXErrorCodeIncomingCallError.FilteredByDoNotDisturb) {
                    // Handle call while in do-not-disturb mode
                } else {
                    // Handle unknown error
                }
            }
        });
    }

}
```

### <a name="voip-audio"></a>Audio VOIP

CallKit offre plusieurs avantages pour gérer les ressources audio qu’une application VOIP iOS 10 nécessitera lors d’un appel VOIP en direct. L’un des principaux avantages est que la session audio de l’application aura des priorités élevées lors de l’exécution dans iOS 10. Il s’agit du même niveau de priorité que les applications intégrées Phone et FaceTime, et ce niveau de priorité amélioré empêchera les autres applications en cours d’exécution d’interrompre la session audio de l’application VOIP.

En outre, CallKit a accès à d’autres indicateurs de routage audio qui peuvent améliorer les performances et acheminer intelligemment les données audio VOIP vers des périphériques de sortie spécifiques pendant un appel en direct en fonction des préférences de l’utilisateur et des États de l’appareil. Par exemple, sur la base d’appareils connectés tels que le casque Bluetooth, une connexion CarPlay en direct ou des paramètres d’accessibilité.

Pendant le cycle de vie d’un appel VOIP classique à l’aide de CallKit, l’application doit configurer le flux audio que CallKit fournira. Jetez un coup d’œil à l’exemple suivant:

[![](callkit-images/callkit09.png "Séquence d’action de démarrage de l’appel")](callkit-images/callkit09.png#lightbox)

1. Une action de début d’appel est reçue par l’application pour répondre à un appel entrant.
2. Avant que cette action soit exécutée par l’application, elle fournit la configuration requise pour son `AVAudioSession`.
3. L’application informe le système que l’action a été exécutée.
4. Avant que l’appel ne se connecte, CallKit fournit `AVAudioSession` une haute priorité qui correspond à la configuration demandée par l’application. L’application sera notifiée via la `DidActivateAudioSession` méthode de son `CXProviderDelegate`.

## <a name="working-with-call-directory-extensions"></a>Utilisation des extensions d’annuaire d’appels

Lors de l’utilisation de CallKit, les _extensions d’annuaire d’appels_ permettent d’ajouter des numéros d’appel bloqués et d’identifier des numéros spécifiques à une application VoIP donnée à des contacts dans l’application de contact sur l’appareil iOS.

### <a name="implementing-a-call-directory-extension"></a>Implémentation d’une extension de répertoire d’appels

Pour implémenter une extension de répertoire d’appels dans une application Xamarin. iOS, procédez comme suit:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet**.
3. Sélectionnez > **Extensions** iOS extensions de répertoire appel, puis cliquez sur le bouton suivant: >  

    [![](callkit-images/calldir01.png "Création d’une extension de répertoire d’appels")](callkit-images/calldir01.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le bouton **suivant** : 

    [![](callkit-images/calldir02.png "Saisie d’un nom pour l’extension")](callkit-images/calldir02.png#lightbox)
5. Ajustez le **nom du projet** et/ou le nom de la **solution** , si nécessaire, puis cliquez sur le bouton **créer** : 

    [![](callkit-images/calldir03.png "Création du projet")](callkit-images/calldir03.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrez la solution de l’application dans Visual Studio.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter** > **Ajouter un nouveau projet**.
3. Sélectionnez > **Extensions** iOS extensions de répertoire appel, puis cliquez sur le bouton suivant: >  

    [![](callkit-images/calldir01w.png "Création d’une extension de répertoire d’appels")](callkit-images/calldir01.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le bouton **OK**

-----

Cette opération ajoute une `CallDirectoryHandler.cs` classe au projet qui ressemble à ce qui suit:

```csharp
using System;

using Foundation;
using CallKit;

namespace MonkeyCallDirExtension
{
    [Register ("CallDirectoryHandler")]
    public class CallDirectoryHandler : CXCallDirectoryProvider, ICXCallDirectoryExtensionContextDelegate
    {
        #region Constructors
        protected CallDirectoryHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void BeginRequest (CXCallDirectoryExtensionContext context)
        {
            context.Delegate = this;

            if (!AddBlockingPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add blocking phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 1, null);
                context.CancelRequest (error);
                return;
            }

            if (!AddIdentificationPhoneNumbers (context)) {
                Console.WriteLine ("Unable to add identification phone numbers");
                var error = new NSError (new NSString ("CallDirectoryHandler"), 2, null);
                context.CancelRequest (error);
                return;
            }

            context.CompleteRequest (null);
        }
        #endregion

        #region Private Methods
        private bool AddBlockingPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to block from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 14085555555, 18005555555 };

            foreach (var phoneNumber in phoneNumbers)
                context.AddBlockingEntry (phoneNumber);

            return true;
        }

        private bool AddIdentificationPhoneNumbers (CXCallDirectoryExtensionContext context)
        {
            // Retrieve phone numbers to identify and their identification labels from data store. For optimal performance and memory usage when there are many phone numbers,
            // consider only loading a subset of numbers at a given time and using autorelease pool(s) to release objects allocated during each batch of numbers which are loaded.
            //
            // Numbers must be provided in numerically ascending order.

            long [] phoneNumbers = { 18775555555, 18885555555 };
            string [] labels = { "Telemarketer", "Local business" };

            for (var i = 0; i < phoneNumbers.Length; i++) {
                long phoneNumber = phoneNumbers [i];
                string label = labels [i];
                context.AddIdentificationEntry (phoneNumber, label);
            }

            return true;
        }
        #endregion

        #region Public Methods
        public void RequestFailed (CXCallDirectoryExtensionContext extensionContext, NSError error)
        {
            // An error occurred while adding blocking or identification entries, check the NSError for details.
            // For Call Directory error codes, see the CXErrorCodeCallDirectoryManagerError enum.
            //
            // This may be used to store the error details in a location accessible by the extension's containing app, so that the
            // app may be notified about errors which occurred while loading data even if the request to load data was initiated by
            // the user in Settings instead of via the app itself.
        }
        #endregion
    }
}
```

La `BeginRequest` méthode dans le gestionnaire de répertoire des appels doit être modifiée pour fournir les fonctionnalités requises. Dans le cas de l’exemple ci-dessus, il tente de définir la liste des nombres bloqués et disponibles dans la base de données de contacts de l’application VOIP. Si l’une des requêtes échoue pour une raison quelconque `NSError` , créez un pour décrire l’échec et `CancelRequest` lui passer la `CXCallDirectoryExtensionContext` méthode de la classe.

Pour définir les numéros bloqués, `AddBlockingEntry` utilisez la méthode `CXCallDirectoryExtensionContext` de la classe. Les nombres fournis à la méthode _doivent_ être dans l’ordre numérique croissant. Pour optimiser les performances et l’utilisation de la mémoire lorsqu’il existe de nombreux numéros de téléphone, envisagez de charger uniquement un sous-ensemble de nombres à un moment donné et d’utiliser un ou plusieurs pools de mise en service pour libérer des objets alloués au cours de chaque lot de numéros chargés.

Pour informer l’application de contact des numéros de contact connus de l’application VoIP, utilisez `AddIdentificationEntry` la méthode de `CXCallDirectoryExtensionContext` la classe et fournissez le nombre et une étiquette d’identification. Là encore, les nombres fournis à la méthode _doivent_ être dans l’ordre numérique croissant. Pour optimiser les performances et l’utilisation de la mémoire lorsqu’il existe de nombreux numéros de téléphone, envisagez de charger uniquement un sous-ensemble de nombres à un moment donné et d’utiliser un ou plusieurs pools de mise en service pour libérer des objets alloués au cours de chaque lot de numéros chargés.

## <a name="summary"></a>Récapitulatif

Cet article a abordé la nouvelle API CallKit qu’Apple a publiée dans iOS 10 et comment l’implémenter dans les applications VOIP Xamarin. iOS. Il a montré comment CallKit permet à une application de s’intégrer au système iOS, comment elle fournit la parité des fonctionnalités avec les applications intégrées (comme le téléphone) et comment elle augmente la visibilité d’une application dans iOS dans des emplacements tels que les écrans de verrouillage et d’habitation, via des interactions Siri et via les applications contacts.

## <a name="related-links"></a>Liens connexes

- [Exemples iOS 10](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS10)
