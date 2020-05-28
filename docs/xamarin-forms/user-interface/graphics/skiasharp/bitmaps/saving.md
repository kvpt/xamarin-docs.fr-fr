---
title: ''
description: ''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 01f4fcf1953658af44d2a8996913860a3b605abf
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138656"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Enregistrement de bitmaps SkiaSharp dans des fichiers

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Après la création ou la modification d’une image bitmap par une application SkiaSharp, l’application peut souhaiter enregistrer l’image bitmap dans la bibliothèque de photos de l’utilisateur :

![Enregistrement de bitmaps](saving-images/SavingSample.png "Enregistrement de bitmaps")

Cette tâche englobe deux étapes :

- Conversion de l’image bitmap SkiaSharp en données dans un format de fichier particulier, tel que JPEG ou PNG.
- Enregistrement du résultat dans la bibliothèque de photos à l’aide de code spécifique à la plateforme.

## <a name="file-formats-and-codecs"></a>Formats de fichiers et codecs

La plupart des formats de fichier bitmap populaires actuels utilisent la compression pour réduire l’espace de stockage. Les deux grandes catégories de techniques de compression sont appelées « _perte_ » et « _sans perte_». Ces termes indiquent si l’algorithme de compression entraîne ou non la perte de données.

Le format de perte le plus populaire a été développé par le groupe Joint Photographic Experts et est appelé JPEG. L’algorithme de compression JPEG analyse l’image à l’aide d’un outil mathématique appelé _transformation cosinus discret_et tente de supprimer des données qui ne sont pas essentielles pour préserver la fidélité visuelle de l’image. Le degré de compression peut être contrôlé avec un paramètre généralement appelé _qualité_. Les paramètres de qualité supérieure génèrent des fichiers plus volumineux.

En revanche, un algorithme de compression sans perte analyse l’image pour la répétition et les modèles de pixels qui peuvent être encodés d’une manière qui réduit les données, mais n’entraîne pas la perte de toutes les informations. Les données bitmap d’origine peuvent être entièrement restaurées à partir du fichier compressé. Le format de fichier compressé sans perte principal utilisé aujourd’hui est le format PNG (Portable Network Graphics).

En général, le format JPEG est utilisé pour les photographies, tandis que PNG est utilisé pour les images qui ont été générées manuellement ou par algorithme. Tout algorithme de compression sans perte qui réduit la taille de certains fichiers doit nécessairement augmenter la taille des autres. Heureusement, cette augmentation de la taille se produit généralement uniquement pour les données qui contiennent de nombreuses informations aléatoires (ou apparemment aléatoires).

Les algorithmes de compression sont suffisamment complexes pour justifier deux termes qui décrivent les processus de compression et de décompression :

- _décoder_ &mdash; lire un format de fichier bitmap et le décompresser
- _coder_ &mdash; compresser la bitmap et écrire dans un format de fichier bitmap

La [`SKBitmap`](xref:SkiaSharp.SKBitmap) classe contient plusieurs méthodes nommées `Decode` qui créent un `SKBitmap` à partir d’une source compressée. Vous devez fournir un nom de fichier, un flux ou un tableau d’octets. Le décodeur peut déterminer le format de fichier et le remettre à la fonction de décodage interne appropriée.

