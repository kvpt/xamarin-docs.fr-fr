---
title: Modifications apportées à StoreKit dans iOS 6
description: 'iOS 6 introduit deux modifications à l’API du Kit de Store : la possibilité d’afficher iTunes (et l’App Store/iBookstore) option où Apple hébergera vos fichiers téléchargeables d’achat de produits à partir de votre application et une nouveau dans l’application. Ce document explique comment implémenter ces fonctionnalités avec Xamarin.iOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 35bac91e54181753bd1f3fd8b4cf0b851bfa1882
ms.sourcegitcommit: 2eb8961dd7e2a3e06183923adab6e73ecb38a17f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2019
ms.locfileid: "66827498"
---
# <a name="changes-to-storekit-in-ios-6"></a>Modifications apportées à StoreKit dans iOS 6

_iOS 6 a introduit deux modifications à l’API du Kit de Store : la possibilité d’afficher iTunes (et l’App Store/iBookstore) option où Apple hébergera vos fichiers téléchargeables d’achat de produits à partir de votre application et une nouveau dans l’application. Ce document explique comment implémenter ces fonctionnalités avec Xamarin.iOS._

Les principales modifications au Kit de Store dans iOS6 sont ces deux nouvelles fonctionnalités :

- **Affichage de contenu dans l’application & Purchasing** : les utilisateurs peuvent acheter et télécharger des applications, de musique, la documentation et autres iTunes de contenu sans quitter votre application. Vous pouvez également lier à vos propres applications pour promouvoir des achats ou simplement encourager les évaluations et les révisions.
- **Le contenu hébergé d’achat dans l’application** – Apple stocke et fournir le contenu associé à vos produits achat dans l’application, ce qui élimine le besoin d’un serveur distinct héberger vos fichiers, automatiquement prend en charge le téléchargement en arrière-plan et vous permet de écrire moins de code.

Reportez-vous à la [In-App Purchase](~/ios/platform/in-app-purchasing/index.md) guides pour une présentation détaillée des APIs StoreKit.

## <a name="requirements"></a>Configuration requise

Les fonctionnalités de Store Kit présentées dans ce document requièrent iOS 6 et Xcode 4.5, ainsi que de Xamarin.iOS 6.0.

## <a name="in-app-content-display--purchasing"></a>Affichage de contenu dans l’application et les achats

La nouvelle fonctionnalité d’achat dans l’application dans iOS permet aux utilisateurs d’afficher des informations sur les produits et acheter ou télécharger le produit à partir de votre application.
Précédemment applications devrait déclencher iTunes, l’App Store ou l’iBookstore, ce qui entraînerait l’utilisateur en laissant l’application d’origine. Cette nouvelle fonctionnalité renvoie automatiquement l’utilisateur à votre application lorsqu’elles sont effectuées.

