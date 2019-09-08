---
title: Accès à la vidéothèque de l’appareil
description: Cet article explique comment accéder à la vidéothèque de l’appareil dans une application de lecteur vidéo à l’aide de Xamarin.Forms.
ms.prod: xamarin
ms.assetid: 364C1D43-EAAE-45B9-BE24-0DA5AE74C4D9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/12/2018
ms.openlocfilehash: 2f9de881621b6634b95bdca56a0aa9e7b9f2bb98
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771850"
---
# <a name="accessing-the-devices-video-library"></a>Accès à la vidéothèque de l’appareil

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)

La plupart des appareils mobiles et ordinateurs de bureau modernes ont la possibilité d’enregistrer des vidéos à l’aide de l’appareil photo de l’appareil. Les vidéos créées par un utilisateur sont ensuite stockées sous forme de fichiers sur l’appareil. Ces fichiers peuvent être récupérés à partir de la bibliothèque d’images et lus par la classe `VideoPlayer` comme toute autre vidéo.

## <a name="the-photo-picker-dependency-service"></a>Service de dépendance de sélecteur de photos

Chacune des plateformes inclut une fonctionnalité qui permet à l’utilisateur de sélectionner une photo ou une vidéo à partir de la bibliothèque d’images de l’appareil. La première étape de la lecture d’une vidéo à partir de la bibliothèque d’images de l’appareil est la création d’un service de dépendance qui appelle le sélecteur d’images sur chaque plateforme. Le service de dépendance décrit ci-dessous est très similaire à celui défini dans l’article [**Sélection d’une photo dans la bibliothèque d’images**](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md), à ceci près que le sélecteur de vidéos retourne un nom de fichier et non un objet `Stream`.

Le projet de bibliothèque .NET Standard définit une interface nommée `IVideoPicker` pour le service de dépendance :

```csharp
namespace FormsVideoLibrary
{
    public interface IVideoPicker
    {
        Task<string> GetVideoFileAsync();
    }
}
```

Chacune des plateformes contient une classe nommée `VideoPicker` qui implémente cette interface.

### <a name="the-ios-video-picker"></a>Sélecteur de vidéos iOS

Le `VideoPicker` IOS utilise le [`UIImagePickerController`](xref:UIKit.UIImagePickerController) iOS pour accéder à la bibliothèque d’images, en spécifiant que la recherche doit être limitée aux vidéos (appelées « films ») dans la propriété `MediaType` iOS. Notez que `VideoPicker` implémente de manière explicite l’interface `IVideoPicker`. Notez également l’attribut `Dependency` qui identifie cette classe comme un service de dépendance. Il s’agit des deux conditions qui permettent à Xamarin.Forms de rechercher le service de dépendance dans le projet de plateforme :

```csharp
using System;
using System.Threading.Tasks;
using UIKit;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.iOS.VideoPicker))]

namespace FormsVideoLibrary.iOS
{
    public class VideoPicker : IVideoPicker
    {
        TaskCompletionSource<string> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<string> GetVideoFileAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.SavedPhotosAlbum,
                MediaTypes = new string[] { "public.movie" }
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<string>();
            return taskCompletionSource.Task;
        }

        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            if (args.MediaType == "public.movie")
            {
                taskCompletionSource.SetResult(args.MediaUrl.AbsoluteString);
            }
            else
            {
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }
    }
}
```

### <a name="the-android-video-picker"></a>Sélecteur de vidéos Android

L’implémentation Android de `IVideoPicker` nécessite une méthode de rappel qui fait partie de l’activité de l’application. Pour cette raison, la classe `MainActivity` définit deux propriétés, un champ et une méthode de rappel :

