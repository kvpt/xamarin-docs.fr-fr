---
title: Notifications push dans iOS
description: Ce document décrit comment utiliser des notifications push dans iOS 9 et versions antérieures. Il présente les certificats, l’inscription auprès de l’Apple Push Notifications passerelle de Service (APNS) et bien plus encore.
ms.prod: xamarin
ms.assetid: 64B3BE6A-A3E2-4B1B-95ED-02D27A8FDAAC
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/18/2017
ms.openlocfilehash: f6d059e5a30e7e3dac92a2c4e0e6079222e66b22
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865202"
---
# <a name="push-notifications-in-ios"></a>Notifications push dans iOS

> [!IMPORTANT]
> Les informations contenues dans cette section se rapporte à iOS 9 et antérieures, il a été laissé ici pour prendre en charge les anciennes versions d’iOS. Pour iOS 10 et versions ultérieures, consultez le [guide de l’infrastructure de Notification utilisateur](~/ios/platform/user-notifications/index.md) pour prendre en charge locale et une Notification à distance sur un appareil iOS.

Notifications push doivent rester brefs et ne contient suffisamment de données pour notifier l’application mobile qu’il doit contacter l’application serveur pour une mise à jour. Par exemple, e-mail, l’application serveur serait uniquement avertir l’application mobile de l’arrivée de nouveaux messages électroniques. La notification ne contiendrait pas le nouvel e-mail lui-même. L’application mobile serait ensuite récupérer les nouveaux e-mails du serveur lorsqu’il était approprié

Dans le centre de notification push notifications dans iOS est le *Service passerelle Apple Push Notification (APNS)* . Il s’agit d’un service fourni par Apple qui est responsable de routage des notifications à partir d’un serveur d’applications sur des appareils iOS.
L’image suivante illustre la topologie de notification push pour iOS : ![](remote-notifications-in-ios-images/image4.png "Cette image illustre la topologie de notification push pour iOS")

