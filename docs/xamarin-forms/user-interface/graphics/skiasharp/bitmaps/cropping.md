---
title : "recadrage des bitmaps SkiaSharp" Description : "Découvrez comment utiliser SkiaSharp pour concevoir une interface utilisateur pour décrivant un rectangle de rognage de manière interactive.
ms. Prod : xamarin ms. Technology : xamarin-skiasharp ms. AssetID : 0A79AB27-C69F-4376-8FFE-FF46E4783F30 auteur : davidbritch ms. Author : dabritch ms. Date : 07/17/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="cropping-skiasharp-bitmaps"></a>Rognage des bitmaps SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

L’article [**création et dessin de bitmaps SkiaSharp**](drawing.md) a décrit comment un `SKBitmap` objet peut être passé à un `SKCanvas` constructeur. Toute méthode de dessin appelée sur ce canevas provoque le rendu des graphiques sur l’image bitmap. Ces méthodes de dessin incluent `DrawBitmap` , ce qui signifie que cette technique autorise le transfert d’une partie ou de la totalité d’une image bitmap vers une autre bitmap, peut-être avec des transformations appliquées.

Vous pouvez utiliser cette technique pour rogner une image bitmap en appelant la [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) méthode avec des rectangles de source et de destination :

```csharp
canvas.DrawBitmap(bitmap, sourceRect, destRect);
```

Toutefois, les applications qui implémentent le rognage fournissent souvent une interface permettant à l’utilisateur de sélectionner de manière interactive le rectangle de rognage :

![Exemple de rognage](cropping-images/CroppingSample.png "Exemple de rognage")

Cet article se concentre sur cette interface.

## <a name="encapsulating-the-cropping-rectangle"></a>Encapsulation du rectangle de rognage

Il est utile d’isoler une partie de la logique de rognage dans une classe nommée `CroppingRectangle` . Les paramètres de constructeur incluent un rectangle maximal, qui est généralement la taille de l’image bitmap qui est rognée et un proportions facultatives. Le constructeur définit d’abord un rectangle de rognage initial, qu’il rend public dans la `Rect` propriété de type `SKRect` . Ce rectangle de rognage initial est 80% de la largeur et de la hauteur du rectangle de la bitmap, mais il est ajusté si des proportions sont spécifiées :

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

Une information utile qui `CroppingRectangle` est également disponible est un tableau de `SKPoint` valeurs correspondant aux quatre angles du rectangle de rognage dans l’ordre supérieur gauche, supérieur droit, inférieur droit, inférieur droit et inférieur gauche :

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

Ce tableau est utilisé dans la méthode suivante, qui est appelée `HitTest` . Le `SKPoint` paramètre est un point correspondant à un doigt Touch ou à un clic de souris. La méthode retourne un index (0, 1, 2 ou 3) correspondant au coin du doigt ou du pointeur de la souris sur une distance donnée par le `radius` paramètre :

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

Si le point tactile ou la souris n’était pas dans les `radius` unités d’un coin, la méthode retourne &ndash; 1.

La dernière méthode de `CroppingRectangle` est appelée `MoveCorner` , qui est appelée en réponse à un mouvement tactile ou à la souris. Les deux paramètres indiquent l’index du coin déplacé et le nouvel emplacement de ce coin. La première moitié de la méthode ajuste le rectangle de rognage en fonction du nouvel emplacement de l’angle, mais toujours dans les limites de `maxRect` , qui est la taille de l’image bitmap. Cette logique prend également en compte le `MINIMUM` champ pour éviter de réduire le rectangle de rognage en rien :

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

La seconde moitié de la méthode ajuste les proportions facultatives.

N’oubliez pas que tout ce qui se trouve dans cette classe est en unités de pixels.

## <a name="a-canvas-view-just-for-cropping"></a>Vue Canvas uniquement pour le rognage

La `CroppingRectangle` classe que vous venez de voir est utilisée par la `PhotoCropperCanvasView` classe, qui dérive de `SKCanvasView` . Cette classe est chargée d’afficher l’image bitmap et le rectangle de rognage, ainsi que de gérer les événements tactiles ou de souris pour la modification du rectangle de rognage.

Le `PhotoCropperCanvasView` constructeur requiert une image bitmap. Les proportions sont facultatives. Le constructeur instancie un objet de type `CroppingRectangle` en fonction de cette image bitmap et du rapport hauteur/largeur et l’enregistre sous la forme d’un champ :

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

Étant donné que cette classe dérive de `SKCanvasView` , elle n’a pas besoin d’installer un gestionnaire pour l' `PaintSurface` événement. Elle peut remplacer sa `OnPaintSurface` méthode. La méthode affiche la bitmap et utilise deux `SKPaint` objets enregistrés sous forme de champs pour dessiner le rectangle de rognage actuel :

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

