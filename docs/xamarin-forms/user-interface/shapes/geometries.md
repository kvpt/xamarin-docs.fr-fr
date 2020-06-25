---
title: 'Xamarin.FormsFormes : géométries'
description: Xamarin.Formsles classes Geometry vous permettent de décrire la géométrie d’une forme 2D.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 412b0255dafcbd752c7b350ddc25ecd13f8f98d8
ms.sourcegitcommit: 8f6cc5208f675c8cfb645bd9ffb0fc1f8ea71411
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85326182"
---
# <a name="xamarinforms-shapes-geometries"></a>Xamarin.FormsFormes : géométries

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Geometry` classe, et les classes qui dérivent de celle-ci, vous permettent de décrire la géométrie d’une forme 2D. `Geometry`les objets peuvent être simples, tels que des rectangles et des cercles, ou composites, créés à partir de deux objets géométriques ou plus. Vous pouvez créer des géométries plus complexes à l’aide de la `PathGeometry` classe, ce qui vous permet de décrire des arcs et des courbes.

Les `Geometry` `Shape` classes et semblent similaires, dans le sens où elles décrivent toutes deux les formes 2D, mais présentent une différence importante. La classe `Geometry` dérive de la `BindableObject` classe, tandis que la `Shape` classe dérive de la `View` classe. Par conséquent, `Shape` les objets peuvent s’afficher et participer au système de disposition, contrairement aux `Geometry` objets. Bien que `Shape` les objets soient plus facilement utilisables que les `Geometry` objets, `Geometry` les objets sont plus polyvalents. Lorsqu’un `Shape` objet est utilisé pour restituer des graphiques 2D, un `Geometry` objet peut être utilisé pour définir la région géométrique pour les graphiques 2D et définir une région pour le découpage.

La classe de base pour toutes les géométries est la classe abstraite `Geometry` . Les classes qui dérivent de cette classe peuvent être regroupées en trois catégories : géométries simples, géométries de tracés et géométries composites.

## <a name="simple-geometries"></a>Géométries simples

Les classes Geometry simples sont `EllipseGeometry` , `LineGeometry` et `RectangleGeometry` . Ils sont utilisés pour créer des formes géométriques de base, telles que des cercles, des lignes et des rectangles. Ces mêmes formes, ainsi que des formes plus complexes, peuvent être créées à l’aide d’un `PathGeometry` ou en combinant des objets Geometry, mais ces classes fournissent un moyen plus simple de produire ces formes géométriques de base.

### <a name="ellipsegeometry"></a>EllipseGeometry

Une géométrie d’ellipse représente la géométrie ou une ellipse ou un cercle, et est définie par un point central, un rayon x et un rayon y.

La `EllipseGeometry` classe définit les propriétés suivantes :

- `Center`, de type `Point` , qui représente le point central de la géométrie.
- `RadiusX`, de type `double` , qui représente la valeur de rayon x de la géométrie. La valeur par défaut de cette propriété est 0,0.
- `RadiusY`, de type `double` , qui représente la valeur de rayon y de la géométrie. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

L’exemple suivant montre comment créer et restituer un `EllipseGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Red"
      StrokeThickness="1">
  <Path.Data>
    <EllipseGeometry Center="50,50"
                     RadiusX="50"
                     RadiusY="50" />
  </Path.Data>
</Path>
```

Dans cet exemple, le centre de `EllipseGeometry` est défini sur (50, 50) et le rayon x et le rayon y sont tous deux définis sur 50. Un cercle avec un diamètre de 100 est créé, dont l’intérieur est bleu.

### <a name="linegeometry"></a>LineGeometry

Une géométrie de ligne représente la géométrie d’une ligne et est définie en spécifiant le point de départ de la ligne et le point de terminaison.

La `LimeGeometry` classe définit les propriétés suivantes :

- `StartPoint`, de type `Point` , qui représente le point de départ de la ligne.
- `EndPoint`, de type `Point` , qui représente le point de terminaison de la ligne.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

L’exemple suivant montre comment créer et restituer un `LineGeometry` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <LineGeometry StartPoint="10,20"
                  EndPoint="100,130" />
  </Path.Data>
</Path>
```

Dans cet exemple, un `LineGeometry` est dessiné de (10, 20) à (100 130).

