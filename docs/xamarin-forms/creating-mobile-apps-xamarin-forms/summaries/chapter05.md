---
title: Résumé du chapitre 5. Gestion des tailles
description: 'Création d’Mobile Apps avec Xamarin. Forms : Résumé du chapitre 5. Gestion des tailles'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771139"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Résumé du chapitre 5. Gestion des tailles

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Notes sur cette page indiquent des zones où Xamarin.Forms est différente de la matière présentée dans le livre.

Plusieurs tailles de Xamarin.Forms ont été rencontrées jusqu'à présent :

- La hauteur de la barre d’état iOS est 20
- Le `BoxView` a une largeur par défaut et une hauteur de 40
- La valeur par défaut `Padding` dans un `Frame` est 20
- La valeur par défaut `Spacing` sur le `StackLayout` est 6
- Le `Device.GetNamedSize` méthode retourne une taille de police numérique

Ces tailles ne sont pas des pixels. Au lieu de cela, ils sont des unités indépendantes du périphérique reconnues indépendamment par chaque plateforme.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, points, points de distribution, adresses IP dynamiques et DIUs

Au début de l’historique du Mac d’Apple et Microsoft Windows, les programmeurs a travaillé en unités de pixels. Toutefois, l’arrivée de résolution supérieure affiche requis une approche abstraite et plus virtualisée aux coordonnées d’écran. Dans l’univers Mac, les programmeurs travaillé en unités de *points*, traditionnellement 1/72ème de pouce, les développeurs Windows utilisé lors de la *unités indépendantes du périphérique* (DIUs) en fonction de 1/96ème de pouce.

Toutefois, les appareils mobiles sont généralement conservées plus proches de la face et ont une résolution supérieure à desktop écrans, ce qui implique qu’une densité de pixels supérieure peut être tolérée.

Les programmeurs ciblant les appareils iPhone et iPad d’Apple continuent de fonctionner en unités de *points*, mais il existe 160 de ces points par pouce. Selon l’appareil, il peut être 1, 2 ou 3 pixels vers le point.

Android est similaire. Les programmeurs traitent en unités de *pixels indépendants de densité* (dps), et la relation entre les points de distribution et de pixels est basée sur 160 DP par pouce.

Les téléphones Windows et les appareils mobiles ont également établi des facteurs d’échelle qui impliquent quelque chose proche de 160 unités indépendantes du périphérique par pouce.

> [!NOTE]
> Xamarin.Forms ne gère plus n’importe quel téléphone basée sur Windows ou un appareil mobile.

En résumé, un programmeur Xamarin.Forms ciblant les téléphones et tablettes peut supposer que toutes les unités de mesure sont basées sur le critère suivant :

- 160 unités par pouce, équivalent à
- 64 unités pour le centimètre

En lecture seule [ `Width` ](xref:Xamarin.Forms.VisualElement.Width) et [ `Height` ](xref:Xamarin.Forms.VisualElement.Height) propriétés définies par `VisualElement` ont par défaut les valeurs de « simuler » &ndash;1. Uniquement lorsqu’un élément a été dimensionné et prise en charge dans la disposition de ces propriétés reflètent la taille réelle de l’élément dans les unités indépendantes du périphérique. Cette taille inclut au moins un `Padding` défini sur l’élément, mais pas le `Margin`.

Un élément visuel se déclenche le [ `SizeChanged` ](xref:Xamarin.Forms.VisualElement.SizeChanged) événement lors de son `Width` ou `Height` a changé. Le [ **WhatSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) exemple utilise cet événement pour afficher la taille de l’écran de programme.

## <a name="metrical-sizes"></a>Tailles SURMONTANT

Le [ **MetricalBoxView** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) utilise [ `WidthRequest` ](xref:Xamarin.Forms.VisualElement.WidthRequest) et [ `HeightRequest` ](xref:Xamarin.Forms.VisualElement.HeightRequest) pour afficher un `BoxView` un pouce en hauteur et l’autre centimètre large.

## <a name="estimated-font-sizes"></a>Tailles de police estimé

Le [ **tailles de police** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) exemple montre comment utiliser la règle de 160-unités-à-the-pouce pour spécifier des tailles de police en unités de points. La cohérence visuelle entre les plateformes à l’aide de cette technique est meilleure que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Ajustement du texte à la taille disponible

Il est possible d’adapter un bloc de texte dans un rectangle particulier en calculant un `FontSize` de la `Label` selon les critères suivants :

- Interligne est de 120 % de la taille de police (130 % sur les plateformes Windows).
- Largeur de caractère moyenne est de 50 % de la taille de police.

Le [ **EstimatedFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) exemple illustre cette technique. Ce programme a été écrit avant la [ `Margin` ](xref:Xamarin.Forms.View.Margin) propriété était disponible, de sorte qu’il utilise un [ `ContentView` ](xref:Xamarin.Forms.ContentView) avec un [ `Padding` ](xref:Xamarin.Forms.Layout.Padding) paramètre pour simuler un marge.

[![Capture d’écran triple de taille de police estimé](images/ch05fg07-small.png "texte ajuster à la taille disponible")](images/ch05fg07-large.png#lightbox "texte ajuster à la taille disponible")

## <a name="a-fit-to-size-clock"></a>Une horloge de la taille de l’ajustement

Le [ **FitToSizeClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) exemple montre comment utiliser [ `Device.StartTimer` ](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) à démarrer un minuteur qui indique périodiquement à l’application lorsqu’il est temps pour mettre à jour de l’horloge. La taille de police est définie à un sixième de la largeur de page pour rendre l’affichage aussi grand que possible.

## <a name="accessibility-issues"></a>Problèmes d’accessibilité

Le programme **EstimatedFontSize** et le programme **FitToSizeClock** contiennent tous les deux un défaut subtil : Si l’utilisateur modifie les paramètres d’accessibilité du téléphone sur Android ou Windows 10 mobile, le programme ne peut plus estimer l’ampleur du rendu du texte en fonction de la taille de la police. Le [ **AccessibilityTest** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) exemple illustre ce problème.

## <a name="empirically-fitting-text"></a>Ajustement empirique de texte

Un autre en fonction du texte à un rectangle consiste à empirique calculer la taille du rendu de texte et l’ajuster vers le haut ou vers le bas. Le programme dans les appels de livre [ `GetSizeRequest` ](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) sur un élément visuel pour obtenir la taille souhaitée de l’élément. Méthode a été déconseillée et les programmes doivent appeler à la place [ `Measure` ](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags)).

Pour un `Label`, le premier argument doit être de la largeur du conteneur (pour permettre le retour à la ligne), tandis que le second argument doit être défini à `Double.PositiveInfinity` pour que la hauteur sans contrainte. Le [ **EmpiricalFontSize** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) exemple illustre cette technique.

## <a name="related-links"></a>Liens connexes

- [Chapitre 5 de texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Exemples de chapitre 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Chapitre 5 F# exemples](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
