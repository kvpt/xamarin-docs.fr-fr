---
title: Xamarin.FormsFormes
description: Xamarin.FormsLes formes sont des types de vues qui vous permettent de dessiner des formes à l’écran.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: fdab7d422040aee5a00b16bb1e301f917b0c4883
ms.sourcegitcommit: ef3d4a70e70927c4f231b763842c5355f1571d15
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85243761"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsFormes

![](~/media/shared/preview.png "This API is currently pre-release")

Un `Shape` est un type de [`View`](xref:Xamarin.Forms.View) qui vous permet de dessiner une forme à l’écran. `Shape`les objets peuvent être utilisés dans les classes de disposition et la plupart des contrôles, car la `Shape` classe dérive de la `View` classe.

Xamarin.FormsLes formes sont disponibles dans l' `Xamarin.Forms.Shapes` espace de noms sur iOS, Android, MacOS, le plateforme Windows universelle (UWP) et le Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsLes formes sont actuellement expérimentales et ne peuvent être utilisées qu’en définissant l' `Shapes_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).

`Shape` définit les propriétés suivantes :

- `Aspect`, de type `Stretch` , décrit la façon dont la forme remplit son espace alloué. La valeur par défaut de cette propriété est `Stretch.None`.
- `Fill`, de type [`Color`](xref:Xamarin.Forms.Color) , indique la couleur utilisée pour peindre l’intérieur de la forme.
- `Stroke`, de type [`Color`](xref:Xamarin.Forms.Color) , indique la couleur utilisée pour peindre le contour de la forme.
- `StrokeDashArray`, de type `DoubleCollection` , qui représente une collection de `double` valeurs qui indiquent le motif des tirets et des intervalles utilisés pour définir le contour d’une forme.
- `StrokeDashOffset`, de type `double` , spécifie la distance dans le motif des tirets où un tiret commence. La valeur par défaut de cette propriété est 0,0.
- `StrokeLineCap`, de type `PenLineCap` , décrit la forme au début et à la fin d’une ligne ou d’un segment. La valeur par défaut de cette propriété est `PenLineCap.Flat`.
- `StrokeLineJoin`, de type `PenLineJoin` , spécifie le type de jointure utilisé aux sommets d’une forme. La valeur par défaut de cette propriété est `PenLineJoin.Miter`.
- `StrokeThickness`, de type `double` , indique la largeur du contour de la forme. La valeur par défaut de cette propriété est 1,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Xamarin.Formsdéfinit un certain nombre d’objets qui dérivent de la `Shape` classe. Il s’agit de,, `Ellipse` `Line` ,, `Path` `Polygon` `Polyline` et `Rectangle` .

## <a name="paint-shapes"></a>Formes de peinture

[`Color`](xref:Xamarin.Forms.Color)les objets sont utilisés pour peindre les formes `Stroke` et `Fill` :

```xaml
<Ellipse Fill="DarkBlue"
         Stroke="Red"
         StrokeThickness="4"
         WidthRequest="150"
         HeightRequest="50"
         HorizontalOptions="Start" />
```

Dans cet exemple, le trait et le remplissage d’un `Ellipse` sont spécifiés :

![Formes de peinture](images/ellipse.png "Formes de peinture")

> [!IMPORTANT]
> Si vous ne spécifiez [`Color`](xref:Xamarin.Forms.Color) pas de valeur pour `Stroke` , ou si vous définissez `StrokeThickness` sur 0, la bordure autour de la forme n’est pas dessinée.

Pour plus d’informations sur les valeurs valides [`Color`](xref:Xamarin.Forms.Color) , consultez [couleurs dans Xamarin.Forms ](~/xamarin-forms/user-interface/colors.md).

## <a name="stretch-shapes"></a>Étirer les formes

`Shape`les objets ont une `Aspect` propriété, de type `Stretch` . Cette propriété détermine la façon dont `Shape` le contenu d’un objet est étiré pour remplir l' `Shape` espace de disposition de l’objet. L' `Shape` espace de disposition d’un objet correspond à la quantité d’espace `Shape` allouée par le Xamarin.Forms système de disposition, en raison d’un explicite et d’un `WidthRequest` `HeightRequest` paramètre ou de ses `HorizontalOptions` `VerticalOptions` paramètres et.

L’énumération `Stretch` définit les membres suivants :

- `None`, qui indique que le contenu conserve sa taille d’origine. C’est la valeur par défaut de la propriété `Shape.Aspect`.
- `Fill`, qui indique que le contenu est redimensionné pour remplir les dimensions de destination. Les proportions ne sont pas conservées.
- `Uniform`, qui indique que le contenu est redimensionné pour s’ajuster aux dimensions de destination, tout en préservant les proportions.
- `UniformToFill`, indique que le contenu est redimensionné pour remplir les dimensions de destination, tout en conservant les proportions. Si les proportions du rectangle de destination diffèrent de celles de la source, le contenu source est détouré pour s’intégrer dans les dimensions de l’objet.

Le code XAML suivant montre comment définir la `Aspect` propriété :

```xaml
<Path Aspect="Uniform"
      Stroke="Yellow"
      StrokeThickness="1"
      Fill="Red"
      BackgroundColor="LightGray"
      HorizontalOptions="Start"
      HeightRequest="100"
      WidthRequest="100">
    <Path.Data>
        <!-- Path data goes here -->
    </Path.Data>  