En outre, la [`SKCodec`](xref:SkiaSharp.SKCodec) classe a deux méthodes nommées `Create` qui peuvent créer un `SKCodec` objet à partir d’une source compressée et permettre à une application d’être plus impliquée dans le processus de décodage. (La `SKCodec` classe est illustrée dans l’article [**animation des bitmaps SkiaSharp**](animating.md#gif-animation) dans le cadre du décodage d’un fichier GIF animé.)

Lors de l’encodage d’une image bitmap, des informations supplémentaires sont nécessaires : l’encodeur doit connaître le format de fichier particulier que l’application souhaite utiliser (JPEG ou PNG ou autre chose). Si vous souhaitez un format de perte, le codage doit également connaître le niveau de qualité souhaité.

La `SKBitmap` classe définit une [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) méthode avec la syntaxe suivante :

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Cette méthode est décrite plus en détail dans un instant. Le bitmap encodé est écrit dans un flux accessible en écriture. (Le « W » dans `SKWStream` signifie « accessible en écriture ».) Les deuxième et troisième arguments spécifient le format de fichier et (pour les formats de perte) la qualité souhaitée, comprise entre 0 et 100.

En outre, les [`SKImage`](xref:SkiaSharp.SKImage) [`SKPixmap`](xref:SkiaSharp.SKPixmap) classes et définissent également des `Encode` méthodes qui sont un peu plus polyvalentes, et que vous préférerez peut-être. Vous pouvez facilement créer un `SKImage` objet à partir d’un `SKBitmap` objet à l’aide de la [`SKImage.FromBitmap`](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) méthode statique. Vous pouvez obtenir un `SKPixmap` objet à partir d’un `SKBitmap` objet à l’aide de la [`PeekPixels`](xref:SkiaSharp.SKBitmap.PeekPixels) méthode.

L’une des [`Encode`](xref:SkiaSharp.SKImage.Encode) méthodes définies par n' `SKImage` a aucun paramètre et enregistre automatiquement dans un format png. Cette méthode sans paramètre est très facile à utiliser.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Code spécifique à la plateforme pour l’enregistrement des fichiers bitmap

Lorsque vous encodez un `SKBitmap` objet dans un format de fichier particulier, vous conservez généralement un objet de flux d’un certain type, ou un tableau de données. Certaines `Encode` méthodes (y compris celle sans paramètres définis par `SKImage` ) retournent un [`SKData`](xref:SkiaSharp.SKData) objet, qui peut être converti en un tableau d’octets à l’aide de la [`ToArray`](xref:SkiaSharp.SKData.ToArray) méthode. Ces données doivent ensuite être enregistrées dans un fichier.

L’enregistrement dans un fichier dans le stockage local de l’application est très simple, car vous pouvez utiliser `System.IO` des classes et des méthodes standard pour cette tâche. Cette technique est illustrée dans l’article [**animation des bitmaps SkiaSharp**](animating.md#bitmap-animation) dans le cadre de l’animation d’une série de bitmaps de l’ensemble de Mandelbrot.

Si vous souhaitez que le fichier soit partagé par d’autres applications, il doit être enregistré dans la bibliothèque de photos de l’utilisateur. Cette tâche requiert du code spécifique à la plateforme et de l’utilisation de Xamarin.Forms [`DependencyService`](xref:Xamarin.Forms.DependencyService) .

Le projet **SkiaSharpFormsDemo** dans l’application [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) définit une `IPhotoLibrary` interface utilisée avec la `DependencyService` classe. Cela définit la syntaxe d’une `SavePhotoAsync` méthode :

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Cette interface définit également la `PickPhotoAsync` méthode, qui est utilisée pour ouvrir le sélecteur de fichiers propre à la plateforme pour la bibliothèque de photos de l’appareil.

Pour `SavePhotoAsync` , le premier argument est un tableau d’octets qui contient l’image bitmap déjà encodée dans un format de fichier particulier, tel que JPEG ou png. Il est possible qu’une application puisse souhaiter isoler toutes les bitmaps qu’elle crée dans un dossier particulier, qui est spécifié dans le paramètre suivant, suivi du nom de fichier. La méthode retourne une valeur booléenne indiquant la réussite ou non.

Les sections suivantes décrivent comment `SavePhotoAsync` est implémenté sur chaque plateforme.

### <a name="the-ios-implementation"></a>Implémentation iOS

L’implémentation iOS de `SavePhotoAsync` utilise la [`SaveToPhotosAlbum`](xref:UIKit.UIImage.SaveToPhotosAlbum*) méthode de `UIImage` :

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        NSData nsData = NSData.FromArray(data);
        UIImage image = new UIImage(nsData);
        TaskCompletionSource<bool> taskCompletionSource = new TaskCompletionSource<bool>();

        image.SaveToPhotosAlbum((UIImage img, NSError error) =>
        {
            taskCompletionSource.SetResult(error == null);
        });

        return taskCompletionSource.Task;
    }
}
```

Malheureusement, il n’existe aucun moyen de spécifier un nom de fichier ou un dossier pour l’image.

Le fichier **info. plist** du projet iOS requiert une clé indiquant qu’il ajoute des images à la bibliothèque de photos :

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Fais gaffe! La clé d’autorisation permettant d’accéder simplement à la Photothèque est très similaire, mais pas la même :

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>Implémentation Android

L’implémentation Android de `SavePhotoAsync` vérifie d’abord si l' `folder` argument est `null` ou une chaîne vide. Si c’est le cas, l’image bitmap est enregistrée dans le répertoire racine de la bibliothèque de photos. Dans le cas contraire, le dossier est obtenu et, s’il n’existe pas, il est créé :

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        try
        {
            File picturesDirectory = Environment.GetExternalStoragePublicDirectory(Environment.DirectoryPictures);
            File folderDirectory = picturesDirectory;

            if (!string.IsNullOrEmpty(folder))
            {
                folderDirectory = new File(picturesDirectory, folder);
                folderDirectory.Mkdirs();
            }

            using (File bitmapFile = new File(folderDirectory, filename))
            {
                bitmapFile.CreateNewFile();

                using (FileOutputStream outputStream = new FileOutputStream(bitmapFile))
                {
                    await outputStream.WriteAsync(data);
                }

                // Make sure it shows up in the Photos gallery promptly.
                MediaScannerConnection.ScanFile(MainActivity.Instance,
                                                new string[] { bitmapFile.Path },
                                                new string[] { "image/png", "image/jpeg" }, null);
            }
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

L’appel à `MediaScannerConnection.ScanFile` n’est pas strictement obligatoire, mais si vous testez votre programme en vérifiant immédiatement la bibliothèque de photos, cela vous aide à mettre à jour la vue bibliothèque de la bibliothèque.

Le fichier **fichier AndroidManifest. xml** requiert la balise d’autorisation suivante :

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>Implémentation UWP

L’implémentation UWP de `SavePhotoAsync` est très similaire dans la structure de l’implémentation Android :

```csharp
public class PhotoLibrary : IPhotoLibrary
{
    ···
    public async Task<bool> SavePhotoAsync(byte[] data, string folder, string filename)
    {
        StorageFolder picturesDirectory = KnownFolders.PicturesLibrary;
        StorageFolder folderDirectory = picturesDirectory;

        // Get the folder or create it if necessary
        if (!string.IsNullOrEmpty(folder))
        {
            try
            {
                folderDirectory = await picturesDirectory.GetFolderAsync(folder);
            }
            catch
            { }

            if (folderDirectory == null)
            {
                try
                {
                    folderDirectory = await picturesDirectory.CreateFolderAsync(folder);
                }
                catch
                {
                    return false;
                }
            }
        }

        try
        {
            // Create the file.
            StorageFile storageFile = await folderDirectory.CreateFileAsync(filename,
                                                CreationCollisionOption.GenerateUniqueName);

            // Convert byte[] to Windows buffer and write it out.
            IBuffer buffer = WindowsRuntimeBuffer.Create(data, 0, data.Length, data.Length);
            await FileIO.WriteBufferAsync(storageFile, buffer);
        }
        catch
        {
            return false;
        }

        return true;
    }
}
```

La section des **fonctionnalités** du fichier **Package. Appxmanifest** requiert la **bibliothèque d’images**.

## <a name="exploring-the-image-formats"></a>Exploration des formats d’image

Voici [`Encode`](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) à nouveau la méthode `SKImage` :

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat)est une énumération avec des membres qui font référence à onze formats de fichier bitmap, dont certains sont plutôt obscurs :

- `Astc`&mdash;Compression de texture adaptative adaptative
- `Bmp`&mdash;Bitmap Windows
- `Dng`&mdash;Adobe Digital négatif
- `Gif`&mdash;Format d’échange Graphics
- `Ico`&mdash;Images icône Windows
- `Jpeg`&mdash;Groupe d’experts Joint Photographic
- `Ktx`&mdash;Format de texture Khronos pour OpenGL
- `Pkm`&mdash;Pokémon enregistrer le fichier
- `Png`&mdash;Portable Network Graphics
- `Wbmp`&mdash;Format bitmap de protocole d’application sans fil (1 bit par pixel)
- `Webp`&mdash;Format Google WebP

Comme vous le verrez bientôt, seuls trois de ces formats de fichier ( `Jpeg` , `Png` et `Webp` ) sont pris en charge par SkiaSharp.

Pour enregistrer un `SKBitmap` objet nommé `bitmap` dans la bibliothèque de photos de l’utilisateur, vous avez également besoin d’un membre de l' `SKEncodedImageFormat` énumération nommé `imageFormat` et (pour les formats de perte) une variable de type entier `quality` . Vous pouvez utiliser le code suivant pour enregistrer cette image bitmap dans un fichier portant le nom `filename` dans le `folder` dossier :

```csharp
using (MemoryStream memStream = new MemoryStream())
using (SKManagedWStream wstream = new SKManagedWStream(memStream))
{
    bitmap.Encode(wstream, imageFormat, quality);
    byte[] data = memStream.ToArray();

    // Check the data array for content!

    bool success = await DependencyService.Get<IPhotoLibrary>().SavePhotoAsync(data, folder, filename);

    // Check return value for success!
}
```

La `SKManagedWStream` classe dérive de `SKWStream` (qui signifie « flux accessible en écriture »). La `Encode` méthode écrit le fichier bitmap encodé dans ce flux. Les commentaires figurant dans ce code font référence à des vérifications d’erreurs que vous devrez peut-être effectuer.

La page **enregistrer les formats de fichier** de l’application [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) utilise du code similaire pour vous permettre de faire des essais avec l’enregistrement d’une image bitmap dans les différents formats.

Le fichier XAML contient un `SKCanvasView` qui affiche une image bitmap, tandis que le reste de la page contient tout ce dont l’application a besoin pour appeler la `Encode` méthode de `SKBitmap` . Il a un `Picker` pour un membre de l' `SKEncodedImageFormat` énumération, un `Slider` pour l’argument de qualité pour les formats de bitmap avec perte, deux `Entry` vues pour un nom de fichier et de dossier et un `Button` pour l’enregistrement du fichier.

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp;assembly=SkiaSharp"
             xmlns:skiaforms="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             x:Class="SkiaSharpFormsDemos.Bitmaps.SaveFileFormatsPage"
             Title="Save Bitmap Formats">

    <StackLayout Margin="10">
        <skiaforms:SKCanvasView PaintSurface="OnCanvasViewPaintSurface"
                                VerticalOptions="FillAndExpand" />

        <Picker x:Name="formatPicker"
                Title="image format"
                SelectedIndexChanged="OnFormatPickerChanged">
            <Picker.ItemsSource>
                <x:Array Type="{x:Type skia:SKEncodedImageFormat}">
                    <x:Static Member="skia:SKEncodedImageFormat.Astc" />
                    <x:Static Member="skia:SKEncodedImageFormat.Bmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Dng" />
                    <x:Static Member="skia:SKEncodedImageFormat.Gif" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ico" />
                    <x:Static Member="skia:SKEncodedImageFormat.Jpeg" />
                    <x:Static Member="skia:SKEncodedImageFormat.Ktx" />
                    <x:Static Member="skia:SKEncodedImageFormat.Pkm" />
                    <x:Static Member="skia:SKEncodedImageFormat.Png" />
                    <x:Static Member="skia:SKEncodedImageFormat.Wbmp" />
                    <x:Static Member="skia:SKEncodedImageFormat.Webp" />
                </x:Array>
            </Picker.ItemsSource>
        </Picker>

        <Slider x:Name="qualitySlider"
                Maximum="100"
                Value="50" />

        <Label Text="{Binding Source={x:Reference qualitySlider},
                              Path=Value,
                              StringFormat='Quality = {0:F0}'}"
               HorizontalTextAlignment="Center" />

        <StackLayout Orientation="Horizontal">
            <Label Text="Folder Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="folderNameEntry"
                   Text="SaveFileFormats"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <StackLayout Orientation="Horizontal">
            <Label Text="File Name: "
                   VerticalOptions="Center" />

            <Entry x:Name="fileNameEntry"
                   Text="Sample.xxx"
                   HorizontalOptions="FillAndExpand" />
        </StackLayout>

        <Button Text="Save"
                Clicked="OnButtonClicked">
            <Button.Triggers>
                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference formatPicker},
                                               Path=SelectedIndex}"
                             Value="-1">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>

                <DataTrigger TargetType="Button"
                             Binding="{Binding Source={x:Reference fileNameEntry},
                                               Path=Text.Length}"
                             Value="0">
                    <Setter Property="IsEnabled" Value="False" />
                </DataTrigger>
            </Button.Triggers>
        </Button>

        <Label x:Name="statusLabel"
               Text="OK"
               Margin="10, 0" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind charge une ressource bitmap et utilise `SKCanvasView` pour l’afficher. Cette image bitmap ne change jamais. Le `SelectedIndexChanged` Gestionnaire pour le `Picker` modifie le nom de fichier avec une extension qui est identique au membre de l’énumération :

```csharp
public partial class SaveFileFormatsPage : ContentPage
{
    SKBitmap bitmap = BitmapExtensions.LoadBitmapResource(typeof(SaveFileFormatsPage),
        "SkiaSharpFormsDemos.Media.MonkeyFace.png");

    public SaveFileFormatsPage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        args.Surface.Canvas.DrawBitmap(bitmap, args.Info.Rect, BitmapStretch.Uniform);
    }

    void OnFormatPickerChanged(object sender, EventArgs args)
    {
        if (formatPicker.SelectedIndex != -1)
        {
            SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
            fileNameEntry.Text = Path.ChangeExtension(fileNameEntry.Text, imageFormat.ToString());
            statusLabel.Text = "OK";
        }
    }

    async void OnButtonClicked(object sender, EventArgs args)
    {
        SKEncodedImageFormat imageFormat = (SKEncodedImageFormat)formatPicker.SelectedItem;
        int quality = (int)qualitySlider.Value;

        using (MemoryStream memStream = new MemoryStream())
        using (SKManagedWStream wstream = new SKManagedWStream(memStream))
        {
            bitmap.Encode(wstream, imageFormat, quality);
            byte[] data = memStream.ToArray();

            if (data == null)
            {
                statusLabel.Text = "Encode returned null";
            }
            else if (data.Length == 0)
            {
                statusLabel.Text = "Encode returned empty array";
            }
            else
            {
                bool success = await DependencyService.Get<IPhotoLibrary>().
                    SavePhotoAsync(data, folderNameEntry.Text, fileNameEntry.Text);

                if (!success)
                {
                    statusLabel.Text = "SavePhotoAsync return false";
                }
                else
                {
                    statusLabel.Text = "Success!";
                }
            }
        }
    }
}
```

Le `Clicked` Gestionnaire du `Button` effectue tout le travail réel. Il obtient deux arguments pour `Encode` à partir de `Picker` et `Slider` , puis utilise le code présenté précédemment pour créer un `SKManagedWStream` pour la `Encode` méthode. Les deux `Entry` vues fournissent des noms de dossier et de fichier pour la `SavePhotoAsync` méthode.

La majeure partie de cette méthode est consacrée à la gestion des problèmes ou des erreurs. Si `Encode` crée un tableau vide, cela signifie que le format de fichier particulier n’est pas pris en charge. Si `SavePhotoAsync` retourne `false` , cela signifie que le fichier n’a pas été enregistré avec succès.

Voici le programme en cours d’exécution :

[![Enregistrer les formats de fichier](saving-images/SaveFileFormats.png "Enregistrer les formats de fichier")](saving-images/SaveFileFormats-Large.png#lightbox)

Cette capture d’écran montre les trois formats pris en charge sur ces plateformes :

- JPEG
- PNG
- WebP

Pour tous les autres formats, la `Encode` méthode n’écrit rien dans le flux et le tableau d’octets résultant est vide.

La bitmap enregistrée par la page de **formats de fichier d’enregistrement** est de 600 pixels carrés. Avec 4 octets par pixel, il s’agit d’un total de 1 440 000 octets en mémoire. Le tableau suivant indique la taille des fichiers pour différentes combinaisons de format de fichier et de qualité :

|Mettre en forme|Qualité|Size|
|---
titre : Description : ms. Prod : ms. Technology : ms. AssetID : Auteur : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---|---titre : Description : ms. Prod : ms. Technology : ms. AssetID : Auteur : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---:|---:| | PNG | NON APPLICABLE | 492K | | JPEG | 0 | 2.95 k | |      | 50 | 22.1 k | |      | 100 | 206K | | WebP | 0 | 2.71 k | |      | 50 | 11.9 k | |      | 100 | 101K |

Vous pouvez expérimenter différents paramètres de qualité et examiner les résultats.

## <a name="saving-finger-paint-art"></a>Enregistrement d’une image de dessin de doigt

Une image bitmap est couramment utilisée dans les programmes de dessin, où elle fonctionne comme une _image bitmap fictive_. Tout le dessin est conservé sur la bitmap, qui est ensuite affichée par le programme. La bitmap est également utile pour l’enregistrement du dessin.

L’article [**peinture par doigt dans SkiaSharp**](../paths/finger-paint.md) a montré comment utiliser le suivi tactile pour implémenter un programme de dessin de doigt primitif. Le programme ne prenait en charge qu’une seule couleur et une seule largeur de trait, mais il a conservé le dessin entier dans une collection d' `SKPath` objets.

La page **Finger Paint with Save** de l’exemple [**SkiaSharpFormsDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) conserve également l’intégralité du dessin dans une collection d' `SKPath` objets, mais affiche également le dessin sur une image bitmap, qu’il peut enregistrer dans votre bibliothèque de photos.

Une grande partie de ce programme est semblable au programme de **peinture par doigt** d’origine. L’une des améliorations est que le fichier XAML instancie désormais les boutons **Clear** et **Save**:

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:skia="clr-namespace:SkiaSharp.Views.Forms;assembly=SkiaSharp.Views.Forms"
             xmlns:tt="clr-namespace:TouchTracking"
             x:Class="SkiaSharpFormsDemos.Bitmaps.FingerPaintSavePage"
             Title="Finger Paint Save">

    <StackLayout>
        <Grid BackgroundColor="White"
              VerticalOptions="FillAndExpand">
            <skia:SKCanvasView x:Name="canvasView"
                               PaintSurface="OnCanvasViewPaintSurface" />
            <Grid.Effects>
                <tt:TouchEffect Capture="True"
                                TouchAction="OnTouchEffectAction" />
            </Grid.Effects>
        </Grid>

        <Grid>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
        </Grid>

        <Button Text="Clear"
                Grid.Row="0"
                Margin="50, 5"
                Clicked="OnClearButtonClicked" />

        <Button Text="Save"
                Grid.Row="1"
                Margin="50, 5"
                Clicked="OnSaveButtonClicked" />

    </StackLayout>
</ContentPage>
```

