---
title: Filtres de couleurs SkiaSharp
description: Utilisez des filtres de couleurs pour convertir des couleurs avec des transformations ou des tables.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 774E7B55-AEC8-4F12-B657-1C0CEE01AD63
author: davidbritch
ms.author: dabritch
ms.date: 08/28/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b9c89d4d426884d678e77687ffa226cced97be58
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136381"
---
# <a name="skiasharp-color-filters"></a>Filtres de couleurs SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Les filtres de couleur peuvent traduire les couleurs d’une image bitmap (ou d’une autre image) en d’autres couleurs pour des effets tels que l’affiche :

![Exemple de filtres de couleur](color-filters-images/ColorFiltersExample.png "Exemple de filtres de couleur")

Pour utiliser un filtre de couleur, affectez [`ColorFilter`](xref:SkiaSharp.SKPaint.ColorFilter) à la propriété de la valeur `SKPaint` un objet de type [`SKColorFilter`](xref:SkiaSharp.SKColorFilter) créé par l’une des méthodes statiques de cette classe. Cet article présente les éléments suivants : 

- transformation de couleur créée à l’aide de la [`CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) méthode.
- table de couleurs créée avec la [`CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) méthode.

## <a name="the-color-transform"></a>Transformation de couleur

La transformation de couleur implique l’utilisation d’une matrice pour modifier les couleurs. Comme la plupart des systèmes graphiques 2D, SkiaSharp utilise des matrices principalement pour transformer des points de coordonnées en iscussed dans les [**transformations de matrice d’article dans SkiaSharp**](../transforms/matrix.md). [`SKColorFilter`](xref:SkiaSharp.SKColorFilter)Prend également en charge les transformations de matrice, mais la matrice transforme les couleurs RVB. Une certaine connaissance des concepts de matrice est nécessaire pour comprendre ces transformations de couleurs. 

La matrice de transformation des couleurs a une dimension de quatre lignes et cinq colonnes :

<pre>
| M11 M12 M13 M14 M15 |
| M21 M22 M23 M24 M25 |
| M31 M32 M33 M34 M35 |
| M41 M42 M43 M44 M45 |
</pre>

Il transforme une couleur source RVB (R, G, B, A) en couleur de destination (R, G, B, A). 

En vue de la multiplication de la matrice, la couleur source est convertie en matrice 5 × 1 :

<pre>
| R |
| G |
| B |
| A |
| 1 |
</pre>

Ces valeurs R, G, B et A sont les octets d’origine allant de 0 à 255. Elles ne sont _pas_ normalisées en valeurs à virgule flottante dans la plage de 0 à 1.

La cellule supplémentaire est requise pour un facteur de translation. Cela est similaire à l’utilisation d’une matrice 3 × 3 pour transformer des points de coordonnées à deux dimensions, comme décrit dans la section [**la raison de la matrice 3-par-3**](../transforms/matrix.md#the-reason-for-the-3-by-3-matrix) de l’article sur l’utilisation de matrices pour la transformation des points de coordonnées.

La matrice 4 × 5 est multipliée par la matrice 5 × 1, et le produit est une matrice 4 × 1 avec la couleur transformée :

<pre>
| M11 M12 M13 M14 M15 |    | R |   | R' |
| M21 M22 M23 M24 M25 |    | G |   | G' |
| M31 M32 M33 M34 M35 |  × | B | = | B' |
| M41 M42 M43 M44 M45 |    | A |   | A' |
                           | 1 |
</pre>

Voici les formules distinctes pour R', G', B’et' :

`R' = M11·R + M12·G + M13·B + M14·A + M15` 

`G' = M21·R + M22·G + M23·B + M24·A + M25` 

`B' = M31·R + M32·G + M33·B + M34·A + M35` 

`A' = M41·R + M42·G + M43·B + M44·A + M45` 

La majeure partie de la matrice est constituée de facteurs de multiplication qui se trouvent généralement dans la plage de 0 à 2. Toutefois, la dernière colonne (M15 à M45) contient des valeurs ajoutées dans les formules. Ces valeurs sont généralement comprises entre 0 et 255. Les résultats sont bloqués entre les valeurs 0 et 255.

La matrice d’identité est la suivante :

<pre>
| 1 0 0 0 0 |
| 0 1 0 0 0 |
| 0 0 1 0 0 |
| 0 0 0 1 0 |
</pre>

Cela n’entraîne aucune modification des couleurs. Les formules de transformation sont les suivantes :

`R' = R` 

`G' = G`

`B' = B`

`A' = A`

La cellule M44 est très importante, car elle conserve l’opacité. C’est généralement le cas si M41, M42 et M43 sont tous nuls, car il est probable que vous ne souhaitiez pas que l’opacité soit basée sur les valeurs rouge, vert et bleu. Toutefois, si M44 est égal à zéro, un «» est égal à zéro, et rien n’est visible.

L’une des utilisations les plus courantes de la matrice de couleurs consiste à convertir une image bitmap de couleur en image bitmap de nuances de gris. Cela implique une formule pour une moyenne pondérée des valeurs rouge, vert et bleu. Pour les affichages vidéo à l’aide de l’espace de couleurs sRGB (« Red Green vert Blue »), cette formule est la suivante :

ombrage gris = 0,2126 · R + 0,7152 · G + 0,0722 · P

Pour convertir une image bitmap de couleur en bitmap de l’échelle grise, les résultats R', G’et B’doivent tous être égaux à la même valeur. La matrice est la suivante :

<pre>
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0.21 0.72 0.07 0 0 |
| 0    0    0    1 0 |
</pre>

Il n’existe aucun type de données SkiaSharp qui correspond à cette matrice. Au lieu de cela, vous devez représenter la matrice sous la forme d’un tableau de 20 `float` valeurs dans l’ordre des lignes : la première ligne, la deuxième ligne, et ainsi de suite.

La [`SKColorFilter.CreateColorMatrix`](xref:SkiaSharp.SKColorFilter.CreateColorMatrix*) méthode statique a la syntaxe suivante :

```csharp
public static SKColorFilter CreateColorMatrix (float[] matrix);
```

où `matrix` est un tableau des 20 `float` valeurs. Lors de la création du tableau en C#, il est facile de mettre en forme les nombres afin qu’ils ressemblent à la matrice 4 × 5. Cela est illustré dans la page matrice de nuances de **gris** de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) :

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

La `DrawBitmap` méthode utilisée dans ce code provient du fichier **BitmapExtension.cs** inclus avec l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) . 