```csharp
namespace VideoPlayerDemos.Droid
{
    ···
    public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            Current = this;
            ···
        }

        // Field, properties, and method for Video Picker
        public static MainActivity Current { private set; get; }

        public static readonly int PickImageId = 1000;

        public TaskCompletionSource<string> PickImageTaskCompletionSource { set; get; }

        protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
        {
            base.OnActivityResult(requestCode, resultCode, data);

            if (requestCode == PickImageId)
            {
                if ((resultCode == Result.Ok) && (data != null))
                {
                    // Set the filename as the completion of the Task
                    PickImageTaskCompletionSource.SetResult(data.DataString);
                }
                else
                {
                    PickImageTaskCompletionSource.SetResult(null);
                }
            }
        }
    }
}
```

La méthode `OnCreate` dans `MainActivity` stocke sa propre instance dans la propriété `Current` statique. Cela permet à l’implémentation de `IVideoPicker` d’obtenir l’instance `MainActivity` pour le démarrage du sélecteur **Sélectionner une vidéo** :

```csharp
using System;
using System.Threading.Tasks;
using Android.Content;
using Xamarin.Forms;

// Need application's MainActivity
using VideoPlayerDemos.Droid;

[assembly: Dependency(typeof(FormsVideoLibrary.Droid.VideoPicker))]

namespace FormsVideoLibrary.Droid
{
    public class VideoPicker : IVideoPicker
    {
        public Task<string> GetVideoFileAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("video/*");
            intent.SetAction(Intent.ActionGetContent);

            // Get the MainActivity instance
            MainActivity activity = MainActivity.Current;

            // Start the picture-picker activity (resumes in MainActivity.cs)
            activity.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Video"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            activity.PickImageTaskCompletionSource = new TaskCompletionSource<string>();

            // Return Task object
            return activity.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Les ajouts à l’objet `MainActivity` représentent le seul code dans la solution [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) où le code d’application normal doit être modifié pour prendre en charge les classes `FormsVideoLibrary`.

### <a name="the-uwp-video-picker"></a>Sélecteur de vidéos UWP

L’implémentation UWP de l’interface `IVideoPicker` utilise le [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) UWP. Il commence la recherche de fichiers par la bibliothèque d’images et restreint les types de fichiers à MP4 et WMV (Windows Media Video) :

```csharp
using System;
using System.Threading.Tasks;
using Windows.Storage;
using Windows.Storage.Pickers;
using Xamarin.Forms;

[assembly: Dependency(typeof(FormsVideoLibrary.UWP.VideoPicker))]

namespace FormsVideoLibrary.UWP
{
    public class VideoPicker : IVideoPicker
    {
        public async Task<string> GetVideoFileAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary
            };

            openPicker.FileTypeFilter.Add(".wmv");
            openPicker.FileTypeFilter.Add(".mp4");

            // Get a file and return the path
            StorageFile storageFile = await openPicker.PickSingleFileAsync();
            return storageFile?.Path;
        }
    }
}
```

## <a name="invoking-the-dependency-service"></a>Appel du service de dépendance

La page **Lire une vidéo de bibliothèque** du programme [**VideoPlayerDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos) montre comment utiliser le service de dépendance du sélecteur de vidéos. Le fichier XAML contient une instance `VideoPlayer` et un `Button` étiqueté **Show Video Library** (Afficher la vidéothèque) :

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:video="clr-namespace:FormsVideoLibrary"
             x:Class="VideoPlayerDemos.PlayLibraryVideoPage"
             Title="Play Library Video">
    <StackLayout>
        <video:VideoPlayer x:Name="videoPlayer"
                           VerticalOptions="FillAndExpand" />

        <Button Text="Show Video Library"
                Margin="10"
                HorizontalOptions="Center"
                Clicked="OnShowVideoLibraryClicked" />
    </StackLayout>
</ContentPage>
```

Le fichier code-behind contient le gestionnaire `Clicked` pour le `Button`. L’appel du service de dépendance nécessite un appel à `DependencyService.Get` pour obtenir l’implémentation d’une interface `IVideoPicker` dans le projet de plateforme. La méthode `GetVideoFileAsync` est ensuite appelée sur cette instance :

