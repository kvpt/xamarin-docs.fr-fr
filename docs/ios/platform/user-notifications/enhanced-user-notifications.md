---
title: Notifications utilisateur améliorées dans Xamarin. iOS
description: Cet article décrit l’infrastructure de notifications utilisateur introduite dans iOS 10. Il aborde les notifications locales, les notifications à distance, la gestion des notifications, les actions de notification et bien plus encore.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/02/2017
ms.openlocfilehash: 8a18bfe3a72334eab3304492da63e6ce8f889a72
ms.sourcegitcommit: 3edcc63fcf86409b73cd6e5dc77f0093a99b3f87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/11/2021
ms.locfileid: "98062613"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Notifications utilisateur améliorées dans Xamarin. iOS

Nouveauté d’iOS 10, l’infrastructure de notification utilisateur permet la remise et la gestion des notifications locales et distantes. À l’aide de ce Framework, une application ou une extension d’application peut planifier la remise de notifications locales en spécifiant un ensemble de conditions, telles que l’emplacement ou l’heure de la journée.

## <a name="about-user-notifications"></a>À propos des notifications utilisateur

Comme indiqué ci-dessus, la nouvelle infrastructure de notification utilisateur autorise la livraison et la gestion des notifications locales et distantes. À l’aide de ce Framework, une application ou une extension d’application peut planifier la remise de notifications locales en spécifiant un ensemble de conditions, telles que l’emplacement ou l’heure de la journée.

En outre, l’application ou l’extension peut recevoir (et éventuellement modifier) des notifications locales et distantes quand elles sont remises à l’appareil iOS de l’utilisateur.

La nouvelle infrastructure d’interface utilisateur de notification utilisateur permet à une application ou à une extension d’application de personnaliser l’apparence des notifications locales et distantes quand elles sont présentées à l’utilisateur.

Cette infrastructure offre les moyens suivants pour une application de remettre des notifications à un utilisateur :

- **Alertes visuelles** : la notification est remontée à partir du haut de l’écran sous la forme d’une bannière.
- **Sons et vibrations** -peuvent être associés à une notification.
- **Icône d’application badge** -où l’icône de l’application affiche un badge indiquant que le nouveau contenu est disponible, comme le nombre de messages électroniques non lus.

En outre, selon le contexte actuel de l’utilisateur, il existe différentes façons de présenter une notification :

- Si l’appareil est déverrouillé, la notification est reportée à partir du haut de l’écran sous la forme d’une bannière.
- Si l’appareil est verrouillé, la notification s’affiche sur l’écran de verrouillage de l’utilisateur.
- Si l’utilisateur a manqué une notification, il peut ouvrir le centre de notification et afficher toutes les notifications disponibles en attente.

Une application Xamarin. iOS a deux types de notifications utilisateur qu’elle peut envoyer :

- **Notifications locales** : celles-ci sont envoyées par les applications installées localement sur l’appareil de l’utilisateur.
- Les **notifications distantes** sont envoyées à partir d’un serveur distant et présentées à l’utilisateur, ou elles déclenchent une mise à jour en arrière-plan du contenu de l’application.

### <a name="about-local-notifications"></a>À propos des notifications locales

Les notifications locales qu’une application iOS peut envoyer ont les fonctionnalités et attributs suivants :

- Elles sont envoyées par les applications qui sont locales sur l’appareil de l’utilisateur. 
- Ils peuvent être configurés pour utiliser des déclencheurs de type heure ou emplacement. 
- L’application planifie la notification avec l’appareil de l’utilisateur et elle s’affiche lorsque la condition de déclenchement est remplie.
- Lorsque l’utilisateur interagit avec une notification, l’application reçoit un rappel.

Voici quelques exemples de notifications locales :

- Alertes de calendrier
- Alertes de rappel
- Déclencheurs prenant en charge l’emplacement

