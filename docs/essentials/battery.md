---
titre : « Xamarin.Essentials : Battery » Description : « ce document décrit la classe de batterie dans Xamarin.Essentials , qui vous permet de vérifier les informations sur la batterie de l’appareil et de surveiller les modifications ».
ms. AssetID : 47EB26D8-8C62-477B-A13C-6977F74E6E43 auteur : jamesmontemagno ms. Author : Jamont ms. Date : 01/22/2019 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-battery"></a>Xamarin.Essentials: Batterie

La classe **Battery** vous permet de vérifier les informations de batterie de l’appareil et de superviser les changements. Elle fournit aussi des informations sur l’état d’économie d’énergie de l’appareil, qui indique si l’appareil s’exécute dans un mode de basse consommation. Les applications doivent de préférence éviter le traitement en arrière-plan lorsque l’économie d’énergie de l’appareil est activée.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **batterie**, la configuration suivante spécifique à la plateforme est obligatoire.

# <a name="android"></a>[Android](#tab/android)

L’autorisation `Battery` est obligatoire, et doit être configurée dans le projet Android. Vous pouvez l’ajouter des façons suivantes :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.BatteryStats)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **fichier AndroidManifest. xml** dans le dossier **Propriétés** et ajoutez le code suivant dans le nœud **manifeste** .

```xml
<uses-permission android:name="android.permission.BATTERY_STATS" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous **Manifeste Android**, recherchez la zone **Autorisations nécessaires**, puis cochez l’autorisation **Battery**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="ios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwp"></a>[UWP](#tab/uwp)

Aucune configuration supplémentaire n’est requise.

-----

## <a name="using-battery"></a>Utilisation de la batterie

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Examinez les informations actuelles relatives à la batterie :

```csharp
var level = Battery.ChargeLevel; // returns 0.0 to 1.0 or 1.0 when on AC or no battery.

var state = Battery.State;

switch (state)
{
    case BatteryState.Charging:
        // Currently charging
        break;
    case BatteryState.Full:
        // Battery is full
        break;
    case BatteryState.Discharging:
    case BatteryState.NotCharging:
        // Currently discharging battery or not being charged
        break;
    case BatteryState.NotPresent:
        // Battery doesn't exist in device (desktop computer)
    case BatteryState.Unknown:
        // Unable to detect battery state
        break;
}

var source = Battery.PowerSource;

switch (source)
{
    case BatteryPowerSource.Battery:
        // Being powered by the battery
        break;
    case BatteryPowerSource.AC:
        // Being powered by A/C unit
        break;
    case BatteryPowerSource.Usb:
        // Being powered by USB cable
        break;
    case BatteryPowerSource.Wireless:
        // Powered via wireless charging
        break;
    case BatteryPowerSource.Unknown:
        // Unable to detect power source
        break;
}
```

Chaque fois que l’une des propriétés de la batterie change, un événement est déclenché :

```csharp
public class BatteryTest
{
    public BatteryTest()
    {
        // Register for battery changes, be sure to unsubscribe when needed
        Battery.BatteryInfoChanged += Battery_BatteryInfoChanged;
    }

    void Battery_BatteryInfoChanged(object sender, BatteryInfoChangedEventArgs   e)
    {
        var level = e.ChargeLevel;
        var state = e.State;
        var source = e.PowerSource;
        Console.WriteLine($"Reading: Level: {level}, State: {state}, Source: {source}");
    }
}
```

Les appareils qui fonctionnent sur batterie peuvent être mis en mode faible consommation d’énergie. Parfois, ce basculement se fait automatiquement, par exemple, lorsque la batterie est à moins de 20 % de sa capacité. En mode économie d’énergie, le système d’exploitation réduit les activités qui ont tendance à épuiser la batterie. Les applications peuvent aller dans ce sens en évitant le traitement en arrière-plan ou autres activités énergivores lorsque le mode économie d’énergie est activé.

Vous pouvez aussi obtenir l’état d’économie d’énergie actuel de l’appareil à l’aide de la propriété statique `Battery.EnergySaverStatus` :

```csharp
// Get energy saver status
var status = Battery.EnergySaverStatus;
```

Cette propriété retourne un membre de l’énumération `EnergySaverStatus` : `On`, `Off` ou `Unknown`. Si elle retourne `On`, l’application doit de préférence éviter le traitement en arrière-plan ou autres activités susceptibles de consommer beaucoup d’énergie.

L’application doit également installer un gestionnaire d’événements. La classe **Battery** expose un événement qui est déclenché quand l’état d’économie d’énergie change :

```csharp
public class EnergySaverTest
{
    public EnergySaverTest()
    {
        // Subscribe to changes of energy-saver status
        Battery.EnergySaverStatusChanged += OnEnergySaverStatusChanged;
    }

    private void OnEnergySaverStatusChanged(EnergySaverStatusChangedEventArgs e)
    {
        // Process change
        var status = e.EnergySaverStatus;
    }
}
```

Si l’état d’économie d’énergie devient `On`, l’application doit arrêter le traitement en arrière-plan. S’il passe à `Unknown` ou `Off`, elle peut reprendre le traitement en arrière-plan.

## <a name="platform-differences"></a>Différences entre les plateformes

# <a name="android"></a>[Android](#tab/android)

Aucune différence entre les plateformes.

# <a name="ios"></a>[iOS](#tab/ios)

- Vous devez utiliser l’appareil pour tester les API.
- Seul `AC` ou `Battery` est retourné pour `PowerSource`.

# <a name="uwp"></a>[UWP](#tab/uwp)

- Seul `AC` ou `Battery` est retourné pour `PowerSource`.

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité de batterie](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Battery)
- [Documentation sur l’API de batterie](xref:Xamarin.Essentials.Battery)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Battery-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
