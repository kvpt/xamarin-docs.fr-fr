---
title: Filtres de couleur SkiaSharp
description: Utiliser des filtres de couleur pour convertir les couleurs avec des transformations ou des tables.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
ms.openlocfilehash: 5aa8b2e85d5a7d547af5333dcaf350025b86cc26
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68647695"
---
# <a name="skiasharp-color-filters"></a>Filtres de couleur SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Filtres de couleur peut traduire les couleurs dans une image bitmap (ou autre image) aux autres couleurs pour les effets tels que Postérisation :

![Exemple de filtres de couleur](color-filters-images/ColorFiltersExample.png "exemple de filtres de couleur")

Pour utiliser un filtre de couleur, définissez la [ `ColorFilter` ](xref:SkiaSharp.SKPaint.ColorFilter) propriété du `SKPaint` à un objet de type [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) créé par l’une des méthodes statiques de cette classe. Cet article montre : 

- une transformation de couleur créée avec le [ `CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) (méthode).
- une table des couleurs créée avec le [ `CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) (méthode).

## <a name="the-color-transform"></a>La transformation de couleurs

La transformation de couleur implique l’utilisation d’une matrice pour modifier les couleurs. Comme la plupart des systèmes de graphismes 2D, SkiaSharp utilise des matrices principalement pour la transformation des points de coordonnées en tant qu’iscussed dans l’article [ **de transformations de matrice dans SkiaSharp**](../transforms/matrix.md). Le [ `SKColorFilter` ](xref:SkiaSharp.SKColorFilter) prend également en charge les transformations de matrice, mais les transformations de matrice des couleurs RVB. Familiarisé avec les concepts de la matrice est nécessaire de comprendre ces transformations de couleur. 

