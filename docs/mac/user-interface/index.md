---
title: contrôles de l’interface utilisateur macOS dans Xamarin. Mac
description: Ce document contient des liens vers des guides qui décrivent différents contrôles d’interface utilisateur disponibles pour les développeurs Xamarin. Mac. Le contenu lié examine les fenêtres, les boîtes de dialogue, les alertes, les menus, les barres d’outils, les vues de table, les vues en mode plan, etc.
ms.prod: xamarin
ms.assetid: 876B6EC2-E158-43F2-B9C9-03F54F3D2A49
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/27/2018
ms.openlocfilehash: 7f5303cd63c6ff1433b56b3f47b67d3925b1d1e1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032779"
---
# <a name="macos-user-interface-controls-in-xamarinmac"></a>contrôles de l’interface utilisateur macOS dans Xamarin. Mac

_Cet article contient des liens vers des guides qui décrivent différents contrôles d’interface utilisateur macOS._

Lorsque vous travaillez C# avec et .net dans une application Xamarin. Mac, vous avez accès aux mêmes contrôles d’interface utilisateur qu’un développeur qui travaille en *objective-C* et *Xcode* . Comme Xamarin. Mac s’intègre directement à Xcode, vous pouvez utiliser la _Interface Builder_ de Xcode pour créer et gérer vos interfaces utilisateur (ou éventuellement les créer directement dans C# le code).

