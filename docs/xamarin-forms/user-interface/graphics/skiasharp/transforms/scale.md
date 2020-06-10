---
titre : « description de la transformation d’échelle » : « l’article de thhis explore la transformation d’échelle SkiaSharp pour la mise à l’échelle des objets à différentes tailles et illustre cela avec un exemple de code ».
ms. Prod : xamarin ms. Technology : xamarin-skiasharp ms. AssetID : 54A43F3D-9DA8-44A7-9AE4-7E3025129A0B auteur : davidbritch ms. Author : dabritch ms. Date : 03/23/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="the-scale-transform"></a>La transformation d’échelle

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Détection de la transformation d’échelle SkiaSharp pour la mise à l’échelle d’objets à différentes tailles_

Comme vous l’avez vu dans [**l’article traduire la transformation**](translate.md) , la transformation de traduction peut déplacer un objet graphique d’un emplacement à un autre. En revanche, la transformation d’échelle modifie la taille de l’objet graphique :

![](scale-images/scaleexample.png "A tall word scaled in size")

La transformation de mise à l’échelle entraîne souvent le déplacement des coordonnées graphiques au fur et à mesure de leur agrandissement.

Précédemment, vous avez vu deux formules de transformation qui décrivent les effets des facteurs de traduction de `dx` et `dy` :

x' = x + DX

y' = y + dy

Les facteurs de mise à l’échelle de `sx` et `sy` sont multiplicatives plutôt que additives :

x' = SX · x

y' = sy · y

Les valeurs par défaut des facteurs de translation sont 0 ; les valeurs par défaut des facteurs d’échelle sont 1.

La `SKCanvas` classe définit quatre `Scale` méthodes. La première [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single)) méthode est pour les cas où vous souhaitez obtenir le même facteur d’échelle horizontale et verticale :

```csharp
public void Scale (Single s)
```

C’est ce que l’on appelle la mise à l’échelle *isotrope* &mdash; qui est identique dans les deux sens. La mise à l’échelle isotrope conserve les proportions de l’objet.

La deuxième [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single)) méthode vous permet de spécifier des valeurs différentes pour la mise à l’échelle horizontale et verticale :

```csharp
public void Scale (Single sx, Single sy)
```

Cela génère une mise à l’échelle *anisotrope* .
La troisième [`Scale`](xref:SkiaSharp.SKCanvas.Scale(SkiaSharp.SKPoint)) méthode combine les deux facteurs de mise à l’échelle en une seule `SKPoint` valeur :

```csharp
public void Scale (SKPoint size)
```

La quatrième `Scale` méthode sera décrite rapidement.

La page de mise à l' **échelle de base** illustre la `Scale` méthode. Le fichier [**BasicScalePage. Xaml**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml) contient deux `Slider` éléments qui vous permettent de sélectionner des facteurs de mise à l’échelle horizontale et verticale entre 0 et 10. Le fichier code-behind [**BasicScalePage.Xaml.cs**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/BasicScalePage.xaml.cs) utilise ces valeurs pour appeler `Scale` avant d’afficher un rectangle arrondi avec une ligne en pointillés et dimensionné pour s’ajuster à du texte dans l’angle supérieur gauche de la zone de dessin :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] {  7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        canvas.Scale((float)xScaleSlider.Value,
                     (float)yScaleSlider.Value);

        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);

        float margin = 10;
        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

Vous vous demandez peut-être : comment les facteurs de mise à l’échelle affectent-ils la valeur retournée par la `MeasureText` méthode de `SKPaint` ? La réponse est : pas du tout. `Scale`est une méthode de `SKCanvas` . Elle n’affecte pas les opérations que vous effectuez avec un `SKPaint` objet tant que vous n’utilisez pas cet objet pour restituer un élément sur le canevas.

Comme vous pouvez le voir, tout ce qui est dessiné après l' `Scale` appel augmente proportionnellement :

