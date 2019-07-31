---
title: Notifications push dans iOS
description: Ce document explique comment utiliser les notifications push dans iOS 9 et versions antérieures. Il aborde les certificats, l’inscription auprès du service Apple Push notifications Gateway (APNS), et bien plus encore.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: 143dd8c5ad2e762235641680e1217a11e1b48bb5
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68651324"
---
# <a name="push-notifications-in-ios"></a>Notifications push dans iOS

> [!IMPORTANT]
> Les informations contenues dans cette section concernent iOS 9 et les versions antérieures, elles ont été laissées ici pour prendre en charge les anciennes versions d’iOS. Pour iOS 10 et versions ultérieures, consultez le Guide de l' [infrastructure de notification utilisateur](~/ios/platform/user-notifications/index.md) pour prendre en charge la notification locale et distante sur un appareil iOS.

Les notifications push doivent être conservées de façon concise et ne contenir que suffisamment de données pour informer l’application mobile qu’elle doit contacter l’application serveur pour une mise à jour. Par exemple, lorsque de nouveaux courriers électroniques arrivent, l’application serveur notifie uniquement à l’application mobile que le nouvel e-mail est arrivé. La notification ne contient pas le nouvel e-mail lui-même. L’application mobile récupère ensuite les nouveaux messages électroniques à partir du serveur lorsqu’elle était appropriée.

Au centre des notifications push dans iOS se trouve le *service Apple Push Notification Gateway (APNs)* . Il s’agit d’un service fourni par Apple et chargé de router les notifications d’un serveur d’applications vers des appareils iOS.
L’image suivante illustre la topologie de notification push pour iOS: ![](remote-notifications-in-ios-images/image4.png "Cette image illustre la topologie de notification push pour iOS")

