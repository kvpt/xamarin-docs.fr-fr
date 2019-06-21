---
title: vue d’ensemble des fonctionnalités de plateforme iOS
description: Ce document contient des liens vers des guides différents qui décrivent les fonctionnalités introduites dans différentes versions d’e/s et autres fonctionnalités de la plateforme iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/25/2018
ms.openlocfilehash: 9628821be5a979777614eb4f7ad8605087093ed3
ms.sourcegitcommit: a153623a69b5cb125f672df8007838afa32e9edf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67268840"
---
# <a name="ios-platform-features-overview"></a>vue d’ensemble des fonctionnalités de plateforme iOS

Cette page présente iOS récentes versions, ainsi que la mise en évidence certaines des infrastructures d’Apple, vous pouvez accéder avec Xamarin.iOS.

## <a name="ios-releases"></a>versions d’iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Introduction à iOS 12](~/ios/platform/introduction-to-ios12/index.md) | Ce document décrit iOS 12 fonctionnalités disponibles pour les utilisent lorsque les applications buildingXamarin.iOS.|
| [Introduction à iOS 11](~/ios/platform/introduction-to-ios11/index.md) | Ce document décrit les fonctionnalités nouvelles et mises à jour dans iOS 11 et Xcode 9, telles que ARKit, Core ML, Core NFC, glisser-déplacer, MapKit, PDFKit, SiriKit et Vision. Il lie vers des guides qui expliquent comment utiliser ces featureswith Xamarin.iOS. |
| [Introduction à iOS 10](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 inclut plusieurs nouvelles API et services qui vous permettent de développer des applications avec les nouvelles fonctionnalités. IOS 10, les applications ont des fonctionnalités telles que l’extension des cartes, Messages, téléphone et Siri. Cette section montre comment tirer parti de ces fonctionnalités dans une application Xamarin.iOS. |
| [Introduction à iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | Cette section définit les modifications apportées dans iOS 9 lorsque la mise à niveau à partir d’iOS 8 et comment utiliser ces fonctionnalités dans une application Xamarin.iOS. |
| [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)         | iOS 8 de faire un grand nombre de modifications du système d’exploitation à partir d’iOS 7. Ici, nous montrons ce qu’ils sont et comment les utiliser. |
| [Introduction à iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | Sur les principales nouvelles API introduites dans iOS 7, y compris contrôleur d’affichage effectue la transition, améliorations apportées aux animations de UIView, UIKit Dynamics et Kit de texte. |
| [Introduction à iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Obtenir une explication sur les fonctionnalités introduites dans iOS 6, y compris les vues de Collection, passer Kit, Kit d’événement et l’infrastructure des réseaux sociaux. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay a été introduite en même temps qu’iOS 8, permettant aux utilisateurs de payer des marchandises physiques tels que des produits alimentaires, les divertissements et les appartenances via leurs appareils iOS. Il est disponible sur iPhone 6 et iPhone 6 Plus et peut également être associé à l’Apple Watch pour les achats dans le magasin. Lorsqu’il est utilisé sur un iPhone, il utilise des Touch ID pour confirmer et autoriser des transactions à d’un utilisateur carte de crédit ou débit.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

La nouvelle API CallKit dans iOS 10 offre un moyen pour les applications VOIP intégrer l’interface utilisateur de l’iPhone et fournir une interface familière et à l’utilisateur final. Avec cette API les utilisateurs peuvent afficher et interagir avec les appels VOIP à partir de l’écran de verrouillage de l’appareil iOS et gérer des contacts à l’aide de l’application de téléphone **favoris** et **récents** vues.

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contacts et ContactsUI](~/ios/platform/contacts.md)

Avec l’introduction d’iOS 9, Apple a publié deux nouvelles infrastructures, `Contacts` et `ContactsUI`, que le remplacement l’existant carnet d’adresses et les infrastructures d’interface utilisateur du livre adresse utilisés par iOS 8 et versions antérieures.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Sélecteur de document](~/ios/platform/document-picker.md)

Le sélecteur de documents permet aux documents être partagés entre les applications. Ces documents peuvent être stockées dans iCloud ou dans le répertoire d’une autre application. Les documents sont partagés par le biais de l’ensemble de [Extensions de fournisseur de Document](~/ios/platform/extensions.md) l’utilisateur a installé sur leur appareil.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS a intégré, deux applications liés au calendrier : l’Application de calendrier et l’Application des rappels. Il est assez simple de comprendre comment l’Application calendrier gère les données de calendrier, mais l’Application des rappels est moins évidente. Des rappels peuvent avoir réellement des dates qui s’y rapportent en termes de lorsqu’ils sont en raison, quand elles sont traitées, etc. Par conséquent, iOS stocke toutes les données de calendrier, qu’il s’agisse des événements de calendrier ou des rappels, dans un seul emplacement, appelé le *base de données de calendrier*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[extensions iOS](~/ios/platform/extensions.md)

Extensions, introduit dans iOS 8, sont spécialisées `UIViewControllers` qui sont présentées par iOS à l’intérieur des contextes standards tels que dans le **centre de notifications**, comme spécialisé de types de clavier personnalisé demandées par l’utilisateur à effectuer entrée ou autres contextes tels que modifier une photo où l’Extension peut fournir des filtres d’effet spécial.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Graphismes et animation dans iOS](~/ios/platform/graphics-animation-ios/index.md)

Graphismes et Animation dans iOS couvre les principaux concepts de graphiques dans iOS tels que CoreImage, graphismes de base et Core Animation.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple a introduit remise dans iOS 8 et OS X Yosemite (10.10) pour fournir un mécanisme commun pour l’utilisateur à démarrer sur l’un de leurs appareils, des activités de transfert vers un autre appareil exécutant la même application ou une autre application qui prend en charge de la même activité.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

Kit de contrôle d’intégrité fournit une banque de données sécurisé pour les informations relatives à l’intégrité de l’utilisateur. Applications du Kit d’intégrité peuvent, avec l’autorisation explicite de l’utilisateur, lire et écrire dans ce magasin de données et recevoir des notifications lorsque les données pertinentes sont ajoutées. Applications peuvent présenter les données, ou l’utilisateur peut utiliser application de contrôle d’intégrité fourni d’Apple pour afficher un tableau de bord de toutes leurs données.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple a introduit HomeKit dans iOS 8 pour fournir une infrastructure commune pour la découverte et de communiquer avec les appareils domotique à domicile de l’utilisateur. HomeKit fournit une plateforme commune pour la configuration des appareils et configuration des actions pour les contrôler.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[Dans l’application d’achat](~/ios/platform/in-app-purchasing/index.md)

les applications iOS peuvent vendre des produits numériques ou des services à l’aide de StoreKit – un ensemble d’API fournis par iOS qui communiquent avec les serveurs d’Apple pour effectuer des transactions financières avec l’utilisateur par le biais de leur ID Apple. Les APIs StoreKit sont principalement concernés par la récupération des informations produit et la réalisation des transactions : il n’existe aucun composant de l’interface utilisateur. Les applications qui implémentent les achats dans l’application doivent générer leur propre interface utilisateur et effectuer le suivi d’articles achetés avec du code personnalisé pour fournir des produits requis ou des services à l’utilisateur.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[iOS jeux d’API](~/ios/platform/gaming/index.md)

Apple a apporté plusieurs améliorations technologiques à l’API de jeux dans iOS 9 qui le rendent plus facile à implémenter les graphismes de jeu et de l’audio dans une application Xamarin.iOS. Ceux-ci incluent les deux facilité de développement par le biais de frameworks de haut niveau et de maîtriser la puissance du GPU de l’appareil iOS pour l’amélioration de la vitesse et des capacités graphiques.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Intégration de l’application message](~/ios/platform/message-app-integration/index.md)

