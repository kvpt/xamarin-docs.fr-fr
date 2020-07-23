---
title: Notions de base et configuration des achats dans l’application dans Xamarin. iOS
description: Ce document décrit les achats dans l’application dans Xamarin. iOS, en discutant des informations pertinentes sur les règles, la configuration et iTunes Connect.
ms.prod: xamarin
ms.assetid: 11FB7F02-41B3-2B34-5A4F-69F12897FE10
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/18/2017
ms.openlocfilehash: fb63568adee9e89d08a0fc64168c865eeb271f10
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86928885"
---
# <a name="in-app-purchase-basics-and-configuration-in-xamarinios"></a>Notions de base et configuration des achats dans l’application dans Xamarin. iOS

L’implémentation des achats dans l’application requiert que l’application utilise l’API StoreKit sur l’appareil. StoreKit gère toutes les communications avec les serveurs iTunes d’Apple pour recevoir des informations sur les produits et effectuer des transactions. Le profil de provisionnement doit être configuré pour les achats dans l’application et les informations sur le produit doivent être entrées dans iTunes Connect.

 [![StoreKit gère toutes les communications avec Apple, comme indiqué dans ce graphique](in-app-purchase-basics-and-configuration-images/image1.png)](in-app-purchase-basics-and-configuration-images/image1.png#lightbox)

L’utilisation de l’App Store pour fournir des achats dans l’application requiert l’installation et la configuration suivantes :

- **iTunes Connect** : configuration des produits pour vendre et configurer des comptes d’utilisateur sandbox pour tester l’achat. Vous devez également avoir fourni vos informations bancaires et fiscales à Apple afin qu’elles puissent remettre des fonds collectés en votre nom.
- **portail d’approvisionnement iOS** : création d’un identificateur de Bundle et activation de l’accès au magasin d’applications pour votre application.
- **Kit de magasin** : ajout de code à votre application pour afficher des produits, acheter des produits et restaurer des transactions.
- **Code personnalisé** : permet d’effectuer le suivi des achats effectués par les clients et de fournir les produits ou services qu’ils ont achetés. Vous pouvez également avoir besoin d’implémenter un processus côté serveur pour valider les accusés de réception si vos produits consistent en un téléchargement de contenu à partir d’un serveur (par exemple, des livres et des problèmes de magazines).

Il existe deux « environnements de serveurs » du kit de stockage :

- **Production** : transactions avec des sommes réelles. Accessible uniquement via des applications qui ont été soumises et approuvées par Apple. Les produits d’achat dans l’application doivent également être revus et approuvés avant d’être disponibles dans l’environnement de production.
- **Sandbox** : où vos tests se produisent. Les produits sont disponibles immédiatement après la création (le processus d’approbation s’applique uniquement à l’environnement de production). Les transactions dans le bac à sable (sandbox) requièrent des utilisateurs de test (pas des ID Apple réels) pour effectuer des transactions.

## <a name="in-app-purchase-rules"></a>Règles d’achat dans l’application

Vous ne pouvez pas accepter d’autres formes de paiement pour les produits ou services numériques à l’intérieur de votre application, ni les mentionner ni les référencer à partir d’une application. Cela signifie que vous ne pouvez pas accepter de carte de crédit ou PayPal lorsque l’achat dans l’application est le mécanisme de paiement le plus approprié. Il existe un cas particulier pour l’achat de produits numériques en dehors de l’application, mais pour une utilisation dans l’application, tels que des livres d’achat sur un site Web associé à une « connexion » spécifique et l’utilisation de cette « connexion » dans l’application permet à l’utilisateur d’accéder aux livres achetés.
Les applications qui fonctionnent de cette façon ne sont pas autorisées à mentionner ou à établir un lien vers la fonctionnalité d’achat externe : les développeurs doivent communiquer cette fonctionnalité à leurs utilisateurs d’une autre façon (par exemple, via la messagerie marketing ou tout autre canal direct).

Toutefois, étant donné que vous ne pouvez pas utiliser des achats dans l’application pour des biens physiques, dans ce cas, vous êtes autorisé à utiliser un autre mécanisme de paiement (par exemple, carte de crédit, PayPal) à partir de l’application.

Apple doit approuver chaque produit avant qu’il ne soit mis en vente : le nom, la description et une capture d’écran du « produit » sont requis pour la révision. Les temps d’évaluation du produit sont les mêmes que pour les révisions d’application.

Vous ne pouvez pas choisir le prix de votre produit : vous ne pouvez sélectionner qu’un « niveau de prix » qui a une valeur spécifique dans chaque pays/devise pris en charge par Apple. Vous ne pouvez pas avoir un niveau tarifaire différent sur différents marchés.

## <a name="configuration"></a>Configuration

Avant d’écrire du code d’achat dans une application, vous devez effectuer un travail de configuration dans iTunes Connect ( [iTunesConnect.Apple.com](https://itunesconnect.apple.com)) et le portail de provisionnement iOS ( [Developer.Apple.com/iOS](https://developer.apple.com/iOS)).

Ces trois étapes doivent être effectuées avant d’écrire du code :

- **Compte de développeur Apple** : soumettez vos informations bancaires et fiscales à Apple.
- **portail d’approvisionnement iOS** : Assurez-vous que votre application possède un ID d’application valide (et non un caractère générique avec un astérisque *) et que l’achat d’applications est activé.
- **iTunes Connect application Management** – ajoutez des produits à votre application.

### <a name="apple-developer-account"></a>Compte de développeur Apple

La création et la distribution d’applications gratuites nécessitent très peu de configuration dans [iTunes Connect](https://itunesconnect.apple.com). Toutefois, pour vendre des applications payantes ou des achats dans l’application, vous devez fournir à Apple des informations bancaires et fiscales. Cliquez sur **accords, fiscaux et bancaires** dans le menu principal illustré ici :

 [![Cliquez sur accords, taxes et bancaires dans le menu principal.](in-app-purchase-basics-and-configuration-images/image2.png)](in-app-purchase-basics-and-configuration-images/image2.png#lightbox)

Votre compte de développeur doit avoir un contrat d' **applications payantes iOS** en vigueur, comme illustré dans cette capture d’écran :

 [![Votre compte de développeur doit avoir un contrat d’applications payantes iOS en vigueur](in-app-purchase-basics-and-configuration-images/image3.png)](in-app-purchase-basics-and-configuration-images/image3.png#lightbox)

Vous ne pouvez pas tester les fonctionnalités de StoreKit tant que vous n’avez pas de contrat d' **applications payantes iOS** : les appels StoreKit dans votre code échouent tant qu’Apple n’a pas traité vos **contrats, taxes et informations bancaires** .

### <a name="ios-provisioning-portal"></a>Portail de provisionnement iOS

Les nouvelles applications sont configurées dans la section **ID d’application** du **portail de provisionnement iOS**. Pour créer un nouvel ID d’application, accédez au [Centre des membres du portail d’approvisionnement iOS](https://developer.apple.com/membercenter/index.action), accédez à la section **certificats, identificateurs et profils** du portail, puis cliquez sur **identificateurs** sous *applications iOS*. Ensuite, cliquez sur le signe « + » en haut à droite pour générer un nouvel ID d’application.

Formulaire de création de nouveaux **ID d’application**

 ressemble à ceci :

 [![Formulaire de création de nouveaux ID d’application](in-app-purchase-basics-and-configuration-images/image4.png)](in-app-purchase-basics-and-configuration-images/image4.png#lightbox)

Entrez un nom approprié pour la *Description*, ce qui vous permet d’identifier facilement cet ID d’application dans une liste. Pour le *préfixe d’ID d’application*, sélectionnez l’ID d’équipe.

#### <a name="bundle-identifierapp-id-suffix-format"></a>Format d’identificateur de Bundle/de suffixe d’ID d’application

Vous pouvez utiliser n’importe quelle chaîne de votre choix pour votre **identificateur de Bundle** (à condition qu’elle soit unique dans votre compte). Toutefois, Apple vous recommande de suivre le format DNS inversé plutôt que d’utiliser une chaîne arbitraire. L’exemple d’application qui accompagne cet article utilise com. xamarin. storekit. testing pour l’identificateur de Bundle, mais il serait tout aussi valide pour utiliser un identificateur comme my_store_example (même si Apple ne le recommande pas).

> [!IMPORTANT]
> Apple autorise également l’ajout d’un astérisque à caractères génériques à la fin d’un **identificateur de Bundle** afin qu’un ID d’application unique puisse être utilisé pour plusieurs applications. Toutefois, les ID d’application de _carte générique ne peuvent pas être utilisés pour AppPurchase_. Un exemple d’identificateur de Bundle de cartes génériques peut être com. xamarin. *

#### <a name="enabling-app-services"></a>Activation de App Services

Notez que l' **achat dans l’application** est automatiquement activé dans la liste des services :

 [![L’achat dans l’application sera automatiquement activé dans la liste des services](in-app-purchase-basics-and-configuration-images/image5.png)](in-app-purchase-basics-and-configuration-images/image5.png#lightbox)

#### <a name="provisioning-profiles"></a>Profils de provisionnement

Créez des profils de provisionnement de développement et de production comme vous le feriez normalement, en sélectionnant l’ID d’application que vous avez configuré pour l’achat dans l’application. Pour plus d’informations, reportez-vous à l’approvisionnement et à la publication des [appareils iOS](~/ios/get-started/installation/device-provisioning/index.md) [sur les guides de l’App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) .

## <a name="itunes-connect"></a>iTunes Connect

Cliquez sur **mes applications** dans iTunes Connect pour créer ou modifier une entrée d’application iOS. La page vue d’ensemble de l’application s’affiche ici :

 [![Page vue d’ensemble de l’application](in-app-purchase-basics-and-configuration-images/image6.png)](in-app-purchase-basics-and-configuration-images/image6.png#lightbox)

Cliquez sur **achats dans l’application** pour créer ou modifier vos produits en vue de la vente. Cette capture d’écran montre l’exemple d’application avec plusieurs produits déjà ajoutés :

 [![L’exemple d’application avec plusieurs produits a déjà été ajouté](in-app-purchase-basics-and-configuration-images/image7.png)](in-app-purchase-basics-and-configuration-images/image7.png#lightbox)

Le processus d’ajout de nouveaux produits comporte deux étapes :

1. Choisissez le type de produit : [ ![ choisir le type de produit](in-app-purchase-basics-and-configuration-images/image8.png)](in-app-purchase-basics-and-configuration-images/image8.png#lightbox) 
2. Entrez les attributs du produit, y compris l’ID du produit, le niveau tarifaire et les descriptions localisées : [ ![ Saisie des attributs des produits](in-app-purchase-basics-and-configuration-images/image9.png)](in-app-purchase-basics-and-configuration-images/image9.png#lightbox)

Les champs requis pour chaque produit d’achat dans l’application sont décrits ci-dessous :

### <a name="reference-name"></a>Nom de la référence

Le nom de référence n’est pas affiché à vos utilisateurs ; Il est destiné à un usage interne et n’apparaît que dans iTunes Connect.

### <a name="product-id-format"></a>Format d’ID de produit

Un identificateur de produit peut uniquement contenir des caractères alphanumériques (A-Z, a-z, 0-9), un trait de soulignement (_) et un point (.). Bien que vous puissiez utiliser n’importe quelle chaîne pour vos identificateurs, Apple recommande le format DNS inversé. Par exemple, l’exemple d’application utilise cet identificateur de Bundle :

 `com.xamarin.storekit.testing`

Par conséquent, la Convention pour identifier les produits d’achat dans l’application serait la suivante :

```csharp
com.xamarin.storekit.testing.consume5credits
com.xamarin.storekit.testing.consume10credits
com.xamarin.storekit.testing.sepia
com.xamarin.storekit.testing.greyscale
```

Cette Convention d’affectation de noms n’est pas appliquée, il s’agit simplement d’une recommandation pour vous aider à gérer vos produits. En outre, malgré la même convention inverse DNS, les identificateurs de produit ne sont *pas liés* à l’identificateur de Bundle et ne sont pas requis pour commencer avec la même chaîne. Il serait toujours possible d’utiliser des identificateurs comme photo_product_greyscale (même si Apple ne le recommande pas).

L’ID de produit n’est pas affiché à vos utilisateurs, mais il est utilisé pour référencer le produit dans le code de votre application.

### <a name="product-type"></a>Type de produit

Vous pouvez proposer cinq types de produits d’achat dans l’application :

1. **Consommation** : éléments « utilisés », tels que la monnaie en jeu que le joueur peut dépenser. Si l’utilisateur effectue une opération de sauvegarde/restauration ou si son appareil est actualisé, une transaction consommable n’est pas également restaurée (ce qui donne effectivement le même avantage au joueur). Le code d’application doit être en mesure de fournir l’élément « consommable » dès que la transaction est terminée.
1. **Non-consommable** : produits que l’utilisateur possède une fois achetés, par exemple un numéro de magazine numérique ou un niveau de jeu.
1. **Abonnements auto-renouvelés** : tout comme un abonnement de magazine réel, à la fin de la période d’abonnement, Apple facture à nouveau le client et étend la durée de l’abonnement, de manière permanente ou jusqu’à ce que le client l’annule explicitement. Il s’agit de la méthode de paiement par défaut pour les applications Newsstand (en fait, les applications doivent prendre en charge ce mode de paiement pour être approuvées pour la distribution Newsstand).
1. **Abonnement gratuit** : cette option est disponible uniquement dans les applications Newsstand et permet au client d’accéder au contenu de l’abonnement sur tous ses appareils. Les abonnements gratuits n’expirent jamais.
1. **Abonnement qui n’est pas renouvelé** : doit être utilisé pour vendre un accès limité au temps au contenu statique, par exemple l’accès d’un mois à une archive photo.

 *Ce document couvre actuellement uniquement les deux premiers types de produits (consommables et non consommables).*

 <a name="Price_Tiers"></a>

### <a name="price-tiers"></a>Niveaux de prix

L’App Store ne vous permet pas de choisir un prix arbitraire pour vos produits : Apple fournit des niveaux de prix fixes que vous pouvez choisir. Les prix sont fixés dans chaque devise, et Apple se réserve le droit d’ajuster les prix relatifs (par exemple, après une modification soutenue du taux de change relatif entre une devise particulière et le dollar des États-Unis).

Apple fournit une matrice de prix pour vous aider à sélectionner le niveau approprié pour la devise/le prix de votre choix. Un extrait de la matrice tarifaire (août 2012) est présenté ici :

 [![Un extrait de la matrice tarifaire août 2012](in-app-purchase-basics-and-configuration-images/image10.png)](in-app-purchase-basics-and-configuration-images/image10.png#lightbox)

Au moment de la rédaction de l’article (juin 2013), il y a 87 niveaux, de 0,99 USD à 999,99. La matrice de tarification affiche le prix que vos clients paient et le montant que vous allez recevoir d’Apple, ce qui est inférieur à 30% et également les taxes locales qu’ils doivent collecter (Notez dans l’exemple que les vendeurs aux États-Unis et au Canada reçoivent 70C pour un produit 99c, tandis que les vendeurs australiens reçoivent uniquement des 63C en raison des « taxes de biens &amp; » perçues

La tarification de votre produit peut être mise à jour à tout moment, y compris les changements de prix planifiés qui prennent effet à une date ultérieure. Cette capture d’écran montre comment un changement de prix futur daté est ajouté : le prix est provisoirement modifié du niveau 1 au niveau 3 pour le mois de septembre uniquement :

 [![Un changement de prix futur daté où le prix est provisoirement modifié du niveau 1 au niveau 3 pour le mois de septembre uniquement](in-app-purchase-basics-and-configuration-images/image11.png)](in-app-purchase-basics-and-configuration-images/image11.png#lightbox)

### <a name="free-products-not-supported"></a>Produits gratuits non pris en charge

Bien qu’Apple ait fourni une option d’abonnement gratuit spéciale pour les applications Newsstand, il n’est pas possible de définir un prix de zéro (gratuit) pour les autres types d’achat dans l’application. Vous pouvez modifier les prix (IE. Lower) pour les promotions de ventes, mais vous ne pouvez pas effectuer d’achats dans l’application « gratuit » via iTunes Connect.

### <a name="localization"></a>Localisation

Dans iTunes Connect, vous pouvez entrer un nom et un texte de description différents pour un nombre quelconque de langues prises en charge. Chaque langue peut être ajoutée/modifiée dans via une fenêtre contextuelle :

 [![Chaque langue peut être ajoutée/modifiée dans via une fenêtre contextuelle](in-app-purchase-basics-and-configuration-images/image12.png)](in-app-purchase-basics-and-configuration-images/image12.png#lightbox)   

Lorsque vous affichez des informations sur les produits dans votre application, vous pouvez afficher le texte localisé à l’aide de StoreKit. L’affichage de la devise doit également être localisé pour afficher le symbole correct et la mise en forme décimale. cette mise en forme est traitée plus loin dans le document.

### <a name="app-store-review"></a>Revue de l’App Store

Comme pour les applications : chaque produit est revu par Apple avant d’être autorisé à être mis en vente. Les produits peuvent être rejetés en cas de contenu inapproprié dans le nom ou la description, ou Apple peut décider que vous avez choisi le mauvais type de produit (par exemple, vous avez créé un livre ou un problème de magazine, mais utilisé le type de produit consommable). Les révisions de produits peuvent durer tant qu’une application est revue.

La première fois qu’une application est soumise avec l’achat dans l’application activée (qu’il s’agisse d’une nouvelle application ou que la fonctionnalité a été ajoutée à une application existante), vous devez également choisir certains produits à envoyer avec celle-ci. Le portail iTunes Connect vous invite à le faire, comme illustré dans cette capture d’écran :

 [![Le portail iTunes Connect vous invitera à envoyer certains produits également.](in-app-purchase-basics-and-configuration-images/image13.png)](in-app-purchase-basics-and-configuration-images/image13.png#lightbox)   

L’application et les achats dans l’application seront revus ensemble, de sorte qu’ils soient tous approuvés simultanément (afin que l’application ne passe pas dans le magasin sans aucun produit approuvé !).

Une fois que votre première version avec la fonctionnalité d’achat dans l’application a été approuvée, vous pouvez ajouter d’autres produits et les soumettre à des fins de révision à tout moment. Vous pouvez également choisir d’envoyer une nouvelle version avec des produits d’achat spécifiques dans l’application, à l’aide de la page de détails de la **version** , comme le suggère l’invite.

Pour plus d’informations, reportez-vous aux instructions de révision de l' [App Store](https://developer.apple.com/appstore/guidelines.html) .

 [Partie 2 : vue d’ensemble du kit de magasin et extraction d’informations sur le produit](~/ios/platform/in-app-purchasing/store-kit-overview-and-retreiving-product-information.md)
