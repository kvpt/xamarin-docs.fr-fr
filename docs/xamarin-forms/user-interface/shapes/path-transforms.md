---
title: 'Xamarin.FormsFormes : transformations de chemin d’accès'
description: Une Xamarin.Forms transformation définit comment transformer un objet de chemin d’accès d’un espace de coordonnées en un autre espace de coordonnées.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c0abf7b30192ac9183c28e89d7917ea8d08dfc4f
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101326"
---
# <a name="xamarinforms-shapes-path-transforms"></a>Xamarin.FormsFormes : transformations de chemin d’accès

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)

`Transform`Définit comment transformer un `Path` objet d’un espace de coordonnées en un autre espace de coordonnées. Ce mappage est décrit par une transformation `Matrix` , qui est une collection de trois lignes avec trois colonnes de `double` valeurs.

Une matrice 3x3 est utilisée pour les transformations dans un plan x-y 2D. Les matrices de transformation affine peuvent être multipliées pour former un nombre quelconque de transformations linéaires, telles que la rotation et l’inclinaison, suivies de la translation. Le tableau suivant présente la structure d’une Xamarin.Forms matrice :

| | | |
|---------|---------|-----|
| M11     | M12     | 0.0 |
| M21     | M22     | 0.0 |
| OffsetX | OffsetY | 1.0 |

En manipulant des valeurs de matrice, vous pouvez faire pivoter, mettre à l’échelle, incliner et translater des `Path` objets. Par exemple, si vous remplacez la `OffsetX` valeur par 100, vous pouvez l’utiliser pour déplacer un `Path` objet 100 unités indépendantes du périphérique le long de l’axe x. Si vous remplacez la `M22` valeur par 3, vous pouvez l’utiliser pour étirer un `Path` objet à trois fois sa hauteur actuelle. Si vous modifiez les deux valeurs, vous déplacez l' `Path` objet 100 unités indépendantes du périphérique le long de l’axe x et étirez sa hauteur par un facteur de 3.

> [!NOTE]
> Une matrice de transformation affine a sa colonne finale égale à (0, 0, 1), de sorte que seuls les membres des deux premières colonnes doivent être spécifiés. Les membres de la dernière ligne, `OffsetX` et `OffsetY` représentent des valeurs de traduction.

Bien que vous puissiez utiliser une `Matrix` structure directement pour traduire des points individuels, Xamarin.Forms fournit également les classes suivantes qui vous permettent de transformer des `Path` objets sans utiliser directement des matrices :

- `RotateTransform`, qui fait pivoter un `Path` par un spécifié `Angle` .
- `ScaleTransform`, qui met `Path` à l’échelle un objet selon `ScaleX` les valeurs et spécifiées `ScaleY` .
- `SkewTransform`, qui incline un `Path` objet selon `AngleX` les valeurs et spécifiées `AngleY` .
- `TranslateTransform`, qui déplace un `Path` objet selon `X` les valeurs et spécifiées `Y` .

Xamarin.Formsfournit également les classes suivantes pour créer des transformations plus complexes :

- `TransformGroup`, qui représente un composite `Transform` constitué d’autres `Transform` objets.
- `CompositeTransform`, qui représente un composite `Transform` constitué d’autres `Transform` objets.
- `MatrixTransform`, qui crée des transformations personnalisées qui ne sont pas fournies par les autres `Transform` classes.

Toutes ces classes dérivent de la `Transform` classe, qui définit une `Value` propriété de type `Matrix` . Cette propriété représente la transformation actuelle en tant qu' `Matrix` objet.

Pour appliquer une transformation à un `Path` , vous devez créer une classe Transform et la définir en tant que valeur de la `Path.RenderTransform` propriété.

## <a name="rotation-transform"></a>Transformation de rotation

Une transformation de rotation fait pivoter un `Path` objet dans le sens des aiguilles d’une montre autour d’un point spécifié dans un système de coordonnées x-y 2D.

