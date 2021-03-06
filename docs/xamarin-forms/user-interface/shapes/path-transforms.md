---
title: 'Xamarin.Forms Formes : transformations de chemin d’accès'
description: Une Xamarin.Forms transformation définit comment transformer un objet de chemin d’accès d’un espace de coordonnées en un autre espace de coordonnées.
ms.prod: xamarin
ms.assetid: 07DE3D66-1820-4642-BDDF-84146D40C99D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 09/24/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: f66ae6e6947b5dade5302def5a7f89904bf701fe
ms.sourcegitcommit: 044e8d7e2e53f366942afe5084316198925f4b03
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97940003"
---
# <a name="no-locxamarinforms-shapes-path-transforms"></a>Xamarin.Forms Formes : transformations de chemin d’accès

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)

`Transform`Définit comment transformer un `Path` objet d’un espace de coordonnées en un autre espace de coordonnées. Lorsqu’une transformation est appliquée à un `Path` objet, elle modifie le mode de rendu de l’objet dans l’interface utilisateur.

Les transformations peuvent être classées en quatre classifications générales : rotation, mise à l’échelle, inclinaison et translation. Xamarin.Forms définit une classe pour chacune de ces classifications de transformation :

- `RotateTransform`, qui fait pivoter un `Path` par un spécifié `Angle` .
- `ScaleTransform`, qui met `Path` à l’échelle un objet selon `ScaleX` les valeurs et spécifiées `ScaleY` .
- `SkewTransform`, qui incline un `Path` objet selon `AngleX` les valeurs et spécifiées `AngleY` .
- `TranslateTransform`, qui déplace un `Path` objet selon `X` les valeurs et spécifiées `Y` .

Xamarin.Forms fournit également les classes suivantes pour créer des transformations plus complexes :

- `TransformGroup`, qui représente une transformation composite composée de plusieurs objets de transformation.
- `CompositeTransform`, qui applique plusieurs opérations de transformation à un `Path` objet.
- `MatrixTransform`, qui crée des transformations personnalisées qui ne sont pas fournies par les autres classes de transformation.

Toutes ces classes dérivent de la `Transform` classe, qui définit une `Value` propriété de type `Matrix` , qui représente la transformation actuelle en tant qu' `Matrix` objet. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style. Pour plus d’informations sur la `Matrix` structure, consultez [matrice de transformation](#transform-matrix).

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
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <ScaleTransform CenterX="0"
                        CenterY="0"
                        ScaleX="1.5"
                        ScaleY="1.5" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est mis à l’échelle à 1,5 fois la taille.

## <a name="skew-transform"></a>Transformation d’inclinaison

Une transformation d’inclinaison incline un `Path` objet dans le système de coordonnées x-y 2D et est utile pour créer l’illusion de la profondeur 3D dans un objet 2D.

La `SkewTransform` classe, qui dérive de la `Transform` classe, définit les propriétés suivantes :

- `AngleX`, de type `double` , qui représente l’angle d’inclinaison de l’axe x, mesuré en degrés dans le sens inverse des aiguilles d’une position à partir de l’axe y. La valeur par défaut de cette propriété est 0,0.
- `AngleY`, de type `double` , qui représente l’angle d’inclinaison de l’axe y, mesuré en degrés dans le sens inverse des aiguilles d’une position à partir de l’axe x. La valeur par défaut de cette propriété est 0,0.
- `CenterX`, de type `double` , qui représente la coordonnée x du centre de la transformation. La valeur par défaut de cette propriété est 0,0.
- `CenterY`, de type `double` , qui représente la coordonnée y du centre de la transformation. La valeur par défaut de cette propriété est 0,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Pour prédire l’effet d’une transformation d’inclinaison, pensez à `AngleX` faire pivoter les valeurs de l’axe x par rapport au système de coordonnées d’origine. Par conséquent, pour un `AngleX` de 30, l’axe y pivote de 30 degrés par rapport à l’origine et incline les valeurs de x de 30 degrés par rapport à cette origine. De même, un `AngleY` de 30 incline les valeurs y de l' `Path` objet de 30 degrés par rapport à l’origine.