Les notifications distantes sont des chaînes au format JSON qui adhèrent au format et aux protocoles spécifiés dans [la section charge utile de notification](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) du [Guide de programmation des notifications locales et Push](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) dans la [documentation du développeur iOS. ](https://developer.apple.com/devcenter/ios/index.action).

Apple gère deux environnements de APNS: un *bac à sable (sandbox)* et un environnement de *production* . L’environnement du bac à sable (sandbox) est destiné à des tests pendant la `gateway.sandbox.push.apple.com` phase de développement et se trouve sur le port TCP 2195. L’environnement de production est destiné à être utilisé dans les applications qui ont été déployées et se `gateway.push.apple.com` trouve sur le port TCP 2195.

## <a name="requirements"></a>Configuration requise

La notification push doit respecter les règles suivantes qui sont dictées par l’architecture de APNS:

-  **limite de messages de 256 octets** : la taille de message entière de la notification ne doit pas dépasser 256 octets.
-  **Aucune confirmation de réception** -APNs ne fournit pas à l’expéditeur une notification indiquant qu’un message l’a envoyé au destinataire prévu. Si l’appareil est inaccessible et que plusieurs notifications séquentielles sont envoyées, toutes les notifications, à l’exception de la plus récente, seront perdues. Seule la notification la plus récente sera remise à l’appareil.
-  **Chaque application requiert un certificat sécurisé: la** communication avec APNs doit être effectuée via le protocole SSL.


## <a name="creating-and-using-certificates"></a>Création et utilisation de certificats

Chacun des environnements mentionnés dans la section précédente requiert leur propre certificat. Cette section explique comment créer un certificat, l’associer à un profil de provisionnement, puis obtenir un certificat d’échange d’informations personnelles à utiliser avec PushSharp.

1.  Pour créer des certificats, accédez au portail d’approvisionnement iOS sur le site Web d’Apple, comme indiqué dans la capture d’écran suivante (Remarquez l’élément de menu ID d’application sur la gauche):

    [![](remote-notifications-in-ios-images/image5new.png "Portail d’approvisionnement iOS sur le site Web apples")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  Ensuite, accédez à la section de l’ID de l’application et créez un nouvel ID d’application, comme illustré dans la capture d’écran suivante:

    [![](remote-notifications-in-ios-images/image6new.png "Accédez à la section ID d’application et créez un nouvel ID d’application")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Lorsque vous cliquez sur le **+** bouton, vous pouvez entrer la description et un identificateur de Bundle pour l’ID d’application, comme indiqué dans la capture d’écran suivante:

    [![](remote-notifications-in-ios-images/image7new.png "Entrer la description et un identificateur de Bundle pour l’ID d’application")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Veillez à sélectionner l' **ID d’application explicite** et à ce que l’identificateur de Bundle `*` ne se termine pas par un. Cela créera un identificateur adapté à plusieurs applications, et les certificats de notification push doivent être pour une seule application.

5. Sous App Services, sélectionnez **notifications push**:

    [![](remote-notifications-in-ios-images/image8new.png "Sélectionner les notifications push")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. Et appuyez sur **Envoyer** pour confirmer l’inscription du nouvel ID d’application:

    [![](remote-notifications-in-ios-images/image9new.png "Confirmer l’inscription du nouvel ID d’application")](remote-notifications-in-ios-images/image9new.png#lightbox)

7.  Ensuite, vous devez créer le certificat pour l’ID de l’application. Dans le volet de navigation de gauche, accédez à **certificats > tout** et `+` sélectionnez le bouton, comme illustré dans la capture d’écran suivante:

    [![](remote-notifications-in-ios-images/image10new.png "Créer le certificat pour l’ID d’application")](remote-notifications-in-ios-images/image8.png#lightbox)

8. Indiquez si vous souhaitez utiliser un certificat de développement ou de production:

    [![](remote-notifications-in-ios-images/image11new.png "Sélectionner un certificat de développement ou de production")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Puis sélectionnez le nouvel ID d’application que nous venons de créer:

    [![](remote-notifications-in-ios-images/image12new.png "Sélectionnez le nouvel ID d’application que vous venez de créer")](remote-notifications-in-ios-images/image12new.png#lightbox)

10.  Cette opération affiche des instructions qui vous guideront tout au long du processus de création d’une *demande de signature de certificat* à l’aide de l’application Trousseau d' **accès** sur votre Mac.

11.  Maintenant que le certificat a été créé, il doit être utilisé dans le cadre du processus de génération pour signer l’application afin qu’il puisse s’inscrire auprès de APNs. Cela nécessite la création et l’installation d’un profil de provisionnement qui utilise le certificat.

12.  Pour créer un profil de provisionnement de développement, accédez à la section **profils** de provisionnement et suivez les étapes pour le créer, à l’aide de l’ID d’application que nous venons de créer.

13.  Une fois que vous avez créé le profil de provisionnement, ouvrez l' **organiseur Xcode** et actualisez-le. Si le profil de configuration que vous avez créé n’apparaît pas, il peut être nécessaire de télécharger le profil à partir du portail d’approvisionnement iOS et de l’importer manuellement. La capture d’écran suivante montre un exemple de l’organisateur avec le profil de mise en service ajouté:  
    [![](remote-notifications-in-ios-images/image13new.png "Cette capture d’écran montre un exemple de l’organisateur avec le profil de mise en service ajouté")](remote-notifications-in-ios-images/image13new.png#lightbox)

14.  À ce stade, nous devons configurer le projet Xamarin. iOS pour qu’il utilise ce profil de provisionnement nouvellement créé. Cette opération s’effectue à partir de la boîte de dialogue **Options du projet** , sous l’onglet signature de l' **offre groupée iOS** , comme illustré dans la capture d’écran suivante:  
    [![](remote-notifications-in-ios-images/image11.png "Configurer le projet Xamarin. iOS pour utiliser ce profil de provisionnement nouvellement créé")](remote-notifications-in-ios-images/image11.png#lightbox)

À ce stade, l’application est configurée pour fonctionner avec les notifications push. Toutefois, il existe encore quelques étapes supplémentaires requises avec le certificat. Ce certificat est au format DER qui n’est pas compatible avec PushSharp, ce qui nécessite un certificat PKCS12 (Personal Information Exchange). Pour convertir le certificat afin qu’il puisse être utilisé par PushSharp, procédez comme suit:

1.  **Téléchargez le fichier de certificat** : Connectez-vous au portail d’approvisionnement iOS, choisissez l’onglet certificats, sélectionnez le certificat associé au profil de provisionnement correct, puis choisissez **Télécharger** .
1.  **Ouvrir l’accès au trousseau** -il s’agit d’une interface GUI du système de gestion des mots de passe dans OS X.
1.  **Importer le certificat** : si le certificat n’est pas déjà installé, **fichier...** Importez des éléments à partir du menu Trousseau d’accès. Accédez au certificat qui a été exporté précédemment et sélectionnez-le.
1.  **Exporter le certificat** -développez le certificat afin que la clé privée associée soit visible, cliquez avec le bouton droit sur la clé et choisissez Exporter. Vous serez invité à entrer un nom de fichier et un mot de passe pour le fichier exporté.

À ce stade, nous avons terminé avec des certificats. Nous avons créé un certificat qui sera utilisé pour signer des applications iOS et convertir ce certificat dans un format qui peut être utilisé avec PushSharp dans une application serveur. Voyons ensuite comment les applications iOS interagissent avec APNS.

## <a name="registering-with-apns"></a>Inscription auprès de APNS

Pour qu’une application iOS puisse recevoir une notification à distance, elle doit s’inscrire auprès de APNS. APNS génère un jeton d’appareil unique et le retourne à l’application iOS. L’application iOS prend alors le jeton de l’appareil, puis s’inscrit auprès du serveur d’applications. Une fois cela se produit, l’inscription est terminée et le serveur d’applications peut envoyer des notifications push à l’appareil mobile.

En théorie, le jeton d’appareil peut changer chaque fois qu’une application iOS s’inscrit elle-même auprès de APNS. Toutefois, dans la pratique, cela ne se produit pas souvent. En tant qu’optimisation, une application peut mettre en cache le jeton d’appareil le plus récent et mettre à jour le serveur d’applications uniquement lorsqu’il change. Le diagramme suivant illustre le processus d’inscription et d’obtention d’un jeton d’appareil:

 ![](remote-notifications-in-ios-images/image12.png "Ce diagramme illustre le processus d’inscription et d’obtention d’un jeton d’appareil")

L’inscription avec APNs est gérée `FinishedLaunching` dans la méthode de la classe déléguée `RegisterForRemoteNotificationTypes` d’application en `UIApplication` appelant sur l’objet actuel. Quand une application iOS s’inscrit auprès de APNS, elle doit également spécifier les types de notifications distantes qu’elle souhaite recevoir. Ces types de notifications distants sont `UIRemoteNotificationType`déclarés dans l’énumération. L’extrait de code suivant est un exemple de la façon dont une application iOS peut s’inscrire pour recevoir des notifications d’alerte et de badge à distance:

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
    var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

    UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
    UIApplication.SharedApplication.RegisterForRemoteNotifications ();
} else {
    UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
    UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
}
```

La demande d’inscription APNs se produit en arrière-plan-lorsque la réponse est reçue, iOS appelle `RegisteredForRemoteNotifications` la méthode `AppDelegate` dans la classe et passe le jeton d’appareil inscrit. Le jeton sera contenu dans un `NSData` objet. L’extrait de code suivant montre comment récupérer le jeton de l’appareil fourni par APNS:

```csharp
public override void RegisteredForRemoteNotifications (
UIApplication application, NSData deviceToken)
{
    // Get current device token
    var DeviceToken = deviceToken.Description;
    if (!string.IsNullOrWhiteSpace(DeviceToken)) {
        DeviceToken = DeviceToken.Trim('<').Trim('>');
    }

    // Get previous device token
    var oldDeviceToken = NSUserDefaults.StandardUserDefaults.StringForKey("PushDeviceToken");

    // Has the token changed?
    if (string.IsNullOrEmpty(oldDeviceToken) || !oldDeviceToken.Equals(DeviceToken))
    {
        //TODO: Put your own logic here to notify your server that the device token has changed/been created!
    }

    // Save new device token
    NSUserDefaults.StandardUserDefaults.SetString(DeviceToken, "PushDeviceToken");
}
```

Si l’inscription échoue pour une raison quelconque (par exemple, si l’appareil n’est pas connecté à Internet), `FailedToRegisterForRemoteNotifications` iOS appellera sur la classe du délégué d’application. L’extrait de code suivant montre comment afficher une alerte à l’utilisateur pour l’informer que l’inscription a échoué:

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Nettoyage des jetons d’appareil

Les jetons de l’appareil expirent ou évoluent au fil du temps. En raison de ce fait, les applications serveur doivent nettoyer et purger ces jetons arrivés à expiration ou modifiés. Lorsqu’une application envoie une notification push à un appareil qui a un jeton expiré, APNS enregistre et enregistre ce jeton expiré. Les serveurs peuvent ensuite interroger APNS pour connaître les jetons arrivés à expiration.

APNS utilisé pour fournir un *service de commentaires* : un point de terminaison HTTPS qui s’authentifie via le certificat créé pour envoyer des notifications push et envoie des données sur les jetons arrivés à expiration. Il a été déconseillé par Apple et supprimé.

Au lieu de cela, il existe un nouveau code d’état HTTP pour le cas précédemment signalé par le service de Feedback:

> 410-le jeton de l’appareil n’est plus actif pour la rubrique.

En outre, une nouvelle `timestamp` clé de données JSON sera dans le corps de la réponse:

> Si la valeur de l’en-tête d’État est 410, la valeur de cette clé est la dernière fois que APNs a confirmé que le jeton de l’appareil n’était plus valide pour la rubrique.
>
> Arrêtez le push des notifications jusqu’à ce que l’appareil inscrive un jeton avec un horodateur ultérieur auprès de votre fournisseur.

## <a name="summary"></a>Récapitulatif

Cette section présente les concepts clés entourant les notifications push dans iOS. Il a expliqué le rôle du service Apple Push Notification Gateway (APNS). Il a ensuite abordé la création et l’utilisation des certificats de sécurité qui sont essentiels à APNS. Enfin, ce document s’est terminé par une discussion sur la façon dont les serveurs d’applications peuvent utiliser les *services de feedback* pour arrêter le suivi des jetons d’appareil arrivés à expiration.


## <a name="related-links"></a>Liens associés

- [Notifications-démonstration des notifications locales et distantes (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/notifications)
- [Notifications push et locales pour les développeurs](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
