---
title: Résumé du chapitre 5. Gestion des tailles
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 5. Gestion des tailles'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 486800E9-C09F-4B95-9AC2-C0F8FE563BCF
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: c082bdb10732e42b37511cf050e50f46990a5b5b
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771139"
---
# <a name="summary-of-chapter-5-dealing-with-sizes"></a>Résumé du chapitre 5. Gestion des tailles

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)

> [!NOTE]
> Les notes sur cette page indiquent les zones où Xamarin.Forms a divergé du matériel présenté dans le livre.

Plusieurs tailles dans Xamarin.Forms ont été rencontrées jusqu’à présent:

- La hauteur de la barre de statut iOS est de 20
- Le `BoxView` a une largeur par défaut et la hauteur de 40
- La `Padding` valeur `Frame` par défaut dans un est de 20
- La `Spacing` valeur `StackLayout` par défaut sur le est 6
- La `Device.GetNamedSize` méthode renvoie une taille de police numérique

Ces tailles ne sont pas des pixels. Au lieu de cela, il s’œd de chaque plate-forme qui est reconnue indépendamment.

## <a name="pixels-points-dps-dips-and-dius"></a>Pixels, points, dpes, PPER et DCU

Au début de l’histoire de l’Apple Mac et Microsoft Windows, les programmeurs ont travaillé dans des unités de pixels. Cependant, l’avènement d’écrans à haute résolution nécessitait une approche plus virtualisée et abstraite des coordonnées d’écran. Dans le monde Mac, les programmeurs ont travaillé dans des unités de *points,* traditionnellement 1/72 pouce, tandis que les développeurs Windows ont utilisé des *unités indépendantes de l’appareil* (DIU) basée sur 1/96 pouce.

Les appareils mobiles, cependant, sont généralement tenus beaucoup plus près du visage et ont une résolution plus élevée que les écrans de bureau, ce qui implique qu’une plus grande densité de pixels peut être tolérée.

Les programmeurs ciblant les appareils Apple iPhone et iPad continuent de fonctionner en unités de *points,* mais il ya 160 de ces points à la pouce. Selon l’appareil, il peut y avoir 1, 2 ou 3 pixels au point.

Android est similaire. Les programmeurs travaillent dans des unités de *pixels indépendants* de densité (dps), et la relation entre les dps et les pixels est basée sur 160 dps au pouce.

Les téléphones Windows et les appareils mobiles ont également établi des facteurs d’échelle qui impliquent quelque chose de près de 160 unités indépendantes de l’appareil à la pouce.

> [!NOTE]
> Xamarin.Forms ne prend plus en charge aucun téléphone ou appareil mobile windows.

En résumé, un programmeur Xamarin.Forms ciblant les téléphones et les tablettes peut supposer que toutes les unités de mesure sont basées sur le critère suivant :

- 160 unités au pouce, équivalente à
- 64 unités au centimètre

La lecture [`Width`](xref:Xamarin.Forms.VisualElement.Width) seulement [`Height`](xref:Xamarin.Forms.VisualElement.Height) et `VisualElement` les propriétés définies &ndash;par ont par défaut "mock" valeurs de 1. Ce n’est que lorsqu’un élément a été dimensionné et logé dans la mise en page que ces propriétés refléteront la taille réelle de l’élément dans les unités indépendantes de l’appareil. Cette taille `Padding` comprend n’importe quel `Margin`ensemble sur l’élément mais pas le .

Un élément visuel [`SizeChanged`](xref:Xamarin.Forms.VisualElement.SizeChanged) déclenche `Width` l’événement lorsque son ou `Height` a changé. [**L’échantillon WhatSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/WhatSize) utilise cet événement pour afficher la taille de l’écran du programme.

## <a name="metrical-sizes"></a>Tailles métriques

Le [**MetricalBoxView**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/MetricalBoxView) utilise [`WidthRequest`](xref:Xamarin.Forms.VisualElement.WidthRequest) et [`HeightRequest`](xref:Xamarin.Forms.VisualElement.HeightRequest) pour afficher un `BoxView` pouce de haut et un centimètre de large.

## <a name="estimated-font-sizes"></a>Tailles estimées de police

[**L’échantillon de FontSizes**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FontSizes) montre comment utiliser la règle de 160 unités jusqu’au pouce pour spécifier la taille des polices dans les unités de points. La cohérence visuelle entre les plates-formes utilisant cette technique est meilleure que `Device.GetNamedSize`.

## <a name="fitting-text-to-available-size"></a>Montage du texte à la taille disponible

Il est possible d’adapter un bloc de texte `FontSize` dans `Label` un rectangle particulier en calculant un des critères suivant :

- L’espacement de ligne est de 120% de la taille de la police (130% sur les plates-formes Windows).
- La largeur moyenne du caractère est de 50 % de la taille de la police.

[**L’échantillon EstimatedFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EstimatedFontSize) démontre cette technique. Ce programme a [`Margin`](xref:Xamarin.Forms.View.Margin) été écrit avant que [`ContentView`](xref:Xamarin.Forms.ContentView) la [`Padding`](xref:Xamarin.Forms.Layout.Padding) propriété était disponible, de sorte qu’il utilise un avec un réglage pour simuler une marge.

[![Triple capture d’écran de la taille estimée de la police](images/ch05fg07-small.png "Texte adapté à la taille disponible")](images/ch05fg07-large.png#lightbox "Texte adapté à la taille disponible")

## <a name="a-fit-to-size-clock"></a>Une horloge adaptée à la taille

[**L’échantillon FitToSizeClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FitToSizeClock) [`Device.StartTimer`](xref:Xamarin.Forms.Device.StartTimer(System.TimeSpan,System.Func{System.Boolean})) démontre l’utilisation d’une minuterie qui avertit périodiquement l’application quand il est temps de mettre à jour l’horloge. La taille de la police est réglée à un sixième de la largeur de la page pour rendre l’affichage aussi grand que possible.

## <a name="accessibility-issues"></a>Questions d’accessibilité

Le programme **EstimatedFontSize** et le programme **FitToSizeClock** contiennent tous deux un défaut subtil : si l’utilisateur modifie les paramètres d’accessibilité du téléphone sur Android ou Windows 10 Mobile, le programme ne peut plus estimer l’ampleur du texte en fonction de la taille de la police. [**L’échantillon AccessibilityTest**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/AccessibilityTest) démontre ce problème.

## <a name="empirically-fitting-text"></a>Texte empiriquement ajusté

Une autre façon d’adapter le texte à un rectangle est de calculer empiriquement la taille du texte rendu et de l’ajuster vers le haut ou vers le bas. Le programme du [`GetSizeRequest`](xref:Xamarin.Forms.VisualElement.GetSizeRequest(System.Double,System.Double)) livre fait appel à un élément visuel pour obtenir la taille désirée de l’élément. Cette méthode a été dépréciée, [`Measure`](xref:Xamarin.Forms.VisualElement.Measure(System.Double,System.Double,Xamarin.Forms.MeasureFlags))et les programmes devraient plutôt appeler .

Pour `Label`un , le premier argument devrait être la largeur du récipient (pour `Double.PositiveInfinity` permettre l’emballage), tandis que le deuxième argument doit être réglé pour rendre la hauteur sans contrainte. [**L’échantillon EmpiricalFontSize**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/EmpiricalFontSize) démontre cette technique.

## <a name="related-links"></a>Liens connexes

- [Chapitre 5 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch05-Apr2016.pdf)
- [Échantillons du chapitre 5](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05)
- [Échantillons du chapitre 5 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter05/FS)
