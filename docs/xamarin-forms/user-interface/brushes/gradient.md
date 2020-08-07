---
title: 'Xamarin.FormsPinceaux : dégradés'
description: La Xamarin.Forms classe GradientBrush est une classe abstraite qui décrit un dégradé composé de points de dégradé.
ms.prod: xamarin
ms.assetid: 24763E56-74EC-4082-897B-E4EAACCADFEE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/27/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 6b6fe37fda3fd54b4df5cb6f5dacce0d910ecb3f
ms.sourcegitcommit: 08290d004d1a7e7ac579bf1f96abf8437921dc70
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87919017"
---
# <a name="no-locxamarinforms-brushes-gradients"></a>Xamarin.FormsPinceaux : dégradés

![API d’aperçu](~/media/shared/preview.png "Cette API est actuellement en préversion.")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)

La `GradientBrush` classe dérive de la `Brush` classe et est une classe abstraite qui décrit un dégradé, qui est composé de points de dégradé. Un pinceau de dégradé peint une zone avec plusieurs couleurs qui se mélangent le long d’un axe. Les classes qui dérivent de `GradientBrush` décrivent les différents modes d’interprétation des arrêts de dégradé et Xamarin.Forms fournissent les pinceaux de dégradé suivants :

- `LinearGradientBrush`, qui peint une zone avec un dégradé linéaire. Pour plus d’informations, consultez [ Xamarin.Forms pinceaux : dégradés linéaires](lineargradient.md).
- `RadialGradientBrush`, qui peint une zone avec un dégradé radial. Pour plus d’informations, consultez [ Xamarin.Forms pinceaux : dégradés radiaux](radialgradient.md).

La `GradientBrush` classe définit la `GradientStops` propriété, de type `GradientStopsCollection` , qui représente les points de dégradé du pinceau, chacun spécifiant une couleur et un décalage le long de l’axe du dégradé du pinceau. Un `GradientStopsCollection` est un `ObservableCollection` `GradientStop` objet d’objets. La `GradientStops` propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

> [!NOTE]
> La `GradientStops` propriété est du `ContentProperty` de la `GradientBrush` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

## <a name="gradient-stops"></a>Points de dégradé

Les points de dégradé sont les blocs de construction d’un pinceau de dégradé et spécifient les couleurs du dégradé et leur emplacement le long de l’axe du dégradé. Les points de dégradé sont spécifiés à l’aide d' `GradientStop` objets.

La `GradientStop` classe définit les propriétés suivantes :

- `Color`, de type [`Color`](xref:Xamarin.Forms.Color) , qui représente la couleur du point de dégradé. La valeur par défaut de cette propriété est `Color.Default`.
- `Offset`, de type `float` , qui représente l’emplacement du point de dégradé dans le vecteur de dégradé. La valeur par défaut de cette propriété est 0, et les valeurs valides sont comprises dans la plage 0.0-1.0. Plus cette valeur est proche de 0, plus la couleur est proche du début du dégradé. De même, plus cette valeur est proche de 1, plus la couleur est proche de la fin du dégradé.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!IMPORTANT]
> Le système de coordonnées utilisé par les dégradés est relatif à un cadre englobant pour la zone de sortie. 0 indique 0 pour cent de la zone englobante, et 1 indique 100 pour cent de la zone englobante. Par conséquent, (0.5, 0.5) décrit un point au milieu du rectangle englobant, et (1,1) décrit un point dans le coin inférieur droit du cadre englobant.

L’exemple de code XAML suivant crée une diagonale `LinearGradientBrush` avec quatre couleurs :

```xaml
<LinearGradientBrush StartPoint="0,0"
                     EndPoint="1,1">
    <GradientStop Color="Yellow"
                  Offset="0.0" />
    <GradientStop Color="Red"
                  Offset="0.25" />
    <GradientStop Color="Blue"
                  Offset="0.75" />             
    <GradientStop Color="LimeGreen"
                  Offset="1.0" />
</LinearGradientBrush>                                                       
```

La couleur de chaque point entre les points de dégradé est interpolée comme une combinaison de la couleur spécifiée par les deux points de dégradé englobants. Le diagramme suivant montre les points de dégradé de l’exemple précédent :

![Cadre peint avec un LinearGradientBrush Diagonal](gradient-images/gradient-stops.png)

Dans ce diagramme, les cercles marquent la position des points de dégradé et la ligne en pointillés affiche l’axe du dégradé. Le premier point de dégradé spécifie la couleur jaune à un décalage de 0,0. Le deuxième point de dégradé spécifie la couleur rouge à un décalage de 0,25. Les points entre ces deux points de dégradé passent progressivement du jaune au rouge lorsque vous vous déplacez de la gauche vers la droite le long de l’axe du dégradé. Le troisième point de dégradé spécifie la couleur bleue à un décalage de 0,75. Les points situés entre le deuxième et le troisième point de dégradé passent progressivement du rouge au bleu. Le quatrième point de dégradé spécifie la couleur vert citron à au décalage de 1,0. Les points situés entre le troisième et le quatrième point de dégradé passent progressivement du bleu au vert citron.

## <a name="related-links"></a>Liens connexes

- [BrushesDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-brushdemos/)
- [Xamarin.FormsPinceaux : dégradés linéaires](lineargradient.md)
- [Xamarin.FormsPinceaux : dégradés radiaux](radialgradient.md)
