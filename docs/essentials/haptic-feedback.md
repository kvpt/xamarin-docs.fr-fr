---
title: 'Xamarin.Essentials: Commentaires haptique'
description: Ce document décrit la classe HapticFeedback dans Xamarin.Essentials , qui vous permet de contrôler les commentaires haptique sur l’appareil.
ms.assetid: 4462936c-4018-443b-906d-d63da6d0ed7d
author: dimonovdd
ms.author: jamont
ms.date: 01/04/2021
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0b039a8ba7db7b98d30a49b74454b8c0c101f040
ms.sourcegitcommit: 995ee23d93e08dceb8754cc6c682cd2f4594345b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/07/2021
ms.locfileid: "97972303"
---
# <a name="no-locxamarinessentials-haptic-feedback"></a>Xamarin.Essentials: Commentaires haptique

La classe **HapticFeedback** vous permet de contrôler les commentaires haptique sur l’appareil.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

Pour accéder à la fonctionnalité **HapticFeedback** , la configuration spécifique à la plateforme suivante est requise.

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

## <a name="using-haptic-feedback"></a>Utilisation de commentaires haptique

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

La fonctionnalité de commentaires haptique peut être exécutée avec un `Click` `LongPress` type de retour ou.

```csharp
try
{
    // Perform click feedback
    HapticFeedback.Perform(HapticFeedbackType.Click);

    // Or use long press    
    HapticFeedback.Perform(HapticFeedbackType.LongPress);
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

## <a name="api"></a>API

- [Code source HapticFeedback](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/HapticFeedback)
- [Documentation de l’API HapticFeedback](xref:Xamarin.Essentials.HapticFeedback)