Nouveau à iOS 10, une Extension d’application Message intègre le **Messages** application et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer des messages interactifs, texte, autocollants et fichiers multimédias.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitâche pour iPad](~/ios/platform/multitasking.md)

iOS 9 prend en charge de multitâche pour l’exécution des deux applications en même temps sur un matériel spécifique iPad. Multitâche pour iPad est pris en charge via les fonctionnalités suivantes : Diapositive sur, mode fractionné et Picture in Picture.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Le livret est une application pour iPhone et iPod touche avec iOS 6. Il stocke et affiche les codes-barres et autres informations pour lier des transactions de clients sur leur téléphone avec le « monde réel ». Passes sont générées par les commerçants et envoyées au client par courrier électronique, URL ou depuis une application iOS du commerçant. Le livret stocke et organise toutes les étapes sur un téléphone et affiche des rappels de réussite sur l’écran de verrouillage en fonction de la date/heure ou l’emplacement de l’appareil.

Ce document présente le livret, à l’aide de l’API du Kit de passer avec Xamarin.iOS et explique comment implémenter des Passes sur votre serveur.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

Kit de photo est une nouvelle infrastructure qui permet aux applications d’interroger la bibliothèque d’images système et créer des interfaces utilisateur personnalisées pour afficher et modifier son contenu. Il inclut un nombre de classes qui représentent l’image et les ressources vidéo, ainsi que les collections de ressources telles que des albums et des dossiers.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Demander une revue de l’application](~/ios/platform/request-app-review.md)

