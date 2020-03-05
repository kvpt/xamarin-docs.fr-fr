---
title: Fonctionnalités de la plateforme Android
description: Cet article explique comment ajouter des fonctionnalités Android spécifiques aux applications Xamarin. Forms.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
ms.openlocfilehash: 7ad7349c89913129cccdd77ac843188cbe668571
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292073"
---
# <a name="android-platform-features"></a>Fonctionnalités de la plateforme Android

Le développement d’applications Xamarin. Forms pour Android nécessite Visual Studio. La [page plateformes prises en charge](~/get-started/supported-platforms.md) contient des informations supplémentaires sur les conditions préalables.

## <a name="platform-specifics"></a>Spécificités des plateformes

Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets.

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues, les pages et les mises en page Xamarin. Forms sur Android :

- Contrôler l’ordre de plan des éléments visuels pour déterminer l’ordre de dessin. Pour plus d’informations, consultez [élévation de VisualElement sur Android](visualelement-elevation.md).
- Désactivation du mode de couleurs hérité sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement)pris en charge. Pour plus d’informations, consultez [VisualElement Legacy Color mode on Android](legacy-color-mode.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues Xamarin. Forms sur Android :

- À l’aide de la marge intérieure par défaut et les valeurs de clichés instantanés des boutons Android. Pour plus d’informations, consultez [remplissage de bouton et ombres sur Android](button-padding-shadow.md).
- Définition des options de l’éditeur de méthode d’entrée pour le clavier conditionnel d’un [`Entry`](xref:Xamarin.Forms.Entry). Pour plus d’informations, consultez [options de l’éditeur de méthode d’entrée d’entrée sur Android](entry-ime-options.md).
- Activation d’une ombre portée sur un `ImageButton`. Pour plus d’informations, consultez la section [ImageButton supprimer les ombres sur Android](imagebutton-drop-shadow.md).
- Pour plus d’informations sur l’activation du défilement rapide dans une [`ListView`](xref:Xamarin.Forms.ListView) , consultez [défilement rapide de ListView sur Android](listview-fast-scrolling.md).
- Contrôle de la transition utilisée lors de l’ouverture d’un `SwipeView`. Pour plus d’informations, consultez [mode de transition par balayage SwipeView](swipeview-swipetransitionmode.md).
- Contrôler si une [`WebView`](xref:Xamarin.Forms.WebView) peut afficher du contenu mixte. Pour plus d’informations, consultez [vue d’ensemble du contenu mixte sur Android](webview-mixed-content.md).
- Activation du zoom sur un [`WebView`](xref:Xamarin.Forms.WebView). Pour plus d’informations, consultez [Zoom WebView sur Android](webview-zoom-controls.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les cellules Xamarin. Forms sur Android :

- Activation [`ViewCell`](xref:Xamarin.Forms.ViewCell) mode hérité des actions de contexte, afin que le menu actions de contexte ne soit pas mis à jour lorsque l’élément sélectionné dans un [`ListView`](xref:Xamarin.Forms.ListView) change. Pour plus d’informations, consultez [actions de contexte ViewCell sur Android](viewcell-context-actions.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les pages Xamarin. Forms sur Android :

- Définition de la hauteur de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage). Pour plus d’informations, consultez [hauteur de la barre NavigationPage sur Android](navigationpage-bar-height.md).
- Désactivation des animations de transition lors de la navigation dans les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, consultez [animations de transition de page TabbedPage sur Android](tabbedpage-transition-animations.md).
- Activation du balayage entre les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, consultez [page TabbedPage de balayage sur Android](tabbedpage-page-swiping.md).
- Définition de la couleur et de l’emplacement de la barre d’outils sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage). Pour plus d’informations, consultez [placement et couleur de la barre d’outils TabbedPage sur Android](tabbedpage-toolbar-placement-color.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la classe Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application) sur Android :

- Définition du mode de fonctionnement d’un clavier logiciel. Pour plus d’informations, consultez [mode d’entrée de clavier Soft sur Android](soft-keyboard-input-mode.md).
- La désactivation des événements de cycle de vie de la page [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) et [`Appearing`](xref:Xamarin.Forms.Page.Appearing) respectivement sur pause et reprendre, pour les applications qui utilisent AppCompat. Pour plus d’informations, consultez [événements du cycle de vie d’une page sur Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Plateforme prise en charge

À l’origine, le projet Android Xamarin. Forms par défaut utilisait un ancien style de rendu de contrôle qui était courant avant Android 5,0. Les applications générées à l’aide du modèle ont `FormsApplicationActivity` comme classe de base de leur activité principale.

## <a name="material-design-via-appcompat"></a>Conception de matériau via AppCompat

Les projets Android Xamarin. Forms utilisent désormais `FormsAppCompatActivity` comme classe de base de leur activité principale. Cette classe utilise les fonctionnalités de **AppCompat** fournies par Android pour implémenter des thèmes de conception de matériau.

Pour ajouter des thèmes de conception de matériau à votre projet Android Xamarin. Forms, suivez les [instructions d’installation pour la prise en charge de AppCompat](appcompat-material-design.md)

Voici l’exemple **TODO** avec la `FormsApplicationActivity`par défaut :

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

Et il s’agit du même code après la mise à niveau du projet pour utiliser `FormsAppCompatActivity` (et l’ajout d’informations de thème supplémentaires) :

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Lorsque vous utilisez `FormsAppCompatActivity`, les [classes de base de certains convertisseurs personnalisés Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) seront différentes.

## <a name="androidx-migration"></a>Migration AndroidX

AndroidX remplace la bibliothèque de prise en charge Android. Pour en savoir plus sur AndroidX et sur la façon de migrer une application Xamarin. Forms pour utiliser des bibliothèques AndroidX, consultez [migration AndroidX dans Xamarin. Forms](~/xamarin-forms/platform/android/androidx-migration.md).

## <a name="related-links"></a>Liens connexes

- [Ajouter une prise en charge de la conception matérielle](appcompat-material-design.md)
