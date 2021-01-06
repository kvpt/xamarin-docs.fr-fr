---
title: 'Xamarin.Forms Formes : géométries'
description: Xamarin.Forms les classes Geometry vous permettent de décrire la géométrie d’une forme 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/28/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f3a89e0c5c49ec790cf35443030d50d3ddef9ed4
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97939808"
---
# <a name="no-locxamarinforms-shapes-geometries"></a>Xamarin.Forms Formes : géométries

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Geometry` classe, et les classes qui dérivent de celle-ci, vous permettent de décrire la géométrie d’une forme 2D. `Geometry` les objets peuvent être simples, tels que des rectangles et des cercles, ou composites, créés à partir de deux objets géométriques ou plus. En outre, il est possible de créer des géométries plus complexes qui incluent des arcs et des courbes.

La `Geometry` classe est la classe parente de plusieurs classes qui définissent différentes catégories de géométries :

- `EllipseGeometry`, qui représente la géométrie d’une ellipse ou d’un cercle.
- `GeometryGroup`, qui représente un conteneur qui peut combiner plusieurs objets Geometry en un seul objet.
- `LineGeometry`, qui représente la géométrie d’une ligne.
- `PathGeometry`, qui représente la géométrie d’une forme complexe qui peut être composée d’arcs, de courbes, d’ellipses, de lignes et de rectangles.
- `RectangleGeometry`, qui représente la géométrie d’un rectangle ou d’un carré.

> [!NOTE]
> Il y a également une `RoundedRectangleGeometry` classe qui dérive de la `GeometryGroup` classe. Pour plus d’informations, consultez [RoundRectangleGeometry](#roundrectanglegeometry).

Les `Geometry` `Shape` classes et semblent similaires, dans le sens où elles décrivent toutes deux les formes 2D, mais présentent une différence importante. La classe `Geometry` dérive de la [`BindableObject`](xref:Xamarin.Forms.BindableObject) classe, tandis que la `Shape` classe dérive de la [`View`](xref:Xamarin.Forms.View) classe. Par conséquent, `Shape` les objets peuvent s’afficher et participer au système de disposition, contrairement aux `Geometry` objets. Bien que `Shape` les objets soient plus facilement utilisables que les `Geometry` objets, `Geometry` les objets sont plus polyvalents. Lorsqu’un `Shape` objet est utilisé pour restituer des graphiques 2D, un `Geometry` objet peut être utilisé pour définir la région géométrique pour les graphiques 2D et définir une région pour le découpage.

Les classes suivantes ont des propriétés qui peuvent être définies sur des `Geometry` objets :

- La `Path` classe utilise un `Geometry` pour décrire son contenu. Vous pouvez restituer un `Geometry` en affectant `Path.Data` à la propriété un `Geometry` objet et en définissant les `Path` Propriétés et de l’objet `Fill` `Stroke` .
- La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe a une `Clip` propriété, de type `Geometry` , qui définit le contour du contenu d’un élément. Lorsque la `Clip` propriété est définie sur un `Geometry` objet, seule la zone située dans la région du `Geometry` sera visible. Pour plus d’informations, consultez [clip avec une géométrie](#clip-with-a-geometry).

Les classes qui dérivent de la `Geometry` classe peuvent être regroupées en trois catégories : géométries simples, géométries de tracés et géométries composites.

## <a name="simple-geometries"></a>Géométries simples

Les classes Geometry simples sont `EllipseGeometry` , `LineGeometry` et `RectangleGeometry` . Ils sont utilisés pour créer des formes géométriques de base, telles que des cercles, des lignes et des rectangles. Ces mêmes formes, ainsi que des formes plus complexes, peuvent être créées à l’aide d’un `PathGeometry` ou en combinant des objets Geometry, mais ces classes fournissent une approche plus simple pour la production de ces formes géométriques de base.

### <a name="ellipsegeometry"></a>EllipseGeometry

Une géométrie d’ellipse représente la géométrie ou une ellipse ou un cercle, et est définie par un point central, un rayon x et un rayon y.

La `EllipseGeometry` classe définit les propriétés suivantes :

- `Center`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point central de la géométrie.
- `RadiusX`, de type `double` , qui représente la valeur de rayon x de la géométrie. La valeur par défaut de cette propriété est 0,0.
- `RadiusY`, de type `double` , qui représente la valeur de rayon y de la géométrie. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

L’exemple suivant montre comment créer et restituer un `EllipseGeometry` dans un `Path` objet :

```xaml
<Path Fill="Blue"
      Stroke="Red">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

