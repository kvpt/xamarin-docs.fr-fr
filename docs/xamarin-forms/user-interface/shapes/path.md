---
title: 'Xamarin.FormsFormes : chemin d’accès'
description: La Xamarin.Forms classe path peut être utilisée pour dessiner des courbes et des formes complexes.
ms.prod: xamarin
ms.assetid: B29486F4-9A5E-4588-ABDF-7EB1E69B9AE6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: cb2e3ea68f181f0a6e6392c012db221ef35d65bd
ms.sourcegitcommit: 16847681df17ed59b3b3528761c02e8fb48ffc4f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85104297"
---
# <a name="xamarinforms-shapes-path"></a>Xamarin.FormsFormes : chemin d’accès

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

La `Path` classe dérive de la `Shape` classe et peut être utilisée pour dessiner des courbes et des formes complexes. Ces courbes et formes sont souvent décrites à l’aide d' `Geometry` objets. Pour plus d’informations sur les propriétés que la `Path` classe hérite de la `Shape` classe, consultez [ Xamarin.Forms Shapes](index.md).

`Path` définit les propriétés suivantes :

- `Data`, de type `Geometry` , qui spécifie la forme à dessiner.
- `RenderTransform`, de type `Transform` , qui représente la transformation appliquée à la géométrie d’un tracé avant son dessin.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour plus d’informations sur les transformations, consultez [ Xamarin.Forms transformations de chemin d’accès](path-transforms.md).

## <a name="create-a-path"></a>Créer un chemin d’accès

L’exemple de code XAML suivant montre comment dessiner un polygone à l’aide d’une syntaxe abrégée spéciale appelée syntaxe de balisage de chemin d’accès :

```xaml
<Path Data="M 10,50 L 200,70"
      Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100" />
```

La `Data` chaîne commence par la commande « MoveTo », indiquée par `M` , qui établit un point de départ pour le chemin d’accès. `L`commande de ligne qui crée une ligne droite à partir du point de départ jusqu’au point de terminaison spécifié.

> [!NOTE]
> La syntaxe de balisage de chemin d’accès est uniquement disponible en XAML.

Pour plus d’informations sur la syntaxe de balisage de chemin d’accès, consultez [ Xamarin.Forms syntaxe de balisage Path](path-markup-syntax.md).

## <a name="path-geometry"></a>Géométrie du tracé

Les courbes et les formes peuvent être décrites à l’aide `Geometry` d’objets, qui sont utilisés pour définir la `Path` propriété de l’objet `Data` .

Vous avez le `Geometry` choix entre plusieurs objets. Les `EllipseGeometry` `LineGeometry` classes, et `RectangleGeometry` décrivent des formes relativement simples. Pour créer des formes plus complexes ou créer des courbes, utilisez un `PathGeometry` .

`PathGeometry`les objets sont constitués d’un ou de plusieurs `PathFigure` objets. Chaque `PathFigure` objet représente une forme différente. Chaque `PathFigure` objet est lui-même composé d’un ou de plusieurs `PathSegment` objets, représentant chacun une partie de connexion de la forme. Les types de segment sont les suivants : `LineSegment` , `BezierSegment` et `ArcSegment` .

Dans l’exemple suivant, un `Path` est utilisé pour dessiner un triangle :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
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

Pour plus d’informations sur les géométries, consultez [ Xamarin.Forms géométries](geometries.md).

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
- [Xamarin.FormsGéométries](geometries.md)
- [Xamarin.FormsSyntaxe de balisage de chemin](path-markup-syntax.md)
- [Xamarin.FormsTransformations de chemin d’accès](path-transforms.md)
