---
title: Modifications apportées à StoreKit dans iOS 6
description: 'iOS 6 introduit deux modifications apportées à l’API du kit de magasin : la possibilité d’afficher les produits iTunes (et App Store/l’iBookstore) à partir de votre application et une nouvelle option d’achat dans l’application où Apple hébergera vos fichiers téléchargeables. Ce document explique comment implémenter ces fonctionnalités avec Xamarin. iOS.'
ms.prod: xamarin
ms.assetid: 253D37D7-44C7-D012-3641-E15DC41C2699
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 7cf18934c70acf59213a697ab57b6c5e308e7b2a
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "78291903"
---
# <a name="changes-to-storekit-in-ios-6"></a>Modifications apportées à StoreKit dans iOS 6

_iOS 6 a introduit deux modifications à l’API du kit Store : la possibilité d’afficher les produits iTunes (et App Store/l’iBookstore) à partir de votre application, ainsi qu’une nouvelle option d’achat dans l’application, où Apple hébergera vos fichiers téléchargeables. Ce document explique comment implémenter ces fonctionnalités avec Xamarin. iOS._

Les principales modifications apportées au kit de stockage dans iOS6 sont ces deux nouvelles fonctionnalités :

- **Affichage du contenu dans l’application & achat** : les utilisateurs peuvent acheter et télécharger des applications, de la musique, des livres et d’autres contenus iTunes sans quitter votre application. Vous pouvez également créer des liens vers vos propres applications pour promouvoir l’achat ou simplement encourager les révisions et les évaluations.
- **Contenu hébergé dans l’achat dans l’application** : Apple stocke et remet le contenu associé à vos produits d’achat dans l’application, ce qui évite d’avoir à utiliser un serveur distinct pour héberger vos fichiers, prend en charge le téléchargement en arrière-plan et vous permet d’écrire moins de code.

Reportez-vous aux guides d' [achat dans l’application pour une](~/ios/platform/in-app-purchasing/index.md) couverture détaillée des API StoreKit.

## <a name="requirements"></a>Spécifications

Les fonctionnalités du kit de magasin présentées dans ce document requièrent iOS 6 et Xcode 4,5, ainsi que Xamarin. iOS 6,0.

## <a name="in-app-content-display--purchasing"></a>Affichage du contenu dans l’application & achat

La nouvelle fonctionnalité d’achat dans iOS permet aux utilisateurs d’afficher des informations sur les produits et d’acheter ou de télécharger le produit à partir de votre application.
Auparavant, les applications devront déclencher iTunes, l’App Store ou le l’iBookstore, ce qui entraînerait l’utilisateur quittant l’application d’origine. Cette nouvelle fonctionnalité retourne automatiquement l’utilisateur à votre application lorsqu’il est terminé.

