---
title: 'Xamarin.Essentials: Permissions'
description: Ce document décrit la classe Autorisations dans Xamarin.Essentials, qui fournit la possibilité de vérifier et de demander des autorisations d’exécution.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 21f2079ace4adae6fd84d89426e5d66692af2a0a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "78289808"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Permissions

La classe **Autorisations** offre la possibilité de vérifier et de demander des autorisations d’exécution.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Utilisation des autorisations

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Vérification des autorisations

Pour vérifier l’état actuel d’une autorisation, utilisez la `CheckStatusAsync` méthode avec l’autorisation spécifique pour obtenir le statut.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

A `PermissionException` est jeté si la permission requise n’est pas déclarée.

## <a name="requesting-permissions"></a>Demande d'autorisations

Pour demander une autorisation aux `RequestAsync` utilisateurs, utilisez la méthode ainsi que l’autorisation spécifique de demander. Si l’utilisateur a déjà accordé la permission et `Granted` ne l’a pas révoqué, alors cette méthode reviendra immédiatement et ne pas afficher un dialogue. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

A `PermissionException` est jeté si la permission requise n’est pas déclarée. 

Notez que sur certaines plates-formes une demande d’autorisation ne peut être activée qu’une seule fois. D’autres invites doivent être traitées par le développeur `Denied` pour vérifier si une permision est dans l’état et demander à l’utilisateur de l’allumer manuellement.

## <a name="permission-status"></a>Statut d’autorisation

Lors `CheckStatusAsync` de `RequestAsync` `PermissionStatus` l’utilisation ou d’un sera retourné qui sera utilisé pour déterminer les prochaines étapes.

* Inconnu - La permission est dans un état inconnu
* Refusé - L’utilisateur a refusé la demande d’autorisation
* Désactivé - La fonctionnalité est désactivée sur l’appareil
* Accordé - L’utilisateur a accordé la permission ou est automatiquement accordé
* Restreint - Dans un état restreint

## <a name="available-permissions"></a>Autorisations disponibles

Xamarin.Essentials tente d’en extraire autant d’autorisations que possible, mais chaque système d’exploitation dispose d’un ensemble différent d’autorisations de temps d’exécution. En outre, il existe des différences dans la capacité de fournir une seule API pour certaines autorisations. Voici un guide des autorisations actuellement disponibles :

Guide des icônes :

* ![Soutien complet](~/media/shared/yes.png "Prise en charge complète") - Soutenu
* ![Non pris en charge](~/media/shared/no.png "Non pris en charge ou requis") - Non pris en charge/requis

| Autorisation | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendrierLire   | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| CalendrierWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Caméra | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen soutenu](~/media/shared/yes.png "Tizen soutenu") |
| ContactsLisible | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP soutenu](~/media/shared/yes.png "UWP soutenu") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| ContactsWrite (en anglais) | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP soutenu](~/media/shared/yes.png "UWP soutenu") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Lampe torche | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen soutenu](~/media/shared/yes.png "Tizen soutenu") |
| EmplacementWhenInUse | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP soutenu](~/media/shared/yes.png "UWP soutenu") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS pris en charge](~/media/shared/yes.png "tvOS pris en charge")  | ![Tizen soutenu](~/media/shared/yes.png "Tizen soutenu") |
| LocalisationAlways | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP soutenu](~/media/shared/yes.png "UWP soutenu") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen soutenu](~/media/shared/yes.png "Tizen soutenu") |
| Médias | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Microphone | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP soutenu](~/media/shared/yes.png "UWP soutenu") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen soutenu](~/media/shared/yes.png "Tizen soutenu") |
| Téléphone | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Photos | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS pris en charge](~/media/shared/yes.png "tvOS pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Rappels | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Capteurs | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP soutenu](~/media/shared/yes.png "UWP soutenu") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Sms | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Speech | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| StorageRead | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| StorageWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |

Si une autorisation est marquée comme `Granted` non prise en ![charge,](~/media/shared/no.png "non pris en charge") elle reviendra toujours lorsqu’elle est vérifiée ou demandée.

## <a name="general-usage"></a>Utilisation générale
Voici un patter d’utilisation générale pour le traitement des autorisations.

```csharp
public async Task<PermissionStatus> CheckAndRequestPermissionAsync<TPermission>()
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

Chaque type d’autorisation peut avoir un exemple de celui-ci créé que les méthodes peuvent être appelées directement.

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

## <a name="extending-permissions"></a>Prolongation des autorisations

L’API Autorisations a été créée pour être flexible et extensible pour les applications qui nécessitent une validation supplémentaire ou des autorisations qui ne sont pas incluses dans Xamarin.Essentials. Créez une nouvelle classe `BasePermission` qui hérite des méthodes abstraites requises et implémenter. Alors 

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

Lors de la mise en œuvre d’une autorisation dans une plate-forme spécifique, la `BasePlatformPermission` classe peut être héritée. Cela fournit des méthodes supplémentaires d’aide de plate-forme pour vérifier automatiquement les déclarations.

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Les autorisations doivent avoir les attributs correspondants définis dans le fichier Android Manifest.

En savoir plus sur les autorisations dans la documentation [Xamarin.Android.](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions)

# <a name="ios"></a>[iOS](#tab/ios)

Les autorisations doivent avoir `Info.plist` une chaîne correspondante dans le fichier. Une fois qu’une autorisation est demandée et refusée, un pop up n’apparaîtra plus si vous demandez la permission une deuxième fois. Vous devez inciter votre utilisateur à ajuster manuellement le paramètre dans l’écran des paramètres des applications dans iOS.

En savoir plus sur la documentation [iOS Security and Privacy Features.](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy)

# <a name="uwp"></a>[UWP](#tab/uwp)

Les autorisations doivent avoir des capacités de correspondance déclarées dans le manifeste du paquet.

En savoir plus sur la documentation de la [Déclaration de capacité d’application.](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations)

--------------

## <a name="api"></a>API

- [Code source d’autorisation](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Autorisations documentation API](xref:Xamarin.Essentials.Permissions)

