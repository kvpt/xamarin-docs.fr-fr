---
title: Enregistrement de bitmaps de SkiaSharp dans des fichiers
description: Explorez les différents formats de fichier pris en charge par SkiaSharp pour enregistrer des bitmaps dans la bibliothèque de photos de l’utilisateur.
ms.prod: xamarin
ms.technology: xamarin-skiasharp
ms.assetid: 2D696CB6-B31B-42BC-8D3B-11D63B1E7D9C
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: 4500714ae28a6cd26fe7f0dc8657f26131bef53d
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70197799"
---
# <a name="saving-skiasharp-bitmaps-to-files"></a>Enregistrement de bitmaps de SkiaSharp dans des fichiers

[![Télécharger l’exemple](~/media/shared/download.png) télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)

Une fois une application SkiaSharp a créé ou modifié une image bitmap, l’application peut souhaiter d’enregistrer la bitmap dans la bibliothèque de photos de l’utilisateur :

![Enregistrement de Bitmaps](saving-images/SavingSample.png "enregistrement de Bitmaps")

Cette tâche comprend deux étapes :

- Conversion de la bitmap de SkiaSharp aux données dans un format de fichier particulier, tel que JPEG ou PNG.
- Enregistrer le résultat à la bibliothèque de photos à l’aide de code spécifique à la plateforme.

## <a name="file-formats-and-codecs"></a>Codecs et formats de fichier

La plupart des fichiers de bitmap populaires d’aujourd'hui met en forme utilisent la compression pour réduire l’espace de stockage. Les deux grandes catégories de techniques de compression sont appelés _avec perte_ et _sans perte_. Ce termes du contrat indique si l’algorithme de compression entraîne la perte de données.

Le format le plus populaire avec perte de données a été développé par le Joint Photographic Experts Group et est appelé JPEG. L’algorithme de compression JPEG analyse l’image à l’aide d’un outil mathématique appelé le _transformation de cosinus discrète_et tente de supprimer les données qui ne sont pas essentielles de préserver la fidélité visuelle de l’image. Le degré de compression peut être contrôlé avec un paramètre, généralement appelé _qualité_. Les paramètres de qualité supérieure entraînent des fichiers plus volumineux.

En revanche, un algorithme de compression sans perte analyse l’image pour la répétition et de modèles de pixels qui peuvent être codés d’une manière qui réduit les données, mais n’entraîne pas la perte de toutes les informations. Les données de bitmap d’origine peuvent être restaurées entièrement à partir du fichier compressé. Le format de fichier compressé sans perte principal en cours d’utilisation est aujourd'hui graphique PNG (Portable Network).

En règle générale, JPEG est utilisé pour les photographies, tandis que PNG est utilisé pour les images qui ont été manuellement ou par algorithme générées. N’importe quel algorithme de compression sans perte qui réduit la taille de certains fichiers doit augmenter nécessairement de la taille des autres. Heureusement, cette augmentation de taille seulement se produit généralement pour les données qui contient un grand nombre d’informations aléatoires (ou apparemment aléatoires).

La compression des algorithmes sont suffisamment complexes pour comporter deux termes qui décrivent les processus de compression et décompression :

- _décoder_ &mdash; lire un format de fichier bitmap puis les décompresse
- _Encoder_ &mdash; compresser le bitmap et écrire dans un format de fichier bitmap

Le [ `SKBitmap` ](xref:SkiaSharp.SKBitmap) classe contient plusieurs méthodes nommées `Decode` qui créent un `SKBitmap` à partir d’une source compressée. Tout ce qui a nécessaire consiste à fournir un nom de fichier, un flux ou un tableau d’octets. Le décodeur peut déterminer le format de fichier et transmettre à la fonction de décodage interne appropriée.

