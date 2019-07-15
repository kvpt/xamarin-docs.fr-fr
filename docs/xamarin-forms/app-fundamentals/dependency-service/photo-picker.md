---
title: Sélection d’une photo dans la bibliothèque d’images
description: Cet article explique comment utiliser la classe DependencyService de Xamarin.Forms pour sélectionner une photo dans la bibliothèque d’images du téléphone.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 36601b02bb2984d9350166dedac0d650d9642f91
ms.sourcegitcommit: c1d85b2c62ad84c22bdee37874ad30128581bca6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67650646"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Sélection d’une photo dans la bibliothèque d’images

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)

Cet article vous montre comment créer une application qui permet à l’utilisateur de sélectionner une photo dans la bibliothèque d’images du téléphone. Étant donné que Xamarin.Forms n’inclut pas cette fonctionnalité, vous devez utiliser [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour accéder aux API natives sur chaque plateforme.

## <a name="creating-the-interface"></a>Création de l’interface

Tout d’abord, créez une interface dans le code partagé qui expose la fonctionnalité que vous souhaitez implémenter. Dans le cas d’une application de sélection de photo, une seule méthode est requise. Elle est définie dans l’interface [`IPicturePicker`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) dans la bibliothèque .NET Standard de l’exemple de code :

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

La méthode `GetImageStreamAsync` est définie comme asynchrone, car elle doit retourner le résultat rapidement, mais elle ne doit pas retourner d’objet `Stream` pour la photo sélectionnée tant que l’utilisateur n’a pas parcouru la bibliothèque d’images et sélectionné une photo.

Cette interface est implémentée dans chaque plateforme à l’aide de code spécifique.

## <a name="ios-implementation"></a>Implémentation iOS

L’implémentation iOS de l’interface `IPicturePicker` utilise [`UIImagePickerController`](xref:UIKit.UIImagePickerController) de la manière décrite dans la recette [**Choisir une photo dans la galerie**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) et l’[exemple de code](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L’implémentation iOS est contenue dans la classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) dans le projet iOS de l’exemple de code. Pour que cette classe soit visible par le manager `DependencyService`, la classe doit être identifiée par un attribut [`assembly`] de type `Dependency`, être publique et implémenter de manière explicite l’interface `IPicturePicker` :

```csharp
[assembly: Dependency (typeof (PicturePickerImplementation))]

namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;

        public Task<Stream> GetImageStreamAsync()
        {
            // Create and define UIImagePickerController
            imagePicker = new UIImagePickerController
            {
                SourceType = UIImagePickerControllerSourceType.PhotoLibrary,
                MediaTypes = UIImagePickerController.AvailableMediaTypes(UIImagePickerControllerSourceType.PhotoLibrary)
            };

            // Set event handlers
            imagePicker.FinishedPickingMedia += OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled += OnImagePickerCancelled;

            // Present UIImagePickerController;
            UIWindow window = UIApplication.SharedApplication.KeyWindow;
            var viewController = window.RootViewController;
            viewController.PresentModalViewController(imagePicker, true);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

La méthode `GetImageStreamAsync` crée un `UIImagePickerController` et l’initialise pour sélectionner des images dans la photothèque. Deux gestionnaires d’événements sont nécessaires : L’un est utilisé quand l’utilisateur sélectionne une photo et l’autre quand l’utilisateur annule l’affichage de la photothèque. `PresentModalViewController` affiche ensuite la photothèque à l’utilisateur.

À ce stade, la méthode `GetImageStreamAsync` doit retourner un objet `Task<Stream>` au code qui l’appelle. Cette tâche est exécutée uniquement quand l’utilisateur a fini d’interagir avec la photothèque et que l’un des gestionnaires d’événements est appelé. Dans ces situations, la classe [`TaskCompletionSource`](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) est essentielle. La classe fournit un objet `Task` du type générique approprié à retourner de la méthode `GetImageStreamAsync`, et la classe peut être signalée ultérieurement à la fin de la tâche.

Le gestionnaire d’événements `FinishedPickingMedia` est appelé quand l’utilisateur a sélectionné une image. Toutefois, le gestionnaire fournit un objet `UIImage` et `Task` doit retourner un objet `Stream` .NET. Cette tâche s’exécute en deux étapes : L’objet `UIImage` est d’abord converti en un fichier JPEG stocké en mémoire dans un objet `NSData`, puis l’objet `NSData` est converti en un objet `Stream` .NET. Un appel à la méthode `SetResult` de l’objet `TaskCompletionSource` exécute la tâche en fournissant l’objet `Stream` :

```csharp
namespace DependencyServiceSample.iOS
{
    public class PicturePickerImplementation : IPicturePicker
    {
        TaskCompletionSource<Stream> taskCompletionSource;
        UIImagePickerController imagePicker;
        ...
        void OnImagePickerFinishedPickingMedia(object sender, UIImagePickerMediaPickedEventArgs args)
        {
            UIImage image = args.EditedImage ?? args.OriginalImage;

            if (image != null)
            {
                // Convert UIImage to .NET Stream object
                NSData data = image.AsJPEG(1);
                Stream stream = data.AsStream();

                UnregisterEventHandlers();

                // Set the Stream as the completion of the Task
                taskCompletionSource.SetResult(stream);
            }
            else
            {
                UnregisterEventHandlers();
                taskCompletionSource.SetResult(null);
            }
            imagePicker.DismissModalViewController(true);
        }

        void OnImagePickerCancelled(object sender, EventArgs args)
        {
            UnregisterEventHandlers();
            taskCompletionSource.SetResult(null);
            imagePicker.DismissModalViewController(true);
        }

        void UnregisterEventHandlers()
        {
            imagePicker.FinishedPickingMedia -= OnImagePickerFinishedPickingMedia;
            imagePicker.Canceled -= OnImagePickerCancelled;
        }
    }
}

```

Une application iOS doit être autorisée par l’utilisateur à accéder à la photothèque du téléphone. Ajoutez le code suivant dans la section `dict` du fichier Info.plist :

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```

## <a name="android-implementation"></a>Implémentation Android

L’implémentation Android utilise la technique décrite dans la recette [**Sélectionner une image**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) et l’[exemple de code](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Toutefois, la méthode appelée quand l’utilisateur a sélectionné une image dans la bibliothèque d’images est une substitution de `OnActivityResult` dans une classe dérivée de `Activity`. Pour cette raison, la classe [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) normale dans le projet Android a été complétée par un champ, une propriété et une substitution de la méthode `OnActivityResult` :

```csharp
public class MainActivity : FormsAppCompatActivity
{
    ...
    // Field, property, and method for Picture Picker
    public static readonly int PickImageId = 1000;

    public TaskCompletionSource<Stream> PickImageTaskCompletionSource { set; get; }

    protected override void OnActivityResult(int requestCode, Result resultCode, Intent intent)
    {
        base.OnActivityResult(requestCode, resultCode, intent);

        if (requestCode == PickImageId)
        {
            if ((resultCode == Result.Ok) && (intent != null))
            {
                Android.Net.Uri uri = intent.Data;
                Stream stream = ContentResolver.OpenInputStream(uri);

                // Set the Stream as the completion of the Task
                PickImageTaskCompletionSource.SetResult(stream);
            }
            else
            {
                PickImageTaskCompletionSource.SetResult(null);
            }
        }
    }
}

```

La substitution de `OnActivityResult` spécifie le fichier de l’image sélectionnée à l’aide d’un objet `Uri` Android, mais cet objet peut être converti en objet `Stream` .NET en appelant la méthode `OpenInputStream` de l’objet `ContentResolver` qui a été obtenu à partir de la propriété `ContentResolver` de l’activité.

Comme l’implémentation iOS, l’implémentation Android utilise un objet `TaskCompletionSource` pour signaler la fin de la tâche. Cet objet `TaskCompletionSource` est défini en tant que propriété publique dans la classe `MainActivity`. Cela permet de référencer la propriété dans la classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) dans le projet Android. Voici la classe avec la méthode `GetImageStreamAsync` :

```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.Droid
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public Task<Stream> GetImageStreamAsync()
        {
            // Define the Intent for getting images
            Intent intent = new Intent();
            intent.SetType("image/*");
            intent.SetAction(Intent.ActionGetContent);

            // Start the picture-picker activity (resumes in MainActivity.cs)
            MainActivity.Instance.StartActivityForResult(
                Intent.CreateChooser(intent, "Select Picture"),
                MainActivity.PickImageId);

            // Save the TaskCompletionSource object as a MainActivity property
            MainActivity.Instance.PickImageTaskCompletionSource = new TaskCompletionSource<Stream>();

            // Return Task object
            return MainActivity.Instance.PickImageTaskCompletionSource.Task;
        }
    }
}
```

Cette méthode accède à la classe `MainActivity` à plusieurs fins : pour la propriété `Instance`, pour le champ `PickImageId`, pour la propriété `TaskCompletionSource` et pour appeler `StartActivityForResult`. Cette méthode est définie par la classe `FormsAppCompatActivity`, qui est la classe de base de `MainActivity`.

## <a name="uwp-implementation"></a>Implémentation UWP

Contrairement aux implémentations iOS et Android, l’implémentation du sélecteur de photo pour la plateforme Windows universelle n’utilise pas la classe `TaskCompletionSource`. La classe [`PicturePickerImplementation`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) se sert de la classe [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) pour accéder à la photothèque. Étant donné que la méthode `PickSingleFileAsync` de `FileOpenPicker` est elle-même asynchrone, la méthode `GetImageStreamAsync` peut simplement utiliser `await` avec cette méthode (et d’autres méthodes asynchrones) et retourner un objet `Stream` :


```csharp
[assembly: Dependency(typeof(PicturePickerImplementation))]

namespace DependencyServiceSample.UWP
{
    public class PicturePickerImplementation : IPicturePicker
    {
        public async Task<Stream> GetImageStreamAsync()
        {
            // Create and initialize the FileOpenPicker
            FileOpenPicker openPicker = new FileOpenPicker
            {
                ViewMode = PickerViewMode.Thumbnail,
                SuggestedStartLocation = PickerLocationId.PicturesLibrary,
            };

            openPicker.FileTypeFilter.Add(".jpg");
            openPicker.FileTypeFilter.Add(".jpeg");
            openPicker.FileTypeFilter.Add(".png");

            // Get a file and return a Stream
            StorageFile storageFile = await openPicker.PickSingleFileAsync();

            if (storageFile == null)
            {
                return null;
            }

            IRandomAccessStreamWithContentType raStream = await storageFile.OpenReadAsync();
            return raStream.AsStreamForRead();
        }
    }
}
```

## <a name="implementing-in-shared-code"></a>Implémentation dans le code partagé

Maintenant que l’interface a été implémentée pour chaque plateforme, l’application dans la bibliothèque .NET Standard peut l’utiliser.

La classe [`App`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) crée un objet `Button` pour sélectionner une photo :

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

Le gestionnaire d’événements `Clicked` utilise la classe `DependencyService` pour appeler `GetImageStreamAsync`. Il s’ensuit un appel dans le projet de plateforme. Si la méthode retourne un objet `Stream`, le gestionnaire crée un élément `Image` pour cette image avec un `TapGestureRecognizer` et remplace le `StackLayout` sur la page avec cet élément `Image` :

```csharp
pickPictureButton.Clicked += async (sender, e) =>
{
    pickPictureButton.IsEnabled = false;
    Stream stream = await DependencyService.Get<IPicturePicker>().GetImageStreamAsync();

    if (stream != null)
    {
        Image image = new Image
        {
            Source = ImageSource.FromStream(() => stream),
            BackgroundColor = Color.Gray
        };

        TapGestureRecognizer recognizer = new TapGestureRecognizer();
        recognizer.Tapped += (sender2, args) =>
        {
            (MainPage as ContentPage).Content = stack;
            pickPictureButton.IsEnabled = true;
        };
        image.GestureRecognizers.Add(recognizer);

        (MainPage as ContentPage).Content = image;
    }
    else
    {
        pickPictureButton.IsEnabled = true;
    }
};
```

Quand l’utilisateur appuie sur l’élément `Image`, il revient à la page standard.

## <a name="related-links"></a>Liens connexes

- [Choisir une photo dans la galerie (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Sélectionner une image (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (exemple)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
