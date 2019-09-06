---
title: vue d’ensemble des fonctionnalités de la plateforme iOS
description: Ce document contient des liens vers divers guides qui décrivent les fonctionnalités introduites dans différentes versions d’iOS et d’autres fonctionnalités de la plateforme iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: d88832dd4fd69019f9905fb779c5572ba9a689eb
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70281022"
---
# <a name="ios-platform-features-overview"></a>vue d’ensemble des fonctionnalités de la plateforme iOS

Cette page répertorie les dernières versions d’iOS, ainsi que la mise en surbrillance des frameworks Apple auxquels vous pouvez accéder avec Xamarin. iOS.

## <a name="ios-releases"></a>versions iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Présentation d’iOS 13 preview](~/ios/platform/ios13/index.md) | Ce document décrit Xamarin. iOS 13 preview.|
| [Introduction à iOS 12](~/ios/platform/introduction-to-ios12/index.md) | Ce document décrit les fonctionnalités d’iOS 12 disponibles lors de la création d’applications Xamarin. iOS.|
| [Introduction à iOS 11](~/ios/platform/introduction-to-ios11/index.md) | Ce document décrit les fonctionnalités nouvelles et mises à jour d’iOS 11 et de Xcode 9, telles que ARKit, Core ML, Core NFC, glisser-déplacer, MapKit, PDFKit, SiriKit et vision. Elle contient des liens vers des guides qui décrivent comment utiliser ces fonctionnalités avec Xamarin. iOS. |
| [Introduction à iOS 10](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 comprend plusieurs nouvelles API et services qui vous permettent de développer des applications avec de nouvelles fonctionnalités et fonctionnalités. Avec iOS 10, les applications ont de nouvelles fonctionnalités telles que l’extension des cartes, des messages, du téléphone et des Siri. Cette section présente comment pour tirer parti de ces fonctionnalités dans une application Xamarin. iOS. |
| [Introduction à iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | Cette section définit les modifications apportées à iOS 9 lors de la mise à niveau à partir d’iOS 8 et l’utilisation de ces fonctionnalités dans une application Xamarin. iOS. |
| [Introduction à iOS 8](~/ios/platform/introduction-to-ios8.md)         | iOS 8 a apporté un grand nombre de modifications au système d’exploitation à partir d’iOS 7. Ici, nous montrons ce qu’ils sont et comment les utiliser. |
| [Introduction à iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | À propos des nouvelles API majeures introduites dans iOS 7, notamment les transitions de contrôleur d’affichage, les améliorations apportées aux animations UIView, à UIKit Dynamics et au kit de texte. |
| [Introduction à iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Explications des fonctionnalités introduites dans iOS 6, y compris les vues de collection, le kit de réussite, le kit d’événements et l’infrastructure sociale. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay a été introduite avec iOS 8, permettant aux utilisateurs de payer des biens physiques tels que des aliments, du divertissement et des appartenances via leurs appareils iOS. Elle est disponible sur iPhone 6 et iPhone 6 plus, et peut également être associée aux Apple Watch pour les achats en magasin. Lorsqu’il est utilisé sur un iPhone, il utilise Touch ID pour confirmer et autoriser les transactions sur la carte de crédit ou de débit d’un utilisateur.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

La nouvelle API CallKit dans iOS 10 permet aux applications VOIP de s’intégrer à l’interface utilisateur iPhone et de fournir une interface et une expérience familières à l’utilisateur final. Avec cette API, les utilisateurs peuvent afficher et interagir avec les appels VOIP à partir de l’écran de verrouillage de l’appareil iOS et gérer les contacts à l’aide des **favoris** de l’application téléphonique et des vues **récentes** .

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contacts et ContactsUI](~/ios/platform/contacts.md)

Avec l’introduction d’iOS 9, Apple a publié deux nouveaux frameworks, `Contacts` et `ContactsUI`, qui remplacent les infrastructures d’interface utilisateur du carnet d’adresses et du carnet d’adresses existantes utilisées par IOS 8 et les versions antérieures.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Sélecteur de document](~/ios/platform/document-picker.md)

Le sélecteur de documents permet de partager des documents entre les applications. Ces documents peuvent être stockés dans iCloud ou dans un répertoire d’application différent. Les documents sont partagés via l’ensemble des [extensions de fournisseur de documents](~/ios/platform/extensions.md) que l’utilisateur a installées sur leur appareil.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS a deux applications liées au calendrier intégrées : l’application de calendrier et l’application de rappels. Il est assez simple de comprendre comment l’application de calendrier gère les données de calendrier, mais l’application de rappels est moins évidente. Des rappels peuvent avoir réellement des dates qui s’y rapportent en termes de lorsqu’ils sont en raison, quand elles sont traitées, etc. Par conséquent, iOS stocke toutes les données de calendrier, qu’il s’agisse d’événements de calendrier ou de rappels, à un seul emplacement, appelé *base de données de calendrier*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[extensions iOS](~/ios/platform/extensions.md)

Les extensions, telles qu’introduites dans iOS 8 `UIViewControllers` , sont spécialisées, présentées par iOS dans des contextes standard tels que dans le **Centre de notifications**, en tant que types de clavier personnalisés demandés par l’utilisateur pour effectuer une entrée spécialisée ou d’autres contextes. à l’instar de la modification d’une photo dans laquelle l’extension peut fournir des filtres d’effet spéciaux.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Graphiques et animation dans iOS](~/ios/platform/graphics-animation-ios/index.md)

Les graphiques et les animations dans iOS couvrent les principaux concepts des graphiques dans iOS, tels que CoreImage, Core Graphics et Core Animation.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple a introduit la remise dans iOS 8 et OS X Yosemite (10,10) pour fournir un mécanisme commun permettant à l’utilisateur de transférer des activités démarrées sur l’un de ses appareils, à un autre appareil exécutant la même application ou une autre application qui prend en charge la même activité.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

Health kit fournit une banque de données sécurisée pour les informations relatives à l’intégrité de l’utilisateur. Les applications du kit d’intégrité peuvent, avec l’autorisation explicite de l’utilisateur, lire et écrire dans ce magasin de données et recevoir des notifications lorsque des données pertinentes sont ajoutées. Les applications peuvent présenter les données, ou les utilisateurs peuvent utiliser l’application d’intégrité fournie par Apple pour afficher un tableau de bord de toutes leurs données.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple a introduit HomeKit dans iOS 8 pour fournir une infrastructure commune pour la découverte et la communication avec les appareils domotiques de l’utilisateur. HomeKit fournit une plateforme commune pour la configuration des appareils et la configuration des actions pour les contrôler.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[Achats dans l’application](~/ios/platform/in-app-purchasing/index.md)

les applications iOS peuvent vendre des produits ou services numériques à l’aide de StoreKit, un ensemble d’API fournies par iOS, qui communiquent avec les serveurs d’Apple pour effectuer des transactions financières avec l’utilisateur via son IDENTIFIant Apple. Les API StoreKit concernent principalement la récupération des informations sur les produits et la réalisation des transactions. il n’existe pas de composant d’interface utilisateur. Les applications qui implémentent des achats dans l’application doivent créer leur propre interface utilisateur et effectuer le suivi des éléments achetés avec du code personnalisé pour fournir les produits ou services requis à l’utilisateur.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[API de jeu iOS](~/ios/platform/gaming/index.md)

Apple a apporté plusieurs améliorations technologiques aux API de jeu dans iOS 9 qui facilitent l’implémentation de graphiques et de sons de jeu dans une application Xamarin. iOS. Celles-ci incluent la facilité de développement via des frameworks de haut niveau et l’exploitation de la puissance du GPU de l’appareil iOS pour améliorer la vitesse et les capacités graphiques.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Intégration d’application de message](~/ios/platform/message-app-integration/index.md)

