---
title : « notions de base sur les bitmaps dans SkiaSharp » Description : « cet article explique comment charger des bitmaps dans SkiaSharp à partir de différentes sources et les afficher dans des Xamarin.Forms applications, et illustre cela avec un exemple de code. »
ms. Prod : xamarin ms. Technology : xamarin-skiasharp ms. AssetID : 32C95DFF-9065-42D7-966C-D3DBD16906B3 auteur : davidbritch ms. Author : dabritch ms. Date : 07/17/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="bitmap-basics-in-skiasharp"></a>Concepts de base des bitmaps dans SkiaSharp

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Charger des bitmaps à partir de différentes sources et les afficher._

La prise en charge des bitmaps dans SkiaSharp est assez complète. Cet article traite uniquement des bases &mdash; de la façon de charger des bitmaps et de leur affichage :

![](bitmaps-images/basicbitmaps-small.png "The display of two bitmaps")

Une exploration plus approfondie des bitmaps se trouve dans la section [SkiaSharp bitmaps](../bitmaps/index.md).

Une image bitmap SkiaSharp est un objet de type [`SKBitmap`](xref:SkiaSharp.SKBitmap) . Il existe de nombreuses façons de créer une bitmap, mais cet article se limite à la [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) méthode, qui charge l’image bitmap à partir d’un `Stream` objet .net.

La page **bitmaps de base** du programme **SkiaSharpFormsDemos** montre comment charger des bitmaps à partir de trois sources différentes :

- Depuis Internet
- À partir d’une ressource incorporée dans l’exécutable
- À partir de la bibliothèque de photos de l’utilisateur

