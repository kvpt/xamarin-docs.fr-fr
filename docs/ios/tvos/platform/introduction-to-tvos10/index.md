---
title: Introduction à tvOS 10
description: Cet article présente tous les nouveaux et modifiés API et fonctionnalités disponibles dans tvOS 10 pour les développeurs Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: CB9C1EC8-6008-43AD-977E-976AE7C73DD8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 6bad05f62fa9168818aa9a2e73cc1e2830af03b3
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830037"
---
# <a name="introduction-to-tvos-10"></a>Introduction à tvOS 10

_Cet article présente tous les nouveaux et modifiés API et fonctionnalités disponibles dans tvOS 10 pour les développeurs Xamarin.tvOS._

Avec la nouvelle tvOS 10 SDK Apple a inclus les nouvelles API et services qui permettent au développeur de créer des catégories d’applications et de fonctionnalités. 

Pour plus d’informations sur tvOS 10, consultez d’Apple [tvOS + applications](https://developer.apple.com/tvos/) documentation.

## <a name="whats-new-in-tvos-10"></a>Quelles sont les nouveautés dans tvOS 10

Apple a ajouté plusieurs nouvelles API et services dans tvOS 10, ainsi que de nombreuses améliorations aux fonctionnalités existantes, y compris :

## <a name="new-user-interface-styles"></a>Nouveaux Styles d’Interface utilisateur

tvOS 10 maintenant prend en charge un sombre et l’Interface utilisateur de lumière thème que tous le UIKit dans build contrôle va automatiquement s’adapter aux, en fonction des préférences de l’utilisateur.

Lors de la création et implémentation des nouveaux contrôles d’interface utilisateur personnalisées, le développeur doit utiliser le [UITraitCollection](https://developer.apple.com/reference/uikit/uitraitcollection) classe pour s’adapter à thème sélectionné de l’utilisateur.

Pour plus d’informations, consultez notre [nouveaux Styles d’Interface utilisateur](~/ios/tvos/platform/user-interface-styles.md) documentation.

## <a name="security-and-privacy-enhancements"></a>Améliorations de la confidentialité et de sécurité

Apple a apporté plusieurs améliorations à la sécurité et confidentialité dans tvOS 10 qui aideront les développeurs à améliorer la sécurité de leurs applications et garantir la confidentialité de l’utilisateur final.

Par conséquent, les applications qui s’exécutent sur watchOS 3 (ou version ultérieure) doivent déclarer statiquement leur intention d’accéder à des fonctionnalités spécifiques ou des informations utilisateur en entrant une ou plusieurs clés spécifiques confidentialité dans leurs `Info.plist` fichiers expliquent à l’utilisateur pourquoi l’application souhaite accéder.

Étant donné que tvOS 10 partage ces modifications avec iOS 10, consultez notre iOS 10 [sécurité et confidentialité améliorations](~/ios/app-fundamentals/security-privacy.md) guide pour plus d’informations.

## <a name="video-subscriber-account"></a>Compte abonné vidéo

Nouveau pour tvOS 10, le framework compte abonné à la vidéo permet aux applications cette prise en charge authentifié de diffusion en continu ou la vidéo à la demande auprès de leur fournisseur de télévision par câble ou satellite à l’aide d’une seul-expérience de connexion pour l’utilisateur final.

<!--To find out more, please see our [Video Subscriber Account](~/ios/platform-features/introduction-to-ios10/video-subscriber-account/) guide.-->

## <a name="wide-color"></a>Couleur à spectre large

tvOS 10 étend la prise en charge pour les formats de pixel de la plage étendue et des espaces d’une gamme de couleurs dans tout le système, y compris les infrastructures telles que graphismes de base, l’Image de Core, complète et AVFoundation. Prise en charge pour les appareils avec des couleurs large est davantage facilité en fournissant ce comportement dans toute la pile de graphique entière.

En outre, `UIKit` a été modifié pour fonctionner dans la nouvelle étendue **sRVB** espace colorimétrique, facilitant ainsi de mélanger les couleurs utilisées dans les gammes de couleurs large sans perte de performances significatifs.

Apple offre les meilleures pratiques suivantes lorsque vous travaillez avec des couleurs larges :

- `UIColor` maintenant utilise le sRVB espace de couleurs et ne seront plus fixer des valeurs pour le `0.0` à `1.0` plage. Si l’application s’appuie sur le comportement de pince précédent, il devront être modifiées pour tvOS 10.
- Si l’application effectue le rendu personnalisé de `UIImages`, utilisez la nouvelle [UIGraphicsImageRender](https://developer.apple.com/reference/uikit/uigraphicsimagerenderer) classe pour spécifier l’utilisation des formats étendus standard-gamme ou.
- Lorsque vous utilisez une API de bas niveau tels que les graphismes de base ou complète pour fournir le traitement d’image, l’application doit utiliser un format de couleur plage étendue espace et de pixels qui prend en charge les valeurs à virgule flottante 16 bits. Le cas échéant, l’application aura à fixer manuellement les valeurs de composant de couleur.
- Graphismes de base, Image Core et des nuanceurs de performances de métal fournissent tous des nouvelles méthodes pour la conversion entre les espaces de deux couleurs.

Pour en savoir plus, consultez notre [Introduction à large couleur](~/ios/platform/wide-color.md) guide.

## <a name="newly-available-existing-frameworks"></a>Infrastructures existantes qui vient d’être disponibles

Plusieurs infrastructures qui étaient disponibles sur iOS (et pas tvOS), mis à la disposition de tvOS 10 tels que :

- ExternalAccessory
- HomeKit
- MultipeerConnectivity
- Photos
- ReplayKit
- UserNotification

## <a name="additional-framework-changes"></a>Modifications d’infrastructure supplémentaires

Outre les modifications de l’infrastructure principale et les ajouts répertoriés ci-dessus, Apple a effectué de nombreuses modifications de framework mineures supplémentaires dans tvOS 10.

Pour en savoir plus, consultez notre [des modifications supplémentaires Framework](~/ios/tvos/platform/introduction-to-tvos10/additional-framework-changes.md) guide.

## <a name="deprecated-apis"></a>API déconseillées

Aucune API ou des infrastructures ont été déconseillées par tvOS 10. Consultez d’Apple [tvOS 10 différences d’API](https://developer.apple.com/library/prerelease/content/releasenotes/General/tvOS10APIDiffs/index.html) documentation pour obtenir la liste complète des modifications de l’API.



## <a name="related-links"></a>Liens associés

- [Exemples tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Quelles sont les nouveautés dans tvOS 10](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewinTVOS/Articles/tvOS10.html#//apple_ref/doc/uid/TP40017259-SW1)
