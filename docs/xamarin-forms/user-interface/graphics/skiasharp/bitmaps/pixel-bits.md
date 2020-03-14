---
title: L’accès aux bits de pixel de bitmap SkiaSharp
description: Découvrez les diverses techniques d’accès et modification des bits de pixel de bitmaps de SkiaSharp.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: DBB58522-F816-4A8C-96A5-E0236F16A5C6
author: davidbritch
ms.author: dabritch
ms.date: 07/11/2018
ms.openlocfilehash: 6c066f89dc8f558a9154138bf38ad4326fe21291
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305359"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>L’accès aux bits de pixel de bitmap SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu dans l’article [**enregistrement de bitmaps SkiaSharp**](saving.md)dans des fichiers, les bitmaps sont généralement stockés dans des fichiers dans un format compressé, tel que JPEG ou png. En revanche, une image bitmap de SkiaSharp stockée en mémoire n’est pas compressée. Il est stocké comme une série séquentielle de pixels. Ce format non compressé facilite le transfert de bitmaps à une surface d’affichage.

Le bloc de mémoire occupé par une image bitmap SkiaSharp est organisé de manière très simple : il commence par la première ligne de pixels, de gauche à droite et puis se poursuit avec la deuxième ligne. Pour les images bitmap en couleurs, chaque pixel se compose de quatre octets, ce qui signifie que l’espace de mémoire totale requise par l’image bitmap est quatre fois le produit de sa largeur et sa hauteur.

Cet article décrit comment une application peut accéder à ces pixels, soit directement accéder au bloc de mémoire de pixel de la bitmap, ou indirectement. Dans certains cas, un programme peut souhaiter analyser les pixels d’une image et de construire un histogramme quelconque. En général, applications peuvent construire des images uniques en créant de façon algorithmique les pixels qui composent l’image bitmap :

![Exemples de bits de pixel](pixel-bits-images/PixelBitsSample.png "Exemple de bits de pixel")

## <a name="the-techniques"></a>Les techniques

SkiaSharp fournit plusieurs techniques permettant d’accéder aux bits de pixel d’une image bitmap. Votre choix est généralement un compromis entre commodité (qui est liée à la maintenance et de faciliter le débogage) et les performances de codage. Dans la plupart des cas, vous allez utiliser l’une des méthodes et propriétés suivantes de `SKBitmap` pour accéder aux pixels de la bitmap :

- Les méthodes `GetPixel` et `SetPixel` vous permettent d’obtenir ou de définir la couleur d’un pixel unique.
- La propriété `Pixels` obtient un tableau de couleurs de pixels pour l’ensemble de la bitmap, ou définit le tableau de couleurs.
- `GetPixels` retourne l’adresse de la mémoire en pixels utilisée par la bitmap.
- `SetPixels` remplace l’adresse de la mémoire en pixels utilisée par l’image bitmap.

Vous pouvez considérer les deux premières techniques comme « niveau élevé » et les deux en tant que « niveau faible ». Il existe d’autres méthodes et propriétés que vous pouvez utiliser, mais ce sont les plus précieuses.

Pour vous permettre de voir les différences de performances entre ces techniques, l’application [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) contient une page nommée **bitmap de dégradé** qui crée une image bitmap avec des pixels qui combinent des nuances de rouge et de bleu pour créer un dégradé. Le programme crée huit copies différentes de cette image bitmap, tout à l’aide de différentes techniques permettant de définir les pixels du bitmap. Chacun de ces huit bitmaps est créé dans une méthode distincte qui également définit une brève description de la technique et calcule le temps nécessaire pour définir tous les pixels. Chaque méthode effectue une itération sur la logique de la valeur de pixel 100 fois pour obtenir une meilleure estimation des performances.

### <a name="the-setpixel-method"></a>La méthode SetPixel

Si vous avez seulement besoin de définir ou d’avoir plusieurs pixels individuels, les méthodes [`SetPixel`](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) et [`GetPixel`](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) sont idéales. Pour chacun de ces deux méthodes, vous spécifiez la colonne d’entiers et de la ligne. Quel que soit le format de pixel, ces deux méthodes vous permettent d’obtenir ou de définir le pixel comme une valeur de `SKColor` :

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

L’argument `col` doit être compris entre 0 et un de moins que la propriété `Width` de l’image bitmap, et `row` est compris entre 0 et une valeur inférieure à celle de la propriété `Height`.

Voici la méthode dans une **bitmap de dégradé** qui définit le contenu d’une image bitmap à l’aide de la méthode `SetPixel`. La bitmap 256 par 256 pixels et les boucles `for` sont codées en dur avec la plage de valeurs :

