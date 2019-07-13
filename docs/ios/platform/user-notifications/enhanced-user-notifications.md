---
title: Améliorations des Notifications utilisateur dans Xamarin.iOS
description: Cet article décrit l’infrastructure de Notifications à l’utilisateur introduite dans iOS 10. Il aborde les notifications locales, des notifications à distance, gestion de notification, actions de notification et bien plus encore.
ms.prod: xamarin
ms.assetid: 4E1FF652-28F0-4566-B383-9D12664401A4
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/02/2017
ms.openlocfilehash: afa20a264e2509a5658cd0d8f90da3148315e803
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865725"
---
# <a name="enhanced-user-notifications-in-xamarinios"></a>Améliorations des Notifications utilisateur dans Xamarin.iOS

Nouveau à iOS 10, la Notification utilisateur framework permet de la fourniture et la gestion des notifications locales et distantes. À l’aide de cette infrastructure, une application ou une Extension d’application permettre planifier la remise des notifications locales en spécifiant un ensemble de conditions comme emplacement ou de l’heure.

## <a name="about-user-notifications"></a>À propos des Notifications utilisateur

Comme indiqué ci-dessus, la nouvelle infrastructure de Notification à l’utilisateur permet de la fourniture et la gestion des notifications locales et distantes. À l’aide de cette infrastructure, une application ou une Extension d’application permettre planifier la remise des notifications locales en spécifiant un ensemble de conditions comme emplacement ou de l’heure.

En outre, l’application ou une extension peut s’afficher (et éventuellement modifier) notifications locales et distantes comme ils sont remis à l’appareil iOS de l’utilisateur.

La nouvelle infrastructure de l’interface utilisateur de la Notification utilisateur permet à une application ou une Extension d’application pour personnaliser l’apparence des notifications locales et distantes quand elles sont présentées à l’utilisateur.

Cette infrastructure fournit les méthodes suivantes qui une application peut fournir des notifications à un utilisateur :

- **Alertes Visual** - où reporte la notification à partir du haut de l’écran sous forme de bannière.
- **Vibrations et son** -peut être associé à une notification.
- **Icône application un badge** - où icône de l’application affiche un badge indique que le nouveau contenu est disponible, comme le nombre de messages électroniques non lus.

En outre, selon le contexte de l’utilisateur actuel, il existe différentes façons dont une notification s’affiche :

- Si l’appareil est déverrouillé, la notification sera généraliser à partir du haut de l’écran sous forme de bannière.
- Si l’appareil est verrouillé, la notification s’affichera sur l’écran de verrouillage de l’utilisateur.
- Si l’utilisateur a manqué une notification, ils peuvent ouvrir le centre de Notification et afficher les notifications en attente disponibles, il.

Une application Xamarin.iOS a deux types de Notifications à l’utilisateur qu’il est en mesure d’envoyer :

- **Notifications locales** -ceux-ci sont envoyés par les applications installées localement sur l’appareil de l’utilisateur.
- **Notifications à distance** -sont envoyés à partir d’un référentiel distant serveur et soit présenté à l’utilisateur, ou ils déclenchent une mise à jour d’arrière-plan du contenu de l’application.

### <a name="about-local-notifications"></a>À propos des Notifications locales

Les Notifications locales auxquelles une application iOS peut envoyer ont les fonctionnalités et les attributs suivants :

- Elles sont envoyées par les applications qui sont locales sur l’appareil de l’utilisateur. 
- Ils sont peut être configuré heure ou emplacement basé sur les déclencheurs. 
- L’application planifie la notification avec l’appareil de l’utilisateur et il s’affiche lorsque la condition de déclenchement est remplie.
- Lorsque l’utilisateur interagit avec une notification, l’application reçoit un rappel.

Voici quelques exemples de Notifications locales :

- Alertes de calendrier
- Alertes de rappel
- Emplacement des déclencheurs prenant en charge

