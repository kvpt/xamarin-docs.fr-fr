---
title: Concepts de base des bitmaps dans SkiaSharp
description: Cet article explique comment charger des bitmaps dans SkiaSharp à partir de différentes sources et les afficher dans des applications Xamarin. Forms et illustre cela avec un exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 4aa73e1cba3f970396e5a52679372a160f47f7af
ms.sourcegitcommit: 3bf02ecac9a8855779e07eb1e7e27abb9fc38934
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/29/2019
ms.locfileid: "74658266"
---
# <a name="bitmap-basics-in-skiasharp"></a>Concepts de base des bitmaps dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Charger des bitmaps à partir de différentes sources et les afficher._

La prise en charge des bitmaps dans SkiaSharp est assez complète. Cet article aborde uniquement les principes de base &mdash; comment charger des bitmaps et comment les afficher :

![](bitmaps-images/basicbitmaps-small.png "The display of two bitmaps")

Une exploration plus approfondie des bitmaps se trouve dans la section [SkiaSharp bitmaps](../bitmaps/index.md).

Une image bitmap SkiaSharp est un objet de type [`SKBitmap`](xref:SkiaSharp.SKBitmap). Il existe de nombreuses façons de créer une bitmap, mais cet article se limite à la méthode [`SKBitmap.Decode`](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) , qui charge l’image bitmap à partir d’un objet `Stream` .net.

La page **bitmaps de base** du programme **SkiaSharpFormsDemos** montre comment charger des bitmaps à partir de trois sources différentes :

- Depuis Internet
- À partir d’une ressource incorporée dans l’exécutable
- À partir de la bibliothèque de photos de l’utilisateur

Trois `SKBitmap` objets pour ces trois sources sont définis en tant que champs dans la classe [`BasicBitmapsPage`](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) :

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

Pour charger une image bitmap basée sur une URL, vous pouvez utiliser la classe [`HttpClient`](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) . Vous devez instancier une seule instance de `HttpClient` et la réutiliser, afin de la stocker en tant que champ :

```csharp
HttpClient httpClient = new HttpClient();
```