</Path>      
```

Dans cet exemple, un `Path` objet dessine un cœur. Les `Path` Propriétés et de l’objet `WidthRequest` `HeightRequest` ont la valeur 100 unités indépendantes du périphérique, et sa `Aspect` propriété a la valeur `Uniform` . Par conséquent, le contenu de l’objet est redimensionné pour s’ajuster aux dimensions de destination, tout en conservant les proportions :

![Étirer les formes](images/aspect.png "Étirer les formes")

## <a name="draw-dashed-shapes"></a>Dessiner des formes en pointillés

`Shape`les objets ont une `StrokeDashArray` propriété, de type `DoubleCollection` . Cette propriété représente une collection de `double` valeurs qui indiquent le motif des tirets et des intervalles utilisés pour définir le contour d’une forme. Un `DoubleCollection` est un `ObservableCollection` de `double` valeurs. Chaque `double` dans la collection spécifie la longueur d’un tiret ou d’un intervalle. Le premier élément de la collection, situé à l’index 0, spécifie la longueur d’un tiret. Le deuxième élément de la collection, situé à l’index 1, spécifie la longueur d’un intervalle. Par conséquent, les objets avec une valeur d’index pair spécifient des tirets, tandis que les objets avec une valeur d’index impair spécifient des lacunes.

`Shape`les objets ont également une `StrokeDashOffset` propriété, de type `double` , qui spécifie la distance dans le motif des tirets où un tiret commence. Si vous ne définissez pas cette propriété, le `Shape` a un contour solide.

Les formes en pointillés peuvent être dessinées en définissant les `StrokeDashArray` `StrokeDashOffset` Propriétés et. La `StrokeDashArray` propriété doit être définie sur une ou plusieurs `double` valeurs, chaque paire étant délimitée par une virgule unique et/ou un ou plusieurs espaces. Par exemple, « 0,5 1,0 » et « 0.5, 1.0 » sont tous deux valides.

L’exemple de code XAML suivant montre comment dessiner un rectangle en pointillés :

```xaml
<Rectangle Fill="DarkBlue"
           Stroke="Red"
           StrokeThickness="4"
           StrokeDashArray="1,1"
           StrokeDashOffset="6"
           WidthRequest="150"
           HeightRequest="50"
           HorizontalOptions="Start" />
```

Dans cet exemple, un rectangle plein avec un trait en pointillés est dessiné :

![Rectangle en pointillés](images/dashed-rectangle.png "Ligne en pointillés")

## <a name="control-line-ends"></a>Extrémités de la ligne de contrôle

Une ligne comporte trois parties : embout de début, corps de ligne et extrémité de fin. Les majuscules de début et de fin décrivent la forme au début et à la fin d’une ligne ou d’un segment.

`Shape`les objets ont une `StrokeLineCap` propriété, de type `PenLineCap` , qui décrit la forme au début et à la fin d’une ligne ou d’un segment. L’énumération `PenLineCap` définit les membres suivants :

- `Flat`, qui représente une extrémité qui ne s’étend pas au-delà du dernier point de la ligne. Cela est comparable à l’absence d’extrémité de ligne et est la valeur par défaut de la `StrokeLineCap` propriété.
- `Square`, qui représente un rectangle dont la hauteur est égale à l’épaisseur de la ligne et une longueur égale à la moitié de l’épaisseur de la ligne.
- `Round`, qui représente un demi-cercle dont le diamètre est égal à l’épaisseur de la ligne.

> [!IMPORTANT]
> La `StrokeLineCap` propriété n’a aucun effet si vous la définissez sur une forme qui n’a aucun point de départ ou de fin. Par exemple, cette propriété n’a aucun effet si vous la définissez sur un `Ellipse` , ou `Rectangle` .

Le code XAML suivant montre comment définir la `StrokeLineCap` propriété :

```xaml
<Line X1="0"
      Y1="20"
      X2="300"
      Y2="20"
      StrokeLineCap="Round"
      Stroke="Red"
      StrokeThickness="12" />
```

Dans cet exemple, la ligne rouge est arrondie au début et à la fin de la ligne :

![Lignes d’épaisseur](images/linecap.png "Lignes d’épaisseur")

## <a name="control-line-joins"></a>Jointures de lignes de contrôle

`Shape`les objets ont une `StrokeLineJoin` propriété, de type `PenLineJoin` , qui spécifie le type de jointure utilisé aux sommets de la forme. L’énumération `PenLineJoin` définit les membres suivants :

- `Miter`, qui représente des vertex angulaires normaux. C’est la valeur par défaut de la propriété `StrokeLineJoin`.
- `Bevel`, qui représente les vertex biseautés.
- `Round`, qui représente des vertex arrondis.

Le code XAML suivant montre comment définir la `StrokeLineJoin` propriété :

```xaml
<Polyline Points="20 20,250 50,20 120"
          Stroke="DarkBlue"
          StrokeThickness="20"
          StrokeLineJoin="Round" />
```

Dans cet exemple, la polyligne bleue foncée a arrondi les jointures à ses sommets :

![Jointures de lignes](images/linejoin.png "Jointures de lignes")

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shapesdemos/)
- [Couleurs dansXamarin.Forms](~/xamarin-forms/user-interface/colors.md)
