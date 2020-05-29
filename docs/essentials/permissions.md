---
titre : « Xamarin.Essentials : autorisations » Description : « ce document décrit la classe d’autorisations dans Xamarin.Essentials , qui offre la possibilité de vérifier et de demander des autorisations d’exécution. »
ms. AssetID : 34062D84-3E55-4AF7-A688-8551068B1E57 auteur : jamesmontemagno ms. Author : Jamont ms. Custom : Video ms. Date : 01/06/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Autorisations

La classe d' **autorisations** offre la possibilité de vérifier et de demander des autorisations d’exécution.

## <a name="get-started"></a>Bien démarrer

[!include[](~/essentials/includes/get-started.md)]

[!include[](~/essentials/includes/android-permissions.md)]

## <a name="using-permissions"></a>Utilisation des autorisations

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Vérification des autorisations

Pour vérifier l’état actuel d’une autorisation, utilisez la `CheckStatusAsync` méthode avec l’autorisation spécifique pour laquelle obtenir l’État.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

Une `PermissionException` exception est levée si l’autorisation requise n’est pas déclarée.

Il est préférable de vérifier l’état de l’autorisation avant de la demander. Chaque système d’exploitation retourne un état différent par défaut si l’utilisateur n’a jamais été invité à le faire. iOS retourne `Unknown` , tandis que d’autres retournent `Denied` .

## <a name="requesting-permissions"></a>Demande d'autorisations

Pour demander une autorisation aux utilisateurs, utilisez la `RequestAsync` méthode avec l’autorisation spécifique à demander. Si l’utilisateur a précédemment accordé une autorisation et n’a pas été révoqué, cette méthode est immédiatement retournée `Granted` et n’affiche pas de boîte de dialogue.

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

Une `PermissionException` exception est levée si l’autorisation requise n’est pas déclarée.

Notez que sur certaines plateformes, une demande d’autorisation ne peut être activée qu’une seule fois. Les invites supplémentaires doivent être gérées par le développeur pour vérifier si une autorisation est dans l' `Denied` État et demander à l’utilisateur de l’activer manuellement.

## <a name="permission-status"></a>État de l’autorisation

Lorsque `CheckStatusAsync` vous utilisez ou `RequestAsync` un `PermissionStatus` est retourné et peut être utilisé pour déterminer les étapes suivantes :

* Inconnu-l’autorisation est dans un état inconnu
* Refusé-l’utilisateur a refusé la demande d’autorisation
* Désactivé : la fonctionnalité est désactivée sur l’appareil
* Accordé : l’utilisateur a accordé l’autorisation ou est automatiquement accordé
* Restreint : dans un État restreint

## <a name="available-permissions"></a>Autorisations disponibles

Xamarin.Essentialstente de soustraire autant d’autorisations que possible. Toutefois, chaque système d’exploitation possède un ensemble d’autorisations d’exécution différent. En outre, il existe des différences lors de la fourniture d’une seule API pour certaines autorisations. Voici un guide des autorisations actuellement disponibles :

Guide des icônes :

* ![Prise en charge complète](~/media/shared/yes.png "Prise en charge complète") -prise en charge
* ![Non pris en charge](~/media/shared/no.png "Non pris en charge ou obligatoire") -non pris en charge/requis