En outre, le [ `SKCodec` ](xref:SkiaSharp.SKCodec) classe a deux méthodes nommées `Create` qui peut créer un `SKCodec` de l’objet d’une source compressée et permettre à une application permettant d’impliquer plus dans le processus de décodage. (Le `SKCodec` classe est illustrée dans l’article [ **animer les Bitmaps de SkiaSharp** ](animating.md#gif-animation) dans le cadre de décodage d’un fichier GIF animé.)

Lors de l’encodage d’une image bitmap, des informations supplémentaires sont requises: L’encodeur doit connaître le format de fichier particulier que l’application souhaite utiliser (JPEG ou PNG ou autre chose). Si un format avec perte de données est souhaité, l’encodage devez également connaître le niveau de qualité souhaité.

Le `SKBitmap` classe définit un [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) méthode avec la syntaxe suivante :

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

Cette méthode est décrite plus en détail sous peu. La bitmap encodée est écrit dans un flux accessible en écriture. (La « W » dans `SKWStream` est l’acronyme « accessible en écriture ».) Les deuxième et troisième arguments spécifient le format de fichier et (pour les formats avec pertes) la qualité de votre choix compris entre 0 et 100.

En outre, le [ `SKImage` ](xref:SkiaSharp.SKImage) et [ `SKPixmap` ](xref:SkiaSharp.SKPixmap) classes définissent également `Encode` méthodes qui sont un peu plus polyvalents et vous préférerez peut-être. Vous pouvez facilement créer un `SKImage` de l’objet à partir d’un `SKBitmap` à l’aide de la méthode statique de l’objet [ `SKImage.FromBitmap` ](xref:SkiaSharp.SKImage.FromBitmap(SkiaSharp.SKBitmap)) (méthode). Vous pouvez obtenir un `SKPixmap` de l’objet à partir d’un `SKBitmap` à l’aide de l’objet le [ `PeekPixels` ](xref:SkiaSharp.SKBitmap.PeekPixels) (méthode).

Parmi les [ `Encode` ](xref:SkiaSharp.SKImage.Encode) méthodes définies par `SKImage` n’a aucun paramètre et enregistre automatiquement dans un format PNG. Cette méthode sans paramètre est très facile à utiliser.

## <a name="platform-specific-code-for-saving-bitmap-files"></a>Code spécifique à la plateforme pour l’enregistrement des fichiers bitmap

Lorsque vous encodez un `SKBitmap` mettre en forme l’objet dans un fichier particulier, en règle générale, vous obtiendrez avec un objet de flux quelconque, ou un tableau de données. Certaines de la `Encode` méthodes (y compris celui dont aucun paramètre défini par `SKImage`) retournent un [ `SKData` ](xref:SkiaSharp.SKData) objet, ce qui peut être converti en un tableau d’octets à l’aide de la [ `ToArray` ](xref:SkiaSharp.SKData.ToArray) (méthode). Ces données doivent ensuite être enregistrées dans un fichier.

Enregistrer un fichier dans le stockage local d’application est très simple, car vous pouvez utiliser standard `System.IO` classes et méthodes pour cette tâche. Cette technique est illustrée dans l’article [ **animer les Bitmaps de SkiaSharp** ](animating.md#bitmap-animation) dans le cadre de l’animation d’une série de bitmaps de l’ensemble de Mandelbrot.

Si vous souhaitez que le fichier devant être partagé par d’autres applications, il doit être enregistré dans la bibliothèque de photos de l’utilisateur. Cette tâche requiert le code spécifique à la plateforme et l’utilisation de la Xamarin.Forms [ `DependencyService` ](xref:Xamarin.Forms.DependencyService).

Le **SkiaSharpFormsDemo** de projet dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) application définit un `IPhotoLibrary` interface utilisée avec la `DependencyService` classe. Cela définit la syntaxe d’un `SavePhotoAsync` méthode :

```csharp
public interface IPhotoLibrary
{
    Task<Stream> PickPhotoAsync();

    Task<bool> SavePhotoAsync(byte[] data, string folder, string filename);
}
```

Cette interface définit également le `PickPhotoAsync` (méthode), qui est utilisé pour ouvrir le sélecteur de fichiers spécifiques à la plateforme pour la bibliothèque de photos de l’appareil.

Pour `SavePhotoAsync`, le premier argument est un tableau d’octets qui contient l’image bitmap déjà codé dans un format de fichier particulier, tel que JPEG ou PNG. Il est possible qu’une application est utile isoler les bitmaps qu'il crée dans un dossier particulier, qui est spécifié dans le paramètre suivant, suivi du nom de fichier. La méthode retourne une valeur booléenne indiquant le succès ou non.

Les sections suivantes décrivent comment `SavePhotoAsync` est implémentée sur chaque plateforme.

### <a name="the-ios-implementation"></a>L’implémentation d’iOS

L’implémentation d’iOS de `SavePhotoAsync` utilise le [ `SaveToPhotosAlbum` ](xref:UIKit.UIImage.SaveToPhotosAlbum*) méthode de `UIImage`:

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

Le **Info.plist** fichier dans le projet iOS nécessite une clé indiquant qu’il ajoute des images à la bibliothèque de photos :

```xml
<key>NSPhotoLibraryAddUsageDescription</key>
<string>SkiaSharp Forms Demos adds images to your photo library</string>
```

Fais gaffe ! La clé d’autorisation pour simplement accéder à la bibliothèque de photos est très similaire mais pas dans le même :

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>SkiaSharp Forms Demos accesses your photo library</string>
```

### <a name="the-android-implementation"></a>L’implémentation Android

L’implémentation Android de `SavePhotoAsync` vérifie d’abord si le `folder` argument est `null` ou une chaîne vide. Dans ce cas, la bitmap est enregistrée dans le répertoire racine de la bibliothèque de photos. Sinon, le dossier est obtenu et s’il n’existe pas, il est créé :

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

L’appel à `MediaScannerConnection.ScanFile` n’est pas strictement obligatoire, mais si vous testez votre programme en vérifiant immédiatement la bibliothèque de photos, il vous aide à beaucoup en mettant à jour la vue Galerie de bibliothèque.

Le **AndroidManifest.xml** fichier requiert la balise d’autorisation suivante :

```xml
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
```

### <a name="the-uwp-implementation"></a>L’implémentation UWP

L’implémentation UWP de `SavePhotoAsync` est très similaire dans la structure à l’implémentation Android :

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

Le **fonctionnalités** section de la **Package.appxmanifest** fichier nécessite **bibliothèque d’images**.

## <a name="exploring-the-image-formats"></a>Explorer les formats d’image

Voici le [ `Encode` ](xref:SkiaSharp.SKBitmap.Encode(SkiaSharp.SKWStream,SkiaSharp.SKEncodedImageFormat,System.Int32)) méthode de `SKImage` à nouveau :

```csharp
public Boolean Encode (SKWStream dst, SKEncodedImageFormat format, Int32 quality)
```

[`SKEncodedImageFormat`](xref:SkiaSharp.SKEncodedImageFormat) est une énumération avec les membres qui font référence aux formats de fichier bitmap onze, certains d'entre eux sont assez obscurs :

- `Astc` &mdash; Compression de Texture évolutive ADAPTATIF
- `Bmp` &mdash; Image Bitmap Windows
- `Dng` &mdash; Adobe Digital Negative
- `Gif` &mdash; Format GIF
- `Ico` &mdash; Images d’icônes Windows
- `Jpeg` &mdash; Joint Photographic Experts Group
- `Ktx` &mdash; Format de texture Khronos pour OpenGL
- `Pkm` &mdash; Pokémon enregistrer le fichier
- `Png` &mdash; Portable Network Graphics
- `Wbmp` &mdash; Format de Bitmap sans fil protocole d’Application (1 bit par pixel)
- `Webp` &mdash; Format de Google WebP

Comme vous le verrez bientôt, seuls trois de ces formats de fichier (`Jpeg`, `Png`, et `Webp`) sont réellement pris en charge par SkiaSharp.

Pour enregistrer un `SKBitmap` objet nommé `bitmap` à la bibliothèque de photos de l’utilisateur, vous devez également un membre de la `SKEncodedImageFormat` énumération nommée `imageFormat` et (pour les formats avec pertes) entier `quality` variable. Vous pouvez utiliser le code suivant pour enregistrer cette bitmap dans un fichier portant le nom `filename` dans le `folder` dossier :

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

Le `SKManagedWStream` dérive de la classe `SKWStream` (ce qui signifie « flux accessible en écriture »). Le `Encode` méthode écrit le fichier bitmap encodée dans ce flux de données. Les commentaires dans ce code font référence à certaines vous devrez peut-être effectuer de vérification des erreurs.

Le **enregistrer les Formats de fichier** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) application utilise un code similaire pour vous permettre de faire des essais avec l’enregistrement d’une image bitmap dans les différents formats.

Le fichier XAML contient une `SKCanvasView` qui affiche une image bitmap, tandis que le reste de la page contient tout ce que l’application doit appeler la `Encode` méthode de `SKBitmap`. Il a un `Picker` pour un membre de la `SKEncodedImageFormat` énumération, un `Slider` pour l’argument de qualité pour les formats d’image bitmap avec perte de données, deux `Entry` vues pour un nom de nom de fichier et dossier et un `Button` pour l’enregistrement du fichier.

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

Le fichier code-behind charge une ressource bitmap et utilise le `SKCanvasView` pour l’afficher. Qui bitmap jamais de modifications. Le `SelectedIndexChanged` gestionnaire pour la `Picker` modifie le nom de fichier avec une extension qui est le même que le membre d’énumération :

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

Le `Clicked` gestionnaire pour la `Button` fonctionne-t-il tous les réelles. Il obtient deux arguments pour `Encode` à partir de la `Picker` et `Slider`et utilise ensuite le code présenté précédemment pour créer un `SKManagedWStream` pour le `Encode` (méthode). Les deux `Entry` vues fournissent des noms de fichiers et de dossier pour le `SavePhotoAsync` (méthode).

La plupart de cette méthode est consacrée au traitement des problèmes ou des erreurs. Si `Encode` crée un tableau vide, cela signifie que le format de fichier particulier n’est pas pris en charge. Si `SavePhotoAsync` retourne `false`, le fichier n’a pas été enregistré avec succès.

Voici le programme en cours d’exécution :

[![Enregistrer des Formats de fichier](saving-images/SaveFileFormats.png "enregistrer des Formats de fichier")](saving-images/SaveFileFormats-Large.png#lightbox)

Cette capture d’écran montre uniquement trois formats pris en charge par ces plateformes :

- JPEG
- PNG
- Et WebP

Pour tous les autres formats, le `Encode` méthode n’écrit rien dans le flux et le tableau d’octets résultant est vide.

L’image bitmap qui le **enregistrer les Formats de fichier** enregistre de page est le carré de 600 pixels. Avec 4 octets par pixel, qui est un total d’octets 1,440,000 en mémoire. Le tableau suivant présente la taille du fichier pour différentes combinaisons de format de fichier et de qualité :

|Format|Qualité|Size|
|------|------:|---:|
| PNG | N/A | 492K |
| JPEG | 0 | 2,95 K |
|      | 50 | 22.1 K |
|      | 100 | 206 KO |
| Et WebP | 0 | 2.71K |
|      | 50 | 11,9 K |
|      | 100 | 101 KO |

Vous pouvez faire des essais avec différents paramètres de qualité et examiner les résultats.

## <a name="saving-finger-paint-art"></a>L’enregistrement finger-paint art

Une utilisation courante d’une image bitmap est dans programmes, de dessin où il fonctionne en tant que quelque chose appelé un _bitmap de clichés instantanés_. Tout le dessin est conservé sur l’image bitmap, qui est ensuite affichée par le programme. La bitmap est également utile pour l’enregistrement du dessin.

Le [ **peinture au doigt dans SkiaSharp** ](../paths/finger-paint.md) article vous a montré comment utiliser la fonctionnalité tactile pour implémenter un programme de peinture primitif de suivi. Le programme pris en charge qu’une seule couleur et la largeur de trait qu’une seule, mais elle a conservé la totalité du dessin dans une collection de `SKPath` objets.

Le **peinture au doigt avec enregistrer** page dans le [ **SkiaSharpFormsDemos** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos) exemple conserve également la totalité du dessin dans une collection de `SKPath` des objets, mais également restitue le dessin sur une bitmap, il peut enregistrer dans votre bibliothèque de photos.

Une grande partie de ce programme est similaire à l’original **peinture au doigt** programme. Une de ces améliorations est que le fichier XAML instancie maintenant les boutons **clair** et **enregistrer**:

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

Le fichier code-behind tient à jour un champ de type `SKBitmap` nommé `saveBitmap`. Cette image bitmap est créée ou recréée dans le `PaintSurface` gestionnaire chaque fois que la taille de l’affichage des modifications de surface. Si l’image bitmap doit être recréé, le contenu de l’image bitmap existant est copié dans la nouvelle bitmap afin que tout est conservée, quel que soit la façon dont la surface d’affichage change de taille :

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

Le dessin effectué par le `PaintSurface` gestionnaire se produit à la fin et se compose uniquement de rendu de l’image bitmap.

Le traitement tactile est similaire au programme précédent. Le programme gère deux collections, `inProgressPaths` et `completedPaths`, qui contiennent tout ce dont l’utilisateur a dessiné depuis la dernière fois que l’affichage a été effacé. Pour chaque événement tactile, la `OnTouchEffectAction` appels du Gestionnaire de `UpdateBitmap`:

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

Le `UpdateBitmap` méthode redessine `saveBitmap` en créant un nouveau `SKCanvas`, effacer le contenu, puis d’afficher tous les chemins d’accès sur l’image bitmap. Il se termine en invalidant `canvasView` afin que l’image bitmap peut être dessiné sur l’affichage.

Voici les gestionnaires pour les deux boutons. Le **clair** bouton efface les deux collections de chemin d’accès, les mises à jour `saveBitmap` (ce qui entraîne l’effacement de l’image bitmap) et invalide le `SKCanvasView`:

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

Le **enregistrer** Gestionnaire de bouton utilise simplifié [ `Encode` ](xref:SkiaSharp.SKImage.Encode) méthode à partir de `SKImage`. Cette méthode encode en utilisant le format PNG. Le `SKImage` objet est créé selon `saveBitmap`et le `SKData` objet contient le fichier PNG encodé.

Le `ToArray` méthode `SKData` Obtient un tableau d’octets. C’est ce qui est passé à la `SavePhotoAsync` (méthode), ainsi que d’un nom de dossier fixe et un nom de fichier unique construit à partir de la date et heure actuelles.

Voici le programme en action :

[![Finger Paint enregistrer](saving-images/FingerPaintSave.png "Finger Paint d’enregistrement")](saving-images/FingerPaintSave-Large.png#lightbox)

Une technique très similaire est utilisée dans le [ **SpinPaint** ](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint) exemple. C’est également un programme de peinture, à ceci près que l’utilisateur peint sur un disque de rotation qui reproduit puis les conceptions sur ses quatre quadrants. La couleur des modifications de peinture doigt comme disque de sollicite :

[![Lancez Paint](saving-images/SpinPaint.png "Spin Paint")](saving-images/SpinPaint-Large.png#lightbox)

Le **enregistrer** bouton de `SpinPaint` classe est semblable à **peinture au doigt** car elle enregistre l’image dans un nom de dossier fixe (**SpainPaint**) et un nom de fichier construit à partir de la date et l’heure.

## <a name="related-links"></a>Liens connexes

- [API de SkiaSharp](https://docs.microsoft.com/dotnet/api/skiasharp)
- [SkiaSharpFormsDemos (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-demos)
- [SpinPaint (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/skiasharpforms-spinpaint)
