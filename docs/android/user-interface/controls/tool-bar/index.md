---
title: ToolBar
description: 'La barre d’outils est un composant de barre d’action qui offre plus de flexibilité que la barre d’action par défaut : elle peut être placée n’importe où dans l’application, sa taille peut être modifiée et peut utiliser un modèle de couleurs différent du thème de l’application. En outre, chaque écran d’application peut avoir plusieurs barres d’outils.'
ms.prod: xamarin
ms.assetid: 22EE5FBD-3240-4308-AF76-EF45D72936DE
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/01/2018
ms.openlocfilehash: a0ca1aa42d9173abbc86a38ae26b14bfb4865a58
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764620"
---
# <a name="toolbar"></a>ToolBar

_La barre d’outils est un composant de barre d’action qui offre plus de flexibilité que la barre d’action par défaut : elle peut être placée n’importe où dans l’application, sa taille peut être modifiée et peut utiliser un modèle de couleurs différent du thème de l’application. En outre, chaque écran d’application peut avoir plusieurs barres d’outils._

## <a name="overview"></a>Présentation

Un élément de conception clé d’une activité Android est une *barre d’action*. La barre d’action est le composant d’interface utilisateur utilisé pour la navigation, la recherche, les menus et la personnalisation dans une application Android. Dans les versions d’Android antérieures au Lollipop Android 5,0, la barre d’action (également appelée « *barre d’application*») était le composant recommandé pour fournir cette fonctionnalité. 

Le `Toolbar` widget (introduit dans Android 5,0 Lollipop) peut être considéré comme une généralisation de l’interface &ndash; de la barre d’action. il est destiné à remplacer la barre d’action. Le `Toolbar` peut être utilisé n’importe où dans une disposition d’application et il est bien plus personnalisable qu’une barre d’action. La capture d’écran suivante illustre l' `Toolbar` exemple personnalisé créé dans ce guide : 

[![Exemple de capture d’écran d’une barre d’outils avec des éléments de menu modifier, enregistrer et déborder](images/01-toolbar-sml.png)](images/01-toolbar.png#lightbox)

Il existe quelques différences importantes entre le `Toolbar` et la barre d’action : 

- Un `Toolbar` peut être placé n’importe où dans l’interface utilisateur.

- Plusieurs barres d’outils peuvent être affichées sur le même écran.

- Si des fragments sont utilisés, chaque fragment peut avoir son `Toolbar`propre. 

- Un `Toolbar` peut être configuré pour couvrir uniquement une largeur partielle de l’écran. 

- Étant donné `Toolbar` que le n’est pas lié au modèle de couleurs de la fenêtre décor de l’activité, il peut avoir un modèle de couleurs visuellement distinct. 

- Contrairement à la barre d’action `Toolbar` , le n’inclut pas d’icône à gauche. Ses menus sur la droite utilisent moins d’espace. 

- La `Toolbar` hauteur est réglable. 

- D’autres vues peuvent être incluses dans `Toolbar`le. 

Un `Toolbar` peut contenir un ou plusieurs des éléments suivants : 

- Bouton de navigation

- Une image de logo personnalisée

- Titre et sous-titre

- Vues personnalisées

- Menu Action

- Menu de dépassement de capacité

[Les instructions de conception de matériel](https://material.google.com/) de Google vous conseillent de tirer parti de ces éléments pour donner un aspect distinct aux applications (au lieu de s’appuyer uniquement sur une icône d’application et un titre). 

Ce guide couvre les scénarios les plus couramment `Toolbar` utilisés :

- Remplacement de la barre d’action par défaut d' `Toolbar`une activité par un. 

- Ajout d’une `Toolbar` seconde à une activité.

- À l’aide de la bibliothèque de **prise en charge Android version V7** (appelée *AppCompat* dans le reste de ce guide `Toolbar` ) à déployer sur des versions antérieures d’Android. 

## <a name="requirements"></a>Configuration requise

`Toolbar`est disponible sur Android 5,0 Lollipop (API 21) et versions ultérieures. Quand vous ciblez des versions d’Android antérieures à Android 5,0, utilisez la [bibliothèque de prise en charge Android v7 AppCompat](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/), `Toolbar` qui offre une prise en charge à compatibilité descendante dans un package NuGet. 
La rubrique compatibilité de la [barre d’outils](~/android/user-interface/controls/tool-bar/toolbar-compatibility.md) explique comment utiliser cette bibliothèque. 

## <a name="related-links"></a>Liens associés

- [Barre d’outils Lollipop (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android50-toolbar)
- [Barre d’outils AppCompat (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/supportv7-appcompat-toolbar)
