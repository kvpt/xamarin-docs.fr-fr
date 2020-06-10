---
titre : « étapes du programme d’installation-Connectez-vous avec Apple pour Xamarin.Forms «Description : » la connexion avec l’installation d’Apple diffère selon les différentes plateformes ciblées par votre application mobile.»
ms. Prod : xamarin ms. AssetID : 8F712802-395B-469B-B5BE-C927AD1A8391 ms. Technology : xamarin-Forms Author : davidortinau ms. Author : daortin ms. Date : 09/10/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="setup-sign-in-with-apple-for-xamarinforms"></a>Configurer la connexion avec Apple pourXamarin.Forms

Ce guide couvre la série d’étapes nécessaires à la configuration de vos applications multiplateformes pour prendre en main la connexion avec Apple. Tandis que la configuration Apple est directe dans le portail des développeurs Apple, des étapes supplémentaires sont nécessaires pour créer une relation sécurisée entre votre Android et Apple. 

## <a name="apple-developer-setup"></a>Installation d’Apple Developer

Avant de pouvoir utiliser la connexion avec Apple dans vos applications, vous devez suivre certaines étapes de configuration dans la section [certificats, identificateurs & profils](https://developer.apple.com/account/resources/) du portail des développeurs d’Apple.

### <a name="apple-sign-in-domain"></a>Domaine de connexion Apple

Inscrivez votre nom de domaine et vérifiez-le auprès d’Apple dans la section [plus](https://developer.apple.com/account/resources/services/list) de la section *certificats, identificateurs & profils* .

![Plus de section](sign-in-images/readme-signin-domain-configure.png)

Ajoutez votre domaine, puis cliquez sur **inscrire**.

![formulaire inscrire un domaine](sign-in-images/readme-signin-domain-more.png)

> [!NOTE]
> Si vous voyez une erreur indiquant que votre domaine n’est pas compatible avec SPF, vous devez ajouter un enregistrement TXT DNS de SPF à votre domaine et attendre qu’il se propage avant de continuer : le TXT SPF peut ressembler à ceci :`v=spf1 a a:myapp.com -all`

Ensuite, vous devez vérifier la propriété du domaine en cliquant sur **Télécharger** pour récupérer le `apple-developer-domain-association.txt` fichier, puis le télécharger dans le `.well-known` dossier du site Web de votre domaine.

Une fois le `.well-known/apple-developer-domain-association.txt` fichier téléchargé et accessible, vous pouvez cliquer sur **vérifier** pour que Apple vérifie la propriété de votre domaine.

> [!NOTE]
> Apple vérifie la propriété avec `https://` . Vérifiez que vous avez configuré SSL et que le fichier est accessible par le biais d’une URL sécurisée.

Ce processus s’est terminé correctement avant de continuer.

## <a name="setup-your-app-id"></a>Configurer votre ID d’application

Dans la section [identificateurs](https://developer.apple.com/account/resources/identifiers/list) , créez un nouvel identificateur, puis choisissez ID de l' **application**. Si vous disposez déjà d’un ID d’application, choisissez plutôt de le modifier.

![Créer un ID d’application](sign-in-images/readme-appid-create.png)

Activez la **connexion avec Apple**. Vous souhaiterez probablement utiliser l’option **activer en tant qu’ID d’application principale** .

![Activer la connexion avec Apple](sign-in-images/readme-appid-signin.png)

Enregistrez les modifications apportées à l’ID de votre application.

## <a name="create-a-service-id"></a>Créer un ID de service

Dans la section [identificateurs](https://developer.apple.com/account/resources/identifiers/list/serviceId) , créez un nouvel identificateur, puis choisissez **ID du service**.

![Créer un ID de service](sign-in-images/readme-serviceid-create.png)

Donnez une description à votre ID de services et un identificateur.  Cet identificateur sera votre `ServerId` .  Veillez à activer la **connexion avec Apple**.

Avant de continuer, cliquez sur **configurer** en regard de l’option _se connecter avec Apple_ que vous avez activée.

Dans le panneau de configuration, vérifiez que l' **ID d’application principal** correct est sélectionné.

Ensuite, choisissez le **domaine Web** que vous avez configuré précédemment.

Enfin, ajoutez une ou plusieurs **URL de retour**.  `redirect_uri`Si vous utilisez ultérieurement, vous devez l’inscrire exactement comme vous l’utilisez.  Veillez à inclure le `http://` ou `https://` dans l’URL lorsque vous l’entrez.

> [!NOTE]
> À des fins de test, vous ne pouvez pas utiliser `127.0.0.1` ou `localhost` , mais vous pouvez utiliser d’autres domaines tels que `local.test` .  Si vous choisissez cette solution, vous pouvez modifier le fichier de votre ordinateur `hosts` pour résoudre ce domaine fictif en adresse IP locale.

![Configurer votre connexion Apple](sign-in-images/readme-serviceid-configure.png)

Lorsque vous avez terminé, enregistrez vos modifications.

## <a name="create-a-key-for-your-services-id"></a>Créer une clé pour votre ID de services

Dans la section [clés](https://developer.apple.com/account/resources/authkeys/list) , créez une nouvelle **clé**.

Donnez un nom à votre clé, puis activez la **connexion avec Apple**.

![Créer une nouvelle clé](sign-in-images/readme-key-create.png)

Cliquez sur **configurer** en regard de _se connecter avec Apple_.

Vérifiez que l' **ID d’application principal** correct est sélectionné, puis cliquez sur **Enregistrer**.

Cliquez sur **Continuer** , puis **Inscrivez-vous** pour créer votre nouvelle clé.

Ensuite, vous n’aurez qu’une seule chance de télécharger la clé que vous venez de générer.  Cliquez sur **Télécharger**.

![Télécharger la clé](sign-in-images/readme-key-download.png)

Prenez également note de votre **ID de clé** à cette étape. Ce sera utilisé pour votre `KeyId` version ultérieure.

Vous aurez téléchargé un `.p8` fichier de clé.  Vous pouvez ouvrir ce fichier dans le bloc-notes ou VSCode pour afficher le contenu du texte.  Ils doivent ressembler à ceci :

```
---
title: "Setup Steps - Sign In with Apple for Xamarin.Forms"
description: Sign In with Apple setup differs depending on the different platforms your mobile application targets.
ms.prod: xamarin
ms.assetid: 8F712802-395B-469B-B5BE-C927AD1A8391
ms.technology: xamarin-forms
author: davidortinau
ms.author: daortin
ms.date: 09/10/2019
no-loc:
- "Xamarin.Forms"
- "Xamarin.Essentials"

-----END PRIVATE KEY-----
```

Nommez cette clé `P8FileContents` et conservez-la en lieu sûr. Vous allez l’utiliser lors de l’intégration de ce service dans votre application mobile.

## <a name="summary"></a>Résumé

Cet article décrit les étapes nécessaires à la configuration de la connexion avec Apple pour une utilisation dans vos Xamarin.Forms applications.

## <a name="related-links"></a>Liens connexes

- [Se connecter avec les instructions Apple](https://developer.apple.com/design/human-interface-guidelines/sign-in-with-apple/overview/)
  