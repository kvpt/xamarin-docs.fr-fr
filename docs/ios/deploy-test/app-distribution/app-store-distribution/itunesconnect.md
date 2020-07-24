---
title: Configuration d’une application dans iTunes Connect
description: Cet article décrit les étapes nécessaires à l’installation et la gestion d’une application Xamarin.iOS dans iTunes Connect, pour qu’elle puisse être distribuée sur l’App Store.
ms.prod: xamarin
ms.assetid: 74587317-4b15-4904-9582-dcd914827cbc
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 90309fe8a9282110a685151455d0b1407b269128
ms.sourcegitcommit: 952db1983c0bc373844c5fbe9d185e04a87d8fb4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "86997356"
---
# <a name="configuring-an-app-in-itunes-connect"></a>Configuration d’une application dans iTunes Connect

> [!IMPORTANT]
> Apple [a indiqué](https://developer.apple.com/ios/submit/) qu’à partir de mars 2019 toutes les applications et mises à jour envoyées à l’App Store doivent avoir été créées avec le kit SDK d’iOS 12.1 ou ultérieur, inclus dans Xcode 10.1 ou ultérieur.
> Les applications doivent aussi prendre en charge les tailles d’écran de l’iPhone XS et de l’iPad Pro 12,9".

iTunes Connect est une suite d’outils web qui permettent, entre autres choses, de gérer des applications iOS sur l’App Store. Vous devez préparer correctement votre application Xamarin.iOS dans iTunes Connect avant de pouvoir la soumettre pour évaluation à Apple, et ensuite la mettre en vente ou la proposer gratuitement dans l’App Store.

iTunes Connect peut être utilisé pour :

- Définir le nom de l’application (tel qu’il est affiché dans l’App Store)
- Fournir des captures d’écran ou une vidéo de l’application en action sur les appareils iOS pris en charge
- Fournir une description claire et succincte de l’application, notamment ses fonctionnalités et avantages pour l’utilisateur final
- Fournir des catégories et sous-catégories qui permettent à l’utilisateur de trouver l’application dans l’App Store
- Fournir un mot clé qui peut aider l’utilisateur à trouver l’application
- Fournir des URL de contact et de support sur votre site web (condition imposée par Apple)
- Définir la classification de l’application pour les paramètres de contrôle parental dans l’App Store
- Sélectionner un prix de vente ou indiquer que l’application est disponible gratuitement
- Configurer les technologies facultatives de l’App Store, par exemple Game Center et l’achat dans l’application

De plus, l’application doit également disposer d’illustrations attrayantes et d’images en haute résolution, au cas où Apple déciderait de les présenter dans l’App Store. Pour plus d’informations, consultez le [Guide du développeur iTunes Connect](https://developer.apple.com/support/itunes-connect/) d’Apple.

## <a name="managing-agreements-tax-and-banking"></a>Gestion des contrats, des taxes et des informations bancaires

La section **Agreements, Tax, and Banking (Contrats, taxes et informations bancaires)** d’iTunes Connect permet de fournir les informations financières nécessaires au paiement des développeurs iTunes et des imputations fiscales. Elle permet également de suivre l’état des contrats en place avec Apple. Pour pouvoir publier une application iOS (gratuite ou payante) sur l’App Store, vous devez d’abord mettre en place les contrats appropriés et accepter les modifications apportées aux contrats existants.

[![Gestion des contrats, des taxes et des banques](itunesconnect-images/agreement01.png)](itunesconnect-images/agreement01.png#lightbox)

À partir de là, vous pouvez :

- Fournir un **agent d’équipe** et définir d’autres rôles utilisateur pour votre compte iTunes Connect, par exemple **Admin** ou **Finance**
- Inscrire et gérer les **contrats** qui permettent à une organisation de distribuer des applications dans l’App Store
- Indiquer le nom de l’**entité légale** (nom du vendeur dans l’App Store) responsable des contrats, informations bancaires et informations fiscales associées à votre organisation
- Fournir les informations **bancaires** et **fiscales** nécessaires pour pouvoir vendre des applications via l’App Store

Encore une fois, ces informations _doivent_ être correctement fournies, validées et actualisées pour que vous puissiez soumettre une application iOS à iTunes Connect afin qu’elle soit évaluée et, le cas échéant, publiée. Pour plus d’informations, consultez la documentation d’Apple sur la [gestion des contrats, des taxes et des informations bancaires](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/ManagingContractsandBanking.html#//apple_ref/doc/uid/TP40011225-CH21-SW1).

<a name="creating"></a>

## <a name="creating-an-itunes-connect-record"></a>Création d’un enregistrement iTunes Connect

Avant de pouvoir charger une application Xamarin.iOS sur iTunes Connect pour la distribuer via l’App Store, vous devez créer un enregistrement relatif à l’application dans iTunes Connect. Cet enregistrement contient toutes les informations relatives à l’application telles qu’elles apparaissent dans l’App Store (en autant de langues que nécessaire), ainsi que toutes les informations nécessaires à la gestion de l’application via le processus de distribution. De plus, il sert à configurer les technologies de l’App Store, par exemple iAd App Network ou Game Center.

Pour ajouter une application iOS à iTunes Connect, vous devez être l’**agent d’équipe** ou un utilisateur disposant du rôle **Admin** ou **Technical (Technicien)**.

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquez sur **mes applications**:

    [![Cliquez sur mes applications](itunesconnect-images/add01.png)](itunesconnect-images/add01.png#lightbox)
2. Cliquez sur **+** dans le coin supérieur gauche et sélectionnez **nouvelle application iOS**:

    [![Ajout d’une nouvelle application iOS](itunesconnect-images/add02.png)](itunesconnect-images/add02.png#lightbox)
3. iTunes Connect affiche la boîte de dialogue **New iOS App (Nouvelle application iOS)**  :

    [![Boîte de dialogue nouvelle application iOS](itunesconnect-images/add03.png)](itunesconnect-images/add03.png#lightbox)
4. Entrez un **Name (Nom)** et un **Version Number (Numéro de version)** pour l’application, tels qu’ils doivent s’afficher dans l’App Store.
5. Sélectionnez la **Primary Language (Langue principale)**.
6. Entrez un numéro de **SKU (Référence)**. Il s’agit d’un identificateur unique et constant, qui permet de suivre l’application. Il n’est pas visible par l’utilisateur final et _ne peut pas être changé_ après la création de l’application.
7. Sélectionnez le **Bundle ID (ID de bundle)** de l’application que vous avez créée dans le Centre de développement quand vous avez provisionné cette dernière. Vous devez utiliser cet ID de bundle durant la signature du bundle de distribution dans Visual Studio pour Mac ou Visual Studio. Pour plus d’informations, consultez [Création d’un profil de distribution](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) et [Sélection d’un profil de distribution dans un projet Xamarin.iOS](~/ios/get-started/installation/device-provisioning/index.md) dans notre documentation.
8. Cliquez sur le bouton **Create (Créer)** pour créer l’enregistrement iTunes Connect de l’application.

L’application est créée dans iTunes Connect. Vous pouvez ensuite spécifier les informations nécessaires correspondantes, par exemple la description, la tarification, les catégories, la classification, etc. :

[![La nouvelle application sera créée dans iTunes Connect](itunesconnect-images/add04.png)](itunesconnect-images/add04.png#lightbox)

<a name="managing"></a>

## <a name="managing-app-videos-and-screenshots"></a>Gestion des vidéos et des captures d’écran de l’application

Pour commercialiser avec succès votre application iOS dans l’App Store, il est important de disposer d’un bel ensemble de captures d’écran et, éventuellement, d’aperçus vidéo. Utilisez des images réelles de l’application en situation d’exécution, qui mettent en avant l’interaction de l’utilisateur et qui illustrent les fonctionnalités qui rendent votre application unique. Utilisez des vidéos d’aperçu de l’application pour donner aux utilisateurs une idée de son utilisation.

Voici les suggestions d’Apple pour effectuer les captures d’écran :

- Optimisez votre capture d’écran pour fournir la meilleure présentation possible sur les appareils iOS pris en charge par votre application, et vérifiez que le contenu est lisible.
- Évitez d’avoir un cadre autour de la capture d’écran qui corresponde à une image d’appareil iOS.
- Montrez l’affichage réel de votre application, en plein écran, sans graphismes ni bordures autour de la capture d’écran.
- Supprimez toujours la barre d’état des captures d’écran, car iTunes Connect attend des captures d’écran dont les dimensions excluent cette zone.
- Si possible, prenez des captures d’écran sur un véritable matériel iOS Retina, en haute résolution (pas dans un simulateur iOS).
- La première capture d’écran apparaît en tant que résultat de la recherche dans l’App Store sur les iPhone et iPad, si aucune vidéo d’aperçu de l’application n’est disponible. Vous devez donc placer la meilleure capture d’écran en premier.
- Utilisez les cinq captures d’écran pour décrire l’application tout en mettant en avant les points les plus intéressants.

Apple demande que les captures d’écran et les vidéos soient fournies dans toutes les tailles d’écran et de résolution prises en charge par votre application. De plus, vous devez également fournir des versions aux formats portrait et paysage, en fonction des orientations prises en charge.

Les tailles d’écran et résolutions suivantes sont demandées :

[!include[](~/ios/includes/table-itunesconnect.md)]

### <a name="editing-screenshots-in-itunes-connect"></a>Modification des captures d’écran dans iTunes Connect

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquer sur **Mes applications**.
2. Cliquez sur l’**icône** de votre application.
3. Sélectionnez l’onglet **Versions**.
4. Faites défiler l’affichage jusqu’à la section **Screenshots (Captures d’écran)**.
5. Sélectionnez la **Image Size (Taille de l’image)**, puis faites glisser les images nécessaires (jusqu’à 5 par taille d’écran) :

    [![Sélectionnez la taille de l’image et faites-la glisser dans les images requises](itunesconnect-images/screenshot01.png)](itunesconnect-images/screenshot01.png#lightbox)
6. Répétez l’opération pour toutes les tailles d’écran nécessaires.
7. Cliquez sur le bouton **Save (Enregistrer)** en haut de l’écran pour enregistrer vos changements.

> [!NOTE]
> Remarque : Apple rejettera la soumission de votre application si les captures d’écran ou la vidéo d’aperçu de l’application ne correspondent pas aux fonctionnalités réelles de cette dernière.

<a name="metadata"></a>

## <a name="managing-name-description-whats-new-keywords-and-urls"></a>Gestion du nom, de la description, des nouveautés, des mots clés et des URL

Cette section de l’enregistrement d’application iTunes Connect fournit des informations localisées sur l’application, ce qu’elle fait, les modifications apportées aux nouvelles versions, les mots clés utilisés dans les recherches, la prise en charge d’iAd, ainsi que les URL de prise en charge.

### <a name="app-name"></a>Nom de l'application

Choisissez un nom d’application descriptif qui reflète ce que fait votre application. Faites en sorte qu’il soit aussi bref que possible. Le nom de votre application joue un rôle essentiel dans la façon dont les utilisateurs recherchent et découvrent cette dernière, alors optez pour un nom simple et facile à retenir. Portez une attention particulière à la façon dont le nom apparaît sur un appareil iOS (iPad, iPhone et iPod touch).

Apple suggère de suivre les recommandations suivantes pour choisir le nom d’une application :

- Choisissez un nom court, simple et facile à retenir.
- Vérifiez qu’il n’enfreint pas les règles du copyright ou qu’il ne porte pas atteinte à la marque commerciale d’un tiers.
- Faites en sorte qu’il corresponde aux fonctionnalités de l’application.
- Fournissez des noms localisés pour les marchés étrangers, le cas échéant.

### <a name="description"></a>Description

Rédigez une description claire, concise et informative de l’application et de ses fonctionnalités. Les premières lignes sont les plus importantes. Elles vous permettent de faire une bonne première impression et d’attirer l’utilisateur. Décrivez ce qui rend votre application spéciale et la distingue des autres applications similaires.

Apple suggère ce qui suit pour rédiger la description d’une application :

- Incluez un ou deux brefs paragraphes d’ouverture et une courte liste à puces des principales fonctionnalités.
- Fournissez des descriptions localisées pour les marchés étrangers, le cas échéant.
- Incluez les avis, les félicitations ou les témoignages d’utilisateurs uniquement à la fin, voire pas du tout.
- Utilisez des sauts de ligne et des listes à puces pour améliorer la lisibilité.
- Tenez compte de la façon dont la description de l’application s’affiche dans l’App Store sur chaque type d’appareil, pour que les phrases les plus importantes de votre description soient immédiatement visibles.

### <a name="whats-new"></a>What's New

Durant le chargement d’une nouvelle version de votre application, un champ **What’s New in this Version (Nouveautés de cette version)** est disponible. Vous devez le remplir de manière approfondie et réfléchie.

Apple suggère les recommandations suivantes pour le remplissage des informations relatives aux nouveautés :

- Ajoutez des messages pour encourager les utilisateurs à effectuer les mises à jour.
- Dressez la liste des éléments par ordre d’importance. Mettez l’accent sur les changements apportés, ainsi que sur les résolutions de bogues.
- Présentez les changements dans un langage simple et authentique plutôt que dans un jargon technique.

### <a name="keywords"></a>Mots clés

En choisissant des mots clés réfléchis et stratégiques pour décrire les fonctionnalités de votre application, vous aidez les utilisateurs à la localiser facilement quand ils effectuent une recherche sur l’App Store. De plus, si votre application diffuse des annonces iAd, iAd Application Network utilise les mots clés pour choisir les annonces à cibler dans votre application.

Voici les suggestions d’Apple pour choisir les mots clés :

- N’utilisez pas de noms d’applications, de noms de sociétés ou de produits, ou de noms de marques appartenant à vos concurrents.
- N’utilisez pas de mots ou de termes génériques.
- Évitez les termes inappropriés ou répréhensibles, ou les mots non pertinents tels que les noms de célébrités.
- Localisez les mots clés pour les marchés étrangers, le cas échéant.

### <a name="urls"></a>URLs

Apple impose au développeur de fournir un lien vers son site web pour la prise en charge de tout problème ou de toute question qu’un utilisateur peut avoir concernant l’application. Apple impose également la présence d’un lien vers la politique de confidentialité de l’application (qui, encore une fois, doit être hébergée sur le site web du développeur).

Éventuellement, vous pouvez fournir un lien vers les informations marketing hébergées sur votre site web pour fournir des informations sur l’application, en plus de celles données dans l’App Store.

### <a name="editing-name-description-whats-new-keywords-and-urls-in-itunes-connect"></a>Modification du nom, de la description, des nouveautés, des mots clés et des URL dans iTunes Connect

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquer sur **Mes applications**.
2. Cliquez sur l’**icône** de votre application.
3. Sélectionnez l’onglet **Versions**.
4. Faites défiler l’affichage jusqu’à la section **Name (Nom)**.
5. Indiquez toutes les informations obligatoires :

    [![Modification du nom, de la description, des nouveautés, des mots clés et des URL dans iTunes Connect](itunesconnect-images/name01.png)](itunesconnect-images/name01.png#lightbox)
6. Cliquez sur le bouton **Save (Enregistrer)** en haut de l’écran pour enregistrer vos changements.

> [!IMPORTANT]
> Remarque : Apple rejettera la soumission de votre application si le nom, la description, les nouveautés, les mots clés ou les URL ne correspondent pas aux fonctionnalités réelles de l’application.

<a name="general"></a>

## <a name="maintaining-general-app-information"></a>Gestion des informations générales relatives à l’application

Cette section de l’enregistrement d’application iTunes Connect fournit l’ID unique de l’application (attribué par Apple), les catégories auxquelles l’application appartient, la classification, le copyright et les informations sur la société qui publie l’application.

### <a name="app-icon"></a>Icône d’application

> [!IMPORTANT]
> Les icônes d’application ne sont plus envoyées via iTunes Connect. Vous devez les soumettre via l’image **AppIcon** définie dans le fichier **Assets.xcassets** de votre projet. Pour plus d’informations, consultez le guide [Icône de l’App Store](~/ios/app-fundamentals/images-icons/app-store-icon.md).

L’icône d’application est le visage de votre application pour les utilisateurs, elle doit donc être mémorable et s’afficher correctement à une taille réduite. Les icônes mémorables sont nettes, simples et immédiatement reconnaissables.

Apple suggère de suivre les recommandations suivantes pour concevoir des icônes d’application :

- Créez une icône appropriée pour votre application.
- Créez une icône simple et cohérente avec la conception de votre application.
- Évitez d’utiliser des mots dans votre icône.
- Pensez de manière globale : une seule icône d’application est utilisée dans tous les App Store du monde.

Une image de 1 024 x 1 024 pixels est nécessaire pour l’icône d’application affichée dans l’App Store.

Pour plus d’informations, consultez [Recommandations sur l’interface humaine iOS](https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/MobileHIG/index.html#//apple_ref/doc/uid/TP40006556) et la description de la section consacrée aux grandes icônes d’application dans les [informations générales relatives à l’application](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Appendices/Properties.html#//apple_ref/doc/uid/TP40011225-CH26-SW7) au sein de la documentation d’Apple

### <a name="app-id"></a>ID de l’application

Il s’agit d’un numéro d’identification unique affecté à votre application par Apple durant la création de l’enregistrement iTunes Connect. Vous pouvez utiliser ce numéro quand vous appelez plusieurs interfaces web fournies par Apple, notamment les informations de l’App Store sur votre site web.

### <a name="version-number"></a>Numéro de version

Il s’agit de la version actuelle et active de votre application, telle qu’elle est visible par l’utilisateur dans l’App Store.

### <a name="category-and-sub-category"></a>Catégorie et sous-catégorie

La facilité avec laquelle votre application est découverte repose sur un aspect important, la catégorie où elle se trouve dans l’App Store. Les catégories permettent aux utilisateurs de parcourir une collection d’applications et de trouver celles qui les intéressent. iTunes Connect vous permet d’affecter jusqu’à deux catégories différentes quand vous définissez votre application. Veillez à choisir avec soin les catégories qui décrivent le mieux la fonction principale de votre application.

### <a name="ratings"></a>Évaluations

Toutes les applications doivent avoir une classification dans l’App Store. Cette classification sert de base au contrôle parental et permet de limiter l’accès des enfants aux applications en fonction de leur type et de leur contenu. Quand vous définissez votre application, iTunes Connect fournit une liste de descriptions de contenu à partir desquelles vous identifiez la fréquence d’affichage du contenu dans votre application. Ces sélections sont converties en classification affichée dans l’App Store.

Quand vous créez des applications pour enfants, l’App Store propose une catégorie spéciale destinée aux enfants de 11 ans et moins. Même si votre application n’est pas spécifiquement destinée aux enfants, vous aidez vos clients à faire de bons choix en leur fournissant des classifications de contenu appropriées.

> [!IMPORTANT]
> Remarque : Apple rejettera toute soumission d’application jugée obscène, pornographique, offensante ou diffamatoire.

### <a name="copyright-and-company-information"></a>Informations sur le copyright et la société

Apple vous permet de fournir des informations de copyright pour votre application et demande des informations sur la société responsable de sa publication, par exemple son adresse et ses informations de contact (ce qui est obligatoire pour les applications publiées sur l’App Store coréen). Ces informations sont affichées dans l’App Store en fonction des besoins.

### <a name="editing-general-app-information-in-itunes-connect"></a>Modification des informations générales de l’application dans iTunes Connect

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquer sur **Mes applications**.
2. Cliquez sur l’**icône** de votre application.
3. Sélectionnez l’onglet **Versions**.
4. Faites défiler l’affichage jusqu’à la section **General App Information (Informations générales relatives à l’application)**.
5. Indiquez toutes les informations obligatoires :

    [![Modification des informations générales de l’application dans iTunes Connect](itunesconnect-images/general01.png)](itunesconnect-images/general01.png#lightbox)
6. Cliquez sur le bouton **Edit (Modifier)** à côté de **Rating (Classification)** pour définir les informations de classification :

    [![Modification de l’évaluation](itunesconnect-images/general02.png)](itunesconnect-images/general02.png#lightbox)
7. Cliquez sur le bouton **Save (Enregistrer)** en haut de l’écran pour enregistrer vos changements.

> [!NOTE]
> Remarque : Apple rejettera la soumission de votre application si sa catégorie ou sa classification ne correspond pas aux fonctionnalités réelles de l’application.

<a name="game-center"></a>

## <a name="maintaining-game-center-information"></a>Gestion des informations relatives à Game Center

Pour les applications de jeu iOS prenant en charge le Game Center d’Apple, vous pouvez fournir des informations telles que les scores et les succès de l’utilisateur, et indiquer si l’application est compatible avec le mode multijoueur en réseau.

### <a name="editing-game-center-information-in-itunes-connect"></a>Modification des informations relatives à Game Center dans iTunes Connect

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquer sur **Mes applications**.
2. Cliquez sur l’**icône** de votre application.
3. Sélectionnez l’onglet **Versions**.
4. Faites défiler l’affichage jusqu’à la section **Game Center**.
5. Poussez le bouton de la section **Game Center** vers la position **On (Activé)**.
6. Indiquez toutes les informations obligatoires :

    [![Modification des informations relatives à Game Center dans iTunes Connect](itunesconnect-images/gamecenter01.png)](itunesconnect-images/gamecenter01.png#lightbox)
7. Cliquez sur le bouton **Save (Enregistrer)** en haut de l’écran pour enregistrer vos changements.

Utilisez l’onglet **Game Center** pour activer Game Center et gérer les **Leaderboards** ou **Achievements (Succès)** disponibles pour cette application :

[![Activer Game Center](itunesconnect-images/gamecenter02.png)](itunesconnect-images/gamecenter02.png#lightbox)

[![Conserver les Leaderboards ou les réalisations disponibles pour cette application](itunesconnect-images/gamecenter03.png)](itunesconnect-images/gamecenter03.png#lightbox)

## <a name="maintaining-app-review-information"></a>Gestion des informations relatives à l’évaluation de l’application

Utilisez cette section pour fournir les informations nécessaires au personnel d’Apple chargé d’évaluer votre application, par exemple les informations de contact (si le technicien a des questions), les comptes de démonstration nécessaires, ainsi que toutes les notes qui vont permettre au testeur d’évaluer correctement votre application.

### <a name="editing-app-review-information-in-itunes-connect"></a>Modification des informations relatives à l’évaluation de l’application dans iTunes Connect

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquer sur **Mes applications**.
2. Cliquez sur l’**icône** de votre application.
3. Sélectionnez l’onglet **Versions**.
4. Faites défiler l’affichage jusqu’à la section **App Review Information (Informations relatives à l’évaluation de l’application)**.
5. Indiquez toutes les informations obligatoires :

    [![Modification des informations relatives à l’évaluation de l’application dans iTunes Connect](itunesconnect-images/review01.png)](itunesconnect-images/review01.png#lightbox)
6. Indiquez la façon dont vous souhaitez que l’application soit publiée sur l’App Store une fois son évaluation réussie :

    [![Modification des informations de version dans iTunes Connect](itunesconnect-images/review02.png)](itunesconnect-images/review02.png#lightbox)
7. Cliquez sur le bouton **Save (Enregistrer)** en haut de l’écran pour enregistrer vos changements.

## <a name="maintaining-pricing-information"></a>Gestion des informations relatives à la tarification

Si vous comptez publier une application payante, vous devez définir le prix de vente en sélectionnant l’un des niveaux tarifaires proposés par Apple, ainsi que sa date d’entrée en vigueur. Par exemple, au moment de la rédaction de ce document, la tarification **Tier 1 (Niveau 1)** ressemble à ce qui suit :

[![Gestion des informations relatives à la tarification](itunesconnect-images/price01.png)](itunesconnect-images/price01.png#lightbox)

### <a name="educational-discount"></a>Remise pour le secteur de l’éducation

Cochez cette case, si vous souhaitez que votre application soit proposée à prix réduit aux établissements d’enseignement quand ils achètent plusieurs exemplaires à la fois. Les détails de la réduction figurent dans le dernier **Contrat d’application payante**, que vous devez signer pour rendre votre application accessible aux clients du secteur de l’éducation.

### <a name="custom-business-to-business-application"></a>Application interentreprises personnalisée

Une application configurée comme une **application métier personnalisée à l’entreprise** est uniquement disponible pour les clients du **programme d’achat en volume** que vous spécifiez dans iTunes Connect. elle ne sera disponible que dans les territoires applicables (par exemple, les clients du programme d’achat en volume des États-Unis doivent utiliser le programme d’achat en volume de l’App Store pour entreprises).

Les applications interentreprises personnalisées ne sont pas accessibles aux établissements d’enseignement, ni aux clients classiques de l’App Store. Pour en savoir plus sur le * Programme d’achat en volume de l’App Store pour entreprises*, consultez la page des [Questions fréquentes (FAQ)](https://vpp.itunes.apple.com/faq) d’Apple. Pour en savoir plus sur la façon dont vos clients peuvent s’inscrire au **Programme d’achat en volume**, consultez la page relative aux [programmes de déploiement](https://enroll.vpp.itunes.apple.com) d’Apple.

### <a name="editing-pricing-information-in-itunes-connect"></a>Modification des informations relatives à la tarification dans iTunes Connect

Effectuez ce qui suit dans [iTunes Connect](https://itunesconnect.apple.com/WebObjects/iTunesConnect.woa) :

1. Cliquer sur **Mes applications**.
2. Cliquez sur l’**icône** de votre application.
3. Sélectionnez l’onglet **Pricing (Tarification)**  :

    [![Modification des informations de tarification dans iTunes Connect](itunesconnect-images/price02.png)](itunesconnect-images/price02.png#lightbox)
4. Sélectionnez une **Availability Date (Date de disponibilité)**.
5. Sélectionnez le prix souhaité dans la liste déroulante **Price Tier (Niveau de prix)**.
6. Activez éventuellement **Educational Discounts (Remise pour le secteur de l’éducation)**.
7. Définissez éventuellement l’application en tant que **Custom Business to Business Application (Application interentreprises personnalisée)**.
8. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.

<a name="iap"></a>

## <a name="maintaining-in-app-purchase-information"></a>Gestion des informations relatives aux achats dans l’application

Si vous comptez vendre des produits virtuels dans l’application, à partir de votre application (par exemple, de nouveaux niveaux de jeu ou des fonctionnalités d’application), utilisez cette section pour créer et gérer les éléments d’achat nécessaires.

[![Gestion des informations sur les achats dans l’application](itunesconnect-images/inapp01.png)](itunesconnect-images/inapp01.png#lightbox)

Pour plus d’informations sur l’utilisation des achats dans l’application au sein d’une application Xamarin.iOS, consultez la documentation sur les [achats dans l’application](~/ios/platform/in-app-purchasing/index.md).

## <a name="viewing-application-reviews"></a>Affichage des avis relatifs à l’application

Une fois que votre application a été publiée sur l’App Store, les utilisateurs qui l’ont achetée ou téléchargée gratuitement peuvent rédiger des avis et laisser des évaluations (étoiles). Utilisez cette section pour consulter ces avis. Par exemple :

[![Affichage des révisions d’application](itunesconnect-images/reviews01.png)](itunesconnect-images/reviews01.png#lightbox)

## <a name="summary"></a>Récapitulatif

Cet article explique comment utiliser iTunes Connect pour préparer une application Xamarin.iOS à être publiée sur l’App Store, et comment gérer toutes les informations affichées concernant votre application dans l’App Store.

## <a name="related-links"></a>Liens associés

- [Utilisation d’images](~/ios/app-fundamentals/images-icons/index.md)
- [Guide du workflow de développement d’applications iOS : distribution d’applications](https://developer.apple.com/library/ios/#documentation/Xcode/Conceptual/ios_development_workflow/35-Distributing_Applications/distributing_applications.html)
- [Conseils pour la soumission à l’App Store](https://developer.apple.com/appstore/resources/submission/tips.html)
- [Recommandations pour l’évaluation dans l’App Store](https://developer.apple.com/appstore/resources/approval/guidelines.html)
- [Guide du développeur iTunes Connect](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/About.html#//apple_ref/doc/uid/TP40011225-CH1-SW1)