Nouveauté d’iOS 10, une extension d’application de message s’intègre à l’application **messages** et présente de nouvelles fonctionnalités à l’utilisateur. L’extension peut envoyer du texte, des autocollants, des fichiers multimédias et des messages interactifs.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitâche pour iPad](~/ios/platform/multitasking.md)

iOS 9 ajoute la prise en charge de la multitâche pour l’exécution de deux applications en même temps sur un matériel iPad spécifique. Le multitâche pour iPad est pris en charge via les fonctionnalités suivantes : Glisser-déplacer, mode fractionné & image dans l’image.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Le responsable des appareils est une application pour les iPhone et l’iPod s’appuyant sur iOS 6. Il stocke et affiche des codes-barres et d’autres informations pour lier les transactions client sur leur téléphone avec le « monde réel ». Les passes sont générées par les commerçants et envoyées au client par courrier électronique, URL ou à partir d’une application iOS propre à un commerçant. Le répertoire de stockage et d’organisation de tous les passages sur un téléphone, et affiche les rappels de passage sur l’écran de verrouillage en fonction de la date et de l’heure ou de l’emplacement de l’appareil.

Ce document présente le fichier au moyen de l’API Pass kit avec Xamarin. iOS, et explique comment implémenter des passes sur votre serveur.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

Le kit photo est une nouvelle infrastructure qui permet aux applications d’interroger la bibliothèque d’images système et de créer des interfaces utilisateur personnalisées pour afficher et modifier son contenu. Il comprend un certain nombre de classes qui représentent les ressources d’images et de vidéos, ainsi que des collections d’éléments multimédias tels que des albums et des dossiers.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Demander une révision de l’application](~/ios/platform/request-app-review.md)

