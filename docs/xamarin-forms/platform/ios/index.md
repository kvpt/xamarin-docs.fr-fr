---
title: fonctionnalités de la plateforme iOS dansXamarin.Forms
description: Ajout de fonctionnalités spécifiques à iOS aux Xamarin.Forms applications.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1008eab6e56be7a235498e01ffd3ea1b27d2bbae
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84130167"
---
# <a name="ios-platform-features-in-xamarinforms"></a>fonctionnalités de la plateforme iOS dansXamarin.Forms

Le développement Xamarin.Forms d’applications pour iOS nécessite Visual Studio. La [page plateformes prises en charge](~/get-started/supported-platforms.md) contient des informations supplémentaires sur les conditions préalables.

## <a name="platform-specifics"></a>Spécificités des plateformes

Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés.

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les Xamarin.Forms affichages, les pages et les mises en page sur iOS :

- Une prise en charge floue pour tout [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Pour plus d’informations, consultez [VisualElement Blur on iOS](visualelement-blur.md).
- Désactivation du mode de couleurs hérité sur un pris en charge [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Pour plus d’informations, consultez [VisualElement Legacy Color mode sur iOS](legacy-color-mode.md).
- Activation d’une ombre portée sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Pour plus d’informations, consultez [VisualElement Drop Shadows on iOS](visualelement-drop-shadow.md).
- Activation d’un [`VisualElement`](xref:Xamarin.Forms.VisualElement) objet pour devenir le premier répondeur aux événements tactiles. Pour plus d’informations, consultez [premier répondeur VisualElement](visualelement-first-responder.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les Xamarin.Forms vues sur iOS :

- Définition de la [`Cell`](xref:Xamarin.Forms.Cell) couleur d’arrière-plan. Pour plus d’informations, consultez [couleur d’arrière-plan de la cellule sur iOS](cell-background-color.md).
- Contrôle de la sélection des éléments dans un [`DatePicker`](xref:Xamarin.Forms.DatePicker) . Pour plus d’informations, consultez [sélection de l’élément DatePicker sur iOS](datepicker-selection.md).
- S’assurer que le texte entré s’ajuste à un [`Entry`](xref:Xamarin.Forms.Entry) en ajustant la taille de la police. Pour plus d’informations, consultez [taille de police d’entrée sur iOS](entry-font-size.md).
- Définition de la couleur du curseur dans un [`Entry`](xref:Xamarin.Forms.Entry) . Pour plus d’informations, consultez [couleur du curseur d’entrée sur iOS](entry-cursor-color.md).
- Contrôle de la [`ListView`](xref:Xamarin.Forms.ListView) flotte des cellules d’en-tête lors du défilement. Pour plus d’informations, consultez [style d’en-tête de groupe ListView sur iOS](listview-group-header-style.md).
- Contrôler si les animations de lignes sont désactivées lors de la [`ListView`](xref:Xamarin.Forms.ListView) mise à jour de la collection d’éléments. Pour plus d’informations, consultez [animations de lignes ListView sur iOS](listview-row-animations.md).
- Définition du style de séparateur sur un [`ListView`](xref:Xamarin.Forms.ListView) . Pour plus d’informations, consultez [style de séparateur ListView sur iOS](listview-separator-style.md).
- Contrôle de la sélection des éléments dans un [`Picker`](xref:Xamarin.Forms.Picker) . Pour plus d’informations, consultez [sélection d’un élément de sélecteur sur iOS](picker-selection.md).
- Contrôle de l’existence ou non d’un [`SearchBar`](xref:Xamarin.Forms.SearchBar) arrière-plan. Pour plus d’informations, consultez [Searchbar style on iOS](searchbar-style.md).
- Activation de la [`Slider.Value`](xref:Xamarin.Forms.Slider.Value) propriété à définir en appuyant sur une position sur la [`Slider`](xref:Xamarin.Forms.Slider) barre, plutôt qu’en faisant glisser le `Slider` curseur de défilement. Pour plus d’informations, consultez [curseur sur iOS](slider-thumb.md).
- Contrôle de la transition utilisée lors de l’ouverture d’un `SwipeView` . Pour plus d’informations, consultez [mode de transition par balayage SwipeView](swipeview-swipetransitionmode.md).
- Contrôle de la sélection des éléments dans un [`TimePicker`](xref:Xamarin.Forms.TimePicker) . Pour plus d’informations, consultez [sélection d’élément timepicker sur iOS](timepicker-selection.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les Xamarin.Forms pages sur iOS :

- Contrôle de l’application de la page de détails d’une [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) ombre, lors de la révélation de la page maître. Pour plus d’informations, consultez [Shadow MasterDetailPage](masterdetailpage-shadow.md).
- Masquage du séparateur de barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Pour plus d’informations, consultez [séparateur de barre NavigationPage sur iOS](navigation-bar-separator.md).
- Contrôle de l’affichage transparent de la barre de navigation. Pour plus d’informations, consultez [barre de navigation translucidité sur iOS](navigation-bar-translucent.md).
- Contrôle si la couleur de texte de la barre d’État sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) est ajustée pour correspondre à la luminosité de la barre de navigation. Pour plus d’informations, consultez [mode de couleur du texte de la barre NavigationPage sur iOS](status-bar-text-color.md).
- Contrôle du fait que le titre de la page s’affiche sous la forme d’un titre volumineux dans la barre de navigation de la page. Pour plus d’informations, consultez [titres de page de grande taille sur iOS](page-large-title.md).
- Définition de la visibilité de l’indicateur de démarrage sur un [`Page`](xref:Xamarin.Forms.Page) . Pour plus d’informations, consultez [visibilité de l’indicateur de démarrage sur iOS](page-home-indicator.md).
- Définition de la visibilité de la barre d’État sur un [`Page`](xref:Xamarin.Forms.Page) . Pour plus d’informations, consultez [visibilité de la barre d’état de la page sur iOS](page-status-bar-visibility.md).
- S’assurer que le contenu de la page est positionné sur une zone de l’écran qui est sécurisée pour tous les appareils iOS. Pour plus d’informations, consultez [le Guide de disposition en zone sécurisée sur iOS](page-safe-area-layout.md).
- Définition du style de présentation des pages modales. Pour plus d’informations, consultez [style de présentation de page modale](page-presentation-style.md).
- Définition du mode translucidité de la barre d’onglets sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Pour plus d’informations, consultez [TabbedPage translucide TabBar sur iOS](tabbedpage-translucent-tabbar.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les Xamarin.Forms dispositions sur iOS :

- Contrôle de la [`ScrollView`](xref:Xamarin.Forms.ScrollView) gestion d’un mouvement tactile ou de sa transmission à son contenu. Pour plus d’informations, consultez [ScrollView content touche on iOS](scrollview-content-touches.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) classe sur iOS :

- Désactivation de la mise à l’échelle de l’accessibilité pour les tailles de police nommées. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommées sur iOS](named-font-size-scaling.md).
- Activation de la disposition du contrôle et du rendu des mises à jour à effectuer sur le thread principal. Pour plus d’informations, consultez [mises à jour du contrôle de thread principal sur iOS](main-thread-updates-ui.md).
- Activation d’un [`PanGestureRecognizer`](xref:Xamarin.Forms.PanGestureRecognizer) dans une vue de défilement pour capturer et partager le mouvement panoramique avec la vue de défilement. Pour plus d’informations, consultez [reconnaissance simultanée des mouvements de panoramiques sur iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>mise en forme spécifique à iOS

Xamarin.Formspermet de définir des couleurs et des styles d’interface utilisateur multiplateforme, mais il existe d’autres options pour définir le thème de votre iOS à l’aide d’API de plateforme dans le projet iOS.

[En savoir plus](formatting.md) sur la mise en forme de l’interface utilisateur à l’aide d’API spécifiques à iOS, telles que la configuration d' **info. plist** et l' `UIAppearance` API.

![](images/status-white-sml.png "iOS Theming")

## <a name="other-ios-features"></a>Autres fonctionnalités iOS

À l’aide de [convertisseurs personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), de [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)et de [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), il est possible d’incorporer un large éventail de fonctionnalités natives dans Xamarin.Forms des applications pour iOS.
