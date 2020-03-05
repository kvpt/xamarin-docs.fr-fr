---
title: Fonctionnalités de la plateforme Windows
description: Cet article explique la prise en charge de la plateforme Windows qui est disponible dans Xamarin. Forms.
ms.prod: xamarin
ms.assetid: F6EA9E49-FB3E-442F-AF13-B7AD0C80D11F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/16/2020
ms.openlocfilehash: 694ec24697937b114036eceab1cafd5aae3617d8
ms.sourcegitcommit: 10b4d7952d78f20f753372c53af6feb16918555c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/26/2020
ms.locfileid: "78292728"
---
# <a name="windows-platform-features"></a>Fonctionnalités de la plateforme Windows

Le développement d’applications Xamarin. Forms pour les plateformes Windows nécessite Visual Studio. La [page plateformes prises en charge](~/get-started/supported-platforms.md) contient des informations supplémentaires sur les conditions préalables.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Spécificités des plateformes

Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets.

La fonctionnalité spécifique à la plateforme suivante est fournie pour les vues, les pages et les dispositions Xamarin. Forms sur le plateforme Windows universelle (UWP) :

- Définition d’une clé d’accès pour un [`VisualElement`](xref:Xamarin.Forms.VisualElement). Pour plus d’informations, consultez [clés d’accès VisualElement sur Windows](visualelement-access-keys.md).
- Désactivation du mode de couleurs hérité sur un [`VisualElement`](xref:Xamarin.Forms.VisualElement)pris en charge. Pour plus d’informations, consultez [VisualElement Legacy Color mode on Windows](legacy-color-mode.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les vues Xamarin. Forms sur UWP :

- Détection de l’ordre de lecture à partir du contenu de texte dans les instances [`Entry`](xref:Xamarin.Forms.Entry), [`Editor`](xref:Xamarin.Forms.Editor)et [`Label`](xref:Xamarin.Forms.Label) . Pour plus d’informations, consultez [ordre de lecture de InputView sur Windows](inputview-reading-order.md).
- Activation de la prise en charge du mouvement TAP dans une [`ListView`](xref:Xamarin.Forms.ListView). Pour plus d’informations, consultez [ListView SelectionMode sur Windows](listview-selectionmode.md).
- Activation de la direction d’extraction d’un `RefreshView` à modifier. Pour plus d’informations, consultez [direction RefreshView pull sur Windows](refreshview-pulldirection.md).
- Activation d’une [`SearchBar`](xref:Xamarin.Forms.SearchBar) pour interagir avec le moteur de vérification orthographique. Pour plus d’informations, consultez [SearchBar vérification orthographique sur Windows](searchbar-spell-check.md).
- Activation d’un [`WebView`](xref:Xamarin.Forms.WebView) pour afficher des alertes JavaScript dans une boîte de dialogue de message UWP. Pour plus d’informations, consultez [afficher des alertes JavaScript sur Windows](webview-javascript-alert.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les pages Xamarin. Forms sur UWP :

- Réduction de la barre de navigation [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) . Pour plus d’informations, consultez [barre de navigation MasterDetailPage sur Windows](masterdetailpage-navigation-bar.md).
- Définition des options de placement de barre d’outils. Pour plus d’informations, consultez [positionnement de la barre d’outils de la page sur Windows](page-toolbar-placement.md).
- Activation des icônes de page à afficher dans une barre d’outils [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) . Pour plus d’informations, consultez [TabbedPage icons on Windows](tabbedpage-icons.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la classe Xamarin. Forms [`Application`](xref:Xamarin.Forms.Application) sur UWP :

- Spécifie le répertoire du projet à partir duquel les ressources d’images seront chargées. Pour plus d’informations, consultez [répertoire d’images par défaut sur Windows](default-image-directory.md).

## <a name="platform-support"></a>Plateforme prise en charge

Les modèles Xamarin. Forms disponibles dans Visual Studio contiennent un projet plateforme Windows universelle (UWP).

> [!NOTE]
> Xamarin. Forms 1. x et 2. x prennent en charge _Windows Phone 8 Silverlight_, _Windows Phone 8,1_et _Windows 8.1_ développement d’applications. Toutefois, ces types de projets ont été dépréciés.

## <a name="getting-started"></a>Prise en main

Accédez à **fichier > nouveau projet de >** dans Visual Studio et choisissez l’un des modèles d' **application vide > multiplateforme (Xamarin. Forms)** pour commencer.

Les anciennes solutions Xamarin. Forms, ou celles créées sur macOS, n’ont pas tous les projets Windows listés ci-dessus (mais elles doivent être ajoutées manuellement). Si la plateforme Windows que vous souhaitez cibler ne figure pas dans votre solution, consultez les [instructions d’installation](installation/index.md) pour ajouter le ou les types de projet Windows souhaités.

## <a name="samples"></a>Exemples

[Tous les exemples](https://github.com/xamarin/xamarin-forms-book-preview-2) du livre de Charles Petzold [*qui crée des Mobile Apps avec Xamarin. forms*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluent plateforme Windows universelle (pour les projets Windows 10).

L' [application de démonstration « Scott Hanselman »](https://github.com/jamesmontemagno/Hanselman.Forms) est disponible séparément. elle comprend également des projets d’usure Apple Watch et Android (à l’aide de Xamarin. iOS et Xamarin. Android, Xamarin. Forms ne s’exécute pas sur ces plateformes).

## <a name="related-links"></a>Liens connexes

- [Configurer des projets Windows](~/xamarin-forms/platform/windows/installation/index.md)
