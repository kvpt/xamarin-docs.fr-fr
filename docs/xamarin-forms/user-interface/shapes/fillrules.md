---
title: 'Xamarin.Forms Formes : règles de remplissage'
description: Xamarin.Forms Les règles de remplissage de formes déterminent si un point se trouve dans la région de remplissage d’une forme.
ms.prod: xamarin
ms.assetid: 5CABB22B-C6BE-43D1-91D9-6E90A4BD5622
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: e78acc88c9f159fdeb797663f1c90283ed0d803b
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939782"
---
# <a name="no-locxamarinforms-shapes-fill-rules"></a>Xamarin.Forms Formes : règles de remplissage

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

Plusieurs Xamarin.Forms classes Shapes ont `FillRule` des propriétés, de type `FillRule` . Il s’agit notamment de `Polygon` , `Polyline` et `GeometryGroup` .

L' `FillRule` énumération définit les `EvenOdd` `Nonzero` membres et. Chaque membre représente une règle différente pour déterminer si un point se trouve dans la région de remplissage d’une forme.

> [!IMPORTANT]
> Toutes les formes sont considérées comme fermées dans le cadre des règles de remplissage.

## <a name="evenodd"></a>EvenOdd

La `EvenOdd` règle de remplissage dessine un rayon du point vers l’infini dans n’importe quelle direction et compte le nombre de segments de la forme que le rayon traverse. Si ce nombre est impair, le point est à l’intérieur de. Si ce nombre est pair, le point est à l’extérieur de.

L’exemple XAML suivant crée et restitue une forme composite, avec `FillRule` comme valeur par défaut `EvenOdd` :

```xaml
<Path Stroke="Black"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <!-- FillRule doesn't need to be set, because EvenOdd is the default. -->
        <GeometryGroup>
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

Dans cet exemple, une forme composite composée d’une série d’anneaux concentriques s’affiche :

![Forme composite avec une règle de remplissage EvenOdd](fillrule-images/evenodd.png "Forme composite avec une règle de remplissage EvenOdd")

Dans la forme composite, Notez que le centre et le troisième anneau ne sont pas remplis. Cela est dû au fait qu’un rayon dessiné à partir de n’importe quel point dans l’un de ces deux anneaux traverse un nombre pair de segments :

![Forme composite annotée avec une règle de remplissage EvenOdd](fillrule-images/evenodd-annotated.png "Forme composite annotée avec une règle de remplissage EvenOdd")

Dans l’image ci-dessus, les cercles rouges représentent des points et les lignes représentent des rayons arbitraires. Pour le point supérieur, les deux rayons arbitraires passent par un nombre pair de segments de ligne. Par conséquent, la sonnerie dans laquelle se trouve le point n’est pas remplie. Pour le point inférieur, les deux rayons arbitraires passent par un nombre impair de segments de ligne. Par conséquent, l’anneau dans lequel se trouve le point est rempli.

## <a name="nonzero"></a>Différente

La `Nonzero` règle de remplissage dessine un rayon du point vers l’infini dans n’importe quelle direction, puis examine les emplacements où un segment de la forme croise le rayon. En commençant par un nombre égal à zéro, le nombre est incrémenté chaque fois qu’un segment croise le rayon de gauche à droite et décrémenté chaque fois qu’un segment croise le rayon de droite à gauche. Après avoir compté les intersections, si le résultat est égal à zéro, le point est à l’extérieur du polygone. Dans le cas contraire, elle est à l’intérieur.

L’exemple XAML suivant crée et restitue une forme composite, avec le `FillRule` défini sur `Nonzero` :

```xaml
<Path Stroke="Black"
      Fill="#CCCCFF"
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <GeometryGroup FillRule="Nonzero">
            <EllipseGeometry RadiusX="50"
                             RadiusY="50"
                             Center="75,75" />
            <EllipseGeometry RadiusX="70"
                             RadiusY="70"
                             Center="75,75" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="75,75" />
            <EllipseGeometry RadiusX="120"
                             RadiusY="120"
                             Center="75,75" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

Dans cet exemple, une forme composite composée d’une série d’anneaux concentriques s’affiche :

