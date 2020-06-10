---
titre : « Xamarin.Essentials : vibratoire » Description : « ce document décrit la classe vibratoire dans Xamarin.Essentials , qui vous permet de démarrer et d’arrêter la fonctionnalité vibreur pendant un laps de temps souhaité. »
ms. AssetID : 7E8B24C4-2625-4DAE-A129-383542D34F1E Author : jamesmontemagno ms. Custom : vidéo ms. Author : Jamont ms. Date : 11/04/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials: Vibration

La classe **Vibration** vous permet de démarrer et d’arrêter la fonctionnalité de vibration pendant la durée souhaitée.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **vibration**, la configuration suivante spécifique à la plateforme est obligatoire.

# <a name="android"></a>[Android](#tab/android)

L’autorisation Vibrate est obligatoire, et doit être configurée dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** , puis ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous **Manifeste Android**, recherchez la zone **Autorisations nécessaires**, puis cochez l’autorisation **VIBRATE**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

## <a name="using-vibration"></a>Utilisation de la vibration

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Vous pouvez demander la fonctionnalité de vibration pour une durée définie ou pour une durée par défaut de 500 millisecondes.

```csharp
try
{
    // Use default vibration length
    Vibration.Vibrate();

    // Or use specified time
    var duration = TimeSpan.FromSeconds(1);
    Vibration.Vibrate(duration);
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

Vous pouvez demander l’annulation de la vibration de l’appareil avec la méthode `Cancel` :

```csharp
try
{
    Vibration.Cancel();
}
catch (FeatureNotSupportedException ex)
{
    // Feature not supported on device
}
catch (Exception ex)
{
    // Other error has occurred.
}
```

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

Aucune différence entre les plateformes.

# <a name="ios"></a>[iOS](#tab/ios)

- Vibre uniquement quand l’appareil est configuré pour vibrer en cas de sonnerie.
- Vibre toujours pendant 500 millisecondes.
- Impossible d’annuler la vibration.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité de vibration](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentation sur l’API de vibration](xref:Xamarin.Essentials.Vibration)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Vibration-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