Trois `SKBitmap` objets pour ces trois sources sont définis en tant que champs dans la [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe :

```csharp
public class BasicBitmapsPage : ContentPage
{
    SKCanvasView canvasView;
    SKBitmap webBitmap;
    SKBitmap resourceBitmap;
    SKBitmap libraryBitmap;

    public BasicBitmapsPage()
    {
        Title = "Basic Bitmaps";

        canvasView = new SKCanvasView();
        canvasView.PaintSurface += OnCanvasViewPaintSurface;
        Content = canvasView;
        ...
    }
    ...
}
```

## <a name="loading-a-bitmap-from-the-web"></a>Chargement d’une image bitmap à partir du Web

Pour charger une image bitmap basée sur une URL, vous pouvez utiliser la [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) classe. Vous ne devez instancier qu’une seule instance de `HttpClient` et la réutiliser, donc la stocker en tant que champ :

```csharp
HttpClient httpClient = new HttpClient();
```

Lorsque `HttpClient` vous utilisez avec des applications iOS et Android, vous devez définir les propriétés du projet, comme décrit dans les documents sur le **[protocole TLS (Transport Layer Security) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)**.

Étant donné qu’il est plus commode d’utiliser l' `await` opérateur avec `HttpClient` , le code ne peut pas être exécuté dans le `BasicBitmapsPage` constructeur. Au lieu de cela, elle fait partie du `OnAppearing` remplacement. Ici, l’URL pointe vers une zone sur le site Web Xamarin avec des exemples de bitmaps. Un package sur le site Web permet d’ajouter une spécification pour redimensionner l’image bitmap à une largeur particulière :

```csharp
protected override async void OnAppearing()
{
    base.OnAppearing();

    // Load web bitmap.
    string url = "https://developer.xamarin.com/demo/IMG_3256.JPG?width=480";

    try
    {
        using (Stream stream = await httpClient.GetStreamAsync(url))
        using (MemoryStream memStream = new MemoryStream())
        {
            await stream.CopyToAsync(memStream);
            memStream.Seek(0, SeekOrigin.Begin);

            webBitmap = SKBitmap.Decode(memStream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

Le système d’exploitation Android lève une exception lors de l’utilisation du `Stream` retourné à partir de `GetStreamAsync` dans la `SKBitmap.Decode` méthode, car il effectue une opération de longue durée sur un thread principal. Pour cette raison, le contenu du fichier bitmap est copié dans un `MemoryStream` objet à l’aide de `CopyToAsync` .

La `SKBitmap.Decode` méthode statique est chargée de décoder les fichiers bitmap. Il fonctionne avec les formats de bitmap JPEG, PNG et GIF, et stocke les résultats dans un format SkiaSharp interne. À ce stade, `SKCanvasView` doit être invalidé pour permettre au `PaintSurface` Gestionnaire de mettre à jour l’affichage.

## <a name="loading-a-bitmap-resource"></a>Chargement d’une ressource bitmap

En termes de code, l’approche la plus simple pour charger des bitmaps consiste à inclure une ressource bitmap directement dans votre application. Le programme **SkiaSharpFormsDemos** inclut un dossier nommé **Media** contenant plusieurs fichiers bitmap, y compris un nom **monkey.png**. Pour les bitmaps stockées en tant que ressources de programme, vous devez utiliser la boîte de dialogue **Propriétés** pour attribuer au fichier une **action de génération** de **ressource incorporée**.

Chaque ressource incorporée possède un *ID de ressource* qui se compose du nom du projet, du dossier et du nom de fichier, tous connectés par périodes : **SkiaSharpFormsDemos.Media.monkey.png**. Vous pouvez accéder à cette ressource en spécifiant cet ID de ressource en tant qu’argument de la [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) méthode de la [`Assembly`](xref:System.Reflection.Assembly) classe :

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Cet `Stream` objet peut être passé directement à la `SKBitmap.Decode` méthode.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Chargement d’une image bitmap à partir de la bibliothèque de photos

Il est également possible pour l’utilisateur de charger une photo à partir de la bibliothèque d’images de l’appareil. Cette fonctionnalité n’est pas fournie par Xamarin.Forms elle-même. Le travail requiert un service de dépendance, tel que celui décrit dans l’article [sélection d’une photo de la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Le fichier **IPhotoLibrary.cs** dans le projet **SkiaSharpFormsDemos** et les trois fichiers **PhotoLibrary.cs** dans les projets de plateforme ont été adaptés à partir de cet article. En outre, le fichier Android **MainActivity.cs** a été modifié comme décrit dans l’article et le projet iOS a reçu l’autorisation d’accéder à la bibliothèque de photos avec deux lignes vers le bas du fichier **info. plist** .

Le `BasicBitmapsPage` constructeur ajoute un `TapGestureRecognizer` au `SKCanvasView` pour être averti des clics. À la réception d’un TAP, le `Tapped` Gestionnaire obtient l’accès au service de dépendance de sélecteur d’images et appelle `PickPhotoAsync` . Si un `Stream` objet est retourné, il est passé à la `SKBitmap.Decode` méthode :

```csharp
// Add tap gesture recognizer
TapGestureRecognizer tapRecognizer = new TapGestureRecognizer();
tapRecognizer.Tapped += async (sender, args) =>
{
    // Load bitmap from photo library
    IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();

    using (Stream stream = await photoLibrary.PickPhotoAsync())
    {
        if (stream != null)
        {
            libraryBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        }
    }
};
canvasView.GestureRecognizers.Add(tapRecognizer);
```

Notez que le `Tapped` Gestionnaire appelle également la `InvalidateSurface` méthode de l' `SKCanvasView` objet. Cela génère un nouvel appel au `PaintSurface` Gestionnaire.

## <a name="displaying-the-bitmaps"></a>Affichage des bitmaps

Le `PaintSurface` gestionnaire doit afficher trois bitmaps. Le gestionnaire part du principe que le téléphone est en mode portrait et divise la zone de dessin verticalement en trois parties égales.

La première bitmap est affichée avec la méthode la plus simple [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) . Il vous suffit de spécifier les coordonnées X et Y de l’angle supérieur gauche de l’image bitmap à positionner :

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Bien qu’un `SKPaint` paramètre soit défini, il a la valeur par défaut `null` et vous pouvez l’ignorer. Les pixels de l’image bitmap sont simplement transférés vers les pixels de la surface d’affichage avec un mappage un-à-un. Vous verrez une application pour cet `SKPaint` argument dans la section suivante sur la [**transparence SkiaSharp**](transparency.md).

Un programme peut obtenir les dimensions en pixels d’une image bitmap avec les [`Width`](xref:SkiaSharp.SKBitmap.Width) [`Height`](xref:SkiaSharp.SKBitmap.Height) Propriétés et. Ces propriétés permettent au programme de calculer des coordonnées pour positionner l’image bitmap au centre du tiers supérieur du canevas :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    SKImageInfo info = args.Info;
    SKSurface surface = args.Surface;
    SKCanvas canvas = surface.Canvas;

    canvas.Clear();

    if (webBitmap != null)
    {
        float x = (info.Width - webBitmap.Width) / 2;
        float y = (info.Height / 3 - webBitmap.Height) / 2;
        canvas.DrawBitmap(webBitmap, x, y);
    }
    ...
}
```

Les deux autres bitmaps sont affichés avec une version de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) avec un `SKRect` paramètre :

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Une troisième version de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) a deux `SKRect` arguments pour spécifier un sous-ensemble rectangulaire de la bitmap à afficher, mais cette version n’est pas utilisée dans cet article.