Les guides listés ci-dessous fournissent des informations détaillées sur l’utilisation des éléments d’interface utilisateur macOS dans une application Xamarin. Mac. Nous vous recommandons vivement d’utiliser l’article [Hello, Mac](~/mac/get-started/hello-mac.md) , en particulier la [Présentation de Xcode et Interface Builder](~/mac/get-started/hello-mac.md#introduction-to-xcode-and-interface-builder) et les sections [actions et actions](~/mac/get-started/hello-mac.md#outlets-and-actions) , car il aborde les concepts et les techniques clés que nous allons utiliser dans. chaque article.

Vous pouvez également jeter un coup d’œil à la section [exposition des C# classes/méthodes à Objective-C](~/mac/internals/how-it-works.md#exposing-c-classes--methods-to-objective-c) du document [Internals Xamarin. Mac](~/mac/internals/how-it-works.md) , car elle explique la `Register` et `Export` attributs utilisés pour relier vos C# classes à Objets objective-C et éléments d’interface utilisateur.

## <a name="windowsmacuser-interfacewindowmd"></a>[Fenêtres](~/mac/user-interface/window.md)

Cet article traite de l’utilisation des fenêtres et des panneaux dans une application Xamarin. Mac. Il aborde la création et la maintenance des fenêtres et des panneaux dans Xcode et Interface Builder, le chargement des fenêtres et des panneaux à partir de fichiers. Storyboard ou. C# XIB, l’utilisation de Windows et la réponse à Windows dans le code.

## <a name="dialogsmacuser-interfacedialogmd"></a>[Boîtes de dialogue](~/mac/user-interface/dialog.md)

Cet article traite de l’utilisation des boîtes de dialogue et des fenêtres modales dans une application Xamarin. Mac. Il aborde la création et la gestion des fenêtres modales dans Xcode et Interface Builder, l’utilisation de boîtes de dialogue standard et C# l’affichage et la réponse à Windows dans le code.

## <a name="alertsmacuser-interfacealertmd"></a>[Alertes](~/mac/user-interface/alert.md)

Cet article traite de l’utilisation des alertes dans une application Xamarin. Mac. Il aborde la création et l' C# affichage des alertes à partir du code et la réponse aux alertes.

## <a name="menusmacuser-interfacemenumd"></a>[Menus](~/mac/user-interface/menu.md)

Les menus sont utilisés dans différentes parties de l’interface utilisateur d’une application Mac. à partir du menu principal de l’application en haut de l’écran, aux menus contextuels et aux menus contextuels qui peuvent apparaître n’importe où dans une fenêtre. Les menus sont une partie intégrante de l’expérience utilisateur d’une application Mac. Cet article traite de l’utilisation des menus de cacao dans une application Xamarin. Mac.

## <a name="standard-controlsmacuser-interfacestandard-controlsmd"></a>[Contrôles standard](~/mac/user-interface/standard-controls.md)

Utilisation des contrôles AppKit standard, tels que les boutons, les étiquettes, les champs de texte, les cases à cocher et les contrôles segmentés dans une application Xamarin. Mac. Ce guide explique comment les ajouter à la conception d’une interface utilisateur dans le Interface Builder de Xcode, les exposer à du code via des sorties et des actions, et C# utiliser des contrôles AppKit dans le code.

## <a name="toolbarsmacuser-interfacetoolbarmd"></a>[Barres d’outils](~/mac/user-interface/toolbar.md)

Cet article traite de l’utilisation des barres d’outils dans une application Xamarin. Mac. Il aborde la création et la gestion des barres d’outils dans Xcode et Interface Builder, comment exposer les éléments de la barre d’outils au code à l’aide de prises et d’actions, activer et désactiver C# des éléments de barre d’outils et enfin répondre aux éléments de barre d’outils dans le code.

## <a name="table-viewsmacuser-interfacetable-viewmd"></a>[Vues de table](~/mac/user-interface/table-view.md)

Cet article traite de l’utilisation des vues de table dans une application Xamarin. Mac. Il aborde la création et la gestion des vues de table dans Xcode et Interface Builder, comment exposer les éléments d’affichage de table au code à l’aide de prises et d’actions, en remplissant C# les vues de table et en répondant aux éléments de vue de table dans le code.

## <a name="outline-viewsmacuser-interfaceoutline-viewmd"></a>[Modes plan](~/mac/user-interface/outline-view.md)

Cet article traite de l’utilisation des vues en mode plan dans une application Xamarin. Mac. Il aborde la création et la gestion des vues en mode plan dans Xcode et Interface Builder, comment exposer les éléments du mode plan au code à l’aide de prises et d’actions, le remplissage C# des vues en mode plan et la réponse aux éléments de vue de plan dans le code.

## <a name="source-listsmacuser-interfacesource-listmd"></a>[Listes de sources](~/mac/user-interface/source-list.md)

Cet article traite de l’utilisation des listes de sources dans une application Xamarin. Mac. Il aborde la création et la gestion des listes de sources dans Xcode et Interface Builder, l’exposition des éléments de liste source au code à l’aide de prises et d’actions, le remplissage C# des listes de sources et la réponse aux éléments de liste source dans le code.

## <a name="collection-viewsmacuser-interfacecollection-viewmd"></a>[Vues de collection](~/mac/user-interface/collection-view.md)

Cet article traite de l’utilisation des vues de collection dans une application Xamarin. Mac. Il aborde la création et la gestion des vues de collection dans Xcode et Interface Builder, comment exposer les éléments d’affichage de collection au code à l’aide de prises et d’actions, en C# remplissant des vues de collection et en répondant à des vues de collection dans le code.

## <a name="creating-custom-controlsmacuser-interfacecustom-controlsmd"></a>[Création de contrôles personnalisés](~/mac/user-interface/custom-controls.md)

Cet article explique comment créer des contrôles d’interface utilisateur personnalisés (en héritant de `NSControl`), en dessinant une interface personnalisée pour le contrôle et en créant des actions personnalisées qui peuvent être utilisées avec les Interface Builder de Xcode.

## <a name="mac-samples-gallery"></a>Galerie d’exemples Mac

Nous vous suggérons également de jeter un coup d’œil à la [Galerie d’exemples Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac). Il comprend un grand éventail de code prêt à l’emploi qui peut vous aider à faire passer rapidement un projet Xamarin. Mac.

## <a name="related-links"></a>Liens connexes

- [Human Interface Guidelines pour macOS](https://developer.apple.com/macos/human-interface-guidelines/overview/themes/)