Dans cet exemple, le centre de `EllipseGeometry` est défini sur (50, 50) et le rayon x et le rayon y sont tous deux définis sur 50. Cela crée un cercle rouge avec un diamètre de 100 unités indépendantes du périphérique, dont l’intérieur est en bleu peint :

![EllipseGeometry](geometry-images/ellipse.png "EllipseGeometry")

### <a name="linegeometry"></a>LineGeometry

Une géométrie de ligne représente la géométrie d’une ligne et est définie en spécifiant le point de départ de la ligne et le point de terminaison.

La `LineGeometry` classe définit les propriétés suivantes :

- `StartPoint`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de départ de la ligne.
- `EndPoint`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de terminaison de la ligne.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

L’exemple suivant montre comment créer et restituer un `LineGeometry` dans un `Path` objet :

```xaml
<Path Stroke="Black">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

Dans cet exemple, un `LineGeometry` est dessiné de (10, 20) à (100 130) :

![LineGeometry](geometry-images/line.png "LineGeometry")

> [!NOTE]
> La définition `Fill` de la propriété d’un `Path` qui restitue un n’a `LineGeometry` aucun effet, car une ligne n’a pas d’intérieur.

### <a name="rectanglegeometry"></a>RectangleGeometry

Une géométrie rectangle représente la géométrie d’un rectangle ou d’un carré, et est définie avec une `Rect` structure qui spécifie sa position relative, sa hauteur et sa largeur.

La `RectangleGeometry` classe définit la `Rect` propriété, de type `Rect` , qui représente les dimensions du rectangle. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

L’exemple suivant montre comment créer et restituer un `RectangleGeometry` dans un `Path` objet :

```xaml
<Path Fill="Blue"
      Stroke="Red">
  <Path.Data>
    <RectangleGeometry Rect="10,10,150,100" />
  </Path.Data>
