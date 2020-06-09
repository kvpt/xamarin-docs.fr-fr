---
title: Introduction à iOS 9
description: Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans iOS 9 pour les développeurs Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: de4b6e8b95eed33e7fb38baf51a0da73cef313c0
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84574130"
---
# <a name="introduction-to-ios-9"></a>Introduction à iOS 9

_Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans iOS 9 pour les développeurs Xamarin. iOS._

![](images/ios9-sml.png "The iOS 9 logo")

Apple a ajouté plusieurs nouvelles API et services dans iOS 9, ainsi que de nombreuses améliorations apportées aux fonctionnalités existantes.

## <a name="3d-touch"></a>3D Touch

Nouveauté d’iOS 9 et de iPhone 6S et iPhone 6S plus, le toucher 3D ajoute des gestes sensibles à la pression à vos applications iOS. Avec l’interface tactile en 3D, une application iPhone est désormais en mesure de savoir que l’utilisateur touche l’écran de l’appareil, il peut également déterminer la pression exercée par l’utilisateur et répondre aux différents niveaux de pression.

l’interface tactile 3D fournit les fonctionnalités suivantes à votre application :

- **Sensibilité** à la pression : les applications peuvent désormais mesurer la clarté ou la lumière que l’utilisateur touche l’écran et tirer parti de ces informations. Par exemple, une application de peinture peut rendre une ligne plus épaisse ou plus fine en fonction de la façon dont l’utilisateur touche l’écran.
- **Aperçu et pop** : votre application peut désormais permettre à l’utilisateur d’interagir avec ses données sans avoir à quitter leur contexte actuel. En appuyant sur l’écran, les utilisateurs peuvent *lire* l’élément qui les intéresse (par exemple, afficher un message). En appuyant sur une pression plus complexe, elles peuvent *apparaître* dans l’élément.
- **Actions rapides** : pensez à des actions rapides comme les menus contextuels qui peuvent être dépilés lorsqu’un utilisateur clique avec le bouton droit sur un élément dans une application de bureau. À l’aide des actions rapides, vous pouvez ajouter des raccourcis communs, rapides et faciles à accéder aux fonctions de votre application à partir de l’icône d’écran d’accueil sur l’appareil iOS.

Pour en savoir plus, consultez notre [Introduction au Guide tactile 3D](~/ios/platform/3d-touch.md) .

## <a name="app-transport-security"></a>Sécurité de transport de l’application

Dans iOS 9, la sécurité d’application transport (ATS) applique des connexions sécurisées entre les ressources Internet (par exemple, le serveur principal de l’application) et votre application. ATS garantit que toutes les communications Internet sont conformes aux meilleures pratiques en matière de connexion, ce qui empêche la divulgation accidentelle d’informations sensibles, directement via votre application ou une bibliothèque qu’elle consomme.

Dans la mesure où ATS est activé par défaut dans les applications conçues pour iOS 9 et OS X 10,11 (El Capitan), toutes les connexions utilisant [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) ou [passer](xref:Foundation.NSUrlSession) seront soumises aux exigences de sécurité ATS. Si vos connexions ne répondent pas à ces exigences, elles échouent avec une exception.

Pour en savoir plus sur ATS, consultez notre guide de [sécurité App transport](~/ios/app-fundamentals/ats.md) .

<a name="multitasking"></a>

## <a name="multitasking-for-ipad"></a>Multitâche pour iPad

Avec iOS 9, Apple a ajouté la prise en charge de la multitâche pour l’exécution de deux applications en même temps sur un matériel iPad spécifique. Par conséquent, vos applications Xamarin. iOS ne peuvent plus supposer qu’il s’agit de la seule application en cours d’exécution à un moment donné ou qu’elles ont accès à la totalité de l’écran ou aux ressources de l’appareil.

Le multitâche pour iPad est pris en charge via les fonctionnalités suivantes :

- **Glisser-déplacer** : permet à l’utilisateur d’exécuter temporairement une deuxième application iOS dans un panneau coulissant (à droite ou à gauche de l’écran en fonction de la direction de la langue) qui couvre approximativement 25% de l’application principale en cours d’exécution. La glissement est disponible uniquement sur un iPad Pro, iPad air, iPad air 2, iPad mini 2, iPad Mini 3 ou iPad mini 4.
- **Mode fractionné** : sur le matériel iPad pris en charge (iPad air 2, iPad mini 4 et iPad Pro uniquement), l’utilisateur peut choisir une deuxième application et l’exécuter côte à côte avec l’application en cours d’exécution en mode fractionné. L’utilisateur peut contrôler le pourcentage de l’écran principal occupé par chaque application.
- **Image dans Picture** : pour les applications qui retournent du contenu vidéo, la vidéo peut maintenant être lue dans une fenêtre déplaçable et redimensionnable qui flotte sur les autres applications en cours d’exécution sur l’appareil iOS. L’utilisateur dispose d’un contrôle total sur la taille et la position de cette fenêtre. Picture in Picture est disponible uniquement sur un iPad Pro, iPad air, iPad air 2, iPad mini 2, iPad Mini 3 ou iPad mini 4.