[![](changes-to-storekit-images/image1.png "Renvoyer automatiquement à une application après l’achat")](changes-to-storekit-images/image1.png#lightbox)

Voici quelques exemples de la façon dont il peut être utilisé :

- **Inciter les utilisateurs à évaluer votre application** – vous pouvez ouvrir la page de l’App Store afin que l’utilisateur peut évaluer et passez en revue votre application sans quitter ce dernier.
- **Promotion d’entre les applications** – autoriser l’utilisateur Voir d’autres applications que vous publiez, avec la possibilité d’acheter/télécharger immédiatement.
- **Aider les utilisateurs de rechercher et de télécharger du contenu** : aider les utilisateurs à acheter le contenu que votre application de recherche, gère ou agrège (par ex. une application liées à la musique peut fournir une sélection de chansons et autoriser chaque chanson dans les quatre-vingt-dix à partir de l’application).

Une fois le `SKStoreProductViewController` a été affiché l’utilisateur peut interagir avec les informations de produit comme s’ils étaient dans iTunes, l’App Store ou l’iBookstore. L’utilisateur peut :

- Captures d’écran de la vue (pour les applications),
- Chansons d’exemple ou une vidéo (pour la musique, émissions de télévision et films),
- Révisions de lecture (et d’écriture),
- Acheter et télécharger, ce qui se passe entièrement dans le Kit de Store et le contrôleur d’affichage.

Certaines options dans le `SKStoreProductViewController` seront toujours forcer l’utilisateur pour laisser votre application et ouvrez l’application de banque pertinente, telles qu’un clic sur **produits connexes** ou d’une application **prise en charge** lien.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

L’API pour afficher un produit dans n’importe quelle application est simple : il suffit de créer et d’afficher un `SKStoreProductViewController`. Suivez ces étapes pour créer et afficher un produit :

1. Créer un `StoreProductParameters` objet à passer des paramètres pour le contrôleur d’affichage, y compris le `productId` dans le constructeur.
1. Instanciez le `SKProductViewController`. Affecter à un champ de niveau classe.
1. Affecter un gestionnaire pour le contrôleur d’affichage `Finished` événement, qui devrait faire disparaître le contrôleur d’affichage. Cet événement est appelé lorsque l’utilisateur appuie sur Annuler ; ou sinon finalise une transaction à l’intérieur du contrôleur d’affichage.
1. Appelez le `LoadProduct` méthode en passant le `StoreProductParameters` et un gestionnaire d’achèvement. Le Gestionnaire d’achèvement doit vérifier que la demande de produit a été correctement et si tel est le cas, présenter le `SKProductViewController` modal. Gestion des erreurs doit être ajoutée au cas où le produit ne peut pas être récupéré.

### <a name="example"></a>Exemple

Le *ProductView* de projet dans le *StoreKit* exemple de code pour cet article implémente un `Buy` méthode qui accepte n’importe quel produit de l’ID Apple et affiche le `SKStoreProductViewController`. Le code suivant affiche les informations de produit pour n’importe quel ID Apple donné :

```csharp
void Buy (int productId)
{
    var spp = new StoreProductParameters(productId);
    var productViewController = new SKStoreProductViewController ();
    // must set the Finished handler before displaying the view controller
    productViewController.Finished += (sender, err) => {
        // Apple's docs says to use this method to close the view controller
        this.DismissModalViewControllerAnimated (true);
    };
    productViewController.LoadProduct (spp, (ok, err) => { // ASYNC !!!
        if (ok) {
            PresentModalViewController (productViewController, true);
        } else {
            Console.WriteLine (" failed ");
            if (err != null)
                Console.WriteLine (" with error " + err);
        }
    });
}
```

L’application se présente comme la capture d’écran ci-dessous lors de l’exécution – téléchargement ou en achetant se produit entièrement dans le `SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "L’application se présente comme suit lors de l’exécution")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Prise en charge des systèmes d’exploitation plus anciens

L’exemple d’application inclut le code qui montre comment ouvrir le Store de l’application, iTunes ou l’iBookstore dans les versions antérieures d’e/s. Utilisez le `OpenUrl` méthode pour ouvrir correctement conçu **itunes.com** URL.

Vous pouvez implémenter une vérification de version pour déterminer quel code à exécuter, comme illustré ici :

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (6,0)) {
    // do iOS6+ stuff, using SKStoreProductViewController as shown above
} else {
    // don't do stuff requiring iOS 6.0, use the old syntax 
    // (which will take the user out of your app)
    var nsurl = new NSUrl("http://itunes.apple.com/us/app/angry-birds/id343200656?mt=8");
    UIApplication.SharedApplication.OpenUrl (nsurl);
}
```

### <a name="errors"></a>Erreurs

L’erreur suivante se produit si l’ID Apple que vous utilisez n’est pas valide, ce qui peut prêter à confusion, car elle implique un problème réseau ou d’authentification quelconque.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Lire la Documentation Objective-C

Les développeurs de la lecture sur le Kit de Store sur le portail des développeurs d’Apple verront un protocole – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) : nous l’avons vu par rapport à cette nouvelle fonctionnalité. Le protocole de délégué n'a qu’une seule méthode – productViewControllerDidFinish – a été exposée en tant que le `Finished` événement sur le `SKStoreProductViewController` dans Xamarin.iOS.

## <a name="determining-apple-ids"></a>Détermination des ID Apple

L’ID Apple requis par le `SKStoreProductViewController` est un *nombre* (à ne pas confondre avec les ID de Bundle comme « com.xamarin.mwc2012 »). Il existe plusieurs façons différentes, que vous pouvez trouver l’ID Apple pour les produits que vous souhaitez afficher, répertoriées ci-dessous :

### <a name="itunesconnect"></a>iTunesConnect

Pour les applications que vous publiez, il est facile de trouver le **ID Apple** dans iTunes Connect :

