---
title: 'Xamarin.Essentials: Commentaires haptique'
description: Ce document décrit la classe HapticFeedback dans Xamarin.Essentials , qui vous permet de contrôler les commentaires haptique sur l’appareil.
ms.assetid: 4462936c-4018-443b-906d-d63da6d0ed7d
author: dimonovdd
ms.author: jamont
ms.date: 09/22/2020
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b1bf597874dc22a95ca9a3db239d9c7d2dd5658a
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91436730"
---
# <a name="no-locxamarinessentials-haptic-feedback"></a>Xamarin.Essentials: Commentaires haptique

La classe **HapticFeedback** vous permet de contrôler les commentaires haptique sur l’appareil.

![API de la version préliminaire](~/media/shared/preview.png)

## <a name="get-started"></a>Prise en main

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
