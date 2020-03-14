---
title: Introduction à iOS 8
description: Avec iOS 8, Apple a fourni une multitude de nouvelles infrastructures et API pour les développeurs. Dans ce guide, nous allons introduire ces nouvelles API et découvrir comment iOS 8 peut tirer parti des développeurs et des utilisateurs.
ms.prod: xamarin
ms.assetid: 33AD66C0-3743-49FE-9DCE-88ED3A16BA63
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 9353cffd924688058c1495b9258cc7f0e0ce7b82
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79304883"
---
# <a name="introduction-to-ios-8"></a>Introduction à iOS 8

_Avec iOS 8, Apple a fourni une multitude de nouvelles infrastructures et API pour les développeurs. Dans ce guide, nous allons introduire ces nouvelles API et découvrir comment iOS 8 peut tirer parti des développeurs et des utilisateurs._

iOS 7 a changé visuellement la totalité de l’interface utilisateur iOS à partir de ce que les utilisateurs et les développeurs avaient à l’attendre, directement à partir du premier système d’exploitation iPhone. IOS 8 continue avec cela en fournissant de nombreux frameworks pour les développeurs, ce qui permet aux utilisateurs de contrôler presque tous les aspects de leur vie directement à partir de leur iPhone. Par exemple, l’intégrité et la pertinence peuvent être analysées avec *HealthKit*, les codes secrets sont obsolète avec l’authentification biométrique à l’aide de *LocalAuthentication*, les *extensions d’application* ouvrent un canal de communication entre les applications tierces, et *HomeKit* permet de transformer votre maison en domicile. 

Si iOS 7 s’est consacré à la délightisation des utilisateurs, iOS 8 se concentre sur la déséclairage des développeurs avec une large gamme de ces nouveaux outils miam. 

Ce guide présente les nouvelles API pour les développeurs Xamarin. iOS.  

Il existe également quelques API qui ont été dépréciées dans iOS 8, qui sont détaillées à la fin de ce document.

## <a name="requirements"></a>Spécifications

Les éléments suivants sont requis pour créer des applications iOS 8 dans Visual Studio pour Mac :

- **Xcode 7 et iOS 8 ou version ultérieure** : les dernières API Xcode et iOS d’Apple doivent être installées et configurées sur l’ordinateur du développeur.
- **Visual Studio pour Mac** : la dernière version de Visual Studio pour Mac doit être installée et configurée sur l’appareil de l’utilisateur.
- **appareil ou simulateur iOS 8** : un appareil iOS exécutant la dernière version d’iOS 8 à des fins de test.

## <a name="home-and-leisure"></a>Immobilier et loisirs

iOS 8 a aidé à intégrer des Apple, et l’appareil iOS directement au cœur de votre foyer par le biais de l’utilisation de HomeKit et HealthKit. Dans cette section, nous allons étudier la façon dont ces nouveaux frameworks fonctionnent et comment ils peuvent être intégrés à votre application Xamarin. iOS.

## <a name="homekit"></a>HomeKit

Le contrôle de vos appliances à partir de votre iPhone n’est pas une nouvelle application de technologie. de nombreux produits connectés à l’hôte peuvent être contrôlés via une application iOS. Toutefois, HomeKit va encore plus loin en promouvant un protocole commun pour les appareils domotiques, et en mettant une API publique à la disposition de certains fabricants, tels que iHome, Philips et Honeywell. Pour l’utilisateur, cela signifie qu’ils peuvent contrôler presque tous les aspects de leur foyer en toute transparence depuis l’intérieur d’une application. Il est inutile pour eux de savoir qu’ils utilisent une ampoule à teinte Philips ou une alarme d’imbrication. Les utilisateurs peuvent également chaîner de nombreux processus d’hébergement intelligents en « scènes ».

Avec HomeKit, les applications tierces et Siri peuvent découvrir des accessoires et les ajouter à leur base de données de configuration personnelle personnelle, modifier et agir sur ces données, et communiquer avec les accessoires et leurs services pour effectuer une action.

### <a name="configuration"></a>Configuration

Le diagramme ci-dessous montre la hiérarchie de base de la configuration des accessoires HomeKit :

![](introduction-to-ios8-images/image1.png "This diagram shows the basic hierarchy of the configuration of HomeKit accessories")