Voici le code permettant d’afficher l’image bitmap chargée à partir d’une image bitmap de ressource incorporée :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (resourceBitmap != null)
    {
        canvas.DrawBitmap(resourceBitmap,
            new SKRect(0, info.Height / 3, info.Width, 2 * info.Height / 3));
    }
    ...
}
```

La bitmap est étirée sur les dimensions du rectangle, ce qui explique pourquoi le singe est étiré horizontalement dans ces captures d’écran :

[![](bitmaps-images/basicbitmaps-small.png "A triple screenshot of the Basic Bitmaps page")](bitmaps-images/basicbitmaps-large.png#lightbox "A triple screenshot of the Basic Bitmaps page")

La troisième image que &mdash; vous pouvez voir uniquement si vous exécutez le programme et que vous chargez une photo à partir de votre propre bibliothèque d’images &mdash; s’affiche également dans un rectangle, mais la position et la taille du rectangle sont ajustées pour conserver les proportions de la bitmap. Ce calcul est un peu plus complexe, car il nécessite de calculer un facteur d’échelle en fonction de la taille de l’image bitmap et du rectangle de destination, et de centrer le rectangle dans cette zone :

```csharp
void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
{
    ...
    if (libraryBitmap != null)
    {
        float scale = Math.Min((float)info.Width / libraryBitmap.Width,
                               info.Height / 3f / libraryBitmap.Height);

        float left = (info.Width - scale * libraryBitmap.Width) / 2;
        float top = (info.Height / 3 - scale * libraryBitmap.Height) / 2;
        float right = left + scale * libraryBitmap.Width;
        float bottom = top + scale * libraryBitmap.Height;
        SKRect rect = new SKRect(left, top, right, bottom);
        rect.Offset(0, 2 * info.Height / 3);

        canvas.DrawBitmap(libraryBitmap, rect);
    }
    else
    {
        using (SKPaint paint = new SKPaint())
        {
            paint.Color = SKColors.Blue;
            paint.TextAlign = SKTextAlign.Center;
            paint.TextSize = 48;

            canvas.DrawText("Tap to load bitmap",
                info.Width / 2, 5 * info.Height / 6, paint);
        }
    }
}
```

Si aucune bitmap n’a encore été chargée à partir de la bibliothèque d’images, le `else` bloc affiche du texte pour inviter l’utilisateur à appuyer sur l’écran.

Vous pouvez afficher des bitmaps avec différents degrés de transparence, et l’article suivant sur la [**transparence SkiaSharp**](transparency.md) décrit comment.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
