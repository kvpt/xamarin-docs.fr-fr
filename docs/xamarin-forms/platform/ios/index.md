---
title: fonctionnalités de la plateforme iOS dans Xamarin.Forms
description: Ajout de fonctionnalités spécifique à iOS pour les applications Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 634AB62E-68C8-454C-838B-F1CC4E4E21BC
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/22/2019
ms.openlocfilehash: 7aa9d298c2219dff60ca8ca1f917c72194ad21ca
ms.sourcegitcommit: 0fd04ea3af7d6a6d6086525306523a5296eec0df
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67512673"
---
# <a name="ios-platform-features-in-xamarinforms"></a>fonctionnalités de la plateforme iOS dans Xamarin.Forms

Développement d’applications Xamarin.Forms pour iOS nécessite Visual Studio. Le [page spécifications](~/get-started/requirements.md) contient plus d’informations sur les conditions préalables.

## <a name="platform-specifics"></a>Caractéristiques de la plateforme

Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets.

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les vues Xamarin.Forms, les pages et les dispositions sur iOS :

- Flou prend en charge aucun [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [flou VisualElement sur iOS](visualelement-blur.md).
- Désactiver le mode hérité de couleur sur la prise en charge [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement Mode de couleur hérité sur iOS](legacy-color-mode.md).
- L’activation d’une ombre sur un [ `VisualElement` ](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [VisualElement ombres sur iOS](visualelement-drop-shadow.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les vues Xamarin.Forms sur iOS :

- Définition de la [ `Cell` ](xref:Xamarin.Forms.Cell) couleur d’arrière-plan. Pour plus d’informations, consultez [couleur d’arrière-plan sur iOS](cell-background-color.md).
- S’assurer qu’entrée texte s’adapte à un [ `Entry` ](xref:Xamarin.Forms.Entry) en ajustant la taille de police. Pour plus d’informations, consultez [taille de police d’entrée sur iOS](entry-font-size.md).
- Définir la couleur du curseur dans un [ `Entry` ](xref:Xamarin.Forms.Entry). Pour plus d’informations, consultez [entrée curseur couleur sur iOS](entry-cursor-color.md).
- Contrôle si [ `ListView` ](xref:Xamarin.Forms.ListView) cellules d’en-tête flottant pendant le défilement. Pour plus d’informations, consultez [Style d’en-tête de groupe ListView sur iOS](listview-group-header-style.md).
- Contrôle si les animations de ligne sont désactivées lorsque le [ `ListView` ](xref:Xamarin.Forms.ListView) collection items est en cours de mise à jour. Pour plus d’informations, consultez [ListView ligne Animations sur iOS](listview-row-animations.md).
- Définir le style du séparateur sur un [ `ListView` ](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [Style de séparateur de ListView sur iOS](listview-separator-style.md).
- Contrôle si la sélection d’éléments se produit dans un [ `Picker` ](xref:Xamarin.Forms.Picker). Pour plus d’informations, consultez [sélecteur d’élément de sélection sur iOS](picker-selection.md).
- L’activation de la [ `Slider.Value` ](xref:Xamarin.Forms.Slider.Value) propriété à définir en appuyant sur une position sur le [ `Slider` ](xref:Xamarin.Forms.Slider) barre, plutôt que d’avoir à faire glisser le `Slider` thumb. Pour plus d’informations, consultez [curseur Thumb appuyez sur iOS](slider-thumb.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les pages Xamarin.Forms sur iOS :

- Masquer le séparateur de barre de navigation sur un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage). Pour plus d’informations, consultez [NavigationPage barre séparateur sur iOS](navigation-bar-separator.md).
- Contrôle si la barre de navigation est translucide. Pour plus d’informations, consultez [translucidité de barre de Navigation sur iOS](navigation-bar-translucent.md).
- Contrôle si le texte de barre d’état de couleur sur un [ `NavigationPage` ](xref:Xamarin.Forms.NavigationPage) est ajustée pour correspondre à la luminosité de la barre de navigation. Pour plus d’informations, consultez [Mode de couleur de texte de barre NavigationPage sur iOS](status-bar-text-color.md).
- Contrôle si le titre de page est affiché comme un titre de grande taille dans la barre de navigation de page. Pour plus d’informations, consultez [grand titres des pages sur iOS](page-large-title.md).
- Définition de la visibilité de l’indicateur de base sur un [ `Page` ](xref:Xamarin.Forms.Page). Pour plus d’informations, consultez [accueil indicateur de visibilité sur iOS](page-home-indicator.md).
- Définition de la visibilité de barre d’état sur un [ `Page` ](xref:Xamarin.Forms.Page). Pour plus d’informations, consultez [Page visibilité de barre d’état sur iOS](page-status-bar-visibility.md).
- S’assurer de ce contenu de la page est positionné sur une zone de l’écran qui est sécurisé pour tous les appareils iOS. Pour plus d’informations, consultez [repère de disposition de la zone sécurisée sur iOS](page-safe-area-layout.md).
- Définir le style de présentation des pages modales sur un iPad. Pour plus d’informations, consultez [iPad Style de présentation Page modale](ipad-page-presentation-style.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les dispositions Xamarin.Forms sur iOS :

- Contrôle si un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gère un mouvement tactile ou la transmet à son contenu. Pour plus d’informations, consultez [ScrollView contenu a un impact sur iOS](scrollview-content-touches.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour Xamarin.Forms [ `Application` ](xref:Xamarin.Forms.Application) classe sur iOS :

- La désactivation d’accessibilité de la mise à l’échelle pour les tailles de police nommée. Pour plus d’informations, consultez [mise à l’échelle de l’accessibilité pour les tailles de police nommé sur iOS](named-font-size-scaling.md).
- L’activation de la disposition des contrôles et le rendu des mises à jour doivent être effectuées sur le thread principal. Pour plus d’informations, consultez [Main Thread contrôle les mises à jour sur iOS](main-thread-updates-ui.md).
- L’activation une [ `PanGestureRecognizer` ](xref:Xamarin.Forms.PanGestureRecognizer) dans une vue de défilement pour capturer et partager le mouvement panoramique avec la vue de défilement. Pour plus d’informations, consultez [simultanées reconnaissance de mouvement panoramique sur iOS](application-pan-gesture.md).

## <a name="ios-specific-formatting"></a>mise en forme spécifique à iOS

Xamarin.Forms permet de styles d’interface utilisateur d’inter-plateformes et des couleurs pour être définie - mais il existe d’autres options permettant de définir le thème de votre iOS à l’aide des API de la plateforme dans le projet iOS.

[En savoir plus](formatting.md) sur la mise en forme de l’interface utilisateur à l’aide d’API spécifique à iOS, tel que **Info.plist** configuration et le `UIAppearance` API.

![](images/status-white-sml.png "iOS thèmes")

## <a name="other-ios-features"></a>Autres fonctionnalités d’iOS

À l’aide de [renderers personnalisés](~/xamarin-forms/app-fundamentals/custom-renderer/index.md), le [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)et le [MessagingCenter](~/xamarin-forms/app-fundamentals/messaging-center.md), il est possible d’incorporer un large éventail de fonctionnalités natives dans Applications Xamarin.Forms pour iOS.