</Path>
```

La position et les dimensions du rectangle sont définies par une `Rect` structure. Dans cet exemple, la position est (10, 10), la largeur est 150 et la hauteur est 100 unités indépendantes du périphérique :

![RectangleGeometry](geometry-images/rectangle.png "RectangleGeometry")

## <a name="path-geometries"></a>Géométries de tracés

Une géométrie de tracé décrit une forme complexe qui peut être composée d’arcs, de courbes, d’ellipses, de lignes et de rectangles.

La `PathGeometry` classe définit les propriétés suivantes :

- `Figures`, de type `PathFigureCollection` , qui représente la collection d' `PathFigure` objets qui décrivent le contenu du tracé.
- `FillRule`, de type `FillRule` , qui détermine la façon dont les zones d’intersection contenues dans la géométrie sont combinées. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour plus d’informations sur l' `FillRule` énumération, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).

> [!NOTE]
> La `Figures` propriété est du `ContentProperty` de la `PathGeometry` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

Un `PathGeometry` est constitué d’une collection d' `PathFigure` objets, chacun d’entre eux `PathFigure` décrivant une forme dans la géométrie. Chaque `PathFigure` objet est constitué d’un ou plusieurs `PathSegment` objets, chacun d’eux décrivant un segment de la forme. Il existe de nombreux types de segments :

- `ArcSegment`, qui crée un arc elliptique entre deux points.
- `BezierSegment`, qui crée une courbe de Bézier cubique entre deux points.
- `LineSegment`, qui crée une ligne entre deux points.
- `PolyBezierSegment`, qui crée une série de courbes de Bézier cubiques.
- `PolyLineSegment`, qui crée une série de lignes.
- `PolyQuadraticBezierSegment`, qui crée une série de courbes de Bézier quadratiques.
- `QuadraticBezierSegment`, qui crée une courbe de Bézier quadratique.

Toutes les classes ci-dessus dérivent de la classe abstraite `PathSegment` .

Les segments dans un `PathFigure` sont combinés en une seule forme géométrique, le point de terminaison de chaque segment étant le point de départ du segment suivant. La `StartPoint` propriété d’un `PathFigure` spécifie le point à partir duquel le premier segment est dessiné. Chaque segment suivant démarre à l’extrémité du segment précédent. Par exemple, une ligne verticale de `10,50` à `10,150` peut être définie en affectant `StartPoint` à la propriété la valeur `10,50` et en créant un `LineSegment` avec un `Point` paramètre de propriété de `10,150` :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,50">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="10,150" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Vous pouvez créer des géométries plus complexes en utilisant une combinaison d' `PathSegment` objets et en utilisant plusieurs `PathFigure` objets dans un `PathGeometry` .

### <a name="create-an-arcsegment"></a>Créer un ArcSegment

`ArcSegment`Crée un arc elliptique entre deux points. Un arc elliptique est défini par les points de début et de fin, le rayon x et y, le facteur de rotation de l’axe x, une valeur indiquant si l’ARC doit être supérieur à 180 degrés et une valeur décrivant le sens dans lequel l’arc est dessiné.

La `ArcSegment` classe définit les propriétés suivantes :

- `Point`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de terminaison de l’arc elliptique. La valeur par défaut de cette propriété est (0,0).
- `Size`, de type [`Size`](xref:Xamarin.Forms.Size) , qui représente le rayon x et le rayon y de l’arc. La valeur par défaut de cette propriété est (0,0).
- `RotationAngle`, de type `double` , qui représente le degré de rotation de l’ellipse autour de l’axe x. La valeur par défaut de cette propriété est 0.
- `SweepDirection`, de type `SweepDirection` , qui spécifie le sens dans lequel l’arc est dessiné. La valeur par défaut de cette propriété est `SweepDirection.CounterClockwise`.
- `IsLargeArc`, de type `bool` , qui indique si l’ARC doit être supérieur à 180 degrés. La valeur par défaut de cette propriété est `false`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!NOTE]
> La `ArcSegment` classe ne contient pas de propriété pour le point de départ de l’arc. Il définit uniquement le point de terminaison de l’arc qu’il représente. Le point de départ de l’arc est le point actuel du auquel `PathFigure` le `ArcSegment` est ajouté.

L’énumération `SweepDirection` définit les membres suivants :

- `CounterClockwise`, qui spécifie que les arcs sont dessinés dans le sens des aiguilles d’une montre.
- `Clockwise`, qui spécifie que les arcs sont dessinés dans le sens des aiguilles d’une montre.

L’exemple suivant montre comment créer et restituer un `ArcSegment` dans un `Path` objet :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <ArcSegment Size="100,50"
                                            RotationAngle="45"
                                            IsLargeArc="True"
                                            SweepDirection="CounterClockwise"
                                            Point="200,100" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, un arc elliptique est dessiné de (10, 10) à (200 100).

### <a name="create-a-beziersegment"></a>Créer un BezierSegment

Un `BezierSegment` crée une courbe de Bézier cubique entre deux points. Une courbe de Bézier cubique est définie par quatre points : un point de départ, un point de terminaison et deux points de contrôle.

La `BezierSegment` classe définit les propriétés suivantes :

- `Point1`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le premier point de contrôle de la courbe. La valeur par défaut de cette propriété est (0,0).
- `Point2`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le deuxième point de contrôle de la courbe. La valeur par défaut de cette propriété est (0,0).
- `Point3`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de terminaison de la courbe. La valeur par défaut de cette propriété est (0,0).

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!NOTE]
> La `BezierSegment` classe ne contient pas de propriété pour le point de départ de la courbe. Le point de départ de la courbe est le point actuel du auquel `PathFigure` le `BezierSegment` est ajouté.

Les deux points de contrôle d’une courbe de Bézier cubique se comportent comme des aimants, attirant des parties de ce qui serait autrement une ligne droite vers elle-même et produisant une courbe. Le premier point de contrôle affecte la partie de début de la courbe. Le deuxième point de contrôle affecte la partie finale de la courbe. La courbe ne passe pas nécessairement par l’un des points de contrôle. Au lieu de cela, chaque point de contrôle déplace sa partie de la ligne vers elle-même, mais pas par le biais de lui-même.

L’exemple suivant montre comment créer et restituer un `BezierSegment` dans un `Path` objet :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <BezierSegment Point1="100,0"
                                               Point2="200,200"
                                               Point3="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, une courbe de Bézier cubique est dessinée de (10, 10) à (300, 10). La courbe a deux points de contrôle à (100, 0) et (200 200) :

![BezierSegment](geometry-images/beziersegment.png "BezierSegment")

### <a name="create-a-linesegment"></a>Créer un LineSegment

`LineSegment`Crée une ligne entre deux points.

La `LineSegment` classe définit la `Point` propriété, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de terminaison du segment de ligne. La valeur par défaut de cette propriété est (0,0), et elle est sauvegardée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

> [!NOTE]
> La `LineSegment` classe ne contient pas de propriété pour le point de départ de la ligne. Il définit uniquement le point de terminaison. Le point de départ de la ligne est le point actuel du auquel `PathFigure` le `LineSegment` est ajouté.

L’exemple suivant montre comment créer et restituer `LineSegment` des objets dans un `Path` objet :

```xaml
<Path Stroke="Black"      
      Aspect="Uniform"
      HorizontalOptions="Start">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure IsClosed="True"
                                StartPoint="10,100">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <LineSegment Point="100,100" />
                                <LineSegment Point="100,50" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, un segment de ligne est dessiné de (10 100) à (100 100) et de (100 100) à (100, 50). En outre, `PathFigure` est fermé, car sa `IsClosed` propriété a la valeur `true` . Un triangle est alors dessiné :

