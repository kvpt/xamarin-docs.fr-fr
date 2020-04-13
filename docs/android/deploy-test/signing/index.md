---
title: Signature du paquet d’application Android
description: Comment signer le paquet d’application Android (APK) pour la publication
ms.prod: xamarin
ms.assetid: 8E3EFBB2-F8AD-C126-5F32-7FD140791E53
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 07/02/2018
ms.openlocfilehash: b646893d8bbe63aa537c973c90552e0367735e47
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "79303903"
---
# <a name="signing-the-android-application-package"></a>Signature du paquet d’application Android

Dans [Préparation d’une application pour sa mise en production](~/android/deploy-test/release-prep/index.md), le **Gestionnaire d’archives** a été utilisé pour générer l’application et la placer dans une archive en vue de sa signature et de sa publication. Cette section explique comment créer une identité de signature Android et un nouveau certificat de signature pour les applications Android et comment publier l’application archivée *ad hoc* sur disque. Le fichier APK ainsi créé peut être chargé de façon indépendante (sideloaded) dans les appareils Android sans passer par un App Store.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Dans [Archive for Publishing](~/android/deploy-test/release-prep/index.md#archive), le dialogue de Distribution **Channel** a présenté deux choix de distribution. Sélectionnez **Ad-Hoc** :

[![Dialogue de canal de distribution](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Dans [Archiver pour publication](~/android/deploy-test/release-prep/index.md#archive), la boîte de dialogue **Signer et distribuer...** proposait deux options pour la distribution. Sélectionnez **Ad-Hoc** et cliquez sur **Suivant** :

[![Signez et distribuez le dialogue](images/xs/01-select-ad-hoc-sml.png)](images/xs/01-select-ad-hoc.png#lightbox)

-----

<a name="newcertvs" />
<a name="newcert" />
<a name="newcertxs" />

## <a name="create-a-new-certificate"></a>Créer un nouveau certificat

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Une fois **Ad-Hoc** sélectionné, Visual Studio ouvre la page **Identité de signature** de la boîte de dialogue comme illustré dans la capture d’écran suivante. Avant de pouvoir être publié, le fichier .APK doit être signé avec une clé de signature (également appelée un certificat).

Un certificat existant peut être utilisé en cliquant sur le bouton **Importer**, puis en poursuivant par [Signer le fichier APK](#sign-the-apk). Sinon, cliquez sur **+** le bouton pour créer un nouveau certificat :

[![Identité de signature Ad Hoc](images/vs/02-ad-hoc-signing-identity-vs-sml.png)](images/vs/02-ad-hoc-signing-identity-vs.png#lightbox)

La boîte de dialogue **Créer un magasin de clés Android** s’affiche. Utilisez cette boîte de dialogue pour créer un nouveau certificat de signature qui sera utilisé pour la signature des applications Android. Entrez les informations requises (indiquées en rouge), comme illustré dans cette boîte de dialogue :

[![Créez le dialogue Android Key Store](images/vs/03-create-android-key-store-vs-sml.png)](images/vs/03-create-android-key-store-vs.png#lightbox)

L’exemple suivant illustre le type d’informations qui doivent être fournies. Cliquez sur **Créer** pour créer le nouveau certificat :

[![Création d’un certificat](images/vs/04-key-store-example-vs-sml.png)](images/vs/04-key-store-example-vs.png#lightbox)

Le magasin de clés ainsi créé se trouve à l’emplacement suivant :

**C:\\\\Utilisateurs*USERNAME*\\\\\\AppData\\Local\\Xamarin\\Mono pour Android Keystore*ALIAS*\\*ALIAS*.keystore**

Par exemple, en utilisant **chimp** comme alias, la procédure ci-dessus créerait une nouvelle clé de signature à l’emplacement suivant :

**C:\\Utilisateurs\\*NOM_UTILISATEUR*\\AppData\\Local\\Xamarin\\Mono for Android\\Keystore\\chimp\\chimp.keystore**

> [!NOTE]
> Veillez à sauvegarder le fichier du magasin de clés résultant et le mot de passe en lieu sûr, car ils ne sont pas inclus dans la solution. Si vous perdez votre fichier de magasin de clés (par exemple, suite à un changement d’ordinateur ou à la réinstallation de Windows), vous ne pourrez pas signer votre application avec le même certificat que les versions précédentes.

Pour plus d’informations sur le magasin de clés, consultez [Recherche de la signature MD5 ou SHA1 de votre magasin de clés](~/android/deploy-test/signing/keystore-signature.md).

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Après avoir cliqué sur **Ad-Hoc**, Visual Studio pour Mac ouvre la boîte de dialogue **Identité de signature Android** comme illustré dans la capture d’écran suivante. Avant de pouvoir être publié, le fichier .APK doit être signé avec une clé de signature (également appelée un certificat). Si un certificat existe déjà, cliquez sur le bouton **Importer une clé** pour l’importer, puis poursuivez avec [Signer le fichier APK](#sign-the-apk). Sinon, cliquez sur le bouton **Créer une clé** pour créer un nouveau certificat :

[![Dialogue d’identité de signature Android](images/xs/02-android-signing-identity-sml.png)](images/xs/02-android-signing-identity.png#lightbox)

La boîte de dialogue **Créer un autre certificat** est utilisée pour créer un nouveau certificat de signature qui peut être utilisé pour la signature des applications Android. Cliquez sur **OK** après avoir entré les informations nécessaires :

[![Créer un nouveau dialogue sur les certificats](images/xs/03-create-new-certificate-sml.png)](images/xs/03-create-new-certificate.png#lightbox)

Le magasin de clés ainsi créé se trouve à l’emplacement suivant :

**~/Library/Developer/Xamarin/Keystore/alias/alias.keystore**

Par exemple, la procédure ci-dessus crée une nouvelle clé de signature à l’emplacement suivant :

**~/Library/Developer/Xamarin/Keystore/chimp/chimp.keystore**

> [!NOTE]
> Veillez à sauvegarder le fichier du magasin de clés résultant et le mot de passe en lieu sûr, car ils ne sont pas inclus dans la solution. Si vous perdez votre fichier de magasin de clés (par exemple, suite à un changement d’ordinateur ou à la réinstallation de macOS), vous ne pourrez pas signer votre application avec le même certificat que les versions précédentes.

Pour plus d’informations sur le magasin de clés, consultez [Recherche de la signature MD5 ou SHA1 de votre magasin de clés](~/android/deploy-test/signing/keystore-signature.md).

-----

## <a name="sign-the-apk"></a>Signer l’APK

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Lorsque vous cliquez sur **Créer**, un nouveau magasin de clés (contenant un nouveau certificat) est enregistré et répertorié sous **Identité de signature** comme illustré dans la capture d’écran suivante. Pour publier une application sur Google Play, cliquez sur **Annuler** et accédez à la rubrique [Publication sur Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
Pour une publication *ad-hoc*, sélectionnez l’identité de signature à utiliser pour la signature, puis cliquez sur **Enregistrer sous** pour publier l’application en vue de sa distribution indépendante. Par exemple, l’identité de signature **chimp** (créée précédemment) est sélectionnée dans cette capture d’écran :

[![Exemple d’identité de signature](images/vs/05-save-as-vs-sml.png)](images/vs/05-save-as-vs.png#lightbox)

Ensuite, le **Gestionnaire d’archives** affiche la progression de la publication. Une fois le processus de publication terminé, la boîte de dialogue **Enregistrer sous** s’ouvre. Vous devez indiquer l’emplacement de stockage du fichier .APK généré :

[![Enregistrer comme dialogue](images/vs/06-save-as-dialog-vs-sml.png)](images/vs/06-save-as-dialog-vs.png#lightbox)

Accédez à l’emplacement souhaité et cliquez sur **Enregistrer**. Si le mot de passe de la clé est inconnu, la boîte de dialogue **Mot de passe de signature** s’affiche et vous invite à entrer le mot de passe pour le certificat sélectionné :

[![Dialogue de mot de passe de signature](images/vs/07-signing-password-vs-sml.png)](images/vs/07-signing-password-vs.png#lightbox)

Une fois le processus de signature terminé, cliquez sur **Ouvrir la distribution** :

[![Bouton de distribution ouverte](images/vs/08-open-distribution-sml.png)](images/vs/08-open-distribution.png#lightbox)

L’Explorateur Windows ouvre alors le dossier contenant le fichier APK généré. À ce stade, Visual Studio a compilé l’application Xamarin.Android dans un APK qui est prêt pour la distribution.
La capture d’écran suivante affiche un exemple de l’application prête à être publier, **MyApp.MyApp.apk** :

[![APK montré dans Windows Explorer](images/vs/09-generated-app-vs-sml.png)](images/vs/09-generated-app-vs.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

Comme nous le voyons ici, un nouveau certificat a été ajouté au magasin de clés. Pour publier une application sur Google Play, cliquez sur **Annuler** et accédez à la rubrique [Publication sur Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md).
Sinon, cliquez sur **Suivant** pour publier l’application *ad-hoc* (en vue de sa distribution indépendante) comme illustré dans cet exemple :

[![Signez et distribuez le dialogue](images/xs/04-select-identity-sml.png)](images/xs/04-select-identity.png#lightbox)

La boîte de dialogue **Publier en tant qu’Ad Hoc** affiche un résumé de l’application signée avant sa publication. Si ces informations sont correctes, cliquez sur **Publier**.

[![Publier sous le titre Ad Hoc dialogue](images/xs/05-publish-ad-hoc-sml.png)](images/xs/05-publish-ad-hoc.png#lightbox)

La boîte de dialogue **Fichier APK de sortie** enregistre l’APK dans le chemin spécifié. Cliquez sur **Enregistrer**.

![Boîte de dialogue Fichier APK de sortie](images/xs/06-output-apk-file.png)

Ensuite, entrez le mot de passe du certificat (celui qui a été utilisé dans la boîte de dialogue **Créer un autre certificat**) et cliquez sur **OK** :

![Entrer le mot de passe du certificat](images/xs/07-signing-certificate.png)

L’APK est signé avec le certificat et enregistré à l’emplacement spécifié. Cliquez sur **Afficher dans le Finder** :

[![Publication Dialogue réussi](images/xs/08-app-is-ready-sml.png)](images/xs/08-app-is-ready.png#lightbox)

Le Finder s’ouvre alors à l’emplacement du fichier APK signé :

[![APK montré dans Finder](images/xs/09-show-in-finder-sml.png)](images/xs/09-show-in-finder.png#lightbox)

L’APK est prêt à être copié à partir du Finder et à être envoyé vers sa destination finale. Il est conseillé d’installer l’APK sur un appareil Android et de le tester avant sa distribution. Voir [Publication indépendante](~/android/deploy-test/publishing/publishing-independently.md) pour plus d’informations sur la publication d’un APK *ad-hoc*.

-----

## <a name="next-steps"></a>Étapes suivantes

Une fois le paquet d’application signé pour sa mise en production, il doit être publié. Les sections suivantes décrivent différentes façons de publier une application.