```csharp
public class GradientBitmapPage : ContentPage
{
    const int REPS = 100;

    Stopwatch stopwatch = new Stopwatch();
    ···
    SKBitmap FillBitmapSetPixel(out string description, out int milliseconds)
    {
        description = "SetPixel";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        for (int rep = 0; rep < REPS; rep++)
            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    bitmap.SetPixel(col, row, new SKColor((byte)col, 0, (byte)row));
                }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
}
```

Le modèle de couleurs pour chaque pixel a un élément rouge est égal à la colonne de bitmap et un élément bleu est égal à la ligne. La bitmap résultante est le noire dans le coin supérieur gauche, rouge en haut à droite, bleu au bas à gauche et magenta dans l’angle inférieur droit, avec des dégradés ailleurs.

La méthode `SetPixel` est appelée 65 536 fois et, quelle que soit l’efficacité de cette méthode, il n’est généralement pas judicieux de faire ce nombre d’appels d’API si une alternative est disponible. Heureusement, il existe plusieurs alternatives.

### <a name="the-pixels-property"></a>La propriété de Pixels

`SKBitmap` définit une propriété [`Pixels`](xref:SkiaSharp.SKBitmap.Pixels) qui retourne un tableau de valeurs de `SKColor` pour la bitmap entière. Vous pouvez également utiliser `Pixels` pour définir un tableau de valeurs de couleur pour l’image bitmap :

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Les pixels sont organisées dans le tableau en commençant par la première ligne, de gauche à droite, puis la deuxième ligne et ainsi de suite. Le nombre total de couleurs dans le tableau est égal au produit de la hauteur et la largeur de la bitmap.

Bien que cette propriété semble être efficace, gardez à l’esprit que les pixels sont copiés à partir de la bitmap dans le tableau, et du tableau vers la bitmap, et les pixels sont convertis à partir de et en `SKColor` valeurs.

Voici la méthode de la classe `GradientBitmapPage` qui définit l’image bitmap à l’aide de la propriété `Pixels`. La méthode alloue un `SKColor` tableau de la taille requise, mais elle peut avoir utilisé la propriété `Pixels` pour créer ce tableau :