La matrice de transformation de couleurs a une dimension de quatre lignes et cinq colonnes :

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Il transforme une couleur de source RVB (R, G, B, A) à la couleur de destination (R », G', B ','). 

En préparation de la multiplication de matrice, la couleur de la source est convertie en une matrice 5 x 1 :

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Ces valeurs R, G, B et A sont les octets d’origine comprises entre 0 et 255. Ils sont _pas_ normalisées en valeurs à virgule flottante dans la plage 0 à 1.

La cellule supplémentaire est requise pour un facteur de translation. Ce comportement est analogue à l’utilisation d’une matrice 3 x 3 pour transformer des points de coordonnées à deux dimensions, comme décrit dans la section [ **le motif de la matrice 3 x 3** ](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) dans l’article sur l’utilisation des matrices de transformation coordonner les points.

La matrice 4 × 5 est multipliée par la matrice 5 × 1, et le produit est une matrice 4 × 1 avec la couleur transformée :

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Voici les formules distinctes pour R », G', 'B et A':

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

La plupart de la matrice se compose de facteurs de multiplication qui se trouvent généralement dans la plage de 0 à 2. Toutefois, la dernière colonne (M15 via M45) contient des valeurs qui sont ajoutés dans les formules. Ces valeurs comprises généralement entre 0 et 255. Les résultats sont limitées entre les valeurs 0 et 255.

La matrice d’identité est :

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Ne subit aucune modification aux couleurs. Les formules de transformation sont :

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

La cellule M44 est très importante car il préserve l’opacité. Il est généralement le cas que M41, M42 et M43 sont nuls, étant donné que vous ne souhaitez probablement pas opacité doit être basé sur les valeurs rouges, vert et bleus. Mais si M44 est zéro, un « est égal à zéro, et rien ne sera visible.

Une des utilisations plus courantes de la matrice des couleurs consiste à convertir une image bitmap de couleur dans une image bitmap en nuances de gris. Cela implique une formule pour une moyenne pondérée des valeurs rouges, vert et bleus. Pour les affichages vidéo à l’aide de l’espace de couleurs sRVB (« standard rouge vert bleu »), cette formule est :

nuances de gris ne = 0.2126· R + 0.7152· G + 0.0722· B

Pour convertir une image bitmap de couleur dans une image bitmap en nuances de gris, R », G', et les résultats de B' doivent tous égal à cette même valeur. La matrice est :

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Il n’existe aucun type de données SkiaSharp qui correspond à cette matrice. Au lieu de cela, vous devez représenter la matrice en tant que tableau de 20 `float` valeurs dans l’ordre ligne : première ligne, puis deuxième ligne et ainsi de suite.

La méthode statique [ `SKColorFilter.CreateColorMatrix` ](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) méthode présente la syntaxe suivante :

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

où `matrix` est un tableau du 20 `float` valeurs. Lors de la création d’un tableau dans C#, il est facile de mettre en forme les nombres afin qu’ils ressemblent à la matrice 4 × 5. Cela est illustré dans le **en nuances de gris matrice** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple :

```csharp
public class GrayScaleMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(CenteredTilesPage),
                        "SkiaSharpFormsDemos.Media.Banana.jpg");

    public GrayScaleMatrixPage()
    {
        Title = "Gray-Scale Matrix";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0.21f, 0.72f, 0.07f, 0, 0,
                    0,     0,     0,     1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Le `DrawBitmap` provient de la méthode utilisée dans ce code la **BitmapExtension.cs** fichier inclus avec le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple. 

Voici le résultat en cours d’exécution sur iOS, Android et plateforme Windows universelle :

[![Matrice de nuances de gris](color-filters-images/GrayScaleMatrix.png "matrice de nuances de gris")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Faites attention pour la valeur dans la quatrième ligne et la quatrième colonne. C’est le facteur essentiel est multiplié par la valeur A de la couleur d’origine pour l’une « valeur de la couleur transformée. Si cette cellule est égal à zéro, rien ne s’affichera et le problème peut être difficile à localiser.

Lorsque vous testez des matrices des couleurs, vous pouvez traiter à partir de la perspective de la source ou la perspective de la destination de la transformation. Comment les pixel rouge de la source doit contribuer aux pixels de rouges, vert et bleus de la destination ? Qui est déterminée par les valeurs dans la première _colonne_ de la matrice. Vous pouvez également comment doit le pixel de destination rouge affecté par les pixels de rouges, vert et bleus de la source ? Qui est déterminée par le premier _ligne_ de la matrice.

Pour obtenir quelques idées sur la façon d’utiliser des transformations de couleur, consultez la [ **recoloriage des Images** ](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) pages. La discussion porte sur Windows Forms et la matrice est un format différent, mais les concepts sont identiques.

Le **Pastel matrice** calcule le pixel de destination rouge en atténuer le pixel source rouge légèrement mettant l’accent sur les pixels rouges et vertes. Ce processus se produit de la même façon pour les pixels de verts et bleus :

```csharp
public class PastelMatrixPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PastelMatrixPage),
                        "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

    public PastelMatrixPage()
    {
        Title = "Pastel Matrix";

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateColorMatrix(new float[]
                {
                    0.75f, 0.25f, 0.25f, 0, 0,
                    0.25f, 0.75f, 0.25f, 0, 0,
                    0.25f, 0.25f, 0.75f, 0, 0,
                    0, 0, 0, 1, 0
                });

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Le résultat consiste à désactiver l’intensité des couleurs comme vous pouvez le voir ici :

[![Matrice pastel](color-filters-images/PastelMatrix.png "Pastel matrice")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tables de couleurs

La méthode statique [ `SKColorFilter.CreateTable` ](xref:SkiaSharp.SKColorFilter.CreateTable*) méthode est disponible en deux versions :

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Les tableaux contiennent toujours les entrées de 256. Dans la `CreateTable` méthode avec une table, la table est utilisée pour les composants rouges, vert et bleus. Il s’agit d’une table de recherche simple: Si la couleur source est (r, G, B) et que la couleur de destination est (r, b, g), les composants de destination sont obtenus par indexation `table` avec les composants sources:

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

Dans la deuxième méthode, chacun des quatre composants de couleur peut avoir une table des couleurs distinctes, ou les mêmes tables de couleur peuvent être partagés entre deux ou plusieurs composants.

Si vous souhaitez définir un des arguments à la seconde `CreateTable` méthode à une table des couleurs qui contient les valeurs 0 et 255 dans la séquence, vous pouvez utiliser `null` à la place. Très souvent la `CreateTable` appel a un `null` premier argument pour le canal alpha.

Dans la section sur **Postérisation** dans l’article sur [bits de pixel de bitmap de l’accès à un SkiaSharp](../bitmaps/pixel-bits.md#posterization), vous avez vu comment modifier les bits de pixel individuelles d’une image bitmap pour réduire sa résolution de couleur. Il s’agit d’une technique appelée _Postérisation_. 

Vous pouvez également Postérisation un bitmap avec une table des couleurs. Le constructeur de la **Table Postérisation** page crée une table des couleurs qui mappe son index à un octet avec le bas 6 bits a la valeur zéro :

```csharp
public class PosterizeTablePage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(
                        typeof(PosterizeTablePage),
                        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    byte[] colorTable = new byte[256];

    public PosterizeTablePage()
    {
        Title = "Posterize Table";

        // Create color table
        for (int i = 0; i < 256; i++)
        {
            colorTable[i] = (byte)(0xC0 & i);
        }

        SKCanvasView canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();

        using (SKPaint paint = new SKPaint())
        {
            paint.ColorFilter =
                SKColorFilter.CreateTable(null, null, colorTable, colorTable);

            canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform, paint: paint);
        }
    }
}
```

Le programme choisit d’utiliser cette table des couleurs uniquement pour les canaux de verts et bleus. Le canal rouge continue à avoir pleine résolution :

[![Postérisation Table](color-filters-images/PosterizeTable.png "Postérisation Table")](color-filters-images/PosterizeTable-Large.png#lightbox)

Vous pouvez utiliser des tables de couleurs différentes pour les canaux de couleur différente pour différents effets. 

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
