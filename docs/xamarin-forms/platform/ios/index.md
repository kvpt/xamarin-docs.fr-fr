---
title: fonctionnalités de la plateforme iOS dans Xamarin. Forms
description: Ajout de fonctionnalités spécifiques à iOS à des applications Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 5d0e289ddeb7eabef6d96c8882c772c704c54b34
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489724"
---
# <a name="ios-platform-features-in-xamarinforms"></a>fonctionnalités de la plateforme iOS dans Xamarin. Forms

Pour développer des applications Xamarin. Forms pour iOS, vous devez disposer de Visual Studio. La [page spécifications](~/get-started/requirements.md) contient des informations supplémentaires sur les conditions préalables.

## <a name="platform-specifics"></a>Spécificités de la plateforme

Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets.

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues, les pages et les mises en page Xamarin. Forms sur iOS :

- Flou prend en charge aucun [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement Blur on iOS](visualelement-blur.md).
- Désactiver le mode hérité de couleur sur la prise en charge [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement Legacy Color mode sur iOS](legacy-color-mode.md).
- L’activation d’une ombre sur un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement Drop Shadows on iOS](visualelement-drop-shadow.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues Xamarin. Forms sur iOS :

- Définition de la couleur d’arrière-plan [`Cell`](xref:Xamarin.Forms.Cell) . Pour plus d’informations, consultez [couleur d’arrière-plan de la cellule sur iOS](cell-background-color.md).
- S’assurer qu’entrée texte s’adapte à un [ `Entry` ](xref:Xamarin.Forms.Entry) en ajustant la taille de police. Pour plus d’informations, consultez [taille de police d’entrée sur iOS](entry-font-size.md).
- Définir la couleur du curseur dans un [ `Entry` ](xref:Xamarin.Forms.Entry). Pour plus d’informations, consultez [couleur du curseur d’entrée sur iOS](entry-cursor-color.md).
- Contrôle de la [`ListView`](xref:Xamarin.Forms.ListView) des cellules d’en-tête flottantes lors du défilement. Pour plus d’informations, consultez [style d’en-tête de groupe ListView sur iOS](listview-group-header-style.md).
- Contrôle de la désactivation des animations de lignes lors de la mise à jour de la collection d’éléments [`ListView`](xref:Xamarin.Forms.ListView) . Pour plus d’informations, consultez [animations de lignes ListView sur iOS](listview-row-animations.md).
- Définir le style du séparateur sur un [ `ListView` ](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [style de séparateur ListView sur iOS](listview-separator-style.md).
- Contrôle si la sélection d’éléments se produit dans un [ `Picker` ](xref:Xamarin.Forms.Picker). Pour plus d’informations, consultez [sélection d’un élément de sélecteur sur iOS](picker-selection.md).
- L’activation de la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriété à définir en appuyant sur une position sur le [ `Slider` ](xref:Xamarin.Forms.Slider) barre, plutôt que d’avoir à faire glisser le `Slider` thumb. Pour plus d’informations, consultez [curseur sur iOS](slider-thumb.md).
- Contrôle de la transition utilisée lors de l’ouverture d’un `SwipeView`. Pour plus d’informations, consultez [mode de transition par balayage SwipeView](swipeview-swipetransitionmode.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les pages Xamarin. Forms sur iOS :

- Masquer le séparateur de barre de navigation sur un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Pour plus d’informations, consultez [séparateur de barre NavigationPage sur iOS](navigation-bar-separator.md).
- Contrôle si la barre de navigation est translucide. Pour plus d’informations, consultez [barre de navigation translucidité sur iOS](navigation-bar-translucent.md).
- Contrôle si le texte de barre d’état de couleur sur un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) est ajustée pour correspondre à la luminosité de la barre de navigation. Pour plus d’informations, consultez [mode de couleur du texte de la barre NavigationPage sur iOS](status-bar-text-color.md).
- Contrôle si le titre de page est affiché comme un titre de grande taille dans la barre de navigation de page. Pour plus d’informations, consultez [titres de page de grande taille sur iOS](page-large-title.md).
- Définition de la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page). Pour plus d’informations, consultez [visibilité de l’indicateur de démarrage sur iOS](page-home-indicator.md).
- Définition de la visibilité de barre d’état sur un [ `Page` ](xref:Xamarin.Forms.Page). Pour plus d’informations, consultez [visibilité de la barre d’état de la page sur iOS](page-status-bar-visibility.md).
- S’assurer de ce contenu de la page est positionné sur une zone de l’écran qui est sécurisé pour tous les appareils iOS. Pour plus d’informations, consultez [le Guide de disposition en zone sécurisée sur iOS](page-safe-area-layout.md).
- Définition du style de présentation des pages modales. Pour plus d’informations, consultez [style de présentation de page modale](page-presentation-style.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les dispositions Xamarin. Forms sur iOS :

- Contrôle si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gère un mouvement tactile ou la transmet à son contenu. Pour plus d’informations, consultez [ScrollView content touche on iOS](scrollview-content-touches.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la classe Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application) sur iOS :

- Désactivation de la mise à l’échelle de l’accessibilité pour les tailles de police nommées. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](named-font-size-scaling.md).
- L’activation de la disposition des contrôles et le rendu des mises à jour doivent être effectuées sur le thread principal. Pour plus d’informations, consultez [mises à jour du contrôle de thread principal sur iOS](main-thread-updates-ui.md).
- L’activation une [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) dans une vue de défilement pour capturer et partager le mouvement panoramique avec la vue de défilement. Pour plus d’informations, consultez [reconnaissance simultanée des mouvements de panoramiques sur iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>mise en forme spécifique à iOS

Xamarin. Forms permet de définir des couleurs et des styles d’interface utilisateur multiplateforme, mais il existe d’autres options pour définir le thème de votre iOS à l’aide d’API de plateforme dans le projet iOS.

[En savoir plus](formatting.md) sur la mise en forme de l’interface utilisateur à l’aide d’API spécifiques à iOS, telles que la configuration d' **info. plist** et l’API `UIAppearance`.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Autres fonctionnalités iOS

À l’aide de [convertisseurs personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), de [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)et de [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), il est possible d’incorporer un large éventail de fonctionnalités natives dans les applications Xamarin. Forms pour iOS.