Nouveau à iOS 10.3, le `RequestReview()` méthode permet à une application iOS demander à évaluer ou réviser l’utilisateur. Lorsque cette méthode est appelée dans une application de livraison que l’utilisateur a installé à partir de l’App Store, iOS 10 sera gérer l’évaluation entière et passez en revue le processus pour les développeurs. Étant donné que ce processus est régi par la stratégie de l’App Store, une alerte peut ou ne peut pas s’afficher.

## <a name="search-apisiosplatformsearchindexmd"></a>[API de recherche](~/ios/platform/search/index.md)

Recherche a été développée dans iOS 9 pour fournir de nouvelles façons d’accéder aux informations et des fonctionnalités à l’intérieur d’une application Xamarin.iOS. En utilisant les nouvelles API de recherche d’application, contenu de l’application est effectuée recherche Spotlight et Safari résultats de recherche, remise et les rappels de Siri et les Suggestions. Cela permet aux utilisateurs d’accéder rapidement aux activités et informations approfondies au sein de votre application.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Nouveau à iOS 10, SiriKit permet à une application iOS fournir des services qui sont accessibles à l’utilisateur à l’aide de Siri et l’application Maps sur un appareil iOS à l’aide des Extensions d’application et le nouveau **intentions** et **Intents UI** frameworks.

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Infrastructure des réseaux sociaux](~/ios/platform/social-framework.md)

L’infrastructure des réseaux sociaux fournit une API unifiée pour interagir avec les réseaux sociaux, y compris _Twitter_ et _Facebook_, ainsi que _SinaWeibo_ pour les utilisateurs en Chine.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Reconnaissance vocale](~/ios/platform/speech.md)

iOS 10 inclut une nouvelle API de reconnaissance vocale qui permet à l’application prendre en charge la reconnaissance vocale continue et transcription vocale (à partir de flux audio en direct ou enregistrés) dans le texte.

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

Kit de texte est une nouvelle API qui offre des fonctionnalités de mise en page et rendu de texte puissant. Il s’appuie sur le framework de texte de Core de bas niveau, mais il est beaucoup plus facile à utiliser que le texte de base.

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

Cet article fournit et introduction à l’aide des nouvelles 3D Touch API pour ajouter des mouvements sensibles pression à vos applications Xamarin.iOS qui sont exécutent sur le nouvel iPhone 6 s et iPhone 6 s ainsi que des appareils.

## <a name="touch-idiosplatformtouchidmd"></a>[Touch ID](~/ios/platform/touchid.md)

Touch ID a été introduit dans iOS 7 en tant que méthode d’authentification de l’utilisateur - semblable à un code secret. Toutefois, il était limité à déverrouiller l’appareil, à l’aide de l’App Store, à l’aide d’iTunes et l’authentification de la chaîne de clé iCloud uniquement.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notifications à l’utilisateur](~/ios/platform/user-notifications/index.md)

Nouveau à iOS 10, la Notification utilisateur framework permet de la fourniture et la gestion des notifications locales et distantes. À l’aide de cette infrastructure, l’application ou une Extension d’application permettre planifier la remise des notifications locales en spécifiant un ensemble de conditions comme emplacement ou de l’heure.

## <a name="wide-coloriosplatformwide-colormd"></a>[Large couleur](~/ios/platform/wide-color.md)

iOS 10 et macOS Sierra améliore la prise en charge pour les formats de pixel de la plage étendue et des espaces d’une gamme de couleurs dans tout le système, y compris les infrastructures telles que graphismes de base, l’Image de Core, complète et AVFoundation. Prise en charge pour les appareils avec des couleurs large est davantage facilité en fournissant ce comportement dans toute la pile de graphique entière.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Liaison Objective-C](binding-objective-c/index.md)

Lorsque vous travaillez sur iOS, vous pouvez rencontrer des cas où vous souhaitez utiliser une bibliothèque Objective-C de fournisseurs tiers. Dans ces situations, vous pouvez utiliser des projets de liaison de MonoTouch pour créer une liaison C# aux bibliothèques Objective-C natifs. Le projet utilise les mêmes outils que nous utilisons pour afficher les API iOS à C#. Ce document décrit comment lier les API Objective-C.

## <a name="referencing-native-librariesnative-interopmd"></a>[Référencement des bibliothèques natives](native-interop.md)

Xamarin.iOS prend en charge la liaison avec les bibliothèques C natives et les bibliothèques Objective-C. Ce document explique comment lier vos bibliothèques C natifs avec votre projet Xamarin.iOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Frameworks incorporés](embedded-frameworks.md)

Explique comment incorporer des infrastructures d’utilisateur Objective-C dans les applications Xamarin.iOS.