Le code de la `CroppingRectangle` classe base le rectangle de rognage sur la taille en pixels de la bitmap. Toutefois, l’affichage de la bitmap par la `PhotoCropperCanvasView` classe est mis à l’échelle en fonction de la taille de la zone d’affichage. Le `bitmapScaleMatrix` calculé dans la `OnPaintSurface` substitution mappe des pixels de la bitmap à la taille et à la position de l’image bitmap telle qu’elle est affichée. Cette matrice est ensuite utilisée pour transformer le rectangle de rognage afin qu’il puisse être affiché par rapport à l’image bitmap.

La dernière ligne de la `OnPaintSurface` substitution prend l’inverse de et l' `bitmapScaleMatrix` enregistre en tant que `inverseBitmapMatrix` champ. Utilisé pour le traitement tactile.

Un `TouchEffect` objet est instancié en tant que champ, et le constructeur joint un gestionnaire à l' `TouchAction` événement, mais `TouchEffect` doit être ajouté à la `Effects` collection du _parent_ de la `SKCanvasView` dérivée, ce qui est fait dans la `OnParentSet` substitution :

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

Les événements tactiles traités par le `TouchAction` Gestionnaire se trouvent dans des unités indépendantes du périphérique. Celles-ci doivent d’abord être converties en pixels à l’aide de la `ConvertToPixel` méthode au bas de la classe, puis converties en `CroppingRectangle` unités à l’aide de `inverseBitmapMatrix` .

Pour les `Pressed` événements, le `TouchAction` Gestionnaire appelle la `HitTest` méthode de `CroppingRectangle` . Si cette valeur retourne un index autre que &ndash; 1, l’un des angles du rectangle de rognage est manipulé. Cet index et un décalage du point tactile réel à partir de l’angle sont stockés dans un `TouchPoint` objet et ajoutés au `touchPoints` dictionnaire.

Pour l' `Moved` événement, la `MoveCorner` méthode de `CroppingRectangle` est appelée pour déplacer l’angle, avec des ajustements possibles pour les proportions.

À tout moment, un programme qui utilise `PhotoCropperCanvasView` peut accéder à la `CroppedBitmap` propriété. Cette propriété utilise la `Rect` propriété du `CroppingRectangle` pour créer une image bitmap de la taille rognée. La version de `DrawBitmap` avec les rectangles de destination et sources extrait ensuite un sous-ensemble de la bitmap d’origine :

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

## <a name="hosting-the-photo-cropper-canvas-view"></a>Hébergement de la vue Canvas photo Cropper

Avec ces deux classes gérant la logique de rognage, la page de **rognage des photos** dans l’application **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** a très peu de travail. Le fichier XAML instancie un `Grid` pour héberger le `PhotoCropperCanvasView` et un bouton **terminé** :

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

Le `PhotoCropperCanvasView` ne peut pas être instancié dans le fichier XAML, car il requiert un paramètre de type `SKBitmap` .

Au lieu de cela, `PhotoCropperCanvasView` est instancié dans le constructeur du fichier code-behind à l’aide de l’une des bitmaps de ressource :

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

Quand un bon rectangle de rognage a été défini, cliquez sur le bouton **terminé** . Le `Clicked` Gestionnaire obtient la bitmap rognée à partir de la `CroppedBitmap` propriété de `PhotoCropperCanvasView` , et remplace tout le contenu de la page par un nouvel `SKCanvasView` objet qui affiche cette bitmap rognée :