```csharp
namespace VideoPlayerDemos
{
    public partial class PlayLibraryVideoPage : ContentPage
    {
        public PlayLibraryVideoPage()
        {
            InitializeComponent();
        }

       async void OnShowVideoLibraryClicked(object sender, EventArgs args)
        {
            Button btn = (Button)sender;
            btn.IsEnabled = false;

            string filename = await DependencyService.Get<IVideoPicker>().GetVideoFileAsync();

            if (!String.IsNullOrWhiteSpace(filename))
            {
                videoPlayer.Source = new FileVideoSource
                {
                    File = filename
                };
            }

            btn.IsEnabled = true;
        }
    }
}
```

Le gestionnaire `Clicked` utilise ensuite ce nom de fichier pour créer un objet `FileVideoSource` et pour le définir sur la propriété `Source` du `VideoPlayer`.

Chacune des classes `VideoPlayerRenderer` contient du code dans sa méthode `SetSource` pour les objets de type `FileVideoSource`. Vous pouvez les voir ci-dessous :

### <a name="handling-ios-files"></a>Gestion des fichiers iOS

La version iOS de `VideoPlayerRenderer` traite des objets `FileVideoSource` à l’aide de la méthode `Asset.FromUrl` statique avec le nom de fichier. Cela crée un objet `AVAsset` représentant le fichier dans la bibliothèque d’images de l’appareil :

```csharp
namespace FormsVideoLibrary.iOS
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, UIView>
    {
        ···
        void SetSource()
        {
            AVAsset asset = null;
            ···
            else if (Element.Source is FileVideoSource)
            {
                string uri = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(uri))
                {
                    asset = AVAsset.FromUrl(new NSUrl(uri));
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-android-files"></a>Gestion des fichiers Android

Lors du traitement des objets de type `FileVideoSource`, l’implémentation Android de `VideoPlayerRenderer` utilise la méthode `SetVideoPath` de `VideoView` pour spécifier le fichier dans la bibliothèque d’images de l’appareil :

```csharp
namespace FormsVideoLibrary.Droid
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, ARelativeLayout>
    {
        ···
        void SetSource()
        {
            isPrepared = false;
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    videoView.SetVideoPath(filename);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

### <a name="handling-uwp-files"></a>Gestion des fichiers UWP

Lors de la gestion des objets de type `FileVideoSource`, l’implémentation UWP de la méthode `SetSource` doit créer un objet `StorageFile`, ouvrir ce fichier pour la lecture et passer l’objet de flux à la méthode`SetSource` du `MediaElement` :

```csharp
namespace FormsVideoLibrary.UWP
{
    public class VideoPlayerRenderer : ViewRenderer<VideoPlayer, MediaElement>
    {
        protected override void OnElementChanged(ElementChangedEventArgs<VideoPlayer> args)
        {
            ···
        async void SetSource()
        {
            bool hasSetSource = false;
            ···
            else if (Element.Source is FileVideoSource)
            {
                // Code requires Pictures Library in Package.appxmanifest Capabilities to be enabled
                string filename = (Element.Source as FileVideoSource).File;

                if (!String.IsNullOrWhiteSpace(filename))
                {
                    StorageFile storageFile = await StorageFile.GetFileFromPathAsync(filename);
                    IRandomAccessStreamWithContentType stream = await storageFile.OpenReadAsync();
                    Control.SetSource(stream, storageFile.ContentType);
                    hasSetSource = true;
                }
            }
            ···
        }
        ···
    }
}
```

Pour chaque plateforme, la lecture de la vidéo débute presque immédiatement après la définition de la source vidéo, car le fichier se trouve sur l’appareil et n’a pas besoin d’être téléchargé.

## <a name="related-links"></a>Liens associés

- [Démonstrations de lecteur vidéo (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-videoplayerdemos)
- [Sélection d’une photo dans la bibliothèque d’images](~/xamarin-forms/app-fundamentals/dependency-service/photo-picker.md)