![Forme composite avec une règle de remplissage différente de zéro](fillrule-images/nonzero.png "Forme composite avec une règle de remplissage différente de zéro")

Dans la forme composite, Notez que tous les anneaux sont remplis. Cela est dû au fait que tous les segments s’exécutent dans la même direction, et qu’un rayon dessiné à partir de n’importe quel point franchit un ou plusieurs segments et que la somme des croisements n’est pas égale à zéro :

![Forme composite annotée avec une règle de remplissage différente de zéro](fillrule-images/nonzero-annotated.png "Forme composite annotée avec une règle de remplissage différente de zéro")

Dans l’image ci-dessus, les flèches rouges représentent la direction dans laquelle les segments sont dessinés, tandis que la flèche noire représente un rayon arbitraire exécuté à partir d’un point dans l’anneau le plus profond. En commençant par une valeur égale à zéro, pour chaque segment que le rayon traverse, une valeur de un est ajoutée car le segment traverse le rayon de gauche à droite.

Une forme plus complexe avec des segments exécutés dans des directions différentes est nécessaire pour mieux illustrer le comportement de la `Nonzero` règle de remplissage. L’exemple de code XAML suivant crée une forme similaire à l’exemple précédent, sauf qu’il est créé avec un `PathGeometry` plutôt qu’un `EllipseGeometry` :

```xaml
<Path Stroke="Black"
      Fill="#CCCCFF">
     <Path.Data>
         <GeometryGroup FillRule="Nonzero">
             <PathGeometry>
                 <PathGeometry.Figures>
                     <!-- Inner ring -->
                     <PathFigure StartPoint="120,120">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="50,50"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,120" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Second ring -->
                     <PathFigure StartPoint="120,100">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="70,70"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,100" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Third ring  -->
                         <PathFigure StartPoint="120,70">
                         <PathFigure.Segments>
                             <PathSegmentCollection>
                                 <ArcSegment Size="100,100"
                                             IsLargeArc="True"
                                             SweepDirection="CounterClockwise"
                                             Point="140,70" />
                             </PathSegmentCollection>
                         </PathFigure.Segments>
                     </PathFigure>

                     <!-- Outer ring -->
                     <PathFigure StartPoint="120,300">
                         <PathFigure.Segments>
                             <ArcSegment Size="130,130"
                                         IsLargeArc="True"
                                         SweepDirection="Clockwise"
                                         Point="140,300" />
                         </PathFigure.Segments>
                     </PathFigure>
                 </PathGeometry.Figures>
             </PathGeometry>
         </GeometryGroup>
     </Path.Data>
 </Path>
```

Dans cet exemple, une série de segments d’arc sont dessinés, qui ne sont pas fermés :

![Forme composite avec une règle de remplissage différente de zéro](fillrule-images/nonzero-gaps.png "Forme composite avec une règle de remplissage différente de zéro")

Dans l’image ci-dessus, le troisième arc à partir du centre n’est pas rempli. Cela est dû au fait que la somme des valeurs d’un rayon donné traversant les segments dans son chemin d’accès est égale à zéro :

![Forme composite annotée avec une règle de remplissage différente de zéro](fillrule-images/nonzero-gaps-annotated.png "Forme composite annotée avec une règle de remplissage différente de zéro")

Dans l’image ci-dessus, le cercle rouge représente un point, les lignes noires représentent des rayons arbitraires qui sortent du point de la région non remplie, et les flèches rouges représentent la direction dans laquelle les segments sont dessinés. Comme vous pouvez le constater, la somme des valeurs des rayons qui franchissent les segments est égale à zéro :

- Le rayon arbitraire qui se déplace en diagonale droite traverse deux segments qui s’exécutent dans des directions différentes. Par conséquent, les segments sont annulés mutuellement, ce qui donne une valeur de zéro.
- Le rayon arbitraire qui se déplace en diagonale gauche dépasse un total de six segments. Toutefois, les intersections s’annulent mutuellement afin que zéro soit la somme finale.

Une somme de zéro entraîne le non-remplissage de l’anneau.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