Quand vous utilisez des `HttpClient` avec des applications iOS et Android, vous pouvez définir les propriétés du projet, comme décrit dans les documents sur le **[protocole TLS (Transport Layer Security) 1,2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Étant donné qu’il est plus commode d’utiliser l’opérateur `await` avec `HttpClient`, le code ne peut pas être exécuté dans le constructeur `BasicBitmapsPage`. Au lieu de cela, elle fait partie du remplacement `OnAppearing`. Ici, l’URL pointe vers une zone sur le site Web Xamarin avec des exemples de bitmaps. Un package sur le site Web permet d’ajouter une spécification pour redimensionner l’image bitmap à une largeur particulière :

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

Le système d’exploitation Android lève une exception lors de l’utilisation de la `Stream` retournée à partir de `GetStreamAsync` dans la méthode `SKBitmap.Decode`, car il effectue une opération de longue durée sur un thread principal. Pour cette raison, le contenu du fichier bitmap est copié dans un objet `MemoryStream` à l’aide de `CopyToAsync`.

La méthode `SKBitmap.Decode` statique est chargée de décoder les fichiers bitmap. Il fonctionne avec les formats de bitmap JPEG, PNG et GIF, et stocke les résultats dans un format SkiaSharp interne. À ce stade, la `SKCanvasView` doit être invalidée pour permettre au gestionnaire de `PaintSurface` de mettre à jour l’affichage.

## <a name="loading-a-bitmap-resource"></a>Chargement d’une ressource bitmap

En termes de code, l’approche la plus simple pour charger des bitmaps consiste à inclure une ressource bitmap directement dans votre application. Le programme **SkiaSharpFormsDemos** inclut un dossier nommé **Media** contenant plusieurs fichiers bitmap, dont l’un nommé **singe. png**. Pour les bitmaps stockées en tant que ressources de programme, vous devez utiliser la boîte de dialogue **Propriétés** pour attribuer au fichier une **action de génération** de **ressource incorporée**.

Chaque ressource incorporée possède un *ID de ressource* qui se compose du nom du projet, du dossier et du nom de fichier, tous connectés par des points : **SkiaSharpFormsDemos. Media. singe. png**. Vous pouvez accéder à cette ressource en spécifiant cet ID de ressource en tant qu’argument de la méthode [`GetManifestResourceStream`](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) de la classe [`Assembly`](xref:System.Reflection.Assembly) :

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Cet objet `Stream` peut être passé directement à la méthode `SKBitmap.Decode`.

## <a name="loading-a-bitmap-from-the-photo-library"></a>Chargement d’une image bitmap à partir de la bibliothèque de photos

Il est également possible pour l’utilisateur de charger une photo à partir de la bibliothèque d’images de l’appareil. Cette fonctionnalité n’est pas fournie par Xamarin. Forms. Le travail requiert un service de dépendance, tel que celui décrit dans l’article [sélection d’une photo de la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Le fichier **IPhotoLibrary.cs** dans le projet **SkiaSharpFormsDemos** et les trois fichiers **PhotoLibrary.cs** dans les projets de plateforme ont été adaptés à partir de cet article. En outre, le fichier Android **MainActivity.cs** a été modifié comme décrit dans l’article et le projet iOS a reçu l’autorisation d’accéder à la bibliothèque de photos avec deux lignes vers le bas du fichier **info. plist** .

Le constructeur `BasicBitmapsPage` ajoute un `TapGestureRecognizer` à l' `SKCanvasView` pour être informé des clics. À la réception d’un TAP, le gestionnaire de `Tapped` obtient l’accès au service de dépendance de sélecteur d’images et appelle `PickPhotoAsync`. Si un objet `Stream` est retourné, il est passé à la méthode `SKBitmap.Decode` :

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

Notez que le gestionnaire de `Tapped` appelle également la méthode `InvalidateSurface` de l’objet `SKCanvasView`. Cela génère un nouvel appel au gestionnaire de `PaintSurface`.

## <a name="displaying-the-bitmaps"></a>Affichage des bitmaps

Le gestionnaire de `PaintSurface` doit afficher trois bitmaps. Le gestionnaire part du principe que le téléphone est en mode portrait et divise la zone de dessin verticalement en trois parties égales.

La première bitmap est affichée avec la méthode [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) la plus simple. Il vous suffit de spécifier les coordonnées X et Y de l’angle supérieur gauche de l’image bitmap à positionner :

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Bien qu’un paramètre `SKPaint` soit défini, sa valeur par défaut est `null` et vous pouvez l’ignorer. Les pixels de l’image bitmap sont simplement transférés vers les pixels de la surface d’affichage avec un mappage un-à-un. Vous verrez une application pour cet argument `SKPaint` dans la section suivante sur la [**transparence SkiaSharp**](transparency.md).

Un programme peut obtenir les dimensions en pixels d’une image bitmap avec les propriétés [`Width`](xref:SkiaSharp.SKBitmap.Width) et [`Height`](xref:SkiaSharp.SKBitmap.Height) . Ces propriétés permettent au programme de calculer des coordonnées pour positionner l’image bitmap au centre du tiers supérieur du canevas :

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

Les deux autres bitmaps sont affichés avec une version de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) avec un paramètre `SKRect` :

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Une troisième version de [`DrawBitmap`](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) a deux arguments `SKRect` pour spécifier un sous-ensemble rectangulaire de la bitmap à afficher, mais cette version n’est pas utilisée dans cet article.

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

La troisième image &mdash; que vous pouvez voir uniquement si vous exécutez le programme et que vous chargez une photo à partir de votre propre bibliothèque d’images &mdash; s’affiche également dans un rectangle, mais la position et la taille du rectangle sont ajustées pour conserver les proportions de la bitmap. Ce calcul est un peu plus complexe, car il nécessite de calculer un facteur d’échelle en fonction de la taille de l’image bitmap et du rectangle de destination, et de centrer le rectangle dans cette zone :

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

Si aucune bitmap n’a encore été chargée à partir de la bibliothèque d’images, le bloc `else` affiche du texte pour inviter l’utilisateur à appuyer sur l’écran.

Vous pouvez afficher des bitmaps avec différents degrés de transparence, et l’article suivant sur la [**transparence SkiaSharp**](transparency.md) décrit comment.

## <a name="related-links"></a>Liens associés

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
