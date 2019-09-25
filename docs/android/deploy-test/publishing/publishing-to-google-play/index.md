---
title: Publication sur Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 656b74bce10d30ddd463486c5103d65c6ba5eb97
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71250021"
---
# <a name="publishing-to-google-play"></a>Publication sur Google Play

Même s’il existe de nombreux marchés pour la distribution d’une application, Google Play est sans nul doute le magasin le plus grand et le plus visité dans le monde des applications Android. Google Play offre une plateforme unique pour la distribution, la promotion, la vente et l’analyse des ventes d’une application Android.

Cette section aborde des rubriques spécifiques à Google Play, comme l’inscription en vue de devenir un éditeur, la collecte des ressources pour aider Google Play à promouvoir et publier votre application, des recommandations pour évaluer votre application sur Google Play et l’utilisation de filtres pour limiter le déploiement d’une application à certains appareils.

## <a name="requirements"></a>Configuration requise

Pour distribuer une application via Google Play, vous devez créer un compte de développeur. La création de ce compte implique des frais uniques de 25 USD.

Toutes les applications doivent être signées avec une clé de chiffrement qui expire après le 22 octobre 2033.

La taille maximale d’un APK publié sur Google Play est de 100 Mo. Si une application dépasse cette taille, Google Play autorise la publication de ressources supplémentaires via les *fichiers d’extension d’APK*. Les fichiers d’extension Android permettent à l’APK d’avoir deux fichiers supplémentaires, dont la taille maximale de chacun est de 2 Go. Google Play héberge et distribuer ces fichiers sans frais. Nous aborderons les fichiers d’extension dans une autre section.

Google Play n’est pas disponible dans le monde entier. Il est possible que certains pays ne soient pas pris en charge pour la distribution d’applications.

## <a name="becoming-a-publisher"></a>Devenir un éditeur

Pour publier des applications sur Google play, il est nécessaire de disposer d’un compte d’éditeur. Pour créer un compte d’éditeur, effectuez les étapes suivantes :

