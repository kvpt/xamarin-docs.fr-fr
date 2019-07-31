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
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68642524"
---
# <a name="accessing-skiasharp-bitmap-pixel-bits"></a>L’accès aux bits de pixel de bitmap SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Comme vous l’avez vu dans l’article [ **SkiaSharp l’enregistrement des bitmaps aux fichiers**](saving.md), les bitmaps sont généralement stockés dans les fichiers dans un format compressé, tel que JPEG ou PNG. En revanche, une image bitmap de SkiaSharp stockée en mémoire n’est pas compressée. Il est stocké comme une série séquentielle de pixels. Ce format non compressé facilite le transfert de bitmaps à une surface d’affichage.

Le bloc de mémoire occupé par une image bitmap SkiaSharp est organisé de manière très simple: Elle commence par la première ligne de pixels, de gauche à droite, puis continue avec la deuxième ligne. Pour les images bitmap en couleurs, chaque pixel se compose de quatre octets, ce qui signifie que l’espace de mémoire totale requise par l’image bitmap est quatre fois le produit de sa largeur et sa hauteur.

Cet article décrit comment une application peut accéder à ces pixels, soit directement accéder au bloc de mémoire de pixel de la bitmap, ou indirectement. Dans certains cas, un programme peut souhaiter analyser les pixels d’une image et de construire un histogramme quelconque. En général, applications peuvent construire des images uniques en créant de façon algorithmique les pixels qui composent l’image bitmap :

![Exemples de Bits de pixel](pixel-bits-images/PixelBitsSample.png "exemple des Bits de Pixel")

## <a name="the-techniques"></a>Les techniques

SkiaSharp fournit plusieurs techniques permettant d’accéder aux bits de pixel d’une image bitmap. Votre choix est généralement un compromis entre commodité (qui est liée à la maintenance et de faciliter le débogage) et les performances de codage. Dans la plupart des cas, vous allez utiliser une des méthodes suivantes et les propriétés de `SKBitmap` pour accéder aux pixels étaient du bitmap :

- Le `GetPixel` et `SetPixel` méthodes vous permettent d’obtenir ou définir la couleur d’un pixel unique.
- Le `Pixels` propriété obtient un tableau de couleurs de pixel pour l’image bitmap entière, ou définit le tableau de couleurs.
- `GetPixels` Retourne l’adresse de la mémoire de pixel utilisée par l’image bitmap.
- `SetPixels` remplace l’adresse de la mémoire de pixels utilisée par l’image bitmap.

Vous pouvez considérer les deux premières techniques comme « niveau élevé » et les deux en tant que « niveau faible ». Il existe d’autres méthodes et propriétés que vous pouvez utiliser, mais ce sont les plus précieuses.

Pour vous permettre de voir les différences de performances entre ces techniques, le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) application contient une page nommée **Bitmap dégradé** qui Crée une image bitmap avec les pixels qui combinent des nuances de rouges et bleus pour créer un dégradé. Le programme crée huit copies différentes de cette image bitmap, tout à l’aide de différentes techniques permettant de définir les pixels du bitmap. Chacun de ces huit bitmaps est créé dans une méthode distincte qui également définit une brève description de la technique et calcule le temps nécessaire pour définir tous les pixels. Chaque méthode effectue une itération sur la logique de la valeur de pixel 100 fois pour obtenir une meilleure estimation des performances.

### <a name="the-setpixel-method"></a>La méthode SetPixel

Si vous devez uniquement définir ou obtenir plusieurs pixels individuels, le [ `SetPixel` ](xref:SkiaSharp.SKBitmap.SetPixel(System.Int32,System.Int32,SkiaSharp.SKColor)) et [ `GetPixel` ](xref:SkiaSharp.SKBitmap.GetPixel(System.Int32,System.Int32)) méthodes sont idéales. Pour chacun de ces deux méthodes, vous spécifiez la colonne d’entiers et de la ligne. Quel que soit le format de pixel, ces deux méthodes vous permettent d’obtenir ou définir le pixel comme un `SKColor` valeur :

```csharp
bitmap.SetPixel(col, row, color);

SKColor color = bitmap.GetPixel(col, row);
```

Le `col` argument doit être comprise entre 0 et un inférieure à la `Width` propriété de l’image bitmap, et `row` comprise entre 0 et un inférieur au `Height` propriété.

