---
title: Polylignes et équations paramétriques
description: Cet article explique comment utiliser SkiaSharp pour afficher une ligne que vous pouvez définir avec des équations paramétriques et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.assetid: 85AEBB33-E954-4364-A6E1-808FAB197BEE
ms.technology: xamarin-skiasharp
author: davidbritch
ms.author: dabritch
ms.date: 03/10/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b435e99180791b64e0a8ad975527fb3cb5316b7d
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84140216"
---
# <a name="polylines-and-parametric-equations"></a>Polylignes et équations paramétriques

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Utilisez SkiaSharp pour afficher une ligne que vous pouvez définir avec des équations paramétriques_

Dans la section [**courbes et chemins d’accès SkiaSharp**](../curves/index.md) de ce guide, vous verrez les différentes méthodes qui [`SKPath`](xref:SkiaSharp.SKPath) définissent le rendu de certains types de courbes. Toutefois, il est parfois nécessaire de dessiner un type de courbe qui n’est pas directement prise en charge par `SKPath` . Dans ce cas, vous pouvez utiliser une polyligne (une collection de lignes connectées) pour dessiner une courbe que vous pouvez définir de façon mathématique. Si vous rendez les lignes suffisamment petites et suffisamment nombreuses, le résultat doit ressembler à une courbe. Cette spirale est en fait 3 600 peu de lignes :

![](polylines-images/spiralexample.png "A spiral")

En général, il est préférable de définir une courbe en termes de paire d’équations paramétrées. Voici des équations pour les coordonnées X et Y qui dépendent d’une troisième variable, parfois appelée `t` pour l’heure. Par exemple, les équations paramétriques suivantes définissent un cercle avec un rayon de 1 centré au point (0,0) pour *t* de 0 à 1 :

`x = cos(2πt)`

`y = sin(2πt)`

 Si vous souhaitez un rayon supérieur à 1, vous pouvez simplement multiplier les valeurs sinus et cosinus par ce rayon, et si vous devez déplacer le centre vers un autre emplacement, ajoutez ces valeurs :

`x = xCenter + radius·cos(2πt)`

`y = yCenter + radius·sin(2πt)`

Pour une ellipse avec les axes parallèles à l’horizontale et à la verticale, deux rayons sont impliqués :

`x = xCenter + xRadius·cos(2πt)`

`y = yCenter + yRadius·sin(2πt)`

Vous pouvez ensuite placer le code SkiaSharp équivalent dans une boucle qui calcule les différents points et les ajoute à un chemin d’accès. Le code SkiaSharp suivant crée un `SKPath` objet pour une ellipse qui remplit la surface d’affichage. La boucle parcourt les 360 degrés directement. Le centre correspond à la moitié de la largeur et de la hauteur de la surface d’affichage, et les deux rayons sont les suivants :

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

Il en résulte une ellipse définie par 360 peu de lignes. Lorsqu’il est rendu, il s’affiche en douceur.

Bien entendu, vous n’avez pas besoin de créer une ellipse à l’aide d’une polyligne, car `SKPath` comprend une `AddOval` méthode qui le fait pour vous. Mais vous souhaiterez peut-être dessiner un objet visuel qui n’est pas fourni par `SKPath` .

La page en **spirale ARCHIMEDEAN** a du code similaire au code de l’ellipse, mais avec une différence cruciale. Il effectue une boucle autour des 360 degrés du cercle 10 fois, en ajustant en permanence le rayon :

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

Le résultat est également appelé une *spirale arithmétique* , car le décalage entre chaque boucle est constant :

[![](polylines-images/archimedeanspiral-small.png "Triple screenshot of the Archimedean Spiral page")](polylines-images/archimedeanspiral-large.png#lightbox "Triple screenshot of the Archimedean Spiral page")

Notez que le `SKPath` est créé dans un `using` bloc. Cela `SKPath` consomme plus de mémoire que les `SKPath` objets des programmes précédents, ce qui suggère qu’un `using` bloc est plus approprié pour supprimer toutes les ressources non managées.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
