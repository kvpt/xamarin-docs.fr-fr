---
title: 'Xamarin.FormsShapes : syntaxe de balisage de chemin'
description: Xamarin.FormsLa syntaxe de balisage Path vous permet de spécifier de manière compacte des géométries de chemin d’accès en XAML.
ms.prod: xamarin
ms.assetid: A2C1BD59-1A16-4E26-A825-0338E2AF9E65
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/19/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: d2eb0ac68bf754e45ffcd2a1c77e4347bb3bcf4c
ms.sourcegitcommit: 8f6cc5208f675c8cfb645bd9ffb0fc1f8ea71411
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85326208"
---
# <a name="xamarinforms-shapes-path-markup-syntax"></a>Xamarin.FormsShapes : syntaxe de balisage de chemin

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Xamarin.Formsla syntaxe de balisage Path vous permet de spécifier de manière compacte des géométries de chemin d’accès en XAML. La syntaxe est spécifiée sous la forme d’une valeur de chaîne pour la `Path.Data` propriété :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Data="M13.908992,16.207977 L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983Z" />
```

La syntaxe de balisage de chemin d’accès est composée d’une `FillRule` valeur facultative, et d’une ou de plusieurs descriptions de figure. Cette syntaxe peut être exprimée comme suit : `<Path Data="` *[fillRule]* *figureDescription* *[figureDescription]* * `" ... />`

Dans cette syntaxe :

- *fillRule* est un facultatif `Xamarin.Forms.Shapes.FillRule` qui spécifie si la géométrie doit utiliser `EvenOdd` ou `Nonzero` `FillRule` . `F0`spécifie la `EvenOdd` règle de remplissage, tandis que `F1` spécifie la `Nonzero` règle de remplissage. Pour plus d’informations sur les règles de remplissage, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).
-  *figureDescription* représente une figure composée d’une commande Move, de commandes de dessin et d’une commande Close facultative. Une commande Move spécifie le point de départ de la figure. Les commandes de dessin décrivent le contenu de la figure, et la commande facultative Close ferme la figure.

Dans l’exemple ci-dessus, la syntaxe de balisage Path spécifie un point de départ à l’aide de la commande Move ( `M` ), une série de lignes droites à l’aide de la commande line ( `L` ) et ferme le chemin d’accès avec la commande Close ( `Z` ).

Dans la syntaxe de balisage Path, les espaces ne sont pas requis avant ou après les commandes. En outre, deux nombres n’ont pas besoin d’être séparés par une virgule ou un espace blanc, mais cela n’est possible que lorsque la chaîne n’est pas ambiguë.

> [!TIP]
> Path Markup Language utilise une syntaxe compatible avec les définitions de chemin d’images SVG (Scalable Vector Graphics). il peut donc être utile pour le portage de graphiques au format SVG.

## <a name="move-command"></a>Commande de déplacement

La commande Move spécifie le point de départ d’une nouvelle figure. La syntaxe de cette commande est : `M` *StartPoint* ou `m` *StartPoint*.

Dans cette syntaxe, *StartPoint* est une [`Point`](xref:Xamarin.Forms.Point) structure qui spécifie le point de départ d’une nouvelle figure. Si vous répertoriez plusieurs points après la commande Move, une ligne est dessinée à ces points.

`M 10,10`est un exemple de commande Move valide.

## <a name="draw-commands"></a>Commandes de dessin

Une commande draw peut se composer de plusieurs commandes shape. Les commandes de dessin suivantes sont disponibles :

- Line ( `L` ou `l` ).
- Ligne horizontale ( `H` ou `h` ).
- Ligne verticale ( `V` ou `v` ).
- Courbe de Bézier cubique ( `C` ou `c` ).
- Courbe de Bézier quadratique ( `Q` ou `q` ).
- Courbe de Bézier cubique lissée ( `S` ou `s` ).
- Courbe de Bézier quadratique lisse ( `T` ou `t` ).
- Arc elliptique ( `A` ou `a` ).

Chaque commande de dessin est spécifiée avec une lettre majuscule ou minuscule. Lorsque vous entrez séquentiellement plusieurs commandes du même type, vous pouvez omettre l’entrée de commande en double. Par exemple `L 100,200 300,400` , équivaut à `L 100, 200 L 300,400` .

### <a name="line-command"></a>Commande de ligne

La commande line crée une ligne droite entre le point actuel et le point de terminaison spécifié. La syntaxe de cette commande est : `L` *point* de terminaison ou `l` *point de terminaison*.

Dans cette syntaxe, *Endpoint* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le point de terminaison de la ligne.

`L 20,30`et `L 20 30` sont des exemples de commandes de ligne valides.

### <a name="horizontal-line-command"></a>Commande de ligne horizontale

La commande de ligne horizontale crée une ligne horizontale entre le point actuel et la coordonnée x spécifiée. La syntaxe de cette commande est la suivante : `H` *x* ou `h` *x*.

Dans cette syntaxe, *x* est un `double` qui représente la coordonnée x du point de terminaison de la ligne.

`H 90` est un exemple de commande horizontal line valide.

### <a name="vertical-line-command"></a>Commande de ligne verticale

La commande de ligne verticale crée une ligne verticale entre le point actuel et la coordonnée y spécifiée. La syntaxe de cette commande est : `V` *y* ou `v` *y*.

Dans cette syntaxe, *y* est un `double` qui représente la coordonnée y du point de terminaison de la ligne.

`V 90` est un exemple de commande vertical line valide.

### <a name="cubic-bezier-curve-command"></a>Commande de courbe de Bézier cubique

La commande de courbe de Bézier cubique crée une courbe de Bézier cubique entre le point actuel et le point de terminaison spécifié à l’aide des deux points de contrôle spécifiés. La syntaxe de cette commande est : `C` *ControlPoint1* *ControlPoint2* *Endpoint* ou `c` *ControlPoint1* *ControlPoint2* *Endpoint*.

Dans cette syntaxe :

- *ControlPoint1* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le premier point de contrôle de la courbe, qui détermine la tangente de départ de la courbe.
- *ControlPoint2* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le deuxième point de contrôle de la courbe, qui détermine la tangente de fin de la courbe.
- *Endpoint* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le point vers lequel la courbe est dessinée.

`C 100,200 200,400 300,200`est un exemple de commande de courbe de Bézier cubique valide.

### <a name="quadratic-bezier-curve-command"></a>Commande de courbe de Bézier quadratique

La commande de courbe de Bézier quadratique crée une courbe de Bézier quadratique entre le point actuel et le point de terminaison spécifié à l’aide du point de contrôle spécifié. La syntaxe de cette commande est : le point de terminaison `Q` *ControlPoint* *endPoint* ou le point de `q` *controlPoint* *terminaison*ControlPoint.

Dans cette syntaxe :

- *ControlPoint* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le point de contrôle de la courbe, qui détermine les tangentes de début et de fin de la courbe.
- *Endpoint* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le point vers lequel la courbe est dessinée.

`Q 100,200 300,200` est un exemple de commande quadratic Bezier curve valide.

### <a name="smooth-cubic-bezier-curve-command"></a>Commande lisser la courbe de Bézier cubique

La commande lisser la courbe de Bézier cubique crée une courbe de Bézier cubique entre le point actuel et le point de terminaison spécifié à l’aide du point de contrôle spécifié. La syntaxe de cette commande est : `S` *controlPoint2* *point* de terminaison ControlPoint2 ou point de `s` *controlPoint2* *terminaison*ControlPoint2.  

Dans cette syntaxe :

- *ControlPoint2* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le deuxième point de contrôle de la courbe, qui détermine la tangente de fin de la courbe.
- *Endpoint* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le point vers lequel la courbe est dessinée.

Le premier point de contrôle est supposé être la réflexion du deuxième point de contrôle de la commande précédente, par rapport au point actuel. S’il n’existe pas de commande précédente, ou si la commande précédente n’était pas une commande de courbe de Bézier cubique ou une commande de courbe de Bézier lissée lissée, le premier point de contrôle est supposé coïncider avec le point actuel.

`S 100,200 200,300`est un exemple de commande de courbe de Bézier cubique lissé valide.

### <a name="smooth-quadratic-bezier-curve-command"></a>Commande de courbe de Bézier lisse quadratique

La commande de courbe de Bézier lisse quadratique crée une courbe de Bézier quadratique entre le point actuel et le point de terminaison spécifié à l’aide d’un point de contrôle. La syntaxe de cette commande est : `T` *point* de terminaison ou `t` *point de terminaison*.

Dans cette syntaxe, *Endpoint* est un [`Point`](xref:Xamarin.Forms.Point) qui représente le point vers lequel la courbe est dessinée.

Le point de contrôle est supposé être la réflexion du point de contrôle de la commande précédente par rapport au point actuel. S’il n’existe pas de commande précédente ou si la commande précédente n’était pas une courbe de Bézier quadratique ou une commande de courbe de Bézier lisse quadratique, le point de contrôle est supposé coïncider avec le point actuel.

`T 100,30`est un exemple de commande de courbe de Bézier cubique lissée valide.

### <a name="elliptical-arc-command"></a>Commande d’arc elliptique

La commande elliptique arc crée un arc elliptique entre le point actuel et le point de terminaison spécifié. La syntaxe de cette commande est la suivante : `A` *size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *Endpoint* ou `a` *Size* *rotationAngle* *isLargeArcFlag* *sweepDirectionFlag* *Endpoint*.

Dans cette syntaxe :

- `size`est un [`Size`](xref:Xamarin.Forms.Size) qui représente le rayon x et le rayon y de l’arc.
- `rotationAngle`est un `double` qui représente la rotation de l’ellipse, en degrés.
- `isLargeArcFlag`doit avoir la valeur 1 si l’angle de l’ARC doit être égal ou supérieur à 180 degrés ; sinon, affectez-lui la valeur 0.
- `sweepDirectionFlag`doit avoir la valeur 1 si l’arc est dessiné dans une direction d’angle positif ; sinon, affectez-lui la valeur 0.
- `endPoint`est un [`Point`](xref:Xamarin.Forms.Point) vers lequel l’arc est dessiné.

`A150,150 0 1,0 150,-150`est un exemple de commande d’arc elliptique valide.

## <a name="close-command"></a>Bouton de fermeture

La commande fermer met fin à la figure actuelle et crée une ligne qui relie le point actuel au point de départ de la figure. Par conséquent, cette commande crée une jointure de ligne entre le dernier segment et le premier segment de la figure.

La syntaxe de la commande Close est : `Z` ou `z` .

## <a name="additional-values"></a>Valeurs supplémentaires

Au lieu d’une valeur numérique standard, vous pouvez également utiliser les valeurs spéciales suivantes qui respectent la casse :

- `Infinity`représente `double.PositiveInfinity` .
- `-Infinity`représente `double.NegativeInfinity` .
- `NaN`représente `double.NaN` .

En outre, vous pouvez également utiliser la notation scientifique ne respectant pas la casse. Par conséquent, `+1.e17` est une valeur valide.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsGéométries](geometries.md)
- [Xamarin.FormsFormes : règles de remplissage](fillrules.md)