Pour en savoir plus sur les nouvelles capacités multitâche d’iOS 9, consultez notre guide sur l’emploi [de la multitâche pour iPad](~/ios/platform/multitasking.md) .

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Nouvelles infrastructures d’interface utilisateur pour les contacts et les contacts

Avec l’introduction d’iOS 9, Apple a publié deux nouveaux frameworks, [contacts](xref:Contacts) et [ContactsUI](xref:ContactsUI), qui remplacent les infrastructures d’interface utilisateur du carnet d’adresses et du carnet d’adresses existantes utilisées par IOS 8 et versions antérieures.

Ces nouvelles infrastructures orientées objet fournissent les éléments suivants :

- **Contacts** : fournit un accès Xamarin. iOS aux informations de contact de l’utilisateur. Étant donné que la plupart des applications requièrent uniquement un accès en lecture seule, cette infrastructure a été optimisée pour l’accès en lecture seule thread-safe.
- **ContactsUI** : fournit des éléments d’interface utilisateur Xamarin. IOS pour afficher, modifier, sélectionner et créer des contacts sur des appareils iOS.

Pour plus d’informations, consultez la documentation de [l’interface utilisateur contacts et contacts](~/ios/platform/contacts.md) .

## <a name="new-search-apis"></a>Nouvelles API de recherche

La recherche a été étendue dans iOS 9 pour offrir de nouvelles façons d’accéder à des informations à l’intérieur de votre application Xamarin. iOS. À l’aide des nouvelles API de recherche, vous pouvez faire en sorte que le contenu de votre application puisse faire l’objet de recherches par le biais des résultats de recherche Spotlight et Safari, de la remise et des rappels Siri et des suggestions. Cela permet aux utilisateurs d’accéder rapidement aux activités et aux informations contenues dans votre application.

En outre, les nouvelles API de recherche facilitent l’intégration de la recherche dans votre application sans une expérience d’implémentation de recherche antérieure. Pour cette raison, Apple prétend qu’il faut généralement quelques heures pour que le contenu d’une application iOS 9 fasse l’objet d’une recherche universelle à l’aide de la recherche d’applications.

Pour plus d’informations, consultez notre documentation sur les [améliorations apportées](~/ios/platform/search/index.md) à la recherche.

## <a name="new-stack-view"></a>Nouvel affichage de la pile

