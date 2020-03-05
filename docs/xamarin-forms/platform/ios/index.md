---
title: fonctionnalités de la plateforme iOS dans Xamarin. Forms
description: Ajout de fonctionnalités spécifiques à iOS à des applications Xamarin. Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/15/2020
ms.openlocfilehash: 015db40ce983d979109d4cce32c011f8c61a729c
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292819"
---
# <a name="ios-platform-features-in-xamarinforms"></a>fonctionnalités de la plateforme iOS dans Xamarin. Forms

Pour développer des applications Xamarin. Forms pour iOS, vous devez disposer de Visual Studio. La [page plateformes prises en charge](~/get-started/supported-platforms.md) contient des informations supplémentaires sur les conditions préalables.

## <a name="platform-specifics"></a>Spécificités des plateformes

Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets.

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues, les pages et les mises en page Xamarin. Forms sur iOS :

- La prise en charge de l’ensemble des [`VisualElement`](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement Blur on iOS](visualelement-blur.md).
- Désactivation du mode de couleurs hérité sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement)pris en charge. Pour plus d’informations, consultez [VisualElement Legacy Color mode sur iOS](legacy-color-mode.md).
- Activation d’une ombre portée sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement Drop Shadows on iOS](visualelement-drop-shadow.md).
- Activation d’un objet [`VisualElement`](xref:Xamarin.Forms.VisualElement) pour devenir le premier répondeur aux événements Touch. Pour plus d’informations, consultez [premier répondeur VisualElement](visualelement-first-responder.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues Xamarin. Forms sur iOS :

- Définition de la couleur d’arrière-plan [`Cell`](xref:Xamarin.Forms.Cell) . Pour plus d’informations, consultez [couleur d’arrière-plan de la cellule sur iOS](cell-background-color.md).
- Contrôle du moment où la sélection de l’élément se produit dans un [`DatePicker`](xref:Xamarin.Forms.DatePicker). Pour plus d’informations, consultez [sélection de l’élément DatePicker sur iOS](datepicker-selection.md).
- S’assurer que le texte entré s’ajuste à un [`Entry`](xref:Xamarin.Forms.Entry) en ajustant la taille de la police. Pour plus d’informations, consultez [taille de police d’entrée sur iOS](entry-font-size.md).
- Définition de la couleur du curseur dans un [`Entry`](xref:Xamarin.Forms.Entry). Pour plus d’informations, consultez [couleur du curseur d’entrée sur iOS](entry-cursor-color.md).
- Contrôle de la [`ListView`](xref:Xamarin.Forms.ListView) des cellules d’en-tête flottantes lors du défilement. Pour plus d’informations, consultez [style d’en-tête de groupe ListView sur iOS](listview-group-header-style.md).
- Contrôle de la désactivation des animations de lignes lors de la mise à jour de la collection d’éléments [`ListView`](xref:Xamarin.Forms.ListView) . Pour plus d’informations, consultez [animations de lignes ListView sur iOS](listview-row-animations.md).
- Définition du style de séparateur sur un [`ListView`](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [style de séparateur ListView sur iOS](listview-separator-style.md).
- Contrôle du moment où la sélection de l’élément se produit dans un [`Picker`](xref:Xamarin.Forms.Picker). Pour plus d’informations, consultez [sélection d’un élément de sélecteur sur iOS](picker-selection.md).
- L’activation de la propriété [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) doit être définie en appuyant sur une position sur la barre de [`Slider`](xref:Xamarin.Forms.Slider) , plutôt qu’en faisant glisser le curseur de `Slider`. Pour plus d’informations, consultez [curseur sur iOS](slider-thumb.md).
- Contrôle de la transition utilisée lors de l’ouverture d’un `SwipeView`. Pour plus d’informations, consultez [mode de transition par balayage SwipeView](swipeview-swipetransitionmode.md).
- Contrôle du moment où la sélection de l’élément se produit dans un [`TimePicker`](xref:Xamarin.Forms.TimePicker). Pour plus d’informations, consultez [sélection d’élément timepicker sur iOS](timepicker-selection.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les pages Xamarin. Forms sur iOS :

- Masquage du séparateur de barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Pour plus d’informations, consultez [séparateur de barre NavigationPage sur iOS](navigation-bar-separator.md).
- Contrôle si la barre de navigation est translucide. Pour plus d’informations, consultez [barre de navigation translucidité sur iOS](navigation-bar-translucent.md).
- Contrôler si la couleur de texte de la barre d’État sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est ajustée pour correspondre à la luminosité de la barre de navigation. Pour plus d’informations, consultez [mode de couleur du texte de la barre NavigationPage sur iOS](status-bar-text-color.md).
- Contrôle si le titre de page est affiché comme un titre de grande taille dans la barre de navigation de page. Pour plus d’informations, consultez [titres de page de grande taille sur iOS](page-large-title.md).
- Définition de la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page). Pour plus d’informations, consultez [visibilité de l’indicateur de démarrage sur iOS](page-home-indicator.md).
- Définition de la visibilité de la barre d’État sur un [`Page`](xref:Xamarin.Forms.Page). Pour plus d’informations, consultez [visibilité de la barre d’état de la page sur iOS](page-status-bar-visibility.md).
- S’assurer de ce contenu de la page est positionné sur une zone de l’écran qui est sécurisé pour tous les appareils iOS. Pour plus d’informations, consultez [le Guide de disposition en zone sécurisée sur iOS](page-safe-area-layout.md).
- Définition du style de présentation des pages modales. Pour plus d’informations, consultez [style de présentation de page modale](page-presentation-style.md).
- Définition du mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, consultez [TabbedPage translucide TabBar sur iOS](tabbedpage-translucent-tabbar.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les dispositions Xamarin. Forms sur iOS :

- Contrôler si un [`ScrollView`](xref:Xamarin.Forms.ScrollView) gère un mouvement tactile ou le passe à son contenu. Pour plus d’informations, consultez [ScrollView content touche on iOS](scrollview-content-touches.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la classe Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application) sur iOS :

- Désactivation de la mise à l’échelle de l’accessibilité pour les tailles de police nommées. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](named-font-size-scaling.md).
- L’activation de la disposition des contrôles et le rendu des mises à jour doivent être effectuées sur le thread principal. Pour plus d’informations, consultez [mises à jour du contrôle de thread principal sur iOS](main-thread-updates-ui.md).
- Activation d’un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) dans une vue de défilement pour capturer et partager le mouvement panoramique avec la vue de défilement. Pour plus d’informations, consultez [reconnaissance simultanée des mouvements de panoramiques sur iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>mise en forme spécifique à iOS

Xamarin. Forms permet de définir des couleurs et des styles d’interface utilisateur multiplateforme, mais il existe d’autres options pour définir le thème de votre iOS à l’aide d’API de plateforme dans le projet iOS.

[En savoir plus](formatting.md) sur la mise en forme de l’interface utilisateur à l’aide d’API spécifiques à iOS, telles que la configuration d' **info. plist** et l’API `UIAppearance`.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Autres fonctionnalités iOS

À l’aide de [convertisseurs personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), de [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)et de [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), il est possible d’incorporer un large éventail de fonctionnalités natives dans les applications Xamarin. Forms pour iOS.