Le fichier code-behind gère un champ de type `SKBitmap` nommé `saveBitmap` . Cette image bitmap est créée ou recréée dans le `PaintSurface` Gestionnaire chaque fois que la taille de la surface d’affichage change. Si la bitmap doit être recréée, le contenu de la bitmap existante est copié dans le nouveau bitmap afin que tout soit conservé, quelle que soit la taille de la surface d’affichage :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    SKBitmap saveBitmap;

    public FingerPaintSavePage ()
    {
        InitializeComponent ();
    }

    void OnCanvasViewPaintSurface(object sender, SKPaintSurfaceEventArgs args)
    {
        SKImageInfo info = args.Info;
        SKSurface surface = args.Surface;
        SKCanvas canvas = surface.Canvas;

        // Create bitmap the size of the display surface
        if (saveBitmap == null)
        {
            saveBitmap = new SKBitmap(info.Width, info.Height);
        }
        // Or create new bitmap for a new size of display surface
        else if (saveBitmap.Width < info.Width || saveBitmap.Height < info.Height)
        {
            SKBitmap newBitmap = new SKBitmap(Math.Max(saveBitmap.Width, info.Width),
                                              Math.Max(saveBitmap.Height, info.Height));

            using (SKCanvas newCanvas = new SKCanvas(newBitmap))
            {
                newCanvas.Clear();
                newCanvas.DrawBitmap(saveBitmap, 0, 0);
            }

            saveBitmap = newBitmap;
        }

        // Render the bitmap
        canvas.Clear();
        canvas.DrawBitmap(saveBitmap, 0, 0);
    }
    ···
}
```

Le dessin effectué par le `PaintSurface` Gestionnaire se produit à la fin et se compose uniquement du rendu de l’image bitmap.

Le traitement tactile est semblable au programme précédent. Le programme gère deux collections, `inProgressPaths` et `completedPaths` , qui contiennent tout ce que l’utilisateur a dessiné depuis la dernière suppression de l’affichage. Pour chaque événement tactile, le `OnTouchEffectAction` Gestionnaire appelle `UpdateBitmap` :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    Dictionary<long, SKPath> inProgressPaths = new Dictionary<long, SKPath>();
    List<SKPath> completedPaths = new List<SKPath>();

    SKPaint paint = new SKPaint
    {
        Style = SKPaintStyle.Stroke,
        Color = SKColors.Blue,
        StrokeWidth = 10,
        StrokeCap = SKStrokeCap.Round,
        StrokeJoin = SKStrokeJoin.Round
    };
    ···
    void OnTouchEffectAction(object sender, TouchActionEventArgs args)
    {
        switch (args.Type)
        {
            case TouchActionType.Pressed:
                if (!inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = new SKPath();
                    path.MoveTo(ConvertToPixel(args.Location));
                    inProgressPaths.Add(args.Id, path);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Moved:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    SKPath path = inProgressPaths[args.Id];
                    path.LineTo(ConvertToPixel(args.Location));
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Released:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    completedPaths.Add(inProgressPaths[args.Id]);
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;

            case TouchActionType.Cancelled:
                if (inProgressPaths.ContainsKey(args.Id))
                {
                    inProgressPaths.Remove(args.Id);
                    UpdateBitmap();
                }
                break;
        }
    }

    SKPoint ConvertToPixel(Point pt)
    {
        return new SKPoint((float)(canvasView.CanvasSize.Width * pt.X / canvasView.Width),
                            (float)(canvasView.CanvasSize.Height * pt.Y / canvasView.Height));
    }

    void UpdateBitmap()
    {
        using (SKCanvas saveBitmapCanvas = new SKCanvas(saveBitmap))
        {
            saveBitmapCanvas.Clear();

            foreach (SKPath path in completedPaths)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }

            foreach (SKPath path in inProgressPaths.Values)
            {
                saveBitmapCanvas.DrawPath(path, paint);
            }
        }

        canvasView.InvalidateSurface();
    }
    ···
}
```

