---
title: 'Xamarin.Essentials : préférences'
description: Ce document décrit la classe Preferences de Xamarin.Essentials, qui enregistre les préférences de l’application dans un magasin de clés/valeurs. Il explique comment utiliser la classe et les types de données pouvant être stockés.
ms.assetid: AA81BCBD-79BA-448F-942B-BA4415CA50FF
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: d50fe3853ab87d5bc14ac15a442140218a1b0fe0
ms.sourcegitcommit: 6be6374664cd96a7d924c2e0c37aeec4adf8be13
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617551"
---
# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials : préférences

![Préversion NuGet](~/media/shared/pre-release.png)

La classe **Preferences** permet de stocker les préférences d’application dans un magasin de clés/valeurs.

## <a name="get-started"></a>Prise en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>Utilisation des préférences

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour enregistrer la valeur d’une _clé_ donnée dans les préférences :

```csharp
Preferences.Set("my_key", "my_value");
```

Pour récupérer une valeur à partir des préférences, ou une valeur par défaut si elle n’est pas définie :

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Pour supprimer la _clé_ des préférences :

```csharp
Preferences.Remove("my_key");
```

Pour supprimer toutes les préférences :

```csharp
Preferences.Clear();
```

En plus de ces méthodes, vous pouvez utiliser un `sharedName` facultatif qui permet de créer des conteneurs supplémentaires pour des préférences. Lisez les spécificités d’implémentation en fonction de la plateforme, ci-dessous.

## <a name="supported-data-types"></a>Types de données pris en charge

Les types de données suivants sont pris en charge dans **Preferences** :

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="implementation-details"></a>Détails de l’implémentation

Les valeurs de `DateTime` sont stockées dans un format binaire 64 bits (entier long) à l’aide de deux méthodes définies par la classe `DateTime` : la méthode [`ToBinary`](xref:System.DateTime.ToBinary) sert à encoder la valeur de `DateTime`, alors que la méthode [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) sert à décoder la valeur. Consultez la documentation de ces méthodes pour connaître les ajustements qui peuvent être apportés aux valeurs décodées quand un `DateTime` stocké n’est pas une valeur UTC (temps universel coordonné).

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Toutes les données sont stockées dans les [Préférences partagées](https://developer.android.com/training/data-storage/shared-preferences.html). Si aucun `sharedName` n’est spécifié, les préférences partagées par défaut sont utilisées. Sinon, le nom sert à obtenir des préférences partagées **privées** avec le nom spécifié.

# <a name="iostabios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/xamarin/ios/app-fundamentals/user-defaults) permet de stocker des valeurs sur les appareils iOS. Si aucun `sharedName` n’est spécifié, `StandardUserDefaults` est utilisé. Sinon, le nom sert à créer un `NSUserDefaults` avec le nom spécifié utilisé pour `NSUserDefaultsType.SuiteName`.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) permet de stocker les valeurs sur l’appareil. Si aucun `sharedName` n’est spécifié, `LocalSettings` est utilisé. Sinon, le nom sert à créer un conteneur dans `LocalSettings`.

--------------

## <a name="persistence"></a>Persistance

La désinstallation de l’application entraîne la suppression de toutes les _Préférences_. Il existe une exception à cette règle. Il s’agit du cas où les applications ciblent (et s’exécutent sur) Android 6.0 (niveau d’API 23) ou version ultérieure, et utilisent la [__sauvegarde automatique__](https://developer.android.com/guide/topics/data/autobackup). Cette fonctionnalité est activée par défaut et conserve les données de l’application, notamment les __Préférences partagées__, utilisées par l’API de **Préférences**. Vous pouvez désactiver cette fonctionnalité en suivant la [documentation](https://developer.android.com/guide/topics/data/autobackup) de Google.

## <a name="limitations"></a>Limitations

Quand vous stockez une chaîne, cette API permet de stocker de petites quantités de texte.  Les performances risquent d’être médiocres si vous essayez de l’utiliser pour stocker de grandes quantités de texte.

## <a name="api"></a>API

- [Code source de la fonctionnalité des préférences](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentation sur l’API de préférences](xref:Xamarin.Essentials.Preferences)