Voici la méthode **Bitmap dégradé** qui définit le contenu d’un bitmap en utilisant le `SetPixel` (méthode). La bitmap est de 256 x 256 pixels et le `for` boucles sont codées en dur avec la plage de valeurs :

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

Le `SetPixel` méthode est appelé une fois 65 536 et quel que soit l’efficacité cette méthode peut être, il est généralement pas judicieux d’effectuer des appels d’API que de nombreux s’il existe une alternative. Heureusement, il existe plusieurs alternatives.

### <a name="the-pixels-property"></a>La propriété de Pixels

`SKBitmap` définit un [ `Pixels` ](xref:SkiaSharp.SKBitmap.Pixels) propriété qui retourne un tableau de `SKColor` valeurs pour l’image bitmap entière. Vous pouvez également utiliser `Pixels` pour définir un tableau de valeurs de couleur pour l’image bitmap :

```csharp
SKColor[] pixels = bitmap.Pixels;

bitmap.Pixels = pixels;
```

Les pixels sont organisées dans le tableau en commençant par la première ligne, de gauche à droite, puis la deuxième ligne et ainsi de suite. Le nombre total de couleurs dans le tableau est égal au produit de la hauteur et la largeur de la bitmap.

Bien que cette propriété s’affiche pour être efficace, n’oubliez pas que les pixels sont copiés à partir de l’image bitmap dans le tableau et à partir du tableau dans l’image bitmap, et les pixels sont converties depuis et vers `SKColor` valeurs.

Voici la méthode le `GradientBitmapPage` classe qui définit l’image bitmap à l’aide de la `Pixels` propriété. La méthode alloue un `SKColor` tableau de la taille requise, mais elle aurait pu utiliser le `Pixels` propriété pour créer ce tableau :

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

Notez que l’index de la `pixels` tableau doit être calculée à partir de la `row` et `col` variables. La ligne est multipliée par le nombre de pixels dans chaque ligne (256 dans le cas présent), et la colonne est ajoutée.

`SKBitmap` définit également un texte similaire `Bytes` propriété, qui retourne un tableau d’octets pour l’image bitmap entière, mais il est plus fastidieuse pour les bitmaps de couleur.

### <a name="the-getpixels-pointer"></a>Le pointeur GetPixels

Potentiellement la technique la plus efficace pour accéder aux pixels du bitmap est [ `GetPixels` ](xref:SkiaSharp.SKBitmap.GetPixels), à ne pas confondre avec le `GetPixel` méthode ou le `Pixels` propriété. Vous remarquerez immédiatement une différence avec `GetPixels` elle retourne un élément très courant dans la programmation en c# :

```csharp
IntPtr pixelsAddr = bitmap.GetPixels();
```

.NET [ `IntPtr` ](xref:System.IntPtr) type représente un pointeur. Elle est appelée `IntPtr` car il s’agit de la longueur d’un entier sur le processeur natif de l’ordinateur sur lequel le programme est exécuté, généralement 32 bits ou 64 bits de longueur. Le `IntPtr` qui `GetPixels` retourne est l’adresse du bloc de mémoire à l’aide de l’objet bitmap pour stocker ses pixels réel.

Vous pouvez convertir le `IntPtr` dans un langage c# pointeur type en utilisant la [ `ToPointer` ](xref:System.IntPtr.ToPointer) (méthode). La syntaxe de pointeur c# est identique à C et C++ :

```csharp
byte* ptr = (byte*)pixelsAddr.ToPointer();
```

Le `ptr` variable est de type _pointeur d’octet_. Cela `ptr` variable vous donne accès à chaque octet du mémoire qui est utilisés pour stocker les pixels de la bitmap. Vous utilisez code similaire à celui-ci pour lire un octet à partir de cette mémoire ou d’écrire un octet dans la mémoire :

```csharp
byte pixelComponent = *ptr;

*ptr = pixelComponent;
```

Dans ce contexte, l’astérisque est celle de C# _opérateur d’indirection_ et est utilisé pour référencer le contenu de la mémoire vers laquelle pointé `ptr`. Initialement, `ptr` pointe vers le premier octet du premier pixel de la première ligne de la bitmap, mais vous pouvez effectuer une opération arithmétique sur les `ptr` variable pour le déplacer vers d’autres emplacements au sein de l’image bitmap.