Pour plus d’informations, consultez le site d’Apple [Local et le Guide de programmation de Notification à distance](https://developer.apple.com/documentation/usernotifications) documentation.

### <a name="about-remote-notifications"></a>À propos des Notifications à distance

Les Notifications à distance une application iOS peut envoyer ont les fonctionnalités et les attributs suivants :

- L’application a un composant côté serveur avec lesquels il communique.
- Apple Push Notification Service (APNs) est utilisé pour transmettre une remise optimale des Notifications à distance à l’appareil de l’utilisateur à partir de serveurs du cloud en fonction du développeur.
- Lorsque l’application reçoit la Notification à distance, il sera affiché à l’utilisateur.
- Lorsque l’utilisateur interagit avec la notification, l’application reçoit un rappel.

Voici quelques exemples de Notifications à distance :

- Nouvelles alertes
- Mises à jour de sport
- Messages de messagerie instantanées

Il existe deux types de Notifications à distance disponibles pour une application iOS :

- **Accessible sur utilisateur** -ceux-ci s’affichent pour l’utilisateur sur l’appareil.
- **Mises à jour en mode silencieux** -ils fournissent un mécanisme pour mettre à jour le contenu d’une application iOS en arrière-plan. Lorsqu’une mise à jour en mode silencieux est reçue, l’application peut contacter l’extraction de serveurs remove vers le bas le contenu le plus récent.

Pour plus d’informations, consultez le site d’Apple [Local et le Guide de programmation de Notification à distance](https://developer.apple.com/documentation/usernotifications) documentation.

### <a name="about-the-existing-notifications-api"></a>À propos de l’API existant de Notifications

Avant d’iOS 10, utilisez une application iOS `UIApplication` pour inscrire une notification avec le système et à planifier la façon dont cette notification doit être déclenchée (par heure ou l’emplacement).

Il existe plusieurs problème qu’un développeur peut rencontrer lorsque vous travaillez avec l’API de notification existant :

- Il y avait des différents rappels requis pour Local ou des Notifications à distance qui risqueraient de duplication de code.
- L’application avait un contrôle limité de la notification une fois qu’il avait été planifiée avec le système.
- Différents niveaux de prise en charge se sont produites sur l’ensemble de plateformes existantes d’Apple.

### <a name="about-the-new-user-notification-framework"></a>Sur la nouvelle infrastructure de Notification utilisateur

Avec iOS 10, Apple a introduit la nouvelle infrastructure de Notification à l’utilisateur, qui remplace l’existante `UIApplication` méthode indiqués ci-dessus.

L’infrastructure de Notification des utilisateurs offre les avantages suivants :

- API familier qui inclut la parité des fonctionnalités avec les méthodes précédentes pour simplifier la porter du code à partir de l’infrastructure existante.
- Inclut un ensemble étendu d’options de contenu qui permet de notifications à envoyer à l’utilisateur plus riches.
- À la fois locaux et des Notifications à distance peuvent être gérées par le même code et les rappels.
- Simplifie le processus de gestion des rappels qui sont envoyés à une application lorsque l’utilisateur interagit avec une notification.
- Gestion améliorée des notifications en attente et remises, y compris la possibilité de supprimer ou mettre à jour de notifications.
- Ajoute la possibilité d’effectuer la présentation dans l’application de notifications.
- Ajoute la possibilité de planifier et gérer les notifications à partir dans les Extensions d’application.
- Ajoute un nouveau point d’extension pour les notifications proprement dits. 

La nouvelle infrastructure de Notification à l’utilisateur fournit une notification unifiée API sur le plusieurs des plateformes que Apple prend en charge, notamment : 

- **iOS** -prise en charge pour gérer et planifier des notifications complète.
- **tvOS** -ajoute la possibilité pour les icônes d’application badge des notifications locales et distantes.
- **watchOS** : ajoute la possibilité de transférer les notifications à partir de son appareil iOS couplé à leur Apple Watch et permet aux applications de surveillance pour les notifications locales directement sur l’Apple watch elle-même.

Pour plus d’informations, consultez le site d’Apple [référence de l’infrastructure UserNotifications](https://developer.apple.com/reference/usernotifications) et [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui) documentation.

## <a name="preparing-for-notification-delivery"></a>Préparation pour la remise de Notification

Avant un iOS application peut envoyer des notifications à l’utilisateur de que l’application doit être inscrit auprès du système et, une notification étant une interruption à l’utilisateur, une application doit demander explicitement l’autorisation avant de les envoyer.

Il existe trois niveaux différents de demandes de notification que l’utilisateur peut approuver pour une application :

- Bannière s’affiche.
- Alertes sonores.
- Un badge l’icône d’application.

En outre, ces niveaux d’approbation doive être demandés et définir pour les notifications locales et distantes.

Autorisation de notification doit être demandée dès que l’application démarre en ajoutant le code suivant à la `FinishedLaunching` méthode de la `AppDelegate` et en définissant le type de notification souhaitée (`UNAuthorizationOptions`) :

```csharp
using UserNotifications;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{
    // Request notification permissions from the user
    UNUserNotificationCenter.Current.RequestAuthorization (UNAuthorizationOptions.Alert, (approved, err) => {
        // Handle approval
    });

    return true;
}
```

En outre, un utilisateur peut toujours modifier les privilèges de notification pour une application à tout moment le **paramètres** application sur l’appareil. L’application doit vérifier pour les privilèges de l’utilisateur demandé de notification avant de présenter une notification en utilisant le code suivant :

```csharp
// Get current notification settings
UNUserNotificationCenter.Current.GetNotificationSettings ((settings) => {
    var alertsAllowed = (settings.AlertSetting == UNNotificationSetting.Enabled);
});    
``` 

### <a name="configuring-the-remote-notifications-environment"></a>Configuration de l’environnement de Notifications à distance

Nouveau à iOS 10, le développeur doit informer le système d’exploitation le notifications Push de l’environnement sont en cours d’exécution en tant que le développement ou de Production. L’incapacité à fournir ces informations peut entraîner l’application rejetée soumis à l’App Store iTune avec une notification similaire à ce qui suit :

> Droits de Notification Push manquant - votre application inclut une API pour le service de Notification Push d’Apple, mais la `aps-environment` droit est manquant dans la signature de l’application.

Pour fournir les droits requis, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Double-cliquez sur le `Entitlements.plist` de fichiers dans le **panneau solutions** à ouvrir pour modification.
2. Basculez vers le **Source** vue : 

    [![](enhanced-user-notifications-images/setup01.png "La vue de Source")](enhanced-user-notifications-images/setup01.png#lightbox)
3. Cliquez sur le **+** pour ajouter une nouvelle clé.
4. Entrez `aps-environment` pour le **propriété**, laissez le **Type** comme `String` et entrez `development` ou `production` pour le **valeur**: 

    [![](enhanced-user-notifications-images/setup02.png "La propriété d’environnement de points d’accès")](enhanced-user-notifications-images/setup02.png#lightbox)
5. Enregistrez les modifications dans le fichier.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Double-cliquez sur le `Entitlements.plist` de fichiers dans le **l’Explorateur de solutions** à ouvrir pour modification.
2. Cliquez sur le **+** pour ajouter une nouvelle clé.
3. Entrez `aps-environment` pour le **propriété**, laissez le **Type** comme `String` et entrez `development` ou `production` pour le **valeur**: 

    [![](enhanced-user-notifications-images/setup02w.png "La propriété d’environnement de points d’accès")](enhanced-user-notifications-images/setup02.png#lightbox)
4. Enregistrez les modifications dans le fichier.

-----

### <a name="registering-for-remote-notifications"></a>L’inscription aux Notifications à distance

Si l’application sera envoyer et recevoir des Notifications à distance, il sera toujours besoin d’effectuer _d’inscription de jeton_ à l’aide des `UIApplication` API. Cette inscription nécessite l’appareil doit avoir un accès à la connexion réseau actif APNs, ce qui génère le jeton nécessaire qui sera envoyé à l’application. L’application doit ensuite transférer ce jeton pour l’application côté serveur du développeur s’inscrire aux notifications à distance :

[![](enhanced-user-notifications-images/token01.png "Vue d’ensemble de jetons d’inscription")](enhanced-user-notifications-images/token01.png#lightbox)

Utilisez le code suivant pour initialiser l’inscription obligatoire :

```csharp
UIApplication.SharedApplication.RegisterForRemoteNotifications ();
```

Le jeton qui est envoyé à l’application côté serveur du développeur doit être inclus comme partie de la Notification de la charge utile que get envoyée à partir du serveur pour APNs lors de l’envoi d’une Notification à distance :

[![](enhanced-user-notifications-images/token02.png "Le jeton inclus dans le cadre de la charge utile de Notification")](enhanced-user-notifications-images/token02.png#lightbox)

Le jeton fait Office de clé qui relie la notification et l’application utilisée pour ouvrir ou de répondre à la notification.

Pour plus d’informations, consultez le site d’Apple [Local et le Guide de programmation de Notification à distance](https://developer.apple.com/documentation/usernotifications) documentation.

## <a name="notification-delivery"></a>Remise de notification

Avec l’application entièrement inscrit et les autorisations requises demandé par et accordé par l’utilisateur, l’application est maintenant prête à envoyer et recevoir des notifications. 

### <a name="providing-notification-content"></a>Fournir un contenu de Notification

Nouveau à iOS 10, toutes les notifications contient à la fois un **titre** et **sous-titre** qui sera toujours affiché avec la **corps** du contenu de notification. Nouvelle, est également la possibilité d’ajouter **médias joints** pour le contenu de la notification.

Pour créer le contenu d’une Notification locale, utilisez le code suivant :

```csharp
var content = new UNMutableNotificationContent();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
```

Pour les Notifications à distance, le processus est similaire :

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

### <a name="scheduling-when-a-notification-is-sent"></a>Planification lors d’une Notification est envoyée.

Avec le contenu de la Notification créée, l’application a besoin de planifier le moment où la notification s’affiche à l’utilisateur en définissant un *déclencheur*. iOS 10 fournit quatre différents types de déclencheur :

- **Notification push** - est utilisé exclusivement avec les Notifications à distance et est déclenchée quand APNs les envoie une notification de package à l’application en cours d’exécution sur l’appareil.
- **Intervalle de temps** -permet à une Notification locale planifié à partir d’une heure, intervalle commencer dès maintenant et se terminant par un un futur point. Par exemple, `var trigger =  UNTimeIntervalNotificationTrigger.CreateTrigger (5, false);`.
- **Date du calendrier** -autorise des Notifications locales est planifié pour une date et heure spécifiques.
- **Basé sur emplacement** -autorise des Notifications locales à planifier quand l’appareil iOS est entrant ou en laissant un emplacement géographique spécifique ou dans une proximité donnée à n’importe quel balises Bluetooth.

Lorsqu’une Notification locale est prête, l’application doit appeler la `Add` méthode de la `UNUserNotificationCenter` objet à planifier son affichage à l’utilisateur. Pour les Notifications à distance, l’application côté serveur envoie une charge utile de Notification pour le certificat APNs, qui envoie ensuite le paquet à l’appareil de l’utilisateur.

Rassemble tous les éléments, un exemple de Notification locale peut ressembler à :

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

## <a name="handling-foreground-app-notifications"></a>Gestion des Notifications de l’application de premier plan

Nouveau à iOS 10, une application peut gérer les Notifications différemment lorsqu’il est au premier plan et une Notification est déclenchée. En fournissant un `UNUserNotificationCenterDelegate` et l’implémentation de la `WillPresentNotification` (méthode), l’application peut prendre en charge la responsabilité pour l’affichage de la Notification. Par exemple :

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

Ce code est simplement écrire le contenu de la `UNNotification` à la sortie de l’Application et demandez au système pour afficher l’alerte standard pour la notification. 

Si l’application souhaite afficher la notification elle-même quand il était au premier plan et pas d’utiliser les valeurs par défaut du système, de passer `None` au gestionnaire d’achèvement. Exemple :

```csharp
completionHandler (UNNotificationPresentationOptions.None);
```

Avec ce code en place, ouvrez le `AppDelegate.cs` pour la modification du fichier et changer la `FinishedLaunching` méthode à rechercher comme suit :

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

Ce code concerne l’attachement personnalisé `UNUserNotificationCenterDelegate` ci-dessus actuel `UNUserNotificationCenter` afin de l’application peut gérer la notification lorsqu’elle est active et au premier plan.

## <a name="notification-management"></a>Gestion de notification

Nouveau à iOS 10, gestion de Notification fournit l’accès aux Notifications en attente et remises et ajoute la possibilité de supprimer, mettre à jour ou promouvoir ces Notifications.

Une partie importante de la gestion de Notification est le _identificateur de demander_ qui a été assigné à la Notification lorsqu’il a été créé et planifié avec le système. Pour des Notifications à distance, il est attribué par le biais de la nouvelle `apps-collapse-id` champ dans l’en-tête de demande HTTP.

L’identificateur de demande permet de sélectionner la Notification selon laquelle l’application souhaite effectuer la gestion de la Notification sur.

### <a name="removing-notifications"></a>Suppression de Notifications

Pour supprimer une Notification en attente à partir du système, utilisez le code suivant :

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemovePendingNotificationRequests (requests);
```

Pour supprimer une Notification déjà remises, utilisez le code suivant :

```csharp
var requests = new string [] { "sampleRequest" };
UNUserNotificationCenter.Current.RemoveDeliveredNotifications (requests);
```

### <a name="updating-an-existing-notification"></a>La mise à jour d’une Notification existante

Pour mettre à jour une Notification existante, simplement créer une notification avec les paramètres souhaités modifiés (par exemple, une nouvelle heure de déclenchement) et ajoutez-le au système avec le même identificateur de demander que la Notification qui doit être modifié. Exemple :


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

Pour les Notifications déjà remises, la Notification existante sera obtenir mis à jour et promue vers le haut de la liste sur les écrans d’accueil et de verrouillage et dans le centre de Notification Si elle a déjà été lue par l’utilisateur.

## <a name="working-with-notification-actions"></a>Utilisation des Actions de Notification

Dans iOS 10, les Notifications sont remises à l’utilisateur ne sont pas statiques et offrent plusieurs moyens que l’utilisateur peut interagir avec eux (à partir d’intégré à des Actions personnalisées).

Il existe trois types d’Actions une application iOS peut répondre à :

- **Action par défaut** -il s’agit quand l’utilisateur appuie sur une Notification à l’application s’ouvre et affiche les détails de la Notification donnée.
- **Actions personnalisées** -ceux-ci ont été ajoutés dans iOS 8 et offrent un moyen rapide pour l’utilisateur d’effectuer une tâche personnalisée directement à partir de la Notification sans avoir à lancer l’application. Ils peuvent être présentées comme une liste de boutons avec un titre personnalisable ou un texte champ d’entrée qui peut s’exécuter en arrière-plan (où l’application reçoit une petite quantité de temps à répondre à la demande) ou de premier plan (où l’application est lancée au premier plan pour fu lfill la demande). Actions personnalisées sont disponibles sur iOS et watchOS.
- **Faire disparaître Action** -cette Action est envoyée à l’application lorsque l’utilisateur fermera une Notification donnée.

### <a name="creating-custom-actions"></a>Création d’Actions personnalisées

Pour créer et enregistrer une Action personnalisée avec le système, utilisez le code suivant :

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

Lorsque vous créez un nouveau `UNNotificationAction`, est associé à un ID unique et le titre qui apparaîtra sur le bouton. Par défaut, l’Action est créée comme une Action en arrière-plan, mais les options peuvent être fournies pour ajuster le comportement de l’Action (par exemple définis sur une Action de premier plan).

Chacune des Actions créées doivent être associées à une catégorie. Lorsque vous créez un nouveau `UNNotificationCategory`, elle est affectée à un ID unique, une liste d’Actions, il peut effectuer, une liste d’ID d’intention de fournir plus d’informations sur l’intention des Actions dans la catégorie et quelques options pour contrôler le comportement de la catégorie.

Enfin, toutes les catégories sont enregistrés avec le système à l’aide de la `SetNotificationCategories` (méthode).

### <a name="presenting-custom-actions"></a>Présentation des Actions personnalisées

Une fois qu’un ensemble d’Actions personnalisées et les catégories ont été créés et enregistrés avec le système, ils peuvent être présentées à partir de Local ou de Notifications à distance.

Pour recevoir une Notification à distance, vous devez définir un `category` dans la charge utile Notification à distance qui correspond à l’une des catégories créés ci-dessus. Par exemple :

```csharp
{
    aps:{
        alert:"Hello world!",
        category:"message"
    }
}
```

Pour les Notifications locales, définissez la `CategoryIdentifier` propriété de la `UNMutableNotificationContent` objet. Par exemple :

```csharp
var content = new UNMutableNotificationContent ();
content.Title = "Notification Title";
content.Subtitle = "Notification Subtitle";
content.Body = "This is the message body of the notification.";
content.Badge = 1;
content.CategoryIdentifier = "message";
...
```

Là encore, cet ID doit correspondre à une des catégories qui a été créé ci-dessus.

### <a name="handling-dismiss-actions"></a>Gestion d’ignorer des Actions

Comme indiqué ci-dessus, une Action de faire disparaître peuvent être envoyée à l’application lorsque l’utilisateur fermera une Notification. Dans la mesure où il ne s’agit pas d’une Action standard, une option devez défini lors de la catégorie est créée. Par exemple :

```csharp
var categoryID = "message";
var actions = new UNNotificationAction [] { action };
var intentIDs = new string [] { };
var categoryOptions = new UNNotificationCategoryOptions [] { };
var category = UNNotificationCategory.FromIdentifier (categoryID, actions, intentIDs, UNNotificationCategoryOptions.CustomDismissAction);

```

### <a name="handling-action-responses"></a>Gestion des réponses aux actions

Quand l’utilisateur interagit avec les Actions personnalisées et les catégories qui ont été créées ci-dessus, l’application doit répondre à la tâche demandée. Cela est effectué en fournissant un `UNUserNotificationCenterDelegate` et l’implémentation de la `UserNotificationCenter` (méthode). Par exemple :

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

Passé dans `UNNotificationResponse` classe a un `ActionIdentifier` propriété qui peut être l’Action par défaut ou l’Action de faire disparaître. Utilisez `response.Notification.Request.Identifier` pour tester des actions personnalisées.

Le `UserText` propriété conserve la valeur de n’importe quel utilisateur saisie. Le `Notification` propriété contient le contenu de Notification et de la Notification d’origine qui inclut la demande avec le déclencheur. L’application peut décider s’il s’agissait d’une variable locale ou Notification à distance en fonction du type de déclencheur.

> [!NOTE]
> iOS 12 rend possible pour une interface utilisateur de notification personnalisée modifier ses boutons d’action lors de l’exécution. Pour plus d’informations, examinez la [boutons d’action dynamique notification](~/ios/platform/introduction-to-ios12/notifications/dynamic-actions.md) documentation.

## <a name="working-with-service-extensions"></a>Utilisation des Extensions de Service

Lorsque vous travaillez avec des Notifications à distance, _Service Extensions_ offrent un moyen pour activer le chiffrement de bout en bout à l’intérieur de la charge utile de Notification. Les Extensions de service sont une extension sans Interface utilisateur (disponible dans iOS 10) qui s’exécutent en arrière-plan avec l’objectif principal de l’augmentation ou en remplaçant le contenu visible d’une Notification avant qu’il est présenté à l’utilisateur. 

[![](enhanced-user-notifications-images/extension01.png "Vue d’ensemble de l’Extension de service")](enhanced-user-notifications-images/extension01.png#lightbox)

Les Extensions de service sont destinées à s’exécuter rapidement et reçoivent uniquement un court laps de temps à exécuter par le système. Dans le cas où l’Extension de Service ne parvient pas à terminer sa tâche dans le délai imparti, une méthode de secours sera appelée. Si la procédure de secours échoue, le contenu de Notification d’origine s’affichera à l’utilisateur.

Certaines utilisations possibles des Extensions de Service sont les suivantes :

- Un cryptage de bout en bout pour le contenu de Notification à distance.
- Ajout de pièces jointes pour envoyer des Notifications à distance pour les enrichir.

### <a name="implementing-a-service-extension"></a>Implémentation d’une Extension de Service

Pour implémenter une Extension de Service dans une application Xamarin.iOS, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

1. Ouvrez la solution de l’application dans Visual Studio pour Mac.
2. Avec le bouton droit sur le nom de la Solution dans le **panneau solutions** et sélectionnez **ajouter** > **ajouter un nouveau projet**.
3. Sélectionnez **iOS** > **Extensions** > **Extensions de Service de Notification** et cliquez sur le **suivant** bouton : 

    [![](enhanced-user-notifications-images/extension02.png "Sélectionnez les Extensions de Service de Notification")](enhanced-user-notifications-images/extension02.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le **suivant** bouton : 

    [![](enhanced-user-notifications-images/extension03.png "Entrez un nom pour l’extension")](enhanced-user-notifications-images/extension03.png#lightbox)
5. Ajuster la **nom_projet** et/ou **nom de la Solution** si nécessaire et cliquez sur le **créer** bouton : 

    [![](enhanced-user-notifications-images/extension04.png "Ajuster le nom du projet et/ou le nom de la Solution")](enhanced-user-notifications-images/extension04.png#lightbox) 

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Ouvrez la solution de l’application dans Visual Studio.
2. Avec le bouton droit sur le nom de la Solution dans le **l’Explorateur de solutions** et sélectionnez **Ajouter > Nouveau projet...** .
3. Sélectionnez **Visual C# > Extensions iOS > Extension de Service de Notification**:

    [![](enhanced-user-notifications-images/extension01.w157-sml.png "Sélectionnez les Extensions de Service de Notification")](enhanced-user-notifications-images/extension01.w157.png#lightbox)
4. Entrez un **nom** pour l’extension et cliquez sur le **OK** bouton.

-----

> [!IMPORTANT]
> L’identificateur de Bundle de l’extension du service doit correspondre à l’identificateur de Bundle de l’application principale avec `.appnameserviceextension` ajouté à la fin. Par exemple, si l’application principale avait un identificateur de Bundle de `com.xamarin.monkeynotify`, l’extension de service doit avoir un identificateur de Bundle de `com.xamarin.monkeynotify.monkeynotifyserviceextension`. Cela doit être définie automatiquement lorsque l’extension est ajoutée à la solution. 

Il est une classe principale dans l’Extension de Service de Notification qui doivent être modifiées pour fournir la fonctionnalité requise. Par exemple :

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

La première méthode, `DidReceiveNotificationRequest`, sera passé l’identificateur de Notification, ainsi que le contenu de Notification via le `request` objet. Passé dans `contentHandler` sera doivent être appelés pour présenter la Notification à l’utilisateur.

La deuxième méthode, `TimeWillExpire`, sera appelée juste avant l’heure est bientôt terminé pour l’Extension de Service traiter la demande. Si l’Extension de Service ne parvient pas à appeler le `contentHandler` dans le délai imparti, le contenu d’origine s’affichera à l’utilisateur.

### <a name="triggering-a-service-extension"></a>Déclenchement d’une Extension de Service

Avec une Extension de Service créé et remis avec l’application, qu’il puisse être déclenché en modifiant la charge à distance utile de la Notification envoyée à l’appareil. Par exemple :

```csharp
{
    aps : {
        alert : "New Message Available",
        mutable-content: 1
    },
    encrypted-content : "#theencryptedcontent"
}
```

La nouvelle `mutable-content` clé spécifie que l’Extension de Service doit être lancé pour mettre à jour le contenu de la Notification à distance. Le `encrypted-content` clé conserve les données chiffrées que l’Extension de Service peut déchiffrer avant de les présenter à l’utilisateur.

Examinons l’exemple suivant, Extension de Service :

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

Ce code déchiffre le contenu chiffré à partir de la `encrypted-content` clé, crée un nouveau `UNMutableNotificationContent`, définit le `Body` propriété au contenu déchiffré et utilise le `contentHandler` pour présenter la notification à l’utilisateur.

## <a name="summary"></a>Récapitulatif

Cet article a présenté toutes les méthodes que la Notification des utilisateurs ont été améliorée par iOS 10. Il affiche la nouvelle infrastructure de Notification à l’utilisateur et comment l’utiliser dans une application Xamarin.iOS ou d’une Extension d’application.



## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](https://developer.xamarin.com/samples/ios/iOS10/)
- [Référence de l’infrastructure Usernotifications](https://developer.apple.com/reference/usernotifications)
- [UserNotificationsUI](https://developer.apple.com/reference/usernotificationsui)
- [Guide de programmation Notification locaux et distants](https://developer.apple.com/documentation/usernotifications)