La `UpdateBitmap` méthode redessine `saveBitmap` en créant un nouveau `SKCanvas` , en l’effaçant, puis en restituant tous les tracés sur la bitmap. Il conclut en invalidant `canvasView` afin que l’image bitmap puisse être dessinée sur l’affichage.

Voici les gestionnaires pour les deux boutons. Le bouton **Effacer** efface les collections de chemins d’accès, les mises à jour `saveBitmap` (ce qui entraîne l’effacement de la bitmap) et invalide les `SKCanvasView` éléments suivants :

```csharp
public partial class FingerPaintSavePage : ContentPage
{
    ···
    void OnClearButtonClicked(object sender, EventArgs args)
    {
        completedPaths.Clear();
        inProgressPaths.Clear();
        UpdateBitmap();
        canvasView.InvalidateSurface();
    }

    async void OnSaveButtonClicked(object sender, EventArgs args)
    {
        using (SKImage image = SKImage.FromBitmap(saveBitmap))
        {
            SKData data = image.Encode();
            DateTime dt = DateTime.Now;
            string filename = String.Format("FingerPaint-{0:D4}{1:D2}{2:D2}-{3:D2}{4:D2}{5:D2}{6:D3}.png",
                                            dt.Year, dt.Month, dt.Day, dt.Hour, dt.Minute, dt.Second, dt.Millisecond);

            IPhotoLibrary photoLibrary = DependencyService.Get<IPhotoLibrary>();
            bool result = await photoLibrary.SavePhotoAsync(data.ToArray(), "FingerPaint", filename);

            if (!result)
            {
                await DisplayAlert("FingerPaint", "Artwork could not be saved. Sorry!", "OK");
            }
        }
    }
}
```