![LineSegments](geometry-images/linesegments.png "LineSegments")

### <a name="create-a-polybeziersegment"></a>Créer un PolyBezierSegment

Un `PolyBezierSegment` crée une ou plusieurs courbes de Bézier cubiques.

La `PolyBezierSegment` classe définit la `Points` propriété, de type `PointCollection` , qui représente les points qui définissent le `PolyBezierSegment` . Un `PointCollection` est un `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) objet d’objets. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

> [!NOTE]
> La `PolyBezierSegment` classe ne contient pas de propriété pour le point de départ de la courbe. Le point de départ de la courbe est le point actuel du auquel `PathFigure` le `PolyBezierSegment` est ajouté.

L’exemple suivant montre comment créer et restituer un `PolyBezierSegment` dans un `Path` objet :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyBezierSegment Points="0,0 100,0 150,100 150,0 200,0 300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, le `PolyBezierSegment` spécifie deux courbes de Bézier cubiques. La première courbe est comprise entre (10, 10) et (150 100) avec un point de contrôle de (0,0) et un autre point de contrôle (100, 0). La deuxième courbe est comprise entre (150 100) et (300), avec un point de contrôle de (150, 0) et un autre point de contrôle (200, 0) :

![PolyBezierSegment](geometry-images/polybeziersegment.png "PolyBezierSegment")

### <a name="create-a-polylinesegment"></a>Créer un PolyLineSegment

Un `PolyLineSegment` crée un ou plusieurs segments de ligne.

La `PolyLineSegment` classe définit la `Points` propriété, de type `PointCollection` , qui représente les points qui définissent le `PolyLineSegment` . Un `PointCollection` est un `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) objet d’objets. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

> [!NOTE]
> La `PolyLineSegment` classe ne contient pas de propriété pour le point de départ de la ligne. Le point de départ de la ligne est le point actuel du auquel `PathFigure` le `PolyLineSegment` est ajouté.