> [!NOTE]
> Pour incliner un `Path` objet sur place, définissez `CenterX` les `CenterY` Propriétés et sur le point central de l’objet.

L’exemple suivant montre comment incliner un `Path` objet :

```xaml
<Path Stroke="Black"
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

## <a name="multiple-transforms"></a>Transformations multiples

Xamarin.Forms a deux classes qui prennent en charge l’application de plusieurs transformations à un `Path` objet. Il s’agit de `TransformGroup` , et `CompositeTransform` . Un `TransformGroup` effectue des transformations dans n’importe quel ordre souhaité, tandis qu’un `CompositeTransform` effectue des transformations dans un ordre spécifique.

### <a name="transform-groups"></a>Groupes de transformations

Les groupes de transformation représentent des transformations composites composées de plusieurs `Transform` objets.

La `TransformGroup` classe, qui dérive de la `Transform` classe, définit une `Children` propriété, de type `TransformCollection` , qui représente une collection d' `Transform` objets. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

L’ordre des transformations est important dans une transformation composite qui utilise la `TransformGroup` classe. Par exemple, si vous faites pivoter, puis mettez à l’échelle, puis Traduisez, vous obtenez un résultat différent de celui que vous convertissiez, puis faites pivoter, puis mettez à l’échelle. L’ordre de raison majeur est que les transformations telles que la rotation et la mise à l’échelle sont effectuées par rapport à l’origine du système de coordonnées. La mise à l’échelle d’un objet centré à l’origine produit un résultat différent pour la mise à l’échelle d’un objet qui a été déplacé hors de l’origine. De même, la rotation d’un objet centré à l’origine produit un résultat différent de la rotation d’un objet qui a été déplacé à l’extérieur de l’origine.

L’exemple suivant montre comment effectuer une transformation composite à l’aide de la `TransformGroup` classe :

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <TransformGroup>
            <ScaleTransform ScaleX="1.5"
                            ScaleY="1.5" />
            <RotateTransform Angle="45" />
        </TransformGroup>
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est mis à l’échelle à 1,5 fois sa taille, puis pivoté de 45 degrés.

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
      Aspect="Uniform"
      HorizontalOptions="Center"
      HeightRequest="100"
      WidthRequest="100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <CompositeTransform ScaleX="1.5"
                            ScaleY="1.5"
                            Rotation="45"
                            TranslateX="50"
                            TranslateY="50" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est mis à l’échelle à 1,5 fois sa taille, puis pivoté de 45 degrés, puis traduit par 50 unités indépendantes du périphérique.

## <a name="transform-matrix"></a>Transformer la matrice

Une transformation peut être décrite en termes de matrice de transformation affine 3x3, qui effectue des transformations dans un espace 2D. Cette matrice 3x3 est représentée par la `Matrix` structure, qui est une collection de trois lignes et trois colonnes de `double` valeurs.

La `Matrix` structure définit les propriétés suivantes :

- `Determinant`, de type `double` , qui obtient le déterminant de la matrice.
- `HasInverse`, de type `bool` , qui indique si la matrice est réversible.
- `Identity`, de type `Matrix` , qui obtient une matrice d’identité.
- `HasIdentity`, de type `bool` , qui indique si la matrice est une matrice d’identité.
- `M11`, de type `double` , qui représente la valeur de la première ligne et de la première colonne de la matrice.
- `M12`, de type `double` , qui représente la valeur de la première ligne et de la deuxième colonne de la matrice.
- `M21`, de type `double` , qui représente la valeur de la deuxième ligne et de la première colonne de la matrice.
- `M22`, de type `double` , qui représente la valeur de la deuxième ligne et de la deuxième colonne de la matrice.
- `OffsetX`, de type `double` , qui représente la valeur de la troisième ligne et de la première colonne de la matrice.
- `OffsetY`, de type `double` , qui représente la valeur de la troisième ligne et de la deuxième colonne de la matrice.

Les `OffsetX` `OffsetY` Propriétés et sont donc nommées, car elles spécifient la valeur de conversion de l’espace de coordonnées le long des axes x et y, respectivement.

En outre, le `Matrix` struct expose une série de méthodes qui peuvent être utilisées pour manipuler les valeurs de la matrice, notamment,,, `Append` `Invert` `Multiply` `Prepend` et bien plus encore.

Le tableau suivant présente la structure d’une Xamarin.Forms matrice :

:::row:::
    :::column:::
        M11
    :::column-end:::
    :::column:::
        M12
    :::column-end:::
    :::column:::
        0.0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        M21
    :::column-end:::
    :::column:::
        M22
    :::column-end:::
    :::column:::
        0.0
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        OffsetX
    :::column-end:::
    :::column:::
        OffsetY
    :::column-end:::
    :::column:::
        1.0
    :::column-end:::
:::row-end:::

> [!NOTE]
> Une matrice de transformation affine a sa colonne finale égale à (0, 0, 1), de sorte que seuls les membres des deux premières colonnes doivent être spécifiés.

En manipulant des valeurs de matrice, vous pouvez faire pivoter, mettre à l’échelle, incliner et translater des `Path` objets. Par exemple, si vous remplacez la `OffsetX` valeur par 100, vous pouvez l’utiliser pour déplacer un `Path` objet 100 unités indépendantes du périphérique le long de l’axe x. Si vous remplacez la `M22` valeur par 3, vous pouvez l’utiliser pour étirer un `Path` objet à trois fois sa hauteur actuelle. Si vous modifiez les deux valeurs, vous déplacez l' `Path` objet 100 unités indépendantes du périphérique le long de l’axe x et étirez sa hauteur par un facteur de 3. En outre, les matrices de transformation affine peuvent être multipliées pour former un nombre quelconque de transformations linéaires, telles que la rotation et l’inclinaison, suivies de la translation.

## <a name="custom-transforms"></a>Transformations personnalisées

La `MatrixTransform` classe, qui dérive de la `Transform` classe, définit une `Matrix` propriété, de type `Matrix` , qui représente la matrice qui définit la transformation. Cette propriété est stockée par un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objet, ce qui signifie qu’il peut s’agir de la cible des liaisons de données et d’un style.

Toute transformation que vous pouvez décrire avec un `TranslateTransform` `ScaleTransform` objet,, `RotateTransform` ou `SkewTransform` peut également être décrite par un `MatrixTransform` . Toutefois, les `TranslateTransform` classes,, `ScaleTransform` `RotateTransform` et `SkewTransform` sont plus faciles à conceptualiser que la définition des composants vectoriels dans un `Matrix` . Par conséquent, la `MatrixTransform` classe est généralement utilisée pour créer des transformations personnalisées qui ne sont pas fournies par les `RotateTransform` classes,, `ScaleTransform` `SkewTransform` ou `TranslateTransform` .

L’exemple suivant montre comment transformer un `Path` objet à l’aide d’un `MatrixTransform` :

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform>
            <MatrixTransform.Matrix>
                <!-- M11 stretches, M12 skews -->
                <Matrix OffsetX="10"
                        OffsetY="100"
                        M11="1.5"
                        M12="1" />
            </MatrixTransform.Matrix>
        </MatrixTransform>
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, l' `Path` objet est étiré, incliné et décalé à la fois dans les dimensions X et Y.

Cela peut également être écrit sous une forme simplifiée qui utilise un convertisseur de type intégré dans Xamarin.Forms :

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z">
    <Path.RenderTransform>
        <MatrixTransform Matrix="1.5,1,0,1,10,100" />
    </Path.RenderTransform>
</Path>
```

Dans cet exemple, la `Matrix` propriété est spécifiée sous la forme d’une chaîne délimitée par des virgules composée de six membres : `M11` ,,,, `M12` `M21` `M22` `OffsetX` , `OffsetY` . Alors que les membres sont délimités par des virgules dans cet exemple, ils peuvent également être délimités par un ou plusieurs espaces.

En outre, l’exemple précédent peut être simplifié encore plus en spécifiant les six mêmes membres comme valeur de la `RenderTransform` propriété :

```xaml
<Path Stroke="Black"
      Aspect="Uniform"
      HorizontalOptions="Center"
      RenderTransform="1.5 1 0 1 10 100"
      Data="M13.908992,16.207977L32.000049,16.207977 32.000049,31.999985 13.908992,30.109983z" />
```

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Xamarin.Forms Formes](index.md)
