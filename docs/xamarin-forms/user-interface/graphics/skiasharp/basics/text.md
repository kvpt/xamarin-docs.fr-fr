---
titre : « intégration du texte et des graphiques » Description : « cet article explique comment déterminer la taille de la chaîne de texte rendue pour intégrer du texte à des graphiques SkiaSharp dans des Xamarin.Forms applications et illustre cela avec un exemple de code ».
ms. Prod : xamarin ms. Technology : xamarin-skiasharp ms. AssetID : A0B5AC82-7736-4AD8-AA16-FE43E18D203C auteur : davidbritch ms. Author : dabritch ms. Date : 03/10/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="integrating-text-and-graphics"></a>Intégration de texte et de graphisme

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Voir comment déterminer la taille de la chaîne de texte rendue pour intégrer du texte à des graphiques SkiaSharp_

Cet article montre comment mesurer du texte, mettre à l’échelle le texte à une taille particulière et intégrer du texte à d’autres graphiques :

![](text-images/textandgraphicsexample.png "Text surrounded by rectangles")

Cette image comprend également un rectangle arrondi. La `Canvas` classe SkiaSharp comprend des [`DrawRect`](xref:SkiaSharp.SKCanvas.DrawRect*) méthodes permettant de dessiner un rectangle et des [`DrawRoundRect`](xref:SkiaSharp.SKCanvas.DrawRoundRect*) méthodes pour dessiner un rectangle avec des angles arrondis. Ces méthodes permettent de définir le rectangle comme une `SKRect` valeur ou d’autres façons.

La page de **texte encadrée** centre une chaîne de texte brève sur la page et l’entoure d’un cadre composé d’une paire de rectangles arrondis. La [`FramedTextPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/FramedTextPage.cs) classe montre comment effectuer cette opération.

Dans SkiaSharp, vous utilisez la `SKPaint` classe pour définir des attributs de texte et de police, mais vous pouvez également l’utiliser pour obtenir la taille rendue du texte. Le début du gestionnaire d' `PaintSurface` événements suivant appelle deux `MeasureText` méthodes différentes. Le premier [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String)) appel a un `string` argument simple et retourne la largeur en pixels du texte en fonction des attributs de police actuels. Le programme calcule ensuite une nouvelle `TextSize` propriété de l' `SKPaint` objet en fonction de cette largeur rendue, de la `TextSize` propriété actuelle et de la largeur de la zone d’affichage. Ce calcul est destiné à être défini de `TextSize` sorte que la chaîne de texte soit rendue à 90% de la largeur de l’écran :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string str = "Hello SkiaSharp!";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Color = SKColors.Chocolate
    };

    // Adjust TextSize property so text is 90% of screen width
    float textWidth = textPaint.MeasureText(str);
    textPaint.TextSize = 0.9f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(str, ref textBounds);
    ...
}
```

Le deuxième [`MeasureText`](xref:SkiaSharp.SKPaint.MeasureText(System.String,SkiaSharp.SKRect@)) appel a un `SKRect` argument, donc il obtient à la fois une largeur et une hauteur du texte rendu. La `Height` propriété de cette `SKRect` valeur dépend de la présence de majuscules, de hampes ascendantes et de descendants dans la chaîne de texte. `Height`Des valeurs différentes sont indiquées pour les chaînes de texte « MOM », « Cat » et « Dog », par exemple.

Les `Left` `Top` Propriétés et de la `SKRect` structure indiquent les coordonnées de l’angle supérieur gauche du texte rendu si le texte est affiché par un `DrawText` appel avec des positions X et Y égales à 0. Par exemple, quand ce programme s’exécute sur un simulateur iPhone 7, `TextSize` la valeur 90,6254 est affectée en tant que résultat du calcul suivant le premier appel à `MeasureText` . La `SKRect` valeur obtenue à partir du deuxième appel à `MeasureText` a les valeurs de propriétés suivantes :

- `Left`= 6
- `Top` = &ndash;68
- `Width`= 664,8214
- `Height`= 88 ;

Gardez à l’esprit que les coordonnées X et Y que vous transmettez à la `DrawText` méthode spécifient le côté gauche du texte au niveau de la ligne de base. La `Top` valeur indique que le texte étend 68 pixels au-dessus de cette ligne de base et (en le soustrayant 68 de 88) de 20 pixels sous la ligne de base. La `Left` valeur 6 indique que le texte commence six pixels à droite de la valeur X dans l' `DrawText` appel. Cela permet un espacement entre les caractères standard. Si vous souhaitez afficher le texte dans l’angle supérieur gauche de l’affichage, transmettez les valeurs négatives de ces `Left` `Top` valeurs et comme les coordonnées X et Y de `DrawText` , dans cet exemple, &ndash; 6 et 68.

La `SKRect` structure définit plusieurs propriétés et méthodes pratiques, dont certaines sont utilisées dans le reste du `PaintSurface` Gestionnaire. Les `MidX` `MidY` valeurs et indiquent les coordonnées du centre du rectangle. (Dans l’exemple iPhone 7, ces valeurs sont 338,4107 et &ndash; 24.) Le code suivant utilise ces valeurs pour le calcul le plus facile de coordonnées pour centrer le texte à l’écran :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(str, xText, yText, textPaint);
    ...
}
```

La `SKImageInfo` structure info définit également une [`Rect`](xref:SkiaSharp.SKImageInfo.Rect) propriété de type `SKRect` , ce qui vous permet également de calculer `xText` et de `yText` procéder comme suit :

```csharp
float xText = info.Rect.MidX - textBounds.MidX;
float yText = info.Rect.MidY - textBounds.MidY;
```

Le `PaintSurface` Gestionnaire se termine par deux appels à `DrawRoundRect` , qui requièrent tous deux des arguments de `SKRect` . Cette `SKRect` valeur est basée sur la `SKRect` valeur obtenue à partir de la `MeasureText` méthode, mais elle ne peut pas être identique. Tout d’abord, il doit être un peu plus grand afin que le rectangle arrondi ne dessine pas sur les bords du texte. Ensuite, il doit être déplacé dans l’espace afin que les `Left` valeurs et `Top` correspondent au coin supérieur gauche où le rectangle doit être positionné. Ces deux tâches sont effectuées par les [`Offset`](xref:SkiaSharp.SKRect.Offset*) [`Inflate`](xref:SkiaSharp.SKRect.Inflate*) méthodes et définies par `SKRect` :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    // Create a new SKRect object for the frame around the text
    SKRect frameRect = textBounds;
    frameRect.Offset(xText, yText);
    frameRect.Inflate(10, 10);

    // Create an SKPaint object to display the frame
    SKPaint framePaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 5,
        Color = SKColors.Blue
    };

    // Draw one frame
    canvas.DrawRoundRect(frameRect, 20, 20, framePaint);

    // Inflate the frameRect and draw another
    frameRect.Inflate(10, 10);
    framePaint.Color = SKColors.DarkBlue;
    canvas.DrawRoundRect(frameRect, 30, 30, framePaint);
}
```