L’exemple suivant montre comment créer et restituer un `PolyLineSegment` dans un `Path` objet :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,10">
                    <PathFigure.Segments>
                        <PolyLineSegment Points="50,10 50,50" />
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, le `PolyLineSegment` spécifie deux lignes. La première ligne est comprise entre (10, 10) et (50, 10), tandis que la deuxième ligne est comprise entre (50, 10) et (50, 50) :

![PolyLineSegment](geometry-images/polylinesegment.png "PolyLineSegment")

### <a name="create-a-polyquadraticbeziersegment"></a>Créer un PolyQuadraticBezierSegment

Un `PolyQuadraticBezierSegment` crée une ou plusieurs courbes de Bézier quadratiques.

La `PolyQuadraticBezierSegment` classe définit la `Points` propriété, de type `PointCollection` , qui représente les points qui définissent le `PolyQuadraticBezierSegment` . Un `PointCollection` est un `ObservableCollection` [`Point`](xref:Xamarin.Forms.Point) objet d’objets. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

> [!NOTE]
> La `PolyQuadraticBezierSegment` classe ne contient pas de propriété pour le point de départ de la courbe. Le point de départ de la courbe est le point actuel du auquel `PathFigure` le `PolyQuadraticBezierSegment` est ajouté.

L’exemple suivant montre comment créer et restituer un `PolyQuadraticBezierSegment` dans un `Path` objet :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <PolyQuadraticBezierSegment Points="100,100 150,50 0,100 15,200" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, le `PolyQuadraticBezierSegment` spécifie deux courbes de Bézier. La première courbe est comprise entre (10, 10) et (150, 50) et un point de contrôle est (100 100). La deuxième courbe est comprise entre (100 100) et (15 200) avec un point de contrôle sur (0100) :

![PolyQuadraticBezierSegment](geometry-images/polyquadraticbeziersegment.png "PolyQuadraticBezierSegment")

### <a name="create-a-quadraticbeziersegment"></a>Créer un QuadraticBezierSegment

Un `QuadraticBezierSegment` crée une courbe de Bézier quadratique entre deux points.

La `QuadraticBezierSegment` classe définit les propriétés suivantes :

- `Point1`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de contrôle de la courbe. La valeur par défaut de cette propriété est (0,0).
- `Point2`, de type [`Point`](xref:Xamarin.Forms.Point) , qui représente le point de terminaison de la courbe. La valeur par défaut de cette propriété est (0,0).

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!NOTE]
> La `QuadraticBezierSegment` classe ne contient pas de propriété pour le point de départ de la courbe. Le point de départ de la courbe est le point actuel du auquel `PathFigure` le `QuadraticBezierSegment` est ajouté.

L’exemple suivant montre comment créer et restituer un `QuadraticBezierSegment` dans un `Path` objet :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigureCollection>
                    <PathFigure StartPoint="10,10">
                        <PathFigure.Segments>
                            <PathSegmentCollection>
                                <QuadraticBezierSegment Point1="200,200"
                                                        Point2="300,10" />
                            </PathSegmentCollection>
                        </PathFigure.Segments>
                    </PathFigure>
                </PathFigureCollection>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, une courbe de Bézier quadratique est dessinée de (10, 10) à (300, 10). La courbe a un point de contrôle sur (200 200) :

![QuadraticBezierSegment](geometry-images/quadraticbeziersegment.png "QuadraticBezierSegment")

### <a name="create-complex-geometries"></a>Créer des géométries complexes

Vous pouvez créer des géométries plus complexes à l’aide d’une combinaison d' `PathSegment` objets. L’exemple suivant crée une forme à l’aide d’un, d’un `BezierSegment` `LineSegment` et d’un `ArcSegment` :