[![](scale-images/basicscale-small.png "Triple screenshot of the Basic Scale page")](scale-images/basicscale-large.png#lightbox "Triple screenshot of the Basic Scale page")

Le texte, la largeur de la ligne en pointillés, la longueur des tirets de cette ligne, l’arrondi des angles et la marge de 10 pixels entre les bords gauche et supérieur de la zone de dessin et le rectangle arrondi sont tous soumis aux mêmes facteurs de mise à l’échelle.

> [!IMPORTANT]
> Le plateforme Windows universelle ne restitue pas correctement le texte mis à l’échelle anisotropicly.

La mise à l’échelle anisotrope fait en sorte que la largeur du trait devienne différente pour les lignes alignées sur les axes horizontal et vertical. (Cela est également évident dans la première image de cette page.) Si vous ne souhaitez pas que la largeur du trait soit affectée par les facteurs de mise à l’échelle, affectez-lui la valeur 0 et il s’agit toujours d’un pixel de largeur, quel que soit le `Scale` paramètre.

La mise à l’échelle est relative au coin supérieur gauche de la zone de dessin. C’est peut-être exactement ce que vous voulez, mais ce n’est peut-être pas le tout. Supposons que vous souhaitez positionner le texte et le rectangle ailleurs sur le canevas et que vous souhaitez le mettre à l’échelle par rapport à son centre. Dans ce cas, vous pouvez utiliser la quatrième version de la [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) méthode, qui comprend deux paramètres supplémentaires pour spécifier le centre de mise à l’échelle :

```csharp
public void Scale (Single sx, Single sy, Single px, Single py)
```

Les `px` `py` paramètres et définissent un point qui est parfois appelé le *Centre de mise* à l’échelle, mais dans la documentation SkiaSharp, on parle de *point pivot*. Il s’agit d’un point relatif à l’angle supérieur gauche de la zone de dessin qui n’est pas affecté par la mise à l’échelle. Toute la mise à l’échelle se produit par rapport à ce centre.

La page de mise à l' [**échelle centrée**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/CenteredScalePage.xaml.cs) montre comment cela fonctionne. Le `PaintSurface` gestionnaire est similaire au programme de mise à l' **échelle de base** , sauf que la `margin` valeur est calculée pour centrer le texte horizontalement, ce qui implique que le programme fonctionne mieux en mode portrait :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear(SKColors.SkyBlue);

    using (SKPaint strokePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Red,
        StrokeWidth = 3,
        PathEffect = SKPathEffect.CreateDash(new float[] { 7, 7 }, 0)
    })
    using (SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Fill,
        Color = SKColors.Blue,
        TextSize = 50
    })
    {
        SKRect textBounds = new SKRect();
        textPaint.MeasureText(Title, ref textBounds);
        float margin = (info.Width - textBounds.Width) / 2;

        float sx = (float)xScaleSlider.Value;
        float sy = (float)yScaleSlider.Value;
        float px = margin + textBounds.Width / 2;
        float py = margin + textBounds.Height / 2;

        canvas.Scale(sx, sy, px, py);

        SKRect borderRect = SKRect.Create(new SKPoint(margin, margin), textBounds.Size);
        canvas.DrawRoundRect(borderRect, 20, 20, strokePaint);
        canvas.DrawText(Title, margin, -textBounds.Top + margin, textPaint);
    }
}
```

L’angle supérieur gauche du rectangle arrondi est positionné en `margin` pixels à partir de la gauche du canevas et en `margin` pixels du haut. Les deux derniers arguments de la `Scale` méthode sont définis sur ces valeurs plus la largeur et la hauteur du texte, qui est également la largeur et la hauteur du rectangle arrondi. Cela signifie que toute la mise à l’échelle est relative au centre de ce rectangle :

[![](scale-images/centeredscale-small.png "Triple screenshot of the Centered Scale page")](scale-images/centeredscale-large.png#lightbox "Triple screenshot of the Centered Scale page")

Les `Slider` éléments de ce programme ont une plage de &ndash; 10 à 10. Comme vous pouvez le voir, les valeurs négatives de la mise à l’échelle verticale (par exemple, sur l’écran Android au centre) entraînent le basculement des objets autour de l’axe horizontal qui traverse le centre de mise à l’échelle. Les valeurs négatives de la mise à l’échelle horizontale (par exemple, dans l’écran UWP à droite) entraînent l’inversion des objets autour de l’axe vertical qui traverse le centre de mise à l’échelle.

La version de la [`Scale`](xref:SkiaSharp.SKCanvas.Scale(System.Single,System.Single,System.Single,System.Single)) méthode avec des points de tableau croisé dynamique est un raccourci pour une série de trois `Translate` `Scale` appels et. Vous souhaiterez peut-être voir comment cela fonctionne en remplaçant la `Scale` méthode dans la page de mise à l' **échelle centrée** par les éléments suivants :

```csharp
canvas.Translate(-px, -py);
```

Il s’agit des valeurs négatives des coordonnées du point pivot.

Réexécutez le programme. Vous verrez que le rectangle et le texte sont décalés afin que le Centre se trouve dans le coin supérieur gauche de la zone de dessin. Vous pouvez le voir à peine. Les curseurs ne fonctionnent pas bien évidemment car le programme n’est pas du tout mis à l’échelle.

Ajoutez maintenant l’appel de base `Scale` (sans le centre de mise à l’échelle) *avant* cet `Translate` appel :

```csharp
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Si vous êtes familiarisé avec cet exercice dans d’autres systèmes de programmation graphique, vous pourriez penser que c’est incorrect, mais ce n’est pas le cas. Skia gère les appels de transformation successifs un peu différemment de ce que vous connaissez peut-être.