Le gestionnaire de bouton d' **enregistrement** utilise la méthode simplifiée [`Encode`](xref:SkiaSharp.SKImage.Encode) de `SKImage` . Cette méthode Encode à l’aide du format PNG. L' `SKImage` objet est créé en fonction de `saveBitmap` et l' `SKData` objet contient le fichier PNG encodé.

La `ToArray` méthode de `SKData` obtient un tableau d’octets. C’est ce qui est passé à la `SavePhotoAsync` méthode, ainsi qu’un nom de dossier fixe et un nom de fichier unique construit à partir de la date et de l’heure actuelles.

Voici le programme en action :

[![Enregistrer le doigt Paint](saving-images/FingerPaintSave.png "Enregistrer le doigt Paint")](saving-images/FingerPaintSave-Large.png#lightbox)

Une technique très similaire est utilisée dans l’exemple [**SpinPaint**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) . Il s’agit également d’un programme de peinture par doigt, à l’exception du fait que l’utilisateur peint sur un disque en rotation qui reproduit ensuite les conceptions sur les quatre autres quadrants. La couleur de la peinture du doigt change au fur et à mesure de la rotation du disque :

[![Peinture de rotation](saving-images/SpinPaint.png "Peinture de rotation")](saving-images/SpinPaint-Large.png#lightbox)

Le bouton d' **enregistrement** de la `SpinPaint` classe est semblable à **Finger Paint** en ce qu’il enregistre l’image dans un nom de dossier fixe (**SpainPaint**) et un nom de fichier construit à partir de la date et de l’heure.

## <a name="related-links"></a>Liens connexes

- [API SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