```xaml
<Path Stroke="Black">
    <Path.Data>
        <PathGeometry>
            <PathGeometry.Figures>
                <PathFigure StartPoint="10,50">
                    <PathFigure.Segments>
                        <BezierSegment Point1="100,0"
                                       Point2="200,200"
                                       Point3="300,100"/>
                        <LineSegment Point="400,100" />
                        <ArcSegment Size="50,50"
                                    RotationAngle="45"
                                    IsLargeArc="True"
                                    SweepDirection="Clockwise"
                                    Point="200,100"/>
                    </PathFigure.Segments>
                </PathFigure>
            </PathGeometry.Figures>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, un `BezierSegment` est d’abord défini à l’aide de quatre points. L’exemple ajoute ensuite un `LineSegment` , qui est dessiné entre le point de terminaison de `BezierSegment` jusqu’au point spécifié par le `LineSegment` . Enfin, un `ArcSegment` est dessiné du point de terminaison de `LineSegment` jusqu’au point spécifié par le `ArcSegment` .

Il est possible de créer des géométries encore plus complexes à l’aide de plusieurs `PathFigure` objets dans un `PathGeometry` . L’exemple suivant crée un `PathGeometry` à partir de sept `PathFigure` objets, dont certains contiennent plusieurs `PathSegment` objets :

```xaml
<Path Stroke="Red"
      StrokeThickness="12"
      StrokeLineJoin="Round">
    <Path.Data>
        <PathGeometry>
            <!-- H -->
            <PathFigure StartPoint="0,0">
                <LineSegment Point="0,100" />
            </PathFigure>
            <PathFigure StartPoint="0,50">
                <LineSegment Point="50,50" />
            </PathFigure>
            <PathFigure StartPoint="50,0">
                <LineSegment Point="50,100" />
            </PathFigure>

            <!-- E -->
            <PathFigure StartPoint="125, 0">
                <BezierSegment Point1="60, -10"
                               Point2="60, 60"
                               Point3="125, 50" />
                <BezierSegment Point1="60, 40"
                               Point2="60, 110"
                               Point3="125, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="150, 0">
                <LineSegment Point="150, 100" />
                <LineSegment Point="200, 100" />
            </PathFigure>

            <!-- L -->
            <PathFigure StartPoint="225, 0">
                <LineSegment Point="225, 100" />
                <LineSegment Point="275, 100" />
            </PathFigure>

            <!-- O -->
            <PathFigure StartPoint="300, 50">
                <ArcSegment Size="25, 50"
                            Point="300, 49.9"
                            IsLargeArc="True" />
            </PathFigure>
        </PathGeometry>
    </Path.Data>
</Path>
```

Dans cet exemple, le mot « Hello » est dessiné à l’aide d’une combinaison d' `LineSegment` `BezierSegment` objets et, avec un seul `ArcSegment` objet :

![Plusieurs objets PathFigure](geometry-images/multiple-pathfigures.png "Plusieurs objets PathFigure")

## <a name="composite-geometries"></a>Géométries composites

Les objets Geometry composites peuvent être créés à l’aide d’un `GeometryGroup` . La `GeometryGroup` classe crée une géométrie composite à partir d’un ou de plusieurs `Geometry` objets. Un nombre quelconque d' `Geometry` objets peut être ajouté à un `GeometryGroup` .

La `GeometryGroup` classe définit les propriétés suivantes :

- `Children`, de type `GeometryCollection` , qui définit l’espèce des objets qui définissent `GeomtryGroup` . Un `GeometryCollection` est un `ObservableCollection` `Geometry` objet d’objets.
- `FillRule`, de type `FillRule` , qui spécifie la façon dont les zones d’intersection dans le `GeometryGroup` sont combinées. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!NOTE]
> La `Children` propriété est du `ContentProperty` de la `GeometryGroup` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

Pour plus d’informations sur l' `FillRule` énumération, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).

Pour dessiner une géométrie composite, définissez les `Geometry` objets requis comme enfants d’un `GeometryGroup` et affichez-les avec un `Path` objet. Le code XAML suivant en montre un exemple :

```xaml
<Path Stroke="Green"
      StrokeThickness="2"
      Fill="Orange">
    <Path.Data>
        <GeometryGroup>
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,150" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="150,250" />
            <EllipseGeometry RadiusX="100"
                             RadiusY="100"
                             Center="250,250" />
        </GeometryGroup>
    </Path.Data>
