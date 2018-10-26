---
title: Sélection d’une Photo dans la bibliothèque d’images
description: Cet article explique comment utiliser la classe Xamarin.Forms DependencyService pour sélectionner une photo à partir de la bibliothèque d’images du téléphone.
ms.prod: xamarin
ms.assetid: 4F51B0E7-6A63-403C-B488-500CCBCE75DD
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/06/2017
ms.openlocfilehash: 00308a6c7883d4ac6ce41592d4a0e18f9fb28d52
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50113311"
---
# <a name="picking-a-photo-from-the-picture-library"></a>Sélection d’une Photo dans la bibliothèque d’images

Cet article présente de la création d’une application qui permet à l’utilisateur de choisir une photo à partir de la bibliothèque d’images du téléphone. Étant donné que Xamarin.Forms n’inclut pas cette fonctionnalité, il est nécessaire d’utiliser [ `DependencyService` ](xref:Xamarin.Forms.DependencyService) pour accéder aux API natives sur chaque plateforme.  Cet article décrit les étapes suivantes pour l’utilisation de `DependencyService` pour cette tâche :

- **[Création de l’Interface](#Creating_the_Interface)**  &ndash; comprendre comment l’interface est créée dans le code partagé.
- **[iOS implémentation](#iOS_Implementation)**  &ndash; Apprenez à implémenter l’interface en code natif pour iOS.
- **[Implémentation Android](#Android_Implementation)**  &ndash; Apprenez à implémenter l’interface en code natif pour Android.
- **[Implémentation de la plateforme Windows universelle](#UWP_Implementation)**  &ndash; Apprenez à implémenter l’interface en code natif pour la plateforme Windows universelle (UWP).
- **[Mise en œuvre dans le Code partagé](#Implementing_in_Shared_Code)**  &ndash; Apprenez à utiliser `DependencyService` pour appeler l’implémentation native à partir de code partagé.

<a name="Creating_the_Interface" />

## <a name="creating-the-interface"></a>Création de l'interface utilisateur

Tout d’abord, créez une interface dans le code partagé qui exprime la fonctionnalité souhaitée. Dans le cas d’une application de choix d’une photo, qu’une seule méthode est requise. Cela est défini dans le [ `IPicturePicker` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/IPicturePicker.cs) interface dans la bibliothèque .NET Standard de l’exemple de code :

```csharp
namespace DependencyServiceSample
{
    public interface IPicturePicker
    {
        Task<Stream> GetImageStreamAsync();
    }
}
```

Le `GetImageStreamAsync` méthode est définie comme asynchrone, car la méthode doit retourner rapidement, mais il ne peut pas retourner un `Stream` objet pour la photo sélectionnée jusqu'à ce que l’utilisateur a parcouru la bibliothèque d’images et sélectionné une.

Cette interface est implémentée dans toutes les plateformes à l’aide de code spécifique à la plateforme.

<a name="iOS_Implementation" />

## <a name="ios-implementation"></a>Implémentation d’iOS

L’implémentation d’iOS de la `IPicturePicker` interface utilise le [ `UIImagePickerController` ](https://developer.xamarin.com/api/type/UIKit.UIImagePickerController/) comme décrit dans la [ **choisir une Photo dans la galerie** ](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery) Recipe (Recette) et [exemple de code](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery).

L’implémentation d’iOS est contenue dans le [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/iOS/PicturePickerImplementation.cs) classe dans le projet iOS de l’exemple de code. Pour que cette classe visible par le `DependencyService` manager, la classe doit être identifiée avec un [`assembly`] attribut de type `Dependency`, et la classe doit être publique et implémenter de manière explicite la `IPicturePicker` interface :

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

Le `GetImageStreamAsync` méthode crée un `UIImagePickerController` et l’initialise pour sélectionner des images dans la bibliothèque de photos. Deux gestionnaires d’événements sont nécessaires : un pour lorsque l’utilisateur sélectionne une photo et l’autre pour que l’utilisateur annule l’affichage de la bibliothèque de photos. Le `PresentModalViewController` affiche ensuite la bibliothèque de photos à l’utilisateur.

À ce stade, le `GetImageStreamAsync` méthode doit retourner un `Task<Stream>` objet au code qui l’appelle. Cette tâche est effectuée uniquement lorsque l’utilisateur a fini d’interagir avec la bibliothèque de photos et d’un des gestionnaires d’événements est appelé. Pour telles situations, le [ `TaskCompletionSource` ](https://msdn.microsoft.com/library/dd449174(v=vs.110).aspx) classe est essentielle. La classe fournit un `Task` objet du type générique approprié à retourner à partir de la `GetImageStreamAsync` méthode et la classe ultérieurement peuvent être signalé lorsque la tâche est terminée.

Le `FinishedPickingMedia` Gestionnaire d’événements est appelé lorsque l’utilisateur a sélectionné une image. Toutefois, le gestionnaire fournit un `UIImage` objet et le `Task` doit retourner un .NET `Stream` objet. Cette opération est effectuée en deux étapes : la `UIImage` objet est tout d’abord converti en un fichier JPEG en mémoire stocké dans un `NSData` objet, puis le `NSData` objet est converti en un .NET `Stream` objet. Un appel à la `SetResult` méthode de la `TaskCompletionSource` objet termine la tâche en fournissant le `Stream` objet :

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

Une application iOS requiert l’autorisation de l’utilisateur à accéder à la bibliothèque de photos du téléphone. Ajoutez le code suivant à la `dict` section du fichier Info.plist :

```xml
<key>NSPhotoLibraryUsageDescription</key>
<string>Picture Picker uses photo library</string>
```


<a name="Android_Implementation" />

## <a name="android-implementation"></a>Implémentation Android

L’implémentation Android utilise la technique décrite dans le [ **sélectionner une Image** ](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image) Recipe (Recette) et le [exemple de code](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image). Toutefois, la méthode est appelée lorsque l’utilisateur a sélectionné une image à partir de la bibliothèque d’images est une `OnActivityResult` substituer dans une classe qui dérive de `Activity`. Pour cette raison, la normale [ `MainActivity` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/MainActivity.cs) classe dans le projet Android a été complété par un champ, une propriété et une substitution de la `OnActivityResult` méthode :

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

Le `OnActivityResult`remplacement indique le fichier de l’image sélectionnée avec un Android `Uri` objet, mais cela peut être converti en un .NET `Stream` objet en appelant le `OpenInputStream` méthode de la `ContentResolver` objet qui a été obtenu à partir de la l’activité `ContentResolver` propriété.

Comme l’implémentation d’iOS, l’implémentation Android utilise un `TaskCompletionSource` pour signaler quand la tâche est terminée. Cela `TaskCompletionSource` objet est défini comme une propriété publique dans le `MainActivity` classe. Cela permet à la propriété d’être référencés dans le [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/Droid/PicturePickerImplementation.cs) classe dans le projet Android. C’est la classe avec le `GetImageStreamAsync` méthode :

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

Cette méthode accède à la `MainActivity` classe à plusieurs fins : pour le `Instance` propriété, pour le `PickImageId` field, pour le `TaskCompletionSource` propriété et d’appeler `StartActivityForResult`. Cette méthode est définie par le `FormsAppCompatActivity` (classe), qui est la classe de base de `MainActivity`.

<a name="UWP_Implementation" />

## <a name="uwp-implementation"></a>Implémentation de UWP

Contrairement à iOS et Android implémentations, l’implémentation du sélecteur de photo pour la plateforme Windows universelle ne nécessite pas la `TaskCompletionSource` classe. Le [ `PicturePickerImplementation` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/UWP/PicturePickerImplementation.cs) classe utilise le [ `FileOpenPicker` ](/uwp/api/Windows.Storage.Pickers.FileOpenPicker/) classe pour accéder à la bibliothèque de photos. Étant donné que le `PickSingleFileAsync` méthode de `FileOpenPicker` lui-même est asynchrone, le `GetImageStreamAsync` méthode peut simplement utiliser `await` avec cette méthode (et d’autres méthodes asynchrones) et retourner un `Stream` objet :


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

<a name="Implementing_in_Shared_Code" />

## <a name="implementing-in-shared-code"></a>Mise en œuvre dans le Code partagé

Maintenant que l’interface a été implémenté pour chaque plateforme, l’application dans la bibliothèque .NET Standard peut tirer parti de celui-ci.

Le [ `App` ](https://github.com/xamarin/xamarin-forms-samples/blob/master/DependencyService/DependencyServiceSample/DependencyServiceSample/DependencyServiceSample.cs) classe crée un `Button` pour sélectionner une photo :

```csharp
Button pickPictureButton = new Button
{
    Text = "Pick Photo",
    VerticalOptions = LayoutOptions.CenterAndExpand,
    HorizontalOptions = LayoutOptions.CenterAndExpand
};
stack.Children.Add(pickPictureButton);
```

Le `Clicked` Gestionnaire utilise la `DependencyService` doit appeler `GetImageStreamAsync`. Cela entraîne un appel dans le projet de plateforme. Si la méthode retourne un `Stream` de l’objet, puis crée le Gestionnaire d’un `Image` élément image avec un `TapGestureRecognizer`et remplace le `StackLayout` sur la page avec qui `Image`:

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

En appuyant sur la `Image` élément retourne la page à la normale.


## <a name="related-links"></a>Liens associés

- [Choisissez une Photo à partir de la galerie (iOS)](https://github.com/xamarin/recipes/tree/master/Recipes/ios/media/video_and_photos/choose_a_photo_from_the_gallery)
- [Sélectionnez une Image (Android)](https://github.com/xamarin/recipes/tree/master/Recipes/android/other_ux/pick_image)
- [DependencyService (exemple)](https://developer.xamarin.com/samples/xamarin-forms/DependencyService/DependencyServiceSample)
