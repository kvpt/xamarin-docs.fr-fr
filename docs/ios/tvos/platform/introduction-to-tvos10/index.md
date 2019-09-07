---
title: Introduction à tvOS 10
description: Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans tvOS 10 pour les développeurs Xamarin. tvOS.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: 8c338f8a5b2f1d41b1ea0f61778a1c14eb84ce08
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70769153"
---
# <a name="introduction-to-tvos-10"></a>Introduction à tvOS 10

_Cet article présente toutes les API et fonctionnalités nouvelles et modifiées disponibles dans tvOS 10 pour les développeurs Xamarin. tvOS._

Avec le nouveau kit de développement logiciel (SDK) tvOS 10, Apple a inclus de nouvelles API et de nouveaux services qui permettent au développeur de créer de nouvelles catégories d’applications et de fonctionnalités. 

Pour plus d’informations sur tvOS 10, consultez la documentation d’Apple [tvOS + Apps](https://developer.apple.com/tvos/) .

## <a name="whats-new-in-tvos-10"></a>Nouveautés de tvOS 10

Apple a ajouté plusieurs nouvelles API et services dans tvOS 10, ainsi que de nombreuses améliorations aux fonctionnalités existantes, notamment :

## <a name="new-user-interface-styles"></a>Nouveaux styles d’interface utilisateur

tvOS 10 prend désormais en charge à la fois un thème d’interface utilisateur sombre et clair, sur lequel tous les contrôles de UIKit de build s’adapteront automatiquement, en fonction des préférences de l’utilisateur.

Lors de la création et de l’implémentation de nouveaux contrôles d’interface utilisateur personnalisés, le développeur doit utiliser la classe [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) pour s’adapter au thème sélectionné de l’utilisateur.

Pour plus d’informations, consultez notre nouvelle documentation sur les styles de l' [interface utilisateur](~/ios/tvos/platform/user-interface-styles.md) .

## <a name="security-and-privacy-enhancements"></a>Améliorations en matière de sécurité et de confidentialité

Apple a apporté plusieurs améliorations à la sécurité et à la confidentialité dans tvOS 10, ce qui permet au développeur d’améliorer la sécurité de ses applications et de garantir la confidentialité de l’utilisateur final.

Par conséquent, les applications qui s’exécutent sur Watchos 3 (ou version ultérieure) doivent déclarer de manière statique leur intention d’accéder à des fonctionnalités ou des informations utilisateur spécifiques en entrant `Info.plist` une ou plusieurs clés spécifiques à la confidentialité dans leurs fichiers qui expliquent à l’utilisateur pourquoi l’application souhaite obtenir un accès.

Étant donné que tvOS 10 partage ces modifications avec iOS 10, consultez notre guide d’amélioration de la [sécurité et](~/ios/app-fundamentals/security-privacy.md) de la confidentialité d’iOS 10 pour plus d’informations.

## <a name="video-subscriber-account"></a>Compte d’abonné vidéo

Nouveauté de tvOS 10, l’infrastructure de compte d’abonné vidéo permet aux applications qui prennent en charge la diffusion en continu authentifiée ou la vidéo à la demande de s’authentifier auprès de leur câble ou de votre fournisseur de télévision par satellite à l’aide d’une expérience d’authentification unique pour l’utilisateur final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Couleur à spectre large

tvOS 10 étend la prise en charge des formats de pixel étendus et des espaces de couleurs à grande échelle dans le système, y compris les infrastructures telles que Core Graphics, Core image, Metal et AVFoundation. La prise en charge des appareils avec des affichages de couleurs larges est encore plus facilitée en fournissant ce comportement dans l’ensemble de la pile graphique.

En outre, `UIKit` a été modifié pour fonctionner dans le nouveau colorspace **sRVB** étendu, ce qui facilite le mixage des couleurs dans des gammes de couleurs larges sans perte de performances significative.

Apple offre les meilleures pratiques suivantes lorsque vous travaillez avec des couleurs larges :

- `UIColor`utilise à présent l’espace de couleurs sRVB et ne pincera plus les `0.0` valeurs `1.0` à la plage à. Si l’application s’appuie sur le comportement de verrouillage précédent, elle doit être modifiée pour tvOS 10.
- Si l’application effectue un rendu personnalisé `UIImages`de, utilisez la nouvelle classe [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) pour spécifier l’utilisation des formats de plage étendue ou standard.
- Lorsque vous utilisez une API de bas niveau, telle que des graphiques de base ou du métal, pour fournir un traitement d’image, l’application doit utiliser un espace de couleurs de plage étendue et un format de pixel qui prend en charge les valeurs à virgule flottante 16 bits. Le cas échéant, l’application devra fixer manuellement les valeurs des composants de couleur.
- Core Graphics, Core image et Metal performance Nuancers fournissent de nouvelles méthodes pour la conversion entre les deux espaces de couleurs.

Pour en savoir plus, consultez notre guide [de présentation des couleurs larges](~/ios/platform/wide-color.md) .

## <a name="newly-available-existing-frameworks"></a>Nouvelles infrastructures existantes disponibles

Plusieurs infrastructures disponibles sur iOS (et non tvOS) ont été mises à disposition pour tvOS 10, par exemple :

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Photos
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>Modifications supplémentaires de l’infrastructure

Outre les principales modifications apportées à l’infrastructure et les ajouts ci-dessus, Apple a apporté de nombreuses modifications d’infrastructure mineures supplémentaires dans tvOS 10.

Pour en savoir plus, consultez notre guide [supplémentaire sur les modifications](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) de l’infrastructure.

## <a name="deprecated-apis"></a>API déconseillées

Aucune API ou infrastructure n’a été déconseillée par tvOS 10. Pour obtenir la liste complète des modifications d’API, consultez la documentation sur les différences de l’API d’Apple [tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) .

## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Nouveautés de tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