Avec les appels successifs `Scale` et `Translate` , le centre du rectangle arrondi est toujours dans l’angle supérieur gauche, mais vous pouvez maintenant le mettre à l’échelle par rapport au coin supérieur gauche de la zone de dessin, qui est également le centre du rectangle arrondi.

Maintenant, avant cet `Scale` appel, ajoutez un autre `Translate` appel avec les valeurs de centrage :

```csharp
canvas.Translate(px, py);
canvas.Scale(sx, sy);
canvas.Translate(–px, –py);
```

Cela ramène le résultat mis à l’échelle à la position d’origine. Ces trois appels sont équivalents à :

```csharp
canvas.Scale(sx, sy, px, py);
```

Les transformations individuelles sont composées de manière à ce que la formule de transformation totale soit :

 x' = SX · (x – PX) + PX

 y' = sy · (y – PY) + py

Gardez à l’esprit que les valeurs par défaut de `sx` et `sy` sont 1. Il est facile de vous convaincre que le point pivot (PX, PY) n’est pas transformé par ces formules. Il reste dans le même emplacement par rapport au canevas.

Lorsque vous combinez `Translate` et `Scale` appelez, l’ordre est important. Si le `Translate` vient après `Scale` , les facteurs de traduction sont mis à l’échelle de manière efficace par les facteurs de mise à l’échelle. Si le précède `Translate` `Scale` , les facteurs de traduction ne sont pas mis à l’échelle. Ce processus devient un peu plus clair (bien plus mathématique) lorsque l’objet des matrices de transformation est introduit.

La `SKPath` classe définit une propriété en lecture seule [`Bounds`](xref:SkiaSharp.SKPath.Bounds) qui retourne un qui `SKRect` définit l’étendue des coordonnées dans le chemin d’accès. Par exemple, lorsque la `Bounds` propriété est obtenue à partir du chemin d’accès hendecagram créé précédemment, les `Left` `Top` Propriétés et du rectangle sont approximativement de 100, les `Right` `Bottom` Propriétés et sont environ 100, et les `Width` `Height` Propriétés et sont environ 200. (La plupart des valeurs réelles sont un peu moins importantes, car les points des étoiles sont définis par un cercle avec un rayon de 100, mais seul le point supérieur est parallèle aux axes horizontaux ou verticaux).

La disponibilité de ces informations implique qu’il est possible de dériver les facteurs de mise à l’échelle et de traduction adaptés à la mise à l’échelle d’un tracé à la taille de la zone de dessin. La page de [**mise à l’échelle anisotrope**](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicScalingPage.cs) illustre cela à l’aide de l’étoile à 11 branches. Une échelle *anisotrope* signifie qu’elle est inégale dans les directions horizontale et verticale, ce qui signifie que l’étoile ne conservera pas ses proportions d’origine. Voici le code approprié dans le `PaintSurface` Gestionnaire :

```csharp
SKPath path = HendecagramPage.HendecagramPath;
SKRect pathBounds = path.Bounds;

using (SKPaint fillPaint = new SKPaint
{
    Style = SKPaintStyle.Fill,
    Color = SKColors.Pink
})
using (SKPaint strokePaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 3,
    StrokeJoin = SKStrokeJoin.Round
})
{
    canvas.Scale(info.Width / pathBounds.Width,
                 info.Height / pathBounds.Height);
    canvas.Translate(-pathBounds.Left, -pathBounds.Top);

    canvas.DrawPath(path, fillPaint);
    canvas.DrawPath(path, strokePaint);
}
```

Le `pathBounds` rectangle est obtenu vers le haut de ce code, puis utilisé ultérieurement avec la largeur et la hauteur de la zone de dessin dans l' `Scale` appel. Cet appel par lui-même met à l’échelle les coordonnées du chemin d’accès lorsqu’il est rendu par l' `DrawPath` appel, mais l’étoile est centrée dans l’angle supérieur droit de la zone de dessin. Il doit être décalé vers le côté et vers la gauche. Il s’agit du travail de l' `Translate` appel. Ces deux propriétés de `pathBounds` sont approximativement de 100, donc les facteurs de traduction sont d’environ 100. Étant donné que l' `Translate` appel est effectué après l' `Scale` appel, ces valeurs sont mises à l’échelle de manière efficace par les facteurs de mise à l’échelle, de sorte qu’ils déplacent le centre de l’étoile au centre de la zone de dessin :

