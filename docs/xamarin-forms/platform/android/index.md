---
title: Fonctionnalités de la plateforme Android
description: Cet article explique comment ajouter des fonctionnalités Android spécifiques aux Xamarin.Forms applications.
ms.prod: xamarin
ms.assetid: E24168F3-0138-4814-86EA-B467F6B8A545
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/11/2019
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 3045db1248aa16529d4e43b9a8afc97377cfd9cb
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84128943"
---
# <a name="android-platform-features"></a>Fonctionnalités de la plateforme Android

Le développement Xamarin.Forms d’applications pour Android nécessite Visual Studio. La [page plateformes prises en charge](~/get-started/supported-platforms.md) contient des informations supplémentaires sur les conditions préalables.

## <a name="platform-specifics"></a>Spécificités des plateformes

Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés.

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les Xamarin.Forms affichages, les pages et les mises en page sur Android :

- Contrôle de l’ordre de plan des éléments visuels pour déterminer l’ordre de dessin. Pour plus d’informations, consultez [élévation de VisualElement sur Android](visualelement-elevation.md).
- Désactivation du mode de couleurs hérité sur un pris en charge [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Pour plus d’informations, consultez [VisualElement Legacy Color mode on Android](legacy-color-mode.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les Xamarin.Forms affichages sur Android :

- À l’aide des valeurs de remplissage par défaut et d’ombre des boutons Android. Pour plus d’informations, consultez [remplissage de bouton et ombres sur Android](button-padding-shadow.md).
- Définition des options de l’éditeur de méthode d’entrée pour le clavier conditionnel d’un [`Entry`](xref:Xamarin.Forms.Entry) . Pour plus d’informations, consultez [options de l’éditeur de méthode d’entrée d’entrée sur Android](entry-ime-options.md).
- Activation d’une ombre portée sur un `ImageButton` . Pour plus d’informations, consultez la section [ImageButton supprimer les ombres sur Android](imagebutton-drop-shadow.md).
- Activation du défilement rapide dans un [`ListView`](xref:Xamarin.Forms.ListView) pour plus d’informations, consultez [défilement rapide de ListView sur Android](listview-fast-scrolling.md).
- Contrôle de la transition utilisée lors de l’ouverture d’un `SwipeView` . Pour plus d’informations, consultez [mode de transition par balayage SwipeView](swipeview-swipetransitionmode.md).
- Contrôler si un [`WebView`](xref:Xamarin.Forms.WebView) peut afficher du contenu mixte. Pour plus d’informations, consultez [vue d’ensemble du contenu mixte sur Android](webview-mixed-content.md).
- Activation du zoom sur un [`WebView`](xref:Xamarin.Forms.WebView) . Pour plus d’informations, consultez [Zoom WebView sur Android](webview-zoom-controls.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les Xamarin.Forms cellules sur Android :

- Activation [`ViewCell`](xref:Xamarin.Forms.ViewCell) du mode hérité des actions de contexte, afin que le menu actions de contexte ne soit pas mis à jour lorsque l’élément sélectionné dans un [`ListView`](xref:Xamarin.Forms.ListView) change. Pour plus d’informations, consultez [actions de contexte ViewCell sur Android](viewcell-context-actions.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les Xamarin.Forms pages sur Android :

- Définition de la hauteur de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) . Pour plus d’informations, consultez [hauteur de la barre NavigationPage sur Android](navigationpage-bar-height.md).
- Désactivation des animations de transition lors de la navigation dans les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Pour plus d’informations, consultez [animations de transition de page TabbedPage sur Android](tabbedpage-transition-animations.md).
- Activation du balayage entre les pages d’un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Pour plus d’informations, consultez [page TabbedPage de balayage sur Android](tabbedpage-page-swiping.md).
- Définition de la couleur et de l’emplacement de la barre d’outils sur un [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Pour plus d’informations, consultez [placement et couleur de la barre d’outils TabbedPage sur Android](tabbedpage-toolbar-placement-color.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) classe sur Android :

- Définition du mode d’opération d’un clavier logiciel. Pour plus d’informations, consultez [mode d’entrée de clavier Soft sur Android](soft-keyboard-input-mode.md).
- Désactivation des [`Disappearing`](xref:Xamarin.Forms.Page.Appearing) événements et du [`Appearing`](xref:Xamarin.Forms.Page.Appearing) cycle de vie de la page pour les applications qui utilisent AppCompat, respectivement. Pour plus d’informations, consultez [événements du cycle de vie d’une page sur Android](page-lifecycle-events.md).

## <a name="platform-support"></a>Plateforme prise en charge

À l’origine, le Xamarin.Forms projet Android par défaut utilisait un ancien style de rendu de contrôle qui était courant avant Android 5,0. Les applications générées à l’aide du modèle ont `FormsApplicationActivity` comme classe de base de leur activité principale.

## <a name="material-design-via-appcompat"></a>Conception de matériau via AppCompat

Xamarin.FormsLes projets Android utilisent désormais `FormsAppCompatActivity` la classe de base de leur activité principale. Cette classe utilise les fonctionnalités de **AppCompat** fournies par Android pour implémenter des thèmes de conception de matériau.

Pour ajouter des thèmes de conception de matériau à votre Xamarin.Forms projet Android, suivez les [instructions d’installation pour la prise en charge de AppCompat](appcompat-material-design.md)

Voici l’exemple **TODO** avec la valeur par défaut `FormsApplicationActivity` :

[![](images/before-appcompat-sml.png "Todo Sample Application Without AppCompat")](images/before-appcompat.png#lightbox "Todo Sample Application Without AppCompat")

Et il s’agit du même code après la mise à niveau du projet pour utiliser `FormsAppCompatActivity` (et l’ajout d’informations de thème supplémentaires) :

[![](images/post-appcompat-sml.png "Todo Sample Application With AppCompat and Theming")](images/post-appcompat.png#lightbox "Todo Sample Application With AppCompat and Theming")

> [!NOTE]
> Lorsque `FormsAppCompatActivity` vous utilisez, les [classes de base de certains convertisseurs personnalisés Android](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) seront différentes.

## <a name="androidx-migration"></a>Migration AndroidX

AndroidX remplace la bibliothèque de prise en charge Android. Pour en savoir plus sur AndroidX et sur la façon de migrer une Xamarin.Forms application pour utiliser des bibliothèques AndroidX, consultez [migration de AndroidX dans Xamarin.Forms ](~/xamarin-forms/platform/android/androidx-migration.md).

## <a name="related-links"></a>Liens connexes

- [Ajouter une prise en charge de la conception matérielle](appcompat-material-design.md)
