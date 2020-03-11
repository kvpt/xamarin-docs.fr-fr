---
title: Résumé du chapitre 5. Gestion des tailles
description: 'Création d’applications mobiles avec Xamarin.Forms : résumé du chapitre 5. Gestion des tailles'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771139"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Résumé du chapitre 5. Gestion des tailles

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Plusieurs tailles de Xamarin.Forms ont été rencontrées jusqu'à présent :

- La hauteur de la barre d’état iOS est 20
- La `BoxView` a une largeur et une hauteur par défaut de 40
- La valeur par défaut `Padding` dans un `Frame` est 20
- Le `Spacing` par défaut sur le `StackLayout` est 6
- La méthode `Device.GetNamedSize` retourne une taille de police numérique

Ces tailles ne sont pas des pixels. Au lieu de cela, ils sont des unités indépendantes du périphérique reconnues indépendamment par chaque plateforme.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, points, points de distribution, adresses IP dynamiques et DIUs

Au début de l’historique du Mac d’Apple et Microsoft Windows, les programmeurs a travaillé en unités de pixels. Toutefois, l’arrivée de résolution supérieure affiche requis une approche abstraite et plus virtualisée aux coordonnées d’écran. Dans le monde Mac, les programmeurs fonctionnaient en unités de *points*, traditionnellement 1/72 pouces, tandis que les développeurs Windows utilisaient des *unités indépendantes* de l’appareil (DIUs) basées sur 1/96 pouces.

Toutefois, les appareils mobiles sont généralement conservées plus proches de la face et ont une résolution supérieure à desktop écrans, ce qui implique qu’une densité de pixels supérieure peut être tolérée.

Les programmeurs ciblant les appareils iPhone et iPad d’Apple continuent à fonctionner en unités de *points*, mais il y a 160 de ces points au pouce. Selon l’appareil, il peut être 1, 2 ou 3 pixels vers le point.

Android est similaire. Les programmeurs fonctionnent en unités de *pixels indépendants* de la densité (DPS) et la relation entre DPS et pixels est basée sur 160 DPS sur le pouce.

Les téléphones Windows et les appareils mobiles ont également établi des facteurs d’échelle qui impliquent quelque chose proche de 160 unités indépendantes du périphérique par pouce.

> [!NOTE]
> Xamarin.Forms ne gère plus n’importe quel téléphone basée sur Windows ou un appareil mobile.

En résumé, un programmeur Xamarin.Forms ciblant les téléphones et tablettes peut supposer que toutes les unités de mesure sont basées sur le critère suivant :

- 160 unités par pouce, équivalent à
- 64 unités pour le centimètre

Les propriétés [`Width`](xref:Xamarin.Forms.VisualElement.Width) et [`Height`](xref:Xamarin.Forms.VisualElement.Height) en lecture seule définies par `VisualElement` ont des valeurs « factices » par défaut de &ndash;1. Uniquement lorsqu’un élément a été dimensionné et prise en charge dans la disposition de ces propriétés reflètent la taille réelle de l’élément dans les unités indépendantes du périphérique. Cette taille comprend tout `Padding` défini sur l’élément, mais pas sur l' `Margin`.

Un élément visuel déclenche l’événement [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) lorsque son `Width` ou `Height` a changé. L' [**exemple de**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) taille utilise cet événement pour afficher la taille de l’écran du programme.

## <a name="metrical-sizes"></a>Tailles SURMONTANT

Le [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) utilise [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) pour afficher un `BoxView` d’un pouce de haut et d’un centimètre de largeur.

## <a name="estimated-font-sizes"></a>Tailles de police estimé

L’exemple [**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) montre comment utiliser la règle 160-Units-to-the-inch pour spécifier les tailles de police en unités de points. La cohérence visuelle parmi les plateformes qui utilisent cette technique est meilleure que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajustement du texte à la taille disponible

Il est possible de faire tenir un bloc de texte dans un rectangle particulier en calculant une `FontSize` de la `Label` à l’aide des critères suivants :

- Interligne est de 120 % de la taille de police (130 % sur les plateformes Windows).
- Largeur de caractère moyenne est de 50 % de la taille de police.

L’exemple [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) illustre cette technique. Ce programme a été écrit avant que la propriété [`Margin`](xref:Xamarin.Forms.View.Margin) soit disponible. il utilise donc un [`ContentView`](xref:Xamarin.Forms.ContentView) avec un paramètre [`Padding`](xref:Xamarin.Forms.Layout.Padding) pour simuler une marge.

[![Capture d’écran triple de la taille estimée de la police](images/ch05fg07-small.png "Ajuster le texte à la taille disponible")](images/ch05fg07-large.png#lightbox "Ajuster le texte à la taille disponible")

## <a name="a-fit-to-size-clock"></a>Une horloge de la taille de l’ajustement

L’exemple [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) illustre l’utilisation de [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) pour démarrer un minuteur qui notifie régulièrement l’application lorsqu’il est temps de mettre à jour l’horloge. La taille de police est définie à un sixième de la largeur de page pour rendre l’affichage aussi grand que possible.

## <a name="accessibility-issues"></a>Problèmes d’accessibilité

Le programme **EstimatedFontSize** et le programme **FitToSizeClock** contiennent tous les deux un défaut subtil : si l’utilisateur modifie les paramètres d’accessibilité du téléphone sur Android ou Windows 10 mobile, le programme ne peut plus estimer la taille du texte en fonction de la taille de la police. L’exemple [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) illustre ce problème.

## <a name="empirically-fitting-text"></a>Ajustement empirique de texte

Un autre en fonction du texte à un rectangle consiste à empirique calculer la taille du rendu de texte et l’ajuster vers le haut ou vers le bas. Le programme du livre appelle [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) sur un élément visuel pour obtenir la taille souhaitée de l’élément. Cette méthode est dépréciée et les programmes doivent appeler à la place [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Pour un `Label`, le premier argument doit être la largeur du conteneur (pour autoriser le renvoi à la position), tandis que le deuxième argument doit être défini sur `Double.PositiveInfinity` pour rendre la hauteur sans contrainte. L’exemple [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) illustre cette technique.

## <a name="related-links"></a>Liens connexes

- [Chapitre 5 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemples du chapitre 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Exemples du F# chapitre 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