L’inconvénient est que vous pouvez utiliser cette `ptr` variable uniquement dans un bloc de code marqué avec le `unsafe` mot clé. En outre, l’assembly doit être marquée comme autorisant des blocs unsafe. Pour cela, utilisez les propriétés du projet.

À l’aide de pointeurs en c# est très puissant, mais il est également très dangereux. Vous devez veiller à ce que vous n’accédez pas à la mémoire au-delà de ce que le pointeur est supposée faire référence à. C’est pourquoi l’utilisation de pointeur est associée avec le terme « unsafe ».

Voici la méthode le `GradientBitmapPage` classe qui utilise le `GetPixels` (méthode). Notez que le `unsafe` bloc qui englobe tout le code à l’aide du pointeur d’octet :

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

Lorsque le `ptr` variable est tout d’abord obtenue à partir de la `ToPointer` (méthode), il pointe vers le premier octet du pixel à l’extrême gauche de la première ligne de la bitmap. Le `for` effectue une itération pour `row` et `col` sont configurés afin que `ptr` peut être incrémenté avec la `++` opérateur après chaque octet de chaque pixel est défini. Pour l’autres 99 parcourt les pixels, la `ptr` doit être définie en retour au début de la bitmap.

Chaque pixel est de quatre octets de mémoire, chaque octet doit donc être définies séparément. Le code ici suppose que les octets sont rouge de l’ordre, vert, bleu et alpha, qui est cohérent avec la `SKColorType.Rgba8888` type de couleur. Vous vous souvenez peut-être qu’il s’agit du type de couleur par défaut pour iOS et Android, mais pas pour la plateforme Windows universelle. Par défaut, la plateforme Windows universelle crée des bitmaps avec la `SKColorType.Bgra8888` type de couleur. Pour cette raison, attendez-vous à voir des résultats différents sur cette plateforme !

Il est possible d’effectuer un cast de la valeur retournée par `ToPointer` à un `uint` pointeur au lieu d’un `byte` pointeur. Ainsi, un pixel entier soient accessibles dans une seule instruction. Appliquer le `++` opérateur à ce pointeur il incrémente de quatre octets pour pointer vers le pixel suivant :

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

Le pixel est défini à l’aide de la `MakePixel` méthode qui construit un pixel entier à partir de composants rouges, verts, bleu et alphabétiques. N’oubliez pas que le `SKColorType.Rgba8888` format a un octet de pixel classement comme suit :

RR, GG BB AA

Mais l’entier correspondant à ces octets :

AABBGGRR

L’octet le moins significatif de l’entier est enregistré en premier en fonction de l’architecture de little-endian. Cela `MakePixel` méthode ne fonctionnera pas correctement pour les bitmaps avec la `Bgra8888` type de couleur.

Le `MakePixel` méthode est marquée avec le [ `MethodImplOptions.AggressiveInlining` ](xref:System.Runtime.CompilerServices.MethodImplOptions) option encourager au compilateur d’éviter ce qui en fait une méthode distincte, mais plutôt pour compiler le code où la méthode est appelée. Cela doit améliorer les performances.

Curieusement, la `SKColor` structure définit une conversion explicite de `SKColor` en entier non signé, ce qui signifie qu’un `SKColor` valeur peut être créée et une conversion vers `uint` peut être utilisé au lieu de `MakePixel`:

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

La seule question est la suivante: Le format d’entier de la `SKColor` valeur est-il dans l' `SKColorType.Rgba8888` ordre du type de couleur `SKColorType.Bgra8888` , ou le type de couleur, ou s’agit-il d’une autre valeur? La réponse à cette question doit être révélée peu de temps.

### <a name="the-setpixels-method"></a>La méthode SetPixels

`SKBitmap` définit également une méthode nommée [ `SetPixels` ](xref:SkiaSharp.SKBitmap.SetPixels(System.IntPtr)), que vous appelez comme suit :

```csharp
bitmap.SetPixels(intPtr);
```

N’oubliez pas que `GetPixels` Obtient un `IntPtr` référençant le bloc de mémoire utilisé par l’image bitmap pour stocker ses pixels. Le `SetPixels` appeler _remplace_ ce bloc de mémoire avec le bloc de mémoire référencé par le `IntPtr` spécifié en tant que le `SetPixels` argument. L’image bitmap puis permet de libérer le bloc de mémoire qu’il utilisait précédemment. La prochaine fois `GetPixels` est appelé, il obtient le bloc de mémoire avec `SetPixels`.