Nouveauté d’iOS 10,3, la `RequestReview()` méthode permet à une application iOS de demander à l’utilisateur de la noter ou de la passer en revue. Lorsque cette méthode est appelée dans une application d’expédition que l’utilisateur a installée à partir de l’App Store, iOS 10 gère l’intégralité du processus d’évaluation et de révision pour le développeur. Étant donné que ce processus est régi par la stratégie App Store, une alerte peut être affichée ou non.

## <a name="search-apisiosplatformsearchindexmd"></a>[API de recherche](~/ios/platform/search/index.md)

La recherche a été développée dans iOS 9 pour offrir de nouvelles façons d’accéder à des informations et à des fonctionnalités à l’intérieur d’une application Xamarin. iOS. À l’aide des nouvelles API de recherche d’application, le contenu de l’application peut faire l’objet d’une recherche via les résultats de recherche Spotlight et Safari, les rappels et les suggestions de Siri et de remise. Cela permet aux utilisateurs d’accéder rapidement aux activités et aux informations contenues dans votre application.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Nouveauté d’iOS 10, SiriKit permet à une application iOS de fournir des services accessibles à l’utilisateur à l’aide de Siri et de l’application Maps sur un appareil iOS à l’aide d’extensions d’application, ainsi que **des nouvelles infrastructures** d' **interface utilisateur** intentions et intentions.

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Infrastructure sociale](~/ios/platform/social-framework.md)

L’infrastructure sociale fournit une API unifiée pour l’interaction avec les réseaux sociaux, y compris _Twitter_ et _Facebook_, ainsi que _SinaWeibo_ pour les utilisateurs en Chine.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Reconnaissance vocale](~/ios/platform/speech.md)

iOS 10 comprend une nouvelle API de reconnaissance vocale qui permet à l’application de prendre en charge la reconnaissance vocale continue et de transcrire la parole (des flux audio en direct ou enregistrés) en texte.

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

Le kit de texte est une nouvelle API qui offre de puissantes fonctionnalités de rendu et de disposition du texte. Il s’appuie sur l’infrastructure de texte de base de bas niveau, mais il est beaucoup plus facile à utiliser que le texte de base.

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

Cet article fournit une introduction à l’utilisation des nouvelles API tactiles 3D pour ajouter des gestes sensibles à la pression à vos applications Xamarin. iOS qui s’exécutent sur les nouveaux appareils iPhone 6S et iPhone 6S plus.

## <a name="touch-idiosplatformtouchidmd"></a>[Touch ID](~/ios/platform/touchid.md)

Touch ID a été introduit dans iOS 7 comme un moyen d’authentifier l’utilisateur, similaire à un code secret. Toutefois, il était limité au déverrouillage de l’appareil à l’aide de l’App Store, en utilisant iTunes et en authentifiant uniquement le trousseau iCloud.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notifications de l’utilisateur](~/ios/platform/user-notifications/index.md)

Nouveauté d’iOS 10, l’infrastructure de notification utilisateur permet la remise et la gestion des notifications locales et distantes. À l’aide de ce Framework, l’application ou l’extension d’application peut planifier la remise de notifications locales en spécifiant un ensemble de conditions, telles que l’emplacement ou l’heure de la journée.

## <a name="wide-coloriosplatformwide-colormd"></a>[Large couleur](~/ios/platform/wide-color.md)

iOS 10 et macOS Sierra améliorent la prise en charge des formats de pixel étendus et des espaces de couleurs à grande échelle dans le système, y compris les infrastructures telles que Core Graphics, Core image, Metal et AVFoundation. La prise en charge des appareils avec des affichages de couleurs larges est encore plus facilitée en fournissant ce comportement dans l’ensemble de la pile graphique.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Liaison Objective-C](binding-objective-c/index.md)

Lorsque vous travaillez sur iOS, vous pouvez rencontrer des cas où vous souhaitez consommer une bibliothèque objective-C tierce. Dans ces situations, vous pouvez utiliser des projets de liaison monotactiles pour C# créer une liaison aux bibliothèques objective-C natives. Le projet utilise les mêmes outils que ceux que nous utilisons pour importer les API C#iOS. Ce document décrit comment lier des API objective-C.

## <a name="referencing-native-librariesnative-interopmd"></a>[Référencement des bibliothèques natives](native-interop.md)

Xamarin. iOS prend en charge la liaison avec les bibliothèques C natives et les bibliothèques objective-C. Ce document explique comment lier vos bibliothèques C natives à votre projet Xamarin. iOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Frameworks incorporés](embedded-frameworks.md)

Explique comment incorporer des infrastructures utilisateur objective-C dans des applications Xamarin. iOS.
