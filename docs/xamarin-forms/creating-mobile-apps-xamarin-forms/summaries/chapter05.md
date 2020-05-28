---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 5. Dealing with sizes''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 37403cfe9f37972c20fb074db5e30cc54b60fea9
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136875"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Résumé du chapitre 5. Gestion des tailles

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Les notes de cette page indiquent les zones où elles Xamarin.Forms divergent du matériel présenté dans le livre.

Plusieurs tailles Xamarin.Forms ont été rencontrées jusqu’à présent :

- La hauteur de la barre d’État iOS est de 20
- `BoxView`A une largeur et une hauteur par défaut de 40
- La valeur par défaut `Padding` `Frame` est 20.
- La valeur par défaut `Spacing` de `StackLayout` est 6
- La `Device.GetNamedSize` méthode retourne une taille de police numérique

Ces tailles ne sont pas des pixels. Au lieu de cela, il s’agit d’unités indépendantes des appareils, reconnues indépendamment par chaque plateforme.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, points, DPS, DIP et DIUs

Au début des historiques des Apple Mac et Microsoft Windows, les programmeurs fonctionnaient en unités de pixels. Toutefois, l’avènement des affichages de plus haute résolution nécessitait une approche plus virtualisée et plus abstraite des coordonnées d’écran. Dans le monde Mac, les programmeurs fonctionnaient en unités de *points*, traditionnellement 1/72 pouces, tandis que les développeurs Windows utilisaient des *unités indépendantes* de l’appareil (DIUs) basées sur 1/96 pouces.

Toutefois, les périphériques mobiles sont généralement maintenus plus près de la face et ont une résolution plus élevée que les écrans de bureau, ce qui implique qu’une densité de pixels supérieure peut être tolérée.

Les programmeurs ciblant les appareils iPhone et iPad d’Apple continuent à fonctionner en unités de *points*, mais il y a 160 de ces points au pouce. En fonction de l’appareil, il peut y avoir 1, 2 ou 3 pixels au point.

Android est similaire. Les programmeurs fonctionnent en unités de *pixels indépendants* de la densité (DPS) et la relation entre DPS et pixels est basée sur 160 DPS sur le pouce.

Les téléphones Windows et les appareils mobiles ont également établi des facteurs de mise à l’échelle qui impliquent un point proche de 160 unités indépendantes du périphérique.

> [!NOTE]
> Xamarin.Formsne prend plus en charge les téléphones Windows ou les appareils mobiles.

En résumé, un Xamarin.Forms programmeur ciblant les téléphones et les tablettes peut supposer que toutes les unités de mesure sont basées sur le critère suivant :

- 160 unités par pouce, équivalent à
- 64 unités au centimètre

Les propriétés en lecture seule [`Width`](xref:Xamarin.Forms.VisualElement.Width) et [`Height`](xref:Xamarin.Forms.VisualElement.Height) définies par `VisualElement` ont des valeurs « factices » par défaut de &ndash; 1. Ces propriétés reflètent la taille réelle de l’élément dans les unités indépendantes du périphérique uniquement lorsqu’un élément a été dimensionné et adapté à la disposition. Cette taille comprend tous les `Padding` ensembles de l’élément, mais pas le `Margin` .

Un élément visuel déclenche l' [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) événement lorsque son `Width` ou `Height` a changé. L' [**exemple de**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) taille utilise cet événement pour afficher la taille de l’écran du programme.

## <a name="metrical-sizes"></a>Tailles métriques

Le [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) utilise [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) pour afficher une `BoxView` hauteur d’un pouce et un centimètre de largeur.

## <a name="estimated-font-sizes"></a>Tailles de police estimées

L’exemple [**FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) montre comment utiliser la règle 160-Units-to-the-inch pour spécifier les tailles de police en unités de points. La cohérence visuelle entre les plateformes utilisant cette technique est meilleure que `Device.GetNamedSize` .

## <a name="fitting-text-to-available-size"></a>Ajuster le texte à la taille disponible

Il est possible de faire tenir un bloc de texte dans un rectangle particulier en calculant un `FontSize` du `Label` à l’aide des critères suivants :

- L’interligne est de 120% de la taille de police (130% sur les plateformes Windows).
- La largeur moyenne des caractères est de 50% de la taille de la police.

L’exemple [**EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) illustre cette technique. Ce programme a été écrit avant que la [`Margin`](xref:Xamarin.Forms.View.Margin) propriété ne soit disponible. il utilise donc un [`ContentView`](xref:Xamarin.Forms.ContentView) avec un [`Padding`](xref:Xamarin.Forms.Layout.Padding) paramètre pour simuler une marge.

[![Capture d’écran triple de la taille estimée de la police](images/ch05fg07-small.png "Ajuster le texte à la taille disponible")](images/ch05fg07-large.png#lightbox "Ajuster le texte à la taille disponible")

## <a name="a-fit-to-size-clock"></a>Une horloge adaptée à la taille

L’exemple [**FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) illustre l’utilisation [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) de pour démarrer un minuteur qui notifie périodiquement l’application lorsqu’il est temps de mettre à jour l’horloge. La taille de police est définie sur un sixième de la largeur de la page pour que l’affichage soit le plus grand possible.

## <a name="accessibility-issues"></a>Problèmes d’accessibilité

Le programme **EstimatedFontSize** et le programme **FitToSizeClock** contiennent tous les deux un défaut subtil : si l’utilisateur modifie les paramètres d’accessibilité du téléphone sur Android ou Windows 10 mobile, le programme ne peut plus estimer la taille du texte en fonction de la taille de la police. L’exemple [**AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) illustre ce problème.

## <a name="empirically-fitting-text"></a>Ajuster le texte de façon empirique

Une autre façon de faire tenir le texte dans un rectangle est de calculer de manière empirique la taille du texte rendu et de l’ajuster. Le programme du livre appelle [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) sur un élément visuel pour obtenir la taille souhaitée de l’élément. Cette méthode est dépréciée et les programmes doivent à la place appeler [ `Measure` ] (XREF : Xamarin.Forms . VisualElement. Measure (System. double, System. double, Xamarin.Forms . MeasureFlags)).

Pour un `Label` , le premier argument doit être la largeur du conteneur (pour permettre l’encapsulation), tandis que le deuxième argument doit avoir la valeur `Double.PositiveInfinity` pour rendre la hauteur sans contrainte. L’exemple [**EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) illustre cette technique.

## <a name="related-links"></a>Liens connexes

- [Chapitre 5 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemples du chapitre 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Exemples F # du chapitre 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