Dans un premier temps, il semble que si `SetPixels` vous offre pas plus de puissance et de performances que `GetPixels` tout en étant moins pratique. Avec `GetPixels` obtenir le bloc de mémoire bitmap et d’y accéder. Avec `SetPixels` vous allouez et accéder à la partie de la mémoire et puis le définir en tant que le bloc de mémoire bitmap.

Toutefois, `SetPixels` l’utilisation de offre un avantage syntaxique distinct: Elle vous permet d’accéder aux bits de pixel de la bitmap à l’aide d’un tableau. Voici la méthode `GradientBitmapPage` qui illustre cette technique. Tout d’abord, la méthode définit un tableau d’octets à plusieurs dimensions correspondant aux octets de pixels étaient du bitmap. La première dimension est la ligne, la deuxième dimension est la colonne et la troisième dimension correspond aux quatre composants de chaque pixel :

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

Ensuite, une fois le tableau a été rempli avec des pixels, un `unsafe` bloc et un `fixed` instruction permet d’obtenir un pointeur d’octet qui pointe vers ce tableau. Ce pointeur d’octet peut ensuite être casté en un `IntPtr` à passer à `SetPixels`.

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

Le `MakePixel` méthode est à nouveau utilisée pour combiner les composants de couleur dans un pixel 32 bits.

Par souci d’exhaustivité, voici le même code, mais avec un `SKColor` valeur convertie en un entier non signé :

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

Le constructeur de la **dégradé de couleur** page appelle toutes les huit méthodes indiquées ci-dessus et enregistre les résultats :

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

Le constructeur conclut en créant un `SKCanvasView` pour afficher les bitmaps résultants. Le `PaintSurface` Gestionnaire divise sa surface en huit des rectangles et des appels `Display` pour afficher chacun d’eux :

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

Pour permettre au compilateur d’optimiser le code, cette page a été exécutée dans **version** mode. Voici la page en cours d’exécution sur un simulateur iPhone 8 sur un MacBook Pro, un téléphone Nexus 5 Android et Surface Pro 3 exécutant Windows 10. En raison des différences de matériel, éviter la comparaison de la durée des performances entre les appareils, mais elles aux moments relatives sur chaque appareil :