Le contrôle d’affichage de la pile ([UIStackView](xref:UIKit.UIStackView) tire parti de la puissance des classes de mise en page automatique et de taille pour gérer une pile de sous-vues (horizontale ou verticale) qui répond dynamiquement à l’orientation et à la taille de l’écran de l’appareil iOS.

En utilisant le contrôle d’affichage de la pile, la quantité de travail nécessaire à la mise en page d’une interface utilisateur est très réduite. La disposition de tous les sous-affichages attachés à un affichage de la pile est gérée automatiquement en fonction des propriétés définies par le développeur, telles que l’axe, la distribution, l’alignement et l’espacement.

Pour plus d’informations, consultez notre [Introduction à la documentation de l’affichage de pile](~/ios/user-interface/controls/uistackview.md) .

## <a name="collection-view-changes"></a>Modifications de la vue de collection

Dans iOS 9, la vue de collection ([UICollectionView](xref:UIKit.UICollectionView) prend désormais en charge la réorganisation des éléments par la suite en ajoutant un nouveau module de reconnaissance de mouvement par défaut et plusieurs nouvelles méthodes de prise en charge.

À l’aide de ces nouvelles méthodes, vous pouvez facilement implémenter la fonction glisser-déplacer dans votre vue de collection et avoir la possibilité de personnaliser l’apparence des éléments lors de n’importe quelle étape du processus de réorganisation.

Pour en savoir plus sur les modifications apportées à la vue de collection pour iOS 9, consultez notre guide des modifications de la [vue de collection](~/ios/user-interface/controls/uicollectionview.md) .

## <a name="game-enhancements"></a>Améliorations des jeux

Avec iOS 9, Apple a apporté plusieurs améliorations technologiques aux API de jeu qui facilitent l’implémentation des graphiques de jeu et de l’audio dans votre application Xamarin. iOS. Celles-ci incluent la facilité de développement via des frameworks de haut niveau et l’exploitation de la puissance du GPU de l’appareil iOS pour améliorer la vitesse et les capacités graphiques grâce à des améliorations de bas niveau.

Cela comprend les nuanceurs GameplayKit, ReplayKit, Model I/O, MetalKit et Metal performance, ainsi que les nouvelles fonctionnalités améliorées de metal, SceneKit et SpriteKit.

Pour plus d’informations, consultez notre documentation sur les [améliorations apportées aux jeux](~/ios/platform/gaming/index.md) .

## <a name="homekit-framework-changes"></a>Modifications de l’infrastructure HomeKit

L’infrastructure [HomeKit](xref:HomeKit) , introduite dans iOS 8, offre la possibilité de configurer et de contrôler divers accessoires compatibles HomeKit (tels que les éclairages automatisés, les verrous de porte et les dispositifs d’ouverture de porte de garage) à partir d’une application Xamarin. iOS. En plus de faciliter la configuration et la configuration, les accessoires HomeKit peuvent être contrôlés par le biais de commandes Siri vocales.

Dans iOS 9, Apple est plus facile à configurer, a développé les types d’accessoires pris en charge et fourni plus d’interactions d’accessoires (par exemple, le contrôle d’un accessoire à distance via iCloud).

Pour plus d’informations, consultez notre [Introduction à HomeKit](~/ios/platform/homekit.md), [exemple d’application HomeKitIntro iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/homekit-homekitintro) et la documentation [HomeKit](https://developer.apple.com/homekit/) d’Apple.

## <a name="handoff-framework-changes"></a>Modifications de l’infrastructure de transfert

La remise (également appelée continuité) a été introduite par Apple dans iOS 8 et OS X Yosemite (10,10) pour que l’utilisateur démarre une activité sur l’un de ses appareils (iOS ou Mac) et continue cette même activité sur un autre de ses appareils (identifiés par le compte iCloud de l’utilisateur).

Le transfert a été étendu dans iOS 9 pour prendre également en charge les nouvelles fonctionnalités de recherche améliorées. Pour plus d’informations, consultez notre documentation sur les [améliorations apportées](~/ios/platform/search/index.md) à la recherche. Pour plus d’informations sur l’utilisation de la remise, consultez notre introduction à la documentation relative [au transfert](~/ios/platform/handoff.md) .

## <a name="new-extension-points"></a>Nouveaux points d'extension

Dans iOS 8, Apple a introduit des extensions, c’est-à-dire des bibliothèques présentées par le système d’exploitation dans des contextes standard, par exemple dans le centre de notification, lorsque l’utilisateur demande un clavier ou lorsqu’il modifie une photo.

Avec iOS 9, Apple étend la prise en charge des extensions en fournissant plusieurs nouveaux _points d’extension_ qui définissent des stratégies d’utilisation et fournissent des API pour travailler dans une zone donnée comme suit :

- **Nouveau point d’extension Audio Unit** : utilisez ce point d’extension pour fournir des effets audio, des instruments de musique, des générateurs de sons, etc. à utiliser dans d’autres applications hôtes d’unités audio (telles que GarageBand). Ce point d’extension vous permet également de vendre des _unités audio_ (plug-ins audio) sur l’App Store.
- **Nouveau point d’extension de maintenance d’index** : utilisez ce point d’extension pour prendre en charge la réindexation des données d’application sans nécessiter un redémarrage de l’application.
- **Nouveaux points d’extension réseau** (ceux-ci requièrent une autorisation spéciale d’Apple) :
  - **Extension du fournisseur de proxy d’application** : utilisez ce point d’extension pour implémenter un proxy réseau transparent côté client personnalisé.
  - **Filtrez l’extension du fournisseur de contrôle fournisseur de données/filtre** -utilisez ces points d’extension pour implémenter le filtrage du contenu réseau dynamique sur l’appareil.
  - **Extension de fournisseur de tunnel de paquets** : utilisez ce point d’extension pour implémenter un protocole de tunnel VPN personnalisé côté client.
- **Nouveaux points d’extension Safari**:
  - **Extension de blocage de contenu** : utilisez ce point d’extension pour définir une liste de contenu bloqué qui ne sera pas affichée lorsque l’utilisateur navigue sur le Web.
  - **Extension des liens partagés** : utilisez ce point d’extension pour activer l’affichage du contenu de votre application dans les liens partagés de Safari.

Pour plus d’informations, consultez notre [Introduction aux extensions](~/ios/platform/extensions.md) et la documentation du Guide de programmation de l' [extension d’application](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) d’Apple.

## <a name="keychain-enhancements"></a>Améliorations des trousseau

Dans iOS 9, Apple a amélioré le trousseau pour fournir un nouveau type de clé de chiffrement pour l’enclave sécurisée et d’autres options de protection d’élément comme suit :

- Nouvelle contrainte Touch ID qui invalide les éléments de trousseau lorsque la base de données d’empreintes digitales est modifiée.
- Nouvelles contraintes qui permettent de créer des entrées de liste Access Control avec Touch ID ou code secret uniquement.
- Nouveau contexte d’authentification qui vous permet d’appeler l’authentification séparément des `SecItem` appels.
- Access Control entropie de liste (à l’aide de l’option de mot de passe d’application) pour le chiffrement d’élément de trousseau fourni par l’application.
- Prise en charge de la génération et de l’utilisation de clés à l’intérieur de l’enclave sécurisée (via l' `kSecAttrTokenIDSecureEnclave` attribut).

Pour plus d’informations, consultez [id tactile et ID facial dans Xamarin. iOS](~/ios/platform/touch-id-face-id.md).

## <a name="right-to-left-language-support"></a>Prise en charge des langues de droite à gauche

Dans iOS 9, Apple a rendu une interface utilisateur retournée plus facile que jamais en fournissant une prise en charge complète des langues de droite à gauche. Ce dernier est détaillé ci-après :

- Les contrôles [UIKit](xref:UIKit) standard sont automatiquement retournés de droite à gauche selon les paramètres régionaux et linguistiques des appareils iOS.
- La classe [UIView](xref:UIKit.UIView) fournit des attributs qui vous permettent de définir la manière dont une vue donnée doit apparaître lorsqu’elle est retournée de droite à gauche.
- La possibilité de retourner une image par programmation à l’aide de la propriété [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) de la classe [UIImage](xref:UIKit.UIImage) .

Pour plus d’informations, consultez la documentation sur les [langues de droite à gauche prises en charge](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) par Apple.

## <a name="additional-framework-changes"></a>Modifications supplémentaires de l’infrastructure

Outre les principales modifications que nous avons abordées ci-dessus, Apple a apporté des modifications et des améliorations à plusieurs infrastructures existantes pour iOS 9, y compris les suivantes :

- Infrastructure de Fondation AV
- Framework AVKit
- Framework CloudKit
- Infrastructure de base
- Infrastructure de transfert
- Framework HealthKit
- Framework HomeKit
- Infrastructure d’authentification locale
- Framework MapKit
- Framework PassKit
- Infrastructure des services Safari
- Framework UIKit

Pour plus d’informations, consultez notre documentation [supplémentaire sur les modifications apportées à IOS 9 Framework](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) .

## <a name="deprecated-apis-and-functions"></a>API et fonctions déconseillées

Apple a déconseillé les API et fonctions suivantes dans iOS 9 :

- **Carnet d’adresses & interface utilisateur du carnet d’adresses** -ces API ont été remplacées par les infrastructures d’interface utilisateur de contact et de contact. Pour plus d’informations, consultez la documentation de [l’interface utilisateur contacts et contacts](~/ios/platform/contacts.md) .
- **CBCentralManager** -les `RetrievePeripherals` `RetrieveConnectedPeripherals` méthodes et de la `CBCentralManager` classe ont été supprimées dans iOS 9. L’appel de ces méthodes entraîne le blocage d’une application lors de l’appariement d’un accessoire ou du lancement de l’application.
- **FetchAllChanges** : la `FetchAllChanges` de la `CKFetchRecordChangesOperation` classe a été amortie et sera supprimée dans iOS 9.
- **Lecteur multimédia** : l’infrastructure de lecteur multimédia est dépréciée dans iOS 9. Utilisez des API AVKit ou AV Foundation à la place.

Pour obtenir la liste complète des désapprobations d’API spécifiques, consultez la documentation relative aux [différences d’API iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) d’Apple.

## <a name="ios-9-sample-apps"></a>Exemples d’applications iOS 9

Nous disposons de quelques [exemples spécifiques à IOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) pour commencer :

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-musicmotion)
- [Photoprogression](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-seguecatalog)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Consultez également les parties iOS de ces exemples (compléments Mac OS X versions à venir) :

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)

## <a name="related-links"></a>Liens connexes

- [Exemples iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Présentation de l’interface tactile 3D](~/ios/platform/3d-touch.md)
- [Sécurité de transport de l’application](~/ios/app-fundamentals/ats.md)
- [Multitâche pour iPad](~/ios/platform/multitasking.md)
- [Interface utilisateur contacts et contacts](~/ios/platform/contacts.md)
- [Nouvelles API de recherche](~/ios/platform/search/index.md)
- [Présentation de l’affichage des piles](~/ios/user-interface/controls/uistackview.md)
- [Modifications de la vue de collection](~/ios/user-interface/controls/uicollectionview.md)
- [Améliorations des jeux](~/ios/platform/gaming/index.md)
- [Présentation de HomeKit](~/ios/platform/homekit.md)
- [Présentation du transfert](~/ios/platform/handoff.md)
- [Autres changements apportés au framework iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Dépannage](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 pour les développeurs](https://developer.apple.com/ios/pre-release/)
- [Nouveautés d’iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Mise à jour de vos applications Xamarin. iOS vers iOS9 (vidéo)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