[![](changes-to-storekit-images/image1.png "Automatically returning to an app after purchase")](changes-to-storekit-images/image1.png#lightbox)

Voici quelques exemples d’utilisation de cette méthode :

- **Encourager les utilisateurs à évaluer votre application** : vous pouvez ouvrir la page App Store afin que l’utilisateur puisse évaluer votre application sans la quitter.
- **Promotion croisée des applications** : autorisez l’utilisateur à voir les autres applications que vous publiez, avec la possibilité d’acheter/télécharger immédiatement.
- Aider **les utilisateurs à trouver et télécharger du contenu** : Aidez les utilisateurs à acheter du contenu que votre application trouve, gère ou agrégats (par exemple, une application de musique peut fournir une sélection de chansons et permettre l’achat de chaque chanson dans l’application.

Une fois le `SKStoreProductViewController` affiché, l’utilisateur peut interagir avec les informations sur le produit comme s’il s’agissait d’iTunes, de l’App Store ou de l’iBookstore. L’utilisateur peut :

- Afficher les captures d’écran (pour les applications),
- Exemples de chansons ou de vidéo (pour la musique, les émissions TV et les films)
- Révisions de lecture (et écriture),
- Achetez & téléchargement, qui se produit entièrement dans le contrôleur d’affichage et le kit de magasin.

Certaines options de la `SKStoreProductViewController` obligent toujours l’utilisateur à sortir votre application et à ouvrir l’application de Store appropriée, par exemple en cliquant sur les **produits associés** ou le lien de **support** d’une application.

### <a name="skstoreproductviewcontroller"></a>SKStoreProductViewController

L’API permettant d’afficher un produit dans n’importe quelle application est simple : il vous suffit de créer et d’afficher un `SKStoreProductViewController`. Pour créer et afficher un produit, procédez comme suit :

1. Créez un objet `StoreProductParameters` pour passer des paramètres au contrôleur d’affichage, y compris les `productId` dans le constructeur.
1. Instanciez le `SKProductViewController`. Affectez-le à un champ de niveau de classe.
1. Assignez un gestionnaire à l’événement `Finished` du contrôleur d’affichage, qui doit faire disparaître le contrôleur d’affichage. Cet événement est appelé quand l’utilisateur appuie sur Annuler. Sinon, finalise une transaction dans le contrôleur d’affichage.
1. Appelez la méthode `LoadProduct` en passant le `StoreProductParameters` et un gestionnaire d’achèvement. Le gestionnaire d’achèvement doit vérifier que la demande de produit s’est bien déroulée et, le cas échéant, présenter le `SKProductViewController` de façon modale. Une gestion appropriée des erreurs doit être ajoutée si le produit ne peut pas être récupéré.

### <a name="example"></a>Exemple

Le projet *ProductView* dans l’exemple de code *StoreKit* pour cet article implémente une méthode `Buy` qui accepte l’ID Apple d’un produit et affiche le `SKStoreProductViewController`. Le code suivant affiche les informations sur les produits pour un ID Apple donné :

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

L’application ressemble à la capture d’écran ci-dessous lors de l’exécution : le téléchargement ou l’achat se produit entièrement dans le `SKStoreProductViewController`:

[![](changes-to-storekit-images/image2.png "The app looks like this when running")](changes-to-storekit-images/image2.png#lightbox)

### <a name="supporting-older-operating-systems"></a>Prise en charge des systèmes d’exploitation plus anciens

L’exemple d’application comprend du code qui montre comment ouvrir l’App Store, iTunes ou l’iBookstore dans les versions antérieures d’iOS. Utilisez la méthode `OpenUrl` pour ouvrir une URL **iTunes.com** correctement créée.

Vous pouvez implémenter une vérification de version pour déterminer le code à exécuter, comme illustré ici :

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

L’erreur suivante se produit si l’ID Apple que vous utilisez n’est pas valide, ce qui peut prêter à confusion, car cela implique un problème de réseau ou d’authentification d’un certain type.

 `Error Domain=SKErrorDomain Code=5 "Cannot connect to iTunes Store"`

### <a name="reading-objective-c-documentation"></a>Lire la documentation objective-C

Les développeurs qui lisent des informations sur le kit Store sur le portail des développeurs d’Apple verront un protocole – [SKStoreProductViewControllerDelegate](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKITunesProductViewControllerDelegate_ProtocolRef/Reference/Reference.html) – discuté par rapport à cette nouvelle fonctionnalité. Le protocole délégué n’a qu’une seule méthode (productViewControllerDidFinish), qui a été exposée en tant qu’événement `Finished` sur le `SKStoreProductViewController` dans Xamarin. iOS.

## <a name="determining-apple-ids"></a>Détermination des ID Apple

L’ID Apple requis par le `SKStoreProductViewController` est un *nombre* (à ne pas confondre avec des ID de Bundle comme « com. xamarin. mwc2012 »). Il existe plusieurs façons de déterminer l’ID Apple pour les produits que vous souhaitez afficher, répertoriés ci-dessous :

### <a name="itunesconnect"></a>iTunesConnect

Pour les applications que vous publiez, il est facile de trouver l' **ID Apple** dans iTunes Connect :

[![](changes-to-storekit-images/image3.png "Finding the Apple ID in iTunes Connect")](changes-to-storekit-images/image3.png#lightbox)

 <a name="Search_API" />

### <a name="search-api"></a>API de recherche

Apple fournit une API de recherche dynamique pour interroger tous les produits dans l’App Store, iTunes et l’iBookstore. Vous trouverez des informations sur l’accès à l’API de recherche dans ressources associées d’Apple, bien que l’API soit exposée à quiconque (pas seulement les filiales inscrites). Le JSON obtenu peut être analysé pour découvrir le `trackId` qui est l’ID Apple à utiliser avec `SKStoreProductViewController`.

Les résultats incluent également d’autres métadonnées, notamment des informations d’affichage et des URL d’illustrations qui peuvent être utilisées pour afficher le produit dans votre application.

Voici quelques exemples :

- **application iBooks** – [https://itunes.apple.com/search?term=ibooks&amp; entité = Software&amp;Country = US](https://itunes.apple.com/search?term=ibooks&amp;entity=software&amp;country=us)
- **Point et Kangaroo iBook** – [https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp; Entity = livre électronique&amp;Country = US](https://itunes.apple.com/search?term=dot+and+the+kangaroo&amp;entity=ebook&amp;country=us)

### <a name="enterprise-partner-feed"></a>Flux des partenaires de l’entreprise

Apple fournit aux partenaires approuvés un vidage complet des données de tous leurs produits, sous la forme de fichiers plats prêts à l’emploi et téléchargeables. Si vous êtes éligible à l’accès au flux des partenaires de l’entreprise, l’ID Apple de tout produit se trouve dans ce jeu de données.

De nombreux utilisateurs du flux des partenaires de l’entreprise sont membres du [programme d’affiliation](https://www.apple.com/itunes/affiliates) qui autorise les commissions à être obtenues sur les ventes de produits. `SKStoreProductViewController` ne prend pas en charge les ID d’affiliés (au moment de l’écriture).

### <a name="direct-product-links"></a>Liens directs sur les produits

L’ID Apple d’un produit peut être déduit à partir de son lien URL d’évaluation d’iTunes.
Dans les liens du produit iTunes (pour les applications, la musique ou les livres), recherchez la partie de l’URL commençant par `id` et utilisez le numéro qui suit.

Par exemple, le lien direct vers iBooks est

```csharp
http://itunes.apple.com/us/app/ibooks/id364709193?mt=8
```

et l’ID Apple est **364709193**. De même, pour l’application MWC2012, le lien direct est

```csharp
http://itunes.apple.com/us/app/mwc-2012-unofficial/id496963922?mt=8
```

et l’ID Apple est **496963922**.

## <a name="in-app-purchase-hosted-content"></a>Achat dans l’application-contenu hébergé

Si vos achats dans l’application se composent d’un contenu téléchargeable (par exemple, une documentation ou autre média, art et configuration au niveau du jeu, ou d’autres fichiers volumineux), ces fichiers sont utilisés pour être hébergés sur votre serveur Web et les applications devaient incorporer du code pour les télécharger en toute sécurité après achat. À compter d’iOS 6, Apple hébergera vos fichiers sur leurs serveurs, supprimant ainsi la nécessité d’un serveur distinct. La fonctionnalité n’est disponible que pour les produits non consommables (non consommables ou abonnements). Les avantages de l’utilisation du service d’hébergement d’Apple sont les suivants :

- Économisez l’hébergement & coûts de bande passante.
- Probablement plus évolutif que n’importe quel hôte serveur que vous utilisez actuellement.
- Moins de code à écrire, car vous n’avez pas à créer de traitement côté serveur.
- Le téléchargement en arrière-plan est implémenté pour vous.

Remarque : le test du contenu des achats dans l’application hébergé dans iOS Simulator n’est pas pris en charge. vous devez donc tester avec un appareil réel.

### <a name="hosted-content-basics"></a>Notions de base sur le contenu hébergé

Avant iOS 6, il existait deux façons de fournir un produit (décrite plus en détail dans la documentation relative à l' [achat dans l’application de Xamarin](~/ios/platform/in-app-purchasing/index.md) ) :

- **Produits intégrés** : fonctionnalités qui sont « déverrouillées » par l’achat, mais qui sont intégrées à l’application (en tant que code ou ressources incorporées). Voici quelques exemples de produits intégrés : les filtres photo déverrouillés ou les Power-ups en jeu.
- **Produits fournis** par le serveur : après l’achat, l’application doit télécharger du contenu à partir d’un serveur que vous travaillez. Ce contenu est téléchargé lors de l’achat, stocké sur l’appareil, puis rendu dans le cadre de la fourniture du produit. Il peut s’agir par exemple de livres, de problèmes de magazines ou de jeux composés d’images et de fichiers de configuration.

Dans iOS 6, Apple offre une variante des produits fournis par le serveur : ils hébergent vos fichiers de contenu sur leurs serveurs. Il est ainsi plus simple de créer des produits fournis par le serveur, car vous n’êtes pas obligé d’utiliser un serveur distinct, et le kit de stockage fournit des fonctionnalités de téléchargement en arrière-plan que vous deviez écrire vous-même. Pour tirer parti de l’hébergement d’Apple, activez l’hébergement de contenu pour les nouveaux produits d’achat dans l’application et modifiez le code de votre kit de magasin pour en tirer parti. Les fichiers de contenu du produit sont ensuite générés à l’aide de Xcode et chargés sur les serveurs d’Apple pour révision et mise en production.

[![](changes-to-storekit-images/image4.png "The build and deliver process")](changes-to-storekit-images/image4.png#lightbox)

L’utilisation de l’App Store pour fournir des achats dans l’application *avec du contenu hébergé* requiert l’installation et la configuration suivantes :

- **iTunes Connect** : vous *devez* avoir fourni vos informations bancaires et fiscales à Apple afin qu’elles puissent remettre des fonds collectés en votre nom. Vous pouvez ensuite configurer les produits à vendre et configurer des comptes d’utilisateur sandbox pour tester l’achat.  _Vous devez également configurer le contenu hébergé pour les produits non consommables que vous souhaitez héberger avec Apple_.
- **portail d’approvisionnement iOS** : création d’un identificateur de Bundle et activation de l’accès au magasin d’applications pour votre application, comme vous le feriez pour n’importe quelle application qui prend en charge l’achat dans l’application.
- **Kit de magasin** : ajout de code à votre application pour afficher des produits, acheter des produits et restaurer des transactions.  _Dans iOS 6 Store Kit gère également le téléchargement du contenu de votre produit, en arrière-plan, avec des mises à jour de progression._
- **Code personnalisé** : permet d’effectuer le suivi des achats effectués par les clients et de fournir les produits ou services qu’ils ont achetés. Utilisez les nouvelles classes du kit de stockage iOS 6 comme `SKDownload` pour récupérer le contenu hébergé par Apple.

Les sections suivantes expliquent comment implémenter le contenu hébergé, de la création et du chargement du package à la gestion du processus d’achat et de téléchargement, à l’aide de l’exemple de code de cet article.

### <a name="sample-code"></a>Exemple de code

L’exemple de projet *HostedNonConsumables* (dans StoreKitiOS6. zip) utilise le contenu hébergé. L’application propose deux « chapitres de livre » pour la vente, le contenu pour lequel est hébergé sur les serveurs d’Apple. Le contenu se compose d’un fichier texte et d’une image, bien qu’un contenu bien plus complexe puisse être utilisé dans une application réelle.

L’application ressemble à ceci avant, pendant et après un achat :

 [![](changes-to-storekit-images/image5.png "The app looks like this before, during and after a purchase")](changes-to-storekit-images/image5.png#lightbox)

Le fichier texte et l’image sont téléchargés et copiés dans le répertoire documents de l’application. Pour plus d’informations sur les différents répertoires disponibles pour le stockage des applications, consultez la [documentation relative au système de fichiers](~/ios/app-fundamentals/file-system.md).

## <a name="itunes-connect"></a>iTunes Connect

Lorsque vous créez de nouveaux produits qui utiliseront l’hébergement de contenu d’Apple, veillez à sélectionner le type de produit **non utilisable** . Les autres types de produits ne prennent pas en charge l’hébergement de contenu. En outre, vous ne devez pas activer l’hébergement de contenu pour les produits *existants* que vous vendez ; activez uniquement l’hébergement de contenu pour les nouveaux produits.

 [![](changes-to-storekit-images/image6.png "Select the Non-Consumable product type")](changes-to-storekit-images/image6.png#lightbox)

Entrez un **ID de produit**. Cet ID sera requis ultérieurement lors de la création du contenu de ce produit.

 [![](changes-to-storekit-images/image7.png "Enter a Product ID")](changes-to-storekit-images/image7.png#lightbox)

L’hébergement de contenu est défini dans la section Détails. Avant l’achat en direct de l’application, décochez la case **héberger le contenu avec Apple** si vous souhaitez annuler (même si vous avez téléchargé du contenu de test). Toutefois, l’hébergement de contenu ne peut pas être supprimé une fois que l’achat dans l’application est en cours.

 [![](changes-to-storekit-images/image8.png "Hosting content with Apple")](changes-to-storekit-images/image8.png#lightbox)

Une fois que vous avez activé le contenu d’hébergement, le produit entrera en **attente d’état de chargement** et affichera le message suivant :

 [![](changes-to-storekit-images/image9.png "The product will enter Waiting for Upload status and show this message")](changes-to-storekit-images/image9.png#lightbox)

Le package de contenu doit être créé avec Xcode et téléchargé à l’aide de l’outil d’archivage. Les instructions de création de packages de contenu sont fournies dans la section suivante **création de. Fichiers PKG**.

## <a name="creating-pkg-files"></a>Création. Fichiers PKG

Les fichiers de contenu que vous téléchargez vers Apple doivent respecter les restrictions suivantes :

- Ne peut pas avoir une taille supérieure à 2 Go.
- Ne peut pas contenir de code exécutable (ou de liens symboliques qui pointent à l’extérieur du contenu).
- Doit être correctement mis en forme (y compris un fichier **. plist** ) et avoir une extension de fichier **. pkg** . Cette opération est effectuée automatiquement si vous suivez ces instructions à l’aide de Xcode.

Vous pouvez ajouter de nombreux fichiers et types de fichiers différents, à condition qu’ils respectent ces restrictions. Le contenu est compressé avant d’être remis à votre application et décompressé par le kit de magasin avant que votre code y accède.

Après le téléchargement d’un package de contenu, il peut être remplacé par un contenu plus récent. Le nouveau contenu doit être téléchargé et soumis pour examen/approbation via le processus normal. Incrémentez le champ `ContentVersion` dans les packages de contenu mis à jour pour indiquer qu’il est plus récent.

### <a name="xcode-in-app-purchase-content-projects"></a>Projets de contenu d’achat dans l’application Xcode

La création de packages de contenu pour les produits d’achat dans l’application nécessite actuellement Xcode. AUCUN codage OBJECTive-C n’est requis ; Xcode possède un nouveau type de projet pour ces packages qui contient simplement vos fichiers et un plist.

Notre exemple d’application contient des chapitres de la vente, chaque package de contenu de chapitre contient les éléments suivants :

- un fichier texte, et
- image représentant le chapitre.

Commencez par sélectionner **fichier > nouveau projet** dans le menu, puis choisissez **contenu de l’achat dans l’application**:

 [![](changes-to-storekit-images/image10.png "Choose In-App Purchase Content")](changes-to-storekit-images/image10.png#lightbox)

Entrez le **nom de produit** et l' **identificateur de société** afin que l’identificateur de **Bundle** corresponde à l' **ID de produit** que vous avez entré dans iTunes Connect pour ce produit.

[![](changes-to-storekit-images/image11.png "Enter the  Name and Identifier")](changes-to-storekit-images/image11.png#lightbox)

À présent, vous disposez d’un projet **de contenu d’achat dans l’application** vide. Vous pouvez cliquer avec le bouton droit et **Ajouter des fichiers...** ou faites-les glisser dans le **navigateur de projet**. Assurez-vous que le **ContentVersion** est correct (il doit commencer à 1,0, mais si vous choisissez ultérieurement de mettre à jour votre contenu, n’oubliez pas de l’incrémenter).

Cette capture d’écran montre Xcode avec les fichiers de contenu inclus dans le projet et les entrées plist visibles dans la fenêtre principale :

[![](changes-to-storekit-images/image12.png "This screenshot shows Xcode with the content files included in the project and the plist entries visible in the main window")](changes-to-storekit-images/image12.png#lightbox)

Une fois que vous avez ajouté tous vos fichiers de contenu, vous pouvez enregistrer ce projet et le modifier ultérieurement, ou commencer le processus de chargement.

## <a name="uploading-pkg-files"></a>Téléchargement. Fichiers PKG

L' **outil d’archivage Xcode**est le moyen le plus simple pour télécharger des packages de contenu. Choisissez **Product > Archive** dans le menu pour commencer :

![](changes-to-storekit-images/image13.png "Choose Archiven")

Le package de contenu apparaît alors dans l’archive, comme indiqué ci-dessous.
Le type d’archive et l’icône affichent cette ligne est une **Archive de contenu d’achat dans l’application**. Cliquez sur **valider...** pour vérifier si le package de contenu présente des erreurs sans réellement effectuer le téléchargement.

[![](changes-to-storekit-images/image14.png "Validate the package")](changes-to-storekit-images/image14.png#lightbox)

Connectez-vous avec vos informations d’identification iTunes Connect :

[![](changes-to-storekit-images/image15.png "Login with your iTunes Connect credentials")](changes-to-storekit-images/image15.png#lightbox)

Choisissez l’application correcte et l’achat dans l’application à associer à ce contenu :

[![](changes-to-storekit-images/image16.png "Choose the correct application and in-app purchase to associate this content with")](changes-to-storekit-images/image16.png#lightbox)

Vous devez voir un message similaire à la capture d’écran suivante :

![Un exemple de message aucun problème](changes-to-storekit-images/image17.png "Un exemple de message aucun problème")

Suivez maintenant un processus similaire, mais en cliquant sur **distribuer...** charge réellement le contenu.

[![Distribuer l’application](changes-to-storekit-images/image18.png "Distribuer l’application")](changes-to-storekit-images/image18.png#lightbox)

Sélectionnez la première option pour télécharger le contenu :

![Télécharger le contenu](changes-to-storekit-images/image19.png "Télécharger le contenu")

Connectez-vous à nouveau :

[![](changes-to-storekit-images/image15.png "Login in")](changes-to-storekit-images/image15.png#lightbox)

Choisissez l’application appropriée et l’enregistrement d’achat dans l’application pour charger le contenu :

[![](changes-to-storekit-images/image20.png "Choose the application and in-app purchase record")](changes-to-storekit-images/image20.png#lightbox)

Patientez pendant le chargement de vos fichiers :

[![](changes-to-storekit-images/image21.png "The content upload dialog")](changes-to-storekit-images/image21.png#lightbox)

Une fois le téléchargement terminé, un message s’affiche pour vous informer que le contenu a été envoyé à l’App Store.

[![](changes-to-storekit-images/image22.png "An example successful upload message")](changes-to-storekit-images/image22.png#lightbox)

Une fois cette opération effectuée, lorsque vous revenez à la page du produit sur iTunes Connect, les détails du package s’affichent et sont **prêts à envoyer** l’État. Lorsque le produit est dans cet État, vous pouvez commencer le test dans l’environnement du bac à sable (sandbox). Vous n’avez pas besoin de « soumettre » le produit à des fins de test dans le bac à sable (sandbox).

[![](changes-to-storekit-images/image23.png "iTunes Connect it will show the package details and be in Ready to Submit status")](changes-to-storekit-images/image23.png#lightbox)

Cela peut prendre un certain temps (par exemple, quelques minutes) entre le chargement de l’archive et l’état d’iTunes Connect mis à jour. Vous pouvez envoyer le produit à des fins de révision séparément ou le soumettre en même temps qu’un fichier binaire d’application. Une fois que le contenu a été officiellement approuvé par Apple, il est disponible dans l’App Store de production pour l’acheter dans votre application.

### <a name="pkg-file-format"></a>Format de fichier PKG

L’utilisation de Xcode et de l’outil d’archivage pour créer et charger un package de contenu hébergé signifie que vous ne voyez jamais le contenu du package lui-même. Les fichiers et répertoires des packages créés pour l’exemple d’application se présentent comme la capture d’écran ci-dessous, avec le fichier **plist** dans la racine et les fichiers du produit dans un sous-répertoire **Contents** :

[![](changes-to-storekit-images/image24.png "The plist file in the root and the product files in a Contents subdirectory")](changes-to-storekit-images/image24.png#lightbox)

Notez la structure de répertoires du package (en particulier l’emplacement des fichiers dans le sous-répertoire `Contents`), car vous devrez comprendre ces informations pour extraire les fichiers du package sur l’appareil.

### <a name="updating-package-content"></a>Mise à jour du contenu du package

Procédure de mise à jour du contenu après l’approbation de ce dernier :

- Modifiez le projet de contenu de l’achat dans l’application dans Xcode.
- Numéro de version de rugosité.
- Téléchargez à nouveau dans iTunes Connect. Les acheteurs suivants obtiendront automatiquement la version la plus récente, mais les utilisateurs qui disposent déjà de l’ancienne version ne recevront aucune notification.
- Votre application est chargée de notifier les utilisateurs et de les encourager à récupérer une version plus récente du contenu. L’application doit également générer une fonction qui télécharge la nouvelle version à l’aide de la fonctionnalité de restauration du kit Store.
- Pour déterminer si une version plus récente existe, vous pouvez créer une fonctionnalité dans votre application pour extraire SKProducts (par exemple, même processus que celui utilisé pour récupérer les prix des produits) et comparer la propriété ContentVersion.

## <a name="purchasing-overview"></a>Présentation des achats

Avant de lire cette section, consultez la [documentation existante sur l’achat dans l’application](~/ios/platform/in-app-purchasing/index.md).

La séquence d’événements qui se produit lorsqu’un produit avec du contenu hébergé est acheté et téléchargé est illustré dans ce diagramme :

[![](changes-to-storekit-images/image25.png "The sequence of events that occurs when a product with hosted content is purchased and download")](changes-to-storekit-images/image25.png#lightbox)

1. De nouveaux produits peuvent être créés dans iTunes Connect avec le contenu hébergé activé. Le contenu réel est construit séparément dans Xcode (tout simplement en faisant glisser des fichiers dans un dossier), puis archivé et téléchargé dans iTunes (aucun codage n’est requis). Chaque produit est ensuite soumis pour approbation, après quoi il est disponible à l’achat. Dans l’exemple de code, ces ID de produit sont codés en dur, mais le contenu d’hébergement avec Apple est plus flexible si vous stockez la liste de produits disponibles sur un serveur distant afin qu’elle puisse être mise à jour lorsque vous envoyez de nouveaux produits et du contenu à iTunes Connect.
1. Lorsque l’utilisateur achète un produit, une transaction est placée dans la file d’attente de paiement pour traitement.
1. Le kit de magasin transfère la demande d’achat aux serveurs iTunes pour traitement.
1. La transaction est terminée sur les serveurs iTunes (par ex. le client est facturé) et un accusé de réception est renvoyé à l’application, avec des informations sur le produit jointes, y compris s’il est téléchargeable (et si tel est le cas, la taille du fichier et les autres métadonnées).
1. Votre code doit vérifier si le produit est téléchargeable et, si tel est le cas, effectuer une demande de téléchargement de contenu qui est également placée sur la file d’attente de paiement. Le kit de magasin envoie cette demande aux serveurs iTunes.
1. Le serveur retourne le fichier de contenu au kit de stockage, qui fournit un rappel pour retourner la progression du téléchargement et les estimations du temps restant à votre code.
1. Une fois l’opération terminée, vous êtes averti et vous avez passé un emplacement de fichier dans le dossier du cache.
1. Votre code doit copier les fichiers et les vérifier, enregistrer tout État dont vous avez besoin pour vous souvenir que le produit a été acheté. Profitez de cette occasion pour définir correctement l’indicateur de sauvegarde sur les nouveaux fichiers (Conseil : s’ils proviennent d’un serveur et ne sont jamais modifiés par l’utilisateur, vous devez probablement les ignorer, car l’utilisateur peut toujours les récupérer à partir des serveurs d’Apple).
1. Appelez FinishTransaction. Cette étape est importante car elle supprime la transaction de la file d’attente de paiement. Il est également important de ne pas appeler FinishTransaction tant que vous n’avez pas copié le contenu du répertoire du cache. Une fois que vous avez appelé FinishTransaction, les fichiers mis en cache sont susceptibles d’être purgés rapidement.

## <a name="implementing-hosted-content-purchase"></a>Implémentation de l’achat de contenu hébergé

Les informations suivantes doivent être lues conjointement avec la documentation complète [relative aux achats dans l’application](~/ios/platform/in-app-purchasing/index.md). Les informations contenues dans ce document portent sur les différences entre le contenu hébergé et l’implémentation précédente.

### <a name="classes"></a>Classes

Les classes suivantes ont été ajoutées ou modifiées pour prendre en charge le contenu hébergé dans iOS 6 :

- **SKDownload** : nouvelle classe qui représente un téléchargement en cours. L’API autorise plusieurs produits par produit, mais initialement un seul a été implémenté.
- **SKProduct** – nouvelles propriétés ajoutées : `Downloadable`, `ContentVersion``ContentLengths` tableau.
- **SKPaymentTransaction** – nouvelle propriété ajoutée : `Downloads`, qui contient une collection d’objets `SKDownload` si le contenu hébergé du produit est disponible en téléchargement.
- **SKPaymentQueue** – nouvelle méthode ajoutée : `StartDownloads`. Appelez cette méthode avec des objets `SKDownload` pour récupérer leur contenu hébergé. Le téléchargement peut être effectué en arrière-plan.
- **SKPaymentTransactionObserver** – nouvelle méthode : `UpdateDownloads`. Le kit de magasin appelle cette méthode avec des informations de progression sur les opérations de téléchargement actuelles.

Détails de la nouvelle classe de `SKDownload` :

- **Progress** : valeur comprise entre 0-1 que vous pouvez utiliser pour afficher un indicateur de pourcentage d’achèvement à l’utilisateur. N’utilisez pas Progress = = 1 pour détecter si le téléchargement est terminé, vérifiez l’état = = terminé.
- **TimeRemaining** : estimation du temps de téléchargement restant, en secondes. -1 signifie qu’il calcule toujours l’estimation.
- **État** : actif, en attente, terminé, échec, suspendu, annulé.
- **ContentURL** : emplacement du fichier où le contenu a été placé sur le disque, dans le répertoire `Cache`. Renseigné uniquement une fois le téléchargement terminé.
- **Erreur** : Vérifiez cette propriété si l’État est failed.

Les interactions entre les classes de l’exemple de code sont présentées dans ce diagramme (le code spécifique aux achats de contenu hébergé est affiché en vert) :

[![](changes-to-storekit-images/image26.png "Hosted content purchases is shown in green in this diagram")](changes-to-storekit-images/image26.png#lightbox)

L’exemple de code dans lequel ces classes ont été utilisées est affiché dans le reste de cette section :

### <a name="custompaymentobserver-skpaymenttransactionobserver"></a>CustomPaymentObserver (SKPaymentTransactionObserver)

Modifiez le `UpdatedTransactions` remplacement existant pour vérifier le contenu téléchargeable, puis appelez `StartDownloads` si nécessaire :

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

Une nouvelle méthode substituée `UpdatedDownloads` est illustrée ci-dessous. Le kit de magasin appelle cette méthode une fois que `StartDownloads` est déclenché dans `UpdatedTransactions`. Cette méthode est appelée *plusieurs fois* à des intervalles indéterminés pour vous fournir la progression du téléchargement, puis à nouveau lorsque le téléchargement est terminé. Notez que la méthode accepte un tableau d’objets `SKDownload`. par conséquent, chaque appel de méthode peut vous fournir l’état de plusieurs téléchargements dans la file d’attente. Comme indiqué dans l’implémentation ci-dessous, les États de téléchargement sont vérifiés chaque fois que l’action appropriée est effectuée.

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

Cette classe contient une nouvelle méthode `SaveDownload` appelée à la fin de chaque téléchargement.

Le contenu hébergé a été téléchargé avec succès et décompressé dans le répertoire `Cache`. Structure de. Le fichier PKG nécessite l’enregistrement de tous les fichiers dans un sous-répertoire `Contents`. le code ci-dessous extrait les fichiers à partir du sous-répertoire `Contents`.

Le code itère au sein de tous les fichiers du package de contenu et les copie dans le répertoire `Documents`, dans un sous-dossier nommé pour le `ProductIdentifier`. Enfin, il appelle `CompleteTransaction`, qui appelle `FinishTransaction` pour supprimer la transaction de la file d’attente de paiement.

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

Lorsque `FinishTransaction` est appelé, il n’est plus garanti que les fichiers téléchargés se trouvent dans le répertoire `Cache`. Tous les fichiers doivent être copiés avant d’appeler `FinishTransaction`.

## <a name="other-considerations"></a>Autres considérations

L’exemple de code ci-dessus illustre une implémentation relativement simple de l’achat de contenu hébergé. Vous devez prendre en compte certains points supplémentaires :

### <a name="detecting-updated-content"></a>Détection du contenu mis à jour

Bien qu’il soit possible de mettre à jour vos packages de contenu hébergés, le kit Store ne fournit aucun mécanisme permettant de transmettre ces mises à jour aux utilisateurs qui ont déjà téléchargé et acheté le produit. Pour implémenter cette fonctionnalité, votre code peut vérifier la nouvelle `SKProduct.ContentVersion` propriété (si la `SKProduct` est `Downloadable`) régulièrement et détecter si la valeur est incrémentée. Vous pouvez également créer un système de notifications push.

### <a name="installing-updated-content-versions"></a>Installation de versions de contenu mises à jour

L’exemple de code ci-dessus ignore la copie de fichiers si le fichier existe déjà. Cela n’est pas une bonne idée si vous souhaitez prendre en charge des versions plus récentes du contenu en cours de téléchargement.

Une autre solution consiste à copier le contenu dans un dossier nommé pour la version et à effectuer le suivi de la version actuelle (par exemple, dans `NSUserDefaults` ou à l’emplacement où vous stockez les enregistrements d’achat terminés).

### <a name="restoring-transactions"></a>Restauration des transactions

Lorsque `SKPaymentQueue.DefaultQueue.RestoreCompletedTransactions` est appelé, le kit de magasin retourne toutes les transactions précédentes pour l’utilisateur. S’ils ont acheté un grand nombre d’éléments, ou si chaque achat a des packages de contenu volumineux, la restauration peut entraîner un trafic réseau important, car tout est mis en file d’attente de téléchargement en même temps.

Envisagez de suivre si un produit a été acheté séparément du téléchargement réel du package de contenu associé.

### <a name="pausing-restarting-and-canceling-downloads"></a>Suspension, redémarrage et annulation des téléchargements

Bien que l’exemple de code n’illustre pas cette fonctionnalité, il est possible de suspendre et de redémarrer les téléchargements de contenu hébergé. Le `SKPaymentQueue.DefaultQueue` a des méthodes pour `PauseDownloads`, `ResumeDownloads` et `CancelDownloads`.

Si le code appelle `FinishTransaction` sur la file d’attente de paiement avant le téléchargement `Finished` alors ce téléchargement est annulé automatiquement.

### <a name="setting-the-skip-backup-flag-on-the-downloaded-content"></a>Définition de l’indicateur SKIP-Backup sur le contenu téléchargé

Les instructions de sauvegarde iCloud d’Apple suggèrent que le contenu non-utilisateur facile à restaurer à partir d’un serveur ne doit *pas* être sauvegardé (car il inutilement d’utiliser le stockage icloud). Pour plus d’informations sur la définition de l’attribut de sauvegarde, consultez la documentation relative au [système de fichiers](~/ios/app-fundamentals/file-system.md) .

## <a name="summary"></a>Résumé

Cet article a introduit deux nouvelles fonctionnalités du kit Store dans iOS6 : l’achat d’iTunes et d’autres contenus à partir de votre application, et l’utilisation du serveur d’Apple pour héberger vos propres achats dans l’application. Cette présentation doit être lue conjointement avec la [documentation de l’achat dans l’application](~/ios/platform/in-app-purchasing/index.md) existante pour une couverture complète de l’implémentation de la fonctionnalité du kit de magasin.

## <a name="related-links"></a>Liens connexes

- [StoreKit (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/storekit)
- [Achats dans l’application](~/ios/platform/in-app-purchasing/index.md)
- [Référence du Framework StoreKit](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/StoreKit_Collection/_index.html)
- [Référence de la classe SKStoreProductViewController](https://developer.apple.com/library/ios/documentation/StoreKit/Reference/SKITunesProductViewController_Ref/SKStoreProductViewController.html)
- [SKDownload](https://developer.apple.com/library/prerelease/ios/#documentation/StoreKit/Reference/SKDownload_Ref/Introduction/Introduction.html)
- [SKPaymentQueue](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKPaymentQueue_Class/Reference/Reference.html#/apple_ref/occ/instm/SKPaymentQueue/cancelDownloads:)
- [SKProduct](https://developer.apple.com/library/prerelease/ios/documentation/StoreKit/Reference/SKProduct_Reference/Reference/Reference.html#/apple_ref/occ/instp/SKProduct/downloadable)
- [Vidéo WWDC : vendre des produits avec le kit Store](https://developer.apple.com/videos/wwdc/2012/?include=302#302)