1. Rendez-vous sur la [Console développeur de Google Play](https://play.google.com/apps/publish).
1. Entrez les informations de base sur votre identité de développeur.
1. Lisez et acceptez le contrat relatif à la distribution pour les développeurs correspondant à vos paramètres régionaux.
1. Réglez les frais d’inscription de 25 USD.
1. Confirmez la vérification par e-mail.
1. Une fois le compte créé, il est possible de publier des applications à l’aide de Google Play.

Google Play ne prend pas en charge tous les pays dans le monde. Vous trouverez les listes de pays les plus récentes dans les liens suivants :

1. [Pays où l'inscription en tant que développeur ou marchand est possible](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324) &ndash; Il s’agit d’une liste de tous les pays où les développeurs peuvent s’inscrire en tant que marchands et vendre des applications payantes.

1. [Pays où la distribution de contenus aux utilisateurs de Google Play est possible](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294) &ndash; Il s’agit d’une liste de tous les pays où les applications peuvent être distribuées.

### <a name="preparing-promotional-assets"></a>Préparation des ressources promotionnelles

Pour assurer efficacement la promotion et la publicité d’une application sur Google Play, Google autorise les développeurs à soumettre des ressources promotionnelles telles que des captures d’écran, des graphiques et des vidéos. Google Play utilisera ensuite ces ressources pour assurer la publicité et la promotion de l’application.

#### <a name="launcher-icons"></a>Icônes de lancement

Une *icône de lancement* est un graphique qui représente une application. Chaque icône de lancement doit être un PNG de 32 bits avec un canal alpha pour la transparence. Une application doit avoir des icônes pour toutes les densités d’écran, comme indiqué dans la liste ci-dessous :

- **ldpi** (120 dpi) &ndash; 36  x 36 pixels
- **mdpi** (160 dpi) &ndash; 48 x 48 pixels
- **hdpi** (240 dpi) &ndash; 72 x 72 pixels
- **xhdpi** (320 dpi) &ndash; 96 x 96 pixels

Les icônes de lancement sont la première chose que l’utilisateur voit sur Google Play. Une attention particulière doit donc être donnée à ces icônes qui doivent être visuellement attrayantes et explicites.

Conseils pour les icônes de lancement :

1. **Simples et épurées** &ndash; Les icônes de lancement doivent rester simples et épurées. Cela signifie que le nom de l’application ne doit pas apparaître sur l’icône. Plus les icônes sont simples, plus elles sont marquantes et faciles à distinguer à taille réduite.

1. **Les icônes ne doivent pas être fines**&ndash; Les icônes trop fines ne ressortiront pas bien sur tous les arrière-plans.

1. **Utilisez le canal alpha**&ndash; Les icônes doivent utiliser le canal alpha et ne doivent pas être entièrement encadrées.

#### <a name="high-resolution-application-icons"></a>Icônes d’application haute résolution

Les applications présentes sur Google Play doivent avoir une version haute fidélité de l’icône d’application. Cette icône haute résolution est utilisée uniquement par Google Play et ne se substitue pas à l’icône de lancement de l’application. Les spécifications de l’icône haute résolution sont les suivantes :

1. PNG 32 bits avec un canal alpha
1. 512 x 512 pixels
1. Taille maximale de 1 024 Ko

[Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) est un outil très utile pour créer les icônes de lancement et l’icône d’application haute résolution.

#### <a name="screen-shots"></a>Captures d’écran

Google Play requiert un minimum de deux et un maximum de huit captures d’écran pour une application. Ces captures seront affichées sur la page de détails d’une application dans Google Play.

Les spécifications pour les captures d’écran sont les suivantes :

1. PNG ou JPG 24 bits sans canal alpha
1. 320 x 480 pixels ou 480 x 800 pixels ou 480 x 854 pixels (largeur x hauteur). Les images au format paysage seront rognées.

#### <a name="promotional-graphic"></a>Image promotionnelle

Il s’agit d’une image facultative utilisée par Google Play :

1. PNG ou JPG 24 bits sans canal alpha de 180 x 120 pixels (largeur x hauteur).
1. Aucune bordure.

#### <a name="feature-graphic"></a>Image de présentation

Utilisée dans la section de présentation de Google Play. Cette image peut être affichée seule sans icône d’application.

1. PNG ou JPG 1 024 x 500 pixels (largeur x hauteur) sans canal alpha et sans transparence.
1. Tout le contenu important doit tenir dans un cadre de 924 x 500 pixels. Les pixels en dehors de ce cadre peuvent être rognés pour des raisons de style.
1. Cette image peut être réduite : utilisez des grandes polices et faites en sorte que l’image reste simple.

#### <a name="video-link"></a>Lien vidéo

Il s’agit d’une URL vers une vidéo YouTube de présentation de l’application. Proposez une vidéo de 30 secondes à 2 minutes présentant les fonctionnalités les plus intéressantes de votre application.

### <a name="publishing-to-google-play"></a>Publication sur Google Play

<!-- markdownlint-disable MD001 -->

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Xamarin Android 7.0 introduit un workflow intégré pour la publication des applications sur Google Play à partir de Visual Studio. Si vous utilisez une version de Xamarin Android antérieures à la version 7.0, vous devez charger manuellement votre APK via la Console développeur de Google Play. En outre, un APK doit déjà avoir été chargé pour que vous puissiez utiliser le workflow intégré. Si vous n’avez pas encore chargé votre premier APK, vous devez la charger manuellement. Pour plus d'informations, consultez [Chargement manuel de l’APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md).

Dans [Création d’un nouveau certificat](~/android/deploy-test/signing/index.md#newcert), vous avez appris comment créer un nouveau certificat pour la signature des applications Android. L’étape suivante consiste à publier une application signée sur Google Play :

1. Connectez-vous à votre compte de développeur Google Play pour créer un projet qui est lié à ce compte.
2. Créez un **client OAuth** qui authentifie votre application.
3. Entrez l’ID client et la Clé secrète client dans Visual Studio.
4. Enregistrez votre compte sur Visual Studio.
5. Signez l’application avec votre certificat.
6. Publiez votre application signée sur Google Play.

Dans [Archiver pour publication](~/android/deploy-test/release-prep/index.md#archive), la boîte de dialogue **Canal de distribution** proposait deux options de distribution : **Ad Hoc** et **Google Play**. Si la boîte de dialogue **Identité de signature** s’affiche à la place, cliquez sur **Précédent** pour revenir à la boîte de dialogue **Canal de Distribution**. Sélectionnez **Google Play** et cliquez sur **Suivant** :

[![Boîte de dialogue Canal de distribution](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

Dans la boîte de dialogue **Identité de signature**, sélectionnez l’identité créée dans [Création d’un nouveau certificat](~/android/deploy-test/signing/index.md#newcert) et cliquez sur **Continuer** :

[![Boîte de dialogue Identité de signature](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

Dans la boîte de dialogue **Comptes Google Play**, cliquez sur le bouton **+** pour ajouter un nouveau compte Google Play :

[![Boîte de dialogue Comptes Google Play](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

Dans la boîte de dialogue **Inscrire l'accès à l'API Google**, vous devez indiquer l’_ID client_ et la _Clé secrète client_ qui fournissent l’accès à l’API à votre compte de développeur Google Play :

[![Boîte de dialogue Inscrire l’accès à l’API Google](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

La section suivante explique comment créer un nouveau projet d’API Google et générer l’_ID client_ et la _Clé secrète client_ nécessaires.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Visual Studio for Mac propose un workflow intégré pour la publication des applications sur Google Play.

Dans [Création d’un certificat](~/android/deploy-test/signing/index.md#newcert), vous avez appris à créer un certificat pour la signature d’applications Android. Les étapes suivantes décrivent comment publier une application Xamarin.Android sur Google Play :

1. Connectez-vous à votre compte de développeur Google Play pour créer un projet qui est lié à ce compte.
2. Créez un _client OAuth_ qui authentifie votre application.
3. Entrez l’_ID client_ et la _Clé secrète client_ dans Visual Studio pour Mac.
4. Enregistrez votre compte sur Visual Studio pour Mac.
5. Signez l’application avec votre certificat.
6. Publiez votre application signée sur Google Play.

Dans [Archiver pour publication](~/android/deploy-test/release-prep/index.md#archive), la boîte de dialogue **Signer et distribuer...** proposait deux options pour la distribution. Sélectionnez **Google Play** et cliquez sur **Suivant** :

[![Boîte de dialogue Sélectionner le canal de distribution Android](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

Dans la boîte de dialogue **Compte d’API Google Play**, vous devez indiquer l’_ID client_ et la _Clé secrète client_ qui fournissent l’accès à l’API à votre compte de développeur Google Play :

[![Boîte de dialogue Compte d’API Google Play](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

La section suivante explique comment créer un nouveau projet d’API Google et générer l’_ID client_ et la _Clé secrète client_ nécessaires.

-----

#### <a name="create-a-google-api-project"></a>Créer un projet d’API Google

Tout d’abord, connectez-vous à votre [compte de développeur Google Play](https://play.google.com/apps/publish).
Si vous n’avez pas encore de compte de développeur Google Play, consultez [Démarrer avec la publication](https://developer.android.com/distribute/googleplay/start.html).
En outre, la page [Démarrage](https://developers.google.com/android-publisher/getting_started) de l’API de développeur Google Play explique comment utiliser l’API de développeur Google Play. Une fois connecté à la Console développeur de Google Play, cliquez sur **Settings** :

[![Icône Settings](images/01-google-play-developer-console-sml.png)](images/01-google-play-developer-console.png#lightbox)

Dans la page **SETTINGS**, sélectionnez **API access**, puis cliquez sur le bouton **Create new project** :

[![Bouton Create new project](images/02-create-new-project-sml.png)](images/02-create-new-project.png#lightbox)

Après environ une minute, le nouveau projet d’API est automatiquement généré et lié à votre compte de développeur Google Play.

L’étape suivante consiste à créer un client OAuth pour l’application (si ce n’est pas déjà fait). Lorsque les utilisateurs demandent l’accès à leurs données privées à l’aide de votre application, votre ID client OAuth est utilisé pour authentifier votre application.
Cliquez sur **Create OAuth Client** pour créer un nouveau client OAuth :

[![Bouton Create OAuth Client](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

Après quelques secondes, un nouvel ID client est généré. Cliquez sur **View in Google Developers Console** pour voir votre nouvel ID client dans la Console développeur de Google :

[![ID client affiché](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

L’ID client est affiché avec son nom et sa date de création. Cliquez sur l’icône **Modifier le client OAuth** pour afficher la clé secrète client de votre application :

[![Afficher les informations d’identification de l’application](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

Le nom par défaut du client OAuth est *Google Play Android Developer*. Vous pouvez le remplacer par le nom de l’application Xamarin.Android, ou tout nom approprié. Dans cet exemple, le nom du client OAuth est modifié et remplacé par le nom de l’application, **MyApp** :

[![ID client et secret affichés](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

Cliquez sur **Save** pour enregistrer les modifications. Vous revenez alors à la page **Credentials** où vous pouvez télécharger les informations d’identification en cliquant sur l’icône **Télécharger le fichier JSON** :

[![Icône Télécharger le fichier JSON](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

Ce fichier JSON contient l’ID client et la Clé secrète client que vous pouvez couper et coller dans la boîte de dialogue **Signer et distribuer** à l’étape suivante.

#### <a name="register-google-api-access"></a>Inscrire l'accès à l'API Google

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Utilisez l’ID client et la Clé secrète client pour remplir la boîte de dialogue **Compte d’API Google Play** dans Visual Studio pour Mac. Il est possible d’ajouter une description au compte ce qui permet d’inscrire plusieurs comptes Google Play et de charger de futures APK dans des comptes Google Play différents. Copiez l’ID client et la Clé secrète client dans cette boîte de dialogue, puis cliquez sur **Inscrire** :

[![Boîte de dialogue Inscrire l’accès à l’API Google](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

Un navigateur web s’ouvre et vous invite à vous connecter à votre compte de développeur Android Google Play (si vous n’êtes pas déjà connecté). Une fois que vous êtes connecté, l’invite suivante s’affiche dans le navigateur web.
Cliquez sur **Autoriser** pour autoriser l’application :

[![Boîte de dialogue Autoriser l’application](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>Publier

Après avoir cliqué sur **Autoriser**, le navigateur indique _Code de vérification reçu. Fermeture en cours..._  et l’application est ajoutée à la liste des comptes Google Play dans Visual Studio. Dans la boîte de dialogue **Comptes Google Play**, cliquez sur **Continuer** :

[![Compte ajouté aux comptes Google Play](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

Ensuite, la boîte de dialogue **Piste Google Play** s’affiche. Google Play propose quatre pistes pour le chargement de votre application :

- **Alpha** &ndash; Utilisé pour charger une toute première version de l’application pour un nombre réduit de testeurs.
- **Bêta** &ndash; Utilisé pour charger une version préliminaire de l’application pour un nombre plus important de testeurs.
- **Lancement** &ndash; Permet à un pourcentage d’utilisateurs de recevoir une version mise à jour de l’application. Cela permet d’augmenter progressivement le pourcentage de 10 % des utilisateurs à 100 % à mesure que vous supprimez les bogues.
- **Production** &ndash; Sélectionnez cette option lorsque l’application est prête pour la distribution complète à partir sur le magasin Google Play.

Choisissez la piste Google Play qui sera utilisée pour chargement l’application et cliquez sur **Charger**. Si vous sélectionnez **Lancement**, veillez à entrer une valeur de pourcentage :

[![Sélectionner Alpha, Bêta, Lancement ou Production](images/vs/08-google-play-track-sml.png)](images/vs/08-google-play-track.png#lightbox)

Pour plus d’informations sur le test et les lancements intermédiaires de Google Play, consultez [Configurer des tests alpha/bêta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

Ensuite, une boîte de dialogue s’affiche dans laquelle vous pouvez entrer le mot de passe du certificat de signature.
Entrez le mot de passe, puis cliquez sur **OK** :

[![Boîte de dialogue Mot de passe de signature](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

Le **Gestionnaire d’archives** affiche la progression du chargement :

[![Progression du chargement de l’APK](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

Lorsque le chargement est terminé, l’état d’achèvement est indiqué dans le coin inférieur gauche de Visual Studio :

[![Message Publication du projet terminée](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)

### <a name="troubleshooting"></a>Résolution des problèmes

Notez qu’un APK doit déjà avoir été soumis à Google Play Store pour que **Publier sur Google Play** fonctionne. Si un fichier APK n’est pas déjà été chargé, l’Assistant Publication affiche l’erreur suivante dans le volet **Erreurs** :

[![Vous devez charger manuellement votre premier APK pour cette application](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

Lorsque cette erreur se produit, chargez manuellement un APK (par exemple, une build Ad-Hoc) via la Console développeur de Google Play et utilisez la boîte de dialogue **Canal de distribution** pour les mises à jour ultérieures de l’APK.  Pour plus d'informations, consultez [Chargement manuel de l’APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md). Le code de version de l’APK doit changer avec chaque chargement, sinon l’erreur suivante se produit :

[![Un APK avec un code de version (1) a déjà été mis à jour](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

Pour résoudre cette erreur, regénérez l’application avec un numéro de version différent et resoumettez-le à Google Play via la boîte de dialogue **Canal de distribution**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Utilisez l’ID client et la Clé secrète client pour remplir la boîte de dialogue **Compte d’API Google Play** dans Visual Studio pour Mac. Il est possible d’ajouter une description au compte ce qui permet d’inscrire plusieurs comptes Google Play et de charger de futures APK dans des comptes Google Play différents. Copiez l’ID client et la Clé secrète client dans cette boîte de dialogue, puis cliquez sur **Inscrire** :

[![Boîte de dialogue Autoriser l’accès](images/xs/10-register-sml.png)](images/xs/10-register.png#lightbox)

Si l’ID client et la Clé secrète client sont acceptés, un message **Inscription réussie** s’affiche. Cliquez sur**Suivant** :

[![Message Inscription réussie](images/xs/11-registration-successful-sml.png)](images/xs/11-registration-successful.png#lightbox)

Dans la boîte de dialogue **Compte Google Play**, sélectionnez un compte Google et une piste pour le téléchargement de l’application :

[![Boîte de dialogue Choisir un compte Google](images/xs/12-choose-google-account-sml.png)](images/xs/12-choose-google-account.png#lightbox)

Google Play propose quatre pistes pour le chargement de votre application :

- **Alpha** &ndash; Utilisé pour charger une toute première version de l’application pour un nombre réduit de testeurs.

- **Bêta** &ndash; Utilisé pour charger une version préliminaire de l’application pour un nombre plus important de testeurs.

- **Lancement** &ndash; Permet à un pourcentage d’utilisateurs de recevoir une version mise à jour de l’application. Cela permet d’augmenter progressivement le pourcentage de 10 % des utilisateurs à 100 % à mesure que vous supprimez les bogues.

- **Production** &ndash; Sélectionnez cette option lorsque l’application est prête pour la distribution complète à partir sur le magasin Google Play.

Pour plus d’informations sur le test et les lancements intermédiaires de Google Play, consultez [Configurer des tests alpha/bêta](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en).

Ensuite, choisissez une identité de signature qui sera utilisée pour signer l’application.
Sélectionnez **Utiliser une clé existante** pour utiliser une identité de signature existante ; sinon, consultez le guide [Création d’un nouveau certificat](~/android/deploy-test/signing/index.md#newcert) pour plus d’informations sur la création d’une nouvelle clé. Après avoir sélectionné un certificat pour signer l’application, cliquez sur **Suivant** :

[![Boîte de dialogue Identité de signature Android](images/xs/13-android-signing-identity-sml.png)](images/xs/13-android-signing-identity.png#lightbox)

À ce stade, l’application peut être chargée sur Google Play. La boîte de dialogue **Publier sur Google Play** résume les informations relatives à votre application. Cliquez sur **Publier** pour publier votre application sur Google Play :

[![Boîte de dialogue Publier sur Google Play](images/xs/14-publish-to-google-play-sml.png)](images/xs/14-publish-to-google-play.png#lightbox)

Notez qu’un APK doit déjà avoir été soumis à Google Play Store pour que **Publier sur Google Play** fonctionne. Si un APK n’est pas chargé, l’erreur suivante peut se produire :

> _Google Play exige que vous chargiez manuellement votre premier fichier APK pour cette application. À cette fin, vous pouvez utiliser un APK spécifique._

ou

> _Aucune application n’a été trouvée pour le nom de package donné. [404]_

Pour résoudre cette erreur, chargez manuellement un APK (par exemple, une build Ad-Hoc) via la Console développeur de Google Play et utilisez la boîte de dialogue **Publier sur Google Play** pour les mises à jour ultérieures de l’APK. Pour plus d’informations sur la façon de charger manuellement un APK, consultez [Chargement manuel de l’APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md).

-----