[![Bitmap dégradé](pixel-bits-images/GradientBitmap.png "Bitmap dégradé")](pixel-bits-images/GradientBitmap-Large.png#lightbox)

Voici un tableau qui consolide les temps d’exécution en millisecondes :

| API       | Type de données | iOS  | Android | UWP  |
| --------- | --------- | ----:| -------:| ----:|
| SetPixel  |           | 3.17 |   10.77 | 3,49 |
| Pixels    |           | 0,32 |    1.23 | 0,07 |
| GetPixels | byte      | 0,09 |    0,24 | 0.10 |
|           | uint      | 0,06 |    0.26 | 0,05 |
|           | SKColor   | 0,29 |    0,99 | 0,07 |
| SetPixels | byte      | 1.33 |    6.78 | 0.11 |
|           | uint      | 0,14 |    0.69 | 0,06 |
|           | SKColor   | 0.35 |    1.93 | 0.10 |

Comme prévu, l’appel `SetPixel` fois 65 536 est la moins effeicient permet de définir les pixels d’une image bitmap. Remplissage d’un `SKColor` tableau et en définissant le `Pixels` propriété sont bien meilleure et même les comparaisons avec de la `GetPixels` et `SetPixels` techniques. Utilisation de `uint` les valeurs de pixel est généralement plus rapide que paramètre distinct `byte` composants et la conversion le `SKColor` valeur en entier non signé ajoute une certaine surcharge pour le processus.

Il est également intéressant de comparer les différents dégradés: Les premières lignes de chaque plateforme sont les mêmes et affichent le dégradé tel qu’il a été prévu. Cela signifie que le `SetPixel` (méthode) et le `Pixels` propriété créer correctement des pixels à partir de couleurs, quel que soit le format de pixel sous-jacent.

Les deux lignes suivantes du iOS et Android captures d’écran sont également les mêmes, ce qui permet de confirmer que les petits `MakePixel` méthode est correctement définie pour la valeur par défaut `Rgba8888` format de pixel pour ces plateformes.

La ligne inférieure des captures d’écran Android et iOS est vers l’arrière, ce qui indique que l’entier non signé est obtenu en castant un `SKColor` valeur se présente sous la forme :

AARRGGBB

Les octets sont dans l’ordre :

BB GG RR AA

Il s’agit du `Bgra8888` classement plutôt que `Rgba8888` classement. Le `Brga8888` format est la valeur par défaut pour la plateforme Windows universelle, c’est pourquoi les dégradés sur la dernière ligne de cette capture d’écran sont les mêmes que la première ligne. Mais les deux lignes intermédiaires sont incorrectes, car le code de création de ces bitmaps supposées un `Rgba8888` classement.

Si vous souhaitez utiliser le même code pour accéder aux bits de pixel sur chaque plateforme, vous pouvez créer explicitement un `SKBitmap` à l’aide du `Rgba8888` ou `Bgra8888` format. Si vous souhaitez effectuer un cast `SKColor` valeurs pixels du bitmap, utilisez `Bgra8888`.

## <a name="random-access-of-pixels"></a>Accès aléatoire de pixels

Le `FillBitmapBytePtr` et `FillBitmapUintPtr` méthodes dans le **Bitmap dégradé** page tiré parti de `for` boucles conçus pour remplir l’image bitmap de manière séquentielle, à partir de la ligne supérieure à la ligne du bas et dans chaque ligne de gauche à droite. Le pixel puisse être défini avec la même instruction qui incrémenté le pointeur.

Il est parfois nécessaire accéder aux pixels au hasard, plutôt que séquentielle. Si vous utilisez le `GetPixels` approche, vous devez calculer les pointeurs basés sur la ligne et colonne. Cela est illustré dans le **arc-en-ciel sinus** page, qui crée une image bitmap montrant un arc-en-ciel sous la forme d’un cycle d’une courbe de sinus.

Les couleurs de l’arc-en-ciel sont plus faciles à créer à l’aide de la palette de couleurs TSL (teinte, saturation, luminosité). Le `SKColor.FromHsl` méthode crée un `SKColor` valeur à l’aide de valeurs de teinte comprises entre 0 et 360 (comme les angles d’un cercle mais allant de rouge, vert et bleu et revenir à rouge) et les valeurs de saturation et de luminosité comprises entre 0 et 100. Pour les couleurs de l’arc-en-ciel, la saturation doit être définie sur un maximum de 100 et la luminosité à un point milieu de 50.

**Sinus de l’arc-en-ciel** crée cette image en effectuant une boucle dans les lignes de l’image bitmap, puis une boucle dans les valeurs de teinte 360. À partir de chaque valeur de teinte, il calcule une colonne de bitmap qui est également basée sur une valeur du sinus :

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

Notez que le constructeur crée l’image bitmap selon le `SKColorType.Bgra8888` format :

```csharp
bitmap = new SKBitmap(360 * 3, 1024, SKColorType.Bgra8888, SKAlphaType.Unpremul);
```

Cela permet au programme d’utiliser la conversion de `SKColor` des valeurs dans `uint` pixels sans problème. Bien qu’il ne joue un rôle dans ce programme en particulier, lorsque vous utilisez le `SKColor` conversion pour définir les pixels, vous devez également spécifier `SKAlphaType.Unpremul` car `SKColor` ne Prémultiplier ses composants de couleur par la valeur alpha.

Le constructeur utilise ensuite le `GetPixels` méthode pour obtenir un pointeur vers le premier pixel de l’image bitmap :

```csharp
uint* basePtr = (uint*)bitmap.GetPixels().ToPointer();
```

Pour toute ligne particulière et de la colonne, une valeur de décalage doit être ajoutée à `basePtr`. Ce décalage est la ligne par la largeur de la bitmap, ainsi que la colonne :

```csharp
uint* ptr = basePtr + bitmap.Width * row + col;
```

Le `SKColor` valeur est stockée dans la mémoire à l’aide de ce pointeur :

```csharp
*ptr = (uint)SKColor.FromHsl(hue, 100, 50);
```

Dans le `PaintSurface` Gestionnaire de la `SKCanvasView`, la bitmap est étirée pour remplir la zone d’affichage :

[![Sinus de l’arc-en-ciel](pixel-bits-images/RainbowSine.png "arc-en-ciel sinus")](pixel-bits-images/RainbowSine-Large.png#lightbox)

## <a name="from-one-bitmap-to-another"></a>À partir d’une bitmap vers un autre

Très nombreuses tâches de traitement d’image impliquent la modification des pixels comme ils sont transférés à partir d’une bitmap vers un autre. Cette technique est illustrée dans le **réglage des couleurs** page. La page se charge une des ressources bitmap et vous permet de modifier l’image à l’aide de trois `Slider` vues :

[![Ajustement de couleur](pixel-bits-images/ColorAdjustment.png "ajustement de couleur")](pixel-bits-images/ColorAdjustment-Large.png#lightbox)

Pour chaque couleur du pixel, le premier `Slider` ajoute une valeur comprise entre 0 et 360 la teinte, mais utilise ensuite l’opérateur modulo pour conserver le résultat comprise entre 0 et 360, efficacement un décalage dans le spectre des couleurs (comme la capture d’écran UWP montre). La seconde `Slider` vous permet de sélectionner un facteur multiplicateur entre 0,5 et 2 à appliquer à la saturation et la troisième `Slider` fait de même pour la luminosité, comme illustré dans la capture d’écran Android.

Le programme maintient deux images bitmaps, la bitmap source d’origine nommée `srcBitmap` et le bitmap de destination ajustée nommé `dstBitmap`. Chaque fois qu’un `Slider` est déplacé, le programme calcule tous les pixels de nouveau dans `dstBitmap`. Bien sûr, les utilisateurs seront faire des essais en déplaçant le `Slider` vues très rapidement, donc les meilleures performances que vous pouvez gérer. Cela implique la `GetPixels` méthode pour les bitmaps de la source et la destination.

Le **réglage des couleurs** page ne contrôle pas le format de couleur des images bitmap source et de destination. Au lieu de cela, elle contient une logique légèrement différente pour `SKColorType.Rgba8888` et `SKColorType.Bgra8888` formats. La source et la destination peuvent être de différents formats, et le programme continue de fonctionner.

Voici le programme à l’exception de l’essentiel `TransferPixels` méthode qui transfère les pixels forment la source vers la destination. Le constructeur affecte `dstBitmap` égal à `srcBitmap`. Le `PaintSurface` gestionnaire affiche `dstBitmap`:

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

Le `ValueChanged` gestionnaire pour le `Slider` vues calcule les valeurs d’ajustement et les appels `TransferPixels`.

L’intégralité de `TransferPixels` méthode est marquée comme `unsafe`. Il commence par l’obtention de pointeurs d’octets pour les bits de pixel de ces deux bitmaps, puis effectue une itération sur toutes les lignes et colonnes. À partir de l’image bitmap source, la méthode obtient quatre octets pour chaque pixel. Il peut s’agir d’une le `Rgba8888` ou `Bgra8888` ordre. Vérification pour le type de couleur permet une `SKColor` valeur doit être créé. Les composants TSL sont ensuite extraites, ajustés et utilisés pour recréer le `SKColor` valeur. Selon que le bitmap de destination est `Rgba8888` ou `Bgra8888`, les octets sont stockés dans le bitmp de destination :

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

Il est probable que les performances de cette méthode peut être amélioré encore plus en créant des méthodes distinctes pour les différentes combinaisons de types de couleur des images bitmap source et de destination et éviter la vérification du type pour chaque pixel. Une autre option consiste à avoir plusieurs `for` effectue une itération pour le `col` variable en fonction du type de couleur.

## <a name="posterization"></a>Postérisation

Un autre travail commun qui implique l’accès aux bits de pixel est _Postérisation_. Le nombre si couleurs encodées en pixels d’une bitmap est réduit afin que le résultat ressemble à un poster dessiné à l’aide d’une palette de couleurs limitée.

Le **cet** page effectue ce processus sur une des images monkey :

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

Le code dans le constructeur accède à chaque pixel, effectue une opération AND au niveau du bit avec la valeur 0xE0E0E0FF, puis stocke le résultat dans l’image bitmap. Les valeurs 0xE0E0E0FF conserve les 3 bits de poids fort de chaque composant de couleur et 5 bits inférieurs à 0. Au lieu de 2<sup>24</sup> ou 16 777 216 couleurs, l’image bitmap est réduite à 2<sup>9</sup> ou 512 couleurs :

[![Postérisation](pixel-bits-images/Posterize.png "Postérisation")](pixel-bits-images/Postérisation-Large.png#lightbox)

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
