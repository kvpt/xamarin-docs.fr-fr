---
title: Mode sombre dans Xamarin. iOS
description: Le mode Dark est une nouvelle option à l’ensemble du système pour les thèmes clairs et foncés. l’utilisateur iOS peut maintenant choisir un thème ou autoriser iOS à changer l’apparence de manière dynamique.
ms.prod: xamarin
ms.assetid: 4F44446E-36B6-4743-9B4D-32278D1D3D66
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/28/2019
ms.openlocfilehash: be487ab839e2fb4d21b85719a56dc34303317a5f
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206389"
---
# <a name="dark-mode-in-xamarinios"></a>Mode sombre dans Xamarin. iOS

Le mode Dark est une option à l’ensemble du système pour les thèmes clairs et foncés. les utilisateurs d’iOS peuvent désormais choisir le thème ou autoriser iOS à modifier l’apparence de manière dynamique en fonction de l’environnement et de l’heure de la journée.

Ce document présente le mode sombre et prend en charge le mode sombre dans les applications iOS 13.

## <a name="requirements"></a>Configuration requise

Le mode Dark requiert iOS 13 et Xcode 11, Xamarin. iOS 12,99 et Visual Studio 2019 ou Visual Studio 2019 pour Mac avec la prise en charge de Xcode 11.

## <a name="turning-on-dark-mode"></a>Activation du mode sombre

Apple fournit un menu Développeur dans iOS 13 pour basculer entre les modes sombre et clair. Dans le simulateur iOS 13, ouvrez **paramètres** et choisissez la section **développeur** , puis faites défiler jusqu’au commutateur d' **apparence sombre** . La modification sera reflétée dans l’ensemble de l’environnement du simulateur :

![Activation du mode sombre](dark-mode-images/LightAndDark_DeveloperSetting.png)

## <a name="assets-for-light-and-dark-modes"></a>Ressources pour les modes Light et Dark

Le catalogue de ressources dans Visual Studio prend désormais en charge les images et les couleurs facultatives pour chaque mode d’apparence : Universal, Dark et Light. Quand vous définissez vos images et couleurs de cette manière, iOS choisit automatiquement l’image et la couleur appropriées.

Ouvrez votre fichier **Assets. xcassets** dans votre projet iOS et ajoutez un nouvel ensemble d’images. Notez que vous pouvez spécifier des images universelles, sombres et légères à n’importe quelle résolution cible. Dans la capture d’écran ci-dessous, il existe une image pour Dark et pour Light avec le nom « MicrosoftLogo » :

![Ressources pour les modes Light et Dark](dark-mode-images/LightAndDark_AssetCatalog2.png)

**Assets. xcassets** contient également des entrées pour **backgroundColor** et **TitleColor**, qui sont des définitions de couleurs. Ces couleurs sont désormais disponibles par nom à utiliser dans toute l’application. **BackgroundColor** a été assigné à l’arrière-plan de la vue, et **TitleColor** à l’étiquette, comme illustré dans cette capture d’écran :

![Ressources pour les modes Light et Dark](dark-mode-images/LightAndDark_01.png)

## <a name="dynamic-system-colors"></a>Couleurs système dynamiques

Apple a introduit de nouvelles couleurs sémantiques qui ajustent leur apparence de manière dynamique en fonction du nouveau paramètre en mode sombre.

## <a name="summary"></a>Récapitulatif

Cet article a introduit le mode sombre pour iOS et spécifie des images et des couleurs pour chaque mode à l’aide du catalogue de ressources.

## <a name="related-links"></a>Liens connexes

- [Recommandations en matière de conception en mode sombre](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/dark-mode/)
- [Couleurs sémantiques](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/#dynamic-system-colors)
