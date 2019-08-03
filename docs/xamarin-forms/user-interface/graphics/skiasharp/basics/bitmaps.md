---
title: Principes fondamentaux de la bitmap dans SkiaSharp
description: Cet article explique comment charger des bitmaps dans SkiaSharp provenant de différentes sources et les afficher dans les applications Xamarin.Forms et illustre ceci avec l’exemple de code.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 32C95DFF-9065-42D7-966C-D3DBD16906B3
author: davidbritch
ms.author: dabritch
ms.date: 07/17/2018
ms.openlocfilehash: 47fd6323e309353446c707730679a191cb8e923c
ms.sourcegitcommit: c6e56545eafd8ff9e540d56aba32aa6232c5315f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/02/2019
ms.locfileid: "68738895"
---
# <a name="bitmap-basics-in-skiasharp"></a>Principes fondamentaux de la bitmap dans SkiaSharp

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

_Charger des bitmaps à partir de différentes sources et les afficher._

La prise en charge des images bitmap dans SkiaSharp est assez longue. Cet article traite uniquement les principes de base &mdash; comment charger des bitmaps et comment les afficher :

![](bitmaps-images/basicbitmaps-small.png "L’affichage de deux bitmaps")

Vous trouverez une quantité exploration plus approfondie des images bitmap dans la section [SkiaSharp Bitmaps](../bitmaps/index.md).

Une image bitmap SkiaSharp est un objet de type [ `SKBitmap` ](xref:SkiaSharp.SKBitmap). Il existe plusieurs façons de créer une image bitmap mais cet article restreint lui-même à le [ `SKBitmap.Decode` ](xref:SkiaSharp.SKBitmap.Decode(System.IO.Stream)) (méthode), qui charge la bitmap à partir d’un .NET `Stream` objet.

Le **base Bitmaps** page dans le **SkiaSharpFormsDemos** programme montre comment charger des bitmaps à partir de trois sources différentes :

- Depuis Internet
- À partir d’une ressource incorporée dans le fichier exécutable
- À partir de la bibliothèque de photos de l’utilisateur

Trois `SKBitmap` objets pour ces trois sources sont définis en tant que champs dans le [ `BasicBitmapsPage` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/SkiaSharpForms/Demos/Demos/SkiaSharpFormsDemos/Basics/BasicBitmapsPage.cs) classe :

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

## <a name="loading-a-bitmap-from-the-web"></a>Le chargement d’une image Bitmap à partir du Web

Pour charger une image bitmap basée sur une URL, vous pouvez utiliser la [ `HttpClient` ](/dotnet/api/system.net.http.httpclient?view=netstandard-2.0) classe. Vous devez instancier qu’une seule instance de `HttpClient` et réutiliser, par conséquent, le stocker en tant que champ :

```csharp
HttpClient httpClient = new HttpClient();
```

Lorsque vous utilisez `HttpClient` avec les applications iOS et Android, vous voudrez définir les propriétés du projet comme décrit dans les documents sur  **[sécurité TLS (Transport Layer) 1.2](~/cross-platform/app-fundamentals/transport-layer-security.md)** .

Car il est plus pratique d’utiliser le `await` opérateur avec `HttpClient`, le code ne peut pas être exécuté dans le `BasicBitmapsPage` constructeur. Au lieu de cela, il fait partie de la `OnAppearing` remplacer. Ici l’URL pointe vers une zone sur le site web de Xamarin avec des bitmaps d’exemple. Un package sur le site web permet d’ajouter une spécification pour le redimensionnement de la bitmap à une largeur spécifique :


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

            webBitmap = SKBitmap.Decode(stream);
            canvasView.InvalidateSurface();
        };
    }
    catch
    {
    }
}
```

Le système d’exploitation Android lève une exception lorsque vous utilisez le `Stream` retourné à partir de `GetStreamAsync` dans le `SKBitmap.Decode` (méthode), car elle est une opération de longue durée sur un thread principal. Pour cette raison, le contenu du fichier bitmap est copié dans un `MemoryStream` à l’aide de l’objet `CopyToAsync`.

La méthode statique `SKBitmap.Decode` méthode est chargée de décodage des fichiers bitmap. Il fonctionne avec les formats bitmap GIF, JPEG et PNG et stocke les résultats dans un format SkiaSharp interne. À ce stade, le `SKCanvasView` doit être invalidée pour autoriser le `PaintSurface` gestionnaire à mettre à jour l’affichage.

## <a name="loading-a-bitmap-resource"></a>Le chargement d’une ressource Bitmap

En termes de code, l’approche la plus simple pour charger des bitmaps est y compris une ressource bitmap directement dans votre application. Le **SkiaSharpFormsDemos** programme inclut un dossier nommé **Media** contenant plusieurs fichiers, dont un nommé de bitmap **monkey.png**. Pour les bitmaps stockés en tant que ressources de programme, vous devez utiliser le **propriétés** boîte de dialogue pour donner au fichier un **Action de génération** de **ressource incorporée**!

Chaque ressource incorporée possède un *ID de ressource* qui se compose du nom du projet, du dossier et du nom de fichier, tous connectés par périodes: **SkiaSharpFormsDemos.Media.monkey.png**. Vous pouvez accéder à cette ressource en spécifiant cette ressource ID en tant qu’argument à la [ `GetManifestResourceStream` ](xref:System.Reflection.Assembly.GetManifestResourceStream(System.String)) méthode de la [ `Assembly` ](xref:System.Reflection.Assembly) classe :

```csharp
string resourceID = "SkiaSharpFormsDemos.Media.monkey.png";
Assembly assembly = GetType().GetTypeInfo().Assembly;

