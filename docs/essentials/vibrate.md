---
title: 'Xamarin.Essentials : vibration'
description: Ce document décrit la classe Vibration de Xamarin.Essentials, qui vous permet de démarrer et d’arrêter la fonctionnalité de vibration pendant la durée souhaitée.
ms.assetid: 7E8B24C4-2625-4DAE-A129-383542D34F1E
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d9bf7a1e5e0d15f1fdc909745cd439115b6f8463
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898933"
---
# <a name="xamarinessentials-vibration"></a>Xamarin.Essentials : vibration

La classe **Vibration** vous permet de démarrer et d’arrêter la fonctionnalité de vibration pendant la durée souhaitée.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité de **vibration**, la configuration suivante spécifique à la plateforme est obligatoire.

# <a name="androidtabandroid"></a>[Android](#tab/android)

L’autorisation Vibrate est obligatoire, et doit être configurée dans le projet Android. Vous pouvez le faire de plusieurs façons, comme indiqué ci-dessous :

Ouvrez le fichier **AssemblyInfo.cs** sous le dossier **Propriétés** et ajoutez :

```csharp
[assembly: UsesPermission(Android.Manifest.Permission.Vibrate)]
```

OU mettez à jour le manifeste Android :

Ouvrez le fichier **AndroidManifest.xml** sous le dossier **Propriétés** et ajoutez ce qui suit dans le nœud du **manifeste**.

```xml
<uses-permission android:name="android.permission.VIBRATE" />
```

Vous pouvez également cliquer avec le bouton droit sur le projet Android, et ouvrir les propriétés du projet. Sous **Manifeste Android**, recherchez la zone **Autorisations nécessaires**, puis cochez l’autorisation **VIBRATE**. Cela entraîne la mise à jour automatique du fichier **AndroidManifest.xml**.

# <a name="iostabios"></a>[iOS](#tab/ios)

Aucune configuration supplémentaire n’est requise.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

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

# <a name="androidtabandroid"></a>[Android](#tab/android)

Aucune différence entre les plateformes.

# <a name="iostabios"></a>[iOS](#tab/ios)

* Vibre uniquement quand l’appareil est configuré pour vibrer en cas de sonnerie.
* Vibre toujours pendant 500 millisecondes.
* Impossible d’annuler la vibration.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Aucune différence entre les plateformes.

-----

## <a name="api"></a>API

- [Code source de la fonctionnalité de vibration](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Vibration)
- [Documentation sur l’API de vibration](xref:Xamarin.Essentials.Vibration)