| Autorisation | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---:
| CalendarRead   | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos pris en charge](~/media/shared/yes.png "Watchos pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| CalendarWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos pris en charge](~/media/shared/yes.png "Watchos pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Appareil photo | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| ContactsRead | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| ContactsWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Lampe torche | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| LocationWhenInUse | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![Watchos pris en charge](~/media/shared/yes.png "Watchos pris en charge") | ![tvOS pris en charge](~/media/shared/yes.png "tvOS pris en charge")  | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| LocationAlways | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![Watchos pris en charge](~/media/shared/yes.png "Watchos pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| Média | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Microphone | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| Téléphone | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Photos | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS pris en charge](~/media/shared/yes.png "tvOS pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Rappels | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos pris en charge](~/media/shared/yes.png "Watchos pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Capteurs | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![Watchos pris en charge](~/media/shared/yes.png "Watchos pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| SMS | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Voix | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| StorageRead | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| StorageWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![Watchos non pris en charge](~/media/shared/no.png "Watchos non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |

Si une autorisation est marquée comme ![non prise en charge](~/media/shared/no.png "non pris en charge") , elle retourne toujours quand elle est `Granted` activée ou demandée.

## <a name="general-usage"></a>Utilisation générale
Voici un modèle d’utilisation générale pour la gestion des autorisations.

```csharp
public async Task<PermissionStatus> CheckAndRequestLocationPermission()
{
    var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
    if (status != PermissionStatus.Granted)
    {
        status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
    }

    // Additionally could prompt the user to turn on in settings

    return status;
}
```

Chaque type d’autorisation peut avoir une instance créée que les méthodes peuvent être appelées directement.

```csharp
public async Task GetLocationAsync()
{
    var status = await CheckAndRequestPermissionAsync(new Permissions.LocationWhenInUse());
    if (status != PermissionStatus.Granted)
    {
        // Notify user permission was denied
        return;
    }

    var location = await Geolocation.GetLocationAsync();
}

public async Task<PermissionStatus> CheckAndRequestPermissionAsync<T>(T permission)
            where T : BasePermission
{
    var status = await permission.CheckStatusAsync();
    if (status != PermissionStatus.Granted)
    {
        status = await permission.RequestAsync();
    }

    return status;
}
```

## <a name="extending-permissions"></a>Extension des autorisations

L’API des autorisations a été créée pour être flexible et extensible pour les applications qui nécessitent une validation ou des autorisations supplémentaires qui ne sont pas incluses dans Xamarin.Essentials . Créez une nouvelle classe qui hérite de `BasePermission` et implémentez les méthodes abstraites requises. Alors

```csharp
public class MyPermission : BasePermission
{
    // This method checks if current status of the permission
    public override Task<PermissionStatus> CheckStatusAsync()
    {
        throw new System.NotImplementedException();
    }

    // This method is optional and a PermissionException is often thrown if a permission is not declared
    public override void EnsureDeclared()
    {
        throw new System.NotImplementedException();
    }

    // Requests the user to accept or deny a permission
    public override Task<PermissionStatus> RequestAsync()
    {
        throw new System.NotImplementedException();
    }
}
```

Lors de l’implémentation d’une autorisation sur une plateforme spécifique, la `BasePlatformPermission` classe peut être héritée de. Cela fournit des méthodes d’assistance de plateforme supplémentaires pour vérifier automatiquement les déclarations. Cela peut être utile lors de la création d’autorisations personnalisées pour effectuer un regroupement. Par exemple, vous pouvez demander l’accès en lecture et en écriture au stockage sur Android en utilisant l’autorisation personnalisée suivante.

Créez une nouvelle autorisation dans votre projet auquel vous appelez des autorisations.

```csharp
public partial class ReadWriteStoragePermission  : Xamarin.Essentials.Permissions.BasePlatformPermission
{

}
```

Dans votre projet Android, étendez l’autorisation avec les autorisations que vous souhaitez demander.

```csharp
public partial class ReadWriteStoragePermission : Xamarin.Essentials.Permissions.BasePlatformPermission
{
    public override (string androidPermission, bool isRuntime)[] RequiredPermissions => new List<(string androidPermission, bool isRuntime)>
    {
        (Android.Manifest.Permission.ReadExternalStorage, true),
        (Android.Manifest.Permission.WriteExternalStorage, true)
    }.ToArray();
}
```

Vous pouvez ensuite appeler votre nouvelle autorisation à partir de la logique partagée.

```csharp
await Permissions.RequestAsync<ReadWriteStoragePermission>();
```

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Les attributs correspondants doivent être définis dans le fichier manifeste Android des autorisations.

Pour plus d’informations sur les autorisations, consultez la documentation [de Xamarin. Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions) .

# <a name="ios"></a>[iOS](#tab/ios)

Les autorisations doivent avoir une chaîne correspondante dans le `Info.plist` fichier. Une fois qu’une autorisation est demandée et refusée, une fenêtre contextuelle ne s’affiche plus si vous demandez l’autorisation une deuxième fois. Vous devez inviter votre utilisateur à ajuster manuellement le paramètre dans l’écran Paramètres des applications dans iOS.

Pour plus d’informations, consultez la documentation sur les [fonctionnalités de sécurité et de confidentialité iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy) .

# <a name="uwp"></a>[UWP](#tab/uwp)

Les autorisations doivent avoir des fonctionnalités correspondantes déclarées dans le manifeste du package.

Pour plus d’informations, consultez la documentation sur la [déclaration des fonctionnalités](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations) de l’application.

--------------

## <a name="api"></a>API

- [Code source des autorisations](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentation sur les API des autorisations](xref:Xamarin.Essentials.Permissions)


## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Permissions-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