</Path>
```

Dans cet exemple, quatre `EllipseGeometry` objets avec des coordonnées x-RADIUS et y-RADIUS identiques, mais avec des coordonnées de centre différentes, sont combinés. Cela crée quatre cercles qui se chevauchent, dont les intérieurs sont remplis en orange en raison de la règle de remplissage par défaut `EvenOdd` :

![GeometryGroup](geometry-images/geometrygroup.png "GeometryGroup")

### <a name="roundrectanglegeometry"></a>RoundRectangleGeometry

Une géométrie à rectangles ronds représente la géométrie d’un rectangle, ou carré, avec des angles arrondis, et est définie par un rayon d’angle et une [`Rect`](xref:Xamarin.Forms.Rect) structure qui spécifie sa position relative, ainsi que sa hauteur et sa largeur.

La `RoundRectangleGeometry` classe, qui dérive de la `GeometryGroup` classe, définit les propriétés suivantes :

- `CornerRadius`, de type [`CornerRadius`](xref:Xamarin.Forms.CornerRadius) , qui est le rayon de l’angle de la géométrie.
- `Rect`, de type [`Rect`](xref:Xamarin.Forms.Rect) , qui représente les dimensions du rectangle.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

> [!NOTE]
> La règle de remplissage utilisée par `RoundRectangleGeometry` est `FillRule.Nonzero` . Pour plus d’informations sur les règles de remplissage, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).

L’exemple suivant montre comment créer et restituer un `RoundRectangleGeometry` dans un `Path` objet :

```xaml
<Path Fill="Blue"
      Stroke="Red">
    <Path.Data>
        <RoundRectangleGeometry CornerRadius="5"
                                Rect="10,10,150,100" />
    </Path.Data>
</Path>
```

La position et les dimensions du rectangle sont définies par une [`Rect`](xref:Xamarin.Forms.Rect) structure. Dans cet exemple, la position est (10, 10), la largeur est 150 et la hauteur est 100 unités indépendantes du périphérique. En outre, les angles des rectangles sont arrondis avec un rayon de 5 unités indépendantes du périphérique.

## <a name="clip-with-a-geometry"></a>Clip avec une géométrie

La [`VisualElement`](xref:Xamarin.Forms.VisualElement) classe a une `Clip` propriété, de type `Geometry` , qui définit le contour du contenu d’un élément. Lorsque la `Clip` propriété est définie sur un `Geometry` objet, seule la zone située dans la région du `Geometry` sera visible.

L’exemple suivant montre comment utiliser un `Geometry` objet comme zone de découpage pour un [`Image`](xref:Xamarin.Forms.Image) :

```xaml
<Image Source="monkeyface.png">
    <Image.Clip>
        <EllipseGeometry RadiusX="100"
                         RadiusY="100"
                         Center="180,180" />
    </Image.Clip>
</Image>
```

Dans cet exemple, un `EllipseGeometry` avec `RadiusX` les `RadiusY` valeurs et de 100 et une `Center` valeur de (180 180) a la valeur de la `Clip` propriété d’un [`Image`](xref:Xamarin.Forms.Image) . Seule la partie de l’image qui se trouve dans la zone de l’ellipse s’affiche :

![Découper une image avec un EllipseGeometry](geometries-images/clip-ellipsegeometry.png "Découper une image avec un EllipseGeometry")

> [!NOTE]
> Les géométries simples, les géométries de tracé et les géométries composites peuvent toutes être utilisées pour découper des [`VisualElement`](xref:Xamarin.Forms.VisualElement) objets.

## <a name="other-features"></a>Autres fonctionnalités

La `GeometryHelper` classe fournit les méthodes d’assistance suivantes :

- `FlattenGeometry`, qui aplatit un `Geometry` en `PathGeometry` .
- `FlattenCubicBezier`, qui aplatit une courbe de Bézier cubique en une `List<Point>` collection.
- `FlattenQuadraticBezier`, qui aplatit une courbe de Bézier quadratique dans une `List<Point>` collection.
- `FlattenArc`, qui aplatit un arc elliptique dans une `List<Point>` collection.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
- [Xamarin.Forms Formes : règles de remplissage](fillrules.md)