La `RotateTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `Angle`, de type `double` , représente l’angle, en degrés, de la rotation dans le sens des aiguilles d’une montre. La valeur par défaut de cette propriété est 0,0.
- `CenterX`, de type `double` , représente la coordonnée x du point central de la rotation. La valeur par défaut de cette propriété est 0,0.
- `CenterY`, de type `double` , représente la coordonnée y du point central de la rotation. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Les `CenterX` `CenterY` Propriétés et spécifient le point sur lequel l' `Path` objet est pivoté. Ce point central est exprimé dans l’espace de coordonnées de l’objet transformé. Par défaut, la rotation est appliquée à (0,0), qui est l’angle supérieur gauche de l' `Path` objet.

L’exemple suivant montre comment faire pivoter un `Path` objet :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <RotateTransform CenterX="0"
                         CenterY="0"
                         Angle="45" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet fait l’objet d’une rotation de 45 degrés autour de son angle supérieur gauche.

## <a name="scale-transform"></a>Transformation d’échelle

Une transformation d’échelle met `Path` à l’échelle un objet dans le système de coordonnées x-y 2D.

La `ScaleTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `ScaleX`, de type `double` , qui représente le facteur d’échelle de l’axe x. La valeur par défaut de cette propriété est 1,0.
- `ScaleY`, de type `double` , qui représente le facteur d’échelle de l’axe y. La valeur par défaut de cette propriété est 1,0.
- `CenterX`, de type `double` , qui représente la coordonnée x du point central de cette transformation. La valeur par défaut de cette propriété est 0,0.
- `CenterY`, de type `double` , qui représente la coordonnée y du point central de cette transformation. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

La valeur de `ScaleX` et `ScaleY` a un impact considérable sur la mise à l’échelle obtenue :

- Les valeurs comprises entre 0 et 1 réduisent la largeur et la hauteur de l’objet mis à l’échelle.
- Les valeurs supérieures à 1 augmentent la largeur et la hauteur de l’objet mis à l’échelle.
- Les valeurs 1 indiquent que l’objet n’est pas mis à l’échelle.
- Les valeurs négatives retournent l’objet de mise à l’échelle horizontalement et verticalement.
- Les valeurs comprises entre 0 et-1 retournent l’objet de mise à l’échelle et diminuent la largeur et la hauteur.
- Les valeurs inférieures à-1 retournent l’objet et augmentent la largeur et la hauteur.
- Les valeurs de-1 inversent l’objet mis à l’échelle, mais ne modifient pas sa taille horizontale ou verticale.

Les `CenterX` `CenterY` Propriétés et spécifient le point sur lequel l' `Path` objet est mis à l’échelle. Ce point central est exprimé dans l’espace de coordonnées de l’objet transformé. Par défaut, la mise à l’échelle est appliquée à (0,0), qui est l’angle supérieur gauche de l' `Path` objet. Cela a pour effet de déplacer l' `Path` objet et de le rendre plus grand, car lorsque vous appliquez une transformation, vous modifiez l’espace de coordonnées dans lequel l' `Path` objet réside.

L’exemple suivant montre comment mettre à l’échelle un `Path` objet :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="2"
                        ScaleY="2" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est mis à l’échelle pour doubler la taille.

## <a name="skew-transform"></a>Transformation d’inclinaison

Une transformation d’inclinaison incline un `Path` objet dans le système de coordonnées x-y 2D et est utile pour créer l’illusion de la profondeur 3D dans un objet 2D.