[![](scale-images/anisotropicscaling-small.png "Triple screenshot of the Anisotropic Scaling page")](scale-images/anisotropicscaling-large.png#lightbox "Triple screenshot of the Anisotropic Scaling page")

Une autre façon de penser aux `Scale` appels et `Translate` consiste à déterminer l’effet en séquence inverse : l' `Translate` appel déplace le chemin d’accès afin qu’il devienne entièrement visible mais orienté dans le coin supérieur gauche du canevas. La `Scale` méthode rend ensuite cette étoile plus grande par rapport à l’angle supérieur gauche.

En fait, il semble que l’étoile soit un peu plus grand que la zone de dessin. Le problème est la largeur du trait. La `Bounds` propriété de `SKPath` indique les dimensions des coordonnées encodées dans le chemin d’accès, et c’est ce que le programme utilise pour le mettre à l’échelle. Lorsque le chemin d’accès est rendu avec une largeur de trait particulière, le chemin d’accès rendu est plus grand que le canevas.

Pour résoudre ce problème, vous devez compenser ce problème. Une approche simple dans ce programme consiste à ajouter l’instruction suivante juste avant l' `Scale` appel :

```csharp
pathBounds.Inflate(strokePaint.StrokeWidth / 2,
                   strokePaint.StrokeWidth / 2);
```

Cela augmente le `pathBounds` rectangle de 1,5 unités sur les quatre côtés. Il s’agit d’une solution raisonnable uniquement lorsque la jointure de traits est arrondie. Une jointure Mitre peut être plus longue et difficile à calculer.

Vous pouvez également utiliser une technique similaire avec du texte, comme le montre la page de **texte anisotrope** . Voici la partie pertinente du `PaintSurface` Gestionnaire de la [`AnisotropicTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/AnisotropicTextPage.cs) classe :

```csharp
using (SKPaint textPaint = new SKPaint
{
    Style = SKPaintStyle.Stroke,
    Color = SKColors.Blue,
    StrokeWidth = 0.1f,
    StrokeJoin = SKStrokeJoin.Round
})
{
    SKRect textBounds = new SKRect();
    textPaint.MeasureText("HELLO", ref textBounds);

    // Inflate bounds by the stroke width
    textBounds.Inflate(textPaint.StrokeWidth / 2,
                       textPaint.StrokeWidth / 2);

    canvas.Scale(info.Width / textBounds.Width,
                 info.Height / textBounds.Height);
    canvas.Translate(-textBounds.Left, -textBounds.Top);

    canvas.DrawText("HELLO", 0, 0, textPaint);
}
```

Il s’agit d’une logique similaire, et le texte s’étend sur la taille de la page en fonction du rectangle de limites de texte retourné à partir de `MeasureText` (ce qui est un peu plus grand que le texte réel) :

[![](scale-images/anisotropictext-small.png "Triple screenshot of the Anisotropic Test page")](scale-images/anisotropictext-large.png#lightbox "Triple screenshot of the Anisotropic Test page")

Si vous avez besoin de conserver les proportions des objets graphiques, vous souhaiterez utiliser la mise à l’échelle isotrope. La page de mise à l' **échelle isotrope** illustre cela pour l’étoile à 11 branches. Conceptuellement, les étapes permettant d’afficher un objet graphique au centre de la page avec une mise à l’échelle isotrope sont les suivantes :

- Traduisez le centre de l’objet graphique dans le coin supérieur gauche.
- Mettre à l’échelle l’objet en fonction du nombre minimal de dimensions horizontales et verticales de la page, divisée par les dimensions de l’objet graphique.
- Traduisez le centre de l’objet mis à l’échelle au centre de la page.

Le [`IsotropicScalingPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Transforms/IsotropicScalingPage.cs) effectue ces étapes dans l’ordre inverse avant d’afficher l’étoile :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    SKPath path = HendecagramArrayPage.HendecagramPath;
    SKRect pathBounds = path.Bounds;

    using (SKPaint fillPaint = new SKPaint())
    {
        fillPaint.Style = SKPaintStyle.Fill;

        float scale = Math.Min(info.Width / pathBounds.Width,
                               info.Height / pathBounds.Height);

        for (int i = 0; i <= 10; i++)
        {
            fillPaint.Color = new SKColor((byte)(255 * (10 - i) / 10),
                                          0,
                                          (byte)(255 * i / 10));
            canvas.Save();
            canvas.Translate(info.Width / 2, info.Height / 2);
            canvas.Scale(scale);
            canvas.Translate(-pathBounds.MidX, -pathBounds.MidY);
            canvas.DrawPath(path, fillPaint);
            canvas.Restore();

            scale *= 0.9f;
        }
    }
}
```

Le code affiche également l’étoile 10 autres fois, chaque fois qui réduit le facteur d’échelle de 10% et change progressivement la couleur du rouge au bleu :

[![](scale-images/isotropicscaling-small.png "Triple screenshot of the Isotropic Scaling page")](scale-images/isotropicscaling-large.png#lightbox "Triple screenshot of the Isotropic Scaling page")

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