Voici le résultat s’exécutant sur iOS, Android et plateforme Windows universelle :

[![Matrice de nuances de gris](color-filters-images/GrayScaleMatrix.png "Matrice de nuances de gris")](color-filters-images/GrayScaleMatrix-Large.png#lightbox)

Regardez la valeur de la quatrième ligne et de la quatrième colonne. C’est le facteur crucial qui est multiplié par la valeur de la couleur d’origine pour la valeur de la couleur transformée. Si cette cellule est égale à zéro, rien ne s’affiche et le problème peut être difficile à localiser.

Lorsque vous expérimentez des matrices de couleurs, vous pouvez traiter la transformation soit du point de vue de la source, soit du point de vue de la destination. Comment le pixel rouge de la source doit-il contribuer aux pixels rouge, vert et bleu de la destination ? Cela est déterminé par les valeurs de la première _colonne_ de la matrice. En guise d’alternative, comment le pixel rouge de destination doit-il être affecté par les pixels rouge, vert et bleu de la source ? Cela est déterminé par la première _ligne_ de la matrice.

Pour obtenir des idées sur l’utilisation des transformations de couleurs, consultez la page [**recoloriage des images**](https://docs.microsoft.com/dotnet/framework/winforms/advanced/recoloring-images) . La discussion concerne Windows Forms et la matrice est un format différent, mais les concepts sont identiques.

La **matrice pastel** calcule le pixel rouge de destination en atténuant le pixel rouge source et en mettant légèrement en évidence les pixels rouges et verts. Ce processus se produit de la même façon pour les pixels vert et bleu :

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

Le résultat est de désactiver l’intensité des couleurs comme vous pouvez le voir ici :

[![Matrice pastel](color-filters-images/PastelMatrix.png "Matrice pastel")](color-filters-images/PastelMatrix-Large.png#lightbox)

## <a name="color-tables"></a>Tables de couleurs

La [`SKColorFilter.CreateTable`](xref:SkiaSharp.SKColorFilter.CreateTable*) méthode statique est disponible en deux versions :

```csharp
public static SKColorFilter CreateTable (byte[] table);

public static SKColorFilter CreateTable (byte[] tableA, byte[] tableR, byte[] tableG, byte[] tableB);
```

Les tableaux contiennent toujours 256 entrées. Dans la `CreateTable` méthode avec une table, la même table est utilisée pour les composants rouge, vert et bleu. Il s’agit d’une table de recherche simple : si la couleur source est (R, G, B) et que la couleur de destination est (R, B, G), les composants de destination sont obtenus par `table` l’indexation avec les composants sources :

`R' = table[R]`

`G' = table[G]`

`B' = table[B]`

Dans la deuxième méthode, chacun des quatre composants de couleur peut avoir une table de couleurs distincte, ou les mêmes tables de couleur peuvent être partagées entre deux composants ou plus.

Si vous souhaitez définir l’un des arguments de la seconde `CreateTable` méthode sur une table des couleurs qui contient les valeurs de 0 à 255 dans l’ordre, vous pouvez utiliser à la `null` place. Très souvent `CreateTable` , l’appel a un `null` premier argument pour le canal alpha.

Dans la **section relative à l’affichage de l'** article sur l' [accès aux bits de pixel SkiaSharp bitmap](../bitmaps/pixel-bits.md#posterization), vous avez appris à modifier les bits de pixel individuels d’une image bitmap afin de réduire sa résolution de couleurs. Il s’agit d’une technique appelée _postérisation_. 

Vous pouvez également poster une image bitmap avec une table des couleurs. Le constructeur de la page de **table postérisation** crée une table de couleurs qui mappe son index à un octet dont les 6 bits inférieurs sont définis à zéro :

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

Le programme choisit d’utiliser cette table des couleurs uniquement pour les canaux vert et bleu. Le canal rouge continue d’avoir une résolution complète :

[![Poster la table](color-filters-images/PosterizeTable.png "Poster la table")](color-filters-images/PosterizeTable-Large.png#lightbox)

Vous pouvez utiliser différentes tables de couleurs pour les différents canaux de couleurs pour différents effets. 

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