La `SkewTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `AngleX`, de type `double` , qui représente l’angle d’inclinaison de l’axe x, mesuré en degrés dans le sens inverse des aiguilles d’une position à partir de l’axe y. La valeur par défaut de cette propriété est 0,0.
- `AngleY`, de type `double` , qui représente l’angle d’inclinaison de l’axe y, mesuré en degrés dans le sens inverse des aiguilles d’une position à partir de l’axe x. La valeur par défaut de cette propriété est 0,0.
- `CenterX`, de type `double` , qui représente la coordonnée x du centre de la transformation. La valeur par défaut de cette propriété est 0,0.
- `CenterY`, de type `double` , qui représente la coordonnée y du centre de la transformation. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour prédire l’effet d’une transformation d’inclinaison, pensez à `AngleX` faire pivoter les valeurs de l’axe x par rapport au système de coordonnées d’origine. Par conséquent, pour un `AngleX` de 30, l’axe y pivote de 30 degrés par rapport à l’origine et incline les valeurs de x de 30 degrés par rapport à cette origine. De même, un `AngleY` de 30 incline les valeurs y de l' `Path` objet de 30 degrés par rapport à l’origine.

Pour incliner un `Path` objet sur place, définissez `CenterX` les `CenterY` Propriétés et sur le point central de l’objet.

L’exemple suivant montre comment incliner un `Path` objet :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <SkewTransform CenterX="0"
                       CenterY="0"
                       AngleX="45"
                       AngleY="0" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, une inclinaison horizontale de 45 degrés est appliquée à l' `Path` objet, à partir d’un point central de (0,0).

## <a name="translate-transform"></a>Traduire la transformation

Une transformation de translation déplace un objet dans le système de coordonnées x-y 2D.

La `TranslateTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `X`, de type `double` , qui représente la distance à déplacer le long de l’axe x. La valeur par défaut de cette propriété est 0,0.
- `Y`, de type `double` , qui représente la distance à déplacer le long de l’axe y. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

`X`Les valeurs négatives déplacent un objet vers la gauche, tandis que les valeurs positives déplacent un objet vers la droite. `Y`Les valeurs négatives déplacent un objet vers le haut, alors que les valeurs positives déplacent un objet vers le haut.

L’exemple suivant montre comment traduire un `Path` objet :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TranslateTransform X="50"
                            Y="50" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est déplacé de 50 unités indépendantes de l’appareil vers la droite et 50 unités indépendantes du périphérique.

## <a name="apply-multiple-transforms"></a>Appliquer plusieurs transformations

Xamarin.Formsa deux classes qui prennent en charge l’application de plusieurs transformations à un `Path` objet. Il s’agit de `TransformGroup` , et `CompositeTransform` . Un `TransformGroup` effectue des transformations dans n’importe quel ordre souhaité, tandis qu’un `CompositeTransform` effectue des transformations dans un ordre spécifique.

### <a name="transform-groups"></a>Groupes de transformations

Les groupes de transformation représentent des transformations composites composées de plusieurs `Transform` objets.

La `TransformGroup` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `Children`, de type `TransformCollection` , qui représente une collection d' `Transform` objets.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

L’ordre des transformations est important dans une transformation composite qui utilise la `TransformGroup` classe. Par exemple, si vous faites pivoter, puis mettez à l’échelle, puis Traduisez, vous obtenez un résultat différent de celui que vous convertissiez, puis faites pivoter, puis mettez à l’échelle. L’ordre de raison majeur est que les transformations telles que la rotation et la mise à l’échelle sont effectuées par rapport à l’origine du système de coordonnées. La mise à l’échelle d’un objet centré à l’origine produit un résultat différent pour la mise à l’échelle d’un objet qui a été déplacé hors de l’origine. De même, la rotation d’un objet centré à l’origine produit un résultat différent de la rotation d’un objet qui a été déplacé à l’extérieur de l’origine.

L’exemple suivant montre comment effectuer une transformation composite à l’aide de la `TransformGroup` classe :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleY="2" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est mis à l’échelle à deux fois sa taille et pivoté de 45 degrés.

## <a name="composite-transforms"></a>Transformations composites

Une transformation composite applique plusieurs transformations à un objet.