[![](changes-to-storekit-images/image3.png "Recherche l’ID Apple dans iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API de recherche

Apple fournit une API de recherche dynamique pour interroger tous les produits dans l’App Store, iTunes et l’iBookstore. Vous trouverez des informations sur la façon d’accéder à l’API de recherche dans [associée ressources d’Apple](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html), bien que l’API est exposée à toute personne (pas simplement des filiales). Le code JSON obtenu peut être analysé pour découvrir la `trackId` qui est l’ID Apple à utiliser avec `SKStoreProductViewController`.

Les résultats inclura également les autres métadonnées notamment afficher des informations et les URL de conception graphique qui peuvent être utilisées pour restituer le produit dans votre application.

Voici quelques exemples :

- **application iBook** – [ http://itunes.apple.com/search?term=ibooks&amp; entité = logiciel&amp;country = us](http://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Point et l’iBook kangourou** – [ http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; entité = livre électronique&amp;country = us](http://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Flux de partenaire d’entreprise

Apple offre aux partenaires approuvés avec un vidage de données complète de leurs produits, sous la forme de fichiers plats de prêt à la base de données téléchargeables. Si vous êtes éligible pour l’accès à la [entreprise partenaire flux](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-enterprise-partner-feed.html), puis vous trouverez l’ID Apple pour n’importe quel produit dans ce jeu de données.

Nombre d’utilisateurs du flux d’entreprise partenaire est membres de la [au programme d’affiliation](http://www.apple.com/itunes/affiliates) qui autorise des commissions pour gagner des ventes de produits. `SKStoreProductViewController` ne prend pas en charge les applications associées à un ID (au moment de l’écriture).

### <a name="direct-product-links"></a>Produit liens directs

L’ID Apple pour un produit peut être déduit à partir de son lien de l’URL d’aperçu d’iTunes.
Dans n’importe quel iTunes liens produit (pour les applications, de musique ou de la documentation) Recherchez la partie de l’URL commençant par `id` et utilisez le numéro qui suit.

Par exemple, le lien direct vers iBooks est

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

et l’ID Apple est **364709193**. De même pour l’application MWC2012, le lien direct est

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

et l’ID Apple est **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Achat dans l’application de contenu hébergé

Si vos achats dans l’application de contenu téléchargeable (par exemple, la documentation ou autre support, jeu illustration du niveau et la configuration ou autres fichiers volumineux) sont ensuite ces fichiers utilisé pour être hébergé sur votre serveur web et applications devaient incorporer du code pour les télécharger en toute sécurité après acheter. À compter d’iOS 6, Apple hébergera vos fichiers sur leurs serveurs, éliminant la nécessité d’un serveur distinct. La fonctionnalité est uniquement disponible pour les produits Non consommables (pas consommables ou abonnements). À l’aide du service d’hébergement d’Apple présente les avantages suivants :

- Réduisez les coûts d’hébergement et de la bande passante.
- Probablement plus évolutif que l’hôte serveur qui vous utilisez actuellement. 
- Moins de code à écrire, étant donné que vous n’êtes pas obligé de créer n’importe quel traitement côté serveur. 
- Téléchargement en arrière-plan est implémenté pour vous.

Remarque : test hébergé contenu achat dans l’application dans iOS que Simulator n’est pas pris en charge, donc vous devez tester avec un appareil réel.

### <a name="hosted-content-basics"></a>Principes fondamentaux de contenu hébergé

Avant iOS 6, il y avait deux façons de fournir un produit (décrit plus en détail dans [In-App Purchase de Xamarin](~/ios/platform/in-app-purchasing/index.md) documentation) :

- **Produits intégrés** – fonctionnalités qui sont déverrouillées en achetant, mais qui sont intégrées à l’application (soit en tant que code, ou des ressources incorporées). Filtres de photo déverrouillés ou dans le jeu power-ups constituent des exemples de produits intégrés.
- **Produits assurée par le serveur** – après l’achat, l’application doit télécharger le contenu d’un serveur qui vous travaillez. Ce contenu est téléchargé lors de l’achat, stocké sur l’appareil et ensuite restitué dans le cadre de la fourniture du produit. Exemples : la documentation, problèmes magazines ou les niveaux de jeu se composent de fichiers art et de configuration d’arrière-plan.

Dans iOS Apple 6 offre une variation de produits assurée par le serveur : ils hébergera vos fichiers de contenu sur leurs serveurs. Cela rend beaucoup plus simple pour générer des produits assurée par le serveur, car vous n’êtes pas obligé de faire fonctionner un serveur distinct, et Store Kit fournit des fonctionnalités de téléchargement en arrière-plan que vous aviez précédemment d’écrire vous-même. Pour tirer parti de l’hébergement d’Apple, activer l’hébergement de contenu pour les nouveaux produits d’achat dans l’application et modifier votre code Store Kit pour tirer parti de celui-ci. Les fichiers de contenu de produit sont créés à l’aide de Xcode, puis téléchargés sur les serveurs d’Apple pour révision et mise en production.

[![](changes-to-storekit-images/image4.png "Le processus de génération et de remise")](changes-to-storekit-images/image4.png#lightbox)

À l’aide de l’App Store pour fournir des achats dans l’application *avec contenu hébergé* nécessite l’installation et la configuration suivante :

- **iTunes Connect** – vous *doit* proposent vos informations fiscales et bancaires à Apple afin qu’ils peuvent reverser des fonds collectées à votre place. Vous pouvez ensuite configurer les produits pour vendre et configurer des comptes d’utilisateur de bac à sable pour tester des achats.  _Vous devez également configurer le contenu hébergé pour les produits non consommables dont vous souhaitez héberger auprès d’Apple_.
- **Portail de provisionnement iOS** : création d’un identificateur de Bundle et activation de l’accès d’App Store pour votre application, comme vous le feriez pour n’importe quelle application qui prend en charge les achats dans l’application.
- **Store Kit** – Ajout de code à votre application pour l’affichage des produits, l’achat de produits et la restauration des transactions.  _IOS 6 Store Kit sera également gérer le téléchargement de contenu de votre produit, en arrière-plan, avec les mises à jour de progression._
- **Code personnalisé** – pour suivre les achats effectués par les clients et indiquer les produits ou services qu’ils ont achetés. Utiliser les nouvelles classes de Store Kit iOS 6 comme `SKDownload` pour récupérer le contenu hébergé par Apple.

Les sections suivantes expliquent comment implémenter le contenu hébergé, à partir de la création et téléchargement du package à la gestion de l’achat et de télécharger des processus, à l’aide de l’exemple de code pour cet article.

### <a name="sample-code"></a>Exemple de code

L’exemple de projet *HostedNonConsumables* (dans StoreKitiOS6.zip) utilise le contenu hébergé. L’application offre deux « chapitres » à la vente, le contenu pour lequel est hébergé sur des serveurs d’Apple. Le contenu se compose d’un fichier texte et une image, bien que le contenu beaucoup plus complexe pourrait être utilisé dans une application réelle.

Avant, pendant et après un achat, l’application se présente comme suit :

 [![](changes-to-storekit-images/image5.png "L’application se présente comme suit avant, pendant et après un achat")](changes-to-storekit-images/image5.png#lightbox)

Le fichier texte et l’image soient téléchargés et copiés dans le répertoire Documents de l’application. Pour plus d’informations sur les différents annuaires disponibles pour le stockage de l’application, consultez le [documentation du système de fichiers](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Lorsque la création de nouveaux produits qui utilisent Apple le contenu de hébergement, veillez à sélectionner le **Non consommable** type de produit. Autres types de produits ne prennent pas en charge l’hébergement de contenu. En outre, vous ne devez pas activer hébergement de contenu pour *existant* produits que vous vendez ; activez uniquement sur l’hébergement de contenu pour les nouveaux produits.

 [![](changes-to-storekit-images/image6.png "Sélectionnez le type de produit Non consommable")](changes-to-storekit-images/image6.png#lightbox)

Entrez un **Id_produit**. Cet ID sera ultérieurement requis lorsque vous créez le contenu pour ce produit.

 [![](changes-to-storekit-images/image7.png "Entrez un ID de produit")](changes-to-storekit-images/image7.png#lightbox)

Hébergement de contenu est définie dans la section Détails. Avant l’achat dans l’application procèdent, désactivez le **héberger du contenu auprès d’Apple** case à cocher si vous souhaitez annuler (même si vous avez téléchargé du contenu test). Toutefois hébergement de contenu ne peut pas être supprimé après que l’achat dans l’application a été activée.

 [![](changes-to-storekit-images/image8.png "Hébergement de contenu auprès d’Apple")](changes-to-storekit-images/image8.png#lightbox)

Une fois que vous avez activé sur l’hébergement de contenu, le produit passe **en attente de téléchargement** état et afficher ce message :

 [![](changes-to-storekit-images/image9.png "Le produit sera entrer en attente pour l’état de chargement et afficher ce message")](changes-to-storekit-images/image9.png#lightbox)

Le package de contenu doit être créé avec Xcode et téléchargées à l’aide de l’outil d’archivage. Instructions pour la création de packages de contenu sont indiquées dans la section suivante **création. Les fichiers PKG**.

## <a name="creating-pkg-files"></a>Création. Fichiers de package

Les fichiers de contenu que vous téléchargez vers Apple doivent respecter les restrictions suivantes :

- Ne peut pas dépasser 2 Go.
- Ne peut pas contenir le code exécutable (ou des liens symboliques qui pointent en dehors du contenu).
- Doit être correctement mis en forme (y compris un **.plist** fichier) et avoir un **.pkg** extension de fichier. Cela se fera automatiquement si vous suivez ces instructions à l’aide de Xcode.

Vous pouvez ajouter de nombreux fichiers différents et les types de fichiers, tant qu’ils répondent à ces restrictions. Le contenu est compressé avant la distribution dans votre application et décompressé par Store Kit avant que votre code n’y accède.

Après avoir chargé un package de contenu, il peut être remplacé avec un contenu plus récente. Nouveau contenu doit être chargé et soumis pour validation par le biais de la procédure normale. Incrément le `ContentVersion` champ dans les packages de contenu mis à jour pour indiquer qu’il est plus récent.

### <a name="xcode-in-app-purchase-content-projects"></a>Projets Xcode dans l’application bon contenu

Création de packages de contenu pour les produits d’achat dans l’application actuellement nécessite Xcode. Il est non OBJECTIVE-C codage requis ; Xcode a un nouveau type de projet pour ces packages qui contient simplement vos fichiers et un fichier plist.

Notre exemple d’application a des chapitres du livre à la vente : chaque package de contenu chapitre contiendra :

-  un fichier texte, et
-  une image pour représenter le chapitre.


Commencez par sélectionner **fichier > Nouveau projet** dans le menu, puis en choisissant **In-App Purchase contenu**:

 [![](changes-to-storekit-images/image10.png "Choisir le contenu de l’achat dans l’application")](changes-to-storekit-images/image10.png#lightbox)

Entrez le **Product Name** et **identifiant de la société** telles que le **identificateur de Bundle** correspond à la **Id_produit** que vous avez entré dans iTunes Se connecter pour ce produit.

[![](changes-to-storekit-images/image11.png "Entrez le nom et l’identificateur")](changes-to-storekit-images/image11.png#lightbox)

À présent avoir une valeur vide **In-App Purchase contenu** projet. Vous pouvez cliquer sur et **ajouter des fichiers...** ou faites-les glisser dans le **Project Navigator**. Vérifiez que le **ContentVersion** est correct (il doit commencer à 1.0, mais si vous souhaitez ultérieurement mettre à jour votre contenu, pensez à incrémenter).

Cette capture d’écran montre Xcode avec les fichiers de contenu inclus dans le projet et les entrées de plist visibles dans la fenêtre principale :

[![](changes-to-storekit-images/image12.png "Cette capture d’écran montre Xcode avec les fichiers de contenu inclus dans le projet et les entrées de plist visibles dans la fenêtre principale")](changes-to-storekit-images/image12.png#lightbox)

Une fois que vous avez ajouté tous vos fichiers de contenu vous pouvez enregistrer ce projet et modifiez-le plus tard ou commencer le processus de téléchargement.

## <a name="uploading-pkg-files"></a>Téléchargement. Fichiers de package

Le moyen le plus simple pour télécharger les packages de contenu est avec la **Xcode Archive outil**. Choisissez **produit > Archive** dans le menu pour commencer :

![](changes-to-storekit-images/image13.png "Choisissez Archiven")

Le package de contenu s’affiche alors dans l’archive, comme indiqué ci-dessous. Le type d’archive et l’icône affichent cette ligne est un **archives de contenu dans l’application achat**. Cliquez sur **valider...** Pour vérifier notre package de contenu pour les erreurs sans réellement effectuer le téléchargement.

[![](changes-to-storekit-images/image14.png "Valider le package")](changes-to-storekit-images/image14.png#lightbox)

Connexion avec vos informations d’identification de connexion iTunes :

[![](changes-to-storekit-images/image15.png "Connexion avec vos informations d’identification de connexion d’iTunes")](changes-to-storekit-images/image15.png#lightbox)

Choisissez l’application correcte et dans l’application achat pour associer ce contenu avec :

[![](changes-to-storekit-images/image16.png "Choisir la bonne application et l’achat dans l’application à associer ce contenu avec")](changes-to-storekit-images/image16.png#lightbox)

Vous devez voir un message similaire à cette capture d’écran :

![Un exemple aucun message de problèmes](changes-to-storekit-images/image17.png "un exemple aucun message de problèmes")

À présent passer par un processus similaire, mais en cliquant sur **distribuer...** réellement pour télécharger le contenu.

[![Distribuer l’application](changes-to-storekit-images/image18.png "distribuer l’application")](changes-to-storekit-images/image18.png#lightbox)

Sélectionnez la première option, pour télécharger le contenu :

![Télécharger le contenu](changes-to-storekit-images/image19.png "télécharger le contenu")

Connectez-vous à nouveau :

[![](changes-to-storekit-images/image15.png "Connectez-vous")](changes-to-storekit-images/image15.png#lightbox)

Choisissez l’application correcte et enregistrement de l’achat dans l’application pour charger le contenu à :

[![](changes-to-storekit-images/image20.png "Choisissez l’enregistrement de bon d’application et dans l’application")](changes-to-storekit-images/image20.png#lightbox)

Patientez pendant que vos fichiers sont téléchargés :

[![](changes-to-storekit-images/image21.png "La boîte de dialogue de téléchargement du contenu")](changes-to-storekit-images/image21.png#lightbox)

Lorsque le téléchargement est terminé, un message s’affiche vous informe que le contenu a été envoyé à l’App Store.

[![](changes-to-storekit-images/image22.png "Un exemple de message de téléchargement réussi")](changes-to-storekit-images/image22.png#lightbox)

Une fois que qui a été effectué, lorsque vous revenez à la page du produit sur iTunes Connect, il affiche les détails du package et se trouver dans **prêt à envoyer** état. Lorsque le produit est dans cet état, vous pouvez commencer à tester dans l’environnement de bac à sable. Il est inutile de « submit » du produit pour le test dans le bac à sable.

[![](changes-to-storekit-images/image23.png "iTunes Connect, il affiche les détails du package et être prêt à l’état de l’envoi")](changes-to-storekit-images/image23.png#lightbox)

Il peut prendre un certain temps (par exemple). quelques minutes) entre le chargement de l’archive et l’état dans iTunes Connect mis à jour. Vous pouvez soumettre le produit pour la révision séparément, ou la soumettre conjointement avec un binaire d’application. Uniquement une fois que Apple a approuvé officiellement le contenu qu’il sera disponible dans l’App Store de production à l’achat dans votre application.

### <a name="pkg-file-format"></a>Format de fichier PKG

Pour créer et charger un package de contenu hébergé à l’aide de Xcode et l’outil Archive signifie que vous verrez jamais le contenu du package lui-même. Les fichiers et répertoires dans les packages créés pour l’application d’exemple ressemble la capture d’écran ci-dessous, avec le **plist** fichier dans la racine et les fichiers du produit dans un **contenu** sous-répertoire :

[![](changes-to-storekit-images/image24.png "Le fichier plist dans la racine et les fichiers du produit dans un sous-répertoire de contenu")](changes-to-storekit-images/image24.png#lightbox)

Notez la structure de répertoires du package (en particulier l’emplacement des fichiers dans le `Contents` sous-répertoire), car vous devrez comprendre ces informations pour extraire les fichiers à partir du package sur l’appareil.

### <a name="updating-package-content"></a>La mise à jour le contenu du Package

La procédure de mise à jour de contenu une fois celle-ci approuvée :

- Modifier le projet dans l’application fournisseur contenu dans Xcode.
- Augmentez le numéro de version.
- Charger à nouveau dans iTunes Connect. Les acheteurs suivantes obtient automatiquement la dernière version, mais les utilisateurs ayant déjà l’ancienne version ne recevront pas de notifications.
- Votre application est responsable de la notification des utilisateurs et les encourager à récupérer une version plus récente du contenu. L’application doit également créer une fonction qui télécharge la nouvelle version, à l’aide de la fonctionnalité de restauration du Store Kit.
- Pour déterminer si une version plus récente existe, vous pouvez créer une fonctionnalité dans votre application de récupérer les SKProducts (par exemple). même les processus qui sont utilisé pour récupérer les prix des produits) et de comparer la propriété ContentVersion.

## <a name="purchasing-overview"></a>Vue d’ensemble d’achat

Avant de lire cette section, passez en revue les existantes [documentation d’achat dans l’application](~/ios/platform/in-app-purchasing/index.md).

Vous pouvez acheter de la séquence d’événements qui se produit lorsqu’un produit avec le contenu hébergé et téléchargement est illustré dans ce diagramme :

[![](changes-to-storekit-images/image25.png "La séquence d’événements qui se produit lorsqu’un produit avec le contenu hébergé est achetée et télécharger")](changes-to-storekit-images/image25.png#lightbox)

1. Nouveaux produits peuvent être créés dans iTunes Connect avec le contenu hébergé activé. Le contenu réel est construit séparément dans Xcode (en tant que simplement en faisant glisser les fichiers dans un dossier) puis archivé et chargé sur iTunes (aucun codage n’est requis). Chaque produit est ensuite soumis pour approbation, après quoi elle deviendra disponible à l’achat. Dans l’exemple de code, ces ID de produit sont codées en dur, mais qui héberge le contenu auprès d’Apple est plus souple si vous stockez la liste des produits disponibles sur un serveur distant afin que vous pouvez mettre à jour lorsque vous envoyez de nouveaux produits et du contenu à iTunes Connect.
1. Lorsque l’utilisateur achète un produit, une transaction est placée dans la file d’attente de paiement pour le traitement.
1. Store Kit transfère la demande d’achat aux serveurs iTunes pour traitement.
1. Transaction est effectuée sur les serveurs iTunes (par ex. la charge du client) et un accusé de réception est renvoyé à l’application, avec les informations de produit attachées notamment s’il est téléchargeable (et dans ce cas, la taille du fichier et autres métadonnées).
1. Votre code doit vérifier si le produit est téléchargeable et si tel est le cas effectuer une demande de téléchargement du contenu qui est aussi placée dans la file d’attente de paiement. Store Kit envoie cette demande vers les serveurs d’iTunes.
1. Serveur retourne un fichier de contenu au Store Kit, qui fournit un rappel pour retourner la progression du téléchargement et le temps restant estimé à votre code.
1. Une fois terminé, vous obtenez notifié et passé d’un emplacement de fichier dans le dossier du Cache.
1. Votre code doit copier les fichiers et les vérifier, enregistrer n’importe quel état dont vous avez besoin de se rappeler que le produit a été acheté. Profitez de cette occasion pour définir l’indicateur de sauvegarde correctement sur les nouveaux fichiers (indicateur : s’ils proviennent d’un serveur et ne sont jamais modifiés par l’utilisateur, vous devez probablement ignorer les sauvegardant, étant donné que l’utilisateur peut toujours les récupérer à partir de serveurs Apple dans les futures).
1. Appelez FinishTransaction. Cette étape est importante car elle supprime la transaction à partir de la file d’attente de paiement. Il est également important que vous n’appelez pas FinishTransaction jusqu'à ce que, après avoir copié le contenu du répertoire de la mémoire Cache. Une fois que vous appelez FinishTransaction, les fichiers mis en cache sont susceptibles d’être purgées rapidement.

## <a name="implementing-hosted-content-purchase"></a>Implémentation de fournisseur de contenu hébergé

Les informations suivantes doivent être lue conjointement avec l’ensemble [documentation des achats dans l’application](~/ios/platform/in-app-purchasing/index.md). Les informations contenues dans ce document se concentre sur les différences entre le contenu hébergé et l’implémentation précédente.

### <a name="classes"></a>Classes

Les classes suivantes ont été ajoutées ou modifiées au contenu de prise en charge hébergé dans iOS 6 :

- **SKDownload** – nouvelle classe qui représente un téléchargement en cours. L’API permet plusieurs par produit, mais initialement un seul a été implémenté.
- **SKProduct** – ajoutées de nouvelles propriétés : `Downloadable`, `ContentVersion`, `ContentLengths` tableau.
- **SKPaymentTransaction** – nouvelle propriété ajoutée : `Downloads`, qui contient une collection de `SKDownload` objets si ce produit a hébergé le contenu disponible pour téléchargement.
- **SKPaymentQueue** – nouvelle méthode ajoutée : `StartDownloads`. Appelez cette méthode avec `SKDownload` objets pour extraire son contenu hébergé. Téléchargement peut se produire en arrière-plan.
- **SKPaymentTransactionObserver** – nouvelle méthode : `UpdateDownloads`. Store Kit appelle cette méthode avec des informations de progression sur les opérations de téléchargement en cours.

Détails de la nouvelle `SKDownload` classe :

- **Progression** – une valeur comprise entre 0-1 et que vous pouvez utiliser pour afficher un indicateur de pourcentage d’achèvement à l’utilisateur. N’utilisez pas de progression == 1 pour détecter si le téléchargement est terminé, accédez à état == terminé.
- **TimeRemaining** – estimation du téléchargement temps restant, en secondes. -1 signifie qu’il est toujours calcul de l’estimation.
- **État** : actif, en attente, terminé, échec, suspendu, annulé.
- **ContentURL** – fichier emplacement où le contenu a été placé sur le disque, dans le `Cache` directory. Rempli uniquement une fois le téléchargement terminé.
- **Erreur** – Vérifiez que cette propriété si l’état est Failed.

Les interactions entre les classes dans l’exemple de code sont affichées dans ce diagramme (le code spécifique aux achats contenus hébergés est représentée en vert) :

[![](changes-to-storekit-images/image26.png "Les achats de contenu hébergés est indiqué en vert dans ce diagramme")](changes-to-storekit-images/image26.png#lightbox)

L’exemple de code où ces classes ont été utilisées est indiqué dans le reste de cette section :

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Modifier existants `UpdatedTransactions` remplacement dont vous recherchez du contenu téléchargeable, puis appelez `StartDownloads` si nécessaire :

```csharp
public override void UpdatedTransactions (SKPaymentQueue queue, SKPaymentTransaction[] transactions)
{
    foreach (SKPaymentTransaction transaction in transactions) {
        switch (transaction.TransactionState) {
        case SKPaymentTransactionState.Purchased:
            // UPDATED FOR iOS 6
            if (transaction.Downloads != null && transaction.Downloads.Length > 0) {
                // Purchase complete, and it has downloads... so download them!
                SKPaymentQueue.DefaultQueue.StartDownloads (transaction.Downloads);
                // CompleteTransaction() call has moved after downloads complete
            } else {
                // complete the transaction now
                theManager.CompleteTransaction(transaction);
            }
            break;
        case SKPaymentTransactionState.Failed:
            theManager.FailedTransaction(transaction);
            break;
        case SKPaymentTransactionState.Restored:
            // TODO: you must decide how to handle restored transactions.
            // Triggering all the downloads at once is not advisable.
            theManager.RestoreTransaction(transaction);
            break;
        default:
            break;
        }
    }
}
```

Nouvelle méthode substituée `UpdatedDownloads` est indiqué ci-dessous. Store Kit appelle cette méthode après `StartDownloads` est déclenchée dans `UpdatedTransactions`. Cette méthode est appelée *plusieurs fois* à intervalles indéterminés pour fournir la progression du téléchargement des et puis à nouveau lorsque le téléchargement est terminé. Notez que la méthode accepte un tableau de `SKDownload` objets, pour que chaque appel de méthode peut vous fournir l’état de plusieurs téléchargements dans la file d’attente. Comme indiqué dans l’implémentation ci-après que les États de téléchargement sont activés chaque fois et l’action appropriée.

```csharp
// ENTIRELY NEW METHOD IN iOS6
public override void PaymentQueueUpdatedDownloads (SKPaymentQueue queue, SKDownload[] downloads)
{
    Console.WriteLine (" -- PaymentQueueUpdatedDownloads");
    foreach (SKDownload download in downloads) {
        switch (download.DownloadState) {
        case SKDownloadState.Active:
            // TODO: implement a notification to the UI (progress bar or something?)
            Console.WriteLine ("Download progress:" + download.Progress);
            Console.WriteLine ("Time remaining:   " + download.TimeRemaining); // -1 means 'still calculating'
            break;
        case SKDownloadState.Finished:
            Console.WriteLine ("Finished!!!!");
            Console.WriteLine ("Content URL:" + download.ContentUrl);

            // UNPACK HERE! Calls FinishTransaction when it's done
            theManager.SaveDownload (download);

            break;
        case SKDownloadState.Failed:
            Console.WriteLine ("Failed"); // TODO: UI?
            break;
        case SKDownloadState.Cancelled:
            Console.WriteLine ("Canceled"); // TODO: UI?
            break;
        case SKDownloadState.Paused:
        case SKDownloadState.Waiting:
            break;
        default:
            break;
        }
    }
}
```

### <a name="inapppurchasemanager-skproductsrequestdelegate"></a>InAppPurchaseManager (SKProductsRequestDelegate)

Cette classe contient une nouvelle méthode `SaveDownload` qui est appelée une fois que chaque téléchargement se termine correctement.

Le contenu hébergé a été correctement téléchargé et décompressé dans le `Cache` directory. La structure de la. Fichier PKG nécessite tous les fichiers à enregistrer dans un `Contents` sous-répertoire, donc le code suivant extrait les fichiers depuis le `Contents` sous-répertoire.

Le code effectue une itération dans tous les fichiers dans le package de contenu et les copie dans le `Documents` répertoire, dans un sous-dossier nommé pour le `ProductIdentifier`. Enfin, il appelle `CompleteTransaction`, qui appelle `FinishTransaction` pour supprimer la transaction à partir de la file d’attente de paiement.

```csharp
// ENTIRELY NEW METHOD IN iOS 6
public void SaveDownload (SKDownload download)
{
    var documentsPath = Environment.GetFolderPath (Environment.SpecialFolder.Personal); // Documents folder
    var targetfolder = System.IO.Path.Combine (documentsPath, download.Transaction.Payment.ProductIdentifier);
    // targetfolder will be "/Documents/com.xamarin.storekitdoc.montouchimages/" or something like that
    if (!System.IO.Directory.Exists (targetfolder))
        System.IO.Directory.CreateDirectory (targetfolder);
    foreach (var file in System.IO.Directory.EnumerateFiles 
             (System.IO.Path.Combine(download.ContentUrl.Path, "Contents"))) { // Contents directory is the default in .PKG files
        var fileName = file.Substring (file.LastIndexOf ("/") + 1);
        var newFilePath = System.IO.Path.Combine(targetfolder, fileName);
        if (!System.IO.File.Exists(newFilePath)) // HACK: this won't support new versions...
            System.IO.File.Copy (file, newFilePath);
        else
            Console.WriteLine ("already exists " + newFilePath);
    }
    CompleteTransaction (download.Transaction); // so it gets 'finished'
}
```

Lorsque `FinishTransaction` est appelée, le téléchargé est n’est plus garantie se trouver dans le `Cache` directory. Tous les fichiers doivent être copiés avant d’appeler `FinishTransaction`.


## <a name="other-considerations"></a>Autres considérations

L’exemple de code ci-dessus illustre une implémentation relativement simple de son contenu hébergé. Il existe quelques points supplémentaires que vous devez prendre en compte :

### <a name="detecting-updated-content"></a>Détection de contenu mis à jour

Bien qu’il soit possible de mettre à jour vos packages de contenu hébergés, Store Kit ne fournit pas de n’importe quel mécanisme pour distribuer ces mises à jour sur les utilisateurs qui ont déjà téléchargé et avez acheté le produit. Pour implémenter cette fonctionnalité, votre code peut vérifier le nouveau `SKProduct.ContentVersion` propriété (si le `SKProduct` est `Downloadable`) régulièrement et détecter si la valeur est incrémentée. Vous pouvez également générer un système de notifications push.

### <a name="installing-updated-content-versions"></a>L’installation des Versions de contenu mis à jour

L’exemple de code ci-dessus ignore la copie de fichier si le fichier existe déjà. Cela n’est pas une bonne idée si vous souhaitez prendre en charge des versions plus récentes du contenu en cours de téléchargement.

Une alternative peut consister à copier le contenu dans un dossier nommé pour la version et effectuer le suivi de ce qui est la version actuelle (par exemple). dans `NSUserDefaults` ou partout où vous stockez des enregistrements d’achats terminée).

### <a name="restoring-transactions"></a>Restauration des Transactions

Lorsque `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` est appelée, Store Kit retourne toutes les transactions précédentes pour l’utilisateur. Si un grand nombre d’articles achetés, ou si chaque achat a des packages de contenu volumineux, puis la restauration peut entraîner un trafic réseau important comme tout Obtient la file d’attente pour le téléchargement à la fois.

Envisagez d’assurer le suivi de si un produit a été acheté séparément à partir du téléchargement réel du package de contenu associé.

### <a name="pausing-restarting-and-canceling-downloads"></a>Suspendre, redémarrer et annuler les téléchargements

Bien que l’exemple de code ne présente pas cette fonctionnalité, il est possible de suspendre et redémarrer des téléchargements de contenu hébergés. Le `SKPaymentQueue.DefaultQueue` a des méthodes pour `PauseDownloads`, `ResumeDownloads` et `CancelDownloads`.

Si le code appelle `FinishTransaction` sur la file d’attente de paiement avant le téléchargement en cours `Finished` alors que le téléchargement est automatiquement annulé.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Définition de l’indicateur de ne pas sauvegarder sur le contenu téléchargé

Instructions de sauvegarde iCloud d’Apple suggère que le contenu non-utilisateur facilement restaurées à partir d’un serveur doit *pas* être sauvegardée (parce qu’il utilise inutilement l’espace de stockage iCloud). Pour plus d’informations sur la définition de l’attribut de sauvegarde, consultez le [système de fichiers](~/ios/app-fundamentals/file-system.md) documentation.

## <a name="summary"></a>Récapitulatif

Cet article a introduit deux nouvelles fonctionnalités du Kit de Store dans iOS6 : achat iTunes et autres contenus à partir de votre application et l’utilisation de serveur d’Apple pour héberger votre propre achats dans l’application. Cette présentation doit être lue conjointement avec le [documentation d’achat dans l’application](~/ios/platform/in-app-purchasing/index.md) pour une couverture complète de l’implémentation de la fonctionnalité Kit de Store.

## <a name="related-links"></a>Liens associés

- [StoreKit (sample)](https://developer.xamarin.com/samples/monotouch/StoreKit/)
- [Achats dans l’application](~/ios/platform/in-app-purchasing/index.md)
- [Référence de l’infrastructure de StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Référence de classe de SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [Référence de l’API recherche d’iTunes](http://www.apple.com/itunes/affiliates/resources/documentation/itunes-store-web-service-search-api.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [WWDC vidéo : Produits vendus avec le Kit de Store](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