[![Photo Cropper 2](cropping-images/PhotoCropping2.png "Photo Cropper 2")](cropping-images/PhotoCropping2-Large.png#lightbox)

Essayez de définir le deuxième argument de `PhotoCropperCanvasView` sur 1,78 f (par exemple) :

```csharp
photoCropper = new PhotoCropperCanvasView(bitmap, 1.78f);
```

Vous verrez le rectangle de rognage limité à une caractéristique de proportions de 16 à 9 de la télévision haute définition.

## <a name="dividing-a-bitmap-into-tiles"></a>Division d’une image bitmap en mosaïques

Une Xamarin.Forms version du célèbre puzzle 14-15 est apparue dans le chapitre 22 du livre [_création d’Mobile Apps avec Xamarin. Forms_](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) et peut être téléchargée en tant que [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). Toutefois, le puzzle devient plus amusant (et souvent plus difficile) lorsqu’il est basé sur une image de votre propre bibliothèque de photos.

Cette version du puzzle 14-15 fait partie de l’application **[SkiaSharpFormsDemos](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)** et se compose d’une série de pages intitulée **photo puzzle**.

Le fichier **PhotoPuzzlePage1. Xaml** se compose d’un `Button` :

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

Le fichier code-behind implémente un `Clicked` gestionnaire qui utilise le `IPhotoLibrary` service de dépendances pour permettre à l’utilisateur de choisir une photo dans la bibliothèque de photos :

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

La méthode accède ensuite à `PhotoPuzzlePage2` , en passant à constructeur la bitmap sélectionnée.

Il est possible que la photo sélectionnée à partir de la bibliothèque ne soit pas orientée telle qu’elle apparaissait dans la bibliothèque de photos, mais qu’elle soit pivotée ou renversée. (Ceci est particulièrement un problème avec les appareils iOS.) Pour cette raison, `PhotoPuzzlePage2` vous permet de faire pivoter l’image à l’orientation souhaitée. Le fichier XAML contient trois boutons étiquetés **90&#x00B0; droit** (sens des aiguilles d’une montre), **90&#x00B0; gauche** (dans le sens inverse des aiguilles d’une montre) et **terminé**.

Le fichier code-behind implémente la logique de rotation bitmap indiquée dans l’article **[création et dessin sur les bitmaps SkiaSharp](drawing.md#rotating-bitmaps)**. L’utilisateur peut faire pivoter l’image de 90 degrés dans le sens des aiguilles d’une montre ou d’un nombre de fois.

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

Quand l’utilisateur clique sur le bouton **terminé** , le `Clicked` Gestionnaire accède à `PhotoPuzzlePage3` , en passant le bitmap de rotation final dans le constructeur de la page.

`PhotoPuzzlePage3`autorise le rognage de la photo. Le programme requiert une image bitmap carrée pour se diviser en une grille 4 par 4 de vignettes.

Le fichier **PhotoPuzzlePage3. Xaml** contient un `Label` , un `Grid` pour héberger le `PhotoCropperCanvasView` et un autre bouton **terminé** :

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

Le fichier code-behind instancie le `PhotoCropperCanvasView` avec l’image bitmap passée à son constructeur. Notez que 1 est passé comme deuxième argument à `PhotoCropperCanvasView` . Ce proportions de 1 force le rectangle de rognage à être un carré :

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

Le gestionnaire de bouton **terminé** obtient la largeur et la hauteur de la bitmap rognée (ces deux valeurs doivent être identiques), puis les divise en 15 bitmaps distinctes, chacun d’entre eux étant de 1/4 la largeur et la hauteur de l’original. (La dernière des 16 bitmaps possibles n’est pas créée.) La `DrawBitmap` méthode avec un rectangle source et destination permet la création d’une image bitmap basée sur un sous-ensemble d’une bitmap plus grande.

## <a name="converting-to-xamarinforms-bitmaps"></a>Convertir en Xamarin.Forms bitmaps

Dans la `OnDoneButtonClicked` méthode, le tableau créé pour les 15 bitmaps est de type [`ImageSource`](xref:Xamarin.Forms.ImageSource) :

```csharp
ImageSource[] imgSources = new ImageSource[15];
```

`ImageSource`est le Xamarin.Forms type de base qui encapsule une image bitmap. Heureusement, SkiaSharp permet la conversion de bitmaps SkiaSharp en Xamarin.Forms bitmaps. L’assembly **SkiaSharp. views. Forms** définit une [`SKBitmapImageSource`](xref:SkiaSharp.Views.Forms.SKBitmapImageSource) classe qui dérive de, `ImageSource` mais qui peut être créée en fonction d’un `SKBitmap` objet SkiaSharp. `SKBitmapImageSource`définit même les conversions entre `SKBitmapImageSource` et `SKBitmap` , et c’est ainsi que les `SKBitmap` objets sont stockés dans un tableau en tant qu' Xamarin.Forms images bitmap :

```csharp
imgSources[4 * row + col] = (SKBitmapImageSource)bitmap;
```

Ce tableau de bitmaps est passé comme constructeur à `PhotoPuzzlePage4` . Cette page est entièrement Xamarin.Forms et n’utilise pas de SkiaSharp. Il est très similaire à [**XamagonXuzzle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter22/XamagonXuzzle). il ne sera donc pas décrit ici, mais il affiche la photo sélectionnée divisée en 15 vignettes carrées :

[![Puzzle photo 1](cropping-images/PhotoPuzzle1.png "Puzzle photo 1")](cropping-images/PhotoPuzzle1-Large.png#lightbox)

En appuyant sur le bouton **randomiser** , vous mélangez toutes les vignettes :

[![Puzzle photo 2](cropping-images/PhotoPuzzle2.png "Puzzle photo 2")](cropping-images/PhotoPuzzle2-Large.png#lightbox)

Vous pouvez désormais les remettre dans le bon ordre. Les vignettes de la même ligne ou colonne que le carré vide peuvent être frappées pour être placées dans le carré vide.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
