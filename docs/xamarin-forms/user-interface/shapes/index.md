---
title: Xamarin.FormsFormes
description: Xamarin.FormsLes formes sont des types de vues qui vous permettent de dessiner des formes à l’écran.
ms.prod: xamarin
ms.assetid: 4E749FE8-852C-46DA-BB1E-652936106357
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 06/16/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9c7c552abe724dca6b06265b73346a399d35c3cb
ms.sourcegitcommit: d86b7a18cf8b1ef28cd0fe1d311f1c58a65101a8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85101401"
---
# <a name="xamarinforms-shapes"></a>Xamarin.FormsFormes

![](~/media/shared/preview.png "This API is currently pre-release")

Un `Shape` est un type de [`View`](xref:Xamarin.Forms.View) qui vous permet de dessiner une forme à l’écran. `Shape`les objets peuvent être utilisés dans les classes de disposition et la plupart des contrôles, car la `Shape` classe dérive de la `View` classe.

Xamarin.FormsLes formes sont disponibles dans l' `Xamarin.Forms.Shapes` espace de noms sur iOS, Android, MacOS, le plateforme Windows universelle (UWP) et le Windows Presentation Foundation (WPF).

> [!IMPORTANT]
> Xamarin.FormsLes formes sont actuellement expérimentales et ne peuvent être utilisées qu’en définissant l' `Shapes_Experimental` indicateur. Pour plus d’informations, consultez la page [indicateurs expérimentaux](~/xamarin-forms/internals/experimental-flags.md).

`Shape` définit les propriétés suivantes :

- `Aspect`, de type `Stretch` , décrit la façon dont la forme remplit son espace alloué. La valeur par défaut de cette propriété est `Stretch.None`.
- `Fill`, de type `Color` , indique la couleur utilisée pour peindre l’intérieur de la forme.
- `Stroke`, de type `Color` , indique la couleur utilisée pour peindre le contour de la forme.
- `StrokeDashArray`, de type `DoubleCollection` , qui représente une collection de `double` valeurs qui indiquent le motif des tirets et des intervalles utilisés pour définir le contour d’une forme.
- `StrokeDashOffset`, de type `double` , spécifie la distance dans le motif des tirets où un tiret commence. La valeur par défaut de cette propriété est 0,0.
- `StrokeLineCap`, de type `PenLineCap` , décrit la forme au début et à la fin d’une ligne ou d’un segment. La valeur par défaut de cette propriété est `PenLineCap.Flat`.
- `StrokeLineJoin`, de type `PenLineJoin` , spécifie le type de jointure utilisé aux sommets d’une forme. La valeur par défaut de cette propriété est `PenLineJoin.Miter`.
- `StrokeThickness`, de type `double` , indique la largeur du contour de la forme. La valeur par défaut de cette propriété est 1,0.

Ces propriétés sont sauvegardées par des [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) objets, ce qui signifie qu’elles peuvent être des cibles de liaisons de données et être stylisées.

Xamarin.Formsdéfinit un certain nombre d’objets qui dérivent de la `Shape` classe. Ces derniers comprennent `Ellipse`, `Line`, `Path`, `Polygon`, `Polyline` et `Rectangle`.

## <a name="related-links"></a>Liens connexes

- [ShapeDemos (exemple)](https://github.com/xamarin/xamarin-forms-samples/tree/master/UserInterface/ShapesDemos/)
