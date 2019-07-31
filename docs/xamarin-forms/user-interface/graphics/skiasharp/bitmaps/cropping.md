---
title: Rognage bitmaps SkiaSharp
description: Découvrez comment utiliser SkiaSharp pour concevoir une interface utilisateur de manière interactive décrivant un rectangle de rognage.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 0A79AB27-C69F-4376-8FFE-FF46E4783F30
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 50174844100eb852ac7daf5ce3f33b02b490ceb2
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646657"
---
# <a name="cropping-skiasharp-bitmaps"></a>Rognage bitmaps SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Le [ **Creating and de dessin SkiaSharp Bitmaps** ](drawing.md) article décrit comment un `SKBitmap` objet peut être transmis à un `SKCanvas` constructeur. N’importe quelle méthode de dessin appelée sur ce graphique de causes de canevas à restituer sur la bitmap. Ces méthodes de dessin incluent `DrawBitmap`, ce qui signifie que cette technique permet le transfert de tout ou partie d’une bitmap vers une autre image bitmap, peut-être avec des transformations appliquées.

Vous pouvez utiliser cette technique pour rogner une image bitmap en appelant le [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode avec les rectangles de source et de destination :

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Toutefois, les applications qui implémentent le rognage souvent fournissent une interface pour l’utilisateur à sélectionner le rectangle de rognage de manière interactive :

![Exemple de rognage](cropping-images/CroppingSample.png "rognage exemple")

Cet article se concentre sur cette interface.

## <a name="encapsulating-the-cropping-rectangle"></a>Qui encapsule le rectangle de rognage

Il est utile isoler une partie de la logique de rognage dans une classe nommée `CroppingRectangle`. Les paramètres de constructeur incluent un rectangle maximal, qui est généralement la taille de la bitmap est rognée, et un rapport hauteur / largeur facultatif. Le constructeur définit tout d’abord un rectangle de rognage initial, ce qui rend publique dans le `Rect` propriété de type `SKRect`. Ce rectangle de rognage initial est 80 % de la largeur et hauteur du rectangle de bitmap, mais elle est ensuite ajustée si un rapport hauteur / largeur est spécifié :

```csharp
class CroppingRectangle
{
    ···
    SKRect maxRect;             // generally the size of the bitmap
    float? aspectRatio;

    public CroppingRectangle(SKRect maxRect, float? aspectRatio = null)
    {
        this.maxRect = maxRect;
        this.aspectRatio = aspectRatio;

        // Set initial cropping rectangle
        Rect = new SKRect(0.9f * maxRect.Left + 0.1f * maxRect.Right,
                          0.9f * maxRect.Top + 0.1f * maxRect.Bottom,
                          0.1f * maxRect.Left + 0.9f * maxRect.Right,
                          0.1f * maxRect.Top + 0.9f * maxRect.Bottom);

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            SKRect rect = Rect;
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;
                rect.Left = (maxRect.Width - width) / 2;
                rect.Right = rect.Left + width;
            }
            else
            {
                float height = rect.Width / aspect;
                rect.Top = (maxRect.Height - height) / 2;
                rect.Bottom = rect.Top + height;
            }

            Rect = rect;
        }
    }
    
    public SKRect Rect { set; get; }
    ···
}
```

Une information utile qui `CroppingRectangle` également met à disposition est un tableau de `SKPoint` valeurs correspondant aux quatre coins du rectangle de rognage dans l’ordre supérieur gauche, supérieur droit, inférieur droit, inférieur gauche :

```csharp
class CroppingRectangle
{
    ···
    public SKPoint[] Corners
    {
        get
        {
            return new SKPoint[]
            {
                new SKPoint(Rect.Left, Rect.Top),
                new SKPoint(Rect.Right, Rect.Top),
                new SKPoint(Rect.Right, Rect.Bottom),
                new SKPoint(Rect.Left, Rect.Bottom)
            };
        }
    }
    ···
}
```

Ce tableau est utilisé dans la méthode suivante, qui est appelée `HitTest`. Le `SKPoint` paramètre est un point correspondant à une pression tactile doigt ou un clic de souris. La méthode retourne un index (0, 1, 2 ou 3) correspondant à l’angle couvertes par le pointeur doigt ou de la souris, dans un rayon donné par le `radius` paramètre : 

```csharp
class CroppingRectangle
{
    ···
    public int HitTest(SKPoint point, float radius)
    {
        SKPoint[] corners = Corners;

        for (int index = 0; index < corners.Length; index++)
        {
            SKPoint diff = point - corners[index];
                
            if ((float)Math.Sqrt(diff.X * diff.X + diff.Y * diff.Y) < radius)
            {
                return index;
            }
        }

        return -1;
    }
    ···
}
```

Si le point tactile ou de la souris n’était pas dans `radius` unités de n’importe quel angle, la méthode retourne &ndash;1.

La dernière méthode dans `CroppingRectangle` est appelée `MoveCorner`, qui est appelée en réponse à touch ou le déplacement de la souris. Les deux paramètres indiquent l’index de l’angle en cours de déplacement et le nouvel emplacement de cet angle. La première moitié de la méthode ajuste le rectangle de rognage basé sur le nouvel emplacement de l’angle, mais toujours dans les limites de `maxRect`, qui est la taille de la bitmap. Cette logique tienne également compte de la `MINIMUM` champ afin d’éviter le rectangle de rognage de réduction à rien :

```csharp
class CroppingRectangle
{
    const float MINIMUM = 10;   // pixels width or height
    ···
    public void MoveCorner(int index, SKPoint point)
    {
        SKRect rect = Rect;

        switch (index)
        {
            case 0: // upper-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 1: // upper-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Top = Math.Min(Math.Max(point.Y, maxRect.Top), rect.Bottom - MINIMUM);
                break;

            case 2: // lower-right
                rect.Right = Math.Max(Math.Min(point.X, maxRect.Right), rect.Left + MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;

            case 3: // lower-left
                rect.Left = Math.Min(Math.Max(point.X, maxRect.Left), rect.Right - MINIMUM);
                rect.Bottom = Math.Max(Math.Min(point.Y, maxRect.Bottom), rect.Top + MINIMUM);
                break;
        }

        // Adjust for aspect ratio
        if (aspectRatio.HasValue)
        {
            float aspect = aspectRatio.Value;

            if (rect.Width > aspect * rect.Height)
            {
                float width = aspect * rect.Height;

                switch (index)
                {
                    case 0:
                    case 3: rect.Left = rect.Right - width; break;
                    case 1:
                    case 2: rect.Right = rect.Left + width; break;
                }
            }
            else
            {
                float height = rect.Width / aspect;

                switch (index)
                {
                    case 0:
                    case 1: rect.Top = rect.Bottom - height; break;
                    case 2:
                    case 3: rect.Bottom = rect.Top + height; break;
                }
            }
        }

        Rect = rect;
    }
}
```

La seconde moitié de la méthode s’ajuste pour le rapport hauteur / largeur facultatif.

N’oubliez pas que tous les éléments de cette classe sont exprimé en unités de pixels.

## <a name="a-canvas-view-just-for-cropping"></a>Une vue de canevas simplement de rognage

Le `CroppingRectangle` vous venez de voir de classe est utilisée par le `PhotoCropperCanvasView` classe qui dérive de `SKCanvasView`. Cette classe est responsable de l’affichage de l’image bitmap et le rectangle de rognage, ainsi que la gestion des événements tactiles ou de la souris pour modifier le rectangle de rognage.

Le `PhotoCropperCanvasView` constructeur requiert une image bitmap. Un rapport hauteur / largeur est facultatif. Le constructeur instancie un objet de type `CroppingRectangle` selon cette bitmap et le rapport hauteur / largeur et l’enregistre en tant que champ :

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        this.bitmap = bitmap;

        SKRect bitmapRect = new SKRect(0, 0, bitmap.Width, bitmap.Height);
        croppingRect = new CroppingRectangle(bitmapRect, aspectRatio);
        ···
    }
    ···
}
```

Étant donné que cette classe dérive `SKCanvasView`, il n’a pas besoin d’installer un gestionnaire pour le `PaintSurface` événement. Il peut se substitue à la place son `OnPaintSurface` (méthode). La méthode affiche l’image bitmap et utilise quelques `SKPaint` objets enregistrés en tant que champs pour dessiner le rectangle de rognage actuel :

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    const int CORNER = 50;      // pixel length of cropper corner
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;
    ···
    // Drawing objects
    SKPaint cornerStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 10
    };

    SKPaint edgeStroke = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.White,
        StrokeWidth = 2
    };
    ···
    protected override void OnPaintSurface(SKPaintSurfaceEventArgs args)
    {
        base.OnPaintSurface(args);

        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear(SKColors.Gray);

        // Calculate rectangle for displaying bitmap 
        float scale = Math.Min((float)info.Width / bitmap.Width, (float)info.Height / bitmap.Height);
        float x = (info.Width - scale * bitmap.Width) / 2;
        float y = (info.Height - scale * bitmap.Height) / 2;
        SKRect bitmapRect = new SKRect(x, y, x + scale * bitmap.Width, y + scale * bitmap.Height);
        canvas.DrawBitmap(bitmap, bitmapRect);

        // Calculate a matrix transform for displaying the cropping rectangle
        SKMatrix bitmapScaleMatrix = SKMatrix.MakeIdentity();
        bitmapScaleMatrix.SetScaleTranslate(scale, scale, x, y);

        // Display rectangle
        SKRect scaledCropRect = bitmapScaleMatrix.MapRect(croppingRect.Rect);
        canvas.DrawRect(scaledCropRect, edgeStroke);

        // Display heavier corners
        using (SKPath path = new SKPath())
        {
            path.MoveTo(scaledCropRect.Left, scaledCropRect.Top + CORNER);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Left + CORNER, scaledCropRect.Top);

            path.MoveTo(scaledCropRect.Right - CORNER, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Top + CORNER);

            path.MoveTo(scaledCropRect.Right, scaledCropRect.Bottom - CORNER);
            path.LineTo(scaledCropRect.Right, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Right - CORNER, scaledCropRect.Bottom);

            path.MoveTo(scaledCropRect.Left + CORNER, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom);
            path.LineTo(scaledCropRect.Left, scaledCropRect.Bottom - CORNER);

            canvas.DrawPath(path, cornerStroke);
        }

        // Invert the transform for touch tracking
        bitmapScaleMatrix.TryInvert(out inverseBitmapMatrix);
    }
    ···
}
```