Après cela, le reste de la méthode est simple. Elle crée un autre `SKPaint` objet pour les bordures et appelle `DrawRoundRect` deux fois. Le deuxième appel utilise un rectangle gonflé par 10 pixels sur deux. Le premier appel spécifie un rayon de l’angle de 20 pixels. Le second a un rayon d’angle de 30 pixels, donc ils semblent être parallèles :

 [![](text-images/framedtext-small.png "Triple screenshot of the Framed Text page")](text-images/framedtext-large.png#lightbox "Triple screenshot of the Framed Text page")

Vous pouvez faire pivoter votre téléphone ou votre simulateur pour voir la taille du texte et de l’image augmenter.

Si vous devez uniquement centrer du texte à l’écran, vous pouvez le faire approximativement sans mesurer le texte. Au lieu de cela, affectez [`TextAlign`](xref:SkiaSharp.SKPaint.TextAlign) à la propriété de `SKPaint` la valeur du membre de l’énumération [`SKTextAlign.Center`](xref:SkiaSharp.SKTextAlign) . La coordonnée X que vous spécifiez dans la `DrawText` méthode indique ensuite où le centre horizontal du texte est positionné. Si vous transmettez le milieu de l’écran à la `DrawText` méthode, le texte est centré horizontalement et *presque* verticalement, car la ligne de base est centrée verticalement.

Le texte peut être traité de la même façon que n’importe quel autre objet graphique. Une option simple consiste à afficher le contour des caractères de texte :

[![](text-images/outlinedtext-small.png "Triple screen shot of the Outlined Text page")](text-images/outlinedtext-large.png#lightbox "Triple screenshot of the Outlined Text page")

Pour ce faire, il suffit de remplacer la valeur `Style` par défaut de la propriété normal de l' `SKPaint` objet par, en `SKPaintStyle.Fill` `SKPaintStyle.Stroke` spécifiant une largeur de trait. Le `PaintSurface` Gestionnaire de la page de **texte avec contour** montre comment procéder :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    string text = "OUTLINE";

    // Create an SKPaint object to display the text
    SKPaint textPaint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        StrokeWidth = 1,
        FakeBoldText = true,
        Color = SKColors.Blue
    };

    // Adjust TextSize property so text is 95% of screen width
    float textWidth = textPaint.MeasureText(text);
    textPaint.TextSize = 0.95f * info.Width * textPaint.TextSize / textWidth;

    // Find the text bounds
    SKRect textBounds = new SKRect();
    textPaint.MeasureText(text, ref textBounds);

    // Calculate offsets to center the text on the screen
    float xText = info.Width / 2 - textBounds.MidX;
    float yText = info.Height / 2 - textBounds.MidY;

    // And draw the text
    canvas.DrawText(text, xText, yText, textPaint);
}
```

Un autre objet graphique courant est l’image bitmap. C’est une grande rubrique traitée en détail dans la section [**SkiaSharp bitmaps**](../bitmaps/index.md), mais l’article suivant, les [**concepts de base de la bitmap dans SkiaSharp**](bitmaps.md), fournit une introduction plus rapide.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
