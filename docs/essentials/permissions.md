---
title: 'Xamarin.Essentials: Autorisations'
description: Ce document décrit la classe Permissions dans Xamarin.Essentials, qui offre la possibilité de vérifier et de demander des autorisations d’exécution.
ms.assetid: 34062D84-3E55-4AF7-A688-8551068B1E57
author: jamesmontemagno
ms.author: jamont
ms.date: 01/06/2020
ms.openlocfilehash: 2b1413c6bce7fc4ce43a190cd25494c69dbadedb
ms.sourcegitcommit: ec62c7f28abc8e121656f1b93146657d90a4cab4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2020
ms.locfileid: "77555687"
---
# <a name="xamarinessentials-permissions"></a>Xamarin.Essentials: Autorisations

La classe **Permissions** permet de vérifier et de demander des autorisations d’exécution.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-permissions"></a>Utilisation de Permissions

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

## <a name="checking-permissions"></a>Vérification des autorisations

Pour vérifier l’état actuel d’une autorisation, utilisez la méthode `CheckStatusAsync`, ainsi que l’autorisation spécifique pour laquelle obtenir l’état.

```csharp
var status = await Permissions.CheckStatusAsync<Permissions.LocationWhenInUse>();
```

`PermissionException` est levé si l’autorisation requise n’est pas déclarée.

## <a name="requesting-permissions"></a>Demande d'autorisations

Pour demander une autorisation aux utilisateurs, utilisez la méthode `RequestAsync`, ainsi que l’autorisation spécifique à demander. Si l’utilisateur a précédemment accordé une autorisation et qu’il ne l’a pas révoquée, cette méthode retourne `Granted` immédiatement et n’affiche pas de boîte de dialogue. 

```csharp
var status = await Permissions.RequestAsync<Permissions.LocationWhenInUse>();
```

`PermissionException` est levé si l’autorisation requise n’est pas déclarée. 

Notez que sur certaines plateformes, une demande d’autorisation ne peut être activée qu’une seule fois. Les invites supplémentaires doivent être gérées par le développeur pour vérifier si une autorisation a l’état `Denied` et demander à l’utilisateur de l’activer manuellement.

## <a name="permission-status"></a>État d’autorisation

Lorsque vous utilisez `CheckStatusAsync` ou `RequestAsync` un `PermissionStatus` est retourné et utilisé pour déterminer les étapes suivantes.

* Inconnu - L’autorisation est dans un état inconnu
* Refusé - L’utilisateur a refusé la demande d’autorisation
* Désactivé - La fonctionnalité est désactivée sur l’appareil
* Accordé - L’utilisateur a accordé l’autorisation ou celle-ci est automatiquement accordée
* Restreint - État restreint

## <a name="available-permissions"></a>Autorisations disponibles

Xamarin.Essentials tente d’extraire autant d’autorisations que possible, mais chaque système d’exploitation possède un ensemble d’autorisations d’exécution différent. En outre, il existe des différences en ce qui concerne la possibilité de fournir une seule API pour certaines autorisations. Voici un guide des autorisations actuellement disponibles :

Guide des icônes :

* ![Prise en charge complète](~/media/shared/yes.png "Prise en charge complète") - Pris en charge
* ![Aucune prise en charge](~/media/shared/no.png "Aucune prise charge ou pas demandé") - Aucune prise en charge/pas demandé

| Autorisation | Android | iOS | UWP | watchOS | tvOS | Tizen |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: 
| CalendarRead   | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| CalendarWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Appareil photo | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| ContactsRead | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| ContactsWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Lampe torche | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| LocationWhenInUse | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS pris en charge](~/media/shared/yes.png "tvOS pris en charge")  | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| LocationAlways | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| Médias | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Microphone | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen pris en charge](~/media/shared/yes.png "Tizen pris en charge") |
| Phone | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Photos | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS pris en charge](~/media/shared/yes.png "tvOS pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Rappels | ![Android non pris en charge](~/media/shared/no.png "Android non pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Capteurs | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP pris en charge](~/media/shared/yes.png "UWP pris en charge") | ![watchOS pris en charge](~/media/shared/yes.png "watchOS pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| SMS | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| Speech | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS pris en charge](~/media/shared/yes.png "iOS pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| StorageRead | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |
| StorageWrite | ![Android pris en charge](~/media/shared/yes.png "Android pris en charge") | ![iOS non pris en charge](~/media/shared/no.png "iOS non pris en charge") | ![UWP non pris en charge](~/media/shared/no.png "UWP non pris en charge") | ![watchOS non pris en charge](~/media/shared/no.png "watchOS non pris en charge") | ![tvOS non pris en charge](~/media/shared/no.png "tvOS non pris en charge") | ![Tizen non pris en charge](~/media/shared/no.png "Tizen non pris en charge") |

Si une autorisation est marquée avec un ❌ elle retourne toujours `Granted` quand elle est vérifiée ou demandée.

## <a name="general-usage"></a>Utilisation générale
Voici un exemple d’utilisation générale pour la gestion des autorisations.

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

Chaque type d’autorisation peut avoir une instance d’elle créée que les méthodes peuvent appeler directement.

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

L’API Permissions a été créée pour être flexible et extensible pour les applications qui nécessitent une validation ou des autorisations supplémentaires qui ne sont pas incluses dans Xamarin.Essentials. Créez une nouvelle classe qui hérite de `BasePermission` et implémentez les méthodes abstraites requises. Then 

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

Lors de l’implémentation d’une autorisation sur une plateforme spécifique, la classe `BasePlatformPermission` peut être héritée. Cela fournit des méthodes d’assistance de plateforme supplémentaires pour vérifier automatiquement les déclarations.

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Les attributs correspondants doivent être définis dans le fichier manifeste Android des autorisations.

Pour plus d’informations, consultez la documentation [Autorisations dans Xamarin.Android](https://docs.microsoft.com/xamarin/android/app-fundamentals/permissions).

# <a name="ios"></a>[iOS](#tab/ios)

Les autorisations doivent avoir une chaîne correspondante dans le fichier `Info.plist`. Une fois qu’une autorisation est demandée et refusée, une fenêtre contextuelle ne s’affiche plus si vous demandez l’autorisation une deuxième fois. Vous devez inviter votre utilisateur à ajuster manuellement le paramètre dans l’écran Paramètres des applications dans iOS.

Pour plus d’informations, consultez la documentation [Fonctionnalités de sécurité et de confidentialité d’iOS](https://docs.microsoft.com/xamarin/ios/app-fundamentals/security-privacy).

# <a name="uwp"></a>[UWP](#tab/uwp)

Les autorisations doivent avoir des capacités correspondantes déclarées dans le manifeste du package.

Pour plus d’informations, consultez la documentation [Déclaration des capacités de l’application](https://docs.microsoft.com/windows/uwp/packaging/app-capability-declarations).

--------------

## <a name="api"></a>API

- [Code source des autorisations](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Permissions)
- [Documentation sur l’API Permissions](xref:Xamarin.Essentials.Permissions)

