---
title: Sélection d’une photo dans la bibliothèque d’images
description: Cet article explique comment utiliser la Xamarin.Forms classe DependencyService pour choisir une photo dans la bibliothèque d’images du téléphone.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 2d66464798860848e42e05bb3aaa36fc80e115b1
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93369400"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Sélection d’une photo dans la bibliothèque d’images

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/dependencyservice/)

Cet article vous montre comment créer une application qui permet à l’utilisateur de sélectionner une photo dans la bibliothèque d’images du téléphone. Étant donné que Xamarin.Forms n’inclut pas cette fonctionnalité, il est nécessaire d’utiliser [`DependencyService`](xref:Xamarin.Forms.DependencyService) pour accéder aux API natives sur chaque plateforme.

## <a name="creating-the-interface"></a>Création de l’interface

Tout d’abord, créez une interface dans le code partagé qui expose la fonctionnalité que vous souhaitez implémenter. Dans le cas d’une application de sélection de photo, une seule méthode est requise. Cela est défini dans l'  [`IPhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos/Services/IPhotoPickerService.cs) interface de la bibliothèque de .NET standard de l’exemple de code :

```csharp
namespace DependencyServiceDemos
{
    public interface IPhotoPickerService
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

La méthode `GetImageStreamAsync` est définie comme asynchrone, car elle doit retourner le résultat rapidement, mais elle ne doit pas retourner d’objet `Stream` pour la photo sélectionnée tant que l’utilisateur n’a pas parcouru la bibliothèque d’images et sélectionné une photo.

Cette interface est implémentée dans chaque plateforme à l’aide de code spécifique.

## <a name="ios-implementation"></a>Implémentation iOS

L’implémentation iOS de l' `IPhotoPickerService` interface utilise la [`UIImagePickerController`](xref:UIKit.UIImagePickerController) , comme décrit dans la recette [**de la Galerie**](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) et l’exemple de [code](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L’implémentation iOS est contenue dans la [`PhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.iOS/Services/PhotoPickerService.cs) classe du projet iOS de l’exemple de code. Pour que cette classe soit visible par le manager `DependencyService`, la classe doit être identifiée par un attribut [`assembly`] de type `Dependency`, être publique et implémenter de manière explicite l’interface `IPhotoPickerService` :

```csharp
[assembly: Dependency (typeof (PhotoPickerService))]
namespace DependencyServiceDemos.iOS
{
    public class PhotoPickerService : IPhotoPickerService
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
            viewController.PresentViewController(imagePicker, true, null);

            // Return Task object
            taskCompletionSource = new TaskCompletionSource<Stream>();
            return taskCompletionSource.Task;
        }
        ...
    }
}

```

La méthode `GetImageStreamAsync` crée un `UIImagePickerController` et l’initialise pour sélectionner des images dans la photothèque. Deux gestionnaires d’événements sont nécessaires : l’un est utilisé quand l’utilisateur sélectionne une photo et l’autre quand l’utilisateur annule l’affichage de la photothèque. La `PresentViewController` méthode affiche ensuite la bibliothèque de photos pour l’utilisateur.

À ce stade, la méthode `GetImageStreamAsync` doit retourner un objet `Task<Stream>` au code qui l’appelle. Cette tâche est exécutée uniquement quand l’utilisateur a fini d’interagir avec la photothèque et que l’un des gestionnaires d’événements est appelé. Pour les cas de ce type, la [`TaskCompletionSource`](/dotnet/api/system.threading.tasks.taskcompletionsource-1) classe est essentielle. La classe fournit un objet `Task` du type générique approprié à retourner de la méthode `GetImageStreamAsync`, et la classe peut être signalée ultérieurement à la fin de la tâche.

Le gestionnaire d’événements `FinishedPickingMedia` est appelé quand l’utilisateur a sélectionné une image. Toutefois, le gestionnaire fournit un objet `UIImage` et `Task` doit retourner un objet `Stream` .NET. Cette opération s’effectue en deux étapes : l' `UIImage` objet est d’abord converti en un fichier PNG en mémoire ou JPEG stocké dans un `NSData` objet, puis l' `NSData` objet est converti en `Stream` objet .net. Un appel à la méthode `SetResult` de l’objet `TaskCompletionSource` exécute la tâche en fournissant l’objet `Stream` :

