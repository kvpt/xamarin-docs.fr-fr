---
title: Polylignes et équations paramétriques
description: Cet article explique comment à SkiaSharp utiliser pour restituer une ligne que vous pouvez définir avec des équations paramétriques et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
ms.openlocfilehash: f326a2ef449b7c807be150a002a4afc600d9908d
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68652702"
---
# <a name="polylines-and-parametric-equations"></a>Polylignes et équations paramétriques

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Permet d’afficher n’importe quelle ligne que vous pouvez définir avec des équations paramétriques SkiaSharp_

Dans le [ **SkiaSharp courbes et chemins d’accès** ](../curves/index.md) section de ce guide, vous verrez les différentes méthodes qui [ `SKPath` ](xref:SkiaSharp.SKPath) définit pour afficher certains types de courbes. Toutefois, il est parfois nécessaire de dessiner un type de courbe n’est pas directement pris en charge par `SKPath`. Dans ce cas, vous pouvez utiliser une polyligne (il s’agit d’une collection de lignes connectées) pour dessiner une courbe que vous pouvez définir mathématiquement. Si vous modifiez les lignes suffisamment petit et nombreux révèle suffisante, le résultat doit ressembler à une courbe. Cette spirale est réellement 3 600 lignes peu :

![](polylines-images/spiralexample.png "Une spirale")

En général, il est préférable de définir une courbe en termes d’une paire d’équations paramétriques. Il s’agit des équations de coordonnées X et Y qui dépendent d’une troisième variable, parfois appelée `t` pour le moment. Par exemple, les équations paramétriques suivantes définissent un cercle avec un rayon de 1 centré au point (0, 0) pour *t* comprise entre 0 et 1 :

`x = cos(2πt)`

`y = sin(2πt)`

 Si vous souhaitez un rayon supérieures à 1, vous pouvez simplement multiplier les valeurs de sinus et le cosinus par ce rayon et si vous avez besoin atteindre le centre d’un autre emplacement, ajoutez ces valeurs :

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Pour une ellipse avec parallèle axes horizontal et vertical, deux rayons sont impliqués :

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

Vous pouvez ensuite placer le code de SkiaSharp équivalent dans une boucle qui calcule les différents points et ajoute celles pour un chemin d’accès. Le code de SkiaSharp suivant crée un `SKPath` objet pour une ellipse qui remplit la surface d’affichage. La boucle parcourt les 360 degrés directement. Le centre est la moitié de la largeur et hauteur de la surface d’affichage, et sont donc les rayons des deux :

```csharp
SKPath path = new SKPath();

for (float angle = 0; angle < 360; angle += 1)
{
    double radians = Math.PI * angle / 180;
    float x = info.Width / 2 + (info.Width / 2) * (float)Math.Cos(radians);
    float y = info.Height / 2 + (info.Height / 2) * (float)Math.Sin(radians);

    if (angle == 0)
    {
        path.MoveTo(x, y);
    }
    else
    {
        path.LineTo(x, y);
    }
}
path.Close();
```

Cela entraîne une ellipse définie par des lignes peu 360. Lorsqu’il est restitué, il apparaît sans heurts.

Bien sûr, vous n’avez pas besoin créer une ellipse à l’aide d’une polyligne car `SKPath` inclut un `AddOval` méthode qui le fait pour vous. Mais vous pouvez dessiner un objet visuel n’est pas fourni par `SKPath`.

Le **Archimède spirale** page de code est autrement similaire au code ellipse, mais avec une différence essentielle. Il effectue une boucle autour du cercle 360 degrés 10 fois, ajuster en permanence le rayon :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPoint center = new SKPoint(info.Width / 2, info.Height / 2);
    float radius = Math.Min(center.X, center.Y);

    using (SKPath path = new SKPath())
    {
        for (float angle = 0; angle < 3600; angle += 1)
        {
            float scaledRadius = radius * angle / 3600;
            double radians = Math.PI * angle / 180;
            float x = center.X + scaledRadius * (float)Math.Cos(radians);
            float y = center.Y + scaledRadius * (float)Math.Sin(radians);
            SKPoint point = new SKPoint(x, y);

            if (angle == 0)
            {
                path.MoveTo(point);
            }
            else
            {
                path.LineTo(point);
            }
        }

        SKPaint paint = new SKPaint
        {
            Style = SKPaintStyle.Stroke,
            Color = SKColors.Red,
            StrokeWidth = 5
        };

        canvas.DrawPath(path, paint);
    }
}
```

Le résultat est également appelé un *spirale arithmétique* , car le décalage entre chaque boucle est constante :

[![](polylines-images/archimedeanspiral-small.png "Capture d’écran triple de la page Archimède spirale")](polylines-images/archimedeanspiral-large.png#lightbox "Triple capture d’écran de la page Archimède spirale")

Notez que le `SKPath` est créé dans un `using` bloc. Cela `SKPath` consomme plus de mémoire que le `SKPath` objets dans les programmes précédents, ce qui suggère qu’un `using` bloc est la plus approprié supprimer les ressources non managées.


## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