Le code dans la `CroppingRectangle` classe base le rectangle de rognage sur la taille en pixels de la bitmap. Toutefois, l’affichage de l’image bitmap par la `PhotoCropperCanvasView` classe est mis à l’échelle en fonction de la taille de la zone d’affichage. Le `bitmapScaleMatrix` calculée dans le `OnPaintSurface` remplacer des mappages depuis les pixels de bitmap à la taille et la position de l’image bitmap, tel qu’il est affiché. Cette matrice est ensuite utilisée pour transformer le rectangle de rognage afin qu’elle peut être affichée par rapport à l’image bitmap.

La dernière ligne de la `OnPaintSurface` remplacement prend l’inverse de la `bitmapScaleMatrix` et l’enregistre en tant que le `inverseBitmapMatrix` champ. Cela est utilisé pour le traitement de tactile.

A `TouchEffect` objet est instancié comme un champ, et le constructeur attache un gestionnaire à la `TouchAction` événement, mais le `TouchEffect` doit être ajouté à la `Effects` collection de la _parent_ de la `SKCanvasView`dérivés, afin que soit terminé le `OnParentSet` remplacer :

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    const int RADIUS = 100;     // pixel radius of touch hit-test
    ···
    CroppingRectangle croppingRect;
    SKMatrix inverseBitmapMatrix;

    // Touch tracking 
    TouchEffect touchEffect = new TouchEffect();
    struct TouchPoint
    {
        public int CornerIndex { set; get; }
        public SKPoint Offset { set; get; }
    }

    Dictionary<long, TouchPoint> touchPoints = new Dictionary<long, TouchPoint>();
    ···
    public PhotoCropperCanvasView(SKBitmap bitmap, float? aspectRatio = null)
    {
        ···
        touchEffect.TouchAction += OnTouchEffectTouchAction;
    }
    ···
    protected override void OnParentSet()
    {
        base.OnParentSet();

        // Attach TouchEffect to parent view
        Parent.Effects.Add(touchEffect);
    }
    ···
    void OnTouchEffectTouchAction(object sender, TouchActionEventArgs args)
    {
        SKPoint pixelLocation = ConvertToPixel(args.Location);
        SKPoint bitmapLocation = inverseBitmapMatrix.MapPoint(pixelLocation);

        switch (args.Type)
        {
            case TouchActionType.Pressed:
                // Convert radius to bitmap/cropping scale
                float radius = inverseBitmapMatrix.ScaleX * RADIUS;

                // Find corner that the finger is touching
                int cornerIndex = croppingRect.HitTest(bitmapLocation, radius);

                if (cornerIndex != -1 && !touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = new TouchPoint
                    {
                        CornerIndex = cornerIndex,
                        Offset = bitmapLocation - croppingRect.Corners[cornerIndex]
                    };

                    touchPoints.Add(args.Id, touchPoint);
                }
                break;

            case TouchActionType.Moved:
                if (touchPoints.ContainsKey(args.Id))
                {
                    TouchPoint touchPoint = touchPoints[args.Id];
                    croppingRect.MoveCorner(touchPoint.CornerIndex, 
                                            bitmapLocation - touchPoint.Offset);
                    InvalidateSurface();
                }
                break;

            case TouchActionType.Released:
            case TouchActionType.Cancelled:
                if (touchPoints.ContainsKey(args.Id))
                {
                    touchPoints.Remove(args.Id);
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Xamarin.Forms.Point pt)
    {
        return new SKPoint((float)(CanvasSize.Width * pt.X / Width),
                           (float)(CanvasSize.Height * pt.Y / Height));
    }
}
```

Les événements tactiles traitement par le `TouchAction` gestionnaire se trouvent dans les unités indépendantes du périphérique. Ces doivent tout d’abord être convertis en pixels à l’aide de la `ConvertToPixel` méthode en bas de la classe, puis convertis en `CroppingRectangle` unités à l’aide `inverseBitmapMatrix`.

Pour `Pressed` événements, le `TouchAction` gestionnaire appelle le `HitTest` méthode de `CroppingRectangle`. Si cela retourne un index autre que &ndash;1, puis un des angles du rectangle de rognage est en cours de manipulation. Que les index et un offset du point tactile réelle à partir de l’angle est stocké dans un `TouchPoint` de l’objet et ajouté à la `touchPoints` dictionnaire.

Pour le `Moved` événement, le `MoveCorner` méthode de `CroppingRectangle` est appelée pour déplacer le coin, avec des ajustements possibles pour le rapport hauteur / largeur.

À tout moment, un programme à l’aide `PhotoCropperCanvasView` peuvent accéder à la `CroppedBitmap` propriété. Cette propriété utilise la `Rect` propriété de la `CroppingRectangle` pour créer une nouvelle image bitmap de la taille rognée. La version de `DrawBitmap` avec la source et de destination rectangles puis extrait un sous-ensemble de l’image bitmap d’origine :

```csharp
class PhotoCropperCanvasView : SKCanvasView
{
    ···
    SKBitmap bitmap;
    CroppingRectangle croppingRect;
    ···
    public SKBitmap CroppedBitmap
    {
        get
        {
            SKRect cropRect = croppingRect.Rect;
            SKBitmap croppedBitmap = new SKBitmap((int)cropRect.Width, 
                                                  (int)cropRect.Height);
            SKRect dest = new SKRect(0, 0, cropRect.Width, cropRect.Height);
            SKRect source = new SKRect(cropRect.Left, cropRect.Top, 
                                       cropRect.Right, cropRect.Bottom);

            using (SKCanvas canvas = new SKCanvas(croppedBitmap))
            {
                canvas.DrawBitmap(bitmap, source, dest);
            }

            return croppedBitmap;
        }
    }
    ···
}
```

## <a name="hosting-the-photo-cropper-canvas-view"></a>Hébergement de la vue de canevas cropper photo

Avec ces deux classes de la logique de rognage, de gestion de la **Photo rognage** page dans le **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application a très peu de travail à faire. Le fichier XAML instancie un `Grid` pour héberger le `PhotoCropperCanvasView` et un **fait** bouton :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoCroppingPage"
             Title="Photo Cropping">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Grid x:Name="canvasViewHost"
              Grid.Row="0"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="1"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

Le `PhotoCropperCanvasView` ne peut pas être instanciée dans le fichier XAML, car elle requiert un paramètre de type `SKBitmap`.

Au lieu de cela, le `PhotoCropperCanvasView` est instancié dans le constructeur du fichier code-behind en utilisant l’une des images bitmap de ressource :

```csharp
public partial class PhotoCroppingPage : ContentPage
{
    PhotoCropperCanvasView photoCropper;
    SKBitmap croppedBitmap;

    public PhotoCroppingPage ()
    {
        InitializeComponent ();

        SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(GetType(),
            "SkiaSharpFormsDemos.Media.MountainClimbers.jpg");

        photoCropper = new PhotoCropperCanvasView(bitmap);
        canvasViewHost.Children.Add(photoCropper);
    }

    void OnDoneButtonClicked(object sender, EventArgs args)
    {
        croppedBitmap = photoCropper.CroppedBitmap;

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
        canvas.DrawBitmap(croppedBitmap, info.Rect, BitmapStretch.Uniform);
    }
}
```

L’utilisateur peut ensuite manipuler le rectangle de rognage :

[![Photo Cropper 1](cropping-images/PhotoCropping1.png "Photo Cropper 1")](cropping-images/PhotoCropping1-Large.png#lightbox)

Lorsqu’un rectangle de rognage bon a été défini, cliquez sur le **fait** bouton. Le `Clicked` gestionnaire obtient la bitmap rognée à partir de la `CroppedBitmap` propriété de `PhotoCropperCanvasView`et remplace tout le contenu de la page avec un nouveau `SKCanvasView` objet qui affiche cette image bitmap rognée :

[![Photo Cropper 2](cropping-images/PhotoCropping2.png "Photo Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

Essayez de définir le deuxième argument de `PhotoCropperCanvasView` à 1.78f (par exemple) :

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Vous verrez le rectangle de rognage limité à 16 : 9 proportions de caractéristique de télévision haute définition.

<a name="tile-division" />

## <a name="dividing-a-bitmap-into-tiles"></a>Division d’une image bitmap en mosaïques

Une version de Xamarin.Forms de la célèbre 14 et 15 puzzle est apparue dans le chapitre 22 du livre [ _création d’applications mobiles avec Xamarin.Forms_ ](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) et peut être téléchargé en tant que [  **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Toutefois, le puzzle devient plus intéressante (et souvent s’avérer plus difficile) si elle est basée sur une image à partir de votre propre bibliothèque de photos.

Cette version du puzzle 14 et 15 fait partie de la **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** application et se compose d’une série de pages intitulée **Photo Puzzle**.

Le **PhotoPuzzlePage1.xaml** fichier se compose d’un `Button`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage1"
             Title="Photo Puzzle">
    
    <Button Text="Pick a photo from your library"
            VerticalOptions="CenterAndExpand" 
            HorizontalOptions="CenterAndExpand"
            Clicked="OnPickButtonClicked"/>
    
</ContentPage>
```

Le fichier code-behind implémente une `Clicked` Gestionnaire utilise la `IPhotoLibrary` service de dépendance pour permettre à l’utilisateur de choisir une photo à partir de la bibliothèque de photos :

```csharp
public partial class PhotoPuzzlePage1 : ContentPage
{
    public PhotoPuzzlePage1 ()
    {
        InitializeComponent ();
    }

    async void OnPickButtonClicked(object sender, EventArgs args)
    {
        IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
        using (Stream stream = await photoLibrary.PickPhotoAsync())
        {
            if (stream != null)
            {
                SKBitmap bitmap = SKBitmap.Decode(stream);

                await Navigation.PushAsync(new PhotoPuzzlePage2(bitmap));
            }
        }
    }
}
```

La méthode accède ensuite à `PhotoPuzzlePage2`, en passant pour le constructeur de l’image bitmap sélectionnée.

Il est possible que la photo sélectionnée à partir de la bibliothèque n’est pas orientée tel qu’il est apparu dans la bibliothèque de photos, mais est pivoté ou envers. (Il s’agit en particulier un problème avec les appareils iOS). Pour cette raison, `PhotoPuzzlePage2` vous permet de faire pivoter l’image vers une orientation souhaitée. Le fichier XAML contient trois boutons marqués **90&#x00B0; droite** (ce qui signifie que dans le sens horaire), **90&#x00B0; gauche** (vers la gauche), et **fait**.

Le fichier code-behind implémente la logique de rotation de bitmap indiquée dans l’article  **[créer et dessiner sur les SkiaSharp Bitmaps](drawing.md#rotating-bitmaps)** . L’utilisateur peut faire pivoter l’image de 90 degrés dans le sens horaire ou antihoraire autant de fois : 

```csharp
public partial class PhotoPuzzlePage2 : ContentPage
{
    SKBitmap bitmap;

    public PhotoPuzzlePage2 (SKBitmap bitmap)
    {
        this.bitmap = bitmap;

        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        canvas.Clear();
        canvas.DrawBitmap(bitmap, info.Rect, BitmapStretch.Uniform);
    }

    void OnRotateRightButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(bitmap.Height, 0);
            canvas.RotateDegrees(90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    void OnRotateLeftButtonClicked(object sender, EventArgs args)
    {
        SKBitmap rotatedBitmap = new SKBitmap(bitmap.Height, bitmap.Width);

        using (SKCanvas canvas = new SKCanvas(rotatedBitmap))
        {
            canvas.Clear();
            canvas.Translate(0, bitmap.Width);
            canvas.RotateDegrees(-90);
            canvas.DrawBitmap(bitmap, new SKPoint());
        }

        bitmap = rotatedBitmap;
        canvasView.InvalidateSurface();
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        await Navigation.PushAsync(new PhotoPuzzlePage3(bitmap));
    }
}
```

Lorsque l’utilisateur clique sur le **fait** bouton, le `Clicked` gestionnaire accède à `PhotoPuzzlePage3`, en passant le bitmap pivoté finale dans le constructeur de la page.

`PhotoPuzzlePage3` permet la photo sera rognée. Le programme requiert une bitmap carrée à diviser une grille 4 par 4 de mosaïques.

Le **PhotoPuzzlePage3.xaml** fichier contient un `Label`, un `Grid` pour héberger le `PhotoCropperCanvasView`et un autre **fait** bouton :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="SkiaSharpFormsDemos.Bitmaps.PhotoPuzzlePage3"
             Title="Photo Puzzle">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

        <Label Text="Crop the photo to a square"
               Grid.Row="0"
               FontSize="Large"
               HorizontalTextAlignment="Center"
               Margin="5" />

        <Grid x:Name="canvasViewHost"
              Grid.Row="1"
              BackgroundColor="Gray"
              Padding="5" />

        <Button Text="Done"
                Grid.Row="2"
                HorizontalOptions="Center"
                Margin="5"
                Clicked="OnDoneButtonClicked" />
    </Grid>
</ContentPage>
```

Le fichier code-behind instancie le `PhotoCropperCanvasView` avec l’image bitmap passé à son constructeur. Notez que 1 est passée comme deuxième argument à `PhotoCropperCanvasView`. Ce rapport hauteur / largeur 1 force le rectangle de rognage pour être un carré :

```csharp
public partial class PhotoPuzzlePage3 : ContentPage
{
    PhotoCropperCanvasView photoCropper;

    public PhotoPuzzlePage3(SKBitmap bitmap)
    {
        InitializeComponent ();

        photoCropper = new PhotoCropperCanvasView(bitmap, 1f);
        canvasViewHost.Children.Add(photoCropper);
    }

    async void OnDoneButtonClicked(object sender, EventArgs args)
    {
        SKBitmap croppedBitmap = photoCropper.CroppedBitmap;
        int width = croppedBitmap.Width / 4;
        int height = croppedBitmap.Height / 4;

        ImageSource[] imgSources = new ImageSource[15];

        for (int row = 0; row < 4; row++)
        {
            for (int col = 0; col < 4; col++)
            {
                // Skip the last one!
                if (row == 3 && col == 3)
                    break;

                // Create a bitmap 1/4 the width and height of the original
                SKBitmap bitmap = new SKBitmap(width, height);
                SKRect dest = new SKRect(0, 0, width, height);
                SKRect source = new SKRect(col * width, row * height, (col + 1) * width, (row + 1) * height);

                // Copy 1/16 of the original into that bitmap
                using (SKCanvas canvas = new SKCanvas(bitmap))
                {
                    canvas.DrawBitmap(croppedBitmap, source, dest);
                }

                imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
            }
        }

        await Navigation.PushAsync(new PhotoPuzzlePage4(imgSources));
    }
}
```

Le **fait** Gestionnaire du bouton Obtient la largeur et la hauteur de la bitmap rognée (ces deux valeurs doivent être les mêmes) et il divise ensuite en 15 bitmaps distincts, chacun d’eux est 1/4 la largeur et la hauteur de l’original. (La dernière des bitmaps de 16 possible n’est pas créée.) Le `DrawBitmap` méthode avec rectangle source et de destination autorise une image bitmap à être créés en fonction de sous-ensemble d’une bitmap plus volumineuse.

## <a name="converting-to-xamarinforms-bitmaps"></a>Convertir des bitmaps de Xamarin.Forms

Dans le `OnDoneButtonClicked` (méthode), le tableau créé pour les 15 bitmaps est de type [ `ImageSource` ](xref:Xamarin.Forms.ImageSource):

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource` est le type de base de Xamarin.Forms qui encapsule une bitmap. Heureusement, SkiaSharp permet la conversion de bitmaps de SkiaSharp dans des bitmaps de Xamarin.Forms. Le **SkiaSharp.Views.Forms** assembly définit un [ `SKBitmapImageSource` ](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) classe qui dérive de `ImageSource` mais peuvent être créés selon une SkiaSharp `SKBitmap` objet. `SKBitmapImageSource` même définit les conversions entre `SKBitmapImageSource` et `SKBitmap`et c’est la façon dont `SKBitmap` objets sont stockés dans un tableau comme les bitmaps de Xamarin.Forms :

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Ce tableau de bitmaps est passé en tant que constructeur à `PhotoPuzzlePage4`. Cette page est entièrement Xamarin.Forms et n’utilise pas n’importe quel SkiaSharp. Il est très similaire à [ **XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle), donc il ne sera pas ici décrite, mais il affiche votre photo sélectionnée est divisé en mosaïques carrées 15 :

[![Photo de Puzzle 1](cropping-images/PhotoPuzzle1.png "Photo Puzzle 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

En appuyant sur la **Randomize** bouton combine toutes les mosaïques :

[![Photo de Puzzle 2](cropping-images/PhotoPuzzle2.png "Photo Puzzle 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

Maintenant vous pouvez les placer dans le bon ordre. Les vignettes dans la même ligne ou colonne en tant que le carré vide peuvent être cliqués pour les déplacer dans le carré vide. 

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