Notifications à distance eux-mêmes sont des chaînes qui respectent le format au format JSON et protocoles spécifié dans [la charge utile de Notification](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/CreatingtheNotificationPayload.html#//apple_ref/doc/uid/TP40008194-CH10-SW1) section de la [Guide locales et Push Notification programmation](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/)dans les [documentation du développeur iOS](https://developer.apple.com/devcenter/ios/index.action).

Apple gère deux environnements d’APNS : un *bac à sable* et un *Production* environnement. L’environnement de bac à sable est destiné à tester pendant la phase de développement et trouverez `gateway.sandbox.push.apple.com` sur le port TCP 2195. L’environnement de Production est destiné à être utilisé dans les applications qui ont été déployées et peuvent être à `gateway.push.apple.com` sur le port TCP 2195.

## <a name="requirements"></a>Configuration requise

Notification push doit respecter les règles suivantes qui sont dictés par l’architecture de APNS :

-  **256 octets de Message limite** -la taille de l’intégralité du message de la notification ne doit pas dépasser 256 octets.
-  **Aucune Confirmation de réception** -APNS ne fournit pas de l’expéditeur avec une notification d’un message rendait au destinataire prévu. Si l’appareil est inaccessible et plusieurs notifications séquentielles sont envoyées, toutes les notifications, sauf les plus récentes seront perdues. Uniquement la dernière notification sera remise à l’appareil.
-  **Chaque application requiert un certificat sécurisé** -Communication avec APNS doit être effectuée via le protocole SSL.


## <a name="creating-and-using-certificates"></a>Création et l’utilisation de certificats

Chacun des environnements mentionnés dans la section précédente nécessitent leur propre certificat. Cette section décrit comment créer un certificat, associez-le à un profil de provisionnement et obtenir un certificat d’échange d’informations personnelles pour une utilisation avec PushSharp.

1.  Pour créer un certificat, accédez à l’iOS portail de provisionnement sur le site Web d’Apple, comme illustré dans la capture d’écran suivante (Notez que l’élément de menu d’ID d’application sur la gauche) :

    [![](remote-notifications-in-ios-images/image5new.png "Le portail de provisionnement iOS sur le site Web de pommes")](remote-notifications-in-ios-images/image5new.png#lightbox)

2.  Ensuite, accédez à la section de l’ID d’application et créer un nouvel ID d’application comme illustré dans la capture d’écran suivante :

    [![](remote-notifications-in-ios-images/image6new.png "Accédez à la section de l’ID d’application et créer un nouvel ID d’application")](remote-notifications-in-ios-images/image6new.png#lightbox)

3.  Lorsque vous cliquez sur le **+** bouton, vous serez en mesure d’entrer la description et un identificateur de Bundle pour l’ID d’application, comme indiqué dans la capture d’écran suivante :

    [![](remote-notifications-in-ios-images/image7new.png "Entrez la description et un identificateur de Bundle pour l’ID d’application")](remote-notifications-in-ios-images/image7new.png#lightbox)

4. Veillez à sélectionner **ID d’application explicite** et qui ne s’arrête pas l’identificateur de Bundle avec un `*` . Cette opération crée un identificateur qui est bon pour plusieurs applications, et les certificats de notification push doivent être pour une seule application.

5. Sous App Services, sélectionnez **Notifications Push**:

    [![](remote-notifications-in-ios-images/image8new.png "Sélectionnez les Notifications Push")](remote-notifications-in-ios-images/image8new.png#lightbox)

6. Appuyez sur **Submit** pour confirmer l’inscription de l’ID d’application :

    [![](remote-notifications-in-ios-images/image9new.png "Confirmer l’inscription de l’ID d’application")](remote-notifications-in-ios-images/image9new.png#lightbox)

7.  Ensuite, vous devez créer le certificat pour l’ID d’application. Dans le volet de navigation gauche, accédez à **certificats > tous les** et sélectionnez le `+` bouton, comme illustré dans la capture d’écran suivante :

    [![](remote-notifications-in-ios-images/image10new.png "Créer le certificat pour l’ID d’application")](remote-notifications-in-ios-images/image8.png#lightbox)

8. Sélectionnez si vous souhaitez utiliser un certificat de développement ou de Production :

    [![](remote-notifications-in-ios-images/image11new.png "Sélectionnez un certificat de développement ou de Production")](remote-notifications-in-ios-images/image11new.png#lightbox)

9. Puis sélectionnez le nouvel ID d’application que nous venons de créer :

    [![](remote-notifications-in-ios-images/image12new.png "Sélectionnez le nouvel ID d’application venez de créer")](remote-notifications-in-ios-images/image12new.png#lightbox)

10.  Cela affiche des instructions qui vous guideront tout au long du processus de création d’un *Certificate Signing Request* à l’aide de la **trousseau d’accès** application sur votre Mac.

11.  Maintenant que le certificat a été créé, il doit être utilisé pour signer l’application afin qu’elle peut s’inscrire auprès d’APNs dans le cadre du processus de génération. Cela nécessite la création et l’installation d’un profil d’approvisionnement qui utilise le certificat.

12.  Pour créer un profil de provisionnement de développement, accédez à la **profils de provisionnement** section et suivez les étapes pour créer, à l’aide de l’Id d’application que nous venons de créer.

13.  Une fois que vous avez créé le profil de configuration, ouvrez **Xcode Organizer** et l’actualiser. Si le profil de configuration que vous avez créé n’apparaît pas qu’il peut être nécessaire de télécharger le profil à partir du portail de provisionnement iOS et l’importer manuellement. La capture d’écran suivante montre un exemple de la bibliothèque multimédia avec le profil d’approvisionnement ajouté :  
    [![](remote-notifications-in-ios-images/image13new.png "Cette capture d’écran montre un exemple de la bibliothèque multimédia avec le profil d’approvisionnement ajouté")](remote-notifications-in-ios-images/image13new.png#lightbox)

14.  À ce stade, nous devons configurer le projet Xamarin.iOS pour utiliser ce nouveau profil de provisionnement. Cette opération est effectuée à partir de **Options du projet** boîte de dialogue, sous **signature du Bundle iOS** onglet, comme dans la capture d’écran suivante :  
    [![](remote-notifications-in-ios-images/image11.png "Configurer le projet Xamarin.iOS pour utiliser ce nouveau profil de provisionnement")](remote-notifications-in-ios-images/image11.png#lightbox)

À ce stade, l’application est configurée pour fonctionner avec des notifications push. Toutefois, il existe quelques étapes supplémentaires nécessaires avec le certificat. Ce certificat est au format DER qui n’est pas compatible avec PushSharp, ce qui nécessite un certificat d’échange d’informations personnelles (PKCS12). Pour convertir le certificat afin qu’il soit utilisable par PushSharp, effectuez ces étapes finales :

1.  **Télécharger le fichier de certificat** -connexion pour le portail de provisionnement iOS, choisissez l’onglet certificats, sélectionnez le certificat associé avec le profil de provisionnement approprié et choisissez **télécharger** .
1.  **Ouvrir l’accès au trousseau** -il s’agit d’application est une interface utilisateur graphique pour le système de gestion de mot de passe dans OS X.
1.  **Importez le certificat** : si le certificat n’est pas déjà installé, **fichier... Importer des éléments** dans le menu de trousseau d’accès. Accédez au certificat exporté ci-dessus et sélectionnez-le.
1.  **Exporter le certificat** : développez le certificat pour que la clé privée associée soit visible, avec le bouton droit sur la clé et a choisi d’exportation. Vous demandera un nom de fichier et un mot de passe pour le fichier exporté.

À ce stade, nous avons terminé avec des certificats. Nous avons créé un certificat qui sera utilisé pour signer des applications iOS et converti ce certificat dans un format qui peut être utilisé avec PushSharp dans une application serveur. Suivant nous allons voir comment les applications iOS interagissent avec APNS.

## <a name="registering-with-apns"></a>L’inscription auprès d’APNS

Avant un iOS application peut recevoir une notification à distance que doit s’inscrire avec APNS. APNS se générer un jeton d’appareil unique et que l’application iOS. L’application iOS prend ensuite le jeton d’appareil et ensuite de s’inscrire avec le serveur d’applications. Une fois tout cela se produit, l’inscription est terminée, et le serveur d’applications peut transmettre des notifications à l’appareil mobile.

En théorie, le jeton d’appareil peut changer chaque fois qu’une application iOS s’inscrit lui-même auprès d’APNS, toutefois, dans la pratique cela n’arrive pas qui souvent. En guise d’optimisation, une application peut mettre en cache le jeton d’appareil plus récente et uniquement mettre à jour le serveur d’applications lorsqu’il change. Le diagramme suivant illustre le processus d’inscription et obtenir un jeton d’appareil :

 ![](remote-notifications-in-ios-images/image12.png "Ce diagramme illustre le processus d’inscription et obtenir un jeton d’appareil")

Inscription auprès d’APNS est gérée dans le `FinishedLaunching` méthode de la classe de délégué d’application en appelant `RegisterForRemoteNotificationTypes` sur actuel `UIApplication` objet. Lorsqu’une application iOS s’inscrit auprès d’APNS, il doit également spécifier les types de notifications à distance il aimerait recevoir. Ces types de notification à distance sont déclarées dans l’énumération `UIRemoteNotificationType`. L’extrait de code suivant est un exemple de la manière dont une application iOS peut inscrire pour recevoir des notifications d’alerte et des badges à distance :

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

La demande d’inscription APNS se produit en arrière-plan - lors de la réponse est reçue, iOS appellera la méthode `RegisteredForRemoteNotifications` dans la `AppDelegate` classe et passer le jeton de périphérique enregistré. Le jeton se trouvera dans un `NSData` objet. L’extrait de code suivant montre comment récupérer le jeton d’appareil que APNS fourni :

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

Si l’inscription échoue pour une raison quelconque (tel que l’appareil est non connecté à Internet), iOS appelle `FailedToRegisterForRemoteNotifications` sur l’application de la classe déléguée. L’extrait de code suivant montre comment afficher une alerte à l’utilisateur pour l’informer que l’inscription a échoué :

```csharp
public override void FailedToRegisterForRemoteNotifications (UIApplication application , NSError error)
{
    new UIAlertView("Error registering push notifications", error.LocalizedDescription, null, "OK", null).Show();
}
```

### <a name="device-token-housekeeping"></a>Ménage de jeton de périphérique

Jetons d’appareil seront d’expirer ou de changer au fil du temps. Par conséquent, les applications serveur devront faire un nettoyage de la maison et vider ces jetons expirés ou modifiées. Lorsqu’une application envoie en tant que notification push à un périphérique qui a un jeton expiré, APNS enregistre et enregistrez ce jeton a expiré. Serveurs peuvent ensuite interroger APNS pour savoir quels jetons ont expiré.

APNS permet de fournir un *commentaires Service* -point de terminaison HTTPS qui authentifie via le certificat qui a été créé pour renvoyer les push notifications et envoie les données sur les jetons ont expiré. Cela a été déconseillé par Apple et supprimé.

Au lieu de cela, il existe un nouveau code d’état HTTP pour le cas a été signalé précédemment par le Service de commentaires :

> 410 - le jeton d’appareil n’est plus actif de la rubrique.

En outre, un nouveau `timestamp` clé des données JSON sera dans le corps de réponse :

> Si la valeur dans le : en-tête de l’état est 410, la valeur de cette clé est la dernière heure à laquelle APNs a confirmé que le jeton d’appareil était n’est plus valide pour la rubrique.
>
> Arrêtez d’envoyer des notifications jusqu'à ce que l’appareil inscrit un jeton avec une horodatage la plus récente avec votre fournisseur.

## <a name="summary"></a>Récapitulatif

Cette section présente les concepts de clés qui entourent les notifications push dans iOS. Vous avez appris le rôle de l’Apple Push Notification passerelle de Service (APNS). Ensuite, elle couvrait la création et l’utilisation des certificats de sécurité qui sont essentielles à APNS. Enfin ce document terminé avec une discussion sur comment les serveurs d’applications peuvent utiliser le *commentaires Services* à arrêter le suivi des jetons de périphérique expirés.


## <a name="related-links"></a>Liens associés

- [Notifications - démonstration des notifications locales et distantes (exemple)](https://developer.xamarin.com/samples/monotouch/Notifications/)
- [Local et Notifications Push pour les développeurs](https://developer.apple.com/notifications/)
- [UIApplication](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIApplication)
- [UIRemoteNotificationType](http://iosapi.xamarin.com/?link=T%3aMonoTouch.UIKit.UIRemoteNotificationType)
