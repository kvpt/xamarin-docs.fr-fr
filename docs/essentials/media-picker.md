---
title: 'Xamarin.Essentials: Sélecteur de média'
description: La classe MediaPicker dans Xamarin.Essentials permet à un utilisateur de choisir ou de prendre une photo ou une vidéo sur l’appareil.
ms.assetid: 23460875-6cf9-4440-a97b-46c55b0bca69
author: jamesmontemagno
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a988803b5e18ab91ff65a2b4976158458f68274
ms.sourcegitcommit: 58247fe066ad271ee43c8967ac3301fdab6ca2d1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629559"
---
# <a name="no-locxamarinessentials-media-picker"></a>Xamarin.Essentials: Sélecteur de média

La classe **MediaPicker** permet à l’utilisateur de choisir ou de prendre une photo ou une vidéo sur l’appareil.

![API de la version préliminaire](~/media/shared/preview.png)

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **MediaPicker** , la configuration spécifique à la plateforme suivante est requise.

# <a name="android"></a>[Android](#tab/android)

Les autorisations suivantes sont requises et doivent être configurées dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
// Needed for Picking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.ReadExternalStorage)]

// Needed for Taking photo/video
[assembly: UsesPermission(Android.Manifest.Permission.WriteExternalStorage)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]

// Add these properties if you would like to filter out cameras that do not have cameras or set to false to make them optional
[assembly: UsesFeature("android.hardware.camera", Required = true)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = true)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** , puis ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.CAMERA" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous le **manifeste Android** , recherchez la zone **autorisations requises :** , puis vérifiez ces autorisations. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml** .

# <a name="ios"></a>[iOS](#tab/ios)

Dans, `Info.plist` Ajoutez les clés suivantes :

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs access to the camera to take photos.</string>
<key>NSMicrophoneUsageDescription</key>
<string>This app needs access to microphone for taking videos.</string>
<key>NSPhotoLibraryAddUsageDescription</key>
<string>This app needs access to the photo gallery for picking photos and videos.</string>
<key>NSPhotoLibraryUsageDescription</key>
<string>This app needs access to photos gallery for picking photos and videos.</string>
```

Veillez à mettre à jour le `<string>` dans chaque avec une description spécifique à votre application telle qu’elle sera affichée à vos utilisateurs.

# <a name="uwp"></a>[UWP](#tab/uwp)

Dans les `Package.appxmanifest` **fonctionnalités** sous, assurez-vous que les `Microphone` `Webcam` fonctionnalités et sont activées.

-----

## <a name="using-media-picker"></a>Utilisation du sélecteur de média

La `MediaPicker` classe possède les méthodes suivantes qui retournent toutes un `FileResult` qui peut être utilisé pour obtenir l’emplacement des fichiers ou le lire en tant que `Stream` .

* `PickPhotoAsync`: Ouvre l’Explorateur de médias pour sélectionner une photo.
* `CapturePhotoAsync`: Ouvre l’appareil photo pour prendre une photo.
* `PickVideoAsync`: Ouvre l’Explorateur de médias pour sélectionner une vidéo.
* `CaptureVideoAsync`: Ouvre l’appareil photo pour prendre une vidéo.

Chaque méthode accepte éventuellement un `MediaPickerOptions` paramètre qui autorise la définition de `Title` sur certains systèmes d’exploitation qui sont affichés aux utilisateurs.

> [!TIP]
> Toutes les méthodes doivent être appelées sur le thread d’interface utilisateur, car les vérifications et les demandes d’autorisations sont gérées automatiquement par Xamarin.Essentials .

## <a name="general-usage"></a>Utilisation générale

```csharp
async Task TakePhotoAsync()
{
    try
    {
        var photo = await MediaPicker.CapturePhotoAsync();
        await LoadPhotoAsync(photo);
        Console.WriteLine($"CapturePhotoAsync COMPLETED: {PhotoPath}");
    }
    catch (Exception ex)
    {
        Console.WriteLine($"CapturePhotoAsync THREW: {ex.Message}");
    }
}

async Task LoadPhotoAsync(FileResult photo)
{
    // canceled
    if (photo == null)
    {
        PhotoPath = null;
        return;
    }
    // save the file into local storage
    var newFile = Path.Combine(FileSystem.CacheDirectory, photo.FileName);
    using (var stream = await photo.OpenReadAsync())
    using (var newStream = File.OpenWrite(newFile))
        await stream.CopyToAsync(newStream);

    PhotoPath = newFile;
}
```


## <a name="api"></a>API

- [Code source MediaPicker](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/MediaPicker)
- [Documentation de l’API MediaPicker](xref:Xamarin.Essentials.MediaPicker)