Pour plus d’informations, consultez la documentation relative au [Guide de programmation des notifications locales et distantes](https://developer.apple.com/documentation/usernotifications) d’Apple.

### <a name="about-remote-notifications"></a>À propos des notifications distantes

Les notifications distantes qu’une application iOS peut envoyer ont les fonctionnalités et attributs suivants :

- L’application a un composant côté serveur avec lequel il communique.
- Le Apple Push Notification Service (APNs) est utilisé pour transmettre à l’appareil de l’utilisateur des notifications à distance au meilleur effort à partir des serveurs basés sur le Cloud du développeur.
- Lorsque l’application reçoit la notification distante, elle est affichée à l’utilisateur.
- Lorsque l’utilisateur interagit avec la notification, l’application reçoit un rappel.

Voici quelques exemples de notifications distantes :

- Alertes d’actualité
- Mises à jour sportives
- Messages de messagerie instantanée

Deux types de notifications distantes sont disponibles pour une application iOS :

- **Accès** de l’utilisateur : ceux-ci sont affichés à l’utilisateur sur l’appareil.
- **Mises à jour silencieuses** : celles-ci fournissent un mécanisme permettant de mettre à jour le contenu d’une application IOS en arrière-plan. Lorsqu’une mise à jour silencieuse est reçue, l’application peut accéder aux serveurs distants pour extraire le contenu le plus récent.

Pour plus d’informations, consultez la documentation relative au [Guide de programmation des notifications locales et distantes](https://developer.apple.com/documentation/usernotifications) d’Apple.

### <a name="about-the-existing-notifications-api"></a>À propos de l’API notifications existantes

Avant iOS 10, une application iOS utiliserait `UIApplication` pour inscrire une notification auprès du système et planifier la manière dont cette notification doit être déclenchée (par heure ou par emplacement).

Un développeur peut rencontrer plusieurs problèmes lors de l’utilisation de l’API de notification existante :

- Différents rappels sont requis pour les notifications locales ou distantes, ce qui peut entraîner une duplication de code.
- L’application a un contrôle limité de la notification une fois qu’elle a été planifiée avec le système.
- Il existait des niveaux de prise en charge différents sur l’ensemble des plateformes existantes d’Apple.

### <a name="about-the-new-user-notification-framework"></a>À propos de la nouvelle infrastructure de notification utilisateur

Avec iOS 10, Apple a introduit la nouvelle infrastructure de notification utilisateur, qui remplace la `UIApplication` méthode existante mentionnée ci-dessus.

L’infrastructure de notification utilisateur fournit les éléments suivants :

- Une API familière qui comprend la parité des fonctionnalités avec les méthodes précédentes, ce qui facilite le portage du code à partir de l’infrastructure existante.
- Comprend un ensemble étendu d’options de contenu qui permet à l’utilisateur d’envoyer des notifications plus riches.
- Les notifications locales et distantes peuvent être gérées par le même code et les mêmes rappels.
- Simplifie le processus de gestion des rappels envoyés à une application lorsque l’utilisateur interagit avec une notification.
- Gestion améliorée des notifications en attente et remises, y compris la possibilité de supprimer ou de mettre à jour les notifications.
- Ajoute la possibilité d’effectuer une présentation dans l’application des notifications.
- Ajoute la possibilité de planifier et de gérer les notifications à partir des extensions d’application.
- Ajoute un nouveau point d’extension pour les notifications elles-mêmes. 

La nouvelle infrastructure de notification utilisateur fournit une API de notification unifiée sur le multiple des plateformes prises en charge par Apple, notamment : 

- **iOS** : prise en charge complète de la gestion et de la planification des notifications.
- **tvOS** : ajoute la possibilité de Badger les icônes d’application pour les notifications locales et distantes.
- **Watchos** : ajoute la possibilité de transférer les notifications de l’appareil iOS couplé de l’utilisateur à leur Apple Watch et donne aux applications de surveillance la possibilité d’effectuer des notifications locales directement sur la montre.
- **MacOS** -prise en charge complète pour gérer et planifier les notifications.

Pour plus d’informations, consultez la documentation de référence de l' [infrastructure UserNotifications](https://developer.apple.com/reference/usernotifications) d’Apple et la documentation [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) .

## <a name="preparing-for-notification-delivery"></a>Préparation de la remise des notifications

Avant qu’une application iOS puisse envoyer des notifications à l’utilisateur, l’application doit être inscrite auprès du système et, étant donné qu’une notification est une interruption de l’utilisateur, une application doit demander explicitement une autorisation avant de l’envoyer.

Il existe trois niveaux différents de demandes de notification que l’utilisateur peut approuver pour une application :

- Bannière s’affiche.
- Alertes sonores.
- Badge l’icône de l’application.

En outre, ces niveaux d’approbation doivent être demandés et définis pour les notifications locales et distantes.

L’autorisation de notification doit être demandée dès le lancement de l’application en ajoutant le code suivant à la `FinishedLaunching` méthode de `AppDelegate` et en définissant le type de notification souhaité ( `UNAuthorizationOptions` ) :

> [!NOTE]
> `UNUserNotificationCenter` n’est disponible qu’à partir d’iOS 10 +. Par conséquent, il est recommandé de vérifier la version macOS avant d’envoyer la demande. 

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Version check
    if (UIDevice.CurrentDevice.CheckSystemVersion (10, 0)) {
        // Request notification permissions from the user
        UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
            // Handle approval
        });
    }

    return true;
}
```

Étant donné que cette API est unifiée et fonctionne également sur Mac 10.14 +, si vous ciblez macOS, vous devez également vérifier l’autorisation de notification dès que possible :

```csharp
using UserNotifications;
...