### <a name="rectanglegeometry"></a>RectangleGeometry

Une géométrie rectangle représente un rectangle et est définie avec une `Rect` structure qui spécifie sa position relative, sa hauteur et sa largeur.

La `RectangleGeometry` classe définit les propriétés suivantes :

- `Rect`, de type `FormsRect` , qui représente les dimensions du rectangle.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

L’exemple suivant montre comment créer et restituer un `RectangleGeometry` :

```xaml
<Path Fill="Blue"
      Stroke="Black"
      StrokeThickness="1">
  <Path.Data>
    <RectangleGeometry Rect="50,50,25,25" />
  </Path.Data>
</Path>
```

La position et les dimensions du rectangle sont définies par une `Rect` structure. Dans cet exemple, la position est (50, 50), tandis que la hauteur et la largeur sont toutes les 25, ce qui crée un carré.

## <a name="path-geometries"></a>Géométries de tracés

Une géométrie de tracé décrit une forme complexe qui peut être composée d’arcs, de courbes, d’ellipses, de lignes et de rectangles.

La `PathGeometry` classe définit les propriétés suivantes :

- `Figures`, de type `PathFigureCollection` , qui représente la collection d' `PathFigure` objets qui décrivent le contenu du tracé.
- `FillRule`, de type `FillRule` , qui détermine la façon dont les zones d’intersection contenues dans la géométrie sont combinées. La valeur par défaut de cette propriété est `FillRule.EvenOdd`.

> [!NOTE]
> La `Figures` propriété est du `ContentProperty` de la `PathGeometry` classe et n’a donc pas besoin d’être explicitement définie à partir de XAML.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour plus d’informations sur l' `FillRule` énumération, consultez [ Xamarin.Forms Shapes : Fill Rules](fillrules.md).

Un `PathGeometry` est constitué d’une collection d' `PathFigure` objets, chacun d’entre eux `PathFigure` décrivant une forme dans la géométrie. Chaque `PathFigure` objet est constitué d’un ou plusieurs `PathSegment` objets, chacun d’eux décrivant un segment de la forme. Il existe de nombreux types de segments :

- `ArcSegment`, qui crée un arc elliptique entre deux points.
- `BezierSegment`, qui crée une courbe de Bézier cubique entre deux points.
- `LineSegment`, qui crée une ligne entre deux points.
- `PolyBezierSegment`, qui crée une série de courbes de Bézier cubiques.
- `PolyLineSegment`, qui crée une série de lignes.
- `PolyQuadraticBezierSegment`, qui crée une série de courbes de Bézier quadratiques.
- `QuadraticBezierSegment`, qui crée une courbe de Bézier quadratique.

Les segments dans un `PathFigure` sont combinés en une seule forme géométrique, le point de terminaison de chaque segment étant le point de départ du segment suivant. La `StartPoint` propriété d’un `PathFigure` spécifie le point à partir duquel le premier segment est dessiné. Chaque segment suivant démarre à l’extrémité du segment précédent. Par exemple, une ligne verticale de `10,50` à `10,150` peut être définie en affectant `StartPoint` à la propriété la valeur `10,50` et en créant un `LineSegment` avec un `Point` paramètre de propriété de `10,150` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1">
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

## <a name="composite-geometries"></a>Géométries composites

Les objets Geometry composites peuvent être créés à l’aide d’un `GeometryGroup` . La `GeometryGroup` classe crée une géométrie composite à partir d’un ou de plusieurs `Geometry` objets. Un nombre quelconque d' `Geometry` objets peut être ajouté à un `GeometryGroup` .

L’exemple suivant montre comment combiner des géométries dans un `GeometryGroup` :

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

Dans cet exemple, quatre `EllipseGeometry` objets avec des coordonnées x-RADIUS et y-RADIUS identiques, mais avec des coordonnées de centre différentes, sont combinés. Cela crée quatre cercles qui se chevauchent, dont les intérieurs sont remplis en orange avec la `EvenOdd` règle de remplissage.

## <a name="clip-geometries"></a>Géométries de clip

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

- `FlattenGeometry`
- `FlattenCubicBezier`
- `FlattenQuadraticBezier`
- `FlattenArc`

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.FormsFormes](index.md)
- [Xamarin.FormsFormes : règles de remplissage](fillrules.md)