Pour commencer à utiliser HomeKit, les développeurs doivent s’assurer que le service HomeKit est sélectionné pour le profil de configuration. Apple a également fourni aux développeurs un complément HomeKit Simulator pour Xcode. Vous pouvez le trouver dans le [Centre de développement Apple](https://developer.apple.com/downloads/index.action), sous `Hardware IO Tools for Xcode`. 

Pour plus d’informations, consultez notre guide [HomeKit](~/ios/platform/homekit.md) .

## <a name="healthkit"></a>HealthKit

HealthKit est une infrastructure introduite dans iOS 8 qui fournit une banque de données centralisée, coordonnée et sécurisée pour les informations relatives à l’intégrité. Le système d’exploitation garantit la confidentialité et la sécurité des informations d’intégrité et, avec l’application d’intégrité, un tableau de bord pour l’utilisateur. Avec l’autorisation de l’utilisateur, les applications peuvent lire et écrire un large éventail d’informations d’intégrité.

Pour plus d’informations sur l’utilisation de ce dans votre application Xamarin. iOS, reportez-vous au guide [Introduction à HealthKit](~/ios/platform/healthkit.md) .

## <a name="extending-iphone-functionality"></a>Extension de la fonctionnalité iPhone
Avec iOS8, les développeurs disposent d’un plus grand contrôle sur les utilisateurs qui peuvent utiliser leur application, ainsi que des capacités accrues pour une communication plus ouverte entre les applications tierces. Les fonctionnalités telles que les extensions d’application et le sélecteur de documents ouvrent un monde de possibilités pour la façon dont les applications peuvent être utilisées dans l’écosystème d’Apple.

### <a name="app-extensions"></a>Extensions d’application
Les extensions d’application, pour simplifier, sont un moyen pour les applications tierces de communiquer les unes avec les autres. Pour maintenir les normes de sécurité élevées et respecter l’intégrité des applications sandbox, cette communication ne se produit pas directement entre les applications. Au lieu de cela, elle est exécutée par une *extension* au milieu.

La première étape de la création d’une extension d’application consiste à définir le point d’extension correct, ce qui est important pour garantir le comportement et la disponibilité des API appropriées. Pour créer une extension d’application dans Visual Studio pour Mac, ajoutez-la à une application existante en ajoutant un nouveau projet à votre solution.

Dans la boîte de dialogue **nouveau projet** , accédez à **C#**  > **iOS** > **API unifiée** **Extensions**de > , comme illustré dans la capture d’écran ci-dessous :

![](introduction-to-ios8-images/image2.png "Creating a new extension")

La boîte de dialogue Nouveau projet fournit sept nouveaux modèles de projet pour la création d’extensions d’application et sont décrits ci-dessous. Notez que la plupart des extensions sont liées à d’autres nouvelles API dans iOS, telles que le sélecteur de documents :

- **Action** : permet aux développeurs de créer des boutons d’action personnalisés uniques permettant aux utilisateurs d’effectuer certaines tâches.
- **Clavier personnalisé** : cela permet aux développeurs d’ajouter à la plage de Claviers Apple intégrés en ajoutant leur propre clavier personnalisé. Le clavier populaire, Swype l’utilise pour mettre le clavier sur iOS.
- **Sélecteur de documents** : il contient un contrôleur d’affichage du sélecteur de documents qui permet aux utilisateurs d’accéder aux fichiers en dehors du bac à sable de l’application.
- **Fournisseur de fichiers de sélecteur de documents** : fournit un stockage sécurisé pour les fichiers à l’aide du sélecteur de documents.
- **Édition photo** : développe les filtres et les outils d’édition déjà fournis par Apple dans l’application photos pour offrir aux utilisateurs plus de contrôle et d’autres options lors de la modification de leurs photos.
- **Aujourd’hui** : offre aux applications la possibilité d’afficher des widgets dans la section Today du centre de notification.

Pour plus d’informations sur l’utilisation des extensions d’application dans Xamarin, reportez-vous au guide [Présentation des extensions d’application](~/ios/platform/extensions.md) .

### <a name="touch-id"></a>Touch ID

Touch ID a été introduit dans iOS 7 comme un moyen d’authentifier l’utilisateur, comme un code secret. Toutefois, il était limité au déverrouillage de l’appareil, à l’utilisation de l’App Store, à l’utilisation d’iTunes et à l’authentification du trousseau iCloud uniquement 

Il existe deux façons d’utiliser Touch ID comme mécanisme d’authentification dans les applications iOS 8 à l’aide de l’API d’authentification locale. Il n’est actuellement pas possible d’utiliser l’authentification locale pour l’authentification à distance.

Tout d’abord, il aide les services de trousseau existants grâce à l’utilisation de nouvelles listes de Access Control de trousseau (ACL). Les données de trousseau peuvent être déverrouillées avec l’authentification réussie de l’empreinte digitale d’un utilisateur.

Deuxièmement, LocalAuthentication fournit deux méthodes pour authentifier votre application localement. Les développeurs doivent utiliser `CanEvaluatePolicy` pour déterminer si l’appareil est en capacité d’accepter Touch ID, puis `EvaluatePolicy` pour démarrer l’opération d’authentification.

Pour plus d’informations sur Touch ID et pour savoir comment l’intégrer dans une application Xamarin. iOS, consultez [Touch ID et face ID dans Xamarin. iOS](~/ios/platform/touch-id-face-id.md) guides.

### <a name="document-picker"></a>Sélecteur de document

Le sélecteur de documents fonctionne avec un lecteur iCloud des utilisateurs pour permettre à l’utilisateur d’ouvrir des fichiers qui ont été créés dans une autre application, de les importer et de les manipuler et de les réexporter. Cela crée un flux de travail intuitif et, par conséquent, une expérience bien meilleure pour les utilisateurs. la synchronisation iCloud va encore plus loin : toutes les modifications apportées dans une application sont également reflétées de manière cohérente sur tous vos appareils.

Pour en savoir plus sur le sélecteur de documents plus en détail et pour savoir comment l’intégrer dans une application Xamarin. iOS, reportez-vous à la [Présentation du Guide du sélecteur de documents](~/ios/platform/document-picker.md) .

### <a name="handoff"></a>Handoff

Le transfert, qui fait partie de la fonctionnalité de continuité plus importante, va plus loin pour intégrer OS X et iOS. Cela comprend le port d’insertion multiplateforme, la possibilité d’effectuer des appels iPhone, SMS sur iPad et Mac, ainsi que des améliorations apportées à la connexion de votre iPhone.

Le transfert fonctionne avec iOS 8 et Yosemite, et requiert qu’un compte iCloud soit connecté à tous les appareils que vous souhaitez utiliser. Il doit fonctionner avec la plupart des applications Apple pré-installées, notamment Safari, iWork, Maps, Calendars et contacts.

Pour plus d’informations, consultez notre guide de [remise](~/ios/platform/handoff.md) .

## <a name="unified-storyboards"></a>Storyboards unifiés
iOS 8 offre un nouveau mécanisme plus simple d’utilisation pour créer l’interface utilisateur : le Storyboard unifié. Avec une seule table de montage séquentiel pour couvrir toutes les différentes tailles d’écran matérielles, des vues rapides et réactives peuvent être créées dans un style vrai « une fois de conception, utiliser plusieurs ».

Avant iOS8, les développeurs utilisaient `UIInterfaceOrientation` pour faire la distinction entre les modes portrait et paysage, et `UIInterfaceIdiom` pour faire la distinction entre les appareils iOS. Dans iOS8, il n’est plus nécessaire de créer des storyboards distincts pour les appareils iPhone et iPad : l’orientation et l’appareil sont déterminés à l’aide des *classes de taille*.

Chaque appareil est défini par une classe de taille, à la fois dans l’axe vertical et horizontal, et il existe deux types de classes de taille dans iOS 8 :

- **Normal** : il s’agit d’une grande taille d’écran (par exemple, un iPad) ou d’un gadget qui donne l’impression d’une grande taille (par exemple, un UIScrollView
- **Compact** : pour les appareils plus petits (tels que l’iPhone). Cette taille prend en compte l’orientation de l’appareil.

Si les deux concepts sont utilisés ensemble, le résultat est une grille 2 x 2 qui définit les différentes tailles possibles qui peuvent être utilisées dans les deux orientations différentes, comme illustré dans le diagramme suivant :

![](introduction-to-ios8-images/image3.png "A diagram representing the 2 x 2 grid that defines the different possible sizes that can be used in both the differing orientations")

Pour plus d’informations sur les classes de taille, reportez-vous à la [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md).

## <a name="photo-kit"></a>Kit de photos
Le kit photo est une nouvelle infrastructure qui permet aux applications d’interroger la bibliothèque d’images système et de créer des interfaces utilisateur personnalisées pour afficher et modifier son contenu. Il comprend un certain nombre de classes qui représentent les ressources d’images et de vidéos, ainsi que des collections d’éléments multimédias tels que des albums et des dossiers.

Pour plus d’informations, consultez notre guide [PhotoKit](~/ios/platform/photokit.md) .

## <a name="games"></a>Jeux

<a name="scenekit" />

### <a name="scene-kit"></a>Kit de scène

Scene Kit est une API graphique de scène 3D qui simplifie l’utilisation des graphiques 3D. Elle a été introduite pour la première fois dans le système d’exploitation X 10,8 et est désormais disponible dans iOS 8. Avec Scene Kit, la création de visualisations 3D immersifs et de jeux informels 3D ne nécessite pas d’expertise dans OpenGL. En s’appuyant sur les concepts courants du graphique de scène, Scene Kit résume les complexités de OpenGL et OpenGL ES, ce qui facilite l’ajout de contenu 3D à une application. Toutefois, si vous êtes un expert OpenGL, Scene kit offre une excellente prise en charge de la liaison directe avec OpenGL. Il comprend également de nombreuses fonctionnalités qui complètent les graphiques 3D, telles que la physique, et s’intègrent parfaitement avec plusieurs autres frameworks Apple, tels que l’animation principale, l’image principale et le sprite kit.

Pour plus d’informations, consultez notre documentation [SceneKit](~/ios/platform/gaming/scenekit.md) .

<a name="spritekit" />

### <a name="sprite-kit"></a>Sprite Kit

Sprite Kit, l’infrastructure de jeu 2D d’Apple, présente de nouvelles fonctionnalités intéressantes dans iOS 8 et OS X Yosemite. Cela inclut l’intégration avec Scene Kit, la prise en charge du nuanceur, l’éclairage, les ombres, les contraintes, la génération normale de cartes et les améliorations physiques. En particulier, les nouvelles fonctionnalités physiques rendent très facile l’ajout d’effets réalistes à un jeu.

Pour plus d’informations, consultez notre documentation [SpriteKit](~/ios/platform/gaming/spritekit.md) .

## <a name="other-changes"></a>Autres modifications
Outre les principales modifications apportées à iOS 8 décrites ci-dessus, Apple a mis à jour en plus de nombreux Frameworks existants. Celles-ci sont détaillées ci-dessous :

- **[Image principale](https://developer.apple.com/library/prerelease/ios/documentation/GraphicsImaging/Reference/CoreImagingRef/index.html#//apple_ref/doc/uid/TP40001171)** : Apple s’est développée sur son infrastructure de traitement d’images en ajoutant une meilleure prise en charge de la détection des régions rectangulaires et des codes QR dans les images. Mike Bluestein explore cela dans son billet de blog [sur la détection d’images dans iOS 8](https://blog.xamarin.com/image-detection-in-ios-8/)

## <a name="deprecated-apis"></a>API déconseillées
Avec toutes les améliorations apportées à iOS 8, un certain nombre d’API sont dépréciées. Certaines d’entre elles sont détaillées ci-dessous.

- **[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)** : les méthodes et les propriétés utilisées pour l’inscription des notifications distantes sont dépréciées. Il s’agit de registerForRemoteNotificationTypes et enabledRemoteNotificationTypes.
- **[UIViewController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/cl/UIViewController)** – les classes de caractéristiques et de taille ont remplacé les méthodes et les propriétés utilisées pour décrire l’orientation de l’interface. Pour plus d’informations sur l’utilisation de ces storyboards, reportez-vous à la [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md) .

- **[UISearchDisplayController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UISearchDisplayController_Class/index.html#//apple_ref/occ/cl/UISearchDisplayController)** : ce a été remplacé par UISearchController dans iOS8.

## <a name="summary"></a>Résumé
Dans cet article, nous avons abordé certaines des nouvelles fonctionnalités introduites par Apple dans iOS 8.

## <a name="related-links"></a>Liens connexes

- [UIKitEnhancements (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-uikitenhancements)
- [Présentation des extensions d’application](~/ios/platform/extensions.md)
- [Présentation de CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)
- [Présentation du sélecteur de documents](~/ios/platform/document-picker.md)
- [Présentation de HealthKit](~/ios/platform/healthkit.md)
- [Présentation des contrôles d’appareil photo manuels](~/ios/user-interface/controls/intro-to-manual-camera-controls.md)
- [Id tactile et ID de visage avec Xamarin. iOS](~/ios/platform/touch-id-face-id.md)
- [Présentation des storyboards unifiés](~/ios/user-interface/storyboards/unified-storyboards.md)