public override void DidFinishLaunching (NSNotification notification)
{
    // Check we're at least v10.14
    if (NSProcessInfo.ProcessInfo.IsOperatingSystemAtLeastVersion (new NSOperatingSystemVersion (10, 14, 0))) {
        // Request notification permissions from the user
        UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert | UNAuthorizationOptions.Badge | UNAuthorizationOptions.Sound, (approved, err) => {
            // Handle approval
        });
    }
}

> [!NOTE]
> With MacOS apps, for the permission dialog to appear, you must sign your macOS app, even if building locally in DEBUG mode. Therefore, **Project->Options->Mac Signing->Sign the application bundle** must be checked.

Additionally, a user can always change the notification privileges for an app at any time using the **Settings** app on the device. The app should check for the user's requested notification privileges before presenting a notification using the following code:

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configuration de l’environnement de notifications distantes

Nouveauté d’iOS 10, le développeur doit informer le système d’exploitation de l’environnement d’exécution des notifications push dans l’environnement de développement ou de production. Si vous ne fournissez pas ces informations, l’application est rejetée lorsqu’elle est envoyée à l’App Store iTunes avec une notification semblable à la suivante :

> Droit manquant pour les notifications Push : votre application comprend une API pour le service de notification push d’Apple, mais le `aps-environment` droit est manquant dans la signature de l’application.

