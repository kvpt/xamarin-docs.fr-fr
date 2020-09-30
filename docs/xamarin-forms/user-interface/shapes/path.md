---
title: 'Xamarin.Forms Formes : chemin d’accès'
description: La Xamarin.Forms classe path peut être utilisée pour dessiner des courbes et des formes complexes.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/21/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 80b0bcd84602fc999764e7e0896a44f83401a9dd
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91558893"
---
# <a name="no-locxamarinforms-shapes-path"></a>Xamarin.Forms Formes : chemin d’accès

![API de la version préliminaire](~/media/shared/preview.png)

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

La `Path` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des courbes et des formes complexes. Ces courbes et formes sont souvent décrites à l’aide d' `Geometry` objets. Pour plus d’informations sur les propriétés que la `Path` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Path` définit les propriétés suivantes :

- `Data`, de type `Geometry` , qui spécifie la forme à dessiner.
- `RenderTransform`, de type `Transform` , qui représente la transformation appliquée à la géométrie d’un tracé avant son dessin.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour plus d’informations sur les transformations, consultez [ Xamarin.Forms transformations de chemin d’accès](path-transforms.md).

## <a name="create-a-path"></a>Créer un chemin d’accès

Pour dessiner un tracé, créez un `Path` objet et définissez sa `Data` propriété. Il existe deux techniques pour définir la `Data` propriété :

- Vous pouvez définir une valeur de chaîne pour `Data` en XAML, à l’aide de la syntaxe de balisage de chemin. Avec cette approche, la `Path.Data` valeur utilise un format de sérialisation pour les graphiques. En règle générale, vous ne modifiez pas cette valeur de chaîne manuellement après sa création. Au lieu de cela, vous utilisez des outils de conception pour manipuler les données et les exporter en tant que fragment de chaîne qui est consommable par la `Data` propriété.
- Vous pouvez définir la `Data` propriété sur un `Geometry` objet. Il peut s’agir d’un `Geometry` objet spécifique ou d’un `GeometryGroup` qui agit comme un conteneur qui peut combiner plusieurs objets Geometry en un seul objet.

### <a name="create-a-path-with-path-markup-syntax"></a>Créer un chemin d’accès avec la syntaxe de balisage Path

L’exemple de code XAML suivant montre comment dessiner un triangle à l’aide de la syntaxe de balisage de chemin d’accès :

```xaml
<Path Data="M 10,100 L 100,100 100,50Z"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start" />
```

La `Data` chaîne commence par la commande Move, indiquée par `M` , qui établit un point de départ absolu pour le chemin d’accès. `L` commande de ligne qui crée une ligne droite à partir du point de départ jusqu’au point de terminaison spécifié. `Z` commande Close, qui crée une ligne qui relie le point actuel au point de départ. Le résultat est un triangle :

![Triangle du tracé](path-images/triangle.png "Triangle du tracé")

> [!NOTE]
> La syntaxe de balisage de chemin d’accès est uniquement disponible en XAML.

Pour plus d’informations sur la syntaxe de balisage de chemin d’accès, consultez [ Xamarin.Forms syntaxe de balisage Path](path-markup-syntax.md).

### <a name="create-a-path-with-geometry-objects"></a>Créer un chemin d’accès avec des objets Geometry

Les courbes et les formes peuvent être décrites à l’aide `Geometry` d’objets, qui sont utilisés pour définir la `Path` propriété de l’objet `Data` . Vous avez le `Geometry` choix entre plusieurs objets. Les `EllipseGeometry` `LineGeometry` classes, et `RectangleGeometry` décrivent des formes relativement simples. Pour créer des formes plus complexes ou créer des courbes, utilisez un `PathGeometry` .

`PathGeometry` les objets sont constitués d’un ou de plusieurs `PathFigure` objets. Chaque `PathFigure` objet représente une forme différente. Chaque `PathFigure` objet est lui-même composé d’un ou de plusieurs `PathSegment` objets, représentant chacun une partie de connexion de la forme. Les types de segments incluent les `LineSegment` `BezierSegment` classes, et `ArcSegment` .

L’exemple de code XAML suivant montre comment dessiner un triangle à l’aide d’un `PathGeometry` objet :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
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

Dans cet exemple, le point de départ du triangle est (10 100). Un segment de ligne est dessiné de (10 100) à (100 100) et de (100 100) à (100, 50). Ensuite, les figures First et Last sont connectées, car la `PathFigure.IsClosed` propriété a la valeur `true` . Le résultat est un triangle :

![Triangle du tracé](path-images/triangle.png "Triangle du tracé")

Pour plus d’informations sur les géométries, consultez [ Xamarin.Forms géométries](geometries.md).

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
- [Xamarin.Forms Géométries](geometries.md)
- [Xamarin.Forms Syntaxe de balisage de chemin](path-markup-syntax.md)
- [Xamarin.Forms Transformations de chemin d’accès](path-transforms.md)