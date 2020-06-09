---
title: Google Cloud Messaging
description: Google Cloud Messaging (GCM) est un service qui facilite la messagerie entre les applications mobiles et les applications serveur. Cet article fournit une vue d’ensemble du fonctionnement de GCM et explique comment configurer Google services afin que votre application puisse utiliser GCM.
ms.prod: xamarin
ms.assetid: DF8EF401-F63D-4BA0-B2C6-B22DF8FD60CB
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 05/02/2019
ms.openlocfilehash: d5231d57e84d57788f318c8ae04e592da57a0f5d
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84566639"
---
# <a name="google-cloud-messaging"></a>Google Cloud Messaging

> [!WARNING]
> Google a supprimé le GCM le 10 avril 2018. Les documents et exemples de projets suivants peuvent ne plus être gérés. Les API serveur et client GCM de Google seront supprimées dès le 29 mai 2019. Google recommande de migrer des applications GCM vers Firebase Cloud Messaging (FCM). Pour plus d’informations sur la désapprobation et la migration de GCM, voir [Google Deprecated Cloud Messaging](https://developers.google.com/cloud-messaging/).
>
> Pour commencer à utiliser Firebase Cloud Messaging avec Xamarin, consultez [Firebase Cloud Messaging](firebase-cloud-messaging.md).

_Google Cloud Messaging (GCM) est un service qui facilite la messagerie entre les applications mobiles et les applications serveur. Cet article fournit une vue d’ensemble du fonctionnement de GCM et explique comment configurer Google services afin que votre application puisse utiliser GCM._

[![Logo Google Cloud Messaging](google-cloud-messaging-images/preview-sml.png)](google-cloud-messaging-images/preview.png#lightbox)

Cette rubrique fournit une vue d’ensemble de haut niveau de la façon dont Google Cloud Messaging achemine les messages entre votre application et un serveur d’applications, et fournit une procédure pas-à-pas pour acquérir des informations d’identification afin que votre application puisse utiliser les services GCM.

## <a name="overview"></a>Vue d’ensemble

Google Cloud Messaging (GCM) est un service qui gère l’envoi, le routage et la mise en file d’attente des messages entre les applications serveur et les applications clientes mobiles. Une *application cliente* est une application GCM qui s’exécute sur un appareil. Le *serveur d’applications* (fourni par vous ou votre société) est le serveur GCM avec lequel votre application cliente communique via GCM :

[![GCM réside entre l’application cliente et le serveur d’applications](google-cloud-messaging-images/01-server-gcm-app-sml.png)](google-cloud-messaging-images/01-server-gcm-app.png#lightbox)

À l’aide de GCM, les serveurs d’applications peuvent envoyer des messages à un seul appareil, à un groupe d’appareils ou à un certain nombre d’appareils abonnés à une rubrique. Votre application cliente peut utiliser GCM pour s’abonner à des messages en aval à partir d’un serveur d’applications (par exemple, pour recevoir des notifications distantes). En outre, GCM permet aux applications clientes d’envoyer des messages en amont au serveur d’applications.

## <a name="google-cloud-messaging-in-action"></a>Google Cloud Messaging en action

Lorsque des messages en aval sont envoyés à partir d’un serveur d’applications vers une application cliente, le serveur d’applications envoie le message à un *serveur de connexion GCM*. le serveur de connexion GCM transmet ensuite le message à un appareil qui exécute votre application cliente. Les messages peuvent être envoyés via HTTP ou [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) (messagerie extensible et protocole de présence). Étant donné que les applications clientes ne sont pas toujours connectées ou en cours d’exécution, le serveur de connexion GCM met en file d’attente et stocke les messages, en les envoyant aux applications clientes lorsqu’ils se reconnectent et deviennent disponibles. De même, GCM met en file d’attente les messages en amont de l’application cliente sur le serveur d’applications si le serveur d’applications n’est pas disponible.

GCM utilise les informations d’identification suivantes pour identifier le serveur d’applications et votre application cliente, et il utilise ces informations d’identification pour autoriser les transactions de message via GCM :

- **Clé API** &ndash; La *clé API* permet à votre serveur d’applications d’accéder à Google services. GCM utilise cette clé pour authentifier votre serveur d’applications.
    Avant de pouvoir utiliser le service GCM, vous devez d’abord obtenir une clé API à partir de la [console de développement Google](https://console.developers.google.com/) en créant un *projet*. La clé de l’API doit rester sécurisée ; Pour plus d’informations sur la protection de votre clé API, consultez [meilleures pratiques pour l’utilisation sécurisée de clés API](https://support.google.com/cloud/answer/6310037?hl=en).

- ID de l' **expéditeur** &ndash; L' *ID* de l’expéditeur autorise le serveur d’applications à votre application cliente &ndash; . il s’agit d’un numéro unique qui identifie le serveur d’applications autorisé à envoyer des messages à votre application cliente.
    L’ID de l’expéditeur est également votre numéro de projet. vous obtenez l’ID de l’expéditeur à partir de la console des développeurs Google quand vous inscrivez votre projet.

- **Jeton** &ndash; d’inscription Le *jeton d’inscription* est l’identité GCM de votre application cliente sur un appareil donné. Le jeton d’inscription est généré au moment de l’exécution &ndash; , votre application reçoit un jeton d’inscription quand elle s’inscrit pour la première fois auprès de GCM lors de son exécution sur un appareil. Le jeton d’inscription autorise une instance de votre application cliente (s’exécutant sur cet appareil particulier) à recevoir des messages de GCM.

- ID de l' **application** &ndash; Identité de votre application cliente (indépendante d’un appareil donné) qui s’inscrit pour recevoir des messages de GCM. Sur Android, l’ID de l’application est le nom du package enregistré dans **fichier AndroidManifest. xml**, par exemple `com.xamarin.gcmexample` .

La [configuration Google Cloud Messaging](#settingup) (plus loin dans ce guide) fournit des instructions détaillées pour la création d’un projet et la génération de ces informations d’identification.

Les sections suivantes expliquent comment ces informations d’identification sont utilisées lorsque les applications clientes communiquent avec les serveurs d’applications par le biais de GCM.

### <a name="registration-with-gcm"></a>Inscription avec GCM

Une application cliente installée sur un appareil doit d’abord s’inscrire auprès de GCM pour que la messagerie puisse avoir lieu. L’application cliente doit effectuer les étapes d’inscription présentées dans le diagramme suivant :

[![Étapes d’inscription de l’application](google-cloud-messaging-images/02-app-registration-sml.png)](google-cloud-messaging-images/02-app-registration.png#lightbox)

1. L’application cliente contacte GCM pour obtenir un jeton d’inscription, en transmettant l’ID de l’expéditeur à GCM.

2. GCM retourne un jeton d’inscription à l’application cliente.

3. L’application cliente transfère le jeton d’inscription au serveur d’applications.

Le serveur d’applications met en cache le jeton d’inscription pour les communications ultérieures avec l’application cliente. Le cas échéant, le serveur d’applications peut renvoyer un accusé de réception à l’application cliente pour indiquer que le jeton d’inscription a été reçu. Une fois cette négociation effectuée, l’application cliente peut recevoir des messages de (ou envoyer des messages à) au serveur d’applications.

Lorsque l’application cliente ne souhaite plus recevoir de messages du serveur d’applications, elle peut envoyer une demande au serveur d’applications pour supprimer le jeton d’inscription. Si l’application cliente reçoit des messages de rubrique (expliqués plus loin dans cet article), elle peut se désabonner de la rubrique.
Si l’application cliente est désinstallée d’un appareil, GCM la détecte et avertit automatiquement le serveur d’applications de la suppression du jeton d’inscription.

### <a name="downstream-messaging"></a>Messagerie en aval

Lorsque le serveur d’applications envoie un message en aval à l’application cliente, il suit les étapes illustrées dans le diagramme suivant :

[![Diagramme de stockage et de redirection de messagerie en aval](google-cloud-messaging-images/03-downstream-sml.png)](google-cloud-messaging-images/03-downstream.png#lightbox)

1. Le serveur d’applications envoie le message à GCM.

2. Si l’appareil client n’est pas disponible, le serveur GCM stocke le message dans une file d’attente en vue d’une transmission ultérieure.

3. Lorsque le périphérique client est disponible, GCM envoie le message à l’application cliente sur cet appareil.

4. L’application cliente reçoit le message de GCM et la gère en conséquence. Par exemple, si le message est une notification distante, il est présenté à l’utilisateur.

Dans ce scénario de messagerie (où le serveur d’applications envoie un message à une seule application cliente), les messages peuvent avoir une longueur maximale de 4 Ko.

Pour obtenir des informations détaillées (y compris des exemples de code) sur la réception de messages GCM en aval sur Android, consultez [notifications à distance](~/android/data-cloud/google-messaging/remote-notifications-with-gcm.md).

#### <a name="topic-messaging"></a>Messagerie de rubrique

La *messagerie de rubrique* est un type de messagerie en aval dans lequel le serveur d’applications envoie un message unique à plusieurs périphériques d’application cliente qui s’abonnent à une rubrique (par exemple, une prévision météorologique). Les messages de rubrique peuvent avoir une longueur de 2 Ko, et la messagerie des rubriques prend en charge jusqu’à 1 million abonnements par application. Si GCM est utilisé uniquement pour la messagerie de rubrique, il n’est pas nécessaire que l’application cliente envoie un jeton d’inscription au serveur d’applications.

#### <a name="group-messaging"></a>Messagerie de groupe

La *messagerie de groupe* est un type de messagerie en aval dans lequel le serveur d’applications envoie un message unique à plusieurs périphériques d’application cliente appartenant à un groupe (par exemple, un groupe d’appareils qui appartiennent à un seul utilisateur). Les messages de groupe peuvent avoir une longueur maximale de 2 Ko pour les appareils iOS et jusqu’à 4 Ko pour les appareils Android. Un groupe est limité à un maximum de 20 membres.

### <a name="upstream-messaging"></a>Messagerie en amont

Si votre application cliente se connecte à un serveur qui prend en charge [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref), elle peut renvoyer des messages au serveur d’applications, comme illustré dans le diagramme suivant :

[![Diagramme de messagerie en amont](google-cloud-messaging-images/04-upstream-sml.png)](google-cloud-messaging-images/04-upstream.png#lightbox)

1. L’application cliente envoie un message au serveur de connexion GCM XMPP.

2. Si le serveur d’applications est déconnecté, le serveur GCM stocke le message dans une file d’attente pour le transfert ultérieur.

3. Lorsque le serveur d’applications est de nouveau connecté, GCM transfère le message au serveur d’applications.

4. Le serveur d’applications analyse le message pour vérifier l’identité de l’application cliente, puis envoie un « ACK » à GCM pour accuser réception du message.

5. Le serveur d’applications traite le message.

[Les messages en amont](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref#upstream) de Google expliquent comment structurer les messages JSON et les envoyer aux serveurs d’applications qui exécutent le serveur de connexion Cloud basé sur XMPP de Google.

<a name="settingup"></a>

## <a name="setting-up-google-cloud-messaging"></a>Configuration de Google Cloud Messaging

Avant de pouvoir utiliser les services GCM dans votre application, vous devez d’abord obtenir les informations d’identification pour l’accès aux serveurs GCM de Google. Les sections suivantes décrivent les étapes requises pour effectuer ce processus :

### <a name="enable-google-services-for-your-app"></a>Activer les services Google pour votre application

1. Connectez-vous à la [console des développeurs Google](https://developers.google.com/mobile/add?platform=android) avec votre compte Google (par exemple, votre adresse Gmail) et créez un nouveau projet. Si vous disposez d’un projet existant, choisissez le projet que vous souhaitez rendre compatible GCM. Dans l’exemple suivant, un nouveau projet appelé **XamarinGCM** est créé :

    [![Création du projet XamarinGCM](google-cloud-messaging-images/05-create-gcm-app-sml.png)](google-cloud-messaging-images/05-create-gcm-app.png#lightbox)

2. Ensuite, entrez le nom du package pour votre application (dans cet exemple, le nom du package est **com. xamarin. gcmexample**), puis cliquez sur **continuer pour choisir et configurer les services**:

    [![Saisie du nom du package](google-cloud-messaging-images/06-package-name-sml.png)](google-cloud-messaging-images/06-package-name.png#lightbox)

    Notez que ce nom de package est également l’ID d’application de votre application.

3. La section **choisir et configurer des services** répertorie les services Google que vous pouvez ajouter à votre application. Cliquez sur **messagerie Cloud**:

    [![Choisir la messagerie Cloud](google-cloud-messaging-images/07-choose-gcm-service-sml.png)](google-cloud-messaging-images/07-choose-gcm-service.png#lightbox)

4. Ensuite, cliquez sur **Activer Google Cloud Messaging**:

    [![Activer Google Cloud Messaging](google-cloud-messaging-images/08-enable-gcm-sml.png)](google-cloud-messaging-images/08-enable-gcm.png#lightbox)

5. Une **clé d’API serveur** et un **ID d’expéditeur** sont générés pour votre application. Enregistrez ces valeurs et cliquez sur **Fermer**:

    [![Clé de l’API serveur et ID de l’expéditeur affichés](google-cloud-messaging-images/09-get-api-key-and-id-sml.png)](google-cloud-messaging-images/09-get-api-key-and-id.png#lightbox)

    Protéger la clé API &ndash; elle n’est pas destinée à une utilisation publique. Si la clé d’API est compromise, les serveurs non autorisés peuvent publier des messages dans les applications clientes.
    [Meilleures pratiques pour l’utilisation sécurisée de clés d’API](https://support.google.com/cloud/answer/6310037?hl=en) fournit des instructions utiles pour la protection de votre clé API.

### <a name="view-your-project-settings"></a>Afficher les paramètres de votre projet

Vous pouvez afficher les paramètres de votre projet à tout moment en vous connectant à la [console Google Cloud](https://console.cloud.google.com/) et en sélectionnant votre projet. Par exemple, vous pouvez afficher l' **ID** de l’expéditeur en sélectionnant votre projet dans le menu déroulant en haut de la page (dans cet exemple, le projet est appelé **XamarinGCM**). L’ID de l’expéditeur est le numéro du projet, comme indiqué dans cette capture d’écran (l’ID de l’expéditeur ici est **9349932736**) :

[![Affichage de l’ID de l’expéditeur](google-cloud-messaging-images/10-view-server-id-sml.png)](google-cloud-messaging-images/10-view-server-id.png#lightbox)

Pour afficher la **clé API**, cliquez sur **Gestionnaire d’API** , puis sur **informations d’identification**:

[![Affichage de la clé API](google-cloud-messaging-images/11-view-credentials-sml.png)](google-cloud-messaging-images/11-view-credentials.png#lightbox)

## <a name="for-further-reading"></a>Pour obtenir des informations supplémentaires

- Les [rfc 6120](https://tools.ietf.org/html/rfc6120) et [RFC 6121](https://tools.ietf.org/html/rfc6121) expliquent et définissent le protocole de messagerie et de présence extensible (XMPP).

## <a name="summary"></a>Résumé

Cet article a fourni une vue d’ensemble de Google Cloud Messaging (GCM). Il a expliqué les différentes informations d’identification utilisées pour identifier et autoriser la messagerie entre les serveurs d’applications et les applications clientes. Il illustre les scénarios de messagerie les plus courants et décrit les étapes d’inscription de votre application avec GCM pour l’utilisation des services GCM.

## <a name="related-links"></a>Liens connexes

- [Messagerie Cloud](https://developers.google.com/cloud-messaging/)