```csharp
SKBitmap FillBitmapPixelsProp(out string description, out int milliseconds)
{
    description = "Pixels property";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    SKColor[] pixels = new SKColor[256 * 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                pixels[256 * row + col] = new SKColor((byte)col, 0, (byte)row);
            }

    bitmap.Pixels = pixels;

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Notez que l’index du tableau de `pixels` doit être calculé à partir des variables `row` et `col`. La ligne est multipliée par le nombre de pixels dans chaque ligne (256 dans le cas présent), et la colonne est ajoutée.

`SKBitmap` définit également une propriété `Bytes` similaire, qui retourne un tableau d’octets pour l’ensemble de la bitmap, mais il est plus lourd pour les bitmaps de couleur entière.

### <a name="the-getpixels-pointer"></a>Le pointeur GetPixels

Potentiellement, la technique la plus puissante pour accéder aux pixels de la bitmap est [`GetPixels`](xref:SkiaSharp.SKBitmap.GetPixels), à ne pas confondre avec la méthode `GetPixel` ou la propriété `Pixels`. Vous remarquerez immédiatement une différence avec `GetPixels` dans le fait qu’elle renvoie un événement C# qui n’est pas très courant dans la programmation :

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

Le type de [`IntPtr`](xref:System.IntPtr) .net représente un pointeur. Elle est appelée `IntPtr`, car il s’agit de la longueur d’un entier sur le processeur natif de l’ordinateur sur lequel le programme est exécuté, en général 32 bits ou 64 bits. La `IntPtr` que `GetPixels` retourne est l’adresse du bloc réel de mémoire que l’objet Bitmap utilise pour stocker ses pixels.

Vous pouvez convertir le `IntPtr` en type C# pointeur à l’aide de la méthode [`ToPointer`](xref:System.IntPtr.ToPointer) . La syntaxe de pointeur c# est identique à C et C++ :

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

La variable `ptr` est de type _pointeur d’octet_. Cette variable de `ptr` vous permet d’accéder aux octets de mémoire individuels utilisés pour stocker les pixels de la bitmap. Vous utilisez code similaire à celui-ci pour lire un octet à partir de cette mémoire ou d’écrire un octet dans la mémoire :

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

Dans ce contexte, l’astérisque est l' C# _opérateur d’indirection_ et est utilisé pour référencer le contenu de la mémoire vers laquelle pointe `ptr`. Initialement, `ptr` pointe vers le premier octet du premier pixel de la première ligne de la bitmap, mais vous pouvez effectuer une opération arithmétique sur la variable `ptr` pour la déplacer vers d’autres emplacements au sein de la bitmap.

L’inconvénient est que vous pouvez utiliser cette variable `ptr` uniquement dans un bloc de code marqué avec le mot clé `unsafe`. En outre, l’assembly doit être marquée comme autorisant des blocs unsafe. Pour cela, utilisez les propriétés du projet.

À l’aide de pointeurs en c# est très puissant, mais il est également très dangereux. Vous devez veiller à ce que vous n’accédez pas à la mémoire au-delà de ce que le pointeur est supposée faire référence à. C’est pourquoi l’utilisation de pointeur est associée avec le terme « unsafe ».

Voici la méthode de la classe `GradientBitmapPage` qui utilise la méthode `GetPixels`. Notez le bloc `unsafe` qui englobe tout le code à l’aide du pointeur d’octet :

```csharp
SKBitmap FillBitmapBytePtr(out string description, out int milliseconds)
{
    description = "GetPixels byte ptr";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            byte* ptr = (byte*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (byte)(col);   // red
                    *ptr++ = 0;             // green
                    *ptr++ = (byte)(row);   // blue
                    *ptr++ = 0xFF;          // alpha
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Lorsque la variable `ptr` est obtenue pour la première fois à partir de la méthode `ToPointer`, elle pointe vers le premier octet du pixel le plus à gauche de la première ligne de la bitmap. Les boucles `for` pour `row` et `col` sont configurées afin que `ptr` puisse être incrémenté avec l’opérateur `++` après chaque octet de chaque pixel. Pour les autres 99 boucles à travers les pixels, la `ptr` doit être redéfinie au début de l’image bitmap.

Chaque pixel est de quatre octets de mémoire, chaque octet doit donc être définies séparément. Le code ci-dessous suppose que les octets sont dans l’ordre rouge, vert, bleu et alpha, qui est cohérent avec le type de couleur `SKColorType.Rgba8888`. Vous vous souvenez peut-être qu’il s’agit du type de couleur par défaut pour iOS et Android, mais pas pour la plateforme Windows universelle. Par défaut, la série UWP crée des bitmaps avec le type de couleur `SKColorType.Bgra8888`. Pour cette raison, attendez-vous à voir des résultats différents sur cette plateforme !

Il est possible d’effectuer un cast de la valeur retournée de `ToPointer` en pointeur `uint` plutôt qu’en `byte` pointeur. Ainsi, un pixel entier soient accessibles dans une seule instruction. L’application de l’opérateur `++` à ce pointeur l’incrémente de quatre octets pour pointer vers le pixel suivant :

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    SKBitmap FillBitmapUintPtr(out string description, out int milliseconds)
    {
        description = "GetPixels uint ptr";
        SKBitmap bitmap = new SKBitmap(256, 256);

        stopwatch.Restart();

        IntPtr pixelsAddr = bitmap.GetPixels();

        unsafe
        {
            for (int rep = 0; rep < REPS; rep++)
            {
                uint* ptr = (uint*)pixelsAddr.ToPointer();

                for (int row = 0; row < 256; row++)
                    for (int col = 0; col < 256; col++)
                    {
                        *ptr++ = MakePixel((byte)col, 0, (byte)row, 0xFF);
                    }
            }
        }

        milliseconds = (int)stopwatch.ElapsedMilliseconds;
        return bitmap;
    }
    ···
    [MethodImpl(MethodImplOptions.AggressiveInlining)]
    uint MakePixel(byte red, byte green, byte blue, byte alpha) =>
            (uint)((alpha << 24) | (blue << 16) | (green << 8) | red);
    ···
}
```

Le pixel est défini à l’aide de la méthode `MakePixel`, qui construit un pixel entier à partir des composants rouge, vert, bleu et alpha. Gardez à l’esprit que le format de `SKColorType.Rgba8888` a un ordre des octets de pixel comme celui-ci :

RR, GG BB AA

Mais l’entier correspondant à ces octets :

AABBGGRR

L’octet le moins significatif de l’entier est enregistré en premier en fonction de l’architecture de little-endian. Cette méthode `MakePixel` ne fonctionne pas correctement pour les bitmaps avec le type de couleur `Bgra8888`.

La méthode `MakePixel` est marquée avec l’option [`MethodImplOptions.AggressiveInlining`](xref:System.Runtime.CompilerServices.MethodImplOptions) pour inciter le compilateur à éviter d’en faire une méthode distincte, mais plutôt de compiler le code où la méthode est appelée. Cela doit améliorer les performances.

Il est intéressant de savoir que la structure `SKColor` définit une conversion explicite de `SKColor` en entier non signé, ce qui signifie qu’une valeur de `SKColor` peut être créée, et une conversion en `uint` peut être utilisée à la place de `MakePixel`:

```csharp
SKBitmap FillBitmapUintPtrColor(out string description, out int milliseconds)
{
    description = "GetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    IntPtr pixelsAddr = bitmap.GetPixels();

    unsafe
    {
        for (int rep = 0; rep < REPS; rep++)
        {
            uint* ptr = (uint*)pixelsAddr.ToPointer();

            for (int row = 0; row < 256; row++)
                for (int col = 0; col < 256; col++)
                {
                    *ptr++ = (uint)new SKColor((byte)col, 0, (byte)row);
                }
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

La seule question est la suivante : le format d’entier de la valeur de `SKColor` dans l’ordre du type de couleur `SKColorType.Rgba8888`, ou le type de couleur de la `SKColorType.Bgra8888`, ou s’agit-il d’une autre valeur ? La réponse à cette question doit être révélée peu de temps.

### <a name="the-setpixels-method"></a>La méthode SetPixels

`SKBitmap` définit également une méthode nommée [`SetPixels`](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), que vous appelez comme suit :

```csharp
bitmap.SetPixels(intPtr);
```

Rappelez-vous que `GetPixels` obtient un `IntPtr` référençant le bloc de mémoire utilisé par l’image bitmap pour stocker ses pixels. L’appel de `SetPixels` _remplace_ ce bloc de mémoire par le bloc de mémoire référencé par le `IntPtr` spécifié en tant qu’argument `SetPixels`. L’image bitmap puis permet de libérer le bloc de mémoire qu’il utilisait précédemment. La prochaine fois que `GetPixels` est appelé, il obtient le bloc de mémoire défini avec `SetPixels`.

Tout d’abord, il semble que `SetPixels` ne vous donne plus de puissance et de performances que `GetPixels` tout en étant moins pratique. Avec `GetPixels` vous obtenez le bloc de mémoire bitmap et y accédez. Avec `SetPixels` vous allouez et accédez à une partie de la mémoire, puis vous définissez cette valeur en tant que bloc de mémoire bitmap.

Toutefois, l’utilisation de `SetPixels` offre un avantage syntaxique distinct : elle vous permet d’accéder aux bits de pixel de la bitmap à l’aide d’un tableau. Voici la méthode dans `GradientBitmapPage` qui illustre cette technique. Tout d’abord, la méthode définit un tableau d’octets à plusieurs dimensions correspondant aux octets de pixels étaient du bitmap. La première dimension est la ligne, la deuxième dimension est la colonne et la troisième dimension correspond aux quatre composants de chaque pixel :

```csharp
SKBitmap FillBitmapByteBuffer(out string description, out int milliseconds)
{
    description = "SetPixels byte buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    byte[,,] buffer = new byte[256, 256, 4];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col, 0] = (byte)col;   // red
                buffer[row, col, 1] = 0;           // green
                buffer[row, col, 2] = (byte)row;   // blue
                buffer[row, col, 3] = 0xFF;        // alpha
            }

    unsafe
    {
        fixed (byte* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

Ensuite, une fois que le tableau a été rempli de pixels, un bloc `unsafe` et une instruction `fixed` sont utilisés pour obtenir un pointeur d’octet qui pointe vers ce tableau. Ce pointeur d’octet peut ensuite être casté en `IntPtr` à passer à `SetPixels`.

Le tableau que vous créez ne doit être un tableau d’octets. Il peut être un tableau d’entiers avec uniquement deux dimensions pour la ligne et colonne :

```csharp
SKBitmap FillBitmapUintBuffer(out string description, out int milliseconds)
{
    description = "SetPixels uint buffer";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = MakePixel((byte)col, 0, (byte)row, 0xFF);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

La méthode `MakePixel` est encore utilisée pour combiner les composants de couleur en pixels 32 bits.

Pour des tâches d’exhaustivité, voici le même code, mais avec une valeur `SKColor` convertie en entier non signé :

```csharp
SKBitmap FillBitmapUintBufferColor(out string description, out int milliseconds)
{
    description = "SetPixels SKColor";
    SKBitmap bitmap = new SKBitmap(256, 256);

    stopwatch.Restart();

    uint[,] buffer = new uint[256, 256];

    for (int rep = 0; rep < REPS; rep++)
        for (int row = 0; row < 256; row++)
            for (int col = 0; col < 256; col++)
            {
                buffer[row, col] = (uint)new SKColor((byte)col, 0, (byte)row);
            }

    unsafe
    {
        fixed (uint* ptr = buffer)
        {
            bitmap.SetPixels((IntPtr)ptr);
        }
    }

    milliseconds = (int)stopwatch.ElapsedMilliseconds;
    return bitmap;
}
```

### <a name="comparing-the-techniques"></a>Comparaison des techniques

Le constructeur de la page de **couleur de dégradé** appelle les huit méthodes indiquées ci-dessus et enregistre les résultats :

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    string[] descriptions = new string[8];
    SKBitmap[] bitmaps = new SKBitmap[8];
    int[] elapsedTimes = new int[8];

    SKCanvasView canvasView;

    public GradientBitmapPage ()
    {
        Title = "Gradient Bitmap";

        bitmaps[0] = FillBitmapSetPixel(out descriptions[0], out elapsedTimes[0]);
        bitmaps[1] = FillBitmapPixelsProp(out descriptions[1], out elapsedTimes[1]);
        bitmaps[2] = FillBitmapBytePtr(out descriptions[2], out elapsedTimes[2]);
        bitmaps[4] = FillBitmapUintPtr(out descriptions[4], out elapsedTimes[4]);
        bitmaps[6] = FillBitmapUintPtrColor(out descriptions[6], out elapsedTimes[6]);
        bitmaps[3] = FillBitmapByteBuffer(out descriptions[3], out elapsedTimes[3]);
        bitmaps[5] = FillBitmapUintBuffer(out descriptions[5], out elapsedTimes[5]);
        bitmaps[7] = FillBitmapUintBufferColor(out descriptions[7], out elapsedTimes[7]);

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
    }
    ···
}
```

Le constructeur se termine en créant un `SKCanvasView` pour afficher les bitmaps résultantes. Le gestionnaire de `PaintSurface` divise sa surface en huit rectangles et appelle `Display` pour afficher chacun d’entre eux :

```csharp
public class GradientBitmapPage : ContentPage
{
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        int width = info.Width;
        int height = info.Height;

        canvas.Clear();

        Display(canvas, 0, new SKRect(0, 0, width / 2, height / 4));
        Display(canvas, 1, new SKRect(width / 2, 0, width, height / 4));
        Display(canvas, 2, new SKRect(0, height / 4, width / 2, 2 * height / 4));
        Display(canvas, 3, new SKRect(width / 2, height / 4, width, 2 * height / 4));
        Display(canvas, 4, new SKRect(0, 2 * height / 4, width / 2, 3 * height / 4));
        Display(canvas, 5, new SKRect(width / 2, 2 * height / 4, width, 3 * height / 4));
        Display(canvas, 6, new SKRect(0, 3 * height / 4, width / 2, height));
        Display(canvas, 7, new SKRect(width / 2, 3 * height / 4, width, height));
    }

    void Display(SKCanvas canvas, int index, SKRect rect)
    {
        string text = String.Format("{0}: {1:F1} msec", descriptions[index],
                                    (double)elapsedTimes[index] / REPS);

        SKRect bounds = new SKRect();

        using (SKPaint textPaint = new SKPaint())
        {
            textPaint.TextSize = (float)(12 * canvasView.CanvasSize.Width / canvasView.Width);
            textPaint.TextAlign = SKTextAlign.Center;
            textPaint.MeasureText("Tly", ref bounds);

            canvas.DrawText(text, new SKPoint(rect.MidX, rect.Bottom - bounds.Bottom), textPaint);
            rect.Bottom -= bounds.Height;
            canvas.DrawBitmap(bitmaps[index], rect, BitmapStretch.Uniform);
        }
    }
}
```

Pour permettre au compilateur d’optimiser le code, cette page a été exécutée en mode **Release** . Voici la page en cours d’exécution sur un simulateur iPhone 8 sur un MacBook Pro, un téléphone Nexus 5 Android et Surface Pro 3 exécutant Windows 10. En raison des différences de matériel, éviter la comparaison de la durée des performances entre les appareils, mais elles aux moments relatives sur chaque appareil :

[![Bitmap de dégradé](pixel-bits-images/GradientBitmap.png "Bitmap de dégradé")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Voici un tableau qui consolide les temps d’exécution en millisecondes :

| API       | Type de données | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3.49 |
| Pixels    |           | 0.32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0.24 | 0.10 |
|           | uint      | 0.06 |    0,26 | 0.05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0.14 |    0,69 | 0.06 |
|           | SKColor   | 0,35 |    1.93 | 0.10 |

Comme prévu, l’appel de `SetPixel` 65 536 fois est le moins effeicient pour définir les pixels d’une bitmap. Le remplissage d’un tableau `SKColor` et la définition de la propriété `Pixels` sont bien meilleurs, et même une comparaison plus favorable avec certaines techniques de `GetPixels` et de `SetPixels`. L’utilisation de `uint` valeurs en pixels est généralement plus rapide que la définition de composants `byte` distincts, et la conversion de la valeur `SKColor` en entier non signé ajoute une surcharge au processus.

Il est également intéressant de comparer les dégradés différents : les premières lignes de chaque plateforme sont les mêmes et afficher le dégradé comme il était prévu. Cela signifie que la méthode `SetPixel` et la propriété `Pixels` créent correctement des pixels à partir des couleurs, quel que soit le format de pixel sous-jacent.

Les deux lignes suivantes des captures d’écran iOS et Android sont également les mêmes, ce qui confirme que la petite méthode `MakePixel` est correctement définie pour le format de pixel `Rgba8888` par défaut pour ces plateformes.

La ligne inférieure des captures d’écran iOS et Android est vers l’arrière, ce qui indique que l’entier non signé obtenu en effectuant un cast d’une valeur `SKColor` se présente sous la forme suivante :

AARRGGBB

Les octets sont dans l’ordre :

BB GG RR AA

Il s’agit de l’ordre de `Bgra8888` plutôt que du classement des `Rgba8888`. Le format `Brga8888` est la valeur par défaut pour la plateforme Windows universelle, c’est pourquoi les dégradés sur la dernière ligne de cette capture d’écran sont les mêmes que la première ligne. Toutefois, les deux lignes du milieu sont incorrectes, car le code qui crée ces bitmaps supposait un ordre de `Rgba8888`.

Si vous souhaitez utiliser le même code pour accéder aux bits de pixel sur chaque plateforme, vous pouvez créer explicitement un `SKBitmap` à l’aide du format `Rgba8888` ou `Bgra8888`. Si vous souhaitez effectuer un cast `SKColor` de valeurs en pixels bitmap, utilisez `Bgra8888`.

## <a name="random-access-of-pixels"></a>Accès aléatoire de pixels

Les méthodes `FillBitmapBytePtr` et `FillBitmapUintPtr` dans la page **bitmap de dégradé** tirent parti de `for` boucles conçues pour remplir l’image bitmap de manière séquentielle, de la ligne supérieure à la ligne inférieure et de chaque ligne de gauche à droite. Le pixel puisse être défini avec la même instruction qui incrémenté le pointeur.

Il est parfois nécessaire accéder aux pixels au hasard, plutôt que séquentielle. Si vous utilisez l’approche `GetPixels`, vous devez calculer des pointeurs en fonction de la ligne et de la colonne. Cela est illustré dans la page **arc sinus** , qui crée une bitmap indiquant un arc-en-ciel sous la forme d’un cycle d’une courbe sinusoïdale.

Les couleurs de l’arc-en-ciel sont plus faciles à créer à l’aide de la palette de couleurs TSL (teinte, saturation, luminosité). La méthode `SKColor.FromHsl` crée une valeur `SKColor` à l’aide de valeurs de teinte comprises entre 0 et 360 (comme les angles d’un cercle, mais allant du rouge au vert et du bleu, puis de nouveau vers le rouge) et des valeurs de saturation et de luminosité allant de 0 à 100. Pour les couleurs de l’arc-en-ciel, la saturation doit être définie sur un maximum de 100 et la luminosité à un point milieu de 50.

Le **sinus arc** -en-ciel crée cette image en effectuant une boucle dans les lignes de la bitmap, puis en effectuant une boucle sur 360 valeurs de teinte. À partir de chaque valeur de teinte, il calcule une colonne de bitmap qui est également basée sur une valeur du sinus :

```csharp
public class RainbowSinePage : ContentPage
{
    SKBitmap bitmap;

    public RainbowSinePage()
    {
        Title = "Rainbow Sine";

        bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);

        unsafe
        {
            // Pointer to first pixel of bitmap
            uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();

            // Loop through the rows
            for (int row = 0; row < bitmap.Height; row++)
            {
                // Calculate the sine curve angle and the sine value
                double angle = 2 * Math.PI * row / bitmap.Height;
                double sine = Math.Sin(angle);

                // Loop through the hues
                for (int hue = 0; hue < 360; hue++)
                {
                    // Calculate the column
                    int col = (int)(360 + 360 * sine + hue);

                    // Calculate the address
                    uint* ptr = basePtr + bitmap.Width * row + col;

                    // Store the color value
                    *ptr = (uint)SKColor.FromHsl(hue, 100, 50);
                }
            }
        }

        // Create the SKCanvasView
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
        canvas.DrawBitmap(bitmap, info.Rect);
    }
}
```

Notez que le constructeur crée la bitmap en fonction du format de `SKColorType.Bgra8888` :

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Cela permet au programme d’utiliser la conversion de valeurs `SKColor` en `uint` pixels sans crainte. Bien qu’il ne joue pas un rôle dans ce programme particulier, chaque fois que vous utilisez la conversion `SKColor` pour définir les pixels, vous devez également spécifier `SKAlphaType.Unpremul`, car `SKColor` ne prémultiplie pas ses composants de couleur par la valeur alpha.

Le constructeur utilise ensuite la méthode `GetPixels` pour obtenir un pointeur vers le premier pixel de la bitmap :

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Pour une ligne et une colonne particulières, une valeur de décalage doit être ajoutée à `basePtr`. Ce décalage est la ligne par la largeur de la bitmap, ainsi que la colonne :

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

La valeur `SKColor` est stockée en mémoire à l’aide de ce pointeur :

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

Dans le gestionnaire de `PaintSurface` de la `SKCanvasView`, la bitmap est étirée pour remplir la zone d’affichage :

[![Arc sinus](pixel-bits-images/RainbowSine.png "Arc sinus")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>À partir d’une bitmap vers un autre

Très nombreuses tâches de traitement d’image impliquent la modification des pixels comme ils sont transférés à partir d’une bitmap vers un autre. Cette technique est illustrée dans la page **réglage des couleurs** . La page charge l’une des ressources bitmap, puis vous permet de modifier l’image à l’aide de trois `Slider` vues :

[![Réglage des couleurs](pixel-bits-images/ColorAdjustment.png "Réglage des couleurs")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Pour chaque couleur de pixel, la première `Slider` ajoute une valeur comprise entre 0 et 360 à la teinte, mais utilise ensuite l’opérateur modulo pour conserver le résultat entre 0 et 360, en décalant efficacement les couleurs le long du spectre (comme le montre la capture d’écran UWP). La deuxième `Slider` vous permet de sélectionner un facteur de multiplication entre 0,5 et 2 à appliquer à la saturation, tandis que la troisième `Slider` fait de même pour la luminosité, comme illustré dans la capture d’écran Android.

Le programme gère deux bitmaps, la bitmap source d’origine nommée `srcBitmap` et le bitmap de destination ajusté nommé `dstBitmap`. Chaque fois qu’un `Slider` est déplacé, le programme calcule tous les nouveaux pixels dans `dstBitmap`. Bien entendu, les utilisateurs effectuent des expérimentations en déplaçant très rapidement les vues de `Slider`, ce qui vous permet d’obtenir les meilleures performances que vous pouvez gérer. Cela implique la méthode `GetPixels` pour les bitmaps sources et de destination.

La page **réglage des couleurs** ne contrôle pas le format des couleurs des bitmaps sources et de destination. Au lieu de cela, elle contient une logique légèrement différente pour les formats `SKColorType.Rgba8888` et `SKColorType.Bgra8888`. La source et la destination peuvent être de différents formats, et le programme continue de fonctionner.

Voici le programme, à l’exception de la méthode `TransferPixels` essentielle qui transfère les pixels à la source vers la destination. Le constructeur définit `dstBitmap` égal à `srcBitmap`. Le gestionnaire de `PaintSurface` affiche `dstBitmap`:

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    SKBitmap srcBitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    SKBitmap dstBitmap;

    public ColorAdjustmentPage()
    {
        InitializeComponent();

        dstBitmap = new SKBitmap(srcBitmap.Width, srcBitmap.Height);
        OnSliderValueChanged(null, null);
    }

    void OnSliderValueChanged(object sender, ValueChangedEventArgs args)
    {
        float hueAdjust = (float)hueSlider.Value;
        hueLabel.Text = $"Hue Adjustment: {hueAdjust:F0}";

        float saturationAdjust = (float)Math.Pow(2, saturationSlider.Value);
        saturationLabel.Text = $"Saturation Adjustment: {saturationAdjust:F2}";

        float luminosityAdjust = (float)Math.Pow(2, luminositySlider.Value);
        luminosityLabel.Text = $"Luminosity Adjustment: {luminosityAdjust:F2}";

        TransferPixels(hueAdjust, saturationAdjust, luminosityAdjust);
        canvasView.InvalidateSurface();
    }
    ···
    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(dstBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

Le gestionnaire de `ValueChanged` pour les vues `Slider` calcule les valeurs d’ajustement et appelle `TransferPixels`.

La méthode `TransferPixels` entière est marquée comme `unsafe`. Il commence par l’obtention de pointeurs d’octets pour les bits de pixel de ces deux bitmaps, puis effectue une itération sur toutes les lignes et colonnes. À partir de l’image bitmap source, la méthode obtient quatre octets pour chaque pixel. Il peut s’agir de l’ordre `Rgba8888` ou `Bgra8888`. La vérification du type de couleur permet de créer une valeur `SKColor`. Les composants TSL sont ensuite extraits, ajustés et utilisés pour recréer la valeur de `SKColor`. Selon que le bitmap de destination est `Rgba8888` ou `Bgra8888`, les octets sont stockés dans le bitmp de destination :

```csharp
public partial class ColorAdjustmentPage : ContentPage
{
    ···
    unsafe void TransferPixels(float hueAdjust, float saturationAdjust, float luminosityAdjust)
    {
        byte* srcPtr = (byte*)srcBitmap.GetPixels().ToPointer();
        byte* dstPtr = (byte*)dstBitmap.GetPixels().ToPointer();

        int width = srcBitmap.Width;       // same for both bitmaps
        int height = srcBitmap.Height;

        SKColorType typeOrg = srcBitmap.ColorType;
        SKColorType typeAdj = dstBitmap.ColorType;

        for (int row = 0; row < height; row++)
        {
            for (int col = 0; col < width; col++)
            {
                // Get color from original bitmap
                byte byte1 = *srcPtr++;         // red or blue
                byte byte2 = *srcPtr++;         // green
                byte byte3 = *srcPtr++;         // blue or red
                byte byte4 = *srcPtr++;         // alpha

                SKColor color = new SKColor();

                if (typeOrg == SKColorType.Rgba8888)
                {
                    color = new SKColor(byte1, byte2, byte3, byte4);
                }
                else if (typeOrg == SKColorType.Bgra8888)
                {
                    color = new SKColor(byte3, byte2, byte1, byte4);
                }

                // Get HSL components
                color.ToHsl(out float hue, out float saturation, out float luminosity);

                // Adjust HSL components based on adjustments
                hue = (hue + hueAdjust) % 360;
                saturation = Math.Max(0, Math.Min(100, saturationAdjust * saturation));
                luminosity = Math.Max(0, Math.Min(100, luminosityAdjust * luminosity));

                // Recreate color from HSL components
                color = SKColor.FromHsl(hue, saturation, luminosity);

                // Store the bytes in the adjusted bitmap
                if (typeAdj == SKColorType.Rgba8888)
                {
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Alpha;
                }
                else if (typeAdj == SKColorType.Bgra8888)
                {
                    *dstPtr++ = color.Blue;
                    *dstPtr++ = color.Green;
                    *dstPtr++ = color.Red;
                    *dstPtr++ = color.Alpha;
                }
            }
        }
    }
    ···
}
```

Il est probable que les performances de cette méthode peut être amélioré encore plus en créant des méthodes distinctes pour les différentes combinaisons de types de couleur des images bitmap source et de destination et éviter la vérification du type pour chaque pixel. Une autre option consiste à avoir plusieurs `for` boucles pour la variable `col` en fonction du type de couleur.

## <a name="posterization"></a>Postérisation

Une autre tâche courante qui implique l’accès aux bits de pixel est l' _postérisation_. Le nombre si couleurs encodées en pixels d’une bitmap est réduit afin que le résultat ressemble à un poster dessiné à l’aide d’une palette de couleurs limitée.

La page **postérisation** effectue ce processus sur l’une des images de singe :

```csharp
public class PosterizePage : ContentPage
{
    SKBitmap bitmap =
        BitmapExtensions.LoadBitmapResource(typeof(FillRectanglePage),
                                            "SkiaSharpFormsDemos.Media.Banana.jpg");
    public PosterizePage()
    {
        Title = "Posterize";

        unsafe
        {
            uint* ptr = (uint*)bitmap.GetPixels().ToPointer();
            int pixelCount = bitmap.Width * bitmap.Height;

            for (int i = 0; i < pixelCount; i++)
            {
                *ptr++ &= 0xE0E0E0FF;
            }
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
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform;
    }
}
```

Le code dans le constructeur accède à chaque pixel, effectue une opération AND au niveau du bit avec la valeur 0xE0E0E0FF, puis stocke le résultat dans l’image bitmap. Les valeurs 0xE0E0E0FF conserve les 3 bits de poids fort de chaque composant de couleur et 5 bits inférieurs à 0. Au lieu de 2<sup>24</sup> ou 16 777 216 couleurs, la bitmap est réduite à 2<sup>9</sup> ou 512 couleurs :

[![Postérisation](pixel-bits-images/Posterize.png "Postérisation")](pixel-bits-images/Posterize-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