La `CompositeTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `CenterX`, de type `double` , qui représente la coordonnée x du point central de cette transformation. La valeur par défaut de cette propriété est 0,0.
- `CenterY`, de type `double` , qui représente la coordonnée y du point central de cette transformation. La valeur par défaut de cette propriété est 0,0.
- `ScaleX`, de type `double` , qui représente le facteur d’échelle de l’axe x. La valeur par défaut de cette propriété est 1,0.
- `ScaleY`, de type `double` , qui représente le facteur d’échelle de l’axe y. La valeur par défaut de cette propriété est 1,0.
- `SkewX`, de type `double` , qui représente l’angle d’inclinaison de l’axe x, mesuré en degrés dans le sens inverse des aiguilles d’une position à partir de l’axe y. La valeur par défaut de cette propriété est 0,0.
- `SkewY`, de type `double` , qui représente l’angle d’inclinaison de l’axe y, mesuré en degrés dans le sens inverse des aiguilles d’une position à partir de l’axe x. La valeur par défaut de cette propriété est 0,0.
- `Rotation`, de type `double` , représente l’angle, en degrés, de la rotation dans le sens des aiguilles d’une montre. La valeur par défaut de cette propriété est 0,0.
- `TranslateX`, de type `double` , qui représente la distance à déplacer le long de l’axe x. La valeur par défaut de cette propriété est 0,0.
- `TranslateY`, de type `double` , qui représente la distance à déplacer le long de l’axe y. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Un `CompositeTransform` applique des transformations dans cet ordre :

1. Scale ( `ScaleX` et `ScaleY` ).
1. Skew ( `SkewX` et `SkewY` ).
1. Faites pivoter ( `Rotation` ).
1. Translate ( `TranslateX` , `TranslateY` ).

Si vous souhaitez appliquer plusieurs transformations à un objet dans un ordre différent, vous devez créer un `TransformGroup` et insérer les transformations dans l’ordre souhaité.

> [!IMPORTANT]
> A `CompositeTransform` utilise les mêmes points centraux, `CenterX` et `CenterY` , pour toutes les transformations. Si vous souhaitez spécifier différents points centraux par transformation, utilisez un `TransformGroup` ,

L’exemple suivant montre comment effectuer une transformation composite à l’aide de la `CompositeTransform` classe :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="2"
                            ScaleY="2"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est mis à l’échelle à deux fois sa taille, pivoté de 45 degrés et traduit par 50 unités indépendantes du périphérique.

## <a name="custom-transforms"></a>Transformations personnalisées

Une transformation de matrice manipule des objets ou des systèmes de coordonnées dans un plan 2D, à l’aide d’une matrice affine. Une matrice 3x3 est utilisée pour les transformations. Vous pouvez multiplier des transformations de matrice affines pour former des transformations linéaires, telles que la rotation et l’inclinaison, suivies de la translation.

La `MatrixTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `Matrix`, de type `Matrix` , qui représente la matrice qui définit la transformation.

Ces propriétés sont sauvegardées par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de cibles de liaisons de données et d’un style.

La `MatrixTransform` classe est utilisée pour créer des transformations personnalisées qui ne sont pas fournies par les `RotateTransform` `ScaleTransform` classes,, `SkewTransform` ou `TranslateTransform` .

L’exemple suivant montre comment transformer un `Path` objet à l’aide d’un `MatrixTransform` :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="3"
                        M12="2" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est étiré, incliné et décalé à la fois dans les dimensions X et Y.

Cela peut également être écrit sous une forme simplifiée qui utilise un convertisseur de type intégré dans Xamarin.Forms :

```xaml
<Path Stroke="Black"
      StrokeThickness="1"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="3,2,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, la `Matrix` propriété est spécifiée sous la forme d’une chaîne délimitée par des virgules composée de six membres : `M11` ,,,, `M12` `M21` `M22` `OffsetX` , `OffsetY` .

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
- [Xamarin.FormsFormes](index.md)
