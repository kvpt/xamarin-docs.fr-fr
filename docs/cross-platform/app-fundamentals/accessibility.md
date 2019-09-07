---
title: Accessibilité dans les applications Xamarin
description: Ce document fournit différents conseils pour la création d’applications accessibles. Par exemple, il comprend des recommandations sur les grandes polices, le contraste élevé, les interfaces auto-descriptives, et bien plus encore.
ms.prod: xamarin
ms.assetid: E587F0CF-7C1D-41F8-B5A8-DA3E738EDA81
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 55d531036336cdd6c3ac7efa1c5ba21b09a7be9e
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70758134"
---
# <a name="accessibility-in-xamarin-apps"></a>Accessibilité dans les applications Xamarin

_S’assurer que vos applications sont utilisables par le plus grand public possible_

L’accessibilité fait référence au concept de conception d’interfaces utilisateur d’application qui fonctionnent avec des fonctionnalités de système d’exploitation et d’assistance en entrée très performantes, telles que le type de grande taille, le contraste élevé, le zoom avant, la lecture d’écran (synthèse vocale), les signaux de commentaires visuels ou haptique, et autres méthodes d’entrée.

Les plateformes de bureau et mobiles comme iOS, Android et Windows fournissent des API intégrées qui aident les développeurs à créer des applications accessibles, telles que [Google Talkback](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) et [Apple VoiceOver](http://www.apple.com/accessibility/ios/voiceover/).

## <a name="platform-specific-apis"></a>API spécifiques à la plateforme

Pour mettre en œuvre les instructions de ce document, utilisez les API fournies par chaque plateforme :

- [**Accessibilité Android**](~/android/app-fundamentals/accessibility.md)
- [**Accessibilité iOS**](~/ios/app-fundamentals/accessibility.md)
- [**Accessibilité OS X**](~/mac/app-fundamentals/accessibility.md)
- [**Xamarin.Forms**](~/xamarin-forms/app-fundamentals/accessibility/index.md)

<a name="checklist" />

## <a name="accessibility-checklist"></a>Liste de vérification de l’accessibilité

Suivez ces conseils pour vous assurer que vos applications sont accessibles à l’audience la plus étendue possible. Consultez la [liste de vérification des tests d’accessibilité Android](https://developer.android.com/training/accessibility/testing.html) et la [page d’accessibilité d’Apple](http://www.apple.com/accessibility/) pour plus d’informations.

### <a name="support-large-fonts-and-high-contrast"></a>Prendre en charge les grandes polices et le contraste élevé

Évitez le codage en dur des dimensions et, à la place, préférez des dispositions qui peuvent être redimensionnées pour prendre en charge des tailles de police supérieures.
Testez les jeux de couleurs en mode de contraste élevé pour vous assurer qu’ils sont lisibles.

### <a name="make-the-user-interface-self-describing"></a>Rendre l’interface utilisateur auto-descriptive

Baliser tous les éléments de votre interface utilisateur avec du texte descriptif et des indicateurs compatibles avec les API de lecture d’écran sur chaque plateforme.

### <a name="ensure-that-images-and-icons-have-an-alternate-text-description"></a>S’assurer que les images et les icônes ont une description textuelle de remplacement

Les images et les icônes qui font partie de l’interface utilisateur de l’application (telles que les boutons ou indicateurs d’État, par exemple) doivent être marquées avec une description accessible.

### <a name="design-the-visual-tree-with-accessible-navigation-in-mind"></a>Concevoir l’arborescence d’éléments visuels avec navigation accessible à l’esprit

Utilisez des contrôles de disposition ou des API appropriés afin que la navigation entre les contrôles utilisant d’autres méthodes d’entrée suit le même Flow logique que l’écran tactile.

Excluez les éléments inutiles des lecteurs d’écran (images décoratives ou étiquettes pour les champs qui sont déjà accessibles, par exemple).

### <a name="dont-rely-on-audio-or-color-cues-alone"></a>Ne vous fiez pas uniquement aux signaux audio ou de couleur

Évitez les situations où la seule indication de la progression, de l’achèvement ou d’un autre État est un bruit ou un changement de couleur. Vous pouvez soit concevoir l’interface utilisateur de façon à inclure des signaux visuels clairs (avec le son et la couleur pour le renforcement uniquement), soit ajouter des indicateurs d’accessibilité spécifiques.

Quand vous choisissez des couleurs, évitez d’utiliser une palette difficile à distinguer pour les utilisateurs avec le daltonisme.

### <a name="captioning-for-video-text-for-audio"></a>Sous-titrage pour la vidéo, texte pour l’audio

Fournissez des sous-titres pour le contenu vidéo et un script lisible pour le contenu audio. Il est également utile de fournir des contrôles qui ajustent la vitesse du contenu audio ou vidéo, et de s’assurer que les boutons de volume et de lecture/pause sont faciles à trouver et à utiliser.

### <a name="localize"></a>Localize

Les descriptions d’accessibilité peuvent (et doivent) être localisées là où l’application prend en charge plusieurs langues.

## <a name="related-links"></a>Liens associés

- [Accessibilité Android](~/android/app-fundamentals/accessibility.md)
- [Accessibilité iOS](~/ios/app-fundamentals/accessibility.md)
- [Accessibilité OS X](~/mac/app-fundamentals/accessibility.md)
- [Accessibilité Xamarin. Forms](~/xamarin-forms/app-fundamentals/accessibility/index.md)
