---
title: Firebase Cloud Messaging
description: Firebase Cloud Messaging (FCM) est un service qui facilite la messagerie entre les applications mobiles et les applications serveur. Cet article fournit une vue d’ensemble du fonctionnement de FCM et explique comment configurer Google services afin que votre application puisse utiliser FCM.
ms.prod: xamarin
ms.assetid: E5314D7F-2AAC-40DA-BEBA-27C834F078DD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/31/2018
ms.openlocfilehash: acb0e9ea23d4043a72b9d2688501c576a3d43358
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570192"
---
# <a name="firebase-cloud-messaging"></a>Firebase Cloud Messaging

_Firebase Cloud Messaging (FCM) est un service qui facilite la messagerie entre les applications mobiles et les applications serveur. Cet article fournit une vue d’ensemble du fonctionnement de FCM et explique comment configurer Google services afin que votre application puisse utiliser FCM._

[![Image du héros Firebase Cloud Messaging](firebase-cloud-messaging-images/preview.png)](firebase-cloud-messaging-images/preview.png#lightbox)

Cette rubrique fournit une vue d’ensemble de haut niveau de la façon dont Firebase Cloud Messaging achemine les messages entre votre application Xamarin. Android et un serveur d’applications, et fournit une procédure pas-à-pas pour acquérir des informations d’identification afin que votre application puisse utiliser les services FCM.

## <a name="overview"></a>Vue d’ensemble

Firebase Cloud Messaging (FCM) est un service multiplateforme qui gère l’envoi, le routage et la mise en file d’attente des messages entre les applications serveur et les applications clientes mobiles. FCM est le successeur de Google Cloud Messaging (GCM) et repose sur Google Play Services.

Comme illustré dans le diagramme suivant, FCM agit comme un intermédiaire entre les expéditeurs et les clients des messages. Une *application cliente* est une application FCM qui s’exécute sur un appareil. Le *serveur d’applications* (fourni par vous ou votre société) est le serveur FCM avec lequel votre application cliente communique via FCM. Contrairement à GCM, FCM vous permet d’envoyer des messages aux applications clientes directement via l’interface graphique utilisateur des notifications de la console Firebase :

[![FCM se situe entre l’application cliente et un serveur d’applications](firebase-cloud-messaging-images/01-server-fcm-app-sml.png)](firebase-cloud-messaging-images/01-server-fcm-app.png#lightbox)

À l’aide de FCM, les serveurs d’applications peuvent envoyer des messages à un seul appareil, à un groupe d’appareils ou à un certain nombre d’appareils abonnés à une rubrique. Une application cliente peut utiliser FCM pour s’abonner à des messages en aval à partir d’un serveur d’applications (par exemple, pour recevoir des notifications distantes). Pour plus d’informations sur les différents types de messages Firebase, consultez [à propos des messages FCM](https://firebase.google.com/docs/cloud-messaging/concept-options).

## <a name="firebase-cloud-messaging-in-action"></a><a name="fcm-in-action"></a>Firebase Cloud Messaging en action

Lorsqu’un message en aval est envoyé à une application cliente à partir d’un serveur d’applications, le serveur d’applications envoie le message à un *serveur de connexion FCM* fourni par Google. le serveur de connexion FCM, à son tour, transmet le message à un appareil qui exécute l’application cliente. Les messages peuvent être envoyés via HTTP ou [XMPP](https://firebase.google.com/docs/cloud-messaging/xmpp-server-ref) (messagerie extensible et protocole de présence). Étant donné que les applications clientes ne sont pas toujours connectées ou en cours d’exécution, le serveur de connexion FCM met en file d’attente et stocke les messages, en les envoyant aux applications clientes lorsqu’ils se reconnectent et deviennent disponibles. De même, FCM met en file d’attente les messages en amont de l’application cliente sur le serveur d’applications si le serveur d’applications n’est pas disponible. Pour plus d’informations sur les serveurs de connexion FCM, consultez [à propos de Firebase Cloud Messaging Server](https://firebase.google.com/docs/cloud-messaging/server).

FCM utilise les informations d’identification suivantes pour identifier le serveur d’applications et l’application cliente, et utilise ces informations d’identification pour autoriser les transactions de message via FCM :

- <a name="fcm-in-action-sender-id"></a>ID de l' **expéditeur** &ndash; L' *ID* de l’expéditeur est une valeur numérique unique qui est assignée lorsque vous créez votre projet Firebase. L’ID de l’expéditeur est utilisé pour identifier chaque serveur d’applications qui peut envoyer des messages à l’application cliente. L’ID de l’expéditeur est également votre numéro de projet. vous obtenez l’ID de l’expéditeur à partir de la console Firebase quand vous inscrivez votre projet. Un exemple d’ID d’expéditeur est `496915549731` .

- <a name="fcm-in-action-api-key"></a>**Clé API** &ndash; La *clé API* permet au serveur d’applications d’accéder aux services Firebase. FCM utilise cette clé pour authentifier le serveur d’applications. Cette information d’identification est également appelée *clé de serveur* ou *clé d’API Web*. Exemple de clé API `AJzbSyCTcpfRT1YRqbz-jIwp1h06YdauvewGDzk` .

- <a name="fcm-in-action-app-id"></a>**ID** &ndash; d’application Identité de votre application cliente (indépendante d’un appareil donné) qui s’inscrit pour recevoir des messages de FCM. Un exemple d’ID d’application est `1:415712510732:android:0e1eb7a661af2460` .

- <a name="fcm-in-action-registration-token"></a>**Jeton** &ndash; d’inscription Le *jeton d’inscription* (également appelé ID d' *instance*) est l’identité FCM de votre application cliente sur un appareil donné. Le jeton d’inscription est généré au moment de l’exécution &ndash; que votre application reçoit un jeton d’inscription quand elle s’inscrit pour la première fois auprès de FCM lorsqu’elle est exécutée sur un appareil. Le jeton d’inscription autorise une instance de votre application cliente (s’exécutant sur cet appareil particulier) à recevoir des messages de FCM.
    Un exemple de jeton d’inscription est `fkBQTHxKKhs:AP91bHuEedxM4xFAUn0z ... JKZS` (une chaîne très longue).

La [configuration de Firebase Cloud Messaging](#setup_fcm) (plus loin dans ce guide) fournit des instructions détaillées pour la création d’un projet et la génération de ces informations d’identification. Lorsque vous créez un projet dans la [console Firebase](https://console.firebase.google.com/), un fichier d’informations d’identification appelé **Google-services. JSON** est créé. &ndash; Ajoutez ce fichier à votre projet Xamarin. Android, comme expliqué dans [notifications à distance avec FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

Les sections suivantes expliquent comment ces informations d’identification sont utilisées lorsque les applications clientes communiquent avec les serveurs d’applications par le biais de FCM.

<a name="registration"></a>

### <a name="registration-with-fcm"></a>Inscription avec FCM

Une application cliente doit d’abord s’inscrire auprès de FCM pour que la messagerie puisse avoir lieu. L’application cliente doit effectuer les étapes d’inscription présentées dans le diagramme suivant :

[![Diagramme des étapes d’inscription de l’application](firebase-cloud-messaging-images/02-app-registration-sml.png)](firebase-cloud-messaging-images/02-app-registration.png#lightbox)

1. L’application cliente contacte FCM pour obtenir un jeton d’inscription, en transmettant l’ID de l’expéditeur, la clé de l’API et l’ID de l’application à FCM.

2. FCM retourne un jeton d’inscription à l’application cliente.

3. L’application cliente (facultatif) transfère le jeton d’inscription au serveur d’applications.

Le serveur d’applications met en cache le jeton d’inscription pour les communications ultérieures avec l’application cliente. Le serveur d’applications peut renvoyer un accusé de réception à l’application cliente pour indiquer que le jeton d’inscription a été reçu. Une fois cette négociation effectuée, l’application cliente peut recevoir des messages de (ou envoyer des messages à) au serveur d’applications. L’application cliente peut recevoir un nouveau jeton d’inscription si l’ancien jeton est compromis (voir [notifications distantes avec FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md) pour obtenir un exemple de la façon dont une application reçoit les mises à jour de jeton d’inscription).

Lorsque l’application cliente ne souhaite plus recevoir de messages du serveur d’applications, elle peut envoyer une demande au serveur d’applications pour supprimer le jeton d’inscription. Si l’application cliente est désinstallée d’un appareil, FCM le détecte et informe automatiquement le serveur d’applications de la suppression du jeton d’inscription.

### <a name="downstream-messaging"></a>Messagerie en aval

Le diagramme suivant illustre la façon dont Firebase Cloud Messaging stocke et transfère les messages en aval :

[![FCM utilise le stockage et le transfert pour la messagerie en aval](firebase-cloud-messaging-images/03-downstream-sml.png)](firebase-cloud-messaging-images/03-downstream.png#lightbox)

Lorsque le serveur d’applications envoie un message en aval à l’application cliente, il utilise les étapes suivantes comme énuméré dans le diagramme ci-dessus :

1. Le serveur d’applications envoie le message à FCM.

2. Si l’appareil client n’est pas disponible, le serveur FCM stocke le message dans une file d’attente pour une transmission ultérieure. Les messages sont conservés dans le stockage FCM pendant un maximum de 4 semaines (pour plus d’informations, consultez [définition de la durée de vie d’un message](https://firebase.google.com/docs/cloud-messaging/concept-options#ttl)).

3. Lorsque l’appareil client est disponible, FCM transfère le message à l’application cliente sur cet appareil.

4. L’application cliente reçoit le message de FCM, le traite et l’affiche à l’utilisateur. Par exemple, si le message est une notification distante, il est présenté à l’utilisateur dans la zone de notification.

Dans ce scénario de messagerie (où le serveur d’applications envoie un message à une seule application cliente), les messages peuvent avoir une longueur maximale de 4 Ko.

Pour plus d’informations sur la réception des messages FCM en aval sur Android, consultez [notifications distantes avec FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

### <a name="topic-messaging"></a>Messagerie de rubrique

La *messagerie de rubrique* permet à un serveur d’applications d’envoyer un message à plusieurs appareils qui ont opté pour une rubrique particulière. Vous pouvez également composer et envoyer des messages de rubrique par le biais de l’interface graphique utilisateur des notifications de la console Firebase. FCM gère le routage et la remise des messages de rubrique aux clients abonnés. Cette fonctionnalité peut être utilisée pour les messages tels que les alertes météo, les cotations boursières et les actualités.

[![Diagramme de messagerie de rubrique](firebase-cloud-messaging-images/04-topic-messaging-sml.png)](firebase-cloud-messaging-images/04-topic-messaging.png#lightbox)

Les étapes suivantes sont utilisées dans la messagerie de rubrique (après que l’application cliente a obtenu un jeton d’inscription, comme expliqué précédemment) :

1. L’application cliente s’abonne à une rubrique en envoyant un message d’abonnement à FCM.

2. Le serveur d’applications envoie des messages de rubrique à FCM pour la distribution.

3. FCM transfère les messages de rubrique aux clients qui se sont abonnés à cette rubrique.

Pour plus d’informations sur la messagerie de rubrique Firebase, consultez la [rubrique messagerie de Google sur Android](https://firebase.google.com/docs/cloud-messaging/android/topic-messaging).

<a name="setup_fcm"></a>

## <a name="setting-up-firebase-cloud-messaging"></a>Configuration de la messagerie Firebase Cloud

Avant de pouvoir utiliser les services FCM dans votre application, vous devez créer un nouveau projet (ou importer un projet existant) via la [console Firebase](https://console.firebase.google.com/). Pour créer un projet Firebase Cloud Messaging pour votre application, procédez comme suit :

1. Connectez-vous à la [console Firebase](https://console.firebase.google.com/) avec votre compte Google (par exemple, votre adresse Gmail), puis cliquez sur **créer un projet**:

    [![Bouton créer un projet](firebase-cloud-messaging-images/05-firebase-console-sml.png)](firebase-cloud-messaging-images/05-firebase-console.png#lightbox)

    Si vous disposez d’un projet existant, cliquez sur **Importer un projet Google**.

2. Dans la boîte de dialogue **créer un projet** , entrez le nom de votre projet et cliquez sur **créer un projet**. Dans l’exemple suivant, un nouveau projet appelé **XamarinFCM** est créé :

    [![Boîte de dialogue créer un projet](firebase-cloud-messaging-images/06-create-a-project-sml.png)](firebase-cloud-messaging-images/06-create-a-project.png#lightbox)

3. Dans la **vue d’ensemble**de la console Firebase, cliquez sur **Ajouter Firebase à votre application Android**:

    [![Ajouter Firebase à votre application Android](firebase-cloud-messaging-images/07-add-firebase-sml.png)](firebase-cloud-messaging-images/07-add-firebase.png#lightbox)

4. Dans l’écran suivant, entrez le nom du package de votre application. Dans cet exemple, le nom du package est **com. xamarin. fcmexample**. Cette valeur doit correspondre au nom du package de votre application Android. Un surnom d’application peut également être entré dans le champ surnom de l' **application** :

    [![Entrée de l’exemple FCM comme surnom d’application](firebase-cloud-messaging-images/08-package-name-sml.png)](firebase-cloud-messaging-images/08-package-name.png#lightbox)

5. Si votre application utilise des liens dynamiques, des invitations ou Google auth, vous devez également entrer votre certificat de signature de débogage. Pour plus d’informations sur la localisation de votre certificat de signature, consultez [recherche de la signature MD5 ou SHA1 de votre magasin de clés](~/android/deploy-test/signing/keystore-signature.md).
    Dans cet exemple, le certificat de signature est laissé vide.

6. Cliquez sur **Ajouter une application**:

    [![Cliquer sur le bouton Ajouter une application](firebase-cloud-messaging-images/09-add-app-sml.png)](firebase-cloud-messaging-images/09-add-app.png#lightbox)

    Une clé API serveur et un ID client sont générés automatiquement pour l’application. Ces informations sont empaquetées dans un fichier **Google-services. JSON** qui est automatiquement téléchargé lorsque vous cliquez sur **Ajouter une application**.
    Veillez à enregistrer ce fichier dans un endroit sûr.

Pour obtenir un exemple détaillé de la façon d’ajouter **Google-services. JSON** à un projet d’application pour recevoir des messages de notification push FCM sur Android, consultez [notifications distantes avec FCM](~/android/data-cloud/google-messaging/remote-notifications-with-fcm.md).

## <a name="for-further-reading"></a>Pour aller plus loin

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/) de Google offre une vue d’ensemble des fonctionnalités clés de Firebase Cloud Messaging, une explication de son fonctionnement et des instructions d’installation.

- Les [demandes d’envoi du serveur d’applications de build](https://firebase.google.com/docs/cloud-messaging/send-message) de Google expliquent comment envoyer des messages avec votre serveur d’applications.

- Les [rfc 6120](https://tools.ietf.org/html/rfc6120) et [RFC 6121](https://tools.ietf.org/html/rfc6121) expliquent et définissent le protocole de messagerie et de présence extensible (XMPP).

- [À propos des messages FCM](https://firebase.google.com/docs/cloud-messaging/concept-options) décrit les différents types de messages qui peuvent être envoyés avec Firebase Cloud Messaging.

## <a name="summary"></a>Résumé

Cet article a fourni une vue d’ensemble de Firebase Cloud Messaging (FCM). Il a expliqué les différentes informations d’identification utilisées pour identifier et autoriser la messagerie entre les serveurs d’applications et les applications clientes. Il illustre les scénarios d’inscription et de messagerie en aval, et décrit en détail les étapes d’inscription de votre application auprès de FCM pour utiliser les services FCM.

## <a name="related-links"></a>Liens connexes

- [Firebase Cloud Messaging](https://firebase.google.com/docs/cloud-messaging/)
