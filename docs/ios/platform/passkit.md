---
title: PassKit dans Xamarin. iOS
description: L’application Wallet permet aux utilisateurs d’iOS de stocker des passages numériques sur leurs appareils. L’infrastructure PassKit permet aux développeurs d’interagir par programme avec les passes.
ms.prod: xamarin
ms.assetid: 74B9973B-C1E8-B727-3F6D-59C1F98BAB3A
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/13/2018
ms.openlocfilehash: 150a4e3c1deafbabea892d5adb786374c3d97d12
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769582"
---
# <a name="passkit-in-xamarinios"></a>PassKit dans Xamarin. iOS

L’application iOS Wallet permet aux utilisateurs de stocker des passages numériques sur leurs appareils.
Ces tests sont générés par les commerçants et envoyés au client par courrier électronique, URL ou par le biais de l’application iOS du commerçant. Ces passes peuvent représenter diverses choses, des tickets de cinéma aux cartes de fidélité aux passes de la carte. L’infrastructure PassKit permet aux développeurs d’interagir par programme avec les passes.

Ce document présente Wallet et utilise l’API PassKit avec Xamarin. iOS.

 [![](passkit-images/image1.png "Le portefeuille stocke et organise tous les passages sur un téléphone")](passkit-images/image1.png#lightbox)

## <a name="requirements"></a>Configuration requise

Les fonctionnalités PassKit présentées dans ce document requièrent iOS 6 et Xcode 4,5, ainsi que Xamarin. iOS 6,0.

## <a name="introduction"></a>Présentation

Le problème clé que PassKit résout est la distribution et la gestion des codes-barres. Voici quelques exemples concrets de la façon dont les codes-barres sont actuellement utilisés :

- **Achat de tickets de film en ligne** : les clients sont généralement envoyés par e-mail à un code-barres qui représente leurs tickets. Ce code-barres est imprimé et dirigé vers le cinéma à analyser pour l’entrée.
- **Cartes de fidélité** : les clients comportent un certain nombre de cartes spécifiques au magasin dans leur portefeuille ou sur leur porte-monnaie pour les afficher et les analyser lorsqu’ils achètent des marchandises.
- **Coupons** : les coupons sont distribués par e-mail, en tant que pages Web imprimables, via cadres et en tant que codes-barres dans les journaux et les magazines. Les clients les apportent à un magasin pour la numérisation, pour recevoir des marchandises, des services ou des remises en retour.
- Des **passes d’embarquement** , similaires à l’achat d’un ticket de film.

PassKit offre une alternative pour chacun de ces scénarios :

- **Movie Tickets** – après l’achat, le client ajoute une passe de ticket d’événement (par e-mail ou un lien de site Web). À mesure que le temps pour le film approche, le passage s’affiche automatiquement sur l’écran de verrouillage comme un rappel, et à l’arrivée au cinéma, la passe est facilement récupérée et affichée dans Wallet pour analyse.
- Les **cartes de fidélité** : plutôt que (ou en plus) fournissant une carte physique, les magasins peuvent émettre (par courrier électronique ou après une connexion à un site Web) une carte de magasin. Le magasin peut fournir des fonctionnalités supplémentaires telles que la mise à jour du solde du compte lors de la transmission via des notifications push et l’utilisation des services de géolocalisation. la passe peut s’afficher automatiquement sur l’écran de verrouillage lorsque le client est proche de l’emplacement du magasin.
- **Coupons : les** passes de coupon peuvent facilement être générées avec des caractéristiques uniques pour faciliter le suivi et être distribuées par e-mail ou via des liens de sites Web. Les coupons téléchargés peuvent apparaître automatiquement sur l’écran de verrouillage lorsque l’utilisateur est proche d’un emplacement spécifique, et/ou à une date donnée (par exemple, lorsque la date d’expiration approche). Étant donné que les coupons sont stockés sur le téléphone de l’utilisateur, ils sont toujours pratiques et ne sont pas mal placés. Les coupons peuvent inciter les clients à télécharger des applications auxiliaires, car les liens de l’App Store peuvent être incorporés dans la passe, ce qui renforce l’engagement avec le client.
- **Passes** de l’embarquement : après un processus d’archivage en ligne, le client recevrait sa transmission par e-mail ou par lien. Une application auxiliaire fournie par le fournisseur de transport peut inclure le processus d’archivage et permettre au client d’effectuer des fonctions supplémentaires telles que le choix de son siège ou de son repas. Le fournisseur de transport peut utiliser des notifications push pour mettre à jour la passe si le transport est retardé ou annulé. À mesure que le temps d’embarquement s’approche de la passe, l’écran de verrouillage s’affiche comme rappel et pour fournir un accès rapide à la passe.

PassKit offre un moyen simple et pratique de stocker et d’afficher des codes-barres sur votre appareil iOS. Avec l’intégration de l’écran de verrouillage de l’emplacement et du temps supplémentaire, les notifications push et l’intégration de l’application auxiliaire constituent une base pour les services de vente, de ticket et de facturation très sophistiqués.

## <a name="passkit-ecosystem"></a>Écosystème PassKit

PassKit n’est pas simplement une API dans CocoaTouch, il fait plutôt partie d’un plus grand écosystème d’applications, de données et de services qui facilitent le partage et la gestion sécurisés de codes-barres et d’autres données. Ce diagramme de haut niveau montre les différentes entités qui peuvent être impliquées dans la création et l’utilisation de passes :

 [![](passkit-images/image2.png "Ce diagramme de haut niveau montre les entités impliquées dans la création et l’utilisation de passes.")](passkit-images/image2.png#lightbox)

Chaque partie de l’écosystème a un rôle clairement défini :

- **Portefeuille** : application iOS intégrée à Apple qui stocke et affiche des passes. C’est le seul endroit où les passes sont rendues pour une utilisation dans le monde réel (c’est-à-dire que le code-barres est affiché, ainsi que toutes les données localisées dans la passe).
- **Applications auxiliaires** : applications iOS 6 générées par les fournisseurs de transmission pour étendre les fonctionnalités des passes qu’elles émettent, telles que l’ajout d’une valeur à une carte de magasin, la modification du siège sur une passe d’embarquement ou une autre fonction spécifique à l’entreprise. Les applications auxiliaires ne sont pas requises pour qu’une Pass soit utile.
- **Votre serveur** : serveur sécurisé sur lequel les passes peuvent être générées et signées pour être distribuées. Votre application auxiliaire peut se connecter à votre serveur pour générer de nouvelles Pass ou demander des mises à jour à des passes existantes. Vous pouvez éventuellement implémenter l’API de service Web que Wallet appellera pour mettre à jour les passes.
- **Serveurs APNs** : votre serveur a la possibilité de notifier le portefeuille de mises à jour à un transfert sur un appareil donné à l’aide de APNs. Envoyer une notification push à Wallet, qui contactera ensuite votre serveur pour obtenir des détails sur la modification. Les applications auxiliaires n’ont pas besoin d’implémenter APNS pour cette fonctionnalité (elles `PKPassLibraryDidChangeNotification` peuvent écouter le).
- Applications de **conduit** : applications qui ne manipulent pas directement les passes (comme les applications auxiliaires), mais qui peuvent améliorer leur utilitaire en reconnaissant des passes et en leur permettant d’être ajoutées à Wallet. Les clients de messagerie, les navigateurs de réseau social et d’autres applications d’agrégation de données peuvent rencontrer des pièces jointes ou des liens vers des réussites.

La totalité de l’écosystème semble complexe. il est donc intéressant de noter que certains composants sont facultatifs et que les implémentations de PassKit les plus simples sont possibles.

## <a name="what-is-a-pass"></a>Qu’est-ce qu’un pass ?

Une passe est une collection de données représentant un ticket, un coupon ou une carte. Il peut être destiné à une utilisation unique par un individu (et donc contenir des détails tels qu’un numéro de vol et une allocation de sièges) ou un jeton d’utilisation multiple qui peut être partagé par un nombre quelconque d’utilisateurs (par exemple, un coupon de remise). Une description détaillée est disponible dans le document [sur les fichiers de réussite](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html) d’Apple.

### <a name="types"></a>Types

Actuellement, cinq types sont pris en charge, qui peuvent être distingués dans l’application Wallet par la disposition et le bord supérieur de la passe :

- **Ticket d’événement** : petit découpage semi-circulaire.
- **Passe d’embarquement** : des crans dans le côté, une icône spécifique au transport peut être spécifiée (par exemple, bus, apprentissage, avion).
- **Carte de stockage** : arrondi à la partie supérieure, comme une carte de crédit ou de débit.
- **Coupon** : perforé en haut.
- **Générique** : identique à la carte de magasin, arrondie à la partie supérieure.

Les cinq types de tests sont affichés dans cette capture d’écran (dans l’ordre : coupon, générique, carte de stockage, passe de carte et ticket d’événement) :

 [![](passkit-images/image3.png "Les cinq types de passes sont affichés dans cette capture d’écran.")](passkit-images/image3.png#lightbox)

### <a name="file-structure"></a>Structure de fichier

Un fichier Pass est en fait une archive ZIP avec une extension **. pkpass** , contenant des fichiers JSON spécifiques (obligatoires), divers fichiers image (facultatif) ainsi que des chaînes localisées (également facultatives).

- **Pass. JSON** : requis. Contient toutes les informations relatives à la passe.
- **Manifest. JSON** – requis. Contient les hachages SHA1 pour chaque fichier du test, à l’exception du fichier de signature et de ce fichier (manifest. Json).
- **signature** : obligatoire. Créé en signant le `manifest.json` fichier avec le certificat généré dans le portail d’approvisionnement iOS.
- **logo. png** – facultatif.
- **Background. png** – facultatif.
- **Icon. png** – facultatif.
- **Fichiers de chaînes localisables** – facultatif.

La structure de répertoires d’un fichier Pass est indiquée ci-dessous (il s’agit du contenu de l’archive ZIP) :

 [![](passkit-images/image4.png "La structure de répertoires d’un fichier Pass est présentée ici")](passkit-images/image4.png#lightbox)

### <a name="passjson"></a>Pass. JSON

JSON est le format, car les passes sont généralement créées sur un serveur, ce qui signifie que le code de génération est indépendant de la plateforme sur le serveur. Les trois éléments clés de chaque passe sont les suivants :

- **teamIdentifier** : ce lien permet d’accéder à toutes les étapes que vous générez sur votre compte App Store. Cette valeur est visible dans le portail d’approvisionnement iOS.
- **passTypeIdentifier** : Inscrivez-vous au portail de provisionnement pour regrouper les passes (si vous générez plusieurs types). Par exemple, un café peut créer un type de passe de carte de magasin pour permettre à ses clients de gagner des crédits de fidélité, mais également un type de passe de coupon distinct pour créer et distribuer des bons de réduction. Ce même café peut même contenir des événements de musique en direct et émettre des passages de ticket d’événement pour ceux-ci.
- **SerialNumber** : chaîne unique dans ce `passTypeidentifier` . La valeur est opaque pour Wallet, mais elle est importante pour le suivi de passes spécifiques lors de la communication avec votre serveur.

Il y a un grand nombre d’autres clés JSON à chaque passe, un exemple qui est illustré ci-dessous :

``` 
{
   "passTypeIdentifier":"com.xamarin.passkitdoc.banana",  //Type Identifier (iOS Provisioning Portal)
   "formatVersion":1,                                     //Always 1 (for now)
   "organizationName":"Xamarin",                          //The name which appears on push notifications
   "serialNumber":"12345436XYZ",                          //A number for you to identify this pass
   "teamIdentifier":"XXXAAA1234",                         //Your Team ID
   "description":"Xamarin Demo",                          //
   "foregroundColor":"rgb(54,80,255)",                    //color of the data text (note the syntax)
   "backgroundColor":"rgb(209,255,247)",                  //color of the background
   "labelColor":"rgb(255,15,15)",                         //color of label text and icons
   "logoText":"Banana ",                                  //Text that appears next to logo on top
   "barcode":{                                            //Specification of the barcode (optional)
      "format":"PKBarcodeFormatQR",                       //Format can be QR, Text, Aztec, PDF417
      "message":"FREE-BANANA",                            //What to encode in barcode
      "messageEncoding":"iso-8859-1"                      //Encoding of the message
   },
   "relevantDate":"2012-09-15T15:15Z",                    //When to show pass on screen. ISO8601 formatted.
  /* The following fields are specific to which type of pass. The name of this object specifies the type, e.g., boardingPass below implies this is a boarding pass. Other options include storeCard, generic, coupon, and eventTicket */
   "boardingPass":{
/*headerFields, primaryFields, secondaryFields, and auxiliaryFields are arrays of field object. Each field has a key, label, and value*/
      "headerFields":[          //Header fields appear next to logoText
         {
            "key":"h1-label",   //Must be unique. Used by iOS apps to get the data.
            "label":"H1-label", //Label of the field
            "value":"H1"        //The actual data in the field
         },
         {
            "key":"h2-label",
            "label":"H2-label",
            "value":"H2"
         }
      ],
      "primaryFields":[       //Appearance differs based on pass type
         {
            "key":"p1-label",
            "label":"P1-label",
            "value":"P1"
         }
      ],
      "secondaryFields":[     //Typically appear below primaryFields
         {
            "key":"s1-label",
            "label":"S1-label",
            "value":"S1"
         }
      ],
      "auxiliaryFields":[    //Appear below secondary fields
         {
            "key":"a1-label",
            "label":"A1-label",
            "value":"A1"
         }
      ],
      "transitType":"PKTransitTypeAir"  //Only present in boradingPass type. Value can
                                        //Air, Bus, Boat, or Train. Impacts the picture
                                        //that shows in the middle of the pass.
   }
}
```

### <a name="barcodes"></a>Codes-barres

Seuls les formats 2D sont pris en charge : PDF417, Aztec, QR. Apple prétend que les codes-barres 1D ne sont pas de suite à l’analyse sur un écran de téléphone rétroéclairé.

Le texte de remplacement affiché sous le code-barres est facultatif : certains commerçants souhaitent pouvoir lire/taper manuellement.

L’encodage ISO-8859-1 est le plus courant. Vérifiez l’encodage utilisé par les systèmes d’analyse qui lisent vos passes.

### <a name="relevancy-lock-screen"></a>Pertinence (écran de verrouillage)

Il existe deux types de données qui peuvent entraîner l’affichage d’une passe sur l’écran de verrouillage :

 **Lieu**

Vous pouvez spécifier jusqu’à 10 emplacements dans une passe, par exemple, les magasins qu’un client visite fréquemment ou l’emplacement d’un Cinema ou d’un aéroport. Un client peut définir ces emplacements via une application auxiliaire ou le fournisseur peut les déterminer à partir des données d’utilisation (si elles sont collectées avec l’autorisation du client).

Lorsque la passe est affichée sur l’écran de verrouillage, une clôture est calculée de sorte que lorsque l’utilisateur quitte la zone, la passe est masquée dans l’écran de verrouillage. Le rayon est lié au style de passage pour empêcher tout abus.

 **Date et heure**

Une seule date/heure peut être spécifiée dans une passe. La date et l’heure sont utiles pour déclencher des rappels d’écran de verrouillage pour les passes de bord et les tickets d’événement.

Peut être mis à jour via l’API push ou via l’API PassKit, de sorte que la date/l’heure peut être mise à jour dans le cas d’un ticket à utilisation multiple (par exemple, un ticket de saison pour un théâtre ou un complexe sportif).

### <a name="localization"></a>Localisation

La traduction d’un passage en plusieurs langues est similaire à la localisation d’une application iOS : créer des répertoires `.lproj` spécifiques à la langue avec l’extension et placer les éléments localisés dans. Les traductions de texte doivent être entrées `pass.strings` dans un fichier, tandis que les images localisées doivent avoir le même nom que l’image qu’elles remplacent dans la racine de réussite.

## <a name="security"></a>Sécurité

Les passes sont signées avec un certificat privé que vous générez dans le portail d’approvisionnement iOS. Les étapes à suivre pour signer la passe sont les suivantes :

1. Calculez un hachage SHA1 pour chaque fichier dans le répertoire de réussite (n’incluez `signature` pas le `manifest.json` fichier ou, aucun d’entre eux ne doit malgré tout exister à ce niveau).
1. Écrire `manifest.json` sous la forme d’une liste de clés/valeurs JSON de chaque nom de fichier avec son hachage.
1. Utilisez le certificat pour signer le `manifest.json` fichier et écrire le résultat dans un fichier appelé `signature` .
1. Compressez tout le contenu et donnez à l' `.pkpass` extension de fichier le fichier résultant.

Étant donné que votre clé privée est nécessaire pour signer la passe, ce processus ne doit être effectué que sur un serveur sécurisé que vous contrôlez. NE distribuez pas vos clés pour essayer de générer des passes dans une application.

## <a name="configuration-and-setup"></a>Configuration et configuration

Cette section contient des instructions pour vous aider à configurer les détails de votre approvisionnement et à créer votre premier passage.

### <a name="provisioning-passkit"></a>Approvisionnement de PassKit

Pour qu’une passe entre dans l’App Store, elle doit être liée à un compte de développeur. Cela nécessite deux étapes :

1. La passe doit être inscrite à l’aide d’un identificateur unique, appelé ID du type de passe.
1. Un certificat valide doit être généré pour signer la passe avec la signature numérique du développeur.

Pour créer un ID de type de passe, procédez comme suit.

#### <a name="create-a-pass-type-id"></a>Créer un ID de type de passe

La première étape consiste à configurer un ID de type de passe pour chaque _type_ de passe à prendre en charge. L’ID de passage (ou l’identificateur de type de passe) crée un identificateur unique pour la passe. Nous utiliserons cet ID pour lier la passe à votre compte de développeur à l’aide d’un certificat.

1. Dans la [section certificats, identificateurs et profils du portail d’approvisionnement iOS](https://developer.apple.com/account/overview.action), accédez à **identificateurs** , puis sélectionnez ID de **type de passe** . Sélectionnez ensuite le **+** bouton pour créer un type de passe : [![](passkit-images/passid.png "Créer un type de passe")](passkit-images/passid.png#lightbox)

2. Fournissez une **Description** (nom) et un **identificateur** (chaîne unique) pour la passe. Notez que tous les ID de type de passe doivent commencer `pass.` par la chaîne dans cet `pass.com.xamarin.coupon.banana` exemple nous utilisons : [![](passkit-images/register.png "Fournir une description et un identificateur")](passkit-images/register.png#lightbox)

3. Confirmez l’ID de test en appuyant sur le bouton **Register** .

#### <a name="generate-a-certificate"></a>Générer un certificat

Pour créer un nouveau certificat pour cet ID de type de passe, procédez comme suit :

1. Sélectionnez l’ID de passe nouvellement créé dans la liste, puis cliquez sur **modifier** : [![](passkit-images/pass-done.png "Sélectionner le nouvel ID de test dans la liste")](passkit-images/pass-done.png#lightbox)

    Ensuite, sélectionnez **créer un certificat...** :

    [![](passkit-images/cert-dist.png "Sélectionner créer un certificat")](passkit-images/cert-dist.png#lightbox)

2. Suivez les étapes pour créer une demande de signature de certificat (CSR).
  
3. Appuyez sur le bouton **Continuer** sur le portail des développeurs et chargez la CSR pour générer votre certificat.

4. Téléchargez le certificat et double-cliquez dessus pour l’installer dans votre trousseau.

Maintenant que nous avons créé un certificat pour cet ID de type de passe, la section suivante explique comment générer un pass manuellement.

Pour plus d’informations sur l’approvisionnement de Wallet, reportez-vous au Guide d' [utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) de.

### <a name="create-a-pass-manually"></a>Créer un pass manuellement

Maintenant que nous avons créé le type de passe, nous pouvons créer manuellement une étape de test sur le simulateur ou sur un appareil. Les étapes de création d’une passe sont les suivantes :

- Créez un répertoire destiné à contenir les fichiers de réussite.
- Créez un fichier Pass. JSON qui contient toutes les données requises.
- Inclure les images dans le dossier (si nécessaire).
- Calculez les hachages SHA1 pour chaque fichier dans le dossier, puis écrivez dans le fichier manifest. JSON.
- Signez manifest. JSON avec le fichier. P12 de certificat téléchargé.
- Compressez le contenu du répertoire et renommez-le avec l’extension. pkpass.

L' [exemple de code](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) de cet article contient des fichiers sources qui peuvent être utilisés pour générer une passe. Utilisez les fichiers dans le `CouponBanana.raw` répertoire du répertoire CreateAPassManually. Les fichiers suivants sont présents :

 [![](passkit-images/image18.png "Ces fichiers sont présents")](passkit-images/image18.png#lightbox)

Ouvrez Pass. JSON et modifiez le JSON. Vous devez au moins mettre à `passTypeIdentifier` jour `teamIdentifer` et pour qu’il corresponde à votre compte de développeur Apple.

```csharp
"passTypeIdentifier" : "pass.com.xamarin.coupon.banana",
"teamIdentifier" : "?????????",
```

Vous devez ensuite calculer les hachages pour chaque fichier et créer le `manifest.json` fichier. Une fois que vous avez terminé, cela ressemble à ce qui suit :

```csharp
{
  "icon@2x.png" : "30806547dcc6ee084a90210e2dc042d5d7d92a41",
  "icon.png" : "87e9ffb203beb2cce5de76113f8e9503aeab6ecc",
  "pass.json" : "c83cd1441c17ecc6c5911bae530d54500f57d9eb",
  "logo.png" : "b3cd8a488b0674ef4e7d941d5edbb4b5b0e6823f",
  "logo@2x.png" : "3ccd214765507f9eab7244acc54cc4ac733baf87"
}
```

Ensuite, une signature doit être générée pour ce fichier à l’aide du certificat (fichier. p12) qui a été généré pour cet ID de type de passe.

#### <a name="signing-on-a-mac"></a>Signature sur un Mac

Téléchargez les **documents du support technique Wallet** sur le site de [téléchargements Apple](https://developer.apple.com/downloads/index.action?name=Passbook) . Utilisez l' `signpass` outil pour transformer votre dossier en un test (cela permet également de calculer les hachages SHA1 et de compresser la sortie dans un fichier. pkpass).

#### <a name="testing"></a>Test

Si vous deviez examiner la sortie de ces outils (en définissant le nom de fichier sur. zip et en l’ouvrant), vous verrez les fichiers suivants (Notez l' `manifest.json` ajout `signature` des fichiers et) :

 [![](passkit-images/image19.png "Examen de la sortie de ces outils")](passkit-images/image19.png#lightbox)

Une fois que vous avez signé, compressé et renommé le fichier (par exemple, à `BananaCoupon.pkpass`) vous pouvez le faire glisser dans le simulateur pour le tester, ou l’envoyer par courrier électronique à vous-même pour effectuer une récupération sur un appareil réel. Vous devez voir un écran pour **Ajouter** la passe, comme suit :

 [![](passkit-images/image20.png "Ajouter l’écran de réussite")](passkit-images/image20.png#lightbox)

Normalement, le processus est automatisé sur un serveur, mais la création manuelle de passe peut être une option pour les petites entreprises qui créent uniquement des coupons qui ne nécessitent pas la prise en charge d’un serveur principal.

## <a name="wallet"></a>Wallet

Wallet est le composant central de l’écosystème PassKit. Cette capture d’écran montre le portefeuille vide et l’aspect de la liste de tests et des passages individuels :

 [![](passkit-images/image21.png "Cette capture d’écran montre le portefeuille vide et l’apparence de la liste de tests et des passages individuels")](passkit-images/image21.png#lightbox)

Les fonctionnalités de Wallet sont les suivantes :

- C’est le seul endroit où les passes sont rendues avec leur code-barres pour l’analyse.
- L’utilisateur peut modifier les paramètres des mises à jour. Si cette option est activée, les notifications push peuvent déclencher des mises à jour des données dans la passe.
- L’utilisateur peut activer ou désactiver l’intégration de l’écran de verrouillage. S’il est activé, cette option permet d’afficher automatiquement la passe sur l’écran de verrouillage, en fonction des données de temps et d’emplacement appropriées incorporées dans la passe.
- Le côté inverse de la passe prend en charge l’extraction à l’actualisation, si une URL de serveur Web est fournie dans le JSON Pass.
- Les applications auxiliaires peuvent être ouvertes (ou téléchargées) si l’ID de l’application est fourni dans le JSON Pass.
- Les passes peuvent être supprimées (avec une animation de jolies broyage).

## <a name="adding-passes-into-wallet"></a>Ajout de passes à Wallet

Les passes peuvent être ajoutées à Wallet de l’une des manières suivantes :

- **Applications de conduit** : celles-ci ne manipulent pas directement les passes, elles chargent simplement les fichiers de réussite et présentent à l’utilisateur la possibilité de les ajouter à Wallet. 

- **Applications auxiliaires** : celles-ci sont écrites par les fournisseurs pour distribuer les passes et offrent des fonctionnalités supplémentaires pour les parcourir ou les modifier. Les applications Xamarin. iOS ont un accès complet à l’API PassKit pour créer et manipuler des passes. Les passes peuvent ensuite être ajoutées à Wallet `PKAddPassesViewController`à l’aide du. Ce processus est décrit plus en détail dans la section **applications auxiliaires** de ce document.

### <a name="conduit-applications"></a>Applications de conduit

Les applications de canalisation sont des applications intermédiaires qui peuvent recevoir des passages pour le compte d’un utilisateur et doivent être programmées pour reconnaître leur type de contenu et fournir des fonctionnalités à ajouter au portefeuille. Voici quelques exemples d’applications de conduit :

- **Mail** : reconnaît les pièces jointes en tant que Pass.
- **Safari** : reconnaît le type de contenu Pass quand l’utilisateur clique sur un lien de test d’URL.
- **Autres applications personnalisées** : toute application qui reçoit des pièces jointes ou des liens ouverts (clients de réseaux sociaux, lecteurs de messagerie, etc.).

Cette capture d’écran montre comment le **courrier électronique** dans iOS 6 reconnaît une pièce jointe de réussite et (lorsqu’il est touché) permet de l' **Ajouter** à Wallet.

 [![](passkit-images/image22.png "Cette capture d’écran montre comment le courrier dans iOS 6 reconnaît une pièce jointe de réussite")](passkit-images/image22.png#lightbox)

 [![](passkit-images/image23.png "Cette capture d’écran montre comment les courriers électroniques permettent d’ajouter une pièce jointe à Wallet")](passkit-images/image23.png#lightbox)

Si vous créez une application qui peut être un conduit pour les passes, elle peut être reconnue par :

- **Extension de fichier** -. pkpass
- **Type MIME** -application/vnd. Apple. pkpass
- **UTI** – com. Apple. pkpass

Le fonctionnement de base d’une application de canalisation consiste à récupérer le fichier Pass et `PKAddPassesViewController` à appeler PassKit pour permettre à l’utilisateur d’ajouter la passe à son portefeuille. L’implémentation de ce contrôleur d’affichage est traitée dans la section suivante sur les **applications auxiliaires**.

Les applications de conduit n’ont pas besoin d’être configurées pour un ID de type de passe spécifique de la même façon que les applications auxiliaires.

## <a name="companion-applications"></a>Applications auxiliaires

Une application auxiliaire fournit des fonctionnalités supplémentaires pour l’utilisation des passes, notamment la création d’une passe, la mise à jour des informations associées à une passe et la gestion des passes associées à l’application.

Les applications auxiliaires ne doivent pas tenter de dupliquer les fonctionnalités de Wallet. Ils ne sont pas destinés à afficher des passes pour l’analyse.

Ce reste de cette section décrit comment créer une application auxiliaire de base qui interagit avec PassKit.

### <a name="provisioning"></a>Approvisionnement

Étant donné que Wallet est une technologie de magasin, l’application doit être approvisionnée séparément et ne peut pas utiliser le profil de provisionnement d’équipe ou un ID d’application générique. Reportez-vous au Guide d' [utilisation des fonctionnalités](~/ios/deploy-test/provisioning/capabilities/wallet-capabilities.md) pour créer un ID d’application et un profil de provisionnement uniques pour l’application Wallet.

### <a name="entitlements"></a>Droits

Le fichier **Entitlements. plist** doit être inclus dans tous les projets récents Xamarin. iOS. Pour ajouter un nouveau fichier Entitlements. plist, suivez les étapes décrites dans le guide [utilisation des droits](~/ios/deploy-test/provisioning/entitlements.md) .

Pour définir les droits, procédez comme suit :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

Double-cliquez sur le fichier **habilitations. plist** dans le panneau solutions pour ouvrir l’éditeur de droits. plist :

![](passkit-images/image31.png "Éditeur de droits. plst")

Sous la section Wallet, sélectionnez l’option **activer Wallet**

![](passkit-images/image32.png "Activer le droit portefeuille")

L’option par défaut permet à votre application d’autoriser tous les types de passe. Toutefois, il est possible de restreindre votre application et d’autoriser uniquement un sous-ensemble de types de passes d’équipe. Pour activer cette option, sélectionnez **autoriser le sous-ensemble de types de passe d’équipe** et entrez l’identificateur de type de passe du sous-ensemble que vous souhaitez autoriser.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Double-cliquez sur le fichier **Entitlements. plist** pour ouvrir le fichier source XML.

Pour ajouter le droit Wallet, affectez à `Passbook Identifiers` la propriété la valeur dans la liste déroulante, ce qui définit automatiquement le **type** `Array`. Ensuite, définissez la **valeur** de chaîne `$(TeamIdentifierPrefix)*`sur :

![](passkit-images/image33.png "Activer le droit portefeuille")

Avec cette valeur de chaîne, votre application accepte tous les types de passes. Pour restreindre votre application et autoriser uniquement un sous-ensemble de types de passes d’équipe, définissez la valeur de chaîne sur :

`$(TeamIdentifierPrefix)pass.$(CFBundleIdentifier)`

Où `pass.$(CFBundleIdentifier)` est l’ID de réussite qui a été créé [ci-dessus](~/ios/platform/passkit.md)

-----

### <a name="debugging"></a>Débogage

Si vous rencontrez des problèmes lors du déploiement de votre application, vérifiez que vous utilisez le **profil de provisionnement** correct `Entitlements.plist` et que l’est sélectionné comme fichier de **droits personnalisés** dans les options de **signature du bundle iPhone** .

Si vous rencontrez cette erreur lors du déploiement :

```csharp
Installation failed: Your code signing/provisioning profiles are not correctly configured (error: 0xe8008016)
```

le tableau `pass-type-identifiers` de droits est incorrect (ou ne correspond pas au **profil de provisionnement**). Vérifiez que les ID de type de passe et votre ID d’équipe sont corrects.

## <a name="classes"></a>Classes

Les classes PassKit suivantes sont disponibles pour les applications à accéder aux passes :

- **PKPass** : instance d’une passe.
- **PKPassLibrary** : fournit l’API pour accéder aux passes sur l’appareil.
- **PKAddPassesViewController** : permet d’afficher un test permettant à l’utilisateur de s’enregistrer dans son portefeuille.
- **PKAddPassesViewControllerDelegate** – Xamarin. iOS développeurs

## <a name="example"></a>Exemple

Reportez-vous au projet PassLibrary dans l' [exemple de code](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) de cet article. Il illustre les fonctions courantes suivantes qui seraient nécessaires dans une application de portefeuille :

### <a name="check-that-wallet-is-available"></a>Vérifier que Wallet est disponible

Wallet n’est pas disponible sur iPad. les applications doivent donc vérifier avant de tenter d’accéder aux fonctionnalités PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    // create an instance and do stuff...
}
```

### <a name="creating-a-pass-library-instance"></a>Création d’une instance de la bibliothèque Pass

La bibliothèque PassKit n’est pas un singleton, les applications doivent créer et stocker une instance pour accéder à l’API PassKit.

```csharp
if (PKPassLibrary.IsAvailable) {
    library = new PKPassLibrary ();
    // do stuff...
}
```

### <a name="get-a-list-of-passes"></a>Obtenir une liste de passes

Les applications peuvent demander une liste de passes de la bibliothèque. Cette liste est automatiquement filtrée par PassKit, afin que vous puissiez voir uniquement les passes créées avec votre ID d’équipe et répertoriées dans vos droits.

```csharp
var passes = library.GetPasses ();  // returns PKPass[]
```

Notez que le simulateur ne filtre pas la liste des passes retournées. cette méthode doit donc toujours être testée sur les appareils réels. Cette liste peut être affichée dans un UITableView. L' [exemple d’application](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit) ressemble à ceci après que deux coupons ont été ajoutés :

 [![](passkit-images/image29.png "L’exemple d’application ressemble à ce qui suit deux coupons ajoutés")](passkit-images/image29.png#lightbox)

### <a name="displaying-passes"></a>Affichage des passes

Un ensemble limité d’informations est disponible pour le rendu des passes dans les applications auxiliaires.

Choisissez parmi cet ensemble de propriétés standard pour afficher les listes de passes, comme l’illustre l’exemple de code.

```csharp
string passInfo =
                "Desc:" + pass.LocalizedDescription
                + "\nOrg:" + pass.OrganizationName
                + "\nID:" + pass.PassTypeIdentifier
                + "\nDate:" + pass.RelevantDate
                + "\nWSUrl:" + pass.WebServiceUrl
                + "\n#" + pass.SerialNumber
                + "\nPassUrl:" + pass.PassUrl;
```

Cette chaîne est affichée sous la forme d’une alerte dans l' [exemple](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit):

 [![](passkit-images/image30.png "Alerte de coupon sélectionnée dans l’exemple")](passkit-images/image30.png#lightbox)

Vous pouvez également utiliser la `LocalizedValueForFieldKey()` méthode pour récupérer des données à partir de champs dans les passes que vous avez créées (puisque vous saurez quels champs doivent être présents). L’exemple de code ne montre pas cela.

### <a name="loading-a-pass-from-a-file"></a>Chargement d’une passe à partir d’un fichier

Comme une passe peut uniquement être ajoutée à Wallet avec l’autorisation de l’utilisateur, un contrôleur d’affichage doit être présenté pour lui permettre de décider. Ce code est utilisé dans le bouton **Ajouter** de l’exemple, pour charger une passe pré-générée incorporée dans l’application (vous devez remplacer ceci par une étape que vous avez signée) :

```csharp
NSData nsdata;
using ( FileStream oStream = File.Open (newFilePath, FileMode.Open ) ) {
        nsdata = NSData.FromStream ( oStream );
}
var err = new NSError(new NSString("42"), -42);
var newPass = new PKPass(nsdata,out err);
var pkapvc = new PKAddPassesViewController(newPass);
NavigationController.PresentModalViewController (pkapvc, true);
```

La réussite est présentée avec des options d' **Ajout** et d' **annulation** :

 [![](passkit-images/image20.png "Le passage présenté avec des options d’ajout et d’annulation")](passkit-images/image20.png#lightbox)

### <a name="replace-an-existing-pass"></a>Remplacer une passe existante

Le remplacement d’une passe existante ne nécessite pas l’autorisation de l’utilisateur, mais elle échoue si elle n’existe pas déjà.

```csharp
if (library.Contains (newPass)) {
     library.Replace (newPass);
}
```

### <a name="editing-a-pass"></a>Modification d’une passe

PKPass n’est pas mutable, vous ne pouvez donc pas mettre à jour les objets Pass dans votre code. Pour modifier les données d’un Pass, une application doit avoir accès à un serveur Web qui peut conserver un enregistrement de passes et générer un nouveau fichier de réussite avec des valeurs mises à jour que l’application peut télécharger.

La création d’un fichier Pass doit être effectuée sur un serveur, car les passes doivent être signées avec un certificat qui doit rester privé et sécurisé.

Une fois qu’un fichier Pass mis à jour a été `Replace` généré, utilisez la méthode pour remplacer les anciennes données sur l’appareil.

### <a name="display-a-pass-for-scanning"></a>Afficher une passe pour l’analyse

Comme indiqué précédemment, seul Wallet peut afficher une passe pour l’analyse. Une passe peut être affichée à l' `OpenUrl` aide de la méthode, comme indiqué ci-dessous :

 `UIApplication.SharedApplication.OpenUrl (p.PassUrl);`

### <a name="receiving-notifications-of-changes"></a>Réception des notifications de modifications

Les applications peuvent écouter les modifications apportées à la bibliothèque de `PKPassLibraryDidChangeNotification`tests à l’aide du. Des modifications peuvent être provoquées par des notifications déclenchant des mises à jour en arrière-plan. il est donc conseillé de les écouter dans votre application.

```csharp
noteCenter = NSNotificationCenter.DefaultCenter.AddObserver (PKPassLibrary.DidChangeNotification, (not) => {
    BeginInvokeOnMainThread (() => {
        new UIAlertView("Pass Library Changed", "Notification Received", null, "OK", null).Show();
        // refresh the list
        var passlist = library.GetPasses ();
        table.Source = new TableSource (passlist, library);
        table.ReloadData ();
    });
}, library);  // IMPORTANT: must pass the library in
```

Il est important de passer une instance de bibliothèque lors de l’inscription de la notification, car PKPassLibrary n’est pas un singleton.

## <a name="server-processing"></a>Traitement du serveur

La présentation détaillée de la création d’une application serveur pour la prise en charge de PassKit n’entre pas dans le cadre de cet article de présentation.

Consultez le code côté serveur Open C# source [dotnet-](https://github.com/tomasmcguinness/dotnet-passbook) encodé.

## <a name="push-notifications"></a>Notifications Push

La présentation détaillée de l’utilisation de notifications push pour les passes de mise à jour n’entre pas dans le cadre de cet article de présentation.

Vous devez implémenter l’API de type REST définie par Apple pour répondre aux demandes Web de Wallet lorsque des mises à jour sont requises.

Pour plus d’informations, consultez le Guide de [mise à jour d’un test](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/Updating.html#//apple_ref/doc/uid/TP40012195-CH5-SW1) d’Apple.

## <a name="summary"></a>Récapitulatif

Cet article a présenté PassKit, expliquant les raisons pour lesquelles il est utile et décrit les différentes parties qui doivent être implémentées pour une solution PassKit complète. Il a décrit les étapes nécessaires à la configuration de votre compte de développeur Apple pour créer des passes, le processus permettant d’effectuer un pass manuellement et également comment accéder aux API PassKit à partir d’une application Xamarin. iOS.

## <a name="related-links"></a>Liens associés

- [Portefeuille pour les développeurs](https://developer.apple.com/wallet/)
- [Exemple PassKit](https://docs.microsoft.com/samples/xamarin/ios-samples/passkit)
- [Guide du développeur Wallet](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/index.html#//apple_ref/doc/uid/TP40012195-CH1-SW1)
- [Frameworks – Apple Pay et Wallet (vidéos WWDC)](https://developer.apple.com/videos/frameworks/apple-pay-and-wallet)
- [Référence du Framework PassKit](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Framework/_index.html)
- [Référence du service Web de la mémoire](https://developer.apple.com/library/prerelease/ios/#documentation/PassKit/Reference/PassKit_WebService/WebService.html)
- [À propos des fichiers de réussite](https://developer.apple.com/library/prerelease/ios/#documentation/UserExperience/Reference/PassKit_Bundle/Chapters/Introduction.html)
- [dotnet-crédits](https://github.com/tomasmcguinness/dotnet-passbook), bibliothèque open source pour la génération de packages Wallet iOS
- [Introduction à iOS 6](~/ios/platform/introduction-to-ios6/index.md)