using (Stream stream = assembly.GetManifestResourceStream(resourceID))
{
    resourceBitmap = SKBitmap.Decode(stream);
}
```

Cela `Stream` objet peut être transmis directement à la `SKBitmap.Decode` (méthode).

## <a name="loading-a-bitmap-from-the-photo-library"></a>Le chargement d’une image Bitmap à partir de la bibliothèque de photos

Il est également possible pour l’utilisateur de charger une photo à partir de la bibliothèque d’images de l’appareil. Cette fonctionnalité n’est pas fournie par Xamarin.Forms lui-même. Le travail nécessite un service de dépendance, tel que celui décrit dans l’article [sélection d’une Photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md).

Le **IPhotoLibrary.cs** de fichiers dans le **SkiaSharpFormsDemos** projet et les trois **PhotoLibrary.cs** fichiers dans les projets de plateforme ont été adaptés à partir de cet article. En outre, l’Android **MainActivity.cs** fichier a été modifié comme décrit dans l’article et le projet iOS a été donné l’autorisation pour accéder à la bibliothèque de photos avec deux lignes vers le bas de la **info.plist**  fichier.

Le `BasicBitmapsPage` constructeur ajoute un `TapGestureRecognizer` à la `SKCanvasView` pour être informé de coefficients. À la réception d’une pression simple, le `Tapped` gestionnaire obtient l’accès au service de dépendance de sélecteur d’images et les appels `PickPhotoAsync`. Si un `Stream` est retourné, puis elle est passée à la `SKBitmap.Decode` méthode :

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

Notez que le `Tapped` gestionnaire appelle également la `InvalidateSurface` méthode de la `SKCanvasView` objet. Cela génère un nouvel appel à la `PaintSurface` gestionnaire.

## <a name="displaying-the-bitmaps"></a>Afficher les Bitmaps

Le `PaintSurface` gestionnaire a besoin pour afficher trois bitmaps. Le gestionnaire suppose que le téléphone est en mode portrait et divise la zone de dessin verticalement en trois parties égales.

La première bitmap s’affiche avec la plus simple [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,System.Single,System.Single,SkiaSharp.SKPaint)) (méthode). Il que vous suffit de spécifier sont les coordonnées X et Y, où le coin supérieur gauche de l’image bitmap doit être placé :

```csharp
public void DrawBitmap (SKBitmap bitmap, Single x, Single y, SKPaint paint = null)
```

Bien qu’un `SKPaint` paramètre est défini, il a la valeur par défaut `null` et vous pouvez l’ignorer. Les pixels de l’image bitmap sont simplement transférés vers les pixels de la surface d’affichage avec un mappage un à un. Vous verrez une application pour ce `SKPaint` argument dans la section suivante sur [ **SkiaSharp transparence**](transparency.md).

Un programme peut obtenir les dimensions en pixels d’une image bitmap avec le [ `Width` ](xref:SkiaSharp.SKBitmap.Width) et [ `Height` ](xref:SkiaSharp.SKBitmap.Height) propriétés. Ces propriétés permettent de calculer des coordonnées pour positionner l’image bitmap dans le centre de la troisième de coin de la zone de dessin :

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

Les deux images sont affichées avec une version de [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKPaint)) avec un `SKRect` paramètre :

```csharp
public void DrawBitmap (SKBitmap bitmap, SKRect dest, SKPaint paint = null)
```

Une troisième version de [ `DrawBitmap` ](xref:SkiaSharp.SKCanvas.DrawBitmap(SkiaSharp.SKBitmap,SkiaSharp.SKRect,SkiaSharp.SKRect,SkiaSharp.SKPaint)) a deux `SKRect` arguments pour spécifier un sous-ensemble rectangulaire de la bitmap à afficher, mais cette version n’est pas utilisé dans cet article.

Voici le code pour afficher la bitmap chargée à partir d’une bitmap de ressource incorporée :

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

La bitmap est étirée pour les dimensions du rectangle, c’est pourquoi le monkey est étiré horizontalement dans ces captures d’écran :

[![](bitmaps-images/basicbitmaps-small.png "Une capture d’écran triple de la page de base Bitmaps")](bitmaps-images/basicbitmaps-large.png#lightbox "une triple capture d’écran de la page de base Bitmaps")

La troisième image &mdash; que vous pouvez voir uniquement si vous exécutez le programme et chargez une photo à partir de votre propre bibliothèque d’images &mdash; s’affiche également dans un rectangle, mais du rectangle position et la taille sont ajustées pour conserver les proportions de la bitmap. Ce calcul est un peu plus complexe, car elle nécessite de calculer une échelle en fonction de la taille de la bitmap et le rectangle de destination et centrage du rectangle dans la zone :

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

Si aucune bitmap n’a encore été chargé à partir de la bibliothèque d’images, puis le `else` bloc affiche du texte pour inviter l’utilisateur d’appuyer sur l’écran.

Vous pouvez afficher des bitmaps avec différents degrés de transparence et de l’article suivant sur [ **SkiaSharp transparence** ](transparency.md) décrit comment.

## <a name="related-links"></a>Liens associés

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [Sélection d’une Photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