Pour fournir le droit requis, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Entitlements.plist` fichier dans le **panneau solutions** pour l’ouvrir et le modifier.
2. Basculez vers la vue **source** : 

    [![Vue source](enhanced-user-notifications-images/setup01.png)](enhanced-user-notifications-images/setup01.png#lightbox)
3. Cliquez sur le **+** bouton pour ajouter une nouvelle clé.
4. Entrez `aps-environment` pour la **propriété**, laissez le **type** en tant que `String` et entrez `development` ou `production` pour la **valeur**: 

    [![Propriété APS-Environment](enhanced-user-notifications-images/setup02.png)](enhanced-user-notifications-images/setup02.png#lightbox)
5. Enregistrez les modifications du fichier.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Entitlements.plist` fichier dans le **Explorateur de solutions** pour l’ouvrir et le modifier.
2. Cliquez sur le **+** bouton pour ajouter une nouvelle clé.
3. Entrez `aps-environment` pour la **propriété**, laissez le **type** en tant que `String` et entrez `development` ou `production` pour la **valeur**: 

    [![Propriété APS-Environment](enhanced-user-notifications-images/setup02w.png)](enhanced-user-notifications-images/setup02.png#lightbox)
4. Enregistrez les modifications du fichier.

-----

### <a name="registering-for-remote-notifications"></a>Inscription pour les notifications distantes

Si l’application envoie et reçoit des notifications distantes, elle doit toujours effectuer une _inscription de jeton_ à l’aide de l’API existante `UIApplication` . Cette inscription requiert que l’appareil ait un APNs d’accès à la connexion réseau active, ce qui génère le jeton nécessaire qui sera envoyé à l’application. L’application doit ensuite transférer ce jeton à l’application côté serveur du développeur pour s’inscrire aux notifications distantes :

[![Présentation de l’inscription des jetons](enhanced-user-notifications-images/token01.png)](enhanced-user-notifications-images/token01.png#lightbox)

Utilisez le code suivant pour initialiser l’inscription requise :

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

Le jeton qui est envoyé à l’application côté serveur du développeur doit être inclus dans la charge utile de notification qui est envoyée du serveur à APNs lors de l’envoi d’une notification distante :

[![Jeton inclus dans la charge utile de notification](enhanced-user-notifications-images/token02.png)](enhanced-user-notifications-images/token02.png#lightbox)

Le jeton joue le rôle de clé qui relie la notification et l’application utilisée pour ouvrir ou répondre à la notification.

Pour plus d’informations, consultez la documentation relative au [Guide de programmation des notifications locales et distantes](https://developer.apple.com/documentation/usernotifications) d’Apple.

## <a name="notification-delivery"></a>Remise de notifications

Une fois l’application inscrite entièrement et les autorisations requises demandées et accordées par l’utilisateur, l’application est maintenant prête à envoyer et à recevoir des notifications. 

### <a name="providing-notification-content"></a>Fourniture de contenu de notification

Nouveauté d’iOS 10, toutes les notifications contiennent à la fois un **titre** et un **sous-titre** qui seront toujours affichés avec le **corps** du contenu de la notification. En outre, il est également possible d’ajouter des **pièces jointes** au contenu de notification.

Pour créer le contenu d’une notification locale, utilisez le code suivant :

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Pour les notifications distantes, le processus est similaire :

```csharp
{
    "aps":{
        "alert":{
            "title":"Notification Title",
            "subtitle":"Notification Subtitle",
            "body":"This is the message body of the notification."
        },
        "badge":1
    }
}
```

### <a name="scheduling-when-a-notification-is-sent"></a>Planification de l’envoi d’une notification

Une fois le contenu de la notification créé, l’application doit planifier le moment auquel la notification est présentée à l’utilisateur en définissant un *déclencheur*. iOS 10 propose quatre types de déclencheurs :

- **Notification push** : est utilisé exclusivement avec les notifications distantes et est déclenché quand APNs envoie un package de notification à l’application en cours d’exécution sur l’appareil.
- **Intervalle de temps** -permet de planifier une notification locale à partir d’un intervalle de temps commençant à maintenant et en terminant un point ultérieur. Par exemple : `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`
- **Date du calendrier** -permet de planifier des notifications locales pour une date et une heure spécifiques.
- **Basé sur l’emplacement** : permet de planifier les notifications locales lorsque l’appareil iOS entre en déplacement ou en quittant un emplacement géographique spécifique, ou se trouve dans une proximité donnée à n’importe quelle balise Bluetooth.

Lorsqu’une notification locale est prête, l’application doit appeler la `Add` méthode de l' `UNUserNotificationCenter` objet pour planifier son affichage à l’utilisateur. Pour les notifications distantes, l’application côté serveur envoie une charge utile de notification à APNs, qui envoie ensuite le paquet à l’appareil de l’utilisateur.

En regroupant tous les éléments, un exemple de notification locale peut se présenter comme suit :

```csharp
using UserNotifications;
...

var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);

var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

## <a name="handling-foreground-app-notifications"></a>Gestion des notifications d’application de premier plan

Nouveauté d’iOS 10, une application peut gérer les notifications différemment lorsqu’elle se trouve au premier plan et qu’une notification est déclenchée. En fournissant un `UNUserNotificationCenterDelegate` et en implémentant la `WillPresentNotification` méthode, l’application peut assumer la responsabilité de l’affichage de la notification. Par exemple :

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        #region Constructors
        public UserNotificationCenterDelegate ()
        {
        }
        #endregion

        #region Override Methods
        public override void WillPresentNotification (UNUserNotificationCenter center, UNNotification notification, Action<UNNotificationPresentationOptions> completionHandler)
        {
            // Do something with the notification
            Console.WriteLine ("Active Notification: {0}", notification);

            // Tell system to display the notification anyway or use
            // `None` to say we have handled the display locally.
            completionHandler (UNNotificationPresentationOptions.Alert);
        }
        #endregion
    }
}
```

Ce code écrit simplement le contenu de `UNNotification` dans la sortie de l’application et demande au système d’afficher l’alerte standard pour la notification. 

Si l’application souhaite afficher la notification elle-même lorsqu’elle se trouvait au premier plan et n’utilise pas les valeurs système par défaut, transmettez `None` au gestionnaire d’achèvement. Exemple :

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Une fois ce code en place, ouvrez le `AppDelegate.cs` fichier pour le modifier et modifiez la `FinishedLaunching` méthode pour qu’elle ressemble à ce qui suit :

```csharp
public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    // Watch for notifications while the app is active
    UNUserNotificationCenter.Current.Delegate = new UserNotificationCenterDelegate ();

    return true;
}
```

Ce code associe le personnalisé `UNUserNotificationCenterDelegate` ci-dessus au actuel `UNUserNotificationCenter` , de sorte que l’application peut gérer la notification pendant qu’elle est active et au premier plan.

## <a name="notification-management"></a>Gestion des notifications

Nouveauté d’iOS 10, la gestion des notifications permet d’accéder aux notifications en attente et remises, et ajoute la possibilité de supprimer, mettre à jour ou promouvoir ces notifications.

Une partie importante de la gestion des notifications est l' _identificateur de demande_ qui a été affecté à la notification lorsqu’elle a été créée et planifiée avec le système. Pour les notifications distantes, elles sont affectées via le nouveau `apps-collapse-id` champ dans l’en-tête de requête http.

L’identificateur de demande permet de sélectionner la notification sur laquelle l’application souhaite effectuer la gestion des notifications.

### <a name="removing-notifications"></a>Suppression de notifications

Pour supprimer une notification en attente du système, utilisez le code suivant :

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Pour supprimer une notification déjà livrée, utilisez le code suivant :

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>Mise à jour d’une notification existante

Pour mettre à jour une notification existante, il vous suffit de créer une nouvelle notification avec les paramètres souhaités modifiés (par exemple, une nouvelle heure de déclenchement) et de l’ajouter au système avec le même identificateur de demande que la notification qui doit être modifiée. Exemple :

```csharp
using UserNotifications;
...

// Rebuild notification
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;

// New trigger time
var trigger = UNTimeIntervalNotificationTrigger.CreateTrigger (10, false);

// ID of Notification to be updated
var requestID = "sampleRequest";
var request = UNNotificationRequest.FromIdentifier (requestID, content, trigger);

// Add to system to modify existing Notification
UNUserNotificationCenter.Current.AddNotificationRequest (request, (err) => {
    if (err != null) {
        // Do something with error...
    }
});
```

Pour les notifications déjà remises, la notification existante sera mise à jour et promue en haut de la liste sur les écrans d’hébergement et de verrouillage, et dans le centre de notifications si elle a déjà été lue par l’utilisateur.

## <a name="working-with-notification-actions"></a>Utilisation des actions de notification

Dans iOS 10, les notifications envoyées à l’utilisateur ne sont pas statiques et offrent plusieurs moyens d’interagir avec eux (des actions intégrées aux actions personnalisées).

Une application iOS peut répondre à trois types d’actions :

- **Action par défaut** : lorsque l’utilisateur clique sur une notification pour ouvrir l’application et afficher les détails de la notification donnée.
- **Actions personnalisées** : celles-ci ont été ajoutées dans iOS 8 et permettent à l’utilisateur d’exécuter rapidement une tâche personnalisée à partir de la notification sans avoir à lancer l’application. Ils peuvent être présentés sous la forme d’une liste de boutons avec des titres personnalisables ou d’un champ d’entrée de texte qui peut s’exécuter en arrière-plan (dans lequel l’application reçoit un peu de temps pour satisfaire la demande) ou le premier plan (où l’application est lancée au premier plan pour traiter la demande). Les actions personnalisées sont disponibles à la fois pour iOS et Watchos.
- **Ignorer l’action** : cette action est envoyée à l’application lorsque l’utilisateur ignore une notification donnée.

### <a name="creating-custom-actions"></a>Création d’actions personnalisées

Pour créer et inscrire une action personnalisée avec le système, utilisez le code suivant :

```csharp
// Create action
var actionID = "reply";
var title = "Reply";
var action = UNNotificationAction.FromIdentifier (actionID, title, UNNotificationActionOptions.None);

// Create category
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.None);
    
// Register category
var categories = new UNNotificationCategory [] { category };
UNUserNotificationCenter.Current.SetNotificationCategories (new NSSet<UNNotificationCategory>(categories)); 
```

Lors de la création d’un nouveau `UNNotificationAction` , il reçoit un ID unique et le titre qui s’affiche sur le bouton. Par défaut, l’action est créée en tant qu’action en arrière-plan, mais des options peuvent être fournies pour ajuster le comportement de l’action (par exemple, en lui affectant une action de premier plan).

Chacune des actions créées doit être associée à une catégorie. Lors de la création d’un `UNNotificationCategory` , un ID unique lui est attribué, une liste d’actions qu’il peut effectuer, une liste d’ID d’intention fournissant plus d’informations sur l’intention des actions dans la catégorie et certaines options pour contrôler le comportement de la catégorie.

Enfin, toutes les catégories sont inscrites auprès du système à l’aide de la `SetNotificationCategories` méthode.

### <a name="presenting-custom-actions"></a>Présentation des actions personnalisées

Une fois qu’un ensemble d’actions et de catégories personnalisées a été créé et inscrit auprès du système, il peut être présenté à partir de notifications locales ou distantes.

Pour notification à distance, définissez une valeur `category` dans la charge utile de notification distante qui correspond à l’une des catégories créées ci-dessus. Par exemple :

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Pour les notifications locales, définissez la `CategoryIdentifier` propriété de l' `UNMutableNotificationContent` objet. Par exemple :

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Là encore, cet ID doit correspondre à l’une des catégories créées ci-dessus.

### <a name="handling-dismiss-actions"></a>Gestion des actions d’annulation

Comme indiqué ci-dessus, une action d’annulation peut être envoyée à l’application lorsque l’utilisateur ignore une notification. Étant donné qu’il ne s’agit pas d’une action standard, une option doit être définie lors de la création de la catégorie. Par exemple :

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Gestion des réponses aux actions

Lorsque l’utilisateur interagit avec les actions personnalisées et les catégories créées ci-dessus, l’application doit accomplir la tâche demandée. Pour ce faire, vous fournissez un `UNUserNotificationCenterDelegate` et implémentez la `UserNotificationCenter` méthode. Par exemple :

```csharp
using System;
using UserNotifications;

namespace MonkeyNotification
{
    public class UserNotificationCenterDelegate : UNUserNotificationCenterDelegate
    {
        ...

        #region Override Methods
        public override void DidReceiveNotificationResponse (UNUserNotificationCenter center, UNNotificationResponse response, Action completionHandler)
        {
            // Take action based on Action ID
            switch (response.ActionIdentifier) {
            case "reply":
                // Do something
                break;
            default:
                // Take action based on identifier
                if (response.IsDefaultAction) {
                    // Handle default action...
                } else if (response.IsDismissAction) {
                    // Handle dismiss action
                }
                break;
            }

            // Inform caller it has been handled
            completionHandler();
        }
        #endregion
    }
}
```

La classe passée `UNNotificationResponse` a une `ActionIdentifier` propriété qui peut être l’action par défaut ou l’action faire disparaître. Utilisez `response.Notification.Request.Identifier` pour tester toutes les actions personnalisées.

La `UserText` propriété contient la valeur de toute entrée de texte de l’utilisateur. La `Notification` propriété contient la notification d’origine qui inclut la demande avec le déclencheur et le contenu de notification. L’application peut décider s’il s’agit d’une notification locale ou distante selon le type de déclencheur.

> [!NOTE]
> iOS 12 permet à une interface utilisateur de notification personnalisée de modifier ses boutons d’action au moment de l’exécution. Pour plus d’informations, consultez la documentation sur les [boutons d’action de notification dynamiques](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) .

## <a name="working-with-service-extensions"></a>Utilisation des extensions de service

Lorsque vous utilisez des notifications distantes, les _extensions de service_ offrent un moyen d’activer le chiffrement de bout en bout à l’intérieur de la charge utile de notification. Les extensions de service sont une extension non-utilisateur (disponible dans iOS 10) qui s’exécute en arrière-plan avec l’objectif principal de l’augmentation ou du remplacement du contenu visible d’une notification avant qu’elle ne soit présentée à l’utilisateur. 

[![Présentation de l’extension de service](enhanced-user-notifications-images/extension01.png)](enhanced-user-notifications-images/extension01.png#lightbox)

Les extensions de service sont conçues pour s’exécuter rapidement et ne peuvent être exécutées que par le système. Si l’extension de service ne parvient pas à terminer sa tâche dans le délai imparti, une méthode de secours sera appelée. Si le secours échoue, le contenu de la notification d’origine sera affiché à l’utilisateur.

Certaines utilisations possibles des extensions de service sont les suivantes :

- Fournir un chiffrement de bout en bout du contenu de notification à distance.
- Ajout de pièces jointes aux notifications distantes pour les enrichir.

### <a name="implementing-a-service-extension"></a>Implémentation d’une extension de service

Pour implémenter une extension de service dans une application Xamarin. iOS, procédez comme suit :

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **panneau solutions** puis sélectionnez **Ajouter**  >  **Ajouter un nouveau projet**.
3. Sélectionnez extensions **iOS** extensions  >    >  de service de **notification** , puis cliquez sur le bouton **suivant** : 

    [![Sélectionner les extensions du service de notification](enhanced-user-notifications-images/extension02.png)](enhanced-user-notifications-images/extension02.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le bouton **suivant** : 

    [![Entrer un nom pour l’extension](enhanced-user-notifications-images/extension03.png)](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajustez le **nom du projet** et/ou le nom de la **solution** , si nécessaire, puis cliquez sur le bouton **créer** : 

    [![Ajuster le nom du projet et/ou le nom de la solution](enhanced-user-notifications-images/extension04.png)](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

1. Ouvrez la solution de l’application dans Visual Studio.
2. Cliquez avec le bouton droit sur le nom de la solution dans le **Explorateur de solutions** puis sélectionnez **Ajouter > nouveau projet...**.
3. Sélectionnez **Visual C# > extensions iOS > extension de service de notification**:

    [![Sélectionner les extensions du service de notification](enhanced-user-notifications-images/extension01.w157-sml.png)](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le bouton **OK** .

-----

> [!IMPORTANT]
> L’identificateur de Bundle pour l’extension de service doit correspondre à l’identificateur de Bundle de l’application principale avec `.appnameserviceextension` ajouté à la fin. Par exemple, si l’application principale avait un identificateur de Bundle de  `com.xamarin.monkeynotify` , l’extension de service doit avoir un identificateur de Bundle `com.xamarin.monkeynotify.monkeynotifyserviceextension` . Elle doit être définie automatiquement lorsque l’extension est ajoutée à la solution. 

Il existe une classe principale dans l’extension de service de notification qui doit être modifiée pour fournir les fonctionnalités requises. Par exemple :

```csharp
using System;
using Foundation;
using UIKit;
using UserNotifications;

namespace MonkeyChatServiceExtension
{
    [Register ("NotificationService")]
    public class NotificationService : UNNotificationServiceExtension
    {
        #region Computed Properties
        public Action<UNNotificationContent> ContentHandler { get; set; }
        public UNMutableNotificationContent BestAttemptContent { get; set; }
        #endregion

        #region Constructors
        protected NotificationService (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void DidReceiveNotificationRequest (UNNotificationRequest request, Action<UNNotificationContent> contentHandler)
        {
            ContentHandler = contentHandler;
            BestAttemptContent = (UNMutableNotificationContent)request.Content.MutableCopy ();

            // Modify the notification content here...
            BestAttemptContent.Title = $"{BestAttemptContent.Title}[modified]";

            ContentHandler (BestAttemptContent);
        }

        public override void TimeWillExpire ()
        {
            // Called just before the extension will be terminated by the system.
            // Use this as an opportunity to deliver your "best attempt" at modified content, otherwise the original push payload will be used.

            ContentHandler (BestAttemptContent);
        }
        #endregion
    }
}
```

La première méthode, `DidReceiveNotificationRequest` , reçoit l’identificateur de notification ainsi que le contenu de notification via l' `request` objet. Le passé doit `contentHandler` être appelé pour présenter la notification à l’utilisateur.

La deuxième méthode, `TimeWillExpire` , sera appelée juste avant que le temps ne soit sur le point de s’exécuter pour que l’extension de service traite la demande. Si l’extension de service ne parvient pas à appeler l’intervalle `contentHandler` de temps alloué, le contenu d’origine est affiché à l’utilisateur.

### <a name="triggering-a-service-extension"></a>Déclenchement d’une extension de service

Lorsqu’une extension de service est créée et fournie avec l’application, elle peut être déclenchée par la modification de la charge utile de notification distante envoyée à l’appareil. Par exemple :

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

La nouvelle `mutable-content` clé spécifie que l’extension de service doit être lancée pour mettre à jour le contenu de la notification distante. La `encrypted-content` clé contient les données chiffrées que l’extension de service peut déchiffrer avant de la présenter à l’utilisateur.

Jetez un coup d’œil à l’exemple d’extension de service suivant :

```csharp
using UserNotification;

namespace myApp {
    public class NotificationService : UNNotificationServiceExtension {
    
        public override void DidReceiveNotificationRequest(UNNotificationRequest request, contentHandler) {
            // Decrypt payload
            var decryptedBody = Decrypt(Request.Content.UserInfo["encrypted-content"]);
            
            // Modify Notification body
            var newContent = new UNMutableNotificationContent();
            newContent.Body = decryptedBody;
            
            // Present to user
            contentHandler(newContent);
        }
        
        public override void TimeWillExpire() {
            // Handle out-of-time fallback event
            ...
        }
        
    }
}
```

Ce code déchiffre le contenu chiffré à partir de la `encrypted-content` clé, crée un nouveau `UNMutableNotificationContent` , définit la `Body` propriété sur le contenu déchiffré et utilise le `contentHandler` pour présenter la notification à l’utilisateur.

## <a name="summary"></a>Récapitulatif

Cet article a abordé tous les modes d’amélioration de la notification des utilisateurs par iOS 10. Il a présenté la nouvelle infrastructure de notification utilisateur et comment l’utiliser dans une application Xamarin. iOS ou une extension d’application.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [Référence du Framework UserNotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guide de programmation des notifications locales et distantes](https://developer.apple.com/documentation/usernotifications)