```csharp
namespace DependencyServiceDemos.iOS
{
    public class PhotoPickerService : IPhotoPickerService
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
                NSData data;
                if (args.ReferenceUrl.PathExtension.Equals("PNG") || args.ReferenceUrl.PathExtension.Equals("png"))
                {
                    data = image.AsPNG();
                }
                else
                {
                    data = image.AsJPEG(1);
                }
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

L’implémentation Android utilise la technique décrite dans la recette [**Sélectionner une image**](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) et l’ [exemple de code](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Toutefois, la méthode appelée quand l’utilisateur a sélectionné une image dans la bibliothèque d’images est une substitution de `OnActivityResult` dans une classe dérivée de `Activity`. Pour cette raison, la [`MainActivity`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.Android/MainActivity.cs) classe normale dans le projet Android a été complétée par un champ, une propriété et une substitution de la `OnActivityResult` méthode :

```csharp
public class MainActivity : FormsAppCompatActivity
{
    internal static MainActivity Instance { get; private set; }  

    protected override void OnCreate(Bundle savedInstanceState)
    {
        // ...
        Instance = this;
    }
    // ...
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

Comme l’implémentation iOS, l’implémentation Android utilise un objet `TaskCompletionSource` pour signaler la fin de la tâche. Cet objet `TaskCompletionSource` est défini en tant que propriété publique dans la classe `MainActivity`. Cela permet à la propriété d’être référencée dans la [`PhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.Android/Services/PhotoPickerService.cs) classe du projet Android. Voici la classe avec la méthode `GetImageStreamAsync` :

```csharp
[assembly: Dependency(typeof(PhotoPickerService))]
namespace DependencyServiceDemos.Droid
{
    public class PhotoPickerService : IPhotoPickerService
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

Contrairement aux implémentations iOS et Android, l’implémentation du sélecteur de photo pour la plateforme Windows universelle n’utilise pas la classe `TaskCompletionSource`. La [`PhotoPickerService`](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceDemos.UWP/Services/PhotoPickerService.cs) classe utilise la [`FileOpenPicker`](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) classe pour accéder à la bibliothèque de photos. Étant donné que la méthode `PickSingleFileAsync` de `FileOpenPicker` est elle-même asynchrone, la méthode `GetImageStreamAsync` peut simplement utiliser `await` avec cette méthode (et d’autres méthodes asynchrones) et retourner un objet `Stream` :

```csharp
[assembly: Dependency(typeof(PhotoPickerService))]
namespace DependencyServiceDemos.UWP
{
    public class PhotoPickerService : IPhotoPickerService
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

Maintenant que l’interface a été implémentée pour chaque plateforme, le code partagé dans la bibliothèque .NET Standard peut l’utiliser.

L’interface utilisateur comprend un [`Button`](xref:Xamarin.Forms.Button) sur lequel vous pouvez cliquer pour choisir une photo :

```xaml
<Button Text="Pick Photo"
        Clicked="OnPickPhotoButtonClicked" />
```

Le gestionnaire d’événements `Clicked` utilise la classe `DependencyService` pour appeler `GetImageStreamAsync`. Il s’ensuit un appel au projet de plateforme. Si la méthode retourne un objet `Stream`, le gestionnaire définit la propriété `Source` de l’objet `image` aux données `Stream` :

```csharp
async void OnPickPhotoButtonClicked(object sender, EventArgs e)
{
    (sender as Button).IsEnabled = false;

    Stream stream = await DependencyService.Get<IPhotoPickerService>().GetImageStreamAsync();
    if (stream != null)
    {
        image.Source = ImageSource.FromStream(() => stream);
    }

    (sender as Button).IsEnabled = true;
}
```

## <a name="related-links"></a>Liens connexes

- [DependencyService (exemple)](/samples/xamarin/xamarin-forms-samples/dependencyservice/)
- [Choisir une photo dans la galerie (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Sélectionner une image (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)