---
titre : « Xamarin.Essentials : torchure » Description : « ce document décrit la classe de torche dans Xamarin.Essentials , qui a la possibilité d’allumer ou de sortir le flash de l’appareil photo de l’appareil pour la transformer en torche. »
ms. AssetID : 06A03553-D212-43A2-9E6E-C2D2D93EB136 Author : jamesmontemagno ms. Custom : vidéo ms. Author : Jamont ms. Date : 11/04/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-flashlight"></a>Xamarin.Essentials: Torche

La classe **Flashlight** permet d’activer ou de désactiver le flash de l’appareil photo de l’appareil pour transformer celui-ci en lampe de poche.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **lampe de poche**, la configuration suivante spécifique à la plateforme est obligatoire.

# <a name="android"></a>[Android](#tab/android)

Les autorisations Flashlight et Camera sont obligatoires, et doivent être configurées dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Flashlight)]
[assembly: UsesPermission(Android.Manifest.Permission.Camera)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **fichier AndroidManifest. xml** dans le dossier **Propriétés** et ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.FLASHLIGHT" />
<uses-permission android:name="android.permission.CAMERA" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous **Manifeste Android**, recherchez la zone **Autorisations nécessaires**, puis cochez les autorisations **FLASHLIGHT** et **CAMERA**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

Si vous ajoutez ces autorisations, [Google Play va filtrer automatiquement les appareils](https://developer.android.com/guide/topics/manifest/uses-feature-element.html#permissions-features) n’ayant pas un matériel spécifique. Vous pouvez contourner ce problème en ajoutant ce qui suit au fichier AssemblyInfo.cs de votre projet Android :

```csharp
[assembly: UsesFeature("android.hardware.camera", Required = false)]
[assembly: UsesFeature("android.hardware.camera.autofocus", Required = false)]
```

[!include[](~/essentials/includes/android-permissions.md)]

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune configuration supplémentaire n’est requise.

-----

## <a name="using-flashlight"></a>Utilisation de la lampe de poche

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Vous pouvez activer et désactiver la lampe de poche à l’aide des méthodes `TurnOnAsync` et `TurnOffAsync` :

```csharp
try
{
    // Turn On
    await Flashlight.TurnOnAsync();

    // Turn Off
    await Flashlight.TurnOffAsync();
}
catch (FeatureNotSupportedException fnsEx)
{
    // Handle not supported on device exception
}
catch (PermissionException pEx)
{
    // Handle permission exception
}
catch (Exception ex)
{
    // Unable to turn on/off flashlight
}
```

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

### <a name="android"></a>[Android](#tab/android)

La classe Flashlight a été optimisée en fonction du système d’exploitation de l’appareil.

#### <a name="api-level-23-and-higher"></a>Niveau d’API 23 et plus

Avec les nouveaux niveaux d’API, le [mode Torche](https://developer.android.com/reference/android/hardware/camera2/CameraManager.html#setTorchMode) permet d’activer ou de désactiver le flash de l’appareil.

#### <a name="api-level-22-and-lower"></a>Niveau d’API 22 et moins

Une texture de surface d’appareil photo est créée pour activer ou désactiver le `FlashMode` de l’unité d’appareil photo.

### <a name="ios"></a>[iOS](#tab/ios)

[AVCaptureDevice](xref:AVFoundation.AVCaptureDevice) permet d’activer et de désactiver le mode Torche et Flash de l’appareil.

### <a name="uwp"></a>[UWP](#tab/uwp)

[Lamp](https://docs.microsoft.com/uwp/api/windows.devices.lights.lamp) permet de détecter l’activation ou la désactivation de la première lampe à l’arrière de l’appareil.

-----

## <a name="api"></a>API

- [Code source Flashlight](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Flashlight)
- [Documentation sur l’API Flashlight](xref:Xamarin.Essentials.Flashlight)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Flashlight-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
